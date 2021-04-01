# AWS Batch Jobs as CloudWatch Events Targets<a name="batch-cwe-target"></a>

Amazon CloudWatch Events delivers a near real\-time stream of system events that describe changes in Amazon Web Services resources\. AWS Batch jobs are available as CloudWatch Events targets\. Using simple rules, you can match events and submit AWS Batch jobs in response to them\. For more information, see [What is Amazon CloudWatch Events?](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/WhatIsCloudWatchEvents.html) in the *Amazon CloudWatch Events User Guide*\.

You can also use CloudWatch Events to schedule automated actions that are invoked at certain times using cron or rate expressions\. For more information, see [Schedule expressions for rules](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/ScheduledEvents.html) in the *Amazon CloudWatch Events User Guide*\.

Common use cases for AWS Batch jobs as a CloudWatch Events target include the following use cases:
+ A scheduled job is created to occurs at regular time intervals\. For example, a cron job occurs only during low\-usage hours when Amazon EC2 Spot Instances are less expensive\.
+ An AWS Batch job runs in response to an API operation that's logged in CloudTrail\. For example, a job is submitted whenever an object is uploaded to a specified Amazon S3 bucket, with the CloudWatch Events input transformer passing the bucket and key name of the object to AWS Batch parameters each time\.
**Note**  
In this scenario, all of the AWS resources \(such as the Amazon S3 bucket, the CloudWatch Events rule, and all CloudTrail logs\) must be in the same Region\.

Before you can submit AWS Batch jobs with CloudWatch Events rules and targets, the CloudWatch Events service needs several permissions to run AWS Batch jobs on your behalf\. When you create a rule in the CloudWatch Events console that specifies an AWS Batch job as a target, you're provided with an opportunity to create this role\. For more information about the required service principal and IAM permissions for this role, see [CloudWatch Events IAM Role](CWE_IAM_role.md)\.

## Creating a Scheduled AWS Batch Job<a name="scheduled-batch-job"></a>

The procedure below shows how to create a scheduled AWS Batch job and the required CloudWatch Events IAM role\.

**To create a scheduled AWS Batch job with CloudWatch Events**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the left navigation, choose **Events**, **Create rule**\.

1. For **Event source**, choose **Schedule**, and then choose whether to use a fixed interval schedule or a `cron` expression for your schedule rule\. For more information, see [Schedule Expressions for Rules](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/ScheduledEvents.html) in the *Amazon CloudWatch Events User Guide*\.
   + For **Fixed rate of**, enter the interval and unit for your schedule\.
   + For **Cron expression**, enter the `cron` expression for your task schedule\. These expressions have six required fields\. Each field is separated by white space\. For more information and examples of `cron` expressions, see [Cron Expressions](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/ScheduledEvents.html#CronExpressions) in the *Amazon CloudWatch Events User Guide*\.

1. For **Targets**, choose **Add target**\. 

1. Choose **Batch job queue** and fill in the following fields appropriately:
   + **Job queue:** Enter the Amazon Resource Name \(ARN\) of the job queue to schedule your job in\.
   + **Job definition:** Enter the name and revision or full ARN of the job definition to use for your job\.
   + **Job name:** Enter a name for your job\.
   + **Array size:** \(Optional\) Enter an array size for your job to run more than one copy\. For more information, see [Array Jobs](array_jobs.md)\.
   + **Job attempts:** \(Optional\) Enter the number of times to retry your job if it fails\. For more information, see [Automated Job Retries](job_retries.md)\.

1. Choose an existing CloudWatch Events IAM role to use for your job, or **Create a new role for this specific resource** to create a new one\. For more information, see [CloudWatch Events IAM Role](CWE_IAM_role.md)\.

1. For **Rule definition**, fill in the following fields appropriately, and then choose **Create rule**\.
   + **Name:** Enter a name for your rule\.
   + **Description:** \(Optional\) Enter a description for your rule\.
   + **State:** Choose whether to enable your rule so that it begins scheduling at the next interval, or to enable it until later\.

## Passing Event Information to an AWS Batch Target using the CloudWatch Events Input Transformer<a name="cwe-input-transformer"></a>

You can use the CloudWatch Events input transformer to pass event information to AWS Batch in a job submission\. This can be especially valuable if you invoke jobs as a result of other AWS event information, such as an object upload to an Amazon S3 bucket\. You can also use a job definition with parameter substitution values in the container's command, and the CloudWatch Events input transformer can provide the parameter values based on the event data\. For example, the following job definition expects to see parameter values called *S3bucket* and *S3key*\.

```
{
  "jobDefinitionName": "echo-parameters",
  "containerProperties": {
    "image": "busybox",
    "vcpus": 2,
    "memory": 2000,
    "command": [
      "echo",
      "Ref::S3bucket",
      "Ref::S3key"
    ]
  }
}
```

Then, you simply create an AWS Batch event target that parses information from the event that starts it and transforms it into a `parameters` object\. When the job runs, the parameters from the trigger event are passed to the command of the job container\.

**Note**  
In this scenario, all of the AWS resources \(such as Amazon S3 buckets, CloudWatch Events rules, and CloudTrail logs\) must be in the same Region\.

**To create an AWS Batch target that uses the input transformer**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the left navigation, choose **Events**, **Create rule**\.

1. For **Event source**, choose **Event Pattern**, and then construct the rule as desired to match your application needs\.

1. For **Targets**, choose **Batch job queue** and then specify the job queue, job definition, and job name to use for the jobs that are invoked by this rule\.

1. Choose **Configure input**, and then choose **Input Transformer**\.

1. For the upper input transformer text box, specify the values to parse from the triggering event\. For example, to parse the bucket and key name from an Amazon S3 event, use the following JSON\.

   ```
   {"S3BucketValue":"$.detail.requestParameters.bucketName","S3KeyValue":"$.detail.requestParameters.key"}
   ```

1. For the lower input transformer text box, create the `Parameters` structure to pass to the AWS Batch job\. These parameters are substituted for the *Ref::S3bucket* and *Ref::S3key* placeholders in the command of the job container when the job runs\.

   ```
   {"Parameters" : {"S3bucket": <S3BucketValue>, "S3key": <S3KeyValue>}}
   ```

   You can also update the [ContainerOverrides](https://docs.aws.amazon.com/batch/latest/APIReference/API_ContainerOverrides.html) structure to pass to update commands, environment variables, and other settings\.

   ```
   {"Parameters" : {"S3bucket": <S3BucketValue>}, "ContainerOverrides" : {"Command": ["echo","Ref::S3bucket"]}}
   ```

1. Choose an existing CloudWatch Events IAM role to use for your job, or **Create a new role for this specific resource** to create a new one\. For more information, see [CloudWatch Events IAM Role](CWE_IAM_role.md)\.

1. Choose **Configure details** and then for **Rule definition**, fill in the following fields appropriately, and then choose **Create rule**\.
   + **Name:** Enter a name for your rule\.
   + **Description:** \(Optional\) Enter a description for your rule\.
   + **State:** Choose whether to enable your rule now or to enable it until later\.