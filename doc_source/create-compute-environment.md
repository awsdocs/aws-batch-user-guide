# Creating a compute environment<a name="create-compute-environment"></a>

Before you can run jobs in AWS Batch, you need to create a compute environment\. You can create a managed compute environment where AWS Batch manages the Amazon EC2 instances or AWS Fargate resources within the environment based on your specifications\. Or, alternatively, you can create an unmanaged compute environment where you handle the Amazon EC2 instance configuration within the environment\.

**Contents**
+ [To create a managed compute environment using AWS Fargate resources](#create-compute-environment-fargate)
+ [To create a managed compute environment using EC2 resources](#create-compute-environment-managed-ec2)
+ [To create an unmanaged compute environment using EC2 resources](#create-compute-environment-unmanaged-ec2)

## To create a managed compute environment using AWS Fargate resources<a name="create-compute-environment-fargate"></a>

1. Open the AWS Batch console at [https://console\.aws\.amazon\.com/batch/](https://console.aws.amazon.com/batch/)\.

1. From the navigation bar, select the Region to use\.

1. In the navigation pane, choose **Compute environments**, **Create**\.

1. Configure the environment\.

   1. For **Compute environment type**, choose **Managed**\.

   1. For **Compute environment name**, specify a unique name for your compute environment\. You can use up to 128 characters\. Valid characters are letters \(both uppercase and lowercase\), numbers, hyphens \(\-\), and underscores \(\_\)\.

   1. Ensure that **Enable compute environment** is selected so that your compute environment can accept jobs from the AWS Batch job scheduler\.

   1. \(Optional\) Expand **Additional settings: service role, instance role, EC2 key pair**\.

      1. For **Service role**, choose to create a new role or use an existing role\. The role allows the AWS Batch service to make calls to the required AWS API operations on your behalf\. For more information, see [AWS Batch Service IAM Role](service_IAM_role.md)\. If you choose to create a new role, the required role \(`AWSBatchServiceRole`\) is created for you\.

      1. For **Instance role**, choose to create a new instance profile or use an existing instance profile that has the required IAM permissions attached\. This instance profile allows the Amazon ECS container instances that are created for your compute environment to make calls to the required AWS API operations on your behalf\. For more information, see [Amazon ECS Instance Role](instance_IAM_role.md)\. If you choose to create a new instance profile, the required role \(`ecsInstanceRole`\) is created for you\.

      1. For **EC2 key pair** choose an existing Amazon EC2 key pair to associate with the instance at launch\. This key pair allows you to connect to your instances with SSH \(ensure that your security group allows incoming traffic on port 22\)\.

1. Configure your Instance configuration\.

   1. For **Provisioning model**, choose **Fargate** to launch Fargate On\-Demand resources or **Fargate Spot** to use Fargate Spot resources\.

   1. For **Maximum vCPUs**, choose the maximum number of vCPUs that your compute environment can scale out to, regardless of job queue demand\.

1. Configure networking\.
**Important**  
Compute resources need access to communicate with the Amazon ECS service endpoint\. This can be through an interface VPC endpoint or through your compute resources having public IP addresses\.  
For more information about interface VPC endpoints, see [Amazon ECS Interface VPC Endpoints \(AWS PrivateLink\)](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/vpc-endpoints.html) in the *Amazon Elastic Container Service Developer Guide*\.  
If you do not have an interface VPC endpoint configured and your compute resources do not have public IP addresses, then they must use network address translation \(NAT\) to provide this access\. For more information, see [NAT Gateways](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) in the *Amazon VPC User Guide*\. For more information, see [Tutorial: Creating a VPC with Public and Private Subnets for Your Compute Environments](create-public-private-vpc.md)\.

   1. For **VPC ID**, choose a VPC where you intend to launch your instances\.

   1. For **Subnets**, choose which subnets in the selected VPC should host your instances\. By default, all subnets within the selected VPC are chosen\.

   1. \(Optional\) Expand **Additional settings: Security groups, EC2 tags**\.

      1. For **Security groups**, choose a security group to attach to your instances\. By default, the default security group for your VPC is chosen\.

1. \(Optional\) In the **Tags** section, you can specify the key and value for each tag to associate with the compute environment\. For more information, see [Tagging your AWS Batch resources](using-tags.md)\.

1. Choose **Create compute environment** to finish\.

## To create a managed compute environment using EC2 resources<a name="create-compute-environment-managed-ec2"></a>

****

1. Open the AWS Batch console at [https://console\.aws\.amazon\.com/batch/](https://console.aws.amazon.com/batch/)\.

1. From the navigation bar, select the Region to use\.

1. In the navigation pane, choose **Compute environments**, **Create**\.

1. Configure the environment\.

   1. For **Compute environment type**, choose **Managed**\.

   1. For **Compute environment name**, specify a unique name for your compute environment\. You can use up to 128 characters\. Valid characters are letters \(both uppercase and lowercase\), numbers, hyphens \(\-\), and underscores \(\_\)\.

   1. Ensure that **Enable compute environment** is selected so that your compute environment can accept jobs from the AWS Batch job scheduler\.

   1. \(Optional\) Expand **Additional settings: service role, instance role, EC2 key pair**\.

      1. For **Service role**, choose to create a new role or use an existing role\. The role allows the AWS Batch service to make calls to the required AWS API operations on your behalf\. For more information, see [AWS Batch Service IAM Role](service_IAM_role.md)\. If you choose to create a new role, the required role \(`AWSBatchServiceRole`\) is created for you\.

      1. For **Instance role**, choose to create a new instance profile or use an existing instance profile that has the required IAM permissions attached\. This instance profile allows the Amazon ECS container instances that are created for your compute environment to make calls to the required AWS API operations on your behalf\. For more information, see [Amazon ECS Instance Role](instance_IAM_role.md)\. If you choose to create a new instance profile, the required role \(`ecsInstanceRole`\) is created for you\.

      1. For **EC2 key pair** choose an existing Amazon EC2 key pair to associate with the instance at launch\. This key pair allows you to connect to your instances with SSH \(verify that your security group allows incoming traffic on port 22\)\.

1. Configure your Instance configuration\.

   1. For **Provisioning model**, choose **On\-Demand** to launch Amazon EC2 On\-Demand Instances or **Spot** to use Amazon EC2 Spot Instances\.

   1. If you chose to use Spot Instances:

      1. \(Optional\) For **Maximum % on\-demand price**, choose the maximum percentage that a Spot Instance price can be when compared with the On\-Demand price for that instance type before instances are launched\. For example, if your maximum price is 20%, then the Spot price must be below 20% of the current On\-Demand price for that EC2 instance\. You always pay the lowest \(market\) price and never more than your maximum percentage\. If you leave this field empty, the default value is 100% of the On\-Demand price\.

   1. For **Minimum vCPUs**, choose the minimum number of EC2 vCPUs that your compute environment should maintain, regardless of job queue demand\.

   1. For **Maximum vCPUs**, choose the maximum number of EC2 vCPUs that your compute environment can scale out to, regardless of job queue demand\.

   1. For **Desired vCPUs**, choose the number of EC2 vCPUs that your compute environment should launch with\. As your job queue demand increases, AWS Batch can increase the desired number of vCPUs in your compute environment and add EC2 instances, up to the maximum vCPUs\. As demand decreases, AWS Batch can decrease the desired number of vCPUs in your compute environment and remove instances, down to the minimum vCPUs\.

   1. For **Allowed instance types**, choose the Amazon EC2 instance types that may be launched\. You can specify instance families to launch any instance type within those families \(for example, `c5`, `c5n`, or `p3`\), or you can specify specific sizes within a family \(such as `c5.8xlarge`\)\. Note that metal instance types aren't in the instance families\. For example, `c5` doesn't include `c5.metal`\. You can also choose `optimal` to select instance types \(from the C, M, and R instance families\) as you need that match the demand of your job queues\.
**Note**  
When you create a compute environment, the instance types that you select for the compute environment must share the same architecture\. For example, you can't mix x86 and ARM instances in the same compute environment\.
**Note**  
AWS Batch will scale GPUs based on the required amount in your job queues\. In order to use GPU scheduling, the compute environment must include instance types from the `p2`, `p3`, `g3`, `g3s`, or `g4` families\.

   1. For **Allocation strategy**, choose the allocation strategy to use when selecting instance types from the list of allowed instance types\. **BEST\_FIT\_PROGRESSIVE** is usually the better choice for EC2 On\-Demand compute environments, and **SPOT\_CAPACITY\_OPTIMIZED** for EC2 Spot compute environments\. For more information, see [Allocation strategies](allocation-strategies.md)\.

   1. \(Optional\) Expand **Additional settings: launch template, user specified AMI**\.

      1. \(Optional\) For **Launch template**, select an existing Amazon EC2 launch template to configure your compute resources; the default version of the template is automatically populated\. For more information, see [Launch template support](launch-templates.md)\.

      1. \(Optional\) For **Launch template version**, enter `$Default`, `$Latest`, or a specific version number to use\.
**Important**  
After the compute environment is created, the launch template version used will not be changed, even if the `$Default` or `$Latest` version for the launch template is updated\. To use a new launch template version, create a new compute environment, add the new compute environment to the existing job queue, remove the old compute environment from the job queue, and delete the old compute environment\.

      1. <a name="enable-custom-ami-step"></a>\(Optional\) Check **Enable user\-specified AMI ID** to use your own custom AMI\. By default, AWS Batch managed compute environments use a recent, approved version of the Amazon ECS\-optimized AMI for compute resources\. You can create and use your own AMI in your compute environment by following the compute resource AMI specification\. For more information, see [Compute resource AMIs](compute_resource_AMIs.md)\.
**Note**  
The AMI that you choose for a compute environment must match the architecture of the instance types that you intend to use for that compute environment\. For example, if your compute environment uses A1 instance types, the compute resource AMI that you choose must support ARM instances\. Amazon ECS vends both x86 and ARM versions of the Amazon ECS optimized Amazon Linux 2 AMI\. For more information, see [Amazon ECS optimized Amazon Linux 2 AMI](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-optimized_AMI.html#ecs-optimized-ami-linux-variants.html) in the *Amazon Elastic Container Service Developer Guide*\.

         1. For **AMI ID**, paste your custom AMI ID and choose **Validate AMI**\.

      1. \(Optional\) For **EC2 configuration** choose **Image type** and **Image ID override** values to provide information for AWS Batch to select Amazon Machine Images \(AMIs\) for instances in the compute environment\. If the **Image ID override** isn't specified for each **Image type**, AWS Batch selects a recent [Amazon ECS optimized AMI](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-optimized_AMI.html)\. If no **Image type** is specified, the default is a **Amazon Linux** for non\-GPU, non\-AWS Graviton instance\. In the future, this default will change to **Amazon Linux 2** for all non\-GPU instances\.  
[Amazon Linux 2](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-optimized_AMI.html#al2ami)  
 Default for all AWS Graviton\-based instance families \(for example, `C6g`, `M6g`, `R6g`, and `T4g`\) and can be used for all non\-GPU instance types\.  
[Amazon Linux 2 \(GPU\)](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-optimized_AMI.html#gpuami)  
Default for all GPU instance families \(for example `P4` and `G4`\) and can be used for all non\-AWS Graviton\-based instance types\.  
[Amazon Linux](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-optimized_AMI.html#alami)  
Default for all non\-GPU, non\-AWS Graviton instance families\. Amazon Linux is reaching the end\-of\-life of standard support\. For more information, see [Amazon Linux AMI](http://aws.amazon.com/amazon-linux-ami/)\.

1. Configure networking\.
**Important**  
Compute resources need access to communicate with the Amazon ECS service endpoint\. This can be through an interface VPC endpoint or through your compute resources having public IP addresses\.  
For more information about interface VPC endpoints, see [Amazon ECS Interface VPC Endpoints \(AWS PrivateLink\)](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/vpc-endpoints.html) in the *Amazon Elastic Container Service Developer Guide*\.  
If you do not have an interface VPC endpoint configured and your compute resources do not have public IP addresses, then they must use network address translation \(NAT\) to provide this access\. For more information, see [NAT Gateways](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) in the *Amazon VPC User Guide*\. For more information, see [Tutorial: Creating a VPC with Public and Private Subnets for Your Compute Environments](create-public-private-vpc.md)\.

   1. For **VPC ID**, choose a VPC into which to launch your instances\.

   1. For **Subnets**, choose which subnets in the selected VPC should host your instances\. By default, all subnets within the selected VPC are chosen\.

   1. \(Optional\) Expand **Additional settings: Security groups, EC2 tags**\.

      1. For **Security groups**, choose a security group to attach to your instances\. By default, the default security group for your VPC is chosen\.

      1. <a name="compute-environment-ec2-tag-step"></a>\(Optional\) In the **EC2 tags**, you can tag the Amazon EC2 instances used by your On\-Demand Instances\. For example, you can specify `"Name": "AWS Batch Instance - C4OnDemand"` as a tag so that each instance in your compute environment has that name\. This is helpful for recognizing your AWS Batch instances in the Amazon EC2 console\.
**Note**  
**EC2 tags** isn't available when using either Spot or Fargate provisioning models\.

1. <a name="compute-environment-tag-step"></a>\(Optional\) In the **Tags** section, you can specify the key and value for each tag to associate with the compute environment\. For more information, see [Tagging your AWS Batch resources](using-tags.md)\.

1. Choose **Create compute environment** to finish\.

## To create an unmanaged compute environment using EC2 resources<a name="create-compute-environment-unmanaged-ec2"></a>

****

1. Open the AWS Batch console at [https://console\.aws\.amazon\.com/batch/](https://console.aws.amazon.com/batch/)\.

1. From the navigation bar, select the Region to use\.

1. In the navigation pane, choose **Compute environments**, **Create environment**\.

1. For **Compute environment type**, choose **Unmanaged**\.

1. For **Compute environment name**, specify a unique name for your compute environment\. You can use up to 128 letters \(uppercase and lowercase\), numbers, hyphens, and underscores\.

1. For **Service role**, choose to create a new role or use an existing role that allows the AWS Batch service to make calls to the required AWS API operations on your behalf\. For more information, see [AWS Batch Service IAM Role](service_IAM_role.md)\. If you choose to create a new role, the required role \(`AWSBatchServiceRole`\) is created for you\.

1. Ensure that **Enable compute environment** is selected so that your compute environment can accept jobs from the AWS Batch job scheduler\.

1. Choose **Create** to finish\.

1. \(Optional\) Retrieve the Amazon ECS cluster ARN for the associated cluster\. The following AWS CLI command provides the Amazon ECS cluster ARN for a compute environment:

   ```
   aws batch describe-compute-environments --compute-environments unmanagedCE --query computeEnvironments[].ecsClusterArn
   ```

1. \(Optional\) Launch container instances into the associated Amazon ECS cluster\. For more information, see [Launching an Amazon ECS container instance](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/launch_container_instance.html) in the *Amazon Elastic Container Service Developer Guide*\. When you launch your compute resources, specify the Amazon ECS cluster ARN that the resources should register with the following Amazon EC2 user data\. Replace *ecsClusterArn* with the cluster ARN you obtained with the previous command\.

   ```
   #!/bin/bash
   echo "ECS_CLUSTER=ecsClusterArn" >> /etc/ecs/ecs.config
   ```
**Note**  
Your unmanaged compute environment does not have any compute resources until you launch them manually\.