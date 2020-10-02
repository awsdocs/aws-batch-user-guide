# Supported Resource\-Level Permissions for AWS Batch API Actions<a name="batch-supported-iam-actions-resources"></a>

The term *resource\-level permissions* refers to the ability to specify the resources on which users are allowed to perform actions\. AWS Batch has partial support for resource\-level permissions\. For certain AWS Batch actions, you can control when users are allowed to use those actions based on conditions that have to be fulfilled, or specific resources that users are allowed to use\. For example, you can grant users permissions to submit jobs, but only to a specific job queue and only with a specific job definition\. 

The following list describes the AWS Batch API actions that currently support resource\-level permissions, as well as the supported resources, resource ARNs, and condition keys for each action\.

**Important**  
If an AWS Batch API action is not listed in this list, then it does not support resource\-level permissions\. If an AWS Batch API action does not support resource\-level permissions, you can grant users permission to use the action, but you have to specify a \* wildcard for the resource element of your policy statement\.

[https://docs.aws.amazon.com/batch/latest/APIReference/API_CreateComputeEnvironment.html](https://docs.aws.amazon.com/batch/latest/APIReference/API_CreateComputeEnvironment.html)  
Creates an AWS Batch compute environment\.    
**Resource**    
**Compute Environment**  
arn:aws::batch:*region*:*account*:compute\-environment/*compute\-environment\-name*  
**Condition keys**  
N/A

[https://docs.aws.amazon.com/batch/latest/APIReference/API_CreateJobQueue.html](https://docs.aws.amazon.com/batch/latest/APIReference/API_CreateJobQueue.html)  
Creates an AWS Batch job queue\.    
**Resource**    
**Compute Environment**  
arn:aws::batch:*region*:*account*:compute\-environment/*compute\-environment\-name*  
**Job Queue**  
arn:aws:batch:*region*:*account*:job\-queue/*queue\-name*  
**Condition keys**  
N/A

[https://docs.aws.amazon.com/batch/latest/APIReference/API_DeleteComputeEnvironment.html](https://docs.aws.amazon.com/batch/latest/APIReference/API_DeleteComputeEnvironment.html)  
Deletes an AWS Batch compute environment\.    
**Resource**    
**Compute Environment**  
arn:aws::batch:*region*:*account*:compute\-environment/*compute\-environment\-name*  
**Condition keys**  
N/A

[https://docs.aws.amazon.com/batch/latest/APIReference/API_DeleteJobQueue.html](https://docs.aws.amazon.com/batch/latest/APIReference/API_DeleteJobQueue.html)  
Deletes the specified job queue\.    
**Resource**    
**Job Queue**  
arn:aws:batch:*region*:*account*:job\-queue/*queue\-name*  
**Condition keys**  
N/A

[https://docs.aws.amazon.com/batch/latest/APIReference/API_DeregisterJobDefinition.html](https://docs.aws.amazon.com/batch/latest/APIReference/API_DeregisterJobDefinition.html)  
Deregisters an AWS Batch job definition\.    
**Resource**    
**Job Definition**  
arn:aws:batch:*region*:*account*:job\-definition/*definition\-name*:*revision*  
**Condition keys**  
N/A

[https://docs.aws.amazon.com/batch/latest/APIReference/API_RegisterJobDefinition.html](https://docs.aws.amazon.com/batch/latest/APIReference/API_RegisterJobDefinition.html)  
Registers an AWS Batch definition\.    
**Resource**    
**Job Definition**  
arn:aws:batch:*region*:*account*:job\-definition/*definition\-name*:*revision*  
**Condition keys**    
`batch:AWSLogsCreateGroup` \(Boolean\)  <a name="batch-supported-condition-keys-awslogscreategroup"></a>
When this parameter is true, the `awslogs-group` will be created for the logs\.  
`batch:AWSLogsGroup` \(String\)  <a name="batch-supported-condition-keys-awslogsgroup"></a>
The `awslogs` group where the logs are located\.  
`batch:AWSLogsRegion` \(String\)  <a name="batch-supported-condition-keys-awslogsregion"></a>
The Region where the logs are sent to\.  
`batch:AWSLogsStreamPrefix` \(String\)  <a name="batch-supported-condition-keys-awslogsstreamprefix"></a>
The `awslogs` log stream prefix\.  
`batch:Image` \(String\)  <a name="batch-supported-condition-keys-image"></a>
The Docker image used to start a job\.  
`batch:LogDriver` \(String\)  <a name="batch-supported-condition-keys-logdriver"></a>
The log driver used for the job\.  
`batch:Privileged` \(Boolean\)  <a name="batch-supported-condition-keys-privileged"></a>
When this parameter is true, the container for the job is given elevated permissions on the host container instance \(similar to the root user\)\.  
`batch:User` \(String\)  <a name="batch-supported-condition-keys-user"></a>
The user name or numeric uid to use inside the container for the job\.

[https://docs.aws.amazon.com/batch/latest/APIReference/API_SubmitJob.html](https://docs.aws.amazon.com/batch/latest/APIReference/API_SubmitJob.html)  
Submits an AWS Batch job from a job definition\.    
**Resource**    
**Job Definition**  
arn:aws:batch:*region*:*account*:job\-definition/*definition\-name*:*revision*  
**Job Queue**  
arn:aws:batch:*region*:*account*:job\-queue/*queue\-name*  
**Condition keys**  
N/A

[https://docs.aws.amazon.com/batch/latest/APIReference/API_UpdateComputeEnvironment.html](https://docs.aws.amazon.com/batch/latest/APIReference/API_UpdateComputeEnvironment.html)  
Updates an AWS Batch compute environment\.    
**Resource**    
**Compute Environment**  
arn:aws::batch:*region*:*account*:compute\-environment/*compute\-environment\-name*  
**Condition keys**  
N/A

[https://docs.aws.amazon.com/batch/latest/APIReference/API_UpdateJobQueue.html](https://docs.aws.amazon.com/batch/latest/APIReference/API_UpdateJobQueue.html)  
Updates a job queue\.    
**Resource**    
**Job Queue**  
arn:aws:batch:*region*:*account*:job\-queue/*queue\-name*  
**Condition keys**  
N/A