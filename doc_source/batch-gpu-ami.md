# Creating a GPU Workload AMI<a name="batch-gpu-ami"></a>

To run GPU workloads on your AWS Batch compute resources, you can start with the [Deep Learning AMI \(Amazon Linux\)](https://aws.amazon.com/marketplace/pp/B077GF11NF) as a base AMI and configure it to be able to run AWS Batch jobs\.

This deep learning AMI is based on Amazon Linux, so you can install the `ecs-init` package and make it compatible as a compute resource AMI\. The `nvidia-docker2` RPM installs the required components for Docker containers in AWS Batch jobs to be able to access the GPUs on supported instance types\.

**To configure the Deep Learning AMI for AWS Batch**

1. Launch a GPU instance type \(for example, P3\) with the [Deep Learning AMI \(Amazon Linux\)](https://aws.amazon.com/marketplace/pp/B077GF11NF) in a region that AWS Batch supports\. 

1. Connect to your instance with SSH\. For more information, see [Connecting to Your Linux Instance Using SSH](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. With your favorite text editor, create a file called `configure-gpu.sh` with the following contents:

   ```
   #!/bin/bash
   # Install ecs-init, start docker, and install nvidia-docker 2
   sudo yum install -y ecs-init
   sudo service docker start
   DOCKER_VERSION=$(docker -v | awk '{ print $3 }' | cut -f1 -d"-")
   DISTRIBUTION=$(. /etc/os-release;echo $ID$VERSION_ID)
   curl -s -L https://nvidia.github.io/nvidia-docker/$DISTRIBUTION/nvidia-docker.repo | \
     sudo tee /etc/yum.repos.d/nvidia-docker.repo
   PACKAGES=$(sudo yum search -y --showduplicates nvidia-docker2 nvidia-container-runtime | grep $DOCKER_VERSION | awk '{ print $1 }')
   sudo yum install -y $PACKAGES
   sudo pkill -SIGHUP dockerd
   
   # Get CUDA version
   CUDA_VERSION=$(cat /usr/local/cuda/version.txt | grep "^CUDA Version" | awk '{ print $3 }' | cut -f1-2 -d".")
   
   # Run test container to verify installation
   sudo docker run --privileged --runtime=nvidia --rm nvidia/cuda:$CUDA_VERSION-base nvidia-smi
   
   # Update Docker daemon.json to user nvidia-container-runtime by default
   sudo tee /etc/docker/daemon.json <<EOF
   {
       "runtimes": {
           "nvidia": {
               "path": "/usr/bin/nvidia-container-runtime",
               "runtimeArgs": []
           }
       },
       "default-runtime": "nvidia"
   }
   EOF
   
   sudo service docker restart
   ```

1. Run the script\.

   ```
   bash ./configure-gpu.sh
   ```

1. Get the CUDA version that is installed on your instance\.

   ```
   CUDA_VERSION=$(cat /usr/local/cuda/version.txt | grep "^CUDA Version" | awk '{ print $3 }' | cut -f1-2 -d".")
   ```

1. Validate that you can run a Docker container and access the installed drivers with the following command\.

   ```
   sudo docker run --privileged --runtime=nvidia --rm nvidia/cuda:$CUDA_VERSION-base nvidia-smi
   ```

   You should see something similar to the following output\.

   ```
   +-----------------------------------------------------------------------------+
   | NVIDIA-SMI 410.79       Driver Version: 410.79       CUDA Version: 10.0     |
   |-------------------------------+----------------------+----------------------+
   | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
   | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
   |===============================+======================+======================|
   |   0  Tesla V100-SXM2...  On   | 00000000:00:1E.0 Off |                    0 |
   | N/A   38C    P0    25W / 300W |      0MiB / 16130MiB |      1%      Default |
   +-------------------------------+----------------------+----------------------+
   
   +-----------------------------------------------------------------------------+
   | Processes:                                                       GPU Memory |
   |  GPU       PID   Type   Process name                             Usage      |
   |=============================================================================|
   |  No running processes found                                                 |
   +-----------------------------------------------------------------------------+
   ```

1. Remove any Docker containers and images on the instance to reduce the size of your AMI\.

   1. Remove containers\.

      ```
      sudo docker rm $(sudo docker ps -aq)
      ```

   1. Remove images\.

      ```
      sudo docker rmi $(sudo docker images -q)
      ```

1. If you started the Amazon ECS container agent on your instance, you must stop it and remove the persistent data checkpoint file before creating your AMI; otherwise, the agent will not start on instances that are launched from your AMI\. 

   1. Stop the Amazon ECS container agent\.

      ```
      sudo stop ecs
      ```

   1. Remove the persistent data checkpoint file\. By default, this file is located at `/var/lib/ecs/data/ecs_agent_data.json`\. Use the following command to remove the file\.

      ```
      sudo rm -rf /var/lib/ecs/data/ecs_agent_data.json
      ```

1. Create a new AMI from your running instance\. For more information, see [Creating an Amazon EBS\-Backed Linux AMI](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/creating-an-ami-ebs.html) in the *Amazon EC2 User Guide for Linux Instances* guide\.

**To use your new AMI with AWS Batch**

1. When the AMI creation process is complete, create a compute environment with your new AMI \(be sure to select **Enable user\-specified AMI ID** and specify your custom AMI ID in [Step 9](create-compute-environment.md#enable-custom-ami-step)\)\. For more information, see [Creating a Compute Environment](create-compute-environment.md)\.

1. Create a job queue and associate your new compute environment\. For more information, see [Creating a Job Queue](create-job-queue.md)\.

1. \(Optional\) Submit a sample job to your new job queue to test the GPU functionality\. For more information, see [Test GPU Functionality](example-job-definitions.md#example-test-gpu), [Creating a Job Definition](create-job-definition.md), and [Submitting a Job](submit_job.md)\.