# Example policies<a name="ExamplePolicies_BATCH"></a>

The following examples show policy statements that you could use to control the permissions that IAM users have to AWS Batch\.

**Topics**
+ [Read\-only access](#iam-example-read-only)
+ [Restricting user, image, privilege, role](#iam-example-job-def)
+ [Restrict job submission](#iam-example-restrict-job-submission)
+ [Restrict job queue](#iam-example-restrict-job-queue)

## Example: Read\-only access<a name="iam-example-read-only"></a>

The following policy grants users permissions to use all AWS Batch API actions whose names start with `Describe` and `List`\.

Users don't have permission to perform any actions on the resources unless another statement grants them permission to do so\. This is because, by default, they're denied permission to use API actions\.

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

## Example: Restricting to POSIX user, Docker image, privilege level, and role on job submission<a name="iam-example-job-def"></a>

The following policy allows a user to manage their own set of restricted job definitions\.

The first and second statements allow a user to register and deregister any job definition name whose name is prefixed with *JobDefA\_*\.

The first statement also uses conditional context keys to restrict the POSIX user, privileged status, and container image values within the `containerProperties` of a job definition\. For more information, see [RegisterJobDefinition](https://docs.aws.amazon.com/batch/latest/APIReference/API_RegisterJobDefinition.html) in the *AWS Batch API Reference*\. In this example, job definitions can only be registered when the POSIX user is set to `nobody`, the privileged flag is set to `false`, and the image is set to `myImage` in an Amazon ECR repository\.

**Important**  
Docker resolves the `user` parameter to that user `uid` from within the container image\. In most cases, this is found in the `/etc/passwd` file within the container image\. This name resolution can be avoided by using direct `uid` values in both the job definition and any associated IAM policies\. Both the AWS Batch API operations and the `batch:User` IAM conditional keys support numeric values\.

The third statement restricts a user to passing only a specific role to a job definition\.

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

## Example: Restrict to job definition prefix on job submission<a name="iam-example-restrict-job-submission"></a>

The following policy allows a user to submit jobs to any job queue with any job definition name that begins with *JobDefA\_*\.

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

## Example: Restrict to job queue<a name="iam-example-restrict-job-queue"></a>

The following policy allows a user to submit jobs to a specific job queue, named **queue1**, with any job definition name\.

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