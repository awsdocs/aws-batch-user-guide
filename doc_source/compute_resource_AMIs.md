# Compute resource AMIs<a name="compute_resource_AMIs"></a>

By default, AWS Batch managed compute environments use a recent, approved version of the Amazon ECS optimized AMI for compute resources\. However, you might want to create your own AMI to use for your managed and unmanaged compute environments\. If you require any of the following, we recommend you create your own AMI:
+ Increasing the storage size of your AMI root or data volumes
+ Adding instance storage volumes for supported Amazon EC2 instance types
+ Customizing the Amazon ECS container agent
+ Customizing Docker
+ Configuring a GPU workload AMI to allow containers to access GPU hardware on supported Amazon EC2 instance types

**Note**  
After a compute environment is created, AWS Batch doesn't upgrade the AMIs in the compute environment\. AWS Batch also doesn't update the AMIs in your compute environment when a newer version of the Amazon ECS optimized AMI is available\. You're responsible for the management of the guest operating system\. This includes any updates and security patches\. You're also responsible for any additional application software or utilities that you install on the compute resources\. To use a new AMI for your AWS Batch jobs, do the following:  
Create a new compute environment with the new AMI\.
Add the compute environment to an existing job queue\.
Remove the earlier compute environment from your job queue\.
Delete the earlier compute environment\.
In April 2022, AWS Batch added enhanced support for updating compute environments\. For more information, see [Updating compute environments](updating-compute-environments.md)\. To use the enhanced updating of compute environments to update AMIs, follow these rules:  
Either don't set the service role \([https://docs.aws.amazon.com/batch/latest/APIReference/API_CreateComputeEnvironment.html#Batch-CreateComputeEnvironment-request-serviceRole](https://docs.aws.amazon.com/batch/latest/APIReference/API_CreateComputeEnvironment.html#Batch-CreateComputeEnvironment-request-serviceRole)\) parameter or set it to the **AWSServiceRoleForBatch** service\-linked role\.
Set the allocation strategy \([https://docs.aws.amazon.com/batch/latest/APIReference/API_ComputeResource.html#Batch-Type-ComputeResource-allocationStrategy](https://docs.aws.amazon.com/batch/latest/APIReference/API_ComputeResource.html#Batch-Type-ComputeResource-allocationStrategy)\) parameter to `BEST_FIT_PROGRESSIVE` or `SPOT_CAPACITY_OPTIMIZED`\.
Set the update to latest image version \([https://docs.aws.amazon.com/batch/latest/APIReference/API_ComputeResourceUpdate.html#Batch-Type-ComputeResourceUpdate-updateToLatestImageVersion](https://docs.aws.amazon.com/batch/latest/APIReference/API_ComputeResourceUpdate.html#Batch-Type-ComputeResourceUpdate-updateToLatestImageVersion)\) parameter to `true`\.
Don't specify an AMI ID in [https://docs.aws.amazon.com/batch/latest/APIReference/API_ComputeResourceUpdate.html#Batch-Type-ComputeResourceUpdate-imageId](https://docs.aws.amazon.com/batch/latest/APIReference/API_ComputeResourceUpdate.html#Batch-Type-ComputeResourceUpdate-imageId), [https://docs.aws.amazon.com/batch/latest/APIReference/API_Ec2Configuration.html#Batch-Type-Ec2Configuration-imageIdOverride](https://docs.aws.amazon.com/batch/latest/APIReference/API_Ec2Configuration.html#Batch-Type-Ec2Configuration-imageIdOverride) \(in [https://docs.aws.amazon.com/batch/latest/APIReference/API_Ec2Configuration.html](https://docs.aws.amazon.com/batch/latest/APIReference/API_Ec2Configuration.html)\), or in the launch template \([https://docs.aws.amazon.com/batch/latest/APIReference/API_ComputeResourceUpdate.html#Batch-Type-ComputeResourceUpdate-launchTemplate](https://docs.aws.amazon.com/batch/latest/APIReference/API_ComputeResourceUpdate.html#Batch-Type-ComputeResourceUpdate-launchTemplate)\)\. When you don't specify an AMI ID, AWS Batch selects the latest Amazon ECS optimized AMI that AWS Batch supports at the time the infrastructure update is initiated\. Alternatively, you can specify the AMI ID in the `imageId` or `imageIdOverride` parameters\. Or, you can specify the launch template that's identified by the `LaunchTemplate` properties\. Changing any of these properties starts an infrastructure update\. If the AMI ID is specified in the launch template, the AMI ID can't be replaced by specifying an AMI ID in either the `imageId` or `imageIdOverride` parameters\. The AMI ID can only be replaced by specifying a different launch template\. If the launch template version is set to `$Default` or `$Latest`, the AMI ID can be replaced by setting either a new default version for the launch template \(if `$Default`\) or by adding a new version to the launch template \(if `$Latest`\)\.
If these rules are followed, any update that starts an infrastructure update causes the AMI ID to be re\-selected\. If the [https://docs.aws.amazon.com/batch/latest/APIReference/API_LaunchTemplateSpecification.html#Batch-Type-LaunchTemplateSpecification-version](https://docs.aws.amazon.com/batch/latest/APIReference/API_LaunchTemplateSpecification.html#Batch-Type-LaunchTemplateSpecification-version) setting in the launch template \([https://docs.aws.amazon.com/batch/latest/APIReference/API_ComputeResourceUpdate.html#Batch-Type-ComputeResourceUpdate-launchTemplate](https://docs.aws.amazon.com/batch/latest/APIReference/API_ComputeResourceUpdate.html#Batch-Type-ComputeResourceUpdate-launchTemplate)\) is set to `$Latest` or `$Default`, the latest or default version of the launch template is evaluated up at the time of the infrastructure update, even if the [https://docs.aws.amazon.com/batch/latest/APIReference/API_ComputeResourceUpdate.html#Batch-Type-ComputeResourceUpdate-launchTemplate](https://docs.aws.amazon.com/batch/latest/APIReference/API_ComputeResourceUpdate.html#Batch-Type-ComputeResourceUpdate-launchTemplate) wasn't updated\.

**Topics**
+ [Compute resource AMI specification](#batch-ami-spec)
+ [Creating a compute resource AMI](create-batch-ami.md)
+ [Using a GPU workload AMI](batch-gpu-ami.md)
+ [Amazon Linux deprecation](al1-ami-deprecation.md)

## Compute resource AMI specification<a name="batch-ami-spec"></a>

The basic AWS Batch compute resource AMI specification consists of the following:

Required

 
+ A modern Linux distribution that's running at least version 3\.10 of the Linux kernel on an HVM virtualization type AMI\. Windows containers aren't supported\.
**Important**  
Multi\-node parallel jobs can only run on compute resources that were launched on an Amazon Linux instance with the `ecs-init` package installed\. We recommend that you use the default Amazon ECS optimized AMI when you create your compute environment\. You can do this by not specifying a custom AMI\. For more information, see [Multi\-node parallel jobs](multi-node-parallel-jobs.md)\.
+ The Amazon ECS container agent\. We recommend that you use the latest version\. For more information, see [Installing the Amazon ECS Container Agent](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-agent-install.html) in the *Amazon Elastic Container Service Developer Guide*\.
+ The `awslogs` log driver must be specified as an available log driver with the `ECS_AVAILABLE_LOGGING_DRIVERS` environment variable when the Amazon ECS container agent is started\. For more information, see [Amazon ECS Container Agent Configuration](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-agent-config.html) in the *Amazon Elastic Container Service Developer Guide*\. 
+ A Docker daemon that's running at least version 1\.9, and any Docker runtime dependencies\. For more information, see [Check runtime dependencies](https://docs.docker.com/engine/installation/binaries/#check-runtime-dependencies) in the Docker documentation\.
**Note**  
We recommend the Docker version that ships with and is tested with the corresponding Amazon ECS agent version that you're using\. For more information, see [Amazon ECS Container Agent Versions](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/container_agent_versions.html) in the *Amazon Elastic Container Service Developer Guide*\.

Recommended

 
+ An initialization and nanny process to run and monitor the Amazon ECS agent\. The Amazon ECS optimized AMI uses the `ecs-init` upstart process, and other operating systems might use `systemd`\. For more information and examples, see [Example container instance User Data Configuration Scripts](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/example_user_data_scripts.html) in the *Amazon Elastic Container Service Developer Guide*\. For more information about `ecs-init`, see the [`ecs-init` project](https://github.com/aws/amazon-ecs-init) on GitHub\. At a minimum, managed compute environments require the Amazon ECS agent to start at boot\. If the Amazon ECS agent isn't running on your compute resource, then it can't accept jobs from AWS Batch\. 

The Amazon ECS optimized AMI is preconfigured with these requirements and recommendations\. We recommend that you use the Amazon ECS optimized AMI or an Amazon Linux AMI with the `ecs-init` package that's installed for your compute resources\. Choose another AMI if your application requires a specific operating system or a Docker version that's not yet available in those AMIs\. For more information, see [Amazon ECS\-Optimized AMI](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-optimized_AMI.html) in the *Amazon Elastic Container Service Developer Guide*\.