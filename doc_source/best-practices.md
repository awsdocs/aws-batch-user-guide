# Best Practices for AWS Batch<a name="best-practices"></a>

You can use AWS Batch to run a variety of demanding computational workloads at scale without managing a complex architecture\. AWS Batch jobs can be used in a wide range of use cases in areas such as epidemiology, gaming, and machine learning\.

This topic covers the best practices to consider while using AWS Batch and guidance on how to run and optimize your workloads when using AWS Batch\.

**Topics**
+ [When to use AWS Batch](#bestpractice1)
+ [Checklist to run at scale](#bestpractice2)
+ [Optimize containers and AMIs](#bestpractice3)
+ [Choose the right compute environment resource](#bestpractice4)
+ [Amazon EC2 On\-Demand or Amazon EC2 Spot](#bestpractice5)
+ [Use Amazon EC2 Spot best practices for AWS Batch](#bestpractice6)
+ [Common errors and troubleshooting](#bestpractice7)

## When to use AWS Batch<a name="bestpractice1"></a>

AWS Batch runs jobs at scale and at low cost, and provides queuing services and cost\-optimized scaling\. However, not every workload is suitable to be run using AWS Batch\.
+ **Short jobs** – If a job runs for only a few seconds, the overhead to schedule the batch job might take longer than the runtime of the job itself\. As a workaround, binpack your tasks together before you submit them in AWS Batch\. Then, configure your AWS Batch jobs to iterate over the tasks\. For example, stage the individual task arguments into an Amazon DynamoDB table or as a file in an Amazon S3 bucket\. Consider grouping tasks so the jobs run 3\-5 minutes each\. After you binpack the jobs, loop through your task groups within your AWS Batch job\.
+ **Jobs that must be run immediately** – AWS Batch can process jobs quickly\. However, AWS Batch is a scheduler and optimizes for cost performance, job priority, and throughput\. AWS Batch might require time to process your requests\. If you need a response in under a few seconds, then a service\-based approach using Amazon ECS or Amazon EKS is more suitable\.

## Checklist to run at scale<a name="bestpractice2"></a>

Before you run a large workload on 50 thousand or more vCPUs, consider the following checklist\.

**Note**  
If you plan to run a large workload on a million or more vCPUs or need guidance running at large scale, contact your AWS team\.
+ **Check your Amazon EC2 quotas** – Check your Amazon EC2 quotas \(also known as limits\) in the Service Quotas panel of the AWS Management Console\. If necessary, request a quota increase for your peak number of Amazon EC2 instances\. Remember that Amazon EC2 Spot and Amazon On\-Demand instances have separate quotas\. For more information, see [Getting started with Service Quotas](https://docs.aws.amazon.com/servicequotas/latest/userguide/getting-started.html)\.
+ **Verify your Amazon Elastic Block Store quota for each Region** – Each instance uses a GP2 or GP3 volume for the operating system\. By default, the quota for each AWS Region is 300 TiB\. However, each instance uses counts as part of this quota\. So, make sure to factor this in when you verify your Amazon Elastic Block Store quota for each Region\. If your quota is reached, you can’t create more instances\. For more information, see [Amazon Elastic Block Store endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/ebs-service.html)
+ **Use Amazon S3 for storage** – Amazon S3 provides high throughput and helps to eliminate the guesswork on how much storage to provision based on the number of jobs and instances in each Availability Zone\. For more information, see [Best practices design patterns: optimizing Amazon S3 performance](https://docs.aws.amazon.com/AmazonS3/latest/userguide/optimizing-performance.html)\.
+ **Scale gradually to identify bottlenecks early** – For a job that runs on a million or more vCPUs, start lower and gradually increase so that you can identify bottlenecks early\. For example, start by running on 50 thousand vCPUs\. Then, increase the count to 200 thousand vCPUs, and then 500 thousand vCPUs, and so on\. In other words, continue to gradually increase the vCPU count until you reach the desired number of vCPUs\.
+ **Monitor to identify potential issues early** – To avoid potential breaks and issues when running at scale, make sure to monitor both your application and architecture\. Breaks might occur even when scaling from 1 thousand to 5 thousand vCPUs\. You can use Amazon CloudWatch Logs to review log data or use CloudWatch Embedded Metrics using a client library\. For more information, see [CloudWatch Logs agent reference](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/AgentReference.html) and [https://github.com/awslabs/aws-embedded-metrics-python](https://github.com/awslabs/aws-embedded-metrics-python)

## Optimize containers and AMIs<a name="bestpractice3"></a>

Container size and structure are important for the first set of jobs that you run\. This is especially true if the container is larger than 4 GB\. Container images are built in layers\. The layers are retrieved in parallel by Docker using three concurrent threads\. You can increase the number of concurrent threads using the `max-concurrent-downloads` parameter\. For more information, see the [Dockerd documentation](https://docs.docker.com/engine/reference/commandline/dockerd/)\.

Although you can use larger containers, we recommend that you optimize container structure and size for faster startup times\.
+ **Smaller containers are fetched faster** – Smaller containers can lead to faster application start times\. To decrease container size, offload libraries or files that are updated infrequently to the Amazon Machine Image \(AMI\)\. You can also use bind mounts to give access to your containers\. For more information, see [Bind mounts](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/bind-mounts.html)\.
+ **Create layers that are even in size and break up large layers** – Each layer is retrieved by one thread\. So, a large layer might significantly impact your job startup time\. We recommend a maximum layer size of 2 GB as a good tradeoff between larger container size and faster startup times\. You can run the `docker history your_image_id` command to check your container image structure and layer size\. For more information, see the [Docker documentation](https://docs.docker.com/engine/reference/commandline/history/)\.
+ **Use Amazon Elastic Container Registry as your container repository** – When you run thousands of jobs in parallel, a self\-managed repository can fail or throttle throughput\. Amazon ECR works at scale and can handle workloads with up to over a million vCPUs\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/batch/latest/userguide/images/batch-best-practices-f1.png)

## Choose the right compute environment resource<a name="bestpractice4"></a>

AWS Fargate requires less initial setup and configuration than Amazon EC2 and is likely easier to use, particularly if it's your first\-time\. With Fargate, you don't need to manage servers, handle capacity planning, or isolate container workloads for security\.

If you have the following requirements, we recommend you use Fargate instances:
+ Your jobs must start quickly, specifically less than 30 seconds\.
+ The requirements of your jobs are 4 vCPUs or less, no GPUs, and 30 GiB of memory or less\.

For more information, see [When to use Fargate](fargate.md#when-to-use-fargate)\.

If you have the following requirements, we recommend that you use Amazon EC2 instances:
+ You require increased control over the instance selection or require using specific instance types\.
+ Your jobs require resources that AWS Fargate can’t provide, such as GPUs, more memory, several vCPUs, or the Amazon Elastic Fabric Adapter\.
+ You require a high level of throughput or concurrency\.
+ You need to customize your AMI, Amazon EC2 Launch Template, or access to special Linux parameters\.

With Amazon EC2, you can more finely tune your workload to your specific requirements and run at scale if needed\.

## Amazon EC2 On\-Demand or Amazon EC2 Spot<a name="bestpractice5"></a>

Most AWS Batch customers use Amazon EC2 Spot instances because of the savings over On\-Demand instances\. However, if your workload runs for multiple hours and can't be interrupted, On\-Demand instances might be more suitable for you\. You can always try Spot instances first and switch to On\-Demand if necessary\.

If you have the following requirements and expectations, use Amazon EC2 On\-Demand instances:
+ The runtime of your jobs is more than an hour, and you can't tolerate interruptions to your workload\.
+ You have a strict SLO \(service\-level objective\) for your overall workload and can’t increase computational time\.
+ The instances that you require are more likely to see interruptions\. 

If you have the following requirements and expectations, use Amazon EC2 Spot instances:
+ The runtime for your jobs is typically 30 minutes or less\.
+ You can tolerate potential interruptions and job rescheduling as a part of your workload\. For more information, see [Spot Instance advisor](http://aws.amazon.com/ec2/spot/instance-advisor/)\. 
+ Long running jobs can be restarted from a checkpoint if interrupted\.

You can mix both purchasing models by submitting on Spot instance first and then use On\-Demand instance as a fallback option\. For example, submit your jobs on a queue that's connected to compute environments that are running on Amazon EC2 Spot instances\. If a job gets interrupted, catch the event from Amazon EventBridge and correlate it to a Spot instance reclamation\. Then, resubmit the job to an On\-Demand queue using an AWS Lambda function or AWS Step Functions\. For more information, see [Tutorial: Sending Amazon Simple Notification Service Alerts for Failed Job Events](batch_sns_tutorial.md), [Best practices for handling Amazon EC2 Spot Instance interruptions](http://aws.amazon.com/blogs/compute/best-practices-for-handling-ec2-spot-instance-interruptions/) and [ Manage AWS Batch with Step Functions](https://docs.aws.amazon.com/step-functions/latest/dg/connect-batch.html)\.

**Important**  
Use different instance types, sizes, and Availability Zones for your On\-Demand compute environment to maintain Amazon EC2 Spot instance pool availability and decrease the interruption rate\.

## Use Amazon EC2 Spot best practices for AWS Batch<a name="bestpractice6"></a>

When you choose Amazon Elastic Compute Cloud \(EC2\) Spot instances, you likely can optimize your workflow to save costs, sometimes significantly\. For more information, see [Best practices for Amazon EC2 Spot](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/spot-best-practices.html#be-instance-type-flexible)\.

To optimize your workflow to save costs, consider the following Amazon EC2 Spot best practices for AWS Batch:
+ **Choose the `SPOT_CAPACITY_OPTIMIZED` allocation strategy** – AWS Batch chooses Amazon EC2 instances from the deepest Amazon EC2 Spot capacity pools\. If you’re concerned about interruptions, this is a suitable choice\. For more information, see [Allocation strategies](allocation-strategies.md)\.
+ **Diversify instance types** – To diversify your instance types, consider compatible sizes and families, then let AWS Batch choose based on price or availability\. For example, consider `c5.24xlarge` as an alternative to `c5.12xlarge` or `c5a`, `c5n`, `c5d`, `m5`, and `m5d` families\. For more information, see [Be flexible about instance types and Availability Zones](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/spot-best-practices.html#be-instance-type-flexible)\.
+ **Reduce job runtime or checkpoint** – We advise against running jobs that take an hour or more when using Amazon EC2 Spot instances to avoid interruptions\. If you divide or checkpoint your jobs into smaller parts that consist of 30 minutes or less, you can significantly reduce the possibility of interruptions\.
+ **Use automated retries** – To avoid disruptions to AWS Batch jobs, set automated retries for jobs\. Batch jobs can be disrupted for any of the following reasons: a non\-zero exit code is returned, a service error occurs, or an instance reclamation occurs\. You can set up to 10 automated retries\. For a start, we recommend that you set at least 1\-3 automated retries\. For information about tracking Amazon EC2 Spot interruptions, see [Spot Interruption Dashboard](https://github.com/aws-samples/ec2-spot-interruption-dashboard)\.

  For AWS Batch, if you set the retry parameter, the job is placed at the front of the job queue\. That is, the job is given priority\. When you create the job definition or you submit the job in the AWS CLI, you can configure a retry strategy\. For more information, see [submit\-job](https://docs.aws.amazon.com/goto/aws-cli/batch-2016-08-10/SubmitJob       )\.

  ```
  $ aws batch submit-job --job-name MyJob \
      --job-queue MyJQ \
      --job-definition MyJD \
      --retry-strategy attempts=2
  ```
+ **Use custom retries** – You can configure a job retry strategy to a specific application exit code or instance reclamation\. In the following example, if the host causes the failure, the job can be retried up to five times\. However, if the job fails for a different reason, the job exits and the status is set to `FAILED`\.

  ```
  "retryStrategy": {
      "attempts": 5,
      "evaluateOnExit":
      [{
          "onStatusReason" :"Host EC2*",
          "action": "RETRY"
      },{
        "onReason" : "*"
          "action": "EXIT"
      }]
  }
  ```
+ **Use the Spot Interruption Dashboard** – You can use the Spot Interruption Dashboard to track Spot interruptions\. The application provides metrics on Amazon EC2 Spot instances that are reclaimed and which Availability Zones that Spot instances are in\. For more information, see [Spot Interruption Dashboard](https://github.com/aws-samples/ec2-spot-interruption-dashboard) 

## Common errors and troubleshooting<a name="bestpractice7"></a>

Errors in AWS Batch often occur at the application level or are caused by instance configurations that don’t meet your specific job requirements\. Other issues include jobs getting stuck in the `RUNNABLE` status or compute environments getting stuck in an `INVALID` state\. For more information about troubleshooting jobs getting stuck in `RUNNABLE` status, see [Jobs stuck in a `RUNNABLE` status](troubleshooting.md#job_stuck_in_runnable)\. For information about troubleshooting compute environments in an `INVALID` state, see [`INVALID` compute environment](troubleshooting.md#invalid_compute_environment)\.
+ **Check Amazon EC2 Spot vCPU quotas** – Verify that your current service quotas meet the job requirements\. For example, suppose that your current service quota is 256 vCPUs and the job requires 10,000 vCPUs\. Then, the service quota doesn't meet the job requirement\. For more information and troubleshooting instructions, see [Amazon EC2 service quotas](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-resource-limits.html) and [How do I increase the service quota of my Amazon EC2resources?](http://aws.amazon.com/premiumsupport/knowledge-center/ec2-instance-limit/)\.
+ **Jobs fail before the application runs** – Some jobs might fail because of a `DockerTimeoutError` error or a `CannotPullContainerError` error\. For troubleshooting information, see [How do I resolve the "DockerTimeoutError" error in AWS Batch?](http://aws.amazon.com/premiumsupport/knowledge-center/batch-docker-timeout-error/)\.
+ **Insufficient IP addresses** – The number of IP addresses in your VPC and subnets can limit the number of instances that you can create\. Use Classless Inter\-Domain Routings \(CIDRs\) to provide more IP addresses than are required to run your workloads\. If necessary, you can also build a dedicated VPC with a large address space\. For example, you can create a VPC with multiple CIDRs in `10.x.0.0/16` and a subnet in every Availability Zone with a CIDR of `10.x.y.0/17`\. In this example, *x* is between 1\-4 and *y* is either 0 or 128\. This configuration provides 36,000 IP addresses in every subnet\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/batch/latest/userguide/images/batch-best-practices-VPC_larges_scale-1.png)
+ **Verify that instances are registered with Amazon EC2** – If you see your instances in the Amazon EC2 console, but no Amazon Elastic Container Service container instances in your Amazon ECS cluster, the Amazon ECS agent might not be installed on an Amazon Machine Image \(AMI\)\. The Amazon ECS Agent, the Amazon EC2 Data in your AMI, or the launch template might also not be configured correctly\. To isolate the root cause, create a separate Amazon EC2 instance or connect to an existing instance using SSH\. For more information, see [Amazon ECS container agent configuration](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-agent-config.html), [Amazon ECS Log File Locations](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/logs.html), and [Compute resource AMIs](compute_resource_AMIs.md)\.
+ **Review the AWS Dashboard** – Review the AWS Dashboard to verify that the expected job states and that the compute environment scales as expected\. You can also review the job logs in CloudWatch\.
+ **Verify that your instance is created** – If an instance is created, it means that your compute environment scaled as expected\. If your instances aren’t created, find the associated subnets in your compute environment to change\. For more information, see [Verify a scaling activity for an Auto Scaling group](https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-verify-scaling-activity.html)\.

  We also recommend that you verify that your instances can fulfill your related job requirements\. For example, a job might require 1 TiB of memory, but the compute environment uses a C5 instance type that's limited to 192 GB of memory\.
+ **Verify that your instances are being requested by AWS Batch** – Check Auto Scaling group history to verify that your instances are being requested by AWS Batch\. This is an indication of how Amazon EC2 tries to acquire instances\. If you receive an error stating the Amazon EC2 Spot can’t acquire an instance in a specific Availability Zone, this might be because the Availability Zone doesn't offer a specific instance family\.
+ **Verify that instances register with Amazon ECS** – If you see instances in the Amazon EC2 console, but no Amazon ECS container instances in your Amazon ECS cluster, the Amazon ECS agent might not be installed on the Amazon Machine Image \(AMI\)\. Moreover, the Amazon ECS Agent, the Amazon EC2 Data in your AMI, or the launch template might not be configured correctly\. To isolate the root cause, create a separate Amazon EC2 instance or connect to an existing instance using SSH\. For more information, see [CloudWatch agent configuration file: Logs section](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-Agent-Configuration-File-Details.html#CloudWatch-Agent-Configuration-File-Logssection), [Amazon ECS Log File Locations](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/logs.html), and [Compute resource AMIs](compute_resource_AMIs.md)\.
+ **Open a support ticket** – If you're still experiencing issues after some troubleshooting and have a support plan, open a support ticket\. In the support ticket, make sure to include information about the issue, workload specifics, the configuration, and test results\. For more information, see [Compare AWS Support Plans](http://aws.amazon.com/premiumsupport/plans/)\.
+ **Review the AWS Batch and HPC forums** – For more information, see the [AWS Batch](https://repost.aws/tags/TAAQ5TlH16Tc686CgyYUNX0g/aws-batch) and [HPC](https://repost.aws/tags/TAjBvP4otfT3eX8PswbXo9AQ/high-performance-compute) forums\.
+ **Review the AWS Batch Runtime Monitoring Dashboard** – This dashboard uses a serverless architecture to capture events from Amazon ECS, AWS Batch, and Amazon EC2 to provide insights into jobs and instances\. For more information, see [AWS Batch Runtime Monitoring Dashboards Solution](https://github.com/aws-samples/aws-batch-runtime-monitoring)\.