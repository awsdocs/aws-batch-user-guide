# Tutorial: Listening for AWS Batch​ EventBridge<a name="batch_cwet"></a>

In this tutorial, you set up a simple AWS Lambda function that listens for AWS Batch job events and writes them out to a CloudWatch Logs log stream\.

## Prerequisites<a name="cwet_prereqs"></a>

This tutorial assumes that you have a working compute environment and job queue that are ready to accept jobs\. If you don't have a running compute environment and job queue to capture events from, follow the steps in [Getting Started with AWS Batch](Batch_GetStarted.md) to create one\. At the end of this tutorial, you can optionally submit a job to this job queue to test that you have configured your Lambda function correctly\. 

## Step 1: Create the Lambda Function<a name="cwet_create_lam"></a>

 In this procedure, you create a simple Lambda function to serve as a target for AWS Batch event stream messages\. 

**To create a target Lambda function**

1. Open the AWS Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/)\.

1. Choose **Create function**, **Author from scratch**\. 

1. For **Function name**, enter **batch\-event\-stream\-handler**\.

1. For **Runtime**, choose **Python 3\.8**\.

1. Choose **Create function**\.

1. In the **Code source** section, edit the sample code to match the following example:

   ```
   import json
   
   
   def lambda_handler(event, _context):
       # _context is not used
       del _context
       if event["source"] != "aws.batch":
           raise ValueError("Function only supports input from events with a source type of: aws.batch")
   
       print(json.dumps(event))
   ```

   This is a simple Python 3\.8 function that prints the events sent by AWS Batch\. If everything is configured correctly, at the end of this tutorial, the event details appear in the CloudWatch Logs log stream that's associated with this Lambda function\.

1. Choose **Deploy**\.

## Step 2: Register Event Rule<a name="cwet_register_event_rule"></a>

In this section, you create an EventBridge event rule that captures job events that are coming from your AWS Batch resources\. This rule captures all events coming from AWS Batch within the account where it's defined\. The job messages themselves contain information about the event source, including the job queue where it was submitted\. You can use this information to filter and sort events programmatically\.

**Note**  
If you use the AWS Management Console to create an event rule, the console automatically adds the IAM permissions for EventBridge to call your Lambda function\. However, if you're creating an event rule using the AWS CLI, you must grant permissions explicitly\. For more information, see [Events and Event Patterns](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-events.html) in the *Amazon EventBridge User Guide*\.

**To create your EventBridge rule**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**\.

1. Choose **Create rule**\.

1. Enter a name and description for the rule\.

   A rule can't have the same name as another rule in the same Region and on the same event bus\.

1. For **Event bus**, choose the event bus that you want to associate with this rule\. If you want this rule to match events that come from your account, select **AWS default event bus**\. When an AWS service in your account emits an event, it always goes to your account's default event bus\.

1. For **Rule type**, choose **Rule with an event pattern**\.

1. Choose **Next**\.

1. For **Event source**, choose **Other**\.

1. For **Event pattern**, select **Custom patterns \(JSON editor\)**\.

1. Paste the following event pattern into the text area\.

   ```
   {
     "source": [
       "aws.batch"
     ]
   }
   ```

   This rule applies across all of your AWS Batch groups and to every AWS Batch event\. Alternatively, you can create a more specific rule to filter out some results\.

1. Choose **Next**\.

1. For **Target types**, choose **AWS service**\.

1. For **Select a target**, choose **Lambda function**, and select your Lambda function\.

1. \(Optional\) For **Additional settings**, do the following:

   1. For **Maximum age of event**, enter a value between one minute \(00:01\) and 24 hours \(24:00\)\.

   1. For **Retry attempts**, enter a number between 0 and 185\.

   1. For **Dead\-letter queue**, choose whether to use a standard Amazon SQS queue as a dead\-letter queue\. EventBridge sends events that match this rule to the dead\-letter queue if they are not successfully delivered to the target\. Do one of the following:
      + Choose **None** to not use a dead\-letter queue\.
      + Choose **Select an Amazon SQS queue in the current AWS account to use as the dead\-letter queue** and then select the queue to use from the dropdown\.
      + Choose **Select an Amazon SQS queue in an other AWS account as a dead\-letter queue** and then enter the ARN of the queue to use\. You must attach a resource\-based policy to the queue that grants EventBridge permission to send messages to it\. For more information, see [Granting permissions to the dead\-letter queue](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-rule-dlq.html#eb-dlq-perms) in the *Amazon EventBridge User Guide*\.

1. Choose **Next**\.

1. \(Optional\) Enter one or more tags for the rule\. For more information, see [Amazon EventBridge tags](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-tagging.html) in the *Amazon EventBridge User Guide*\.

1. Choose **Next**\.

1. Review the details of the rule and choose **Create rule**\.

## Step 3: Test Your Configuration<a name="cwet_test"></a>

You can now test your EventBridge configuration by submitting a job to your job queue\. If everything is configured properly, your Lambda function is triggered and it writes the event data to a CloudWatch Logs log stream for the function\.

**To test your configuration**

1. Open the AWS Batch console at [https://console\.aws\.amazon\.com/batch/](https://console.aws.amazon.com/batch/)\.

1. Submit a new AWS Batch job\. For more information, see [Submitting a job](submit_job.md)\.

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. On the navigation pane, choose **Logs** and select the log group for your Lambda function \(for example, **/aws/lambda/***my\-function*\)\.

1. Select a log stream to view the event data\. 