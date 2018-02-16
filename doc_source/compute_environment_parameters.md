# Compute Environment Parameters<a name="compute_environment_parameters"></a>

Compute environments are split into five basic components: the name, type, and state of the compute environment, the compute resource definition \(if it is a managed compute environment\), and the service role to use to provide IAM permissions to AWS Batch\.


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
Valid values: `MANAGED` | `UNMANAGED`  
Required: Yes

## State<a name="compute_environment_state"></a>

`state`  
The state of the compute environment\.  
If the state is `ENABLED`, then the AWS Batch scheduler can attempt to place jobs from an associated job queue on the compute resources within the environment\. If the compute environment is managed, then it can scale its instances out or in automatically, based on job queue demand\.  
If the state is `DISABLED`, then the AWS Batch scheduler does not attempt to place jobs within the environment\. Jobs in a `STARTING` or `RUNNING` state continue to progress normally\. Managed compute environments in the `DISABLED` state do not scale out; however, they scale in when instances are idle and nearing the end of an Amazon EC2 billing hour\.   
Type: String  
Valid values: `ENABLED` | `DISABLED`  
Required: No

## Compute Resources<a name="compute_environment_compute_resources"></a>

`computeResources`  
Details of the compute resources managed by the compute environment\.   
Type: [ComputeResource](http://docs.aws.amazon.com/batch/latest/APIReference/API_ComputeResource.html) object  
Required: this parameter is required for managed compute environments    
`type`  
The type of compute environment\. Use this parameter to specify whether to use Amazon EC2 On\-Demand Instances or Amazon EC2 Spot Instances in your compute environment\. If you choose `SPOT`, you must also specify an Amazon EC2 Spot Fleet role with the `spotIamFleetRole` parameter\. For more information, see [Amazon EC2 Spot Fleet Role](spot_fleet_IAM_role.md)\.  
Valid values: `EC2` | `SPOT`  
Required: Yes  
`minvCpus`  
The minimum number of EC2 vCPUs that an environment should maintain\.  
Type: Integer  
Required: Yes  
`maxvCpus`  
The maximum number of EC2 vCPUs that an environment can reach\.   
Type: Integer  
Required: Yes  
`desiredvCpus`  
The desired number of EC2 vCPUS in the compute environment\. AWS Batch modifies this value between the minimum and maximum values, based on job queue demand\.  
Type: Integer  
Required: No  
`instanceTypes`  
The instance types that may be launched\. You can specify instance families to launch any instance type within those families \(for example, `c4` or `p3`\), or you can specify specific sizes within a family \(such as `c4.8xlarge`\)\. You can also choose `optimal` to pick instance types \(from the latest C, M, and R instance families\) on the fly that match the demand of your job queues\.  
Type: Array of strings  
Valid values: `"optimal", "m3", "m4", "c3", "c4", "r3", "r4", "i2", "i3", "d2", "g2", "g3", "p2", "p3", "x1", "f1", "m3.medium", "m3.large", "m3.xlarge", "m3.2xlarge", "m4.large", "m4.xlarge", "m4.2xlarge", "m4.4xlarge", "m4.10xlarge", "m4.16xlarge", "c3.large", "c3.xlarge", "c3.2xlarge", "c3.4xlarge", "c3.8xlarge", "c4.large", "c4.xlarge", "c4.2xlarge", "c4.4xlarge", "c4.8xlarge", "r3.large", "r3.xlarge", "r3.2xlarge", "r3.4xlarge", "r3.8xlarge", "r4.large", "r4.xlarge", "r4.2xlarge", "r4.4xlarge", "r4.8xlarge", "i2.xlarge", "i2.2xlarge", "i2.4xlarge", "i2.8xlarge", "i3.xlarge", "i3.2xlarge", "i3.4xlarge", "i3.8xlarge", "i3.16xlarge", "d2.xlarge", "d2.4xlarge", "d2.8xlarge", "g2.2xlarge", "g2.8xlarge", "g3.4xlarge", "g3.8xlarge", "g3.16xlarge", "p2.xlarge", "p2.8xlarge", "p2.16xlarge", "p3.2xlarge", "p3.8xlarge", "p3.16xlarge", "x1.16xlarge", "x1.32xlarge", "f1.2xlarge", "f1.16xlarge"`  
Required: yes  
`imageId`  
The Amazon Machine Image \(AMI\) ID used for instances launched in the compute environment\.  
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
The maximum percentage that a Spot Instance price must be when compared with the On\-Demand price for that instance type before instances are launched\. For example, if your bid percentage is 20%, then the Spot price must be below 20% of the current On\-Demand price for that EC2 instance\.  
Required: This parameter is required for `SPOT` compute environments\.  
`spotIamFleetRole`  
The Amazon Resource Name \(ARN\) of the Amazon EC2 Spot Fleet IAM role applied to a `SPOT` compute environment\. For more information, see [Amazon EC2 Spot Fleet Role](spot_fleet_IAM_role.md)\.  
To tag your Spot Instances on creation, the Spot Fleet IAM role specified here must use the newer **AmazonEC2SpotFleetTaggingRole** managed policy\. The previously recommended **AmazonEC2SpotFleetRole** managed policy does not have the required permissions to tag Spot Instances\. For more information, see [Spot Instances Not Tagged on Creation](troubleshooting.md#spot-instance-no-tag)\.
Type: String  
Required: This parameter is required for `SPOT` compute environments\.

## Service Role<a name="compute_environment_service_role"></a>

`serviceRole`  
The full Amazon Resource Name \(ARN\) of the IAM role that allows AWS Batch to make calls to other AWS services on your behalf\. For more information, see [AWS Batch Service IAM Role](service_IAM_role.md)\.  
Type: String  
Required: Yes