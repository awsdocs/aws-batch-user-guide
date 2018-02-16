# AWS Batch Service Limits<a name="service_limits"></a>

The following table provides the default limits for AWS Batch for an AWS account; default limits can be changed on request\. For more information, see [AWS Service Limits](http://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html) in the *Amazon Web Services General Reference*\.


| Resource | Default Limit | 
| --- | --- | 
| Maximum number of job queues | 20 | 
| Maximum number of compute environments per job queue | 3 | 

The following table provides limits for AWS Batch that cannot be changed\.


| Resource | Default Limit | 
| --- | --- | 
| Maximum number of compute environments | 18 | 
| Maximum number of job dependencies | 20 | 
| Maximum job definition size \(for RegisterJobDefinition API operations\) | 20 KiB | 
| Maximum job payload size \(for SubmitJob API operations\) | 50 KiB | 
| Maximum array size for array jobs | 10,000 | 
| Maximum number of jobs in SUBMITTED state | 1,000,000 | 