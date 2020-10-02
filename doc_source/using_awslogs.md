# Using the awslogs Log Driver<a name="using_awslogs"></a>

AWS Batch automatically enables the `awslogs` log driver to send log information to CloudWatch Logs\. This enables you to view different logs from your containers in one convenient location, and it prevents your container logs from taking up disk space on your container instances\. This topic helps you configure the `awslogs` log driver in your job definitions\.

**Note**  
The type of information that is logged by the containers in your job depends mostly on their `ENTRYPOINT` command\. By default, the logs that are captured show the command output that you would normally see in an interactive terminal if you ran the container locally, which are the `STDOUT` and `STDERR` I/O streams\. The `awslogs` log driver simply passes these logs from Docker to CloudWatch Logs\. For more information on how Docker logs are processed, including alternative ways to capture different file data or streams, see [View logs for a container or service](https://docs.docker.com/config/containers/logging/) in the Docker documentation\.

To send system logs from your container instances to CloudWatch Logs, see [Using CloudWatch Logs with AWS Batch](using_cloudwatch_logs.md)\. For more information about CloudWatch Logs, see [Monitoring Log Files](https://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/WhatIsCloudWatchLogs.html) and [CloudWatch Logs quotas](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/cloudwatch_limits_cwl.html) in the *Amazon CloudWatch Logs User Guide*\.

## Available awslogs Log Driver Options<a name="create_awslogs_logdriver_options"></a>

The `awslogs` log driver supports the following options in AWS Batch job definitions\. For more information, see [CloudWatch Logs logging driver](https://docs.docker.com/config/containers/logging/awslogs/) in the Docker documentation\.

`awslogs-region`  
Required: No  
Specify the Region to which the `awslogs` log driver should send your Docker logs\. By default the Region used is that of the job\. You can choose to send all of your logs from jobs in different Regions to a single Region in CloudWatch Logs so that they are all visible in one location, or you can separate them by Region for more granularity\. Be sure that the specified log group exists in the Region that you specify with this option\.

`awslogs-group`  
Required: Optional  
The `awslogs-group` option allows you to specify the log group to which the `awslogs` log driver sends its log streams\. If not specified, `aws/batch/job` is used\.

`awslogs-stream-prefix`  
Required: Optional  
The `awslogs-stream-prefix` option allows you to associate a log stream with the specified prefix, and the Amazon ECS task ID of the AWS Batch job to which the container belongs\. If you specify a prefix with this option, then the log stream takes the following format:  

```
prefix-name/default/ecs-task-id
```

`awslogs-datetime-format`  
Required: No  
This option defines a multiline start pattern in Python `strftime` format\. A log message consists of a line that matches the pattern and any following lines that don’t match the pattern\. Thus the matched line is the delimiter between log messages\.  
One example of a use case for using this format is for parsing output such as a stack dump, which might otherwise be logged in multiple entries\. The correct pattern allows it to be captured in a single entry\.  
For more information, see [awslogs\-datetime\-format](https://docs.docker.com/config/containers/logging/awslogs/#awslogs-datetime-format)\.  
This option always takes precedence if both `awslogs-datetime-format` and `awslogs-multiline-pattern` are configured\.  
Multiline logging performs regular expression parsing and matching of all log messages, which may have a negative impact on logging performance\.

`awslogs-multiline-pattern`  
Required: No  
This option defines a multiline start pattern using a regular expression\. A log message consists of a line that matches the pattern and any following lines that don’t match the pattern\. Thus the matched line is the delimiter between log messages\.  
For more information, see [awslogs\-multiline\-pattern](https://docs.docker.com/config/containers/logging/awslogs/#awslogs-multiline-pattern) in the Docker documentation\.  
This option is ignored if `awslogs-datetime-format` is also configured\.  
Multiline logging performs regular expression parsing and matching of all log messages\. This may have a negative impact on logging performance\.

`awslogs-create-group`  
Required: No  
Specify whether you want the log group automatically created\. If this option is not specified, it defaults to `false`\.  
This option is not recommended\. We recommend that you create the log group in advance using the CloudWatch Logs [CreateLogGroup](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_CreateLogGroup.html) API action as each job will try to create the log group, increasing the chance that the job will fail\.
The IAM policy for your execution role must include the `logs:CreateLogGroup` permission before you attempt to use `awslogs-create-group`\.

## Specifying a Log Configuration in your Job Definition<a name="specify-log-config"></a>

AWS Batch enables the `awslogs` log driver by default\. This section describes how to customize the `awslogs` log configuration for a job\. For more information, see [Creating a Job Definition](create-job-definition.md)\.

The log configuration JSON snippets shown below have a `logConfiguration` object specified for each job; one for a WordPress job that sends logs to a log group called `awslogs-wordpress`, and one for a MySQL container that sends logs to a log group called `awslogs-mysql`\. Both containers use the `awslogs-example` log stream prefix\.

```
"logConfiguration": {
    "logDriver": "awslogs",
    "options": {
        "awslogs-group": "awslogs-wordpress",
        "awslogs-stream-prefix": "awslogs-example"
    }
}
```

```
"logConfiguration": {
    "logDriver": "awslogs",
    "options": {
        "awslogs-group": "awslogs-mysql",
        "awslogs-stream-prefix": "awslogs-example"
    }
}
```

In the AWS Batch console, the log configuration for the `wordpress` job definition is specified as shown in the image below\. 

![\[Console log configuration\]](http://docs.aws.amazon.com/batch/latest/userguide/images/awslogs-console-config.png)

After you have registered a task definition with the `awslogs` log driver in a job definition log configuration, you can submit a job with that job definition to start sending logs to CloudWatch Logs\. For more information, see [Submitting a Job](submit_job.md)\.