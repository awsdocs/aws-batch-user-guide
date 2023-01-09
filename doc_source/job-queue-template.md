# Job queue template<a name="job-queue-template"></a>

The following is an empty job queue template\. You can use this template to create your job queue\. You can then save this job queue to a file and use it with the AWS CLI `--cli-input-json` option\. For more information about these parameters, see [CreateJobQueue](https://docs.aws.amazon.com/batch/latest/APIReference/API_CreateJobQueue.html) in the *AWS Batch API Reference*\.

```
{
    "jobQueueName": "",
    "state": "ENABLED",
    "schedulingPolicyArn": "",
    "priority": 0,
    "computeEnvironmentOrder": [
        {
            "order": 0,
            "computeEnvironment": ""
        }
    ],
    "tags": {
        "KeyName": ""
    }
}
```

**Note**  
You can generate the preceding job queue template with the following AWS CLI command\.  

```
$ aws batch create-job-queue --generate-cli-skeleton
```