# Tutorial: Creating a VPC with public and private subnets for Your compute environments<a name="create-public-private-vpc"></a>

Compute resources in your compute environments need external network access to communicate with the AWS Batch and Amazon ECS service endpoints\. However, you might have jobs that you would like to run in private subnets\. Creating a VPC with both public and private subnets provides you the flexibility to run jobs in either a public or private subnet\. 

This tutorial guides you through creating a VPC with two public subnets and two private subnets, which are provided with internet access through a NAT gateway\.

## Run the VPC Wizard<a name="run-VPC-wizard"></a>

The VPC wizard automatically creates and configures most of your VPC resources for you\.

**To run the VPC wizard**

1. In the left navigation pane, choose **VPC Dashboard**\.

1. Choose **Launch VPC Wizard**\.

1. Under **VPC settings**, do the following:

   1. Choose **VPC, subnets, etc**\.

   1. For **IPv4 CIDR block**, type the CIDR block if you want a value other than the default\.

   1. For **Availability Zones \(AZs\) **, choose the number of Availability Zones\.

   1. For **Number of public subnets**, choose **2**\. 

      Optional: you can customize the CIDRs for your subnets\.

   1. For **Number of private subnets**, choose **2**\. 

      Optional: you can customize the CIDRs for your subnets\.

   1. For **NAT gateways**, choose the number of NAT gateways to use\.

      If you choose not to use NAT gateways, internet traffic for the public subnets uses the internet gateway\. Private subnets will not have internet access\.

   1. For **VPC endpoints**, choose whether you want an Amazon S3 gateway endpoint\.

1. Choose **Create VPC**\.

1. When the wizard is finished, choose **OK**\. Note the Availability Zone in which your VPC subnets were created\. Your additional subnets should be created in a different Availability Zone\.

   Non\-default subnets, such as those created by the VPC wizard, are not auto\-assigned public IPv4 addresses\. Instances launched in the public subnet must be assigned a public IPv4 address to communicate with the Amazon ECS service endpoint\.

**To modify your public subnet's IPv4 addressing behavior**

1. In the left navigation pane, choose **Subnets**\.

1. Select the public subnet for your VPC\. By default, the name created by the VPC wizard is **Public subnet**\.

1. Choose **Actions**, **Modify auto\-assign IP settings**\.

1. Select the **Enable auto\-assign public IPv4 address** check box, and then choose **Save**\.

## <a name="create-add-subnets"></a>

## Next Steps<a name="vpc-next-steps"></a>

After you have created your VPC, you should consider the following next steps:
+ Create security groups for your public and private resources if they require inbound network access\. For more information, see [Work with security groups](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html#working-with-security-groups) in the *Amazon VPC User Guide*\.
+ Create an AWS Batch managed compute environment that launches compute resources into your new VPC\. For more information, see [Creating a compute environment](create-compute-environment.md)\. If you use the compute environment creation wizard in the AWS Batch console, you can specify the VPC that you just created and the public or private subnets into which to launch your instances, depending on your use case\.
+ Create an AWS Batch job queue that is mapped to your new compute environment\. For more information, see [Creating a job queue](create-job-queue.md)\.
+ Create a job definition to run your jobs with\. For more information, see [Creating a job definition](create-job-definition.md)\.
+ Submit a job with your job definition to your new job queue\. This job will land in the compute environment you created with your new VPC and subnets\. For more information, see [Submitting a Job](submit_job.md)\.