# AWS Batch Events<a name="batch_cwe_events"></a>

AWS Batch sends job status change events to EventBridge\. AWS Batch tracks the state of your jobs\. If a previously submitted job's status changes, an event is invoked\. For example, if a job in the `RUNNING` status moves to the `FAILED` status\. These events are classified as job state change events\.

**Note**  
AWS Batch might add other event types, sources, and details in the future\. If you're programmatically deserializing event JSON data, make sure that your application is prepared to handle unknown properties to avoid issues if and when these additional properties are added\.

## Job State Change Events<a name="batch_job_events"></a>

Any time that an existing \(previously submitted\) job changes states, an event is created\. For more information about AWS Batch job states, see [Job States](job_states.md)\.

**Note**  
Events aren't created for the initial job submission\.

**Example Job State Change Event**  
Job state change events are delivered in the following format \(the `detail` section resembles the [JobDetail](https://docs.aws.amazon.com/batch/latest/APIReference/API_JobDetail.html) object that's returned from a [DescribeJobs](https://docs.aws.amazon.com/batch/latest/APIReference/API_DescribeJobs.html) API operation in the *AWS Batch API Reference*\)\. For more information about EventBridge parameters, see [Events and Event Patterns](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-events.html) in the *Amazon EventBridge User Guide*\.  

```
{
    "version": "0",
    "id": "c8f9c4b5-76e5-d76a-f980-7011e206042b",
    "detail-type": "Batch Job State Change",
    "source": "aws.batch",
    "account": "123456789012",
    "time": "2022-01-11T23:36:40Z",
    "region": "us-east-1",
    "resources": [
        "arn:aws:batch:us-east-1:123456789012:job/4c7599ae-0a82-49aa-ba5a-4727fcce14a8"
    ],
    "detail": {
        "jobArn": "arn:aws:batch:us-east-1:123456789012:job/4c7599ae-0a82-49aa-ba5a-4727fcce14a8",
        "jobName": "event-test",
        "jobId": "4c7599ae-0a82-49aa-ba5a-4727fcce14a8",
        "jobQueue": "arn:aws:batch:us-east-1:123456789012:job-queue/PexjEHappyPathCanary2JobQueue",
        "status": "RUNNABLE",
        "attempts": [],
        "createdAt": 1641944200058,
        "retryStrategy": {
            "attempts": 2,
            "evaluateOnExit": []
        },
        "dependsOn": [],
        "jobDefinition": "arn:aws:batch:us-east-1:123456789012:job-definition/first-run-job-definition:1",
        "parameters": {},
        "container": {
            "image": "137112412989.dkr.ecr.us-east-1.amazonaws.com/amazonlinux:latest",
            "command": [
                "sleep",
                "600"
            ],
            "volumes": [],
            "environment": [],
            "mountPoints": [],
            "ulimits": [],
            "networkInterfaces": [],
            "resourceRequirements": [
                {
                    "value": "2",
                    "type": "VCPU"
                }, {
                    "value": "256",
                    "type": "MEMORY"
                }
            ],
            "secrets": []
        },
        "tags": {
            "resourceArn": "arn:aws:batch:us-east-1:123456789012:job/4c7599ae-0a82-49aa-ba5a-4727fcce14a8"
        },
        "propagateTags": false,
        "platformCapabilities": []
    }
}
```