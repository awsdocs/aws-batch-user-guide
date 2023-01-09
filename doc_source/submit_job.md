# Submitting a job<a name="submit_job"></a>

After you register a job definition, you can submit it as a job to an AWS Batch job queue\. You can override many of the parameters that are specified in the job definition at runtime\.

**To submit a job**

1. Open the AWS Batch console at [https://console\.aws\.amazon\.com/batch/](https://console.aws.amazon.com/batch/)\.

1. From the navigation bar, select the AWS Region to use\.

1. In the navigation pane, choose **Jobs**\.

1. Choose **Submit new job**\.

1. For **Name**, enter a unique name for your job definition\. The name can be up to 128 characters in length\. It can contain uppercase and lowercase letters, numbers, hyphens \(\-\), and underscores \(\_\)\.

1. For **Job definition**, choose an existing job definition for your job\. For more information, see [Creating a single\-node job definition ](create-job-definition.md)\.

1. For **Job queue**, choose an existing job queue\. For more information, see [Creating a job queue](create-job-queue.md)\.

1. For **Job dependencies**, choose **Add Job dependencies**\.

   1. For **Job id**, enter the job ID for any dependencies\. Then choose **Add job dependencies**\. A job can have up to 20 dependencies\. For more information, see [Job dependencies](job_dependencies.md)\.

1. \(Array jobs only\) For **Array size**, specify an array size between 2 and 10,000\.

1. \(Optional\) Expand **Tags** and then choose **Add tag** to add tags to the resource\. Enter a key and optional value, then choose **Add tag**\.

1. Choose **Next page**\.

1. In the **Job overrides** section:

   1. 

      \(Optional\) For **Scheduling priority**, enter a scheduling priority value between 0 and 100\. Higher values are given higher priority\.

   1. \(Optional\) For **Job attempts**, enter the maximum number of times that AWS Batch attempts to move the job to a `RUNNABLE` status\. You can enter a number between 1 and 10\. For more information, see [Automated job retries](job_retries.md)\.

   1. \(Optional\) For **Execution timeout**, enter the timeout value \(in seconds\)\. The execution timeout is the length of time before an unfinished job is terminated\. If an attempt exceeds the timeout duration, it's stopped and moves to a `FAILED` status\. For more information, see [Job timeouts](job_timeouts.md)\. The minimum value is 60 seconds\.
**Important**  
Don't rely on jobs that run on Fargate resources to run for more than 14 days\. After 14 days, the Fargate resources might no longer be available with the job being likely terminated\.

   1. \(Optional\) Turn on **Propagate tags** to propagate tags from the job and job definition to the Amazon ECS task\.

1. Expand **Additional configuration**\.

1. \(Optional\) For **Retry strategy conditions**, choose **Add evaluate on exit**\. Enter at least one parameter value and then choose an **Action**\. For each set of conditions, **Action** must be set to either **Retry** or **Exit**\. These actions mean the following:
   + **Retry** – AWS Batch retries until the number of job attempts that you specified is reached\.
   + **Exit** – AWS Batch stops retrying the job\.
**Important**  
If you choose **Add evaluate on exit**, configure at least one parameter and choose either an **Action** or choose **Remove evaluate on exit**\.

1. For **Parameters**, choose **Add parameters** to add parameter substitution placeholders\. Then, enter a **Key** and an optional **Value**\.

1. In the **Container overrides** section:

   1. For **Command**, specify the command to pass to the container\. For simple commands, enter the command as you do for a command prompt\. For more complicated commands, for example with special characters\), use **JSON** syntax\.

   1. For **vCPUs**, enter the number of vCPUs to reserve for the container\. This parameter maps to `CpuShares` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--cpu-shares` option to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\. Each vCPU is equivalent to 1,024 CPU shares\. You must specify at least one vCPU\.

   1. For **Memory**, enter the memory limit that's available to the container\. If your container attempts to exceed the memory specified here, the container is stopped\. This parameter maps to `Memory` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--memory` option to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\. You must specify at least 4 MiB of memory for a job\.
**Note**  
To maximize your resource utilization, prioritize memory for jobs of a specific instance type\. For more information, see [Compute Resource Memory Management](memory-management.md)\.

   1. \(Optional\) For **Number of GPUs**, choose the number of GPUs to reserve for the container\.

   1. \(Optional\) For **Environment variables**, choose **Add environment variable** to add environment variables as name\-value pairs\. These variables are passed to the container\.

   1. Choose **Next page**\.

   1. For **Job review**, review the configuration steps\. If you need to make changes, choose **Edit**\. When you're finished, choose **Create job definition**\.