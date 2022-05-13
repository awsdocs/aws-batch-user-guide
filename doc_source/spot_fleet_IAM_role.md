# Amazon EC2 spot fleet role<a name="spot_fleet_IAM_role"></a>

If you create a managed compute environment that uses Amazon EC2 Spot Fleet Instances, you must create a role that grants the Spot Fleet permission to launch, tag, and terminate instances on your behalf\. Specify the role in your Spot Fleet request\. You must also have the **AWSServiceRoleForEC2Spot** and **AWSServiceRoleForEC2SpotFleet** service\-linked roles for Amazon EC2 Spot and Spot Fleet\. Use the following instruction to create all of these roles\. For more information, see [Using Service\-Linked Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html) and [Creating a Role to Delegate Permissions to an AWS Service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html) in the *IAM User Guide*\.

**Topics**
+ [Create Amazon EC2 spot fleet roles in the AWS Management Console](#spot-fleet-roles-console)
+ [Create Amazon EC2 Spot Fleet Roles with the AWS CLI](#spot-fleet-roles-cli)

## Create Amazon EC2 spot fleet roles in the AWS Management Console<a name="spot-fleet-roles-console"></a>

**To create the `AmazonEC2SpotFleetTaggingRole` IAM service\-linked role for Amazon EC2 Spot Fleet**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Roles**, **Create role**\. 

1. For **Select type of trusted entity**, choose **AWS service**\. Under **Or select a service to view its use cases**, choose **EC2**\.

1. In the **Select your use case** section, choose **EC2 \- Spot Fleet Tagging **\.

1. Choose **Next: Permissions**, **Next: Tags**, and **Next: Review**\.

1. For **Role Name**, type `AmazonEC2SpotFleetTaggingRole`\. Choose **Create role**\.

**Note**  
In the past, there have been two managed policies for the Amazon EC2 Spot Fleet role\.  
**AmazonEC2SpotFleetRole**: This is the original managed policy for the Spot Fleet role\. However, we no longer recommend you use it with AWS Batch\. This policy doesn't support Spot Instance tagging in compute environments, which is required to use the `AWSServiceRoleForBatch` service\-linked role\. If you previously created a Spot Fleet role with this policy, see [Spot Instances not tagged on creation](troubleshooting.md#spot-instance-no-tag) to apply the new recommended policy to that role\.
**AmazonEC2SpotFleetTaggingRole**: This role provides all of the necessary permissions to tag Amazon EC2 Spot Instances\. Use this role to allow Spot Instance tagging on your AWS Batch compute environments\.

## Create Amazon EC2 Spot Fleet Roles with the AWS CLI<a name="spot-fleet-roles-cli"></a>

**To create the **AmazonEC2SpotFleetTaggingRole** IAM role for your Spot Fleet compute environments**

1. Run the following command with the AWS CLI:

   ```
   aws iam create-role --role-name AmazonEC2SpotFleetTaggingRole \
       --assume-role-policy-document '{"Version":"2012-10-17","Statement":[{"Sid":"","Effect":"Allow","Principal":{"Service":"spotfleet.amazonaws.com"},"Action":"sts:AssumeRole"}]}'
   ```

1. To attach the **AmazonEC2SpotFleetTaggingRole** managed IAM policy to your **AmazonEC2SpotFleetTaggingRole** role, run the following command with the AWS CLI: 

   ```
   aws iam attach-role-policy \
       --policy-arn arn:aws:iam::aws:policy/service-role/AmazonEC2SpotFleetTaggingRole \
       --role-name AmazonEC2SpotFleetTaggingRole
   ```

**To create the `AWSServiceRoleForEC2Spot` IAM service\-linked role for Amazon EC2 Spot**
+ Run the following command with the AWS CLI:

  ```
  aws iam create-service-linked-role --aws-service-name spot.amazonaws.com
  ```

**To create the `AWSServiceRoleForEC2SpotFleet` IAM service\-linked role for Amazon EC2 Spot Fleet**
+ Run the following command with the AWS CLI: 

  ```
  aws iam create-service-linked-role --aws-service-name spotfleet.amazonaws.com
  ```