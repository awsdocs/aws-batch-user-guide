# AWS Batch IAM policies, roles, and permissions<a name="IAM_policies"></a>

By default, IAM users don't have permission to create or modify AWS Batch resources or to perform tasks using the AWS Batch API\. They don't have these permissions for the AWS Batch console or the AWS CLI\. To allow IAM users to create or modify resources and submit jobs, create IAM policies that grant IAM users permission to use the specific resources and API operations that they need\. Then, attach those policies to the IAM users or groups that require those permissions\.

When you attach a policy to a user or group of users, the policy allows or denies the users permissions to perform the specific tasks on the specific resources\. For more information, see [Permissions and Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/PermissionsAndPolicies.html) in the *IAM User Guide*\. For more information about managing and creating custom IAM policies, see [Managing IAM Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/ManagingPolicies.html)\.

AWS Batch makes calls to other AWS services on your behalf\. As a result, AWS Batch must authenticate using your credentials\. More specifically, AWS Batch authenticates by creating an IAM role and policy that provides these permissions\. Then, it associates the role with your compute environments when you create them\. For more information, see [Amazon ECS instance role](instance_IAM_role.md), [IAM Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/roles-toplevel.html), [Using Service\-Linked Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html), and [Creating a Role to Delegate Permissions to an AWS Service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html) in the *IAM User Guide*\.

**Getting Started**

An IAM policy must grant or deny permissions to use one or more AWS Batch actions\.

**Topics**
+ [Policy structure](iam-policy-structure.md)
+ [Supported resource\-level permissions for AWS Batch API actions](batch-supported-iam-actions-resources.md)
+ [Example policies](ExamplePolicies_BATCH.md)
+ [AWS Batch managed policy](batch_managed_policies.md)
+ [Creating AWS Batch IAM policies](batch_IAM_user_policies.md)
+ [AWS Batch service IAM role](service_IAM_role.md)
+ [Amazon ECS instance role](instance_IAM_role.md)
+ [Amazon EC2 spot fleet role](spot_fleet_IAM_role.md)
+ [EventBridge IAM role](CWE_IAM_role.md)