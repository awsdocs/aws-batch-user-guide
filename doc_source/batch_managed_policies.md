# AWS Batch Managed Policy<a name="batch_managed_policies"></a>

AWS Batch provides a managed policy that you can attach to IAM users that provides permission to use AWS Batch resources and API operations\. You can apply this policy directly, or you can use it as a starting point for creating your own policies\. For more information about each API operation mentioned in these policies, see [Actions](https://docs.aws.amazon.com/batch/latest/APIReference/API_Operations.html) in the *AWS Batch API Reference*\.

## AWSBatchFullAccess<a name="AWSBatchFullAccess"></a>

This policy allows full administrator access to AWS Batch\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "batch:*",
                "cloudwatch:GetMetricStatistics",
                "ec2:DescribeSubnets",
                "ec2:DescribeSecurityGroups",
                "ec2:DescribeKeyPairs",
                "ec2:DescribeVpcs",
                "ec2:DescribeImages",
                "ec2:DescribeLaunchTemplates",
                "ec2:DescribeLaunchTemplateVersions",
                "ecs:DescribeClusters",
                "ecs:Describe*",
                "ecs:List*",
                "logs:Describe*",
                "logs:Get*",
                "logs:TestMetricFilter",
                "logs:FilterLogEvents",
                "iam:ListInstanceProfiles",
                "iam:ListRoles"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "iam:PassRole"
            ],
            "Resource": [
                "arn:aws:iam::*:role/AWSBatchServiceRole",
                "arn:aws:iam::*:role/service-role/AWSBatchServiceRole",
                "arn:aws:iam::*:role/ecsInstanceRole",
                "arn:aws:iam::*:instance-profile/ecsInstanceRole",
                "arn:aws:iam::*:role/iaws-ec2-spot-fleet-role",
                "arn:aws:iam::*:role/aws-ec2-spot-fleet-role",
                "arn:aws:iam::*:role/AWSBatchJobRole*"
            ]
        }
    ]
}
```