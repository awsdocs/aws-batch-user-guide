# Job Definition Template<a name="job-definition-template"></a>

An empty job definition template is shown below\. You can use this template to create your job definition, which can then be saved to a file and used with the AWS CLI `--cli-input-json` option\. For more information about these parameters, see [Job Definition Parameters](job_definition_parameters.md)\.

```
{
    "jobDefinitionName": "",
    "type": "container",
    "parameters": {
        "KeyName": ""
    },
    "containerProperties": {
        "image": "",
        "vcpus": 0,
        "memory": 0,
        "command": [
            ""
        ],
        "jobRoleArn": "",
        "volumes": [
            {
                "host": {
                    "sourcePath": ""
                },
                "name": ""
            }
        ],
        "environment": [
            {
                "name": "",
                "value": ""
            }
        ],
        "mountPoints": [
            {
                "containerPath": "",
                "readOnly": true,
                "sourceVolume": ""
            }
        ],
        "readonlyRootFilesystem": true,
        "privileged": true,
        "ulimits": [
            {
                "hardLimit": 0,
                "name": "",
                "softLimit": 0
            }
        ],
        "user": "",
        "instanceType": "",
        "resourceRequirements": [
            {
                "value": "",
                "type": "GPU"
            }
        ],
        "linuxParameters": {
            "devices": [
                {
                    "hostPath": "",
                    "containerPath": "",
                    "permissions": [
                        "WRITE"
                    ]
                }
            ]
        }
    },
    "nodeProperties": {
        "numNodes": 0,
        "mainNode": 0,
        "nodeRangeProperties": [
            {
                "targetNodes": "",
                "container": {
                    "image": "",
                    "vcpus": 0,
                    "memory": 0,
                    "command": [
                        ""
                    ],
                    "jobRoleArn": "",
                    "volumes": [
                        {
                            "host": {
                                "sourcePath": ""
                            },
                            "name": ""
                        }
                    ],
                    "environment": [
                        {
                            "name": "",
                            "value": ""
                        }
                    ],
                    "mountPoints": [
                        {
                            "containerPath": "",
                            "readOnly": true,
                            "sourceVolume": ""
                        }
                    ],
                    "readonlyRootFilesystem": true,
                    "privileged": true,
                    "ulimits": [
                        {
                            "hardLimit": 0,
                            "name": "",
                            "softLimit": 0
                        }
                    ],
                    "user": "",
                    "instanceType": "",
                    "resourceRequirements": [
                        {
                            "value": "",
                            "type": "GPU"
                        }
                    ],
                    "linuxParameters": {
                        "devices": [
                            {
                                "hostPath": "",
                                "containerPath": "",
                                "permissions": [
                                    "READ"
                                ]
                            }
                        ]
                    }
                }
            }
        ]
    },
    "retryStrategy": {
        "attempts": 0
    },
    "timeout": {
        "attemptDurationSeconds": 0
    }
}
```

**Note**  
You can generate the above job definition template with the following AWS CLI command:  

```
$ aws batch register-job-definition --generate-cli-skeleton
```