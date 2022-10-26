# Amazon ECS instance role<a name="instance_IAM_role"></a>

AWS Batch compute environments are populated with Amazon ECS container instances\. They run the Amazon ECS container agent locally\. The Amazon ECS container agent makes calls to various AWS API operations on your behalf\. Therefore, container instances that run the agent require an IAM policy and role for these services to recognize that the agent belongs to you\. You must create an IAM role and an instance profile for the container instances to use when they're launched\. Otherwise, you can't create a compute environment and launch container instances into it\. This requirement applies to container instances launched with or without the Amazon ECS optimized AMI provided by Amazon\. For more information, see [Amazon ECS container instance IAM role](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/instance_IAM_role.html) in the *Amazon Elastic Container Service Developer Guide*\.

The Amazon ECS instance role and instance profile are automatically created for you in the console first\-run experience\. However, you can follow these steps to check if your account already has the Amazon ECS instance role and instance profile\. The following steps also cover how to attach the managed IAM policy\.<a name="procedure_check_instance_role"></a>

**To check for the `ecsInstanceRole` in the IAM console**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Roles**\. 

1. Search the list of roles for `ecsInstanceRole`\. If the role doesn't exist, use the following steps to create the role\.

   1. Choose **Create Role**\. 

   1. For **Trusted entity type**, choose **AWS service**\.

   1. For **Common use cases**, choose **EC2**\.

   1. Choose **Next**\.

   1. For **Permissions policies**, search for **AmazonEC2ContainerServiceforEC2Role**\.

   1. Choose the check box next to **AmazonEC2ContainerServiceforEC2Role**, then choose **Next**\.

   1. For **Role Name**, type `ecsInstanceRole` and choose **Create Role**\.
**Note**  
If you use the AWS Management Console to create a role for Amazon EC2, the console creates an instance profile with the same name as the role\.

Alternatively, you can use the AWS CLI to create the `ecsInstanceRole` IAM role\. The following example creates an IAM role with a trust policy and an AWS managed policy\.<a name="create-iam-role-cli"></a>

**To create an IAM role and instance profile \(AWS CLI\)**

1. Create the following trust policy and save it in a text file that's named `ecsInstanceRole-role-trust-policy.json`\.

   ```
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Principal": { "Service": "ec2.amazonaws.com"},
         "Action": "sts:AssumeRole"
       }
     ]
   }
   ```

1. Use the [create\-role](https://docs.aws.amazon.com/cli/latest/reference/iam/create-role.html) command to create the `ecsInstanceRole` role\. Specify the trust policy file location in the `assume-role-policy-document` parameter\.

   ```
   $ aws iam create-role \
       --role-name ecsInstanceRole \
       --assume-role-policy-document file://ecsInstanceRole-role-trust-policy.json
   ```

   The following is an example response\.

   ```
   {
       "Role": {
            "Path": "/",
            "RoleName: "ecsInstanceRole",
            "RoleId": "AROAT46P5RDIY4EXAMPLE",
            "Arn": "arn:aws:iam::123456789012:role/ecsInstanceRole".
           "CreateDate": "2022-12-12T23:46:37.247Z",
           "AssumeRolePolicyDocument": {
               "Version": "2012-10-17",
               "Statement": [
                   {
                       "Effect": "Allow",
                       "Principal": {
                           "Service: "ec2.amazonaws.com"
                       }
                       "Action": "sts:AssumeRole",
                    }
                 ]
             }
           }
   ```

1. Use the [create\-instance\-profile](https://docs.aws.amazon.com/cli/latest/reference/iam/create-instance-profile.html) command to create an instance profile that's named `ecsInstanceRole`\.
**Note**  
You need to create roles and instance profiles as separate actions in the AWS CLI and AWS API\. 

   ```
   $ aws iam create-instance-profile --instance-profile-name ecsInstanceRole
   ```

   The following is an example response\.

   ```
   {
       "InstanceProfile": {
           "Path": "/",
           "InstanceProfileName": "ecsInstanceRole",
           "InstanceProfileId": "AIPAT46P5RDITREXAMPLE",
           "Arn": "arn:aws:iam::123456789012:instance-profile/ecsInstanceRole",
           "CreateDate": "2022-06-30T23:53:34.093Z",
           "Roles": [],    }
   }
   ```

1. Use the [add\-role\-to\-instance\-profile](https://docs.aws.amazon.com/cli/latest/reference/iam/add-role-to-instance-profile.html) command to add the `ecsInstanceRole` role to the `ecsInstanceRole` instance profile\.

   ```
   aws iam add-role-to-instance-profile \
       --role-name ecsInstanceRole --instance-profile-name ecsInstanceRole
   ```

1. Use the [attach\-role\-policy](https://docs.aws.amazon.com/cli/latest/reference/iam/attach-role-policy.html) command to attach the `AmazonEC2ContainerServiceforEC2Role` AWS managed policy to the `ecsInstanceRole` role\.

   ```
   $ aws iam attach-role-policy \
       --policy-arn arn:aws:iam::aws:policy/service-role/AmazonEC2ContainerServiceforEC2Role \
       --role-name ecsInstanceRole
   ```