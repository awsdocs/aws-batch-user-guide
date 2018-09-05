# AWS Batch Job Environment Variables<a name="job_env_vars"></a>

AWS Batch automatically sets specific environment variables in container jobs\. These environment variables provide introspection for the containers inside jobs, and you can use the values of these variables in the logic of your applications\. All variables that are set by AWS Batch begin with the prefix, `AWS_BATCH_`; this is a protected environment variable prefix, and you cannot use this prefix for your own variables in job definitions or overrides\.

The following environment variables are available in job containers:

`AWS_BATCH_CE_NAME`  
This variable is set to the name of the compute environment that your job is placed in\.

`AWS_BATCH_JOB_ARRAY_INDEX`  
This variable is only set in child array jobs\. The array job index begins at 0, and each child job receives a unique index number\. For example, an array job with 10 children has index values of 0\-9\. You can use this index value to control how your array job children are differentiated\. For more information, see [Tutorial: Using the Array Job Index to Control Job Differentiation](array_index_example.md)\.

`AWS_BATCH_JOB_ATTEMPT`  
This variable is set to the job attempt number\. The first attempt is numbered 1\. For more information, see [Automated Job Retries](job_retries.md)\.

`AWS_BATCH_JOB_ID`  
This variable is set to the AWS Batch job ID\.

`AWS_BATCH_JQ_NAME`  
This variable is set to the name of the job queue that your job was submitted to\.