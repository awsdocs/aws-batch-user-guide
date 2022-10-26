# Job logs<a name="review-job-logs"></a>

You can configure your AWS Batch jobs to send log information to CloudWatch Logs\. This way, you can view different logs from your jobs in one convenient location\. For more information, see [Using CloudWatch Logs with AWS Batch](using_cloudwatch_logs.md)\.

You can also use **Job logs** in the AWS Batch console to monitor or troubleshoot an AWS Batch job\.

1. Open the [AWS Batch console](https://console.aws.amazon.com/batch/home)\.

1. Choose **Jobs**\.

1. For **Job queue**, choose the job queue that you want\.
**Tip**  
If there are several jobs in the job queue, you can turn on **Searching and filtering** to find a job faster\. For more information, see [Search and filter AWS Batch jobs](search-filter-jobs.md)\.

1. For **Status**, choose the job status that you want\.

1. Choose the job that you want\.

1. On the **Details** page, scroll down to **Job Logs**\.

1. Choose **Retrieve logs**\.

1. For **Authorization required**, enter **OK**, and then choose **Authorize** to accept Amazon CloudWatch charges\.
**Note**  
To revoke your authorization for CloudWatch charges:  
In the left navigation pane, choose **Permissions**\.
For **Job logs**, choose **Edit**\.
Clear the **Authorize Batch to use CloudWatch** check box\.
Choose **Save changes**\.

1. Review the log data for the AWS Batch job\.
**Tip**  
You can filter the log based on **Keywords**, **Max results**, and **Sorting**\. You can also choose one of the default time intervals or create a custom interval to customize the results\.