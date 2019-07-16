# AWS Batch Service Limits<a name="service_limits"></a>

The following table provides the service limits for AWS Batch, which cannot be changed\.


| Resource | Limit | 
| --- | --- | 
| Maximum number of job queues | 20 | 
| Maximum number of compute environments | 50 | 
| Maximum number of compute environments per job queue | 3 | 
| Maximum number of job dependencies | 20 | 
| Maximum job definition size \(for [https://docs.aws.amazon.com/batch/latest/APIReference/API_RegisterJobDefinition.html](https://docs.aws.amazon.com/batch/latest/APIReference/API_RegisterJobDefinition.html) API operations\) | 24 KiB | 
| Maximum job payload size \(for [https://docs.aws.amazon.com/batch/latest/APIReference/API_SubmitJob.html](https://docs.aws.amazon.com/batch/latest/APIReference/API_SubmitJob.html) API operations\) | 30 KiB | 
| Maximum array size for array jobs | 10000 | 
| Maximum number of jobs in SUBMITTED state | 1000000 | 