# AWS Batch Jobs as CloudWatch Events Targets<a name="batch-cwe-target"></a>

Amazon CloudWatch Events delivers a near real\-time stream of system events that describe changes in Amazon Web Services resources\. AWS Batch jobs are available as CloudWatch Events targets\. Using simple rules that you can quickly set up, you can match events and submit AWS Batch jobs in response to them\. For more information, see [What is Amazon CloudWatch Events?](http://docs.aws.amazon.com/AmazonCloudWatch/latest/events/WhatIsCloudWatchEvents.html) in the *Amazon CloudWatch Events User Guide*\.

You can also use CloudWatch Events to schedule automated actions that self\-trigger at certain times using cron or rate expressions\. For more information, see [Schedule Expressions for Rules](http://docs.aws.amazon.com/AmazonCloudWatch/latest/events/ScheduledEvents.html) in the *Amazon CloudWatch Events User Guide*\.

Some common use cases for AWS Batch jobs as a CloudWatch Events target are:

+ Create a scheduled job that occurs at regular time intervals, such as a cron job that happens in low\-usage hours when Amazon EC2 Spot Instances are less expensive\.

+ Run an AWS Batch job in response to an API operation logged in CloudTrail, such as a job to automatically create a thumbnail of an image when it is uploaded to a specified Amazon S3 bucket\.
**Note**  
In this scenario, all of the AWS resources \(Amazon S3 bucket, CloudWatch Events rule, CloudTrail logs, etc\.\) must be in the same region\.

Before you can submit AWS Batch jobs with CloudWatch Events rules and targets, the CloudWatch Events service needs permissions to run AWS Batch jobs on your behalf\. When you create a rule in the CloudWatch Events console that specifies an AWS Batch job as a target, you are provided with an opportunity to create this role\. For more information about the required service principal and IAM permissions for this role, see [CloudWatch Events IAM Role](CWE_IAM_role.md)\.

The procedure below shows how to create a scheduled AWS Batch job and the required CloudWatch Events IAM role\.

**To create a scheduled AWS Batch job with CloudWatch Events**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the left navigation, choose **Events**, **Create rule**\.

1. For **Event source**, choose **Schedule**, and then whether to use a fixed interval schedule or a `cron` expression for your schedule rule\. For more information, see [Schedule Expressions for Rules](http://docs.aws.amazon.com/AmazonCloudWatch/latest/events/ScheduledEvents.html) in the *Amazon CloudWatch Events User Guide*\.

   + For **Fixed rate of**, enter the interval and unit for your schedule\.

   + For **Cron expression**, enter the `cron` expression for your task schedule\. These expressions have six required fields, and fields are separated by white space\. For more information, and examples of `cron` expressions, see [Cron Expressions](http://docs.aws.amazon.com/AmazonCloudWatch/latest/events/ScheduledEvents.html#CronExpressions) in the *Amazon CloudWatch Events User Guide*\.

1. For **Targets**, choose **Add target**\. 

1. Choose **Batch job queue** and fill in the following fields appropriately:

   + **Job queue:** Enter the Amazon Resource Name \(ARN\) of the job queue in which to schedule your job\.

   + **Job definition:** Enter the name and revision or full ARN of the job definition to use for your job\.

   + **Job name:** Enter a name for your job\.

   + **Array size:** \(Optional\) Enter an array size for your job to run more than one copy\. For more information, see [Array Jobs](array_jobs.md)\.

   + **Job attempts:** \(Optional\) Enter the number of times to retry your job if it fails\. For more information, see [Automated Job Retries](job_retries.md)\.

1. Choose an existing CloudWatch Events IAM role to use for your job, or **Create a new role for this specific resource** to create a new one\. For more information, see [CloudWatch Events IAM Role](CWE_IAM_role.md)\.

1. For **Rule definition**, fill in the following fields appropriately, and then choose **Create rule**\.

   + **Name:** Enter a name for your rule\.

   + **Description:** \(Optional\) Enter a description for your rule\.

   + **State:** Choose whether to enable your rule so that it begins scheduling at the next interval, or disable it until a later date\.