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