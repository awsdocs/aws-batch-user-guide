# Jobs<a name="jobs"></a>

Jobs are the unit of work executed by AWS Batch\. Jobs can be executed as containerized applications running on Amazon ECS container instances in an ECS cluster\.

Containerized jobs can reference a container image, command, and parameters\. For more information, see [Job Definition Parameters](job_definition_parameters.md)\.

You can submit a large number of independent, simple jobs\.

**Topics**
+ [Submitting a Job](submit_job.md)
+ [Job States](job_states.md)
+ [AWS Batch Job Environment Variables](job_env_vars.md)
+ [Automated Job Retries](job_retries.md)
+ [Job Dependencies](job_dependencies.md)
+ [Job Timeouts](job_timeouts.md)
+ [Array Jobs](array_jobs.md)
+ [Multi\-node Parallel Jobs](multi-node-parallel-jobs.md)
+ [GPU Jobs](gpu-jobs.md)