# Scheduling policy template<a name="scheduling-policy-template"></a>

An empty scheduling policy template is shown below\. You can use this template to create your scheduling policy which can then be saved to a file and used with the AWS CLI `--cli-input-json` option\. For more information about these parameters, see [CreateSchedulingPolicy](https://docs.aws.amazon.com/batch/latest/APIReference/API_CreateSchedulingPolicy.html) in the *AWS Batch API Reference*\.

```
{
    "name": "",
    "fairsharePolicy": {
        "shareDecaySeconds": 0,
        "computeReservation": 0,
        "shareDistribution": [
            {
                "shareIdentifier": "",
                "weightFactor": 0.0
            }
        ]
    },
    "tags": {
        "KeyName": ""
    }
}
```

**Note**  
You can generate the preceding job queue template with the following AWS CLI command\.  

```
$ aws batch create-scheduling-policy --generate-cli-skeleton
```