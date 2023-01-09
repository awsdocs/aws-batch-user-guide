# Creating a compute environment<a name="create-compute-environment"></a>

Before you can run jobs in AWS Batch, you need to create a compute environment\. You can create a managed compute environment where AWS Batch manages the Amazon EC2 instances or AWS Fargate resources within the environment based on your specifications\. Or, alternatively, you can create an unmanaged compute environment where you handle the Amazon EC2 instance configuration within the environment\.

**Contents**
+ [To create a managed compute environment using AWS Fargate resources](#create-compute-environment-fargate)
+ [To create a managed compute environment using EC2 resources](#create-compute-environment-managed-ec2)
+ [To create an unmanaged compute environment using EC2 resources](#create-compute-environment-unmanaged-ec2)
+ [To create a managed compute environment using EKS resources](#create-compute-environment-managed-eks)

## To create a managed compute environment using AWS Fargate resources<a name="create-compute-environment-fargate"></a>

1. Open the AWS Batch console at [https://console\.aws\.amazon\.com/batch/](https://console.aws.amazon.com/batch/)\.

1. From the navigation bar, select the AWS Region to use\.

1. In the navigation pane, choose **Compute environments**\.

1. Choose **Create**\.

1. Configure the compute environment\.

   1. For **Compute environment configuration**, choose **Fargate**\.

   1. For **Name**, specify a unique name for your compute environment\. The name can contain up to 128 characters in length\. It can contain uppercase and lowercase letters, numbers, hyphens \(\-\), and underscores \(\_\)\.

   1. For **Service role**, choose service\-linked role that lets the AWS Batch service to make calls to the required AWS API operations on your behalf\. For example, choose **AWSServiceRoleForBatch**\. For more information, see [Service\-linked role permissions for AWS Batch](using-service-linked-roles.md#slr-permissions)\.

   1. \(Optional\) Expand **Tags**\. To add a tag, choose **Add tag**\. Then, enter a **Key** name and optional **Value**\. Choose **Add tag**\.

   1. Choose **Next page**\.

1. In the **Instance configuration** section:

   1. \(Optional\) For **Use Fargate Spot capacity**, turn on Fargate Spot\. For information about Fargate Spot, see [Using Amazon EC2 Spot and Fargate\_SPOT](https://docs.aws.amazon.com/AmazonECS/latest/bestpracticesguide/ec2-and-fargate-spot.html)\. 

   1. For **Maximum vCPUs**, choose the maximum number of vCPUs that your compute environment can scale out to, regardless of job queue demand\.

   1. Choose **Next page**\.

1. Configure networking\.
**Important**  
Compute resources need access to communicate with the Amazon ECS service endpoint\. This can be through an interface VPC endpoint or through your compute resources having public IP addresses\.  
For more information about interface VPC endpoints, see [Amazon ECS Interface VPC Endpoints \(AWS PrivateLink\)](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/vpc-endpoints.html) in the *Amazon Elastic Container Service Developer Guide*\.  
If you do not have an interface VPC endpoint configured and your compute resources do not have public IP addresses, then they must use network address translation \(NAT\) to provide this access\. For more information, see [NAT gateways](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) in the *Amazon VPC User Guide*\. For more information, see [Create a VPC](get-set-up-for-aws-batch.md#create-a-vpc)\.

   1. For **Virtual Private Cloud \(VPC\) ID**, choose a VPC where you want to launch your instances\.

   1. For **Subnets**, choose the subnets to use\. By default, all subnets within the selected VPC are available\.
**Note**  
AWS Batch on Fargate doesn't currently support Local Zones\. For more information, see [ Amazon ECS clusters in Local Zones, Wavelength Zones, and AWS Outposts](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/cluster-regions-zones.html#clusters-local-zones) in the *Amazon Elastic Container Service Developer Guide*\.

   1. For **Security groups**, choose a security group to attach to your instances\. By default, the default security group for your VPC is chosen\.

   1. Choose **Next page**\.

1. For **Review**, review the configuration steps\. If you need to make changes, choose **Edit**\. When you're finished, choose **Create compute environment**\.

## To create a managed compute environment using EC2 resources<a name="create-compute-environment-managed-ec2"></a>

1. Open the AWS Batch console at [https://console\.aws\.amazon\.com/batch/](https://console.aws.amazon.com/batch/)\.

1. From the navigation bar, select the AWS Region to use\.

1. In the navigation pane, choose **Compute environments**\.

1. Choose **Create**\.

1. Configure the environment\.

   1. For **Compute environment configuration**, choose **Amazon Elastic Compute Cloud \(Amazon EC2\)**\.

   1. For **Orchestration type**, choose **Managed**\.

   1. For **Name**, specify a unique name for your compute environment\. The name can contain up to 128 characters in length\. It can contain uppercase and lowercase letters, numbers, hyphens \(\-\), and underscores \(\_\)\.

   1. \(Optional\) For **Service role**, choose service\-linked role that lets the AWS Batch service make calls to the required AWS API operations on your behalf\. For example, choose **AWSServiceRoleForBatch**\. For more information, see [Service\-linked role permissions for AWS Batch](using-service-linked-roles.md#slr-permissions)\.

   1. For **Instance role**, choose to create a new instance profile or use an existing instance profile that has the required IAM permissions attached\. This instance profile allows the Amazon ECS container instances that are created for your compute environment to make calls to the required AWS API operations on your behalf\. For more information, see [Amazon ECS instance role](instance_IAM_role.md)\. If you choose to create a new instance profile, the required role \(`ecsInstanceRole`\) is created for you\.

   1. \(Optional\) Expand **Tags**\. 

   1. \(Optional\) For **EC2 tags**, choose **Add tag** to add a tag to resources that are launched in the compute environment\. Then, enter a **Key** name and optional **Value**\. Choose **Add tag**\. 

   1. \(Optional\) For **Tags**, choose **Add tag**\. Then, enter a **Key** name and optional **Value**\. Choose **Add tag**\. 

      For more information, see [Tagging your AWS Batch resources](using-tags.md)\.

   1.  Choose **Next page**\.

1. In the **Instance configuration** section:

   1. \(Optional\) For **Enable using Spot instances**, turn on Spot\. For more information, see [Spot Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-spot-instances.html)\. 

   1. \(Spot only\) For **Maximum % on\-demand price**, choose the maximum percentage that a Spot Instance price can be when compared with the On\-Demand price for that instance type before instances are launched\. For example, if your maximum price is 20%, then the Spot price must be less than 20% of the current On\-Demand price for that EC2 instance\. You always pay the lowest \(market\) price and never more than your maximum percentage\. If you leave this field empty, the default value is 100% of the On\-Demand price\.

   1. \(Spot only\) For **Spot fleet role**, choose an existing Amazon EC2 Spot Fleet IAM role to apply to your Spot compute environment\. If you don't already have an existing Amazon EC2 Spot Fleet IAM role, you must create one first\. For more information, see [Amazon EC2 spot fleet role](spot_fleet_IAM_role.md)\.
**Important**  
To tag your Spot Instances on creation, your Amazon EC2 Spot Fleet IAM role must use the newer **AmazonEC2SpotFleetTaggingRole** managed policy\. The **AmazonEC2SpotFleetRole** managed policy doesn't have the required permissions to tag Spot Instances\. For more information, see [Spot Instances not tagged on creation](troubleshooting.md#spot-instance-no-tag) and [Tagging your resources](using-tags.md#tag-resources)\.

   1. For **Minimum vCPUs**, choose the minimum number of EC2 vCPUs that your compute environment maintains, regardless of job queue demand\.

   1. For **Desired vCPUs**, choose the number of EC2 vCPUs that your compute environment launches with\. As your job queue demand increases, AWS Batch can increase the desired number of vCPUs in your compute environment and add EC2 instances, up to the maximum vCPUs\. As demand decreases, AWS Batch can decrease the desired number of vCPUs in your compute environment and remove instances, down to the minimum vCPUs\.

   1. For **Maximum vCPUs**, choose the maximum number of EC2 vCPUs that your compute environment can scale out to, regardless of job queue demand\.

   1. For **Allowed instance types**, choose the Amazon EC2 instance types that can be launched\. You can specify instance families to launch any instance type within those families \(for example, `c5`, `c5n`, or `p3`\)\. Or, you can specify specific sizes within a family \(such as `c5.8xlarge`\)\. Metal instance types aren't in the instance families\. For example, `c5` doesn't include `c5.metal`\. You can also choose `optimal` to select instance types \(from the C4, M4, and R4 instance families\) that match the demand of your job queues\.
**Note**  
When you create a compute environment, the instance types that you select for the compute environment must share the same architecture\. For example, you can't mix x86 and ARM instances in the same compute environment\.
**Note**  
AWS Batch will scale GPUs based on the required amount in your job queues\. To use GPU scheduling, the compute environment must include instance types from the `p2`, `p3`, `p4`, `g3`, `g3s`, `g4`, or `g5` families\.
**Note**  
Currently, `optimal` uses instance types from the C4, M4, and R4 instance families\. In AWS Regions that don't have instance types from those instance families, instance types from the C5, M5, and R5 instance families are used\.

   1. Expand **Additional configuration**\.

   1. \(Optional\) For **Placement group**, enter a placement group name to group resources in the compute environment\.

   1. \(Optional\) For **EC2 key pair**, choose a public and private key pair as security credentials when you connect to the instance\. For more information about Amazon EC2 key pairs, see [Amazon EC2 key pairs and Linux instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html)\. 

   1. For **Allocation strategy**, choose the allocation strategy to use when selecting instance types from the list of allowed instance types\. **BEST\_FIT\_PROGRESSIVE** is usually the better choice for EC2 On\-Demand compute environments, and **SPOT\_CAPACITY\_OPTIMIZED** for EC2 Spot compute environments\. For more information, see [Allocation strategies](allocation-strategies.md)\.

   1. \(Optional\) For **EC2 configuration** choose **Image type** and **Image ID override** values to provide information for AWS Batch to select Amazon Machine Images \(AMIs\) for instances in the compute environment\. If the **Image ID override** isn't specified for each **Image type**, AWS Batch selects a recent [Amazon ECS optimized AMI](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-optimized_AMI.html)\. If no **Image type** is specified, the default is a **Amazon Linux 2** for non\-GPU, non AWS Graviton instance\. 
**Important**  
To use a custom AMI, choose the image type and then enter the custom AMI ID in the **Image ID override** box\.  
[Amazon Linux 2](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-optimized_AMI.html#al2ami)  
 Default for all AWS Graviton\-based instance families \(for example, `C6g`, `M6g`, `R6g`, and `T4g`\) and can be used for all non\-GPU instance types\.  
[Amazon Linux 2 \(GPU\)](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-optimized_AMI.html#gpuami)  
Default for all GPU instance families \(for example `P4` and `G4`\) and can be used for all non AWS Graviton\-based instance types\.  
Amazon Linux  
Can be used for non\-GPU, non AWS Graviton instance families\. The standard support for Amazon Linux AMI has ended\. For more information, see [Amazon Linux AMI](http://aws.amazon.com/amazon-linux-ami/)\.
**Note**  
The AMI that you choose for a compute environment must match the architecture of the instance types that you want to use for that compute environment\. For example, if your compute environment uses A1 instance types, the compute resource AMI that you choose must support Arm instances\. Amazon ECS vends both x86 and Arm versions of the Amazon ECS optimized Amazon Linux 2 AMI\. For more information, see [Amazon ECS optimized Amazon Linux 2 AMI](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-optimized_AMI.html#ecs-optimized-ami-linux-variants.html) in the *Amazon Elastic Container Service Developer Guide*\.

   1. \(Optional\) For **Launch template**, select an existing Amazon EC2 launch template to configure your compute resources\. The default version of the template is automatically populated\. For more information, see [Launch template support](launch-templates.md)\.
**Note**  
In a launch template, you can specify a custom AMI that you created\.

   1. \(Optional\) For **Launch template version**, enter `$Default`, `$Latest`, or a specific version number to use\.
**Important**  
After the compute environment is created, the launch template version used isn't changed even if the `$Default` or `$Latest` version for the launch template is updated\. To use a new launch template version, create a new compute environment, add the new compute environment to the existing job queue, remove the old compute environment from the job queue, and delete the old compute environment\.

   1. Choose **Next page**\.

1. In the **Network configuration** section:
**Important**  
Compute resources need access to communicate with the Amazon ECS service endpoint\. This can be through an interface VPC endpoint or through your compute resources having public IP addresses\.  
For more information about interface VPC endpoints, see [Amazon ECS Interface VPC Endpoints \(AWS PrivateLink\)](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/vpc-endpoints.html) in the *Amazon Elastic Container Service Developer Guide*\.  
If you do not have an interface VPC endpoint configured and your compute resources do not have public IP addresses, then they must use network address translation \(NAT\) to provide this access\. For more information, see [NAT gateways](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) in the *Amazon VPC User Guide*\. For more information, see [Create a VPC](get-set-up-for-aws-batch.md#create-a-vpc)\.

   1. For **Virtual Private Cloud \(VPC\) ID**, choose a VPC where to launch your instances\.

   1. For **Subnets**, choose the subnets to use\. By default, all subnets within the selected VPC are available\.
**Note**  
AWS Batch on Amazon EC2 supports Local Zones\. For more information, see [ Local Zones](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html?icmpid=docs_ec2_console#concepts-local-zones) in the *Amazon EC2 User Guide for Linux Instances* and [ Amazon ECS clusters in Local Zones, Wavelength Zones, and AWS Outposts](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/cluster-regions-zones.html#clusters-local-zones) in the *Amazon Elastic Container Service Developer Guide*\.

   1. \(Optional\) For **Security groups**, choose a security group to attach to your instances\. By default, the default security group for your VPC is chosen\.

1. Choose **Next page**\.

1. For **Review**, review the configuration steps\. If you need to make changes, choose **Edit**\. When you're finished, choose **Create compute environment**\.

## To create an unmanaged compute environment using EC2 resources<a name="create-compute-environment-unmanaged-ec2"></a>

****

1. Open the AWS Batch console at [https://console\.aws\.amazon\.com/batch/](https://console.aws.amazon.com/batch/)\.

1. From the navigation bar, select the AWS Region to use\.

1. On the **Compute Environments** page, choose **Create**\.

1. Configure the environment\.

   1. For **Compute environment configuration**, choose **Amazon Elastic Compute Cloud \(Amazon EC2\)**\.

   1. For **Orchestration type**, choose **Unmanaged**\.

1. For **Name**, specify a unique name for your compute environment\. The name can be up to 128 characters in length\. It can contain uppercase and lowercase letters, numbers, hyphens \(\-\), and underscores \(\_\)\.

1. \(Optional\) For **Service role**, choose a role that lets the AWS Batch service to make calls to the required AWS API operations on your behalf\. For example, choose **AWSBatchServiceRole**\. For more information, see [AWS Batch service IAM role](service_IAM_role.md)\.\.

1. For **Maximum vCPUs**, choose the maximum number of vCPUs that your compute environment can scale out to, regardless of job queue demand\.

1. \(Optional\) Expand **Tags**\. To add a tag, choose **Add tag**\. Then, enter a **Key** name and optional **Value**\. Choose **Add tag**\. For more information, see [Tagging your AWS Batch resources](using-tags.md)\.

1. Choose **Next page**\.

1. For **Review**, review the configuration steps\. If you need to make changes, choose **Edit**\. When you're finished, choose **Create compute environment**\.

## To create a managed compute environment using EKS resources<a name="create-compute-environment-managed-eks"></a>

1. Open the AWS Batch console at [https://console\.aws\.amazon\.com/batch/](https://console.aws.amazon.com/batch/)\.

1. From the navigation bar, select the AWS Region to use\.

1. In the navigation pane, choose **Compute environments**\.

1. Choose **Create**\.

1. For **Compute environment configuration**, choose **Amazon Elastic Kubernetes Service \(Amazon EKS\)**\.

1. For **Name**, specify a unique name for your compute environment\. The name can be up to 128 characters in length\. It can contain uppercase and lowercase letters, numbers, hyphens \(\-\), and underscores \(\_\)\.

1. For **Instance role**, choose an existing instance profile that has the required IAM permissions attached\.
**Note**  
To create a compute environment in the AWS Batch console, choose an instance profile that has the `eks:ListClusters` and `eks:DescribeCluster` permissions\.

1. For **EKS cluster**, choose an existing Amazon EKS cluster\.

1. For **Namespace**, enter a Kubernetes namespace to group your AWS Batch processes in the cluster\.

1. \(Optional\) Expand **Tags**\. Choose **Add tag** and then enter a key\-value pair\.

1. Choose **Next page**\.

1. \(Optional\) For **Use EC2 Spot Instances**, turn on **Enable using Spot instances** to use Amazon EC2 Spot Instances\.

1. \(Spot only\) For **Maximum % on\-demand price**, choose the maximum percentage that a Spot Instance price can be when compared with the On\-Demand price for that instance type before instances are launched\. For example, if your maximum price is 20%, then the Spot price must be less than 20% of the current On\-Demand price for that EC2 instance\. You always pay the lowest \(market\) price and never more than your maximum percentage\. If you leave this field empty, the default value is 100% of the On\-Demand price\.

1. \(Spot only\) For **Spot fleet role**, choose the Amazon EC2 Spot fleet IAM role for the `SPOT` compute environment\.
**Important**  
This role is required if the allocation strategy is set to `BEST_FIT` or not specified\.

1. \(Optional\) For **Minimum vCPUs**, choose the minimum number of vCPUs that your compute environment maintains, regardless of job queue demand\.

1. \(Optional\) For **Maximum vCPUs**, choose the maximum number of vCPUs that your compute environment can scale out to, regardless of job queue demand\.

1. For **Allowed instance types**, choose the Amazon EC2 instance types that can be launched\. You can specify instance families to launch any instance type within those families \(for example, `c5`, `c5n`, or `p3`\)\. Or, you can specify specific sizes within a family \(for example, `c5.8xlarge`\)\. Metal instance types aren't in the instance families\. For example, `c5` doesn't include `c5.metal`\. You can also choose `optimal` to select instance types \(from the C4, M4, and R4 instance families\) because you need that match the demand of your job queues\.
**Note**  
When you create a compute environment, the instance types that you select for the compute environment must share the same architecture\. For example, you can't mix x86 and ARM instances in the same compute environment\.
**Note**  
AWS Batch scales GPUs based on the required amount in your job queues\. To use GPU scheduling, the compute environment must include instance types from the `p2`, `p3`, `p4`, `g3`, `g3s`, `g4`, or `g5` families\.
**Note**  
Currently, `optimal` uses instance types from the C4, M4, and R4 instance families\. In AWS Regions that don't have instance types from those instance families, instance types from the C5, M5, and R5 instance families are used\.

1. \(Optional\) Expand **Additional configuration**\.

   1. \(Optional\) For **Placement group**, enter a placement group name to group resources in the compute environment\.

   1. For **Allocation strategy**, choose **BEST\_FIT\_PROGRESSIVE**\.

   1. \(Optional\) For **Amazon Machine Images \(AMIs\) Configuration**, choose **Add amazon machine images \(amis\) configuration**\. Then, choose an **Image Type**, enter an **Image ID override**, and **Kubernetes version**\.
**Important**  
To use a custom AMI, choose the image type and then enter the custom AMI ID in the **Image ID override** box\.
**Note**  
If the **Image ID override** isn't specified for each **Image type**, AWS Batch selects a recent [Amazon ECS optimized AMI](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-optimized_AMI.html)\. If no **Image type** is specified, the default is a **Amazon Linux 2** for non\-GPU, non AWS Graviton instance\.   

[Amazon Linux 2](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-optimized_AMI.html#al2ami)  
 Default for all AWS Graviton\-based instance families \(for example, `C6g`, `M6g`, `R6g`, and `T4g`\) and can be used for all non\-GPU instance types\.

[Amazon Linux 2 \(GPU\)](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-optimized_AMI.html#gpuami)  
Default for all GPU instance families \(for example, `P4` and `G4`\) and can be used for all non AWS Graviton\-based instance types\. 

   1. \(Optional\) For **Launch template**, choose an existing launch template\.

   1. \(Optional\) For **Launch template version**, enter **$Default**, **$Latest**, or a version number\.

1. Choose **Next page**\.

1. For **Virtual Private Cloud \(VPC\) ID**, choose a VPC where to launch the instances\.

1. For **Subnets**, choose the subnets to use\. By default, all subnets within the selected VPC are available\.
**Note**  
AWS Batch on Amazon EKS supports Local Zones\. For more information, see [Amazon EKS and AWS Local Zones](https://docs.aws.amazon.com/eks/latest/userguide/local-zones.html) in the *Amazon EKS User Guide*\.

1. \(Optional\) For **Security groups**, choose a security group to attach to your instances\. By default, the default security group for your VPC is selected\.

1. Choose **Next page**\.

1. For **Review**, review the configuration steps\. If you need to make changes, choose **Edit**\. When you're finished, choose **Create compute environment**\.