# AWS Batch IAM Policies, Roles, and Permissions<a name="IAM_policies"></a>

By default, IAM users don't have permission to create or modify AWS Batch resources, or perform tasks using the AWS Batch API\. This means that they also can't do so using the AWS Batch console or the AWS CLI\. To allow IAM users to create or modify resources and submit jobs, you must create IAM policies that grant IAM users permission to use the specific resources and API actions they need\. Then, attach those policies to the IAM users or groups that require those permissions\.

When you attach a policy to a user or group of users, it allows or denies the users permissions to perform the specified tasks on the specified resources\. For more information, see [Permissions and Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/PermissionsAndPolicies.html) in the *IAM User Guide*\. For more information about managing and creating custom IAM policies, see [Managing IAM Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/ManagingPolicies.html)\.

Likewise, AWS Batch makes calls to other AWS services on your behalf, so the service must authenticate with your credentials\. This authentication is accomplished by creating an IAM role and policy that can provide these permissions and then associating that role with your compute environments when you create them\. For more information, see [Amazon ECS Instance Role](instance_IAM_role.md), [IAM Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/roles-toplevel.html), [Using Service\-Linked Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html), and [Creating a Role to Delegate Permissions to an AWS Service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html) in the *IAM User Guide*\.

**Getting Started**

An IAM policy must grant or deny permissions to use one or more AWS Batch actions\.

**Topics**
+ [Policy Structure](iam-policy-structure.md)
+ [Supported Resource\-Level Permissions for AWS Batch API Actions](batch-supported-iam-actions-resources.md)
+ [Example Policies](ExamplePolicies_BATCH.md)
+ [AWS Batch Managed Policy](batch_managed_policies.md)
+ [Creating AWS Batch IAM Policies](batch_IAM_user_policies.md)
+ [AWS Batch Service IAM Role](service_IAM_role.md)
+ [Amazon ECS Instance Role](instance_IAM_role.md)
+ [Amazon EC2 Spot Fleet Role](spot_fleet_IAM_role.md)
+ [CloudWatch Events IAM Role](CWE_IAM_role.md)