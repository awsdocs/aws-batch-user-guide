# Compute Environment Parameters<a name="compute_environment_parameters"></a>

Compute environments are split into five basic components: the name, type, and state of the compute environment, the compute resource definition \(if it is a managed compute environment\), and the service role to use to provide IAM permissions to AWS Batch\.

**Topics**
+ [Compute Environment Name](#compute_environment_name)
+ [Type](#compute_environment_type)
+ [State](#compute_environment_state)
+ [Compute Resources](#compute_environment_compute_resources)
+ [Service Role](#compute_environment_service_role)

## Compute Environment Name<a name="compute_environment_name"></a>

`computeEnvironmentName`  
The name for your compute environment\. You can use up to 128 letters \(uppercase and lowercase\), numbers, hyphens, and underscores\.  
Type: String  
Required: Yes

## Type<a name="compute_environment_type"></a>

`type`  
The type of the compute environment\. Choose `MANAGED` to have AWS Batch manage the compute resources that you define\)\. For more information, see [Compute Resources](#compute_environment_compute_resources)\. Choose `UNMANAGED` to manage your own compute resources\.  
Type: String  
Valid values: `MANAGED` \| `UNMANAGED`  
Required: Yes

## State<a name="compute_environment_state"></a>

`state`  
The state of the compute environment\.  
If the state is `ENABLED`, then the AWS Batch scheduler can attempt to place jobs from an associated job queue on the compute resources within the environment\. If the compute environment is managed, then it can scale its instances out or in automatically, based on job queue demand\.  
If the state is `DISABLED`, then the AWS Batch scheduler does not attempt to place jobs within the environment\. Jobs in a `STARTING` or `RUNNING` state continue to progress normally\. Managed compute environments in the `DISABLED` state do not scale out; however, they scale in to the fewest number of instances that satisfies the `minvCpus` value once instances become idle\.  
Type: String  
Valid values: `ENABLED` \| `DISABLED`  
Required: No

## Compute Resources<a name="compute_environment_compute_resources"></a>

`computeResources`  
Details of the compute resources managed by the compute environment\.   
Type: [ComputeResource](https://docs.aws.amazon.com/batch/latest/APIReference/API_ComputeResource.html) object  
Required: this parameter is required for managed compute environments    
`type`  
The type of compute environment\. Use this parameter to specify whether to use Amazon EC2 On\-Demand Instances or Amazon EC2 Spot Instances in your compute environment\. If you choose `SPOT`, you must also specify an Amazon EC2 Spot Fleet role with the `spotIamFleetRole` parameter\. For more information, see [Amazon EC2 Spot Fleet Role](spot_fleet_IAM_role.md)\.  
Valid values: `EC2` \| `SPOT`  
Required: Yes  
`allocationStrategy`  
The allocation strategy to use for the compute resource in case not enough instances of the best fitting instance type can be allocated\. This could be due to availability of the instance type in the region or [Amazon EC2 service limits](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-resource-limits.html)\. If this is not specified, the default is `BEST_FIT`, which will use only the best fitting instance type, waiting for additional capacity if it's not available\. This allocation strategy keeps costs lower but can limit scaling\. `BEST_FIT_PROGRESSIVE` will select additional instance types that are large enough to meet the requirements of the jobs in the queue, with a preference for instance types with a lower cost per unit vCPU\. `SPOT_CAPACITY_OPTIMIZED` is only available for Spot Instance compute resources and will select additional instance types that are large enough to meet the requirements of the jobs in the queue, with a preference for instance types that are less likely to be interrupted\.  
Valid values: `BEST_FIT` \| `BEST_FIT_PROGRESSIVE` \| `SPOT_CAPACITY_OPTIMIZED`  
Required: No  
`minvCpus`  
The minimum number of Amazon EC2 vCPUs that an environment should maintain \(even if a compute environment is `DISABLED`\)\.  
Type: Integer  
Required: Yes  
`maxvCpus`  
The maximum number of Amazon EC2 vCPUs that an environment can reach\.  
With both `BEST_FIT_PROGRESSIVE` and `SPOT_CAPACITY_OPTIMIZED` allocation strategies, AWS Batch may need to go above `maxvCpus` to meet your capacity requirements\. In this event, AWS Batch will never go above `maxvCpus` by more than a single instance \(e\.g\., no more than a single instance from among those specified in your compute environment\)\.
Type: Integer  
Required: Yes  
`desiredvCpus`  
The desired number of Amazon EC2 vCPUS in the compute environment\. AWS Batch modifies this value between the minimum and maximum values, based on job queue demand\.  
Type: Integer  
Required: No  
`instanceTypes`  
The instance types that may be launched\. You can specify instance families to launch any instance type within those families \(for example, `c5`, `c5n`, or `p3`\), or you can specify specific sizes within a family \(such as `c5.8xlarge`\)\. Note that metal instance types are not in the instance families \(for example `c5` does not include `c5.metal`\.\) You can also choose `optimal` to pick instance types \(from the C, M, and R instance families\) on the fly that match the demand of your job queues\.  
When you create a compute environment, the instance types that you select for the compute environment must share the same architecture\. For example, you can't mix x86 and ARM instances in the same compute environment\.
Type: Array of strings  
Required: yes  
`imageId`  
The Amazon Machine Image \(AMI\) ID used for instances launched in the compute environment\.  
The AMI that you choose for a compute environment must match the architecture of the instance types that you intend to use for that compute environment\. For example, if your compute environment uses A1 instance types, the compute resource AMI that you choose must support ARM instances\. Amazon ECS vends both x86 and ARM versions of the Amazon ECS\-optimized Amazon Linux 2 AMI\. For more information, see [Amazon ECS\-optimized Amazon Linux 2 AMI](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/al2ami.html) in the *Amazon Elastic Container Service Developer Guide*\.
Type: String  
Required: No  
`subnets`  
The VPC subnets into which the compute resources are launched\. These subnets must be within the same VPC\.  
Type: Array of strings  
Required: Yes  
`securityGroupIds`  
The EC2 security groups to associate with the instances launched in the compute environment\.   
Type: Array of strings  
Required: Yes  
`ec2KeyPair`  
The EC2 key pair that is used for instances launched in the compute environment\. You can use this key pair to log in to your instances with SSH\.  
Type: String  
Required: No  
`instanceRole`  
The Amazon ECS instance profile to attach to Amazon EC2 instances in a compute environment\. You can specify the short name or full Amazon Resource Name \(ARN\) of an instance profile\. For example, `ecsInstanceRole` or `arn:aws:iam::aws_account_id:instance-profile/ecsInstanceRole`\. For more information, see [Amazon ECS Instance Role](instance_IAM_role.md)\.  
Type: String  
Required: Yes  
`tags`  
Key\-value pair tags to be applied to instances that are launched in the compute environment\. For example, you can specify `"Name": "AWS Batch Instance - C4OnDemand"` as a tag so that each instance in your compute environment has that name\. This is helpful for recognizing your AWS Batch instances in the Amazon EC2 console\.  
Type: String to string map  
Required: No  
`bidPercentage`  
The maximum percentage that a Spot Instance price can be when compared with the On\-Demand price for that instance type before instances are launched\. For example, if your maximum percentage is 20%, then the Spot price must be below 20% of the current On\-Demand price for that EC2 instance\. You always pay the lowest \(market\) price and never more than your maximum percentage\. If you leave this field empty, the default value is 100% of the On\-Demand price\.  
Required: No  
`spotIamFleetRole`  
The Amazon Resource Name \(ARN\) of the Amazon EC2 Spot Fleet IAM role applied to a `SPOT` compute environment\. For more information, see [Amazon EC2 Spot Fleet Role](spot_fleet_IAM_role.md)\.  
To tag your Spot Instances on creation, the Spot Fleet IAM role specified here must use the newer **AmazonEC2SpotFleetTaggingRole** managed policy\. The previously recommended **AmazonEC2SpotFleetRole** managed policy does not have the required permissions to tag Spot Instances\. For more information, see [Spot Instances Not Tagged on Creation](troubleshooting.md#spot-instance-no-tag)\.
Type: String  
Required: This parameter is required for `SPOT` compute environments\.  
`launchTemplate`  
An optional launch template to associate with your compute resources\. To use a launch template, you must specify either the launch template ID or launch template name in the request, but not both\. For more information, see [Launch Template Support](launch-templates.md)\.  
Type: [LaunchTemplateSpecification](https://docs.aws.amazon.com/batch/latest/APIReference/API_LaunchTemplateSpecification.html)  
 object  
Required: No    
`launchTemplateId`  
The ID of the launch template\.  
Type: String  
Required: No  
`launchTemplateName`  
The name of the launch template\.  
Type: String  
Required: No  
`version`  
The version number of the launch template\.  
Type: String  
Required: No

## Service Role<a name="compute_environment_service_role"></a>

`serviceRole`  
The full Amazon Resource Name \(ARN\) of the IAM role that allows AWS Batch to make calls to other AWS services on your behalf\. For more information, see [AWS Batch Service IAM Role](service_IAM_role.md)\.  
Type: String  
Required: Yes