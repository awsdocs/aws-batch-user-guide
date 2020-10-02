# AWS Batch execution IAM role<a name="execution-IAM-role"></a>

The execution role grants the Amazon ECS container agent permission to make AWS API calls on your behalf\. The execution IAM role is required depending on the requirements of your task\. You can have multiple execution roles for different purposes and services associated with your account\.

**Note**  
The execution role is supported by Amazon ECS container agent version 1\.16\.0 and later\.

Amazon ECS provides the managed policy named `AmazonECSTaskExecutionRolePolicy` which contains the permissions the common use cases described above require\. It may be necessary to add inline policies to your execution role for special use cases which are outlined below\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ecr:GetAuthorizationToken",
        "ecr:BatchCheckLayerAvailability",
        "ecr:GetDownloadUrlForLayer",
        "ecr:BatchGetImage",
        "logs:CreateLogStream",
        "logs:PutLogEvents"
      ],
      "Resource": "*"
    }
  ]
}
```

An execution role is automatically created for you in the AWS Batch console first\-run experience; however, you should manually attach the managed IAM policy for tasks to allow Amazon ECS to add permissions for future features and enhancements as they are introduced\. You can use the following procedure to check and see if your account already has the execution role and to attach the managed IAM policy if needed\.<a name="procedure_check_execution_role"></a>

**To check for the `ecsTaskExecutionRole` in the IAM console**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Roles**\. 

1. Search the list of roles for `ecsTaskExecutionRole`\. If the role does not exist, see [Creating the execution IAM role](#create-execution-role)\. If the role does exist, select the role to view the attached policies\.

1. On the **Permissions** tab, verify that the **AmazonECSTaskExecutionRolePolicy** managed policy is attached to the role\. If the policy is attached, your execution role is properly configured\. If not, follow the substeps below to attach the policy\.

   1. Choose **Attach policies**\.

   1. To narrow the available policies to attach, for **Filter**, type **AmazonECSTaskExecutionRolePolicy**\.

   1. Check the box to the left of the **AmazonECSTaskExecutionRolePolicy** policy and choose **Attach policy**\.

1. Choose **Trust relationships**, **Edit trust relationship**\.

1. Verify that the trust relationship contains the following policy\. If the trust relationship matches the policy below, choose **Cancel**\. If the trust relationship does not match, copy the policy into the **Policy Document** window and choose **Update Trust Policy**\.

   ```
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Sid": "",
         "Effect": "Allow",
         "Principal": {
           "Service": "ecs-tasks.amazonaws.com"
         },
         "Action": "sts:AssumeRole"
       }
     ]
   }
   ```

## Creating the execution IAM role<a name="create-execution-role"></a>

If your account does not already have an execution role, use the following steps to create the role\.

**To create the `ecsTaskExecutionRole` IAM role**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Roles**, **Create role**\. 

1. In the **Select type of trusted entity** section, choose **Elastic Container Service**\.

1. For **Select your use case**, choose **Elastic Container Service Task**, then choose **Next: Permissions**\.

1. In the **Attach permissions policy** section, search for **AmazonECSTaskExecutionRolePolicy**, select the policy, and then choose **Next: Review**\.

1. For **Role Name**, type `ecsTaskExecutionRole` and choose **Create role**\.

## Required IAM permissions for private registry authentication<a name="execution-private-auth"></a>

The execution role is required to use the private registry authentication feature\. This allows the Amazon ECS container agent to pull the container image\. For more information, see [Private registry authentication](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/private-auth.html) in the *Amazon Elastic Container Service Developer Guide*\.

To provide access to the secrets that you create, manually add the following permissions as an inline policy to the execution role\. For more information, see [Adding and Removing IAM Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_manage-attach-detach.html) in the *IAM User Guide*\.
+ `secretsmanager:GetSecretValue`
+ `kms:Decrypt`—Required only if your key uses a custom AWS KMS key and not the default key\. The ARN for your custom key should be added as a resource\.

An example inline policy adding the permissions is shown below\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "kms:Decrypt",
        "secretsmanager:GetSecretValue"
      ],
      "Resource": [
        "arn:aws:secretsmanager:<region>:<aws_account_id>:secret:<secret_name>",
        "arn:aws:kms:<region>:<aws_account_id>:key/<key_id>"
      ]
    }
  ]
}
```

## Required IAM permissions for AWS Batch secrets<a name="execution-secrets"></a>

To use the AWS Batch secrets feature, you must have the execution role and reference it in your job definition\. This allows the Amazon ECS container agent to pull the necessary AWS Systems Manager or Secrets Manager resources\. For more information, see [Specifying sensitive data](specifying-sensitive-data.md)\.

To provide access to the AWS Systems Manager Parameter Store parameters that you create, manually add the following permissions as an inline policy to the execution role\. For more information, see [Adding and Removing IAM Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_manage-attach-detach.html) in the *IAM User Guide*\.
+ `ssm:GetParameters`—Required if you are referencing a Systems Manager Parameter Store parameter in a task definition\.
+ `secretsmanager:GetSecretValue`—Required if you are referencing a Secrets Manager secret either directly or if your Systems Manager Parameter Store parameter is referencing a Secrets Manager secret in a task definition\.
+ `kms:Decrypt`—Required only if your secret uses a custom AWS KMS key and not the default key\. The ARN for your custom key should be added as a resource\.

The following example inline policy adds the required permissions:

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "secretsmanager:GetSecretValue",
        "kms:Decrypt"
      ],
      "Resource": [
        "arn:aws:secretsmanager:<region>:<aws_account_id>:secret:<secret_name>",
        "arn:aws:kms:<region>:<aws_account_id>:key/<key_id>"
      ]
    }
  ]
}
```