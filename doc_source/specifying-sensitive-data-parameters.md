# Specifying sensitive data using Systems Manager Parameter Store<a name="specifying-sensitive-data-parameters"></a>

With AWS Batch, you can inject sensitive data into your containers by storing your sensitive data in AWS Systems Manager Parameter Store parameters and then referencing them in your container definition\.

**Topics**
+ [Considerations for specifying sensitive data using Systems Manager Parameter Store](#secrets--parameterstore-considerations)
+ [Required IAM permissions for AWS Batch secrets](#secrets-iam-parameters)
+ [Injecting sensitive data as an environment variable](#secrets-envvar-parameters)
+ [Injecting sensitive data in a log configuration](#secrets-logconfig-parameters)
+ [Creating an AWS Systems Manager Parameter Store parameter](#secrets-create-parameter)

## Considerations for specifying sensitive data using Systems Manager Parameter Store<a name="secrets--parameterstore-considerations"></a>

The following should be considered when specifying sensitive data for containers using Systems Manager Parameter Store parameters\.
+ This feature requires that your container instance have version 1\.22\.0 or later of the container agent\. However, we recommend using the latest container agent version\. For information about checking your agent version and updating to the latest version, see [Updating the Amazon ECS container agent](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-agent-update.html) in the *Amazon Elastic Container Service Developer Guide*\.
+ Sensitive data is injected into the container for your job when the container is initially started\. If the secret or Parameter Store parameter is subsequently updated or rotated, the container doesn't receive the updated value automatically\. You must launch a new job to force the launch of a fresh job with updated secrets\.

## Required IAM permissions for AWS Batch secrets<a name="secrets-iam-parameters"></a>

To use this feature, you must have the execution role and reference it in your job definition\. This allows the Amazon ECS container agent to pull the necessary AWS Systems Manager resources\. For more information, see [AWS Batch execution IAM role](execution-IAM-role.md)\.

To provide access to the AWS Systems Manager Parameter Store parameters that you create, manually add the following permissions as an inline policy to the execution role\. For more information, see [Adding and Removing IAM Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_manage-attach-detach.html) in the *IAM User Guide*\.
+ `ssm:GetParameters`—Required if you're referencing a Systems Manager Parameter Store parameter in a task definition\.
+ `secretsmanager:GetSecretValue`—Required if you're referencing a Secrets Manager secret either directly or if your Systems Manager Parameter Store parameter is referencing a Secrets Manager secret in a task definition\.
+ `kms:Decrypt`—Required only if your secret uses a custom KMS key and not the default key\. The ARN for your custom key should be added as a resource\.

The following example inline policy adds the required permissions:

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ssm:GetParameters",
        "secretsmanager:GetSecretValue",
        "kms:Decrypt"
      ],
      "Resource": [
        "arn:aws:ssm:<region>:<aws_account_id>:parameter/<parameter_name>",
        "arn:aws:secretsmanager:<region>:<aws_account_id>:secret:<secret_name>",
        "arn:aws:kms:<region>:<aws_account_id>:key/<key_id>"
      ]
    }
  ]
}
```

## Injecting sensitive data as an environment variable<a name="secrets-envvar-parameters"></a>

Within your container definition, specify `secrets` with the name of the environment variable to set in the container and the full ARN of the Systems Manager Parameter Store parameter containing the sensitive data to present to the container\.

The following is a snippet of a task definition showing the format when referencing an Systems Manager Parameter Store parameter\. If the Systems Manager Parameter Store parameter exists in the same Region as the task that you're launching, then you can use either the full ARN or name of the parameter\. If the parameter exists in a different Region, then the full ARN must be specified\.

```
{
  "containerProperties": [{
    "secrets": [{
      "name": "environment_variable_name",
      "valueFrom": "arn:aws:ssm:region:aws_account_id:parameter/parameter_name"
    }]
  }]
}
```

## Injecting sensitive data in a log configuration<a name="secrets-logconfig-parameters"></a>

Within your container definition, when specifying a `logConfiguration` you can specify `secretOptions` with the name of the log driver option to set in the container and the full ARN of the Systems Manager Parameter Store parameter containing the sensitive data to present to the container\.

**Important**  
If the Systems Manager Parameter Store parameter exists in the same Region as the task you're launching, then you can use either the full ARN or name of the parameter\. If the parameter exists in a different Region, then the full ARN must be specified\.

The following is a snippet of a task definition showing the format when referencing an Systems Manager Parameter Store parameter\.

```
{
  "containerProperties": [{
    "logConfiguration": [{
      "logDriver": "fluentd",
      "options": {
        "tag": "fluentd demo"
      },
      "secretOptions": [{
        "name": "fluentd-address",
        "valueFrom": "arn:aws:ssm:region:aws_account_id:parameter/parameter_name"
      }]
    }]
  }]
}
```

## Creating an AWS Systems Manager Parameter Store parameter<a name="secrets-create-parameter"></a>

You can use the AWS Systems Manager console to create a Systems Manager Parameter Store parameter for your sensitive data\. For more information, see [Walkthrough: Create and Use a Parameter in a Command \(Console\)](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-paramstore-console.html) in the *AWS Systems Manager User Guide*\.

**To create a Parameter Store parameter**

1. Open the AWS Systems Manager console at [https://console\.aws\.amazon\.com/systems\-manager/](https://console.aws.amazon.com/systems-manager/)\.

1. In the navigation pane, choose **Parameter Store**, **Create parameter**\.

1. For **Name**, type a hierarchy and a parameter name\. For example, type `test/database_password`\.

1. For **Description**, type an optional description\.

1. For **Type**, choose **String**, **StringList**, or **SecureString**\.
**Note**  
If you choose **SecureString**, the **KMS Key ID** field appears\. If you don't provide a KMS key ID, a KMS key ARN, an alias name, or an alias ARN, then the system uses `alias/aws/ssm`\. This is the default KMS key for Systems Manager\. To avoid using this key, choose a custom key\. For more information, see [Use Secure String Parameters](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-paramstore-about.html) in the *AWS Systems Manager User Guide*\.
When you create a secure string parameter in the console by using the `key-id` parameter with either a custom KMS key alias name or an alias ARN, you must specify the prefix `alias/` before the alias\. The following is an ARN example:  

     ```
     arn:aws:kms:us-east-2:123456789012:alias/MyAliasName
     ```
The following is an alias name example:  

     ```
     alias/MyAliasName
     ```

1. For **Value**, type a value\. For example, `MyFirstParameter`\. If you chose **SecureString**, the value is masked exactly as you entered it\.

1. Choose **Create parameter**\.