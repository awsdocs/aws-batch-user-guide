# Automated Job Retries<a name="job_retries"></a>

You can apply a retry strategy to your jobs and job definitions that allows failed jobs to be automatically retried\. Possible failure scenarios include:
+ Any non\-zero exit code from a container job
+ Amazon EC2 instance failure or termination
+ Internal AWS service error or outage

When a job is submitted to a job queue and placed into the `RUNNING` state, that is considered an attempt\. By default, each job is given one attempt to move to either the `SUCCEEDED` or `FAILED` job state\. However, both the job definition and the job submission workflows allow you to specify a retry strategy with between 1 and 10 attempts\. For more information, see [Retry Strategy](job_definition_parameters.md#retryStrategy)\.

At runtime, the `AWS_BATCH_JOB_ATTEMPT` environment variable is set to the container's corresponding job attempt number\. The first attempt is numbered `1`, and subsequent attempts are in ascending order \(2, 3, 4, and so on\)\.

If a job attempt fails for any reason, and the number of attempts specified in the retry configuration is greater than the `AWS_BATCH_JOB_ATTEMPT` number, then the job is placed back in the `RUNNABLE` state\. For more information, see [Job States](job_states.md)\.

**Note**  
Jobs that have been cancelled or terminated are not retried\. Also, jobs that fail due to an invalid job definition are not retried\.

For more information, see [Creating a Job Definition](create-job-definition.md) and [Submitting a Job](submit_job.md)\.