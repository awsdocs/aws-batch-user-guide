# AWS Batch Events<a name="batch_cwe_events"></a>

AWS Batch sends job status change events to CloudWatch Events\. AWS Batch tracks the state of your jobs\. If a previously submitted job's status changes, an event is invoked\. For example, if a job in the `RUNNING` status moves to the `FAILED` status\. These events are classified as job state change events\.

**Note**  
AWS Batch might add other event types, sources, and details in the future\. If you're programmatically deserializing event JSON data, make sure that your application is prepared to handle unknown properties to avoid issues if and when these additional properties are added\.

## Job State Change Events<a name="batch_job_events"></a>

Any time that an existing \(previously submitted\) job changes states, an event is created\. For more information about AWS Batch job states, see [Job States](job_states.md)\.

**Note**  
Events aren't created for the initial job submission\.

**Example Job State Change Event**  
Job state change events are delivered in the following format \(the `detail` section resembles the [JobDetail](https://docs.aws.amazon.com/batch/latest/APIReference/API_JobDetail.html) object that's returned from a [DescribeJobs](https://docs.aws.amazon.com/batch/latest/APIReference/API_DescribeJobs.html) API operation in the *AWS Batch API Reference*\)\. For more information about CloudWatch Events parameters, see [Events and Event Patterns](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/CloudWatchEventsandEventPatterns.html) in the *Amazon CloudWatch Events User Guide*\.  

```
{
  "version": "0",
  "id": "c8f9c4b5-76e5-d76a-f980-7011e206042b",
  "detail-type": "Batch Job State Change",
  "source": "aws.batch",
  "account": "aws_account_id",
  "time": "2017-10-23T17:56:03Z",
  "region": "us-east-1",
  "resources": [
    "arn:aws:batch:us-east-1:aws_account_id:job/4c7599ae-0a82-49aa-ba5a-4727fcce14a8"
  ],
  "detail": {
    "jobName": "event-test",
    "jobId": "4c7599ae-0a82-49aa-ba5a-4727fcce14a8",
    "jobQueue": "arn:aws:batch:us-east-1:aws_account_id:job-queue/HighPriority",
    "status": "RUNNABLE",
    "attempts": [],
    "createdAt": 1508781340401,
    "retryStrategy": {
      "attempts": 1
    },
    "dependsOn": [],
    "jobDefinition": "arn:aws:batch:us-east-1:aws_account_id:job-definition/first-run-job-definition:1",
    "parameters": {},
    "container": {
      "image": "busybox",
      "resourceRequirements": [
          {
              "type": "MEMORY",
              "value": "2000"
          },
          {
              "type": "VCPU",
              "value": "2"
          }
      ],
      "command": [
        "echo",
        "'hello world'"
      ],
      "volumes": [],
      "environment": [],
      "mountPoints": [],
      "ulimits": []
    }
  }
}
```