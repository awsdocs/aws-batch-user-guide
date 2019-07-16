# Amazon ECS Instance Role<a name="instance_IAM_role"></a>

AWS Batch compute environments are populated with Amazon ECS container instances, and they run the Amazon ECS container agent locally\. The Amazon ECS container agent makes calls to various AWS APIs on your behalf, so container instances that run the agent require an IAM policy and role for these services to know that the agent belongs to you\. Before you can create a compute environment and launch container instances into it, you must create an IAM role and an instance profile for those container instances to use when they are launched\. This requirement applies to container instances launched with or without the Amazon ECS\-optimized AMI provided by Amazon\.

The Amazon ECS instance role and instance profile are automatically created for you in the console first\-run experience\. However, you can use the following procedure to check and see if your account already has the Amazon ECS instance role and instance profile and to attach the managed IAM policy if needed\.<a name="procedure_check_instance_role"></a>

**To check for the `ecsInstanceRole` in the IAM console**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Roles**\. 

1. Search the list of roles for `ecsInstanceRole`\. If the role does not exist, use the steps below to create the role\.

   1. Choose **Create Role**\. 

   1. For **Select type of trusted entity**, choose **AWS service**\. For **Choose the service that will use this role**, choose **Elastic Container Service**\. For **Select your use case**, choose **EC2 Role for Elastic Container Service**\.

   1. Choose **Next: Permissions**, **Next: Tags**, and **Next: Review**\.

   1. For **Role Name**, type `ecsInstanceRole` and choose **Create Role**\.