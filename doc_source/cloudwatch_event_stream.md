# AWS Batch Event Stream for CloudWatch Events<a name="cloudwatch_event_stream"></a>

You can use the AWS Batch event stream for CloudWatch Events to receive near real\-time notifications regarding the current state of jobs that have been submitted to your job queues\.

Using CloudWatch Events, you can monitor the progress of jobs, build AWS Batch custom workflows with complex dependencies, generate usage reports and/or metrics around job execution, or build your own custom dashboards\. With AWS Batch CloudWatch events, you can eliminate scheduling and monitoring code that continuously polls the AWS Batch service for job status changes, and instead handle AWS Batch job state changes asynchronously using any CloudWatch Events target, such as AWS Lambda, Amazon Simple Queue Service, Amazon Simple Notification Service, and Amazon Kinesis Data Streams\.

Events from AWS Batch event stream are ensured to be delivered at least one time\. In the event that duplicate events are sent, the event provides enough information to identify duplicates \(you can compare the time stamp of the event and the job status\)\.


+ [AWS Batch Events](batch_cwe_events.md)
+ [Tutorial: Listening for AWS Batch CloudWatch Events](batch_cwet.md)
+ [Tutorial: Sending Amazon Simple Notification Service Alerts for Failed Job Events](batch_sns_tutorial.md)