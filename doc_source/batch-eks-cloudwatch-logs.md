# Use CloudWatch Logs to monitor AWS Batch on Amazon EKS jobs<a name="batch-eks-cloudwatch-logs"></a>

You can use Amazon CloudWatch Logs to monitor, store, and view all your log files in one location\. Using CloudWatch Logs, you can search, filter, and analyze log data from multiple sources\.

You can download an AWS for Fluent Bit image that includes a plugin to monitor AWS Batch on Amazon EKS jobs in CloudWatch Logs\. Fluent Bit is an open\-source log processor and forwarder that's both Docker and Kubernetes compatible\. We recommend that you use Fluent Bit as your log router because it's less resource intensive than Fluentd\. For more information, see [Using the AWS for Fluent Bit image](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/firelens-using-fluentbit.html)\. 

## Prerequisites<a name="batch-eks-cloudwatch-logs-prereqs"></a>

Attach the `CloudWatchAgentServerPolicy` policy to the AWS Identity and Access Management policy of your worker nodes\. For more information, see [Verify prerequisites](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Container-Insights-prerequisites.html)\.

## Install AWS for Fluent Bit<a name="batch-eks-cloudwatch-logs-install-fluent-bit"></a>

For information about how to install AWS for Fluent Bit and create the CloudWatch groups, see [Setting up Fluent Bit](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Container-Insights-setup-logs-FluentBit.html#Container-Insights-FluentBit-setup) or [Quick Start with the CloudWatch agent and Fluent Bit](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Container-Insights-setup-EKS-quickstart.html)\.

**Tip**  
Remember that Fluent Bit uses \.5 CPU and 100 MB of memory on AWS Batch nodes\. This reduces the total available capacity for AWS Batch jobs\. Consider this when you size your jobs\.

## Turn on Fluent Bit for AWS Batch nodes<a name="batch-eks-cloudwatch-logs-fluent-bit-batch-only"></a>

To ensure the Fluent Bit logging DaemonSet runs on AWS Batch managed nodes, modify the Fluent Bit DaemonSet tolerations:

```
tolerations:
- key: "batch.amazonaws.com/batch-node"
  operator: "Exists"
```