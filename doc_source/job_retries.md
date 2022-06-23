# Automated job retries<a name="job_retries"></a>

You can apply a retry strategy to your jobs and job definitions that allows failed jobs to be automatically retried\. Possible failure scenarios include:
+ Any non\-zero exit code from a container job
+ Amazon EC2 instance failure or termination
+ Internal AWS service error or outage

When a job is submitted to a job queue and placed into the `RUNNING` state that is considered an attempt\. By default, each job is given one attempt to move to either the `SUCCEEDED` or `FAILED` job state\. However, both the job definition and the job submission workflows allow you to specify a retry strategy with between 1 and 10 attempts\. If [evaluateOnExit](https://docs.aws.amazon.com/batch/latest/APIReference/API_EvaluateOnExit.html) is specified, but none of the entries match, then the job is retried\. In order for jobs that do not match to exit, add a final entry that exits for any reason\. For example, this `evaluateOnExit` object has two entries that with actions of `RETRY` and a final entry with an action of `EXIT`\.

```
"evaluateOnExit": [
    {
        "action": "RETRY",
        "onReason": "AGENT"
    },
    {
        "action": "RETRY",
        "onReason": "Task failed to start"
    },
    {
        "action": "EXIT",
        "onReason": "*"
    }
]
```

At runtime, the `AWS_BATCH_JOB_ATTEMPT` environment variable is set to the container's corresponding job attempt number\. The first attempt is numbered `1`, and subsequent attempts are in ascending order \(for example, 2, 3, 4\)\.

If a job attempt fails for any reason, and the number of attempts specified in the retry configuration is greater than the `AWS_BATCH_JOB_ATTEMPT` number, then the job is placed back in the `RUNNABLE` state\. For more information, see [Job states](job_states.md)\.

**Note**  
Jobs that have been cancelled or terminated are not retried\. Also, jobs that fail because of an invalid job definition aren't retried\.

For more information, see [Retry strategy](job_definition_parameters.md#retryStrategy), [Creating a single\-node job definition ](create-job-definition.md) and [Submitting a job](submit_job.md)\.