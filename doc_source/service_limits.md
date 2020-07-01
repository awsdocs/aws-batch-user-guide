# AWS Batch Service Limits<a name="service_limits"></a>

The following table provides the service limits for AWS Batch, which cannot be changed\. Each limit is Region\-specific\.


| Resource | Limit | 
| --- | --- | 
| Maximum number of job queues\. For more information, see [Job Queues](job_queues.md)\. | 20 | 
| Maximum number of compute environments\. For more information, see [Compute Environments](compute_environments.md)\. | 50 | 
| Maximum number of compute environments per job queue | 3 | 
| Maximum number of job dependencies for a job | 20 | 
| Maximum job definition size \(for [https://docs.aws.amazon.com/batch/latest/APIReference/API_RegisterJobDefinition.html](https://docs.aws.amazon.com/batch/latest/APIReference/API_RegisterJobDefinition.html) API operations\) | 24 KiB | 
| Maximum job payload size \(for [https://docs.aws.amazon.com/batch/latest/APIReference/API_SubmitJob.html](https://docs.aws.amazon.com/batch/latest/APIReference/API_SubmitJob.html) API operations\) | 30 KiB | 
| Maximum array size for array jobs | 10000 | 
| Maximum number of jobs in SUBMITTED state | 1000000 | 

Depending on how you use AWS Batch, additional quotas may apply\. To learn about Amazon EC2 quotas, see [Amazon EC2 Service Quotas](https://docs.aws.amazon.com/general/latest/gr/ec2-service.html#limits_ec2) in the *AWS General Reference*\. To learn about Amazon ECS quotas, [Amazon ECS Service Quotas](https://docs.aws.amazon.com/general/latest/gr/ecs-service.html#limits_ecs) in the *AWS General Reference*\.