# Updating compute environments<a name="updating-compute-environments"></a>

After you create a compute environment that uses EC2 resources, you can update many of the settings of the compute environment directly\. However, changing some of the settings requires that the instances in the compute environment be replaced by AWS Batch\.

Compute environments that use Fargate resources only support updating security groups \(`securityGroupIds`\) and VPC subnets \(`subnets`\)\.

AWS Batch has two update mechanisms\. The first is a scaling update where instances are added or removed from the compute environment\. The second is an infrastructure update where the instances in the compute environment are replaced\. An infrastructure update takes much longer than a scaling update\.

If you update compute environments with AWS Batch, changing only these settings causes a scaling update: desired vCPUs \(`desiredvCpus`\), maximum vCPUs \(`maxvCpus`\), minimum vCPUs \(`minvCpus`\), service role \(`serviceRole`\), and state \(`state`\)\.

If any of the following settings are changed in an [https://docs.aws.amazon.com/batch/latest/APIReference/API_UpdateComputeEnvironment.html](https://docs.aws.amazon.com/batch/latest/APIReference/API_UpdateComputeEnvironment.html) API action, AWS Batch initiates an infrastructure update\. An infrastructure update requires that the service role is set to **AWSServiceRoleForBatch** \(the default\) and that the allocation strategy is `BEST_FIT_PROGRESSIVE` or `SPOT_CAPACITY_OPTIMIZED`\. `BEST_FIT` isn't supported\. Except for service role, all of the settings that can be changed for a scaling update can also be changed for an infrastructure update\.

During an infrastructure update, the status of the compute environment changes to `UPDATING`\. New instances are launched using the updated settings\. New jobs are scheduled on the new instances\. Currently running jobs are dispatched according to the infrastructure update policy \(`updatePolicy`\) that's set in the [https://docs.aws.amazon.com/batch/latest/APIReference/API_UpdateComputeEnvironment.html](https://docs.aws.amazon.com/batch/latest/APIReference/API_UpdateComputeEnvironment.html) API action\. If the terminate jobs on update setting \(`terminateJobsOnUpdate`\) is set to `true`, the running jobs are terminated and might be retried on the updated compute environment\. Whether this happens depends on the retry settings for the job\. By default, jobs aren't retried\. Otherwise, if they're retried, they're allowed to run until the timeout set in the job execution timeout in minutes \(`jobExecutionTimeoutMinutes`\) has elapsed\. It's 30 minutes by default\. As capacity becomes available in the compute environment, new instances are launched with the updated settings and jobs are started on the new instances\. As all of the jobs complete on instances with the old settings, the old instances are terminated\. What capacity becoming available means is that desired number of vCPUs is below the maximum number of vCPUs by at least as many vCPUs as required by the smallest instance type\.

**If any of the following settings are changed, an infrastructure update is started:**
+ Allocation strategy \(`allocationStrategy`, must be either `BEST_FIT_PROGRESSIVE` or `SPOT_CAPACITY_OPTIMIZED`\. If the original allocation strategy is `BEST_FIT`, infrastructure updates aren't supported\.\)
+ Bid percentage \(`bidPercentage`\)
+ EC2 configuration \(`ec2Configuration`\)
+ Key pair \(`ec2KeyPair`\)
+ Image ID \(`imageId`\)
+ Instance role \(`instanceRole`\)
+ Instance types \(`instanceTypes`\)
+ Launch template \(`launchTemplate`\)
+ Placement group \(`placementGroup`\)
+ Security groups \(`securityGroupIds`\)
+ VPC subnets \(`subnets`\)
+ EC2 tags \(`tags`\)
+ Compute environment type \(`type`, can be one of `EC2` or `SPOT`\)
+ Whether to update to the latest AMI that's supported by AWS Batch during an infrastructure update `updateToLatestImageVersion`

## Updating the AMI ID<a name="updating-compute-environments-ami"></a>

During an infrastructure update, the compute environment's AMI ID might change, depending on whether AMIs are specified in any of these three settings\. AMIs are specified in the `imageId` \(in `computeResources`\), `imageIdOverride` \(in `ec2Configuration`\), or the launch template specified in `launchTemplate`\. Suppose that no AMI IDs are specified in any of those settings and the `updateToLatestImageVersion` setting is `true`\. Then, the latest Amazon ECS optimized AMI supported by AWS Batch is used for any infrastructure update\.

If an AMI ID is specified in at least one of these settings, the update depends on which setting provided the AMI ID used before the update\. When you create a compute environment, the priority for selecting an AMI ID is first the launch template, then the `imageId` setting, and finally the `imageIdOverride` setting\. However, if the AMI ID that is used came from the launch template, updating either the `imageId` or `imageIdOverride` settings doesn't update the AMI ID\. The only way to update an AMI ID selected from the launch template is to update the launch template\. If the version parameter of the launch template is `$Default` or `$Latest`, the default or latest version of the specified launch template is evaluated\. If a different AMI ID is selected by the default or latest version of the launch template, it's used in the update\.

If the launch template was not used to select the AMI ID, the AMI ID that's specified in the `imageId` or `imageIdOverride` parameters is used\. If both are specified, the AMI ID specified in the `imageIdOverride` parameter is used\.

Suppose that the compute environment uses an AMI ID specified by the `imageId`, `imageIdOverride`, or `launchTemplate` parameters, and you want to use the latest Amazon ECS optimized AMI supported by AWS Batch\. Then, the update must remove the settings that provided AMI IDs\. For `imageId`, this requires specifying an empty string for that parameter\. For `imageIdOverride`, this requires specifying an empty string for the `ec2Configuration` parameter\.

If the AMI ID came from the launch template, there are two ways to change to the latest Amazon ECS optimized AMI supported by AWS Batch\. They are the following:
+ Remove the launch template by specifying an empty string for the `launchTemplateId` or `launchTemplateName` parameter\. This removes the entire launch template, rather than the AMI ID alone\.
+ If the updated version of the launch template doesn't specify an AMI ID, the `updateToLatestImageVersion` parameter must be set to `true`\.