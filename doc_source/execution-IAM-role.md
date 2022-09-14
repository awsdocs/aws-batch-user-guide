# AWS Batch execution IAM role<a name="execution-IAM-role"></a>

The execution role grants the Amazon ECS container and AWS Fargate agents permission to make AWS API calls on your behalf\.

**Note**  
The execution role is supported by Amazon ECS container agent version 1\.16\.0 and later\.

The execution IAM role is required depending on the requirements of your task\. You can have multiple execution roles for different purposes and services associated with your account\.

**Note**  
For information about the Amazon ECS instance role, see [Amazon ECS instance role](instance_IAM_role.md)\. For information about service roles, see [How AWS Batch works with IAM](security_iam_service-with-iam.md)\. 

Amazon ECS provides the `AmazonECSTaskExecutionRolePolicy` managed policy\. This policy contains the required permissions for the common use cases described above\. It might be necessary to add inline policies to your execution role for the special use cases outlined below\.

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

An execution role is automatically created for you in the AWS Batch console first\-run experience\. However, you must manually attach the managed IAM policy to let Amazon ECS add permissions for future features and enhancements as they're introduced\. You can use the following procedure to check that your account already has the execution role and to attach the managed IAM policy, if needed\.<a name="procedure_check_execution_role"></a>

**To check for the `ecsTaskExecutionRole` in the IAM console**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Roles**\. 

1. Search the list of roles for `ecsTaskExecutionRole`\. If you can't find the role, see [Creating the execution IAM role](#create-execution-role)\. If you found the role, choose the role to view the attached policies\.

1. On the **Permissions** tab, verify that the **AmazonECSTaskExecutionRolePolicy** managed policy is attached to the role\. If the policy is attached, your execution role is properly configured\. If not, follow the substeps below to attach the policy\.

   1. Choose **Add permissions**, then choose **Attach policies**\.

   1. Search for **AmazonECSTaskExecutionRolePolicy**\.

   1. Check the box to the left of the **AmazonECSTaskExecutionRolePolicy** policy and choose **Attach policies**\.

1. Choose **Trust relationships**\.

1. Verify that the trust relationship contains the following policy\. If the trust relationship matches the policy below, the role is configured correctly\. If the trust relationship does not match, choose **Edit trust policy**, enter the following, and choose **Update policy**\.

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

If your account doesn't already have an execution role, use the following steps to create the role\.

**To create the `ecsTaskExecutionRole` IAM role**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Roles**\. 

1. Choose **Create role**\. 

1. For **Trusted entity type**, choose AWS service\.

1. For **Common use case**, choose **EC2**\.

1. Choose **Next**\.

1. For **Permissions policies**, search for **AmazonECSTaskExecutionRolePolicy**\.

1. Choose the check box to the left of the **AmazonECSTaskExecutionRolePolicy** policy, and then choose **Next**\.

1. For **Role Name**, enter `ecsTaskExecutionRole` and then choose **Create role**\.