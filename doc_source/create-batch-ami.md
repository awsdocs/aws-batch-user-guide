# Creating a Compute Resource AMI<a name="create-batch-ami"></a>

You can create your own custom compute resource AMI to use for your managed and unmanaged compute environments, provided that you follow the [Compute Resource AMI Specification](compute_resource_AMIs.md#batch-ami-spec)\. After you have created your custom AMI, you can create a compute environment that uses that AMI, associate it with a job queue, and then start submitting jobs to that queue\.

**To create a custom compute resource AMI**

1. Choose a base AMI to start from\. The base AMI must use HVM virtualization, and it cannot be a Windows AMI\.
**Note**  
The AMI that you choose for a compute environment must match the architecture of the instance types that you intend to use for that compute environment\. For example, if your compute environment uses A1 instance types, the compute resource AMI that you choose must support ARM instances\. Amazon ECS vends both x86 and ARM versions of the Amazon ECS\-optimized Amazon Linux 2 AMI\. For more information, see [Amazon ECS\-optimized Amazon Linux 2 AMI](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/al2ami.html) in the *Amazon Elastic Container Service Developer Guide*\.

   The Amazon ECS\-optimized AMI is the default AMI for compute resources in managed compute environments\. The Amazon ECS\-optimized AMI is preconfigured and tested on AWS Batch by AWS engineers\. It is the simplest AMI for you to get started and to get your compute resources running on AWS quickly\. For more information, see [Amazon ECS\-Optimized AMI](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-optimized_AMI.html) in the *Amazon Elastic Container Service Developer Guide*\.

   Alternatively, you can choose another Amazon Linux variant and install the `ecs-init` package with the following command:

   ```
   sudo yum install -y ecs-init
   ```

   For example, if you want to run GPU workloads on your AWS Batch compute resources, you could start with the [Amazon Linux Deep Learning AMI](https://aws.amazon.com/marketplace/pp/B01M0AXXQB) and configure it to be able to run AWS Batch jobs\. For more information, see [Using a GPU Workload AMI](batch-gpu-ami.md)\.
**Important**  
If you choose a base AMI that does not support the `ecs-init` package, you must configure a way to start the Amazon ECS agent at boot and keep it running\. To view several example user data configuration scripts that use `systemd` to start and monitor the Amazon ECS container agent, see [Example Container Instance User Data Configuration Scripts](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/example_user_data_scripts.html) in the *Amazon Elastic Container Service Developer Guide*\.

1. Launch an instance from your selected base AMI with the appropriate storage options for your AMI\. You can configure the size and number of attached Amazon EBS volumes, or instance storage volumes if the instance type you've selected supports them\. For more information, see [Launching an Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/launching-instance.html) and [Amazon EC2 Instance Store](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/InstanceStorage.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. Connect to your instance with SSH and perform any necessary configuration tasks, such as:
   + Install the Amazon ECS container agent\. For more information, see [Installing the Amazon ECS Container Agent](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-agent-install.html) in the *Amazon Elastic Container Service Developer Guide*\.
   + Configuring a script to format instance store volumes\.
   + Adding instance store volume or Amazon EFS file systems to the `/etc/fstab` file so that they are mounted at boot\.
   + Configuring Docker options \(enable debugging, adjust base image size, and so on\)\.
   + Installing packages or copying files\.

   For more information, see [Connecting to Your Linux Instance Using SSH](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. If you started the Amazon ECS container agent on your instance, you must stop it and remove the persistent data checkpoint file before creating your AMI; otherwise, the agent will not start on instances that are launched from your AMI\. 

   1. Stop the Amazon ECS container agent\.
      + Amazon ECS\-optimized Amazon Linux 2 AMI:

        ```
        sudo systemctl stop ecs
        ```
      + Amazon ECS\-optimized Amazon Linux AMI:

        ```
        sudo stop ecs
        ```

   1. Remove the persistent data checkpoint file\. By default, this file is located at `/var/lib/ecs/data/ecs_agent_data.json`\. Use the following command to remove the file\.

      ```
      sudo rm -rf /var/lib/ecs/data/ecs_agent_data.json
      ```

1. Create a new AMI from your running instance\. For more information, see [Creating an Amazon EBS\-Backed Linux AMI](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/creating-an-ami-ebs.html) in the *Amazon EC2 User Guide for Linux Instances* guide\.

**To use your new AMI with AWS Batch**

1. When the AMI creation process is complete, create a compute environment with your new AMI \(be sure to select **Enable user\-specified AMI ID** and specify your custom AMI ID in [Step 10](create-compute-environment.md#enable-custom-ami-step)\)\. For more information, see [Creating a Compute Environment](create-compute-environment.md)\.
**Note**  
The AMI that you choose for a compute environment must match the architecture of the instance types that you intend to use for that compute environment\. For example, if your compute environment uses A1 instance types, the compute resource AMI that you choose must support ARM instances\. Amazon ECS vends both x86 and ARM versions of the Amazon ECS\-optimized Amazon Linux 2 AMI\. For more information, see [Amazon ECS\-optimized Amazon Linux 2 AMI](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/al2ami.html) in the *Amazon Elastic Container Service Developer Guide*\.

1. Create a job queue and associate your new compute environment\. For more information, see [Creating a Job Queue](create-job-queue.md)\.
**Note**  
All compute environments that are associated with a job queue must share the same architecture\. AWS Batch doesn't support mixing compute environment architecture types in a single job queue\.

1. \(Optional\) Submit a sample job to your new job queue\. For more information, see [Example Job Definitions](example-job-definitions.md), [Creating a Job Definition](create-job-definition.md), and [Submitting a Job](submit_job.md)\.