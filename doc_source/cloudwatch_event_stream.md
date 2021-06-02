# AWS Batch Event Stream for CloudWatch Events<a name="cloudwatch_event_stream"></a>

You can use the AWS Batch event stream for CloudWatch Events to receive near real\-time notifications regarding the current state of jobs in your job queues\.

You can use CloudWatch Events to gain further insights about your AWS Batch service\. More specifcally, you can use it to check the progress of jobs, build AWS Batch custom workflows, generate usage reports or metrics, or build your own dashboards\. With AWS Batch and CloudWatch Events, you don't need scheduling and monitoring code that continuously polls AWS Batch for job status changes\. Instead, you can handle AWS Batch job state changes asynchronously using a variety of CloudWatch Events targets\. These include AWS Lambda, Amazon Simple Queue Service, Amazon Simple Notification Service, or Amazon Kinesis Data Streams\.

Events from the AWS Batch event stream are ensured to be delivered at least one time\. In the event that duplicate events are sent, the event provides enough information to identify duplicates\. That way, you can compare the time stamp of the event and the job status\.

AWS Batch jobs are available as CloudWatch Events targets\. Using simple rules, you can match events and submit AWS Batch jobs in response to them\. For more information, see [What is Amazon CloudWatch Events?](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/WhatIsCloudWatchEvents.html) in the *Amazon CloudWatch Events User Guide*\. You can also use CloudWatch Events to schedule automated actions that self\-trigger at certain times using cron or rate expressions\. For more information, see [Schedule Expressions for Rules](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/ScheduledEvents.html) in the *Amazon CloudWatch Events User Guide*\. For an example walkthrough, see [AWS Batch Jobs as CloudWatch Events Targets](batch-cwe-target.md)\.

**Topics**
+ [AWS Batch Events](batch_cwe_events.md)
+ [AWS Batch Jobs as CloudWatch Events Targets](batch-cwe-target.md)
+ [Tutorial: Listening for AWS Batch CloudWatch Events](batch_cwet.md)
+ [Tutorial: Sending Amazon Simple Notification Service Alerts for Failed Job Events](batch_sns_tutorial.md)
