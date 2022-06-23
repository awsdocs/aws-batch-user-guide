# Job timeouts<a name="job_timeouts"></a>

You can configure a timeout duration for your jobs so that if a job runs longer than that, AWS Batch terminates the job\. For example, you might have a job that you know should only take 15 minutes to complete\. Sometimes your application gets stuck in a loop and runs forever, so you can set a timeout of 30 minutes to terminate the stuck job\.

**Important**  
By default, AWS Batch doesn't have a job timeout\. If you don't define a job timeout, the job runs until the container exits\.

You specify an `attemptDurationSeconds` parameter, which must be at least 60 seconds, either in your job definition, or when you submit the job\. When this number of seconds has passed following the job attempt's `startedAt` timestamp, AWS Batch terminates the job\. On the compute resource, your job's container receives a `SIGTERM` signal to give your application a chance to shut down gracefully\. If the container is still running after 30 seconds, a `SIGKILL` signal is sent to forcefully shut down the container\.

Timeout terminations are handled on a best\-effort basis\. You shouldn't expect your timeout termination to happen exactly when the job attempt times out \(it may take a few seconds longer\)\. If your application requires precise timeout execution, you should implement this logic within the application\. If you have a large number of jobs timing out concurrently, the timeout terminations behave as a first in, first out queue, where jobs are terminated in batches\.

**Note**  
There's no maximum timeout value for an AWS Batch job\.

If a job is terminated for exceeding the timeout duration, it isn't retried\. If a job attempt fails on its own, then it can retry if retries are enabled, and the timeout countdown is started over for the new attempt\.

**Important**  
Jobs that run on Fargate resources can't expect to run for more than 14 days\. If the timeout duration exceeds 14 days, the Fargate resources may no longer be available and the job will be terminated\. 

For array jobs, child jobs have the same timeout configuration as the parent job\.

For information about submitting an AWS Batch job with a timeout configuration, see [Submitting a job](submit_job.md)\.