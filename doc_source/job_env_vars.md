# AWS Batch Job Environment Variables<a name="job_env_vars"></a>

AWS Batch automatically sets specific environment variables in container jobs\. These environment variables provide introspection for the containers inside jobs, and you can use the values of these variables in the logic of your applications\. All variables that are set by AWS Batch begin with the prefix, `AWS_BATCH_`\. This is a protected environment variable prefix, and you cannot use this prefix for your own variables in job definitions or overrides\.

The following environment variables are available in job containers:

`AWS_BATCH_CE_NAME`  
This variable is set to the name of the compute environment in which your job is placed\.

`AWS_BATCH_JOB_ARRAY_INDEX`  
This variable is only set in child array jobs\. The array job index begins at 0, and each child job receives a unique index number\. For example, an array job with 10 children has index values of 0\-9\. You can use this index value to control how your array job children are differentiated\. For more information, see [Tutorial: Using the Array Job Index to Control Job Differentiation](array_index_example.md)\.

`AWS_BATCH_JOB_ATTEMPT`  
This variable is set to the job attempt number\. The first attempt is numbered 1\. For more information, see [Automated Job Retries](job_retries.md)\.

`AWS_BATCH_JOB_ID`  
This variable is set to the AWS Batch job ID\.

`AWS_BATCH_JOB_MAIN_NODE_INDEX`  
This variable is only set in multi\-node parallel jobs\. This variable is set to the index number of the job's main node\. Your application code can compare the `AWS_BATCH_JOB_MAIN_NODE_INDEX` to the `AWS_BATCH_JOB_NODE_INDEX` on an individual node to determine if it is the main node\.

`AWS_BATCH_JOB_MAIN_NODE_PRIVATE_IPV4_ADDRESS`  
This variable is only set in multi\-node parallel job child nodes \(it is not present on the main node\)\. This variable is set to the private IPv4 address of the job's main node\. Your child node's application code can use this address to communicate with the main node\.

`AWS_BATCH_JOB_NODE_INDEX`  
This variable is only set in multi\-node parallel jobs\. This variable is set to the node index number of the node\. The node index begins at 0, and each node receives a unique index number\. For example, a multi\-node parallel job with 10 children has index values of 0\-9\.

`AWS_BATCH_JOB_NUM_NODES`  
This variable is only set in multi\-node parallel jobs\. This variable is set to the number of nodes that you have requested for your multi\-node parallel job\.

`AWS_BATCH_JQ_NAME`  
This variable is set to the name of the job queue to which your job was submitted\.