# Compute resource AMIs<a name="compute_resource_AMIs"></a>

By default, AWS Batch managed compute environments use a recent, approved version of the Amazon ECS optimized AMI for compute resources\. However, you might want to consider creating your own AMI to use for your managed and unmanaged compute environments\. You should do this if you also require the following actions:
+ Increase the storage size of your AMI root or data volumes
+ Add instance storage volumes for supported Amazon EC2 instance types
+ Configure the Amazon ECS container agent with custom options
+ Configure Docker to use custom options
+ Configure a GPU workload AMI that allows containers to access GPU hardware on supported Amazon EC2 instance types

**Note**  
AWS Batch doesn't upgrade the AMIs in a compute environment after it's created\. For example, it also doesn't update the AMIs in your compute environment when a newer version of the Amazon ECS optimized AMI is available\. You're responsible for the management of the guest operating system\. This includes any updates and security patches\. You're also responsible for any additional application software or utilities that you install on the compute resources\. To use a new AMI for your AWS Batch jobs:  
Create a new compute environment with the new AMI\.
Add the compute environment to an existing job queue\.
Remove the earlier compute environment from your job queue\.
Delete the earlier compute environment\.

**Topics**
+ [Compute resource AMI specification](#batch-ami-spec)
+ [Creating a compute resource AMI](create-batch-ami.md)
+ [Using a GPU workload AMI](batch-gpu-ami.md)

## Compute resource AMI specification<a name="batch-ami-spec"></a>

The basic AWS Batch compute resource AMI specification consists of the following items:

Required

 
+ A modern Linux distribution that's running at least version 3\.10 of the Linux kernel on an HVM virtualization type AMI\.
**Important**  
Multi\-node parallel jobs can only run on compute resources that were launched on an Amazon Linux instance with the `ecs-init` package installed\. We recommend that you use the default Amazon ECS optimized AMI when you create your compute environment\. You can do this by not specifying a custom AMI\. For more information, see [Multi\-node Parallel Jobs](multi-node-parallel-jobs.md)\.
+ The Amazon ECS container agent\. We recommend that you use the latest version\. For more information, see [Installing the Amazon ECS Container Agent](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-agent-install.html) in the *Amazon Elastic Container Service Developer Guide*\.
+ The `awslogs` log driver must be specified as an available log driver with the `ECS_AVAILABLE_LOGGING_DRIVERS` environment variable when the Amazon ECS container agent is started\. For more information, see [Amazon ECS Container Agent Configuration](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-agent-config.html) in the *Amazon Elastic Container Service Developer Guide*\. 
+ A Docker daemon that's running at least version 1\.9, and any Docker runtime dependencies\. For more information, see [Check runtime dependencies](https://docs.docker.com/engine/installation/binaries/#check-runtime-dependencies) in the Docker documentation\.
**Note**  
For a better experience, we recommend the Docker version that ships with and is tested with the corresponding Amazon ECS agent version that you're using\. For more information, see [Amazon ECS Container Agent Versions](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/container_agent_versions.html) in the *Amazon Elastic Container Service Developer Guide*\.

Recommended

 
+ An initialization and nanny process to run and monitor the Amazon ECS agent\. The Amazon ECS optimized AMI uses the `ecs-init` upstart process, and other operating systems might use `systemd`\. To view several example user data configuration scripts that use `systemd` to start and monitor the Amazon ECS container agent, see [Example container instance User Data Configuration Scripts](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/example_user_data_scripts.html) in the *Amazon Elastic Container Service Developer Guide*\. For more information about `ecs-init`, see the [`ecs-init` project](https://github.com/aws/amazon-ecs-init) on GitHub\. At a minimum, managed compute environments require the Amazon ECS agent to start at boot\. If the Amazon ECS agent isn't running on your compute resource, then it can't accept jobs from AWS Batch\. 

The Amazon ECS optimized AMI is preconfigured with these requirements and recommendations\. We recommend that you use the Amazon ECS optimized AMI or an Amazon Linux AMI with the `ecs-init` package installed for your compute resources\. You should choose another AMI if your application requires a specific operating system or a Docker version that's not yet available in those AMIs\. For more information, see [Amazon ECS\-Optimized AMI](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-optimized_AMI.html) in the *Amazon Elastic Container Service Developer Guide*\.