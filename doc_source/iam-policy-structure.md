# Policy Structure<a name="iam-policy-structure"></a>

The following topics explain the structure of an IAM policy\.


+ [Policy Syntax](#policy-syntax)
+ [Actions for AWS Batch](#UsingWithbatch_Actions)
+ [Amazon Resource Names for AWS Batch](#batch_ARN_Format)
+ [Checking That Users Have the Required Permissions](#check-required-permissions)

## Policy Syntax<a name="policy-syntax"></a>

An IAM policy is a JSON document that consists of one or more statements\. Each statement is structured as follows:

```
{
  "Statement":[{
    "Effect":"effect",
    "Action":"action",
    "Resource":"arn",
    "Condition":{
      "condition":{
        "key":"value"
        }
      }
    }
  ]
}
```

There are various elements that make up a statement:

+ **Effect:** The *effect* can be `Allow` or `Deny`\. By default, IAM users don't have permission to use resources and API actions, so all requests are denied\. An explicit allow overrides the default\. An explicit deny overrides any allows\.

+ **Action**: The *action* is the specific API action for which you are granting or denying permission\. To learn about specifying *action*, see [Actions for AWS Batch](#UsingWithbatch_Actions)\. 

+ **Resource**: The resource that's affected by the action\.  AWS Batch API operations currently do not support resource level permissions, so you must use the \* wildcard to specify that all resources can be affected by the action\. 

+ **Condition**: Conditions are optional\. They can be used to control when your policy is in effect\.

For more information about example IAM policy statements for AWS Batch, see [Creating AWS Batch IAM Policies](batch_IAM_user_policies.md)\. 

## Actions for AWS Batch<a name="UsingWithbatch_Actions"></a>

In an IAM policy statement, you can specify any API action from any service that supports IAM\. For AWS Batch, use the following prefix with the name of the API action: `batch:`\. For example: `batch:SubmitJob` and `batch:CreateComputeEnvironment`\.

To specify multiple actions in a single statement, separate them with commas as follows:

```
"Action": ["batch:action1", "batch:action2"]
```

You can also specify multiple actions using wildcards\. For example, you can specify all actions whose name begins with the word "Describe" as follows:

```
"Action": "batch:Describe*"
```

To specify all AWS Batch API actions, use the \* wildcard as follows:

```
"Action": "batch:*"
```

For a list of AWS Batch actions, see [Actions](http://docs.aws.amazon.com/batch/latest/APIReference/API_Operations.html) in the *AWS Batch API Reference*\.

## Amazon Resource Names for AWS Batch<a name="batch_ARN_Format"></a>

Each IAM policy statement applies to the resources that you specify using their ARNs\. 

An ARN has the following general syntax:

```
arn:aws:[service]:[region]:[account]:resourceType/resourcePath
```

*service*  
The service \(for example, `batch`\)\.

*region*  
The region for the resource \(for example, `us-east-1`\)\.

*account*  
The AWS account ID, with no hyphens \(for example, `123456789012`\)\.

*resourceType*  
The type of resource \(for example, `compute-environment`\)\.

*resourcePath*  
A path that identifies the resource\. You can use the \* wildcard in your paths\.

AWS Batch API operations currently do not support resource level permissions\. To specify all resources, use the \* wildcard in the `Resource` element as follows:

```
"Resource": "*"
```

## Checking That Users Have the Required Permissions<a name="check-required-permissions"></a>

After you've created an IAM policy, we recommend that you check whether it grants users the permissions to use the particular API actions and resources they need before you put the policy into production\.

First, create an IAM user for testing purposes, and then attach the IAM policy that you created to the test user\. Then, make a request as the test user\. You can make test requests in the console or with the AWS CLI\. 

**Note**  
You can also test your policies with the [IAM Policy Simulator](https://policysim.aws.amazon.com/home/index.jsp?#)\. For more information on the policy simulator, see [Working with the IAM Policy Simulator](http://docs.aws.amazon.com/IAM/latest/UserGuide/policies_testing-policies.html) in the *IAM User Guide*\.

If the policy doesn't grant the user the permissions that you expected, or is overly permissive, you can adjust the policy as needed and retest until you get the desired results\. 

**Important**  
It can take several minutes for policy changes to propagate before they take effect\. Therefore, we recommend that you allow five minutes to pass before you test your policy updates\.

If an authorization check fails, the request returns an encoded message with diagnostic information\. You can decode the message using the `DecodeAuthorizationMessage` action\. For more information, see [DecodeAuthorizationMessage](http://docs.aws.amazon.com/STS/latest/APIReference/API_DecodeAuthorizationMessage.html) in the *AWS Security Token Service API Reference*, and [decode\-authorization\-message](http://docs.aws.amazon.com/cli/latest/reference/sts/decode-authorization-message.html) in the *AWS CLI Command Reference*\.