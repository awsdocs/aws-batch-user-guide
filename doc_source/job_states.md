# Job States<a name="job_states"></a>

When you submit a job to an AWS Batch job queue, the job enters the `SUBMITTED` state\. It then passes through the following states until it succeeds \(exits with code `0`\) or fails \(exits with a non\-zero code\)\. AWS Batch jobs can have the following states:

`SUBMITTED`  
A job that has been submitted to the queue, and has not yet been evaluated by the scheduler\. The scheduler evaluates the job to determine if it has any outstanding dependencies on the successful completion of any other jobs\. If there are dependencies, the job is moved to `PENDING`\. If there are no dependencies, the job is moved to `RUNNABLE`\.

`PENDING`  
A job that resides in the queue and is not yet able to run due to a dependency on another job or resource\. After the dependencies are satisfied, the job is moved to `RUNNABLE`\.

`RUNNABLE`  
A job that resides in the queue, has no outstanding dependencies, and is therefore ready to be scheduled to a host\. Jobs in this state are started as soon as sufficient resources are available in one of the compute environments that are mapped to the job’s queue\. However, jobs can remain in this state indefinitely when sufficient resources are unavailable\.  
If your jobs do not progress to `STARTING`, see [Jobs Stuck in `RUNNABLE` Status](troubleshooting.md#job_stuck_in_runnable) in the troubleshooting section\.

`STARTING`  
These jobs have been scheduled to a host and the relevant container initiation operations are underway\. After the container image is pulled and the container is up and running, the job transitions to `RUNNING`\.

`RUNNING`  
The job is running as a container job on an Amazon ECS container instance within a compute environment\. When the job's container exits, the process exit code determines whether the job succeeded or failed\. An exit code of `0` indicates success, and any non\-zero exit code indicates failure\. If the job associated with a failed attempt has any remaining attempts left in its optional retry strategy configuration, the job is moved to `RUNNABLE` again\. For more information, see [Automated Job Retries](job_retries.md)\.  
Logs for `RUNNING` jobs are available in CloudWatch Logs; the log group is `/aws/batch/job`, and the log stream name format is `jobDefinitionName/default/ecs_task_id` \(this format may change in the future\)\.  
After a job reaches the `RUNNING` status, you can programmatically retrieve its log stream name with the [DescribeJobs](https://docs.aws.amazon.com/batch/latest/APIReference/API_DescribeJobs.html) API operation\. For more information, see [View Log Data Sent to CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/Working-with-log-groups-and-streams.html#ViewingLogData) in the *Amazon CloudWatch Logs User Guide*\. By default, these logs are set to never expire, but you can modify the retention period\. For more information, see [Change Log Data Retention in CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/SettingLogRetention.html) in the *Amazon CloudWatch Logs User Guide*\.

`SUCCEEDED`  
The job has successfully completed with an exit code of `0`\. The job state for `SUCCEEDED` jobs is persisted in AWS Batch for 24 hours\.  
Logs for `SUCCEEDED` jobs are available in CloudWatch Logs; the log group is `/aws/batch/job`, and the log stream name format is `jobDefinitionName/default/ecs_task_id` \(this format may change in the future\)\.  
After a job reaches the `RUNNING` status, you can programmatically retrieve its log stream name with the [DescribeJobs](https://docs.aws.amazon.com/batch/latest/APIReference/API_DescribeJobs.html) API operation\. For more information, see [View Log Data Sent to CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/Working-with-log-groups-and-streams.html#ViewingLogData) in the *Amazon CloudWatch Logs User Guide*\. By default, these logs are set to never expire, but you can modify the retention period\. For more information, see [Change Log Data Retention in CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/SettingLogRetention.html) in the *Amazon CloudWatch Logs User Guide*\.

`FAILED`  
The job has failed all available attempts\. The job state for `FAILED` jobs is persisted in AWS Batch for 24 hours\.  
Logs for `FAILED` jobs are available in CloudWatch Logs; the log group is `/aws/batch/job`, and the log stream name format is `jobDefinitionName/default/ecs_task_id` \(this format may change in the future\)\.  
After a job reaches the `RUNNING` status, you can programmatically retrieve its log stream with the [DescribeJobs](https://docs.aws.amazon.com/batch/latest/APIReference/API_DescribeJobs.html) API operation\. For more information, see [View Log Data Sent to CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/Working-with-log-groups-and-streams.html#ViewingLogData) in the *Amazon CloudWatch Logs User Guide*\. By default, these logs are set to never expire, but you can modify the retention period\. For more information, see [Change Log Data Retention in CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/SettingLogRetention.html) in the *Amazon CloudWatch Logs User Guide*\.