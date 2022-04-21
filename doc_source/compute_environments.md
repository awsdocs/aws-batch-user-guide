# Compute environment<a name="compute_environments"></a>

Job queues are mapped to one or more compute environments\. Compute environments contain the Amazon ECS container instances that are used to run containerized batch jobs\. A specific compute environment can also be mapped to one or more than one job queue\. Within a job queue, the associated compute environments each have an order that's used by the scheduler to determine where jobs that are ready to be run should run\. If the first compute environment has a status of `VALID` and has available resources, the job is scheduled to a container instance within that compute environment\. If the first compute environment has a status of `INVALID` or can't provide a suitable compute resource, the scheduler attempts to run the job on the next compute environment\.

**Note**  
AWS Batch does not support Windows containers, on either Fargate or EC2 resources\.

**Topics**
+ [Managed compute environments](#managed_compute_environments)
+ [Unmanaged compute environments](#unmanaged_compute_environments)
+ [Compute resource AMIs](compute_resource_AMIs.md)
+ [Launch template support](launch-templates.md)
+ [Creating a compute environment](create-compute-environment.md)
+ [Compute environment template](compute-environment-template.md)
+ [Compute environment parameters](compute_environment_parameters.md)
+ [EC2 Configurations](ec2-configurations.md)
+ [Allocation strategies](allocation-strategies.md)
+ [Updating compute environments](updating-compute-environments.md)
+ [Compute Resource Memory Management](memory-management.md)

## Managed compute environments<a name="managed_compute_environments"></a>

You can use managed compute environments to meet business requirements\. In a managed compute environment, AWS Batch helps you to manage the capacity and instance types of the compute resources within the environment\. This is based on the compute resource specification that you define when you create the compute environment\. You can choose either to use EC2 On\-Demand Instances and EC2 Spot Instances\. Or, you can alternatively use Fargate and Fargate Spot capacity in your managed compute environment\. You can optionally set a maximum price so that Spot Instances only launch when the Spot Instance price is under a specified percentage of the On\-Demand price\.

Managed compute environments launch Amazon ECS container instances into the VPC and subnets that you specify when you create the compute environment\. Amazon ECS container instances need external network access to communicate with the Amazon ECS service endpoint\. Some subnets don't provide container instances with public IP addresses\. If your container instances don't have public IP addresses, they must use network address translation \(NAT\) to gain this access\. For more information, see [NAT gateways](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) in the *Amazon VPC User Guide*\. For more information about how to create a VPC, see [Tutorial: Creating a VPC with Public and Private Subnets for Your Compute Environments](create-public-private-vpc.md)\.

By default, AWS Batch managed compute environments use a recent, approved version of the Amazon ECS optimized AMI for compute resources\. However, you might want to create your own AMI to use for your managed compute environments for various reasons\. For more information, see [Compute resource AMIs](compute_resource_AMIs.md)\.

**Note**  
AWS Batch doesn't automatically upgrade the AMIs in a compute environment after it's created\. For example, it also doesn't update the AMIs in your compute environment when a newer version of the Amazon ECS optimized AMI is available\. You're responsible for the management of the guest operating system\. This includes any updates and security patches\. You're also responsible for any additional application software or utilities that you install on the compute resources\. There are two ways to use a new AMI for your AWS Batch jobs\. The original method is to complete these steps:  
Create a new compute environment with the new AMI\.
Add the compute environment to an existing job queue\.
Remove the earlier compute environment from your job queue\.
Delete the earlier compute environment\.
In April 2022, AWS Batch added enhanced support for updating compute environments\. For more information, see [Updating compute environments](updating-compute-environments.md)\. To use the enhanced updating of compute environments to update AMIs, follow these rules:  
Either do not set the service role \([https://docs.aws.amazon.com/batch/latest/APIReference/API_CreateComputeEnvironment.html#Batch-CreateComputeEnvironment-request-serviceRole](https://docs.aws.amazon.com/batch/latest/APIReference/API_CreateComputeEnvironment.html#Batch-CreateComputeEnvironment-request-serviceRole)\) parameter or set it to the **AWSBatchServiceRole** service\-linked role\.
Set the allocation strategy \([https://docs.aws.amazon.com/batch/latest/APIReference/API_ComputeResource.html#Batch-Type-ComputeResource-allocationStrategy](https://docs.aws.amazon.com/batch/latest/APIReference/API_ComputeResource.html#Batch-Type-ComputeResource-allocationStrategy)\) parameter to `BEST_FIT_PROGRESSIVE` or `SPOT_CAPACITY_OPTIMIZED`\.
Set the update to latest image version \([https://docs.aws.amazon.com/batch/latest/APIReference/API_ComputeResourceUpdate.html#Batch-Type-ComputeResourceUpdate-updateToLatestImageVersion](https://docs.aws.amazon.com/batch/latest/APIReference/API_ComputeResourceUpdate.html#Batch-Type-ComputeResourceUpdate-updateToLatestImageVersion)\) parameter to `true`\.
Do not specify an AMI ID in [https://docs.aws.amazon.com/batch/latest/APIReference/API_ComputeResourceUpdate.html#Batch-Type-ComputeResourceUpdate-imageId](https://docs.aws.amazon.com/batch/latest/APIReference/API_ComputeResourceUpdate.html#Batch-Type-ComputeResourceUpdate-imageId), [https://docs.aws.amazon.com/batch/latest/APIReference/API_Ec2Configuration.html#Batch-Type-Ec2Configuration-imageIdOverride](https://docs.aws.amazon.com/batch/latest/APIReference/API_Ec2Configuration.html#Batch-Type-Ec2Configuration-imageIdOverride) \(in [https://docs.aws.amazon.com/batch/latest/APIReference/API_Ec2Configuration.html](https://docs.aws.amazon.com/batch/latest/APIReference/API_Ec2Configuration.html)\), or in the launch template \([https://docs.aws.amazon.com/batch/latest/APIReference/API_ComputeResourceUpdate.html#Batch-Type-ComputeResourceUpdate-launchTemplate](https://docs.aws.amazon.com/batch/latest/APIReference/API_ComputeResourceUpdate.html#Batch-Type-ComputeResourceUpdate-launchTemplate)\)\. In that case AWS Batch will select the latest Amazon ECS optimized AMI supported by AWS Batch at the time the infrastructure update is initiated\. Alternatively you can specify the AMI ID in the `imageId` or `imageIdOverride` parameters, or the launch template identified by the `LaunchTemplate` properties\. Changing any of these properties will trigger an infrastructure update\. If the AMI ID is specified in the launch template, it can not be replaced by specifying an AMI ID in either the `imageId` or `imageIdOverride` parameters\. It can only be replaced by specifying a different launch template, or if the launch template version is set to `$Default` or `$Latest`, by setting either a new default version for the launch template \(if `$Default`\)or by adding a new version to the launch template \(if `$Latest`\)\.
If these rules are followed, any update that triggers an infrastructure update will cause the AMI ID to be re\-selected\. If the [https://docs.aws.amazon.com/batch/latest/APIReference/API_LaunchTemplateSpecification.html#Batch-Type-LaunchTemplateSpecification-version](https://docs.aws.amazon.com/batch/latest/APIReference/API_LaunchTemplateSpecification.html#Batch-Type-LaunchTemplateSpecification-version) setting in the launch template \([https://docs.aws.amazon.com/batch/latest/APIReference/API_ComputeResourceUpdate.html#Batch-Type-ComputeResourceUpdate-launchTemplate](https://docs.aws.amazon.com/batch/latest/APIReference/API_ComputeResourceUpdate.html#Batch-Type-ComputeResourceUpdate-launchTemplate)\) is set to `$Latest` or `$Default`, the latest or default version of the launch template will be evaluated up at the time of the infrastructure update, even if the [https://docs.aws.amazon.com/batch/latest/APIReference/API_ComputeResourceUpdate.html#Batch-Type-ComputeResourceUpdate-launchTemplate](https://docs.aws.amazon.com/batch/latest/APIReference/API_ComputeResourceUpdate.html#Batch-Type-ComputeResourceUpdate-launchTemplate) was not updated\.

## Unmanaged compute environments<a name="unmanaged_compute_environments"></a>

In an unmanaged compute environment, you manage your own compute resources\. You must verify that the AMI you use for your compute resources meets the Amazon ECS container instance AMI specification\. For more information, see [Compute resource AMI specification](compute_resource_AMIs.md#batch-ami-spec) and [Creating a compute resource AMI](create-batch-ami.md)\.

**Note**  
AWS Fargate resources aren't supported in unmanaged compute environments\.

After you created your unmanaged compute environment, use the [DescribeComputeEnvironments](https://docs.aws.amazon.com/batch/latest/APIReference/API_DescribeComputeEnvironments.html) API operation to view the compute environment details\. Find the Amazon ECS cluster that's associated with the environment and then manually launch your container instances into that Amazon ECS cluster\.

The following AWS CLI command also provides the Amazon ECS cluster ARN:

```
$ aws batch describe-compute-environments \
    --compute-environments unmanagedCE \
    --query "computeEnvironments[].ecsClusterArn"
```

For more information, see [Launching an Amazon ECS container instance](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/launch_container_instance.html) in the *Amazon Elastic Container Service Developer Guide*\. When you launch your compute resources, specify the Amazon ECS cluster ARN that the resources should register with the following Amazon EC2 user data\. Replace *ecsClusterArn* with the cluster ARN you obtained with the previous command\.

```
#!/bin/bash
echo "ECS_CLUSTER=ecsClusterArn" >> /etc/ecs/ecs.config
```