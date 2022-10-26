# Compute environment parameters<a name="compute_environment_parameters"></a>

Compute environments are split into several basic components: the name, type, and state of the compute environment, the compute resource definition \(if it's a managed compute environment\), the Amazon EKS configuration \(if it uses Amazon EKS resources\), the service role to use to provide IAM permissions to AWS Batch, and the tags for the compute environment\.

**Topics**
+ [Compute environment name](#compute_environment_name)
+ [Type](#compute_environment_type)
+ [State](#compute_environment_state)
+ [Compute resources](#compute_environment_compute_resources)
+ [Amazon EKS configuration](#compute_environment_eks_configuration)
+ [Service role](#compute_environment_service_role)
+ [Tags](#compute_environment_tags)

## Compute environment name<a name="compute_environment_name"></a>

`computeEnvironmentName`  
The name for your compute environment\. The name can be up to 128 characters in length\. It can contain uppercase and lowercase letters, numbers, hyphens \(\-\), and underscores \(\_\)\.  
Type: String  
Required: Yes

## Type<a name="compute_environment_type"></a>

`type`  
The type of the compute environment\. Choose `MANAGED` to have AWS Batch manage the EC2 or Fargate compute resources that you define\. For more information, see [Compute resources](#compute_environment_compute_resources)\. Choose `UNMANAGED` to manage your own EC2 compute resources\.  
Type: String  
Valid values: `MANAGED` \| `UNMANAGED`  
Required: Yes

## State<a name="compute_environment_state"></a>

`state`  
The state of the compute environment\.  
If the state is `ENABLED`, the AWS Batch scheduler attempts to place jobs within the environment\. These jobs are from an associated job queue on the compute resources\. If the compute environment is managed, it can scale its instances out or in automatically based on job queue demand\.  
If the state is `DISABLED`, the AWS Batch scheduler doesn't attempt to place jobs within the environment\. Jobs in a `STARTING` or `RUNNING` state continue to progress normally\. Managed compute environments in the `DISABLED` state don't scale out\. However, after instances go idle, they scale in to the smallest number of instances that satisfies the `minvCpus` value\.  
Type: String  
Valid values: `ENABLED` \| `DISABLED`  
Required: No

## Compute resources<a name="compute_environment_compute_resources"></a>

`computeResources`  
Details of the compute resources managed by the compute environment\. For more information, see [Compute environment](compute_environments.md)\.  
Type: [https://docs.aws.amazon.com/batch/latest/APIReference/API_ComputeResource.html](https://docs.aws.amazon.com/batch/latest/APIReference/API_ComputeResource.html) object  
Required: This parameter is required for managed compute environments    
`type`  <a name="compute-environment-compute-resources-type"></a>
The type of compute environment\. You can choose either to use EC2 On\-Demand Instances \(`EC2`\) and EC2 Spot Instances \(`SPOT`\), or to use Fargate capacity \(`FARGATE`\) and Fargate Spot capacity \(`FARGATE_SPOT`\) in your managed compute environment\. If you choose `SPOT`, you must also specify an Amazon EC2 Spot Fleet role with the `spotIamFleetRole` parameter\. For more information, see [Amazon EC2 spot fleet role](spot_fleet_IAM_role.md)\.  
Valid values: `EC2` \| `SPOT` \| `FARGATE` \| `FARGATE_SPOT`  
Required: Yes  
`allocationStrategy`  <a name="compute-environment-compute-resources-allocationStrategy"></a>
The allocation strategy to use for the compute resource if not enough instances of the best fitting EC2 instance type can be allocated\. This might be due to availability of the instance type in the Region or [Amazon EC2 service limits](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-resource-limits.html)\. For more information, see [Allocation strategies](allocation-strategies.md)\.  
This parameter isn't applicable to jobs that run on Fargate resources, and shouldn't be specified\.  
`BEST_FIT` \(default\)  
AWS Batch selects an instance type that best fits the needs of the jobs with a preference for the lowest cost instance type\. If additional instances of the selected instance type aren't available, AWS Batch waits for the additional instances to be available\. If there aren't enough instances available, or if you're hitting [Amazon EC2 service limits](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-resource-limits.html), additional jobs don't run until after currently running jobs are complete\. This allocation strategy keeps costs lower but can limit scaling\. If you're using Spot Fleets with `BEST_FIT`, the Spot Fleet IAM Role must be specified\. Compute resources that use a `BEST_FIT` allocation strategy don't support infrastructure updates and can't update some parameters\. For more information, see [Updating compute environments](updating-compute-environments.md)\.  
`BEST_FIT` isn't supported for compute environments using Amazon EKS resources\.  
`BEST_FIT_PROGRESSIVE`  
Use additional instance types that are large enough to meet the requirements of the jobs in the queue, with a preference for instance types with a lower cost for each unit vCPU\. If additional instances of the previously selected instance types aren't available, AWS Batch selects new instance types\.  
`SPOT_CAPACITY_OPTIMIZED`  
\(Only available for Spot Instance compute resources\) Use additional instance types that are large enough to meet the requirements of the jobs in the queue, with a preference for instance types that are less likely to be interrupted\.
With both `BEST_FIT_PROGRESSIVE` and `SPOT_CAPACITY_OPTIMIZED` strategies using On\-Demand or Spot Instances, and the `BEST_FIT` strategy using Spot Instances, AWS Batch might need to exceed `maxvCpus` to meet your capacity requirements\. In this event, AWS Batch never exceeds `maxvCpus` by more than a single instance\.  
Valid values: `BEST_FIT` \| `BEST_FIT_PROGRESSIVE` \| `SPOT_CAPACITY_OPTIMIZED`  
Required: No  
`minvCpus`  <a name="compute-environment-compute-resources-minvCpus"></a>
The minimum number of Amazon EC2 vCPUs that an environment should maintain \(even if a compute environment is `DISABLED`\)\.  
This parameter isn't applicable to jobs running on Fargate resources, and shouldn't be specified\.
Type: Integer  
Required: No  
`maxvCpus`  <a name="compute-environment-compute-resources-maxvCpus"></a>
The maximum number of Amazon EC2 vCPUs that an environment can reach\.  
With both `BEST_FIT_PROGRESSIVE` and `SPOT_CAPACITY_OPTIMIZED` allocation strategies using On\-Demand or Spot Instances, and the `BEST_FIT` strategy using Spot Instances, AWS Batch might need to exceed `maxvCpus` to meet your capacity requirements\. In this event, AWS Batch never exceeds `maxvCpus` by more than a single instance\. For example, AWS Batch uses no more than a single instance from among those specified in your compute environment\.
Type: Integer  
Required: No  
`desiredvCpus`  <a name="compute-environment-compute-resources-desiredvCpus"></a>
The desired number of Amazon EC2 vCPUS in the compute environment\. AWS Batch modifies this value between the minimum and maximum values based on job queue demand\.  
This parameter isn't applicable to jobs that run on Fargate resources, and shouldn't be specified\.
Type: Integer  
Required: No  
`instanceTypes`  <a name="compute-environment-compute-resources-instanceTypes"></a>
The instance types that can be launched\. This parameter isn't applicable to jobs that run on Fargate resources, and shouldn't be specified\. You can specify instance families to launch any instance type within those families \(for example, `c5`, `c5n`, or `p3`\)\. Or, you can specify specific sizes within a family \(such as `c5.8xlarge`\)\. Note that metal instance types aren't in the instance families \(for example `c5` does not include `c5.metal`\.\) You can also choose `optimal` to select instance types \(from the C4, M4, and R4 instance families\) that match the demand of your job queues\.  
When you create a compute environment, the instance types that you select for the compute environment must share the same architecture\. For example, you can't mix x86 and ARM instances in the same compute environment\.
Currently, `optimal` uses instance types from the C4, M4, and R4 instance families\. In Regions that don't have instance types from those instance families, instance types from the C5, M5, and R5 instance families are used\.
Type: Array of strings  
Required: yes  
`imageId`  <a name="compute-environment-compute-resources-imageId"></a>
*This parameter is deprecated\.*  
The Amazon Machine Image \(AMI\) ID used for instances launched in the compute environment\. This parameter is overridden by the `imageIdOverride` member of the `Ec2Configuration` structure\.  
This parameter isn't applicable to jobs that are running on Fargate resources, and shouldn't be specified\.
The AMI that you choose for a compute environment must match the architecture of the instance types that you intend to use for that compute environment\. For example, if your compute environment uses A1 instance types, the compute resource AMI that you choose must support ARM instances\. Amazon ECS vends both x86 and ARM versions of the Amazon ECS optimized Amazon Linux 2 AMI\. For more information, see [Amazon ECS optimized Amazon Linux 2 AMI](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-optimized_AMI.html#ecs-optimized-ami-linux-variants.html) in the *Amazon Elastic Container Service Developer Guide*\.
Type: String  
Required: No  
`subnets`  <a name="compute-environment-compute-resources-subnets"></a>
The VPC subnets into which the compute resources are launched\. These subnets must be within the same VPC\. Fargate compute resources can contain a maximum of 16 subnets\. For more information, see [VPCs and Subnets](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html) in the *Amazon VPC User Guide*\.  
When updating compute environments, if you provide an empty list of VPC subnets, the resulting behavior differs between Fargate and EC2 compute resources\. For Fargate compute resources, providing an empty list is handled as if this parameter wasn't specified and no change is made\. For EC2 compute resources, providing an empty list removes the VPC subnets from the compute resource\. If you change the VPC subnets, an infrastructure update of the compute environment is required\. This is the case for both Fargate and EC2 compute resources\. For more information, see [Updating compute environments](updating-compute-environments.md)\.  
Type: Array of strings  
Required: Yes  
`securityGroupIds`  <a name="compute-environment-compute-resources-securityGroupIds"></a>
The Amazon EC2 security groups associated with instances launched in the compute environment\. One or more security groups must be specified, either in `securityGroupIds` or using a launch template referenced in `launchTemplate`\. This parameter is required for jobs running on Fargate resources and must contain at least one security group\. \(Fargate doesn't support launch templates\.\) If security groups are specified using both `securityGroupIds` and `launchTemplate`, the values in `securityGroupIds` will be used\.  
When updating compute environments, if you provide an empty list of security groups, the resulting behavior differs between Fargate and EC2 compute resources\. For Fargate compute resources, providing an empty list is handled as if this parameter wasn't specified and no change is made\. For EC2 compute resources, providing an empty list removes the security groups from the compute resource\. If you change the security groups, an infrastructure update of the compute environment is required\. This is the case for both Fargate and EC2 compute resources\. For more information, see [Updating compute environments](updating-compute-environments.md)\.  
Type: Array of strings  
Required: Yes  
`ec2KeyPair`  <a name="compute-environment-compute-resources-ec2KeyPair"></a>
The EC2 key pair that's used for instances launched in the compute environment\. You can use this key pair to log in to your instances with SSH\. When updating a compute environment, if you change the EC2 key pair, an infrastructure update of the compute environment is required\. For more information, see [Updating compute environments](updating-compute-environments.md)\.  
This parameter isn't applicable to jobs that are running on Fargate resources, and shouldn't be specified\.
Type: String  
Required: No  
`instanceRole`  <a name="compute-environment-compute-resources-instanceRole"></a>
The Amazon ECS instance profile to attach to Amazon EC2 instances in a compute environment\. This parameter isn't applicable to jobs that are running on Fargate resources, and shouldn't be specified\. You can specify the short name or full Amazon Resource Name \(ARN\) of an instance profile\. For example, `ecsInstanceRole` or `arn:aws:iam::aws_account_id:instance-profile/ecsInstanceRole`\. For more information, see [Amazon ECS instance role](instance_IAM_role.md)\.  
When updating a compute environment, if you change this setting, an infrastructure update of the compute environment is required\. For more information, see [Updating compute environments](updating-compute-environments.md)\.  
Type: String  
Required: No  
`tags`  <a name="compute-environment-compute-resources-tags"></a>
Key\-value pair tags to be applied to EC2 instances that are launched in the compute environment\. For example, you can specify `"Name": "AWS Batch Instance - C4OnDemand"` as a tag so that each instance in your compute environment has that name\. This is helpful for recognizing your AWS Batch instances in the Amazon EC2 console\. These tags aren't seen when using the AWS Batch​ [https://docs.aws.amazon.com/batch/latest/APIReference/API_ListTagsForResource.html](https://docs.aws.amazon.com/batch/latest/APIReference/API_ListTagsForResource.html) API operation\.  
When updating a compute environment, if you change the EC2 tags, an infrastructure update of the compute environment is required\. For more information, see [Updating compute environments](updating-compute-environments.md)\.  
This parameter isn't applicable to jobs that are running on Fargate resources, and shouldn't be specified\.
Type: String to string map  
Required: No  
`placementGroup`  <a name="compute-environment-compute-resources-placementGroup"></a>
The Amazon EC2 placement group to associate with your compute resources\. This parameter isn't applicable to jobs running on Fargate resources, and shouldn't be specified\. If you intend to submit multi\-node parallel jobs to your compute environment, consider creating a cluster placement group and associate it with your compute resources\. This keeps your multi\-node parallel job on a logical grouping of instances within a single Availability Zone with high network flow potential\. For more information, see [Placement Groups](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/placement-groups.html) in the *Amazon EC2 User Guide for Linux Instances*\.  
This parameter isn't applicable to jobs that are running on Fargate resources, and shouldn't be specified\.
Type: String  
Required: No  
`bidPercentage`  <a name="compute-environment-compute-resources-bidPercentage"></a>
The maximum percentage that an EC2 Spot Instance price can be when compared with the On\-Demand price for that instance type before instances are launched\. For example, if your maximum percentage is 20%, the Spot price must be less than 20% of the current On\-Demand price for that EC2 instance\. You always pay the lowest \(market\) price and never more than your maximum percentage\. If you leave this field empty, the default value is 100% of the On\-Demand price\.  
When updating a compute environment, if you change the bid percentage, an infrastructure update of the compute environment is required\. For more information, see [Updating compute environments](updating-compute-environments.md)\.  
This parameter isn't applicable to jobs that run on Fargate resources, and shouldn't be specified\.
Required: No  
`spotIamFleetRole`  <a name="compute-environment-compute-resources-spotIamFleetRole"></a>
The Amazon Resource Name \(ARN\) of the Amazon EC2 Spot Fleet IAM role applied to a `SPOT` compute environment\. This role is required if the allocation strategy set to `BEST_FIT` or if the allocation strategy isn't specified\. For more information, see [Amazon EC2 spot fleet role](spot_fleet_IAM_role.md)\.  
This parameter isn't applicable to jobs that run on Fargate resources, and shouldn't be specified\.
To tag your Spot Instances on creation, the Spot Fleet IAM role specified here must use the newer **AmazonEC2SpotFleetTaggingRole** managed policy\. The previously recommended **AmazonEC2SpotFleetRole** managed policy doesn't have the required permissions to tag Spot Instances\. For more information, see [Spot Instances not tagged on creation](troubleshooting.md#spot-instance-no-tag)\.
Type: String  
Required: This parameter is required for `SPOT` compute environments\.  
`launchTemplate`  <a name="compute-environment-compute-resources-launchTemplate"></a>
An optional launch template to associate with your compute resources\. This parameter isn't applicable to jobs running on Fargate resources, and shouldn't be specified\. Any other compute resource parameters that you specify in a [https://docs.aws.amazon.com/batch/latest/APIReference/API_CreateComputeEnvironment.html](https://docs.aws.amazon.com/batch/latest/APIReference/API_CreateComputeEnvironment.html) or [https://docs.aws.amazon.com/batch/latest/APIReference/API_UpdateComputeEnvironment.html](https://docs.aws.amazon.com/batch/latest/APIReference/API_UpdateComputeEnvironment.html) API operation override the same parameters in the launch template\. To use a launch template, you must specify either the launch template ID or launch template name in the request, but not both\. For more information, see [Launch template support](launch-templates.md)\.  
When updating a compute environment, to remove the custom launch template and use the default launch template, set `launchTemplateId` or `launchTemplateName` member of the launch template specification to an empty string\. Removing the launch template from a compute environment doesn't remove the AMI specified in the launch template, if it was the one that was used\. To update the AMI selected from a launch template, the `updateToLatestImageVersion` parameter must be set to `true`\. When updating a compute environment, if you change the launch template, an infrastructure update of the compute environment is required\. For more information, see [Updating compute environments](updating-compute-environments.md)\.  
Type: [https://docs.aws.amazon.com/batch/latest/APIReference/API_LaunchTemplateSpecification.html](https://docs.aws.amazon.com/batch/latest/APIReference/API_LaunchTemplateSpecification.html)  
 object  
Required: No    
`launchTemplateId`  <a name="compute-environment-compute-resources-launchTemplateId"></a>
The ID of the launch template\.  
Type: String  
Required: No  
`launchTemplateName`  <a name="compute-environment-compute-resources-launchTemplateName"></a>
The name of the launch template\.  
Type: String  
Required: No  
`version`  <a name="compute-environment-compute-resources-version"></a>
The version number of the launch template, `$Latest`, or `$Default`\.  
If the value is `$Latest`, the latest version of the launch template is used\. If the value is `$Default`, the default version of the launch template is used\. During an infrastructure update, if either `$Latest` or `$Default` was specified for the compute environment, AWS Batch re\-evaluates the launch template version and might use a different version of the launch template\. This is even if the launch template wasn't specified in the update\.  
Default: `$Default`\.  
Type: String  
Required: No  
`ec2Configuration`  <a name="compute-environment-compute-resources-ec2Configuration"></a>
Provides information used to select Amazon Machine Images \(AMIs\) for instances in the EC2 compute environment\. If `Ec2Configuration` isn't specified, the default is [Amazon Linux 2](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-optimized_AMI.html#al2ami) \(`ECS_AL2`\)\. Before March 31, 2021, this default was [Amazon Linux](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-optimized_AMI.html#alami) \(`ECS_AL1`\) for non\-GPU, non AWS Graviton instances\.  
When updating a compute environment, if you change this parameter, an infrastructure update of the compute environment is required\. For more information, see [Updating compute environments](updating-compute-environments.md)\.  
This parameter isn't applicable to jobs that run on Fargate resources, and shouldn't be specified\.
Type: Array of [https://docs.aws.amazon.com/batch/latest/APIReference/API_Ec2Configuration.html](https://docs.aws.amazon.com/batch/latest/APIReference/API_Ec2Configuration.html) objects   
Required: No    
`imageIdOverride`  <a name="compute-environment-compute-resources-imageIdOverride"></a>
The AMI ID used for instances launched in the compute environment that matches the image type\. This setting overrides the `imageId` set in the `computeResource` object\.  
Type: String  
Required: No  
`imageKubernetesVersion`  <a name="compute-environment-compute-resources-imageKubernetesVersion"></a>
The Kubernetes version for the compute environment\. If you don't specify a value, the latest version that AWS Batch supports is used\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 256\.  
Required: No  
`imageType`  <a name="compute-environment-compute-resources-imageType"></a>
The image type to match with the instance type to select an AMI\. The supported values are different for `ECS` and `EKS` resources\.    
ECS  
If the `imageIdOverride` parameter isn't specified, then a recent [Amazon ECS\-optimized Amazon Linux 2 AMI](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-optimized_AMI.html#al2ami) \(`ECS_AL2`\) is used\. If a new image type is specified in an update, but neither an `imageId` nor a `imageIdOverride` parameter is specified, then the latest Amazon ECS optimized AMI for that image type that's supported by AWS Batch is used\.    
ECS\_AL2  
[Amazon Linux 2](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-optimized_AMI.html#al2ami): Default for all non\-GPU instance families\.  
ECS\_AL2\_NVIDIA  
[Amazon Linux 2 \(GPU\)](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-optimized_AMI.html#gpuami): Default for all GPU instance families \(for example `P4` and `G4`\) and can be used for all non AWS Graviton\-based instance types\.  
ECS\_AL1  
[Amazon Linux](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-optimized_AMI.html#alami)\. Amazon Linux has reached the end\-of\-life of standard support\. For more information, see [Amazon Linux AMI](http://aws.amazon.com/amazon-linux-ami/)\.  
EKS  
If the `imageIdOverride` parameter isn't specified, then a recent [Amazon EKS\-optimized Amazon Linux AMI](https://docs.aws.amazon.com/eks/latest/userguide/eks-optimized-ami.html) \(`EKS_AL2`\) is used\. If a new image type is specified in an update, but neither an `imageId` nor a `imageIdOverride` parameter is specified, then the latest Amazon EKS optimized AMI for that image type that AWS Batch supports is used\.    
EKS\_AL2  
[Amazon Linux 2](https://docs.aws.amazon.com/eks/latest/userguide/eks-optimized-ami.html): Default for all non\-GPU instance families\.  
EKS\_AL2\_NVIDIA  
[Amazon Linux 2 \(accelerated\)](https://docs.aws.amazon.com/eks/latest/userguide/eks-optimized-ami.html): Default for all GPU instance families \(for example, `P4` and `G4`\) and can be used for all non AWS Graviton\-based instance types\.
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 256\.  
Required: Yes

## Amazon EKS configuration<a name="compute_environment_eks_configuration"></a>

Configuration for the Amazon EKS cluster that supports the AWS Batch compute environment\. The cluster must exist before the compute environment can be created\.

`eksClusterArn`  
The Amazon Resource Name \(ARN\) of the Amazon EKS cluster\. An example is `arn:aws:eks:us-east-1:123456789012:cluster/ClusterForBatch`\.  
Type: String  
Required: Yes

`kubernetesNamespace`  
The namespace of the Amazon EKS cluster\. AWS Batch manages pods in this namespace\. The value can't left empty or null\. It must be fewer than 64 characters long, can't be set to `default`, can't start with "`kube-`," and must match this regular expression: `^[a-z0-9]([-a-z0-9]*[a-z0-9])?$`\. For more information, see [Namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) in the Kubernetes documentation\.  
Type: String  
Required: Yes

Type: [EksConfiguration](https://docs.aws.amazon.com/batch/latest/APIReference/API_EksConfiguration.html) Object

Required: No

## Service role<a name="compute_environment_service_role"></a>

`serviceRole`  
The full Amazon Resource Name \(ARN\) of the IAM role that allows AWS Batch to make calls to other AWS services on your behalf\. For more information, see [AWS Batch service IAM role](service_IAM_role.md)\. We recommend that you do not specify the service role, in which case AWS Batch uses the **AWSServiceRoleForBatch** service\-linked role\.  
If your account has already created the AWS Batch service\-linked role \(**AWSServiceRoleForBatch**\), that role is used by default for your compute environment unless you specify a role here\. If the AWS Batch service\-linked role doesn't exist in your account, and no role is specified here, the service tries to create the AWS Batch service\-linked role in your account\. For more information about the **AWSServiceRoleForBatch** service\-linked role, see [Service\-linked role permissions for AWS Batch](using-service-linked-roles.md#slr-permissions)\.  
If the compute environment is created using the **AWSServiceRoleForBatch** service\-linked role, it can't be changed to use a regular IAM role\. Likewise, if the compute environment is created with a regular IAM role, it can't be changed to use the **AWSServiceRoleForBatch** service\-linked role\. To update the parameters for the compute environment that require an infrastructure update to change, the **AWSServiceRoleForBatch** service\-linked role must be used\. For more information, see [Updating compute environments](updating-compute-environments.md)\.
If your specified role has a path other than `/`, make sure either to specify the full role ARN \(recommended\) or prefix the role name with the path\.  
Depending on how you created your AWS Batch service role, its ARN might contain the `service-role` path prefix\. When you only specify the name of the service role, AWS Batch assumes that your ARN doesn't use the `service-role` path prefix\. Because of this, we recommend that you specify the full ARN of your service role when you create compute environments\.
Type: String  
Required: No

## Tags<a name="compute_environment_tags"></a>

`tags`  
Key\-value pair tags to associate with the compute environment\. For more information, see [Tagging your AWS Batch resources](using-tags.md)\.  
Type: String to string map  
Required: No