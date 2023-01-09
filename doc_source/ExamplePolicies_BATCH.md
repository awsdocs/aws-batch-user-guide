# Example policies<a name="ExamplePolicies_BATCH"></a>

The following examples show policy statements that you can use to control the permissions that users have for AWS Batch\.

**Topics**
+ [Read\-only access](#iam-example-read-only)
+ [Restricting user, image, privilege, role](#iam-example-job-def)
+ [Restrict job submission](#iam-example-restrict-job-submission)
+ [Restrict job queue](#iam-example-restrict-job-queue)
+ [Deny action when condition all keys match strings](#iam-example-job-def-deny-all-image-logdriver)
+ [Deny action when any condition keys match strings](#iam-example-job-def-deny-any-image-logdriver)
+ [Use the `batch:ShareIdentifier` condition key](#iam-example-share-identifier)

## Read\-only access<a name="iam-example-read-only"></a>

The following policy grants users permissions to use all AWS Batch API actions with a name that starts with `Describe` and `List`\.

Unless another statement grants them permission to do so, users don't have permission to perform any actions on the resources\. By default, they're denied permission to use API actions\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "batch:Describe*",
                "batch:List*"
            ],
            "Resource": "*"
        }
    ]
}
```

## Restricting to POSIX user, Docker image, privilege level, and role on job submission<a name="iam-example-job-def"></a>

The following policy allows a POSIX user to manage their own set of restricted job definitions\.

Use the first and second statements toregister and deregister any job definition name whose name is prefixed with *JobDefA\_*\.

The first statement also uses conditional context keys to restrict the POSIX user, privileged status, and container image values within the `containerProperties` of a job definition\. For more information, see [RegisterJobDefinition](https://docs.aws.amazon.com/batch/latest/APIReference/API_RegisterJobDefinition.html) in the *AWS Batch API Reference*\. In this example, job definitions can only be registered when the POSIX user is set to `nobody`\. The privileged flag is set to `false`\. Last, the image is set to `myImage` in an Amazon ECR repository\.

**Important**  
Docker resolves the `user` parameter to that user `uid` from within the container image\. In most cases, this is found in the `/etc/passwd` file within the container image\. This name resolution can be avoided by using direct `uid` values in both the job definition and any associated IAM policies\. Both the AWS Batch API operations and the `batch:User` IAM conditional keys support numeric values\.

Use the third statement to restrict to only a specific role to a job definition\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "batch:RegisterJobDefinition"
            ],
            "Resource": [
                "arn:aws:batch:<aws_region>:<aws_account_id>:job-definition/JobDefA_*"
            ],
            "Condition": {
                "StringEquals": {
                    "batch:User": [
                        "nobody"
                    ],
                    "batch:Image": [
                        "<aws_account_id>.dkr.ecr.<aws_region>.amazonaws.com/myImage"
                    ]
                },
                "Bool": {
                    "batch:Privileged": "false"
                }
            }
        },
        {
            "Effect": "Allow",
            "Action": [
                "batch:DeregisterJobDefinition"
            ],
            "Resource": [
                "arn:aws:batch:<aws_region>:<aws_account_id>:job-definition/JobDefA_*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "iam:PassRole"
            ],
            "Resource": [
                "arn:aws:iam::<aws_account_id>:role/MyBatchJobRole"
            ]
        }
    ]
}
```

## Restrict to job definition prefix on job submission<a name="iam-example-restrict-job-submission"></a>

Use the following policy to submit jobs to any job queue with any job definition name that starts with *JobDefA*\.

**Important**  
When scoping resource\-level access for job submission, you must provide both job queue and job definition resource types\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "batch:SubmitJob"
      ],
      "Resource": [
        "arn:aws:batch:<aws_region>:<aws_account_id>:job-definition/JobDefA_*",
        "arn:aws:batch:<aws_region>:<aws_account_id>:job-queue/*"
      ]
    }
  ]
}
```

## Restrict to job queue<a name="iam-example-restrict-job-queue"></a>

Use the following policy to submit jobs to a specific job queue that's named **queue1** with any job definition name\.

**Important**  
When scoping resource\-level access for job submission, you must provide both job queue and job definition resource types\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "batch:SubmitJob"
      ],
      "Resource": [
        "arn:aws:batch:<aws_region>:<aws_account_id>:job-definition/*",
        "arn:aws:batch:<aws_region>:<aws_account_id>:job-queue/queue1"
      ]
    }
  ]
}
```

## Deny action when condition all keys match strings<a name="iam-example-job-def-deny-all-image-logdriver"></a>

The following policy denies access to the [https://docs.aws.amazon.com/batch/latest/APIReference/API_RegisterJobDefinition.html](https://docs.aws.amazon.com/batch/latest/APIReference/API_RegisterJobDefinition.html) API operation when both the `batch:Image` \(container image ID\) condition key is "*string1*" and the `batch:LogDriver` \(container log driver\) condition key is "*string2*\." AWS Batch evaluates condition keys on each container\. When a job spans multiple containers such as a multi\-node parallel job, it's possible for the containers to have different configurations\. If multiple condition keys are evaluated in one statement, they're combined using `AND` logic\. So, if any of the multiple condition keys doesn't match for a container, the `Deny` effect isn't applied for that container\. Rather, a different container in the same job might be denied\.

For the list of condition keys for AWS Batch, see [Condition keys for AWS Batch](https://docs.aws.amazon.com/service-authorization/latest/reference/list_awsbatch.html#awsbatch-policy-keys) in the *Service Authorization Reference*\. Except for `batch:ShareIdentifier`, all `batch` condition keys can be used in this way\. The `batch:ShareIdentifier` condition key is defined for a job, not a job definition\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "batch:RegisterJobDefinition"
      ],
      "Resource": [
        "*"
      ]
    },
    {
      "Effect": "Deny",
      "Action": "batch:RegisterJobDefinition",
      "Resource": "*",
      "Condition": {
        "StringEquals": {
          "batch:Image": "string1",
          "batch:LogDriver": "string2"
        }
      }
    }
  ]
}
```

## Deny action when any condition keys match strings<a name="iam-example-job-def-deny-any-image-logdriver"></a>

The following policy denies access to the [https://docs.aws.amazon.com/batch/latest/APIReference/API_RegisterJobDefinition.html](https://docs.aws.amazon.com/batch/latest/APIReference/API_RegisterJobDefinition.html) API operation when either the `batch:Image` \(container image ID\) condition key is "*string1*" or the `batch:LogDriver` \(container log driver\) condition key is "*string2*\." When a job spans multiple containers such as a multi\-node parallel job, it's possible for the containers to have different configurations\. If multiple condition keys are evaluated in one statement, they're combined using `AND` logic\. So, if any of the multiple condition keys doesn't match for a container, the `Deny` effect isn't applied for that container\. Rather, a different container in the same job might be denied\.

For the list of condition keys for AWS Batch, see [Condition keys for AWS Batch](https://docs.aws.amazon.com/service-authorization/latest/reference/list_awsbatch.html#awsbatch-policy-keys) in the *Service Authorization Reference*\. Except for `batch:ShareIdentifier`, all `batch` condition keys can can be used in this way\. \(The `batch:ShareIdentifier` condition key is defined for a job, not a job definition\.\)

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "batch:RegisterJobDefinition"
      ],
      "Resource": [
        "*"
      ]
    },
    {
      "Effect": "Deny",
      "Action": [
        "batch:RegisterJobDefinition"
      ],
      "Resource": [
        "*"
      ],
      "Condition": {
        "StringEquals": {
          "batch:Image": [
            "string1"
          ]
        }
      }
    },
    {
      "Effect": "Deny",
      "Action": [
        "batch:RegisterJobDefinition"
      ],
      "Resource": [
        "*"
      ],
      "Condition": {
        "StringEquals": {
          "batch:LogDriver": [
            "string2"
          ]
        }
      }
    }
  ]
}
```

## Use the `batch:ShareIdentifier` condition key<a name="iam-example-share-identifier"></a>

Use the following policy to submit jobs that use the `jobDefA` job definition to the `jobqueue1` job queue with the `lowCpu` share identifier\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "batch:SubmitJob"
      ],
      "Resource": [
        "arn:aws::batch:<aws_region>:<aws_account_id>:job-definition/JobDefA",
        "arn:aws::batch:<aws_region>:<aws_account_id>:job-queue/jobqueue1"
      ],
      "Condition": {
        "StringEquals": {
          "batch:ShareIdentifier": [
            "lowCpu"
          ]
        }
      }
    }
  ]
}
```