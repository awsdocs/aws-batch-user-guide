# Troubleshooting AWS Batch<a name="troubleshooting"></a>

You might find the need to troubleshoot issues with your compute environments, job queues, job definitions, or jobs\. This chapter helps you troubleshoot and repair issues with your AWS Batch environment\. 

## `INVALID` Compute Environment<a name="invalid_compute_environment"></a>

It's possible to incorrectly configure a managed compute environment so that it enters an `INVALID` state and cannot accept jobs for placement\. These sections describe the possible causes and how to fix them\.

### Incorrect Role Name or ARN<a name="invalid_service_role_arn"></a>

The most common cause for invalid compute environments is an incorrect name or ARN for the AWS Batch service role or the Amazon EC2 Spot Fleet role\. This is more of an issue for compute environments that are created with the AWS CLI or the AWS SDKs\. When you create a compute environment in the AWS Management Console, AWS Batch can help you choose the correct service or Spot Fleet roles\. However, you can't misspell the name or deform the ARN\.

However, if you manually type the name or ARN for an IAM in an AWS CLI command or your SDK code, AWS Batch can't validate the string and it accepts the bad value and attempts to create the environment\. After failing to create the environment, the environment moves to an `INVALID` state, and you see the following errors\.

For an invalid service role:

```
CLIENT_ERROR - Not authorized to perform sts:AssumeRole (Service: AWSSecurityTokenService; Status Code: 403; Error Code: AccessDenied; Request ID: dc0e2d28-2e99-11e7-b372-7fcc6fb65fe7)
```

For an invalid Spot Fleet role:

```
CLIENT_ERROR - Parameter: SpotFleetRequestConfig.IamFleetRole is invalid. (Service: AmazonEC2; Status Code: 400; Error Code: InvalidSpotFleetRequestConfig; Request ID: 331205f0-5ae3-4cea-bac4-897769639f8d)  Parameter: SpotFleetRequestConfig.IamFleetRole is invalid
```

One common cause for this issue is if you only specify the name of an IAM role when using the AWS CLI or the AWS SDKs, instead of the full ARN\. This is because depending on how you created the role, the ARN might contain a `service-role` path prefix\. For example, if you manually create the AWS Batch service role using the procedures in [AWS Batch service IAM role](service_IAM_role.md), your service role ARN would look like this:

```
arn:aws:iam::123456789012:role/AWSBatchServiceRole
```

However, if you created the service role as part of the console first run wizard today, your service role ARN would look like this:

```
arn:aws:iam::123456789012:role/service-role/AWSBatchServiceRole
```

When you only specify the name of an IAM role when using the AWS CLI or the AWS SDKs, AWS Batch assumes that your ARN doesn't use the `service-role` path prefix\. Because of this, we recommend that you specify the full ARN for your IAM roles when you create compute environments\.

To repair a compute environment that's misconfigured this way, see [Repairing an `INVALID` Compute Environment](#repairing_invalid_compute_environment)\.

### Repairing an `INVALID` Compute Environment<a name="repairing_invalid_compute_environment"></a>

When you have a compute environment in an `INVALID` state, you should update it to repair the invalid parameter\. For the case of an [Incorrect Role Name or ARN](#invalid_service_role_arn), you can update the compute environment with the correct service role\.

**To repair a misconfigured compute environment**

1. Open the AWS Batch console at [https://console\.aws\.amazon\.com/batch/](https://console.aws.amazon.com/batch/)\.

1. From the navigation bar, select the Region to use\.

1. In the navigation pane, choose **Compute environments**\.

1. On the **Compute environments** page, select the radio button next to the compute environment to edit, and then choose **Edit**\.

1. On the **Update compute environment** page, for **Service role**, choose the IAM role to use with your compute environment\. The AWS Batch console only displays roles that have the correct trust relationship for compute environments\.

1. Choose **Save** to update your compute environment\.

## Jobs Stuck in `RUNNABLE` Status<a name="job_stuck_in_runnable"></a>

If your compute environment contains compute resources, but your jobs don't progress beyond the `RUNNABLE` status, then there is something preventing the jobs from actually being placed on a compute resource\. Here are some common causes for this issue:

The `awslogs` log driver isn't configured on your compute resources  
AWS Batch jobs send their log information to CloudWatch Logs\. To enable this, you must configure your compute resources to use the `awslogs` log driver\. If you base your compute resource AMI off of the Amazon ECS optimized AMI \(or Amazon Linux\), then this driver is registered by default with the `ecs-init` package\. If you use a different base AMI, then you must verify that the `awslogs` log driver is specified as an available log driver with the `ECS_AVAILABLE_LOGGING_DRIVERS` environment variable when the Amazon ECS container agent is started\. For more information, see [Compute resource AMI specification](compute_resource_AMIs.md#batch-ami-spec) and [Creating a compute resource AMI](create-batch-ami.md)\.

Insufficient resources  
If your job definitions specify more CPU or memory resources than your compute resources can allocate, then your jobs is never placed\. For example, if your job specifies 4 GiB of memory, and your compute resources have less than that available, then the job can't be placed on those compute resources\. In this case, you must reduce the specified memory in your job definition or add larger compute resources to your environment\. Some memory is reserved for the Amazon ECS container agent and other critical system processes\. For more information, see [Compute Resource Memory Management](memory-management.md)\.

No internet access for compute resources  
Compute resources need access to communicate with the Amazon ECS service endpoint\. This can be through an interface VPC endpoint or through your compute resources having public IP addresses\.  
For more information about interface VPC endpoints, see [Amazon ECS Interface VPC Endpoints \(AWS PrivateLink\)](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/vpc-endpoints.html) in the *Amazon Elastic Container Service Developer Guide*\.  
If you do not have an interface VPC endpoint configured and your compute resources do not have public IP addresses, then they must use network address translation \(NAT\) to provide this access\. For more information, see [NAT gateways](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) in the *Amazon VPC User Guide*\. For more information, see [Create a Virtual Private Cloud](get-set-up-for-aws-batch.md#create-a-vpc)\.\.

Amazon EC2 instance limit reached  
The number of Amazon EC2 instances that your account can launch in an AWS Region is determined by your EC2 instance limit\. Certain instance types have a per\-instance\-type limit as well\. For more information on your account's Amazon EC2 instance limits \(including how to request a limit increase\), see [Amazon EC2 Service Limits](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-resource-limits.html) in the *Amazon EC2 User Guide for Linux Instances*

For more information on diagnosing jobs stuck in `RUNNABLE` status, see [Why is my AWS Batch job stuck in RUNNABLE status?](https://aws.amazon.com/premiumsupport/knowledge-center/batch-job-stuck-runnable-status/) in the *AWS Knowledge Center*\.

## Spot Instances Not Tagged on Creation<a name="spot-instance-no-tag"></a>

Spot Instance tagging for AWS Batch compute resources is supported as of October 25, 2017\. Before this, the recommended IAM managed policy \(`AmazonEC2SpotFleetRole`\) for the Amazon EC2 Spot Fleet role didn't contain permissions to tag Spot Instances at launch\. The new recommended IAM managed policy is called `AmazonEC2SpotFleetTaggingRole`\.

To fix Spot Instance tagging on creation, follow the following procedure to apply the current recommended IAM managed policy to your Amazon EC2 Spot Fleet role, and then any future Spot Instances that are created with that role have permissions to apply instance tags on creation\.

**To apply the current IAM managed policy to your Amazon EC2 Spot Fleet role**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. Choose **Roles**, and choose your Amazon EC2 Spot Fleet role\.

1. Choose **Attach policy**\.

1. Select the **AmazonEC2SpotFleetTaggingRole** and choose **Attach policy**\.

1. Choose your Amazon EC2 Spot Fleet role again to remove the previous policy\.

1. Select the **x** to the right of the **AmazonEC2SpotFleetRole** policy, and choose **Detach**\.

## Spot Instances not scaling down<a name="spot-fleet-not-authorized"></a>

AWS Batch introduced the **AWSServiceRoleForBatch** service\-linked role on March 10, 2021\. This service\-linked role is used as the service role if no role is specified in the `serviceRole` parameter of the compute environment\. If the service\-linked role is used in an EC2 Spot compute environment, but the Spot role used doesn't include the **AmazonEC2SpotFleetTaggingRole** managed policy, the Spot instances doesn't scale down\. You will receive an error with the message: "You are not authorized to perform this operation\." Use the following steps to update the spot fleet role that you use in the `spotIamFleetRole` parameter\. For more information, see [Using service\-linked roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html) and [Creating a role to delegate permissions to an AWS Service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html) in the *IAM User Guide*\.

**Topics**
+ [Attach **AmazonEC2SpotFleetTaggingRole** managed policy to your Spot Fleet role in the AWS Management Console](#spot-fleet-not-authorized-console)
+ [Attach **AmazonEC2SpotFleetTaggingRole** managed policy to your Spot Fleet role with the AWS CLI](#spot-fleet-not-authorized-cli)

### Attach **AmazonEC2SpotFleetTaggingRole** managed policy to your Spot Fleet role in the AWS Management Console<a name="spot-fleet-not-authorized-console"></a>

**To apply the current IAM managed policy to your Amazon EC2 Spot Fleet role**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. Choose **Roles**, and choose your Amazon EC2 Spot Fleet role\.

1. Choose **Attach policy**\.

1. Select the **AmazonEC2SpotFleetTaggingRole** and choose **Attach policy**\.

1. Choose your Amazon EC2 Spot Fleet role again to remove the previous policy\.

1. Select the **x** to the right of the **AmazonEC2SpotFleetRole** policy, and choose **Detach**\.

### Attach **AmazonEC2SpotFleetTaggingRole** managed policy to your Spot Fleet role with the AWS CLI<a name="spot-fleet-not-authorized-cli"></a>

The example commands assume that your Amazon EC2 Spot Fleet role is named *AmazonEC2SpotFleetRole*\. If your role uses a different name, adjust the commands to match\.

**To attach the **AmazonEC2SpotFleetTaggingRole** managed policy to your Spot Fleet role**

1. To attach the **AmazonEC2SpotFleetTaggingRole** managed IAM policy to your *AmazonEC2SpotFleetRole* role, run the following command using the AWS CLI\.

   ```
   aws iam attach-role-policy \
       --policy-arn arn:aws:iam::aws:policy/service-role/AmazonEC2SpotFleetTaggingRole \
       --role-name AmazonEC2SpotFleetRole
   ```

1. To detach the **AmazonEC2SpotFleetRole** managed IAM policy from your *AmazonEC2SpotFleetRole* role, run the following command using the AWS CLI\.

   ```
   aws iam detach-role-policy \
       --policy-arn arn:aws:iam::aws:policy/service-role/AmazonEC2SpotFleetRole \
       --role-name AmazonEC2SpotFleetRole
   ```

## Can't retrieve Secrets Manager secrets<a name="troubleshooting-cant-specify-secrets"></a>

If you are using an AMI with an Amazon ECS agent earlier than version 1\.16\.0\-1, then you must use the Amazon ECS agent configuration variable `ECS_ENABLE_AWSLOGS_EXECUTIONROLE_OVERRIDE=true` to use this feature\. You can add it to the `./etc/ecs/ecs.config` file during container instance creation or you can add it to an existing instance and then restart the ECS agent\. For more information, see [Amazon ECS Container Agent Configuration](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-agent-config.html) in the *Amazon Elastic Container Service Developer Guide*\.

## Can't override job definition resource requirements<a name="override-resource-requirements"></a>

Memory and vCPU overrides that are specified in the `memory` and `vcpus` members of the [containerOverrides](https://docs.aws.amazon.com/batch/latest/APIReference/API_ContainerOverrides.html) structure passed to [SubmitJob](https://docs.aws.amazon.com/batch/latest/APIReference/API_SubmitJob.html) can't override the memory and vCPU requirements that are specified in the [resourceRequirements](https://docs.aws.amazon.com/batch/latest/APIReference/API_ContainerProperties.html#Batch-Type-ContainerProperties-resourceRequirements) structure in the job definition\.

If you try to override these resource requirements, you might see the following error message:

"This value was submitted in a deprecated key and may conflict with the value provided by the job definition's resource requirements\."

To correct this, specify the memory and vCPU requirements in the [resourceRequirements](https://docs.aws.amazon.com/batch/latest/APIReference/API_ContainerOverrides.html#Batch-Type-ContainerOverrides-resourceRequirements) member of the [containerOverrides](https://docs.aws.amazon.com/batch/latest/APIReference/API_ContainerOverrides.html)\. For example, if your memory and vCPU overrides are specified in the following lines:

```
"containerOverrides": {
   "memory": 8192,
   "vcpus": 4
}
```

Change them to this:

```
"containerOverrides": {
   "resourceRequirements": [
      {
         "type": "MEMORY",
         "value": "8192"
      },
      {
         "type": "VCPU",
         "value": "4"
      }
   ],
}
```

Do the same change to the memory and vCPU requirements that are specified in the [containerProperties](https://docs.aws.amazon.com/batch/latest/APIReference/API_ContainerProperties.html) object in the job definition\. For example, if your memory and vCPU requirements are specified in the following lines:

```
{
   "containerProperties": {
      "memory": 4096,
      "vcpus": 2,
}
```

Change them to this:

```
"containerProperties": {
   "resourceRequirements": [
      {
         "type": "MEMORY",
         "value": "4096"
      },
      {
         "type": "VCPU",
         "value": "2"
      }
   ],
}
```