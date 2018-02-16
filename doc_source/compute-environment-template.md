# Compute Environment Template<a name="compute-environment-template"></a>

An empty compute environment template is shown below\. You can use this template to create your compute environment that can then be saved to a file and used with the AWS CLI `--cli-input-json` option\. For more information about these parameters, see [CreateComputeEnvironment](http://docs.aws.amazon.com/batch/latest/APIReference/API_CreateComputeEnvironment.html) in the *AWS Batch API Reference*\.

```
{
    "computeEnvironmentName": "",
    "type": "",
    "state": "",
    "computeResources": {
        "type": "",
        "minvCpus": 0,
        "maxvCpus": 0,
        "desiredvCpus": 0,
        "instanceTypes": [""],
        "imageId": "",
        "subnets": [""],
        "securityGroupIds": [""],
        "ec2KeyPair": "",
        "instanceRole": "",
        "tags": {"KeyName": ""},
        "bidPercentage": 0,
        "spotIamFleetRole": ""
    },
    "serviceRole": ""
}
```

**Note**  
You can generate the above task definition template with the following AWS CLI command\.  

```
$ aws batch create-compute-environment --generate-cli-skeleton
```