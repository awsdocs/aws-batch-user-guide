# Troubleshooting AWS Batch<a name="troubleshooting"></a>

You may find the need to troubleshoot issues with your compute environments, job queues, job definitions, or jobs\. This chapter helps you troubleshoot and repair issues with your AWS Batch environment\. 

## `INVALID` Compute Environment<a name="invalid_compute_environment"></a>

It is possible to incorrectly configure a managed compute environment so that it enters an `INVALID` state and cannot accept jobs for placement\. These sections describe the possible causes and how to fix them\.

### Incorrect Role Name or ARN<a name="invalid_service_role_arn"></a>

The most common cause for invalid compute environments is an incorrect name or ARN for the AWS Batch service role or the Amazon EC2 Spot Fleet role\. This is more of an issue for compute environments that are created with the AWS CLI or the AWS SDKs; when you create a compute environment in the AWS Management Console, AWS Batch can help you choose the correct service or Spot Fleet roles and you cannot misspell the name or deform the ARN\.

However, if you manually type the name or ARN for an IAM in an AWS CLI command or your SDK code, AWS Batch is unable to validate the string and it accepts the bad value and attempts to create the environment\. After failing to create the environment, the environment moves to an `INVALID` state, and you see the following errors\.

For an invalid service role:

```
CLIENT_ERROR - Not authorized to perform sts:AssumeRole (Service: AWSSecurityTokenService; Status Code: 403; Error Code: AccessDenied; Request ID: dc0e2d28-2e99-11e7-b372-7fcc6fb65fe7)
```

For an invalid Spot Fleet role:

```
CLIENT_ERROR - Parameter: SpotFleetRequestConfig.IamFleetRole is invalid. (Service: AmazonEC2; Status Code: 400; Error Code: InvalidSpotFleetRequestConfig; Request ID: 331205f0-5ae3-4cea-bac4-897769639f8d)  Parameter: SpotFleetRequestConfig.IamFleetRole is invalid
```

One common cause for this issue is if you only specify the name of an IAM role when using the AWS CLI or the AWS SDKs, instead of the full ARN\. This is because depending on how you created the role, the ARN may contain a `service-role` path prefix\. For example, if you manually create the AWS Batch service role using the procedures in [AWS Batch Service IAM Role](service_IAM_role.md), your service role ARN would look like this:

```
arn:aws:iam::123456789012:role/AWSBatchServiceRole
```

However, if you created the service role as part of the console first run wizard today, your service role ARN would look like this:

```
arn:aws:iam::123456789012:role/service-role/AWSBatchServiceRole
```

When you only specify the name of an IAM role when using the AWS CLI or the AWS SDKs, AWS Batch assumes that your ARN does not use the `service-role` path prefix\. Because of this, we recommend that you specify the full ARN for your IAM roles when you create compute environments\.

To repair a compute environment that is misconfigured this way, see [Repairing an `INVALID` Compute Environment](#repairing_invalid_compute_environment)\.

### Repairing an `INVALID` Compute Environment<a name="repairing_invalid_compute_environment"></a>

When you have a compute environment in an `INVALID` state, you should update it to repair the invalid parameter\. For the case of an [Incorrect Role Name or ARN](#invalid_service_role_arn), you can update the compute environment with the correct service role\.

**To repair a misconfigured compute environment**

1. Open the AWS Batch console at [https://console\.aws\.amazon\.com/batch/](https://console.aws.amazon.com/batch/)\.

1. From the navigation bar, select the region to use\.

1. In the navigation pane, choose **Compute environments**\.

1. On the **Compute environments** page, select the radio button next to the compute environment to edit, and then choose **Edit**\.

1. On the **Update compute environment** page, for **Service role**, choose the IAM role to use with your compute environment\. The AWS Batch console only displays roles that have the correct trust relationship for compute environments\.

1. Choose **Save** to update your compute environment\.

## Jobs Stuck in `RUNNABLE` Status<a name="job_stuck_in_runnable"></a>

If your compute environment contains compute resources, but your jobs do not progress beyond the `RUNNABLE` status, then there is something preventing the jobs from actually being placed on a compute resource\. Here are some common causes for this issue:

The `awslogs` log driver is not configured on your compute resources  
AWS Batch jobs send their log information to CloudWatch Logs\. To enable this, you must configure your compute resources to use the `awslogs` log driver\. If you base your compute resource AMI off of the Amazon ECS\-optimized AMI \(or Amazon Linux\), then this driver is registered by default with the `ecs-init` package\. If you use a different base AMI, then you must ensure that the `awslogs` log driver is specified as an available log driver with the `ECS_AVAILABLE_LOGGING_DRIVERS` environment variable when the Amazon ECS container agent is started\. For more information, see [Compute Resource AMI Specification](compute_resource_AMIs.md#batch-ami-spec) and [Creating a Compute Resource AMI](create-batch-ami.md)\.

Insufficient resources  
If your job definitions specify more CPU or memory resources than your compute resources can allocate, then your jobs will never be placed\. For example, if your job specifies 4 GiB of memory, and your compute resources have less than that available, then the job cannot be placed on those compute resources\. In this case, you must reduce the specified memory in your job definition or add larger compute resources to your environment\. Some memory is reserved for the Amazon ECS container agent and other critical system processes\. For more information, see [Compute Resource Memory Management](memory-management.md)\.

No internet access for compute resources  
Compute resources need access to communicate with the Amazon ECS service endpoint\. This can be through an interface VPC endpoint or through your compute resources having public IP addresses\.  
For more information about interface VPC endpoints, see [Amazon ECS Interface VPC Endpoints \(AWS PrivateLink\)](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/vpc-endpoints.html) in the *Amazon Elastic Container Service Developer Guide*\.  
If you do not have an interface VPC endpoint configured and your compute resources do not have public IP addresses, then they must use network address translation \(NAT\) to provide this access\. For more information, see [NAT Gateways](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) in the *Amazon VPC User Guide*\. For more information, see [Tutorial: Creating a VPC with Public and Private Subnets for Your Compute Environments](create-public-private-vpc.md)\.

Amazon EC2 instance limit reached  
The number of Amazon EC2 instances that your account can launch in an AWS region is determined by your EC2 instance limit\. Certain instance types have a per\-instance\-type limit as well\. For more information on your account's Amazon EC2 instance limits \(including how to request a limit increase\), see [Amazon EC2 Service Limits](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-resource-limits.html) in the *Amazon EC2 User Guide for Linux Instances*

For more information on diagnosing jobs stuck in `RUNNABLE` status, see [Why is my AWS Batch job stuck in RUNNABLE status?](https://aws.amazon.com/premiumsupport/knowledge-center/batch-job-stuck-runnable-status/) in the *AWS Knowledge Center*\.

## Spot Instances Not Tagged on Creation<a name="spot-instance-no-tag"></a>

Spot Instance tagging for AWS Batch compute resources is supported as of October 25, 2017\. Prior to that support, the recommended IAM managed policy \(`AmazonEC2SpotFleetRole`\) for the Amazon EC2 Spot Fleet role did not contain permissions to tag Spot Instances at launch\. The new recommended IAM managed policy is called `AmazonEC2SpotFleetTaggingRole`\.

To fix Spot Instance tagging on creation, follow the procedure below to apply the current recommended IAM managed policy to your Amazon EC2 Spot Fleet role, and then any future Spot Instances that are created with that role have permissions to apply instance tags on creation\.

**To apply the current IAM managed policy to your Amazon EC2 Spot Fleet role**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. Choose **Roles**, and choose your Amazon EC2 Spot Fleet role\.

1. Choose **Attach policy**\.

1. Select the **AmazonEC2SpotFleetTaggingRole** and choose **Attach policy**\.

1. Choose your Amazon EC2 Spot Fleet role again to remove the previous policy\.

1. Select the **x** to the right of the **AmazonEC2SpotFleetRole** policy, and choose **Detach**\.