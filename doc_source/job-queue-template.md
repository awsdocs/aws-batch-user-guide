# Job Queue Template<a name="job-queue-template"></a>

An empty job queue template is shown below\. You can use this template to create your job queue which can then be saved to a file and used with the AWS CLI `--cli-input-json` option\. For more information about these parameters, see [CreateJobQueue](https://docs.aws.amazon.com/batch/latest/APIReference/API_CreateJobQueue.html) in the *AWS Batch API Reference*\.

```
{
    "jobQueueName": "",
    "state": "",
    "priority": 0,
    "computeEnvironmentOrder": [{
        "order": 0,
        "computeEnvironment": ""
    }]
}
```

**Note**  
You can generate the above job queue template with the following AWS CLI command\.  

```
$ aws batch create-job-queue --generate-cli-skeleton
```