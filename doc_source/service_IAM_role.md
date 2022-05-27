# AWS Batch service IAM role<a name="service_IAM_role"></a>

AWS Batch makes calls to other AWS services on your behalf to manage the resources that you use with AWS Batch\. Before you can use the service, you must have an IAM policy and role that provides the necessary permissions to AWS Batch\.

In most cases, the AWS Batch service role is created for you automatically in the console first\-run experience\. You can use the following procedure to check if your account already has the AWS Batch service role\.

The `AWSBatchServiceRole` policy is as follows\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ec2:DescribeAccountAttributes",
                "ec2:DescribeInstances",
                "ec2:DescribeInstanceAttribute",
                "ec2:DescribeSubnets",
                "ec2:DescribeSecurityGroups",
                "ec2:DescribeKeyPairs",
                "ec2:DescribeImages",
                "ec2:DescribeImageAttribute",
                "ec2:DescribeInstanceStatus",
                "ec2:DescribeSpotInstanceRequests",
                "ec2:DescribeSpotFleetInstances",
                "ec2:DescribeSpotFleetRequests",
                "ec2:DescribeSpotPriceHistory",
                "ec2:DescribeVpcClassicLink",
                "ec2:DescribeLaunchTemplateVersions",
                "ec2:CreateLaunchTemplate",
                "ec2:DeleteLaunchTemplate",
                "ec2:RequestSpotFleet",
                "ec2:CancelSpotFleetRequests",
                "ec2:ModifySpotFleetRequest",
                "ec2:TerminateInstances",
                "ec2:RunInstances",
                "autoscaling:DescribeAccountLimits",
                "autoscaling:DescribeAutoScalingGroups",
                "autoscaling:DescribeLaunchConfigurations",
                "autoscaling:DescribeAutoScalingInstances",
                "autoscaling:CreateLaunchConfiguration",
                "autoscaling:CreateAutoScalingGroup",
                "autoscaling:UpdateAutoScalingGroup",
                "autoscaling:SetDesiredCapacity",
                "autoscaling:DeleteLaunchConfiguration",
                "autoscaling:DeleteAutoScalingGroup",
                "autoscaling:CreateOrUpdateTags",
                "autoscaling:SuspendProcesses",
                "autoscaling:PutNotificationConfiguration",
                "autoscaling:TerminateInstanceInAutoScalingGroup",
                "ecs:DescribeClusters",
                "ecs:DescribeContainerInstances",
                "ecs:DescribeTaskDefinition",
                "ecs:DescribeTasks",
                "ecs:ListAccountSettings",
                "ecs:ListClusters",
                "ecs:ListContainerInstances",
                "ecs:ListTaskDefinitionFamilies",
                "ecs:ListTaskDefinitions",
                "ecs:ListTasks",
                "ecs:CreateCluster",
                "ecs:DeleteCluster",
                "ecs:RegisterTaskDefinition",
                "ecs:DeregisterTaskDefinition",
                "ecs:RunTask",
                "ecs:StartTask",
                "ecs:StopTask",
                "ecs:UpdateContainerAgent",
                "ecs:DeregisterContainerInstance",
                "logs:CreateLogGroup",
                "logs:CreateLogStream",
                "logs:PutLogEvents",
                "logs:DescribeLogGroups",
                "iam:GetInstanceProfile",
                "iam:GetRole"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": "ecs:TagResource",
            "Resource": [
                "arn:aws:ecs:*:*:task/*_Batch_*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": "iam:PassRole",
            "Resource": [
                "*"
            ],
            "Condition": {
                "StringEquals": {
                    "iam:PassedToService": [
                        "ec2.amazonaws.com",
                        "ec2.amazonaws.com.cn",
                        "ecs-tasks.amazonaws.com"
                    ]
                }
            }
        },
        {
            "Effect": "Allow",
            "Action": "iam:CreateServiceLinkedRole",
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "iam:AWSServiceName": [
                        "spot.amazonaws.com",
                        "spotfleet.amazonaws.com",
                        "autoscaling.amazonaws.com",
                        "ecs.amazonaws.com"
                    ]
                }
            }
        },
        {
            "Effect": "Allow",
            "Action": [
                "ec2:CreateTags"
            ],
            "Resource": [
                "*"
            ],
            "Condition": {
                "StringEquals": {
                    "ec2:CreateAction": "RunInstances"
                }
            }
        }
    ]
}
```<a name="procedure_check_service_role"></a>

**To check for the `AWSServiceRoleForBatch` role in the IAM console**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Roles**\. 

1. Search the list of roles for `AWSServiceRoleForBatch`\. 
**Note**  
If the `AWSServiceRoleForBatch` role doesn't exist, follow the procedure below to create the role\.

1. Choose `AWSServiceRoleForBatch` to view the attached policies\.

1. For **Permission policies**, verify that the **BatchServiceRolePolicy** policy is attached to the role\. If the policy is attached, your AWS Batch service role is properly configured\. 
**Note**  
The Amazon Resource Name \(ARN\) for the **AWSServiceRoleForBatch** role is in the following format:  
`arn:aws::iam::aws_account_id:role/aws-service-role/batch.amazonaws.com/AWSServiceRoleForBatch`

1. Choose **Trust relationships**\.

1. Verify that the trust relationship contains the following\.

   

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Principal": {
                   "Service": "batch.amazonaws.com"
               },
               "Action": "sts:AssumeRole"
           }
       ]
   }
   ```

**To create the `AWSServiceRoleForBatch` IAM role:**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Roles**, then choose **Create Role**\. 

1. For **Trusted entity type**, choose **AWS service**\. 

1. For **Use cases for other AWS services**, choose **Batch**, then choose **Batch** again\.

1. Choose **Next**\.

1. For **Permissions policies**, verify that the **AWS BatchServiceRole **policy is attached, then choose **Next**\.
**Note**  
The ARN for the **AWSBatchServiceRole** policy is in the following format:  
`arn:aws::iam::aws:policy/service-role/AWSBatchServiceRole`

1. For **Role Name**, enter `AWSServiceRoleForBatch` and then enter a **Description**\.

1. Review the remaining steps\. Choose **Edit** to change the trusted relationships or permissions\.

1. Choose **Create role**\.