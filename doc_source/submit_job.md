# Submitting a Job<a name="submit_job"></a>

After you have registered a job definition, you can submit it as a job to an AWS Batch job queue\. Many of the parameters that are specified in the job definition can be overridden at runtime\.

**To submit a job**

1. Open the AWS Batch console at [https://console\.aws\.amazon\.com/batch/](https://console.aws.amazon.com/batch/)\.

1. From the navigation bar, select the Region to use\.

1. In the navigation pane, choose **Jobs**, **Submit job**\.

1. For **Job name**, choose a unique name for your job\.

1. For **Job definition**, choose a previously created job definition for your job\. For more information, see [Creating a Job Definition](create-job-definition.md)\.

1. For **Job queue**, choose a previously created job queue\. For more information, see [Creating a Job Queue](create-job-queue.md)\.

1. For **Job type**, choose **Single** for a single job or **Array** to submit an array job\. For more information, see [Array Jobs](array_jobs.md)\. This option is not available for multi\-node parallel jobs\.

1. \(Array jobs only\) For **Array size**, specify an array size between 2 and 10,000\.

1. \(Optional\) Declare any job dependencies\. A job may have up to 20 dependencies\. For more information, see [Job Dependencies](job_dependencies.md)\.

   1. For **Job depends on**, enter the job IDs for any jobs that must finish before this job starts\.

   1. \(Array jobs only\) For **N\-To\-N job dependencies**, specify one or more job IDs for any array jobs for which each child job index of this job should depend on the corresponding child index job of the dependency\. For example, `JobB:1` depends on `JobA:1`, and so on\.

   1. \(Array jobs only\) Select **Run children sequentially** to create a `SEQUENTIAL` dependency for the current array job\. This ensures that each child index job waits for its earlier sibling to finish\. For example, `JobA:1` depends on `JobA:0` and so on\.

1. For **Job attempts**, specify the maximum number of times to attempt your job \(in case it fails\)\. For more information, see [Automated Job Retries](job_retries.md)\.

1. \(Optional\) For **Execution timeout**, specify the maximum number of seconds to allow your job attempts to run\. If an attempt exceeds the timeout duration, it is stopped and the status moves to `FAILED`\. For more information, see [Job Timeouts](job_timeouts.md)\.

1. \(Optional\) In the **Parameters** section, you can specify parameter substitution default values and placeholders to use in the command that your job's container runs when it starts\. For more information, see [Parameters](job_definition_parameters.md#parameters)\.

   1. Choose **Add parameter**\.

   1. For **Key**, specify the key for your parameter\.

   1. For **Value**, specify the value for your parameter\.

1. For **vCPUs**, specify the number of vCPUs to reserve for the container\. This parameter maps to `CpuShares` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--cpu-shares` option to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\. Each vCPU is equivalent to 1,024 CPU shares\. You must specify at least one vCPU\.

1. For **Memory**, specify the hard limit \(in MiB\) of memory to present to the job's container\. If your container attempts to exceed the memory specified here, the container is killed\. This parameter maps to `Memory` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--memory` option to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\. You must specify at least 4 MiB of memory for a job\.

1. \(Optional\) For **Number of GPUs**, specify the number of GPUs your job will use\.

   The job will run on a container with the specified number of GPUs pinned to that container\.

1. For **Command**, specify the command to pass to the container\. For simple commands, you can type the command as you would at a command prompt in the **Space delimited** tab\. Verify that the JSON result \(which is passed to the Docker daemon\) is correct\. For more complicated commands \(for example, with special characters\), you can switch to the **JSON** tab and enter the string array equivalent there\.

   This parameter maps to `Cmd` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `COMMAND` parameter to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\. For more information about the Docker `CMD` parameter, go to [https://docs\.docker\.com/engine/reference/builder/\#cmd](https://docs.docker.com/engine/reference/builder/#cmd)\.
**Note**  
You can use parameter substitution default values and placeholders in your command\. For more information, see [Parameters](job_definition_parameters.md#parameters)\.

1. \(Optional\) You can specify environment variables to pass to your job's container\. This parameter maps to `Env` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--env` option to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\.
**Important**  
We do not recommend using plaintext environment variables for sensitive information, such as credential data\.

   1. Choose **Add environment variable**\.

   1. For **Key**, specify the key for your environment variable\.
**Note**  
Environment variables must not start with `AWS_BATCH`; this naming convention is reserved for variables that are set by the AWS Batch service\.

   1. For **Value**, specify the value for your environment variable\.

1. Choose **Submit job**\.
**Note**  
Logs for `RUNNING`, `SUCCEEDED`, and `FAILED` jobs are available in CloudWatch Logs; the log group is `/aws/batch/job`, and the log stream name format is `jobDefinitionName/default/ecs_task_id` \(this format may change in the future\)\.  
After a job reaches the `RUNNING` status, you can programmatically retrieve its log stream name with the [DescribeJobs](https://docs.aws.amazon.com/batch/latest/APIReference/API_DescribeJobs.html) API operation\. For more information, see [View Log Data Sent to CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/Working-with-log-groups-and-streams.html#ViewingLogData) in the *Amazon CloudWatch Logs User Guide*\. By default, these logs are set to never expire, but you can modify the retention period\. For more information, see [Change Log Data Retention in CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/SettingLogRetention.html) in the *Amazon CloudWatch Logs User Guide*\.