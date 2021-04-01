# Job definitions<a name="job_definitions"></a>

AWS Batch job definitions specify how jobs are to be run\. While each job must reference a job definition, many of the parameters that are specified in the job definition can be overridden at runtime\. 

**Topics**
+ [Creating a job definition](create-job-definition.md)
+ [Creating a multi\-node parallel job definition](multi-node-job-def.md)
+ [Job definition template](job-definition-template.md)
+ [Job definition parameters](job_definition_parameters.md)
+ [Using the awslogs log driver](using_awslogs.md)
+ [Specifying sensitive data](specifying-sensitive-data.md)
+ [Amazon EFS volumes](efs-volumes.md)
+ [Example job definitions](example-job-definitions.md)

Some of the attributes specified in a job definition include:
+ Which Docker image to use with the container in your job
+ How many vCPUs and how much memory to use with the container
+ The command the container should run when it is started
+ What \(if any\) environment variables should be passed to the container when it starts
+ Any data volumes that should be used with the container
+ What \(if any\) IAM role your job should use for AWS permissions

For a complete description of the parameters available in a job definition, see [Job definition parameters](job_definition_parameters.md)\.