# Compute environment template<a name="compute-environment-template"></a>

The following example shows an empty compute environment template\. You can use this template to create your compute environment that can then be saved to a file and used with the AWS CLI `--cli-input-json` option\. For more information about these parameters, see [CreateComputeEnvironment](https://docs.aws.amazon.com/batch/latest/APIReference/API_CreateComputeEnvironment.html) in the *AWS Batch API Reference*\.

```
{
    "computeEnvironmentName": "",
    "type": "MANAGED",
    "state": "ENABLED",
    "unmanagedvCpus": 0,
    "computeResources": {
        "type": "FARGATE_SPOT",
        "allocationStrategy": "BEST_FIT_PROGRESSIVE",
        "minvCpus": 0,
        "maxvCpus": 0,
        "desiredvCpus": 0,
        "instanceTypes": [
            ""
        ],
        "imageId": "",
        "subnets": [
            ""
        ],
        "securityGroupIds": [
            ""
        ],
        "ec2KeyPair": "",
        "instanceRole": "",
        "tags": {
            "KeyName": ""
        },
        "placementGroup": "",
        "bidPercentage": 0,
        "spotIamFleetRole": "",
        "launchTemplate": {
            "launchTemplateId": "",
            "launchTemplateName": "",
            "version": ""
        },
        "ec2Configuration": [
            {
                "imageType": "",
                "imageIdOverride": ""
            }
        ]
    },
    "serviceRole": "",
    "tags": {
        "KeyName": ""
    }
}
```

**Note**  
You can generate the preceding compute environment template with the following AWS CLI command\.  

```
$ aws batch create-compute-environment --generate-cli-skeleton
```