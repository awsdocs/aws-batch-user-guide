# Specifying sensitive data using Secrets Manager<a name="specifying-sensitive-data-secrets"></a>

With AWS Batch, you can inject sensitive data into your jobs by storing your sensitive data in AWS Secrets Manager secrets and then referencing them in your job definition\. Sensitive data stored in Secrets Manager secrets can be exposed to a job as environment variables or as part of the log configuration\.

When you inject a secret as an environment variable, you can specify a JSON key or version of a secret to inject\. This process helps you control the sensitive data exposed to your job\. For more information about secret versioning, see [Key Terms and Concepts for AWS Secrets Manager](https://docs.aws.amazon.com/secretsmanager/latest/userguide/terms-concepts.html#term_secret) in the *AWS Secrets Manager User Guide*\.

## Considerations for specifying sensitive data using Secrets Manager<a name="secrets-considerations"></a>

The following should be considered when using Secrets Manager to specify sensitive data for jobs\.
+ To inject a secret using a specific JSON key or version of a secret, the container instance in your compute environment must have version 1\.37\.0 or later of the Amazon ECS container agent installed\. However, we recommend using the latest container agent version\. For information about checking your agent version and updating to the latest version, see [Updating the Amazon ECS container agent](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-agent-update.html) in the *Amazon Elastic Container Service Developer Guide*\.

  To inject the full contents of a secret as an environment variable or to inject a secret in a log configuration, your container instance must have version 1\.22\.0 or later of the container agent\.
+ Only secrets that store text data, which are secrets created with the `SecretString` parameter of the [CreateSecret](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_CreateSecret.html) API, are supported\. Secrets that store binary data, which are secrets created with the `SecretBinary` parameter of the [CreateSecret](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_CreateSecret.html) API aren't supported\.
+ When using a job definition that references Secrets Manager secrets to retrieve sensitive data for your jobs, if you're also using interface VPC endpoints, you must create the interface VPC endpoints for Secrets Manager\. For more information, see [Using Secrets Manager with VPC Endpoints](https://docs.aws.amazon.com/secretsmanager/latest/userguide/vpc-endpoint-overview.html) in the *AWS Secrets Manager User Guide*\.
+ Sensitive data is injected into your job when the job is initially started\. If the secret is subsequently updated or rotated, the job doesn't receive the updated value automatically\. You must launch a new job to force the service to launch a fresh job with the updated secret value\.

## Required IAM permissions for AWS Batch secrets<a name="secrets-iam"></a>

To use this feature, you must have the execution role and reference it in your job definition\. This allows the container agent to pull the necessary Secrets Manager resources\. For more information, see [AWS Batch execution IAM role](execution-IAM-role.md)\.

**Important**  
It's necessary that you use the Amazon ECS container agent configuration variable `ECS_ENABLE_AWSLOGS_EXECUTIONROLE_OVERRIDE=true` to use this feature\. You can add it to the `./etc/ecs/ecs.config` file during container instance creation\. Or, you can add it to an existing instance, and then restart the Amazon ECS container agent\. For more information, see [Amazon ECS Container Agent Configuration](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-agent-config.html) in the *Amazon Elastic Container Service Developer Guide*\.

To provide access to the Secrets Manager secrets that you create, manually add the following permissions as an inline policy to the execution role\. For more information, see [Adding and Removing IAM Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_manage-attach-detach.html) in the *IAM User Guide*\.
+ `secretsmanager:GetSecretValue`–Required if you're referencing a Secrets Manager secret\.
+ `kms:Decrypt`–Required only if your secret uses a custom KMS key and not the default key\. The ARN for your custom key should be added as a resource\.

The following example inline policy adds the required permissions\.

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

## Injecting sensitive data as an environment variable<a name="secrets-envvar"></a>

Within your job definition, you can specify the following items:
+ The `secrets` object containing the name of the environment variable to set in the job
+ The Amazon Resource Name \(ARN\) of the Secrets Manager secret
+ Additional parameters that contain the sensitive data to present to the job

The following example shows the full syntax that must be specified for the Secrets Manager secret\.

```
arn:aws:secretsmanager:region:aws_account_id:secret:secret-name
```

The following section describes the additional parameters\. These parameters are optional\. However, if you don't use them, you must include the colons `:` to use the default values\. Examples are provided below for more context\.

`json-key`  
Specifies the name of the key in a key\-value pair with the value that you want to set as the environment variable value\. Only values in JSON format are supported\. If you don't specify a JSON key, then the full contents of the secret is used\.

`version-stage`  
Specifies the staging label of the version of a secret that you want to use\. If a version staging label is specified, you can't specify a version ID\. If no version stage is specified, the default behavior is to retrieve the secret with the `AWSCURRENT` staging label\.  
Staging labels are used to keep track of different versions of a secret when they are either updated or rotated\. Each version of a secret has one or more staging labels and an ID\. For more information, see [Key Terms and Concepts for AWS Secrets Manager](https://docs.aws.amazon.com/secretsmanager/latest/userguide/terms-concepts.html#term_secret) in the *AWS Secrets Manager User Guide*\.

`version-id`  
Specifies the unique identifier of the version of a secret that you want to use\. If a version ID is specified, you can't specify a version staging label\. If no version ID is specified, the default behavior is to retrieve the secret with the `AWSCURRENT` staging label\.  
Version IDs are used to keep track of different versions of a secret when they are either updated or rotated\. Each version of a secret has an ID\. For more information, see [Key Terms and Concepts for AWS Secrets Manager](https://docs.aws.amazon.com/secretsmanager/latest/userguide/terms-concepts.html#term_secret) in the *AWS Secrets Manager User Guide*\.

### Example container definitions<a name="secrets-examples"></a>

The following examples show ways that you can reference Secrets Manager secrets in your container definitions\.

**Example referencing a full secret**  
The following is a snippet of a task definition showing the format when referencing the full text of a Secrets Manager secret\.  

```
{
  "containerProperties": [{
    "secrets": [{
      "name": "environment_variable_name",
      "valueFrom": "arn:aws:secretsmanager:region:aws_account_id:secret:secret_name-AbCdEf"
    }]
  }]
}
```

**Example referencing a specific key within a secret**  
The following shows an example output from a [get\-secret\-value](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/get-secret-value.html) command that displays the contents of a secret along with the version staging label and version ID associated with it\.  

```
{
    "ARN": "arn:aws:secretsmanager:region:aws_account_id:secret:appauthexample-AbCdEf",
    "Name": "appauthexample",
    "VersionId": "871d9eca-18aa-46a9-8785-981dd39ab30c",
    "SecretString": "{\"username1\":\"password1\",\"username2\":\"password2\",\"username3\":\"password3\"}",
    "VersionStages": [
        "AWSCURRENT"
    ],
    "CreatedDate": 1581968848.921
}
```
Reference a specific key from the previous output in a container definition by specifying the key name at the end of the ARN\.  

```
{
  "containerProperties": [{
    "secrets": [{
      "name": "environment_variable_name",
      "valueFrom": "arn:aws:secretsmanager:region:aws_account_id:secret:appauthexample-AbCdEf:username1::"
    }]
  }]
}
```

**Example referencing a specific secret version**  
The following shows an example output from a [describe\-secret](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/describe-secret.html) command that displays the unencrypted contents of a secret along with the metadata for all versions of the secret\.  

```
{
    "ARN": "arn:aws:secretsmanager:region:aws_account_id:secret:appauthexample-AbCdEf",
    "Name": "appauthexample",
    "Description": "Example of a secret containing application authorization data.",
    "RotationEnabled": false,
    "LastChangedDate": 1581968848.926,
    "LastAccessedDate": 1581897600.0,
    "Tags": [],
    "VersionIdsToStages": {
        "871d9eca-18aa-46a9-8785-981dd39ab30c": [
            "AWSCURRENT"
        ],
        "9d4cb84b-ad69-40c0-a0ab-cead36b967e8": [
            "AWSPREVIOUS"
        ]
    }
}
```
Reference a specific version staging label from the previous output in a container definition by specifying the key name at the end of the ARN\.  

```
{
  "containerProperties": [{
    "secrets": [{
      "name": "environment_variable_name",
      "valueFrom": "arn:aws:secretsmanager:region:aws_account_id:secret:appauthexample-AbCdEf::AWSPREVIOUS:"
    }]
  }]
}
```
Reference a specific version ID from the previous output in a container definition by specifying the key name at the end of the ARN\.  

```
{
  "containerProperties": [{
    "secrets": [{
      "name": "environment_variable_name",
      "valueFrom": "arn:aws:secretsmanager:region:aws_account_id:secret:appauthexample-AbCdEf::9d4cb84b-ad69-40c0-a0ab-cead36b967e8"
    }]
  }]
}
```

**Example referencing a specific key and version staging label of a secret**  
The following shows how to reference both a specific key within a secret and a specific version staging label\.  

```
{
  "containerProperties": [{
    "secrets": [{
      "name": "environment_variable_name",
      "valueFrom": "arn:aws:secretsmanager:region:aws_account_id:secret:appauthexample-AbCdEf:username1:AWSPREVIOUS:"
    }]
  }]
}
```
To specify a specific key and version ID, use the following syntax\.  

```
{
  "containerProperties": [{
    "secrets": [{
      "name": "environment_variable_name",
      "valueFrom": "arn:aws:secretsmanager:region:aws_account_id:secret:appauthexample-AbCdEf:username1::9d4cb84b-ad69-40c0-a0ab-cead36b967e8"
    }]
  }]
}
```

## Injecting sensitive data in a log configuration<a name="secrets-logconfig"></a>

Within your job definition, when specifying a `logConfiguration` you can specify `secretOptions` with the name of the log driver option to set in the container and the full ARN of the Secrets Manager secret containing the sensitive data to present to the container\.

The following is a snippet of a job definition showing the format when referencing an Secrets Manager secret\.

```
{
  "containerProperties": [{
    "logConfiguration": [{
      "logDriver": "splunk",
      "options": {
        "splunk-url": "https://cloud.splunk.com:8080"
      },
      "secretOptions": [{
        "name": "splunk-token",
        "valueFrom": "arn:aws:secretsmanager:region:aws_account_id:secret:secret_name-AbCdEf"
      }]
    }]
  }]
}
```

## Creating an AWS Secrets Manager secret<a name="secrets-create-secret"></a>

You can use the Secrets Manager console to create a secret for your sensitive data\. For more information, see [Creating a Basic Secret](https://docs.aws.amazon.com/secretsmanager/latest/userguide/manage_create-basic-secret.html) in the *AWS Secrets Manager User Guide*\.

**To create a basic secret**

Use Secrets Manager to create a secret for your sensitive data\.

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. Choose **Store a new secret**\.

1. For **Select secret type**, choose **Other type of secrets**\.

1. Specify the details of your custom secret as **Key** and **Value** pairs\. For example, you can specify a key of `UserName`, and then supply the appropriate user name as its value\. Add a second key with the name of `Password` and the password text as its value\. You could also add entries for a database name, server address, or TCP port\. You can add as many pairs as you need to store the information you require\.

   Alternatively, you can choose the **Plaintext** tab and enter the secret value in any way you like\.

1. Choose the AWS KMS encryption key that you want to use to encrypt the protected text in the secret\. If you don't choose one, Secrets Manager checks to see if there's a default key for the account, and uses it if it exists\. If a default key doesn't exist, Secrets Manager creates one for you automatically\. You can also choose **Add new key** to create a custom CMK specifically for this secret\. To create your own AWS KMS CMK, you must have permissions to create CMKs in your account\.

1. Choose **Next**\.

1. For **Secret name**, type an optional path and name, such as **production/MyAwesomeAppSecret** or **development/TestSecret**, and choose **Next**\. You can optionally add a description to help you remember the purpose of this secret later\.

   The secret name must be ASCII letters, digits, or any of the following characters: /\_\+=\.@\-

1. \(Optional\) At this point, you can configure rotation for your secret\. For this procedure, leave it at **Disable automatic rotation** and choose **Next**\.

   For information about how to configure rotation on new or existing secrets, see [Rotating Your AWS Secrets Manager Secrets](https://docs.aws.amazon.com/secretsmanager/latest/userguide/rotating-secrets.html)\.

1. Review your settings, and then choose **Store secret** to save everything you entered as a new secret in Secrets Manager\.