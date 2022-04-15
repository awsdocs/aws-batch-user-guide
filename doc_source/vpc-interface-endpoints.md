# Access AWS Batch using an interface endpoint<a name="vpc-interface-endpoints"></a>

You can use AWS PrivateLink to create a private connection between your VPC and AWS Batch\. You can access AWS Batch as if it were in your VPC, without the use of an internet gateway, NAT device, VPN connection, or AWS Direct Connect connection\. Instances in your VPC don't need public IP addresses to access AWS Batch\.

You establish this private connection by creating an *interface endpoint*, powered by AWS PrivateLink\. We create an endpoint network interface in each subnet that you enable for the interface endpoint\. These are requester\-managed network interfaces that serve as the entry point for traffic destined for AWS Batch\.

For more information, see [Interface VPC endpoints](https://docs.aws.amazon.com/vpc/latest/privatelink/vpce-interface.html) in the *AWS PrivateLink Guide*\.

## Considerations for AWS Batch<a name="vpc-endpoint-considerations"></a>

Before you set up an interface endpoint for AWS Batch, review [Interface endpoint properties and limitations](https://docs.aws.amazon.com/vpc/latest/privatelink/vpce-interface.html#vpce-interface-limitations) in the *AWS PrivateLink Guide*\.

AWS Batch supports making calls to all of its API actions through the interface endpoint\. 

VPC endpoint policies are not supported for AWS Batch\. By default, full access to AWS Batch is allowed through the interface endpoint\. For more information, see [Control access to services using endpoint policies](https://docs.aws.amazon.com/vpc/latest/privatelink/vpc-endpoints-access.html) in the *AWS PrivateLink Guide*\.

Before you set up interface VPC endpoints for AWS Batch, be aware of the following considerations:
+ Jobs using Fargate resources launch type don't require the interface VPC endpoints for Amazon ECS, but you might need interface VPC endpoints for Amazon ECS, Amazon ECR, Secrets Manager, or Amazon CloudWatch Logs described in the following points\.
  + To run jobs, you must create the interface VPC endpoints for Amazon ECS\. For more information, see [Interface VPC Endpoints \(AWS PrivateLink\)](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/vpc-endpoints.html) in the *Amazon Elastic Container Service Developer Guide*\.
  + To allow your jobs to pull private images from Amazon ECR, you must create the interface VPC endpoints for Amazon ECR\. For more information, see [Interface VPC Endpoints \(AWS PrivateLink\)](https://docs.aws.amazon.com/AmazonECR/latest/userguide/vpc-endpoints.html) in the *Amazon Elastic Container Registry User Guide*\.
  + To allow your jobs to pull sensitive data from Secrets Manager, you must create the interface VPC endpoints for Secrets Manager\. For more information, see [Using Secrets Manager with VPC Endpoints](https://docs.aws.amazon.com/secretsmanager/latest/userguide/vpc-endpoint-overview.html) in the *AWS Secrets Manager User Guide*\.
  + If your VPC doesn't have an internet gateway and your jobs use the `awslogs` log driver to send log information to CloudWatch Logs, you must create an interface VPC endpoint for CloudWatch Logs\. For more information, see [Using CloudWatch Logs with Interface VPC Endpoints](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/cloudwatch-logs-and-interface-VPC.html) in the *Amazon CloudWatch Logs User Guide*\.
+ Jobs using the EC2 resources require that the container instances that they're launched on to run version `1.25.1` or later of the Amazon ECS container agent\. For more information, see [Amazon ECS Linux container agent versions](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-agent-versions.html) in the *Amazon Elastic Container Service Developer Guide*\.
+ VPC endpoints currently don't support cross\-Region requests\. Ensure that you create your endpoint in the same Region where you plan to issue your API calls to AWS Batch\.
+ VPC endpoints only support Amazon\-provided DNS through Amazon Route 53\. If you want to use your own DNS, you can use conditional DNS forwarding\. For more information, see [DHCP Options Sets](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_DHCP_Options.html) in the *Amazon VPC User Guide*\.
+ The security group attached to the VPC endpoint must allow incoming connections on port 443 from the private subnet of the VPC\.
+ AWS Batch does not support VPC interface endpoints in the following AWS Regions:
  + Asia Pacific \(Osaka\) \(`ap-northeast-3`\)
  + Asia Pacific \(Jakarta\) \(`ap-southeast-3`\)

## Create an interface endpoint for AWS Batch<a name="vpc-endpoint-create"></a>

You can create an interface endpoint for AWS Batch using either the Amazon VPC console or the AWS Command Line Interface \(AWS CLI\)\. For more information, see [Create an interface endpoint](https://docs.aws.amazon.com/vpc/latest/privatelink/vpce-interface.html#create-interface-endpoint) in the *AWS PrivateLink Guide*\.

Create an interface endpoint for AWS Batch using the following service name:

```
com.amazonaws.region.batch
```

For example:

```
com.amazonaws.us-east-2.batch
```

In the `aws-cn` partition, the format is different:

```
cn.com.amazonaws.region.batch
```

For example:

```
cn.com.amazonaws.cn-northwest-1.batch
```

If you enable private DNS for the interface endpoint, you can make API requests to AWS Batch using its default Regional DNS name\. For example, `batch.us-east-1.amazonaws.com`\.

For more information, see [Access a service through an interface endpoint](https://docs.aws.amazon.com/vpc/latest/privatelink/vpce-interface.html#access-service-though-endpoint) in the *AWS PrivateLink Guide*\.

## Create an endpoint policy for your interface endpoint<a name="vpc-endpoint-policy"></a>

An endpoint policy is an IAM resource that you can attach to an interface endpoint\. The default endpoint policy allows full access to AWS Batch through the interface endpoint\. To control the access allowed to AWS Batch from your VPC, attach a custom endpoint policy to the interface endpoint\.

An endpoint policy specifies the following information:
+ The principals that can perform actions \(AWS accounts, IAM users, and IAM roles\)\.
+ The actions that can be performed\.
+ The resources on which the actions can be performed\.

For more information, see [Control access to services using endpoint policies](https://docs.aws.amazon.com/vpc/latest/privatelink/vpc-endpoints-access.html) in the *AWS PrivateLink Guide*\.

**Example: VPC endpoint policy for AWS Batch actions**  
The following is an example of a custom endpoint policy\. When you attach this policy to your interface endpoint, it grants access to the listed AWS Batch actions for all principals on all resources\.

```
{
   "Statement": [
      {
         "Principal": "*",
         "Effect": "Allow",
         "Action": [
            "batch:SubmitJob",
            "batch:ListJobs",
            "batch:DescribeJobs"
         ],
         "Resource":"*"
      }
   ]
}
```