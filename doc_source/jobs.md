# Jobs<a name="jobs"></a>

Jobs are the unit of work that's started by AWS Batch\. Jobs can be invoked as containerized applications that run on Amazon ECS container instances in an ECS cluster\.

Containerized jobs can reference a container image, command, and parameters\. For more information, see [Job definition parameters](job_definition_parameters.md)\.

You can submit a large number of independent, simple jobs\.

**Topics**
+ [Submitting a job](submit_job.md)
+ [Job states](job_states.md)
+ [AWS Batch job environment variables](job_env_vars.md)
+ [Automated job retries](job_retries.md)
+ [Job dependencies](job_dependencies.md)
+ [Job timeouts](job_timeouts.md)
+ [Amazon EKS jobs](eks-jobs.md)
+ [Array jobs](array_jobs.md)
+ [Multi\-node parallel jobs](multi-node-parallel-jobs.md)
+ [GPU jobs](gpu-jobs.md)
+ [To create a GPU\-based job on EKS resources](run-eks-gpu-workload.md)
+ [Search and filter AWS Batch jobs](search-filter-jobs.md)
+ [Job logs](review-job-logs.md)