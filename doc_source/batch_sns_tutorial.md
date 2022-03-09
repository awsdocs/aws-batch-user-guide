# Tutorial: Sending Amazon Simple Notification Service Alerts for Failed Job Events<a name="batch_sns_tutorial"></a>

In this tutorial, you configure a EventBridge event rule that only captures job events where the job has moved to a `FAILED` status\. At the end of this tutorial, you can optionally also submit a job to this job queue\. This is to test that you have configured your Amazon SNS alerts correctly\.

## Prerequisites<a name="batch_sns_prereq"></a>

This tutorial assumes that you have a working compute environment and job queue that are ready to accept jobs\. If you don't have a running compute environment and job queue to capture events from, follow the steps in [Getting Started with AWS Batch](Batch_GetStarted.md) to create one\. 

## Step 1: Create and Subscribe to an Amazon SNS Topic<a name="batch_sns_create_topic"></a>

 For this tutorial, you configure an Amazon SNS topic to serve as an event target for your new event rule\. 

**To create an Amazon SNS topic**

1. Open the Amazon SNS console at [https://console\.aws\.amazon\.com/sns/v3/home](https://console.aws.amazon.com/sns/v3/home)\.

1. Choose **Topics**, **Create topic**\.

1. For **Topic name**, enter **JobFailedAlert** and choose **Create topic**\.

1. Select the topic that you just created\. On the **Topic details: JobFailedAlert** screen, choose **Create subscription**\. 

1. For **Protocol**, choose **Email**\. For **Endpoint**, enter an email address that you currently have access to and choose **Create subscription**\. 

1.  Check your email account, and wait to receive a subscription confirmation email message\. When you receive it, choose **Confirm subscription**\. 

## Step 2: Register Event Rule<a name="batch_sns_reg_rule"></a>

 Next, register an event rule that captures only job\-failed events\. 

**To register your EventBridge rule**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**\.

1. Choose **Create rule**\.

1. Enter a name and description for the rule\.

   A rule can't have the same name as another rule in the same Region and on the same event bus\.

1. For **Define pattern**, select **Event Pattern** as the event source, and then select **Custom pattern**\. 

1. Paste the following event pattern into the text area\.

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

   This code defines a EventBridge rule that matches any event where the job status is `FAILED`\. For more information about event patterns, see [Events and Event Patterns](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-events.html) in the *Amazon EventBridge User Guide*\.

1. This rule applies across all of your AWS Batch groups and to every AWS Batch event\. Alternatively, you can create a more specific rule to filter out some results\.

1. For **Select event bus**, choose **AWS default event bus**\. You can only create scheduled rules on the default event bus\.

1. For **Select targets**, in **Target**, choose **SNS topic**, and select **JobFailedAlert**\.

1. For **Retry policy and dead\-letter queue:**, under **Retry policy**:

   1. For **Maximum age of event**, enter a value between 1 minute \(00:01\) and 24 hours \(24:00\)\.

   1. For **Retry attempts**, enter a number between 0 and 185\.

   1. For **Dead\-letter queue**, choose whether to use a standard Amazon SQS queue as a dead\-letter queue\. EventBridge sends events that match this rule to the dead\-letter queue if it can't deliver them to the target\. Do one of the following:
      + Choose **None** to not use a dead\-letter queue\.
      + Choose **Select an Amazon SQS queue in the current AWS account to use as the dead\-letter queue** and then select the queue to use from the drop\-down list\.
      + Choose **Select an Amazon SQS queue in an other AWS account as a dead\-letter queue** and then enter the ARN of the queue to use\.

1. \(Optional\) Enter one or more tags for the rule\.

1. Choose **Create**\.

## Step 3: Test Your Rule<a name="batch_sns_test_rule"></a>

 To test your rule, submit a job that exits shortly after it starts with a non\-zero exit code\. If your event rule is configured correctly, you should receive an email message within a few minutes with the event text\. 

**To test a rule**

1. Open the AWS Batch console at [https://console\.aws\.amazon\.com/batch/](https://console.aws.amazon.com/batch/)\.

1. Submit a new AWS Batch job\. For more information, see [Submitting a Job](submit_job.md)\. For the job's command, substitute this command to exit the container with an exit code of 1\.

   ```
   /bin/sh, -c, 'exit 1'
   ```

1. Check your email to confirm that you received an email alert for the failed job notification\.