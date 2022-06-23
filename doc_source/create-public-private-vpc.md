# Creating a virtual private cloud<a name="create-public-private-vpc"></a>

Compute resources in your compute environments need external network access to communicate with AWS Batch and Amazon ECS service endpoints\. However, you might have jobs that you want to run in private subnets\. To have the flexibility to run jobs in either a public or private subnet, create a VPC that has both public and private subnets\. 



You can use Amazon Virtual Private Cloud \(Amazon VPC\) to launch AWS resources into a virtual network that you define\. This topic provides a link to the Amazon VPC wizard and a list of the options to select\.

## Create a VPC<a name="run-VPC-wizard"></a>

For information about how to create an Amazon VPC, see [Create a VPC only](https://docs.aws.amazon.com/vpc/latest/userguide/working-with-vpcs.html#create-vpc-vpc-only) in the *Amazon VPC User Guide* and use the following table to determine what options to select\.


| Option | Value | 
| --- | --- | 
|  Resources to create  | VPC only | 
| Name |  Optionally provide a name for your VPC\.  | 
| IPv4 CIDR block |  IPv4 CIDR manual input The CIDR block size must have a size between /16 and /28\.  | 
|  IPv6 CIDR block  |  No IPv6 CIDR block  | 
|  Tenancy  |  Default  | 

For more information about Amazon VPC, see [What is Amazon VPC?](https://docs.aws.amazon.com/vpc/latest/userguide/) in the *Amazon VPC User Guide*\.

## Next Steps<a name="vpc-next-steps"></a>

After you have created your VPC, consider the following next steps:
+ Create security groups for your public and private resources if they require inbound network access\. For more information, see [Work with security groups](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html#working-with-security-groups) in the *Amazon VPC User Guide*\.
+ Create an AWS Batch managed compute environment that launches compute resources into your new VPC\. For more information, see [Creating a compute environment](create-compute-environment.md)\. If you use the compute environment creation wizard in the AWS Batch console, you can specify the VPC that you just created and the public or private subnets that you want to launch your instances into\.
+ Create an AWS Batch job queue that's mapped to your new compute environment\. For more information, see [Creating a job queue](create-job-queue.md)\.
+ Create a job definition to run your jobs with\. For more information, see [Creating a single\-node job definition ](create-job-definition.md)\.
+ Submit a job with your job definition to your new job queue\. This job lands in the compute environment that you created with your new VPC and subnets\. For more information, see [Submitting a job](submit_job.md)\.