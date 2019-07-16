# Tutorial: Sending Amazon Simple Notification Service Alerts for Failed Job Events<a name="batch_sns_tutorial"></a>

In this tutorial, you configure a CloudWatch Events event rule that only captures job events where the job has moved to a `FAILED` status\. At the end of this tutorial, you can submit a job to this job queue to test that you have configured your Amazon SNS alerts correctly\.

## Prerequisites<a name="batch_sns_prereq"></a>

This tutorial assumes that you have a working compute environment and job queue that are ready to accept jobs\. If you do not have a running compute environment and job queue to capture events from, follow the steps in [Getting Started with AWS Batch](Batch_GetStarted.md) to create one\. 

## Step 1: Create and Subscribe to an Amazon SNS Topic<a name="batch_sns_create_topic"></a>

 For this tutorial, you configure an Amazon SNS topic to serve as an event target for your new event rule\. 

**To create an Amazon SNS topic**

1. Open the Amazon SNS console at [https://console\.aws\.amazon\.com/sns/v3/home](https://console.aws.amazon.com/sns/v3/home)\.

1. Choose **Topics**, **Create new topic**\.

1. For **Topic name**, enter **JobFailedAlert** and choose **Create topic**\.

1. Select the topic that you just created\. On the **Topic details: JobFailedAlert** screen, choose **Create subscription**\. 

1. For **Protocol**, choose **Email**\. For **Endpoint**, enter an email address to which you currently have access and choose **Create subscription**\. 

1.  Check your email account, and wait to receive a subscription confirmation email message\. When you receive it, choose **Confirm subscription**\. 

## Step 2: Register Event Rule<a name="batch_sns_reg_rule"></a>

 Next, register an event rule that captures only job\-failed events\. 

**To create an event rule**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Events**, **Create rule**\.

1. Choose **Show advanced options**, **edit**\.

1. For **Build a pattern that selects events for processing by your targets**, replace the existing text with the following text: 

   ```
   {
     "detail-type": [
       "Batch Job State Change"
     ],
     "source": [
       "aws.batch"
     ],
     "detail": {
       "status": [
         "FAILED"
       ]
     }
   }
   ```

   This code defines a CloudWatch Events rule that matches any event where the job status is `FAILED`\. For more information about event patterns, see [Events and Event Patterns](https://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/CloudWatchEventsandEventPatterns.html) in the *Amazon CloudWatch User Guide*\. 

1. For **Targets**, choose **Add target**\. For **Target type**, choose **SNS topic**, **JobFailedAlert**\.

1. Choose **Configure details**\.

1. For **Rule definition**, type a name and description for your rule and then choose **Create rule**\.

## Step 3: Test Your Rule<a name="batch_sns_test_rule"></a>

 To test your rule, submit a job that exits shortly after it starts with a non\-zero exit code\. If your event rule is configured correctly, you receive an email message within a few minutes with the event text\. 

**To test a rule**

1. Open the AWS Batch console at [https://console\.aws\.amazon\.com/batch/](https://console.aws.amazon.com/batch/)\.

1. Submit a new AWS Batch job\. For more information, see [Submitting a Job](submit_job.md)\. For the job's command, substitute this command to exit the container with an exit code of 1\.

   ```
   /bin/sh, -c, 'exit 1'
   ```

1. Check your email to confirm that you have received an email alert for the failed job notification\.