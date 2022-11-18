# AWS Batch Event Stream for Amazon EventBridge<a name="cloudwatch_event_stream"></a>

You can use the AWS Batch event stream for Amazon EventBridge to receive near real\-time notifications regarding the current state of jobs in your job queues\.

You can use EventBridge to gain further insights about your AWS Batch service\. More specifically, you can use it to check the progress of jobs, build AWS Batch custom workflows, generate usage reports or metrics, or build your own dashboards\. With AWS Batch and EventBridge, you don't need scheduling and monitoring code that continuously polls AWS Batch for job status changes\. Instead, you can handle AWS Batch job state changes asynchronously using a variety of Amazon EventBridge targets\. These include AWS Lambda, Amazon Simple Queue Service, Amazon Simple Notification Service, or Amazon Kinesis Data Streams\.

Events from the AWS Batch event stream are ensured to be delivered at least one time\. In the event that duplicate events are sent, the event provides enough information to identify duplicates\. That way, you can compare the time stamp of the event and the job status\.

AWS Batch jobs are available as EventBridge targets\. Using simple rules, you can match events and submit AWS Batch jobs in response to them\. For more information, see [What is EventBridge?](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-what-is.html) in the *Amazon EventBridge User Guide*\. You can also use EventBridge to schedule automated actions that self\-trigger at certain times using cron or rate expressions\. For more information, see [Creating an Amazon EventBridge rule that runs on a schedule](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-create-rule-schedule.html) in the *Amazon EventBridge User Guide*\. For an example walkthrough, see [AWS Batch jobs as EventBridge targets](batch-cwe-target.md)\.

**Topics**
+ [AWS Batch Events](batch_cwe_events.md)
+ [AWS Batch jobs as EventBridge targets](batch-cwe-target.md)
+ [Tutorial: Listening for AWS Batch​ EventBridge](batch_cwet.md)
+ [Tutorial: Sending Amazon Simple Notification Service Alerts for Failed Job Events](batch_sns_tutorial.md)