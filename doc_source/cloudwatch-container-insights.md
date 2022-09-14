# AWS Batch CloudWatch Container Insights<a name="cloudwatch-container-insights"></a>

CloudWatch Container Insights collects, aggregates, and summarizes metrics and logs from your AWS Batch compute environments and jobs\. The metrics include CPU, memory, disk, and network utilization\. You can add these metrics to CloudWatch dashboards\. 

Operational data is collected as performance log events\. These are entries that use a structured JSON schema that enables high\-cardinality data to be ingested and stored at scale\. From this data, CloudWatch creates higher\-level aggregated metrics at the compute environment and job level as CloudWatch metrics\. For more information, see [Container Insights Structured Logs for Amazon ECS](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Container-Insights-reference-structured-logs-ECS.html) in the *Amazon CloudWatch User Guide*\.

**Important**  
CloudWatch Container Insights are charged as custom metrics by CloudWatch\. For more information, see [Amazon CloudWatch Events pricing](http://aws.amazon.com/cloudwatch/pricing/)

## Turn on Container Insights<a name="cloudwatch-container-insights-working"></a>

You can turn on Container Insights for AWS Batch compute environments\.

1. Open the [AWS Batch console](https://console.aws.amazon.com/batch/home)\.

1. Choose **Compute Environments**\.

1. Choose the compute environment that you want\.

1. For **Container Insights**, turn on Container Insights for the compute  environment\.
**Tip**  
You can select a default interval to aggregate the metrics or create a custom  interval\.

By default, the following metrics are displayed\. For a full list of Amazon ECS Container Insights metrics, see [Amazon ECS Container Insights Metrics](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Container-Insights-metrics-ECS.html) in the *Amazon CloudWatch User Guide*\.
+ **`JobCount`** – The number of jobs that run in the compute environment\.
+ **`ContainerInstanceCount`** – The number of Amazon Elastic Compute Cloud instances that run the Amazon ECS agent and are registered in the compute environment\.
+ **`MemoryReserved`** – The memory that's reserved by compute environment jobs\. This metric is collected only for the jobs that have a defined memory reservation in their job definition\.
+ **`MemoryUtilized`** – The memory that's being used by compute environment jobs\. This metric is collected only for jobs that have a defined memory reservation in their job definition\.
+ **`CpuReserved`** – The CPU units that are reserved by compute environment jobs\. This metric is collected only for jobs that have a defined CPU reservation in their job definition\.
+ **`CpuUtilized`** – The CPU units used by jobs in the compute environment\. This metric is collected only for jobs that have a defined CPU reservation in their job definition\.
+ **`NetworkRxBytes`** \- The number of bytes that are received\. This metric is available only for containers in jobs that use the `awsvpc` or bridge network modes\.
+ **`NetworkTxBytes`** – The number of bytes that are transmitted\. This metric is available only for containers in jobs that use the `awsvpc` or bridge network modes\.
+ **`StorageReadBytes`** – The number of bytes that are read from storage\.
+ **`StorageWriteBytes`** – The number of bytes that are written to storage\.