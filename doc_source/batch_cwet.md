# Tutorial: Listening for AWS Batch CloudWatch Events<a name="batch_cwet"></a>

In this tutorial, you set up a simple AWS Lambda function that listens for AWS Batch job events and writes them out to a CloudWatch Logs log stream\.

## Prerequisites<a name="cwet_prereqs"></a>

This tutorial assumes that you have a working compute environment and job queue that are ready to accept jobs\. If you do not have a running compute environment and job queue to capture events from, follow the steps in [Getting Started with AWS Batch](Batch_GetStarted.md) to create one\. At the end of this tutorial, you can submit a job to this job queue to test that you have configured your Lambda function correctly\. 

## Step 1: Create the Lambda Function<a name="cwet_create_lam"></a>

 In this procedure, you create a simple Lambda function to serve as a target for AWS Batch event stream messages\. 

**To create a target Lambda function**

1. Open the AWS Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/)\.

1. Choose **Create a Lambda function**, **Author from scratch**\. 

1. For **Name**, enter **batch\-event\-stream\-handler**\.

1. For **Role**, choose **Create a custom role**, **Allow**\.

1. Choose **Create function**\.

1. In the **Function code** section, choose **Python 2\.7** for the runtime and edit the sample code to match the following example:

   ```
   import json
   
   def lambda_handler(event, context):
       if event["source"] != "aws.batch":
          raise ValueError("Function only supports input from events with a source type of: aws.batch")
          
       print(json.dumps(event))
   ```

   This is a simple Python 2\.7 function that prints the events sent by AWS Batch\. If everything is configured correctly, at the end of this tutorial, you see that the event details appear in the CloudWatch Logs log stream associated with this Lambda function\.

1. Choose **Save**\.

## Step 2: Register Event Rule<a name="cwet_register_event_rule"></a>

 Next, you create a CloudWatch Events event rule that captures job events coming from your AWS Batch resources\. This rule captures all events coming from AWS Batch within the account where it is defined\. The job messages themselves contain information about the event source, including the job queue to which it was submitted, that you can use to filter and sort events programmatically\. 

**Note**  
When you use the AWS Management Console to create an event rule, the console automatically adds the IAM permissions necessary to grant CloudWatch Events permissions to call your Lambda function\. If you are creating an event rule using the AWS CLI, you must grant permissions explicitly\. For more information, see [Events and Event Patterns](https://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/CloudWatchEventsandEventPatterns.html) in the *Amazon CloudWatch User Guide*\.

**To create your CloudWatch Events rule**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. On the navigation pane, choose **Events**, **Create rule**\.

1. For **Event source**, select **Event Pattern** as the event source, and then select **Build custom event pattern**\. 

1. Paste the following event pattern into the text area\.

   ```
   {
     "source": [
       "aws.batch"
     ]
   }
   ```

   This rule applies to all AWS Batch events for all of your AWS Batch groups\. Alternatively, you can create a more specific rule to filter out some results\.

1. For **Targets**, choose **Add target**\. For **Target type**, choose **Lambda function**, and select your Lambda function\.

1. Choose **Configure details**\.

1. For **Rule definition**, type a name and description for your rule and choose **Create rule**\.

## Step 3: Test Your Configuration<a name="cwet_test"></a>

 Finally, you can test your CloudWatch Events configuration by submitting a job to your job queue\. If everything is configured properly, your Lambda function is triggered and it writes the event data to a CloudWatch Logs log stream for the function\.

**To test your configuration**

1. Open the AWS Batch console at [https://console\.aws\.amazon\.com/batch/](https://console.aws.amazon.com/batch/)\.

1. Submit a new AWS Batch job\. For more information, see [Submitting a Job](submit_job.md)\.

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. On the navigation pane, choose **Logs** and select the log group for your Lambda function \(for example, **/aws/lambda/***my\-function*\)\.

1. Select a log stream to view the event data\. 