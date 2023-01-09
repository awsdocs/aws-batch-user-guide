# Creating a compute resource AMI<a name="create-batch-ami"></a>

You can create your own custom compute resource AMI to use for your managed and unmanaged compute environments\. For instructions, see the [Compute resource AMI specification](compute_resource_AMIs.md#batch-ami-spec)\. Then, after you created a custom AMI, you can create a compute environment that uses that AMI that you can associate a job queue with\. Last, start submitting jobs to that queue\.

**To create a custom compute resource AMI**

1. Choose a base AMI to start from\. The base AMI must use HVM virtualization\. The base AMI can't be a Windows AMI\.
**Note**  
The AMI that you choose for a compute environment must match the architecture of the instance types that you want to use for that compute environment\. For example, if your compute environment uses A1 instance types, the compute resource AMI that you choose must support Arm instances\. Amazon ECS vends both x86 and Arm versions of the Amazon ECS optimized Amazon Linux 2 AMI\. For more information, see [Amazon ECS optimized Amazon Linux 2 AMI](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-optimized_AMI.html#ecs-optimized-ami-linux-variants.html) in the *Amazon Elastic Container Service Developer Guide*\.

   The Amazon ECS optimized Amazon Linux 2 AMI is the default AMI for compute resources in managed compute environments\. The Amazon ECS optimized Amazon Linux 2 AMI is preconfigured and tested on AWS Batch by AWS engineers\. It's a minimal AMI that you can get started with and to get your compute resources that are running on AWS quickly\. For more information, see [Amazon ECS Optimized AMI](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-optimized_AMI.html) in the *Amazon Elastic Container Service Developer Guide*\.

   Alternatively, you can choose another Amazon Linux 2 variant and install the `ecs-init` package with the following commands\. For more information, see [Installing the Amazon ECS container agent on an Amazon Linux 2 EC2 instance ](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-agent-install.html#ecs-agent-install-al2) in the *Amazon Elastic Container Service Developer Guide*:

   ```
   $ sudo amazon-linux-extras disable docker
   $ sudo amazon-linux-extras install ecs-init
   ```

   For example, if you want to run GPU workloads on your AWS Batch compute resources, you can start with the [Amazon Linux Deep Learning AMI](https://aws.amazon.com/marketplace/pp/B01M0AXXQB)\. Then, configure the AMI to run AWS Batch jobs\. For more information, see [Using a GPU workload AMI](batch-gpu-ami.md)\.
**Important**  
You can choose a base AMI that doesn't support the `ecs-init` package\. However, if you do, you must configure a way to start the Amazon ECS agent at boot and keep it running\. You can also view several example user data configuration scripts that use `systemd` to start and monitor the Amazon ECS container agent\. For more information, see [Example container instance user data configuration scripts](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/example_user_data_scripts.html) in the *Amazon Elastic Container Service Developer Guide*\.

1. Launch an instance from your selected base AMI with the appropriate storage options for your AMI\. You can configure the size and number of attached Amazon EBS volumes, or instance storage volumes if the instance type you selected supports them\. For more information, see [Launching an Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/launching-instance.html) and [Amazon EC2 Instance Store](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/InstanceStorage.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. Connect to your instance with SSH and perform any necessary configuration tasks\. This might include any or all of the following steps:
   + Installing the Amazon ECS container agent\. For more information, see [Installing the Amazon ECS Container Agent](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-agent-install.html) in the *Amazon Elastic Container Service Developer Guide*\.
   + Configuring a script to format instance store volumes\.
   + Adding instance store volume or Amazon EFS file systems to the `/etc/fstab` file so that they're mounted at boot\.
   + Configuring Docker options, such as enabling debugging or adjusting base image size\.
   + Installing packages or copying files\.

   For more information, see [Connecting to Your Linux Instance Using SSH](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. If you started the Amazon ECS container agent on your instance, you must stop it and remove any persistent data checkpoint files before creating your AMI\. Otherwise, if you don't do this, the agent doesn't start on instances that are launched from your AMI\. 

   1. Stop the Amazon ECS container agent\.
      + Amazon ECS\-optimized Amazon Linux 2 AMI:

        ```
        sudo systemctl stop ecs
        ```
      + Amazon ECS\-optimized Amazon Linux AMI:

        ```
        sudo stop ecs
        ```

   1. Remove the persistent data checkpoint files\. By default, these files are located in the `/var/lib/ecs/data/` directory\. Use the following command to remove these files, if there are any\.

      ```
      sudo rm -rf /var/lib/ecs/data/*
      ```

1. Create a new AMI from your running instance\. For more information, see [Creating an Amazon EBS Backed Linux AMI](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/creating-an-ami-ebs.html) in the *Amazon EC2 User Guide for Linux Instances* guide\.

**To use your new AMI with AWS Batch**

1. After the AMI is created, create a compute environment with your new AMI\. Make sure that you select **Enable user\-specified AMI ID** and specify your custom AMI ID\. For more information, see [Creating a compute environment](create-compute-environment.md)\.
**Note**  
The AMI that you choose for a compute environment must match the architecture of the instance types that you want to use for that compute environment\. For example, if your compute environment uses A1 instance types, the compute resource AMI that you choose must support Arm instances\. Amazon ECS vends both x86 and Arm versions of the Amazon ECS optimized Amazon Linux 2 AMI\. For more information, see [Amazon ECS optimized Amazon Linux 2 AMI](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-optimized_AMI.html#ecs-optimized-ami-linux-variants.html) in the *Amazon Elastic Container Service Developer Guide*\.

1. Create a job queue and associate your new compute environment\. For more information, see [Creating a job queue](create-job-queue.md)\.
**Note**  
All compute environments that are associated with a job queue must share the same architecture\. AWS Batch doesn't support mixing compute environment architecture types in a single job queue\.

1. \(Optional\) Submit a sample job to your new job queue\. For more information, see [Example job definitions](example-job-definitions.md), [Creating a single\-node job definition ](create-job-definition.md), and [Submitting a job](submit_job.md)\.