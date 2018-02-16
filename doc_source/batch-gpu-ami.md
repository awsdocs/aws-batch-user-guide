# Creating a GPU Workload AMI<a name="batch-gpu-ami"></a>

To run GPU workloads on your AWS Batch compute resources, you can start with the [Deep Learning AMI CUDA 9 Amazon Linux Version](https://aws.amazon.com/marketplace/pp/B076T8RSXY) as a base AMI and configure it to be able to run AWS Batch jobs\.

This deep learning AMI is based on Amazon Linux, so you can install the `ecs-init` package and make it compatible as a compute resource AMI\. The `nvidia-docker` RPM installs the required components for copying the NVIDIA drivers to the correct location for Docker containers in AWS Batch jobs, to be able to access the GPUs on supported instance types\.

**Note**  
Your associated GPU job definitions must use privileged containers that mount the host path `/var/lib/nvidia-docker/volumes/nvidia_driver/latest` at `/usr/local/nvidia`\. For more information, see [Test GPU Functionality](example-job-definitions.md#example-test-gpu)\.

**To configure the Deep Learning AMI for AWS Batch**

1. Launch a GPU instance type \(for example, P3\) with the [Deep Learning AMI CUDA 9 Amazon Linux Version](https://aws.amazon.com/marketplace/pp/B076T8RSXY) in a region that AWS Batch supports\. 

1. Connect to your instance with SSH\. For more information, see [Connecting to Your Linux Instance Using SSH](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. With your favorite text editor, create a file called `configure-gpu.sh` with the following contents:

   ```
   #!/bin/bash
   # Install ecs-init, start docker, and install nvidia-docker
   sudo yum install -y ecs-init
   sudo service docker start
   wget https://github.com/NVIDIA/nvidia-docker/releases/download/v1.0.1/nvidia-docker-1.0.1-1.x86_64.rpm
   sudo rpm -ivh --nodeps nvidia-docker-1.0.1-1.x86_64.rpm
   
   # Validate installation
   rpm -ql nvidia-docker
   rm nvidia-docker-1.0.1-1.x86_64.rpm
   
   # Make sure the NVIDIA kernel modules and driver files are bootstraped
   # Otherwise running a GPU job inside a container will fail with "cuda: unknown exception"
   echo '#!/bin/bash' | sudo tee /var/lib/cloud/scripts/per-boot/00_nvidia-modprobe > /dev/null
   echo 'nvidia-modprobe -u -c=0' | sudo tee --append /var/lib/cloud/scripts/per-boot/00_nvidia-modprobe > /dev/null
   sudo chmod +x /var/lib/cloud/scripts/per-boot/00_nvidia-modprobe
   sudo /var/lib/cloud/scripts/per-boot/00_nvidia-modprobe
   
   # Start the nvidia-docker-plugin and run a container with 
   # nvidia-docker (retry up to 4 times if it fails initially)
   sudo -b nohup nvidia-docker-plugin > /tmp/nvidia-docker.log
   sudo docker pull nvidia/cuda:9.0-cudnn7-devel
   COMMAND="sudo nvidia-docker run nvidia/cuda:9.0-cudnn7-devel nvidia-smi"
   for i in {1..5}; do $COMMAND && break || sleep 15; done
   
   # Create symlink to latest nvidia-driver version
   nvidia_base=/var/lib/nvidia-docker/volumes/nvidia_driver
   sudo ln -s $nvidia_base/$(ls $nvidia_base | sort -n  | tail -1) $nvidia_base/latest
   ```

1. Run the script\.

   ```
   bash ./configure-gpu.sh
   ```

1. Validate that you can run a Docker container and access the installed drivers with the following command\.

   ```
   sudo docker run --privileged -v /var/lib/nvidia-docker/volumes/nvidia_driver/latest:/usr/local/nvidia nvidia/cuda:9.0-cudnn7-devel nvidia-smi
   ```

   You should see something similar to the following output\.

   ```
   +-----------------------------------------------------------------------------+
   | NVIDIA-SMI 384.81                 Driver Version: 384.81                    |
   |-------------------------------+----------------------+----------------------+
   | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
   | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
   |===============================+======================+======================|
   |   0  Tesla V100-SXM2...  Off  | 00000000:00:17.0 Off |                    0 |
   | N/A   43C    P0    42W / 300W |     10MiB / 16152MiB |      0%      Default |
   +-------------------------------+----------------------+----------------------+
   
   +-----------------------------------------------------------------------------+
   | Processes:                                                       GPU Memory |
   |  GPU       PID  Type  Process name                               Usage      |
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

1. Create a new AMI from your running instance\. For more information, see [Creating an Amazon EBS\-Backed Linux AMI](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/creating-an-ami-ebs.html) in the *Amazon EC2 User Guide for Linux Instances* guide\.