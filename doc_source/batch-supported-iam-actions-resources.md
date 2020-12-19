# Supported Resource\-Level Permissions for AWS Batch API Actions<a name="batch-supported-iam-actions-resources"></a>

The term *resource\-level permissions* refers to the ability to specify the resources on which users are allowed to perform actions\. AWS Batch has partial support for resource\-level permissions\. For certain AWS Batch actions, you can control when users are allowed to use those actions based on conditions that have to be fulfilled, or specific resources that users are allowed to use\. For example, you can grant users permissions to submit jobs, but only to a specific job queue and only with a specific job definition\. 

The following list describes the AWS Batch API actions that currently support resource\-level permissions, as well as the supported resources, resource ARNs, and condition keys for each action\.

**Important**  
If an AWS Batch API action isn't listed in this list, then it does not support resource\-level permissions\. If an AWS Batch API action does not support resource\-level permissions, you can grant users permission to use the action, but you have to specify a \* wildcard for the resource element of your policy statement\.

Actions  
[CancelJob](#batch-supported-actions-canceljob), [CreateComputeEnvironment](#batch-supported-actions-createcomputeenvironment), [CreateJobQueue](#batch-supported-actions-createjobqueue), [DeleteComputeEnvironment](#batch-supported-actions-deletecomputeenvironment), [DeleteJobQueue](#batch-supported-actions-deletejobqueue), [DeregisterJobDefinition](#batch-supported-actions-deregisterjobdefinition), [ListTagsForResource](#batch-supported-actions-listtagsforresource), [RegisterJobDefinition](#batch-supported-actions-registerjobdefinition), [SubmitJob](#batch-supported-actions-submitjob), [TagResource](#batch-supported-actions-tagresource), [TerminateJob](#batch-supported-actions-terminatejob), [UntagResource](#batch-supported-actions-untagresource), [UpdateComputeEnvironment](#batch-supported-actions-updatecomputeenvironment), [UpdateJobQueue](#batch-supported-actions-updatejobqueue)

[https://docs.aws.amazon.com/batch/latest/APIReference/API_CancelJob.html](https://docs.aws.amazon.com/batch/latest/APIReference/API_CancelJob.html)  <a name="batch-supported-actions-canceljob"></a>
Cancels a job in an AWS Batch queue\.    
**Resource**    
**Job**  
arn:aws:batch:*region*:*account*:job/*jobId*    
**Condition keys**    
`aws:ResourceTag/${TagKey}` \(String\)  
Filters actions based on the tags associated with the resource\.

[https://docs.aws.amazon.com/batch/latest/APIReference/API_CreateComputeEnvironment.html](https://docs.aws.amazon.com/batch/latest/APIReference/API_CreateComputeEnvironment.html)  <a name="batch-supported-actions-createcomputeenvironment"></a>
Creates an AWS Batch compute environment\.    
**Resource**    
**Compute Environment**  
arn:aws::batch:*region*:*account*:compute\-environment/*compute\-environment\-name*    
**Condition keys**    
`aws:ResourceTag/${TagKey}` \(String\)  
Filters actions based on the tags associated with the resource\.  
**Condition keys**    
`aws:RequestTag/${TagKey}` \(String\)  
Filters actions based on the tags that are passed in the request\.  
`aws:TagKeys` \(String\)  
Filters actions based on the tag keys that are passed in the request\.

[https://docs.aws.amazon.com/batch/latest/APIReference/API_CreateJobQueue.html](https://docs.aws.amazon.com/batch/latest/APIReference/API_CreateJobQueue.html)  <a name="batch-supported-actions-createjobqueue"></a>
Creates an AWS Batch job queue\.    
**Resource**    
**Compute Environment**  
arn:aws::batch:*region*:*account*:compute\-environment/*compute\-environment\-name*    
**Condition keys**    
`aws:ResourceTag/${TagKey}` \(String\)  
Filters actions based on the tags associated with the resource\.  
**Job Queue**  
arn:aws:batch:*region*:*account*:job\-queue/*queue\-name*    
**Condition keys**    
`aws:ResourceTag/${TagKey}` \(String\)  
Filters actions based on the tags associated with the resource\.  
**Condition keys**    
`aws:RequestTag/${TagKey}` \(String\)  
Filters actions based on the tags that are passed in the request\.  
`aws:TagKeys` \(String\)  
Filters actions based on the tag keys that are passed in the request\.

[https://docs.aws.amazon.com/batch/latest/APIReference/API_DeleteComputeEnvironment.html](https://docs.aws.amazon.com/batch/latest/APIReference/API_DeleteComputeEnvironment.html)  <a name="batch-supported-actions-deletecomputeenvironment"></a>
Deletes an AWS Batch compute environment\.    
**Resource**    
**Compute Environment**  
arn:aws::batch:*region*:*account*:compute\-environment/*compute\-environment\-name*    
**Condition keys**    
`aws:ResourceTag/${TagKey}` \(String\)  
Filters actions based on the tags associated with the resource\.

[https://docs.aws.amazon.com/batch/latest/APIReference/API_DeleteJobQueue.html](https://docs.aws.amazon.com/batch/latest/APIReference/API_DeleteJobQueue.html)  <a name="batch-supported-actions-deletejobqueue"></a>
Deletes the specified job queue\. Deleting the job queue eventually deletes all of the jobs in the queue\. Jobs are deleted at a rate of about 16 jobs each second\.    
**Resource**    
**Job Queue**  
arn:aws:batch:*region*:*account*:job\-queue/*queue\-name*    
**Condition keys**    
`aws:ResourceTag/${TagKey}` \(String\)  
Filters actions based on the tags associated with the resource\.

[https://docs.aws.amazon.com/batch/latest/APIReference/API_DeregisterJobDefinition.html](https://docs.aws.amazon.com/batch/latest/APIReference/API_DeregisterJobDefinition.html)  <a name="batch-supported-actions-deregisterjobdefinition"></a>
Deregisters an AWS Batch job definition\.    
**Resource**    
**Job Definition**  
arn:aws:batch:*region*:*account*:job\-definition/*definition\-name*:*revision*    
**Condition keys**    
`aws:ResourceTag/${TagKey}` \(String\)  
Filters actions based on the tags associated with the resource\.

[https://docs.aws.amazon.com/batch/latest/APIReference/API_TagResource.html](https://docs.aws.amazon.com/batch/latest/APIReference/API_TagResource.html)  <a name="batch-supported-actions-listtagsforresource"></a>
Lists the tags for the specified resource\.    
**Resource**    
**Compute Environment**  
arn:aws::batch:*region*:*account*:compute\-environment/*compute\-environment\-name*    
**Condition keys**    
`aws:ResourceTag/${TagKey}` \(String\)  
Filters actions based on the tags associated with the resource\.  
**Job**  
arn:aws:batch:*region*:*account*:job/*jobId*    
**Condition keys**    
`aws:ResourceTag/${TagKey}` \(String\)  
Filters actions based on the tags associated with the resource\.  
**Job Definition**  
arn:aws:batch:*region*:*account*:job\-definition/*definition\-name*:*revision*    
**Condition keys**    
`aws:ResourceTag/${TagKey}` \(String\)  
Filters actions based on the tags associated with the resource\.  
**Job Queue**  
arn:aws:batch:*region*:*account*:job\-queue/*queue\-name*    
**Condition keys**    
`aws:ResourceTag/${TagKey}` \(String\)  
Filters actions based on the tags associated with the resource\.

[https://docs.aws.amazon.com/batch/latest/APIReference/API_RegisterJobDefinition.html](https://docs.aws.amazon.com/batch/latest/APIReference/API_RegisterJobDefinition.html)  <a name="batch-supported-actions-registerjobdefinition"></a>
Registers an AWS Batch definition\.    
**Resource**    
**Job Definition**  
arn:aws:batch:*region*:*account*:job\-definition/*definition\-name*:*revision*    
**Condition keys**    
`aws:ResourceTag/${TagKey}` \(String\)  
Filters actions based on the tags associated with the resource\.  
**Condition keys**    
`batch:AWSLogsCreateGroup` \(Boolean\)  
When this parameter is true, the `awslogs-group` will be created for the logs\.  
`batch:AWSLogsGroup` \(String\)  
The `awslogs` group where the logs are located\.  
`batch:AWSLogsRegion` \(String\)  
The Region where the logs are sent to\.  
`batch:AWSLogsStreamPrefix` \(String\)  
The `awslogs` log stream prefix\.  
`batch:Image` \(String\)  
The Docker image used to start a job\.  
`batch:LogDriver` \(String\)  
The log driver used for the job\.  
`batch:Privileged` \(Boolean\)  
When this parameter is true, the container for the job is given elevated permissions on the host container instance \(similar to the root user\)\.  
`batch:User` \(String\)  
The user name or numeric uid to use inside the container for the job\.  
`aws:RequestTag/${TagKey}` \(String\)  
Filters actions based on the tags that are passed in the request\.  
`aws:TagKeys` \(String\)  
Filters actions based on the tag keys that are passed in the request\.

[https://docs.aws.amazon.com/batch/latest/APIReference/API_SubmitJob.html](https://docs.aws.amazon.com/batch/latest/APIReference/API_SubmitJob.html)  <a name="batch-supported-actions-submitjob"></a>
Submits an AWS Batch job from a job definition\.    
**Resource**    
**Job**  
arn:aws:batch:*region*:*account*:job/*jobId*    
**Condition keys**    
`aws:ResourceTag/${TagKey}` \(String\)  
Filters actions based on the tags associated with the resource\.  
**Job Definition**  
arn:aws:batch:*region*:*account*:job\-definition/*definition\-name*:*revision*    
**Condition keys**    
`aws:ResourceTag/${TagKey}` \(String\)  
Filters actions based on the tags associated with the resource\.  
**Job Queue**  
arn:aws:batch:*region*:*account*:job\-queue/*queue\-name*    
**Condition keys**    
`aws:ResourceTag/${TagKey}` \(String\)  
Filters actions based on the tags associated with the resource\.

[https://docs.aws.amazon.com/batch/latest/APIReference/API_TagResource.html](https://docs.aws.amazon.com/batch/latest/APIReference/API_TagResource.html)  <a name="batch-supported-actions-tagresource"></a>
Tags the specified resource\.    
**Resource**    
**Compute Environment**  
arn:aws::batch:*region*:*account*:compute\-environment/*compute\-environment\-name*    
**Condition keys**    
`aws:ResourceTag/${TagKey}` \(String\)  
Filters actions based on the tags associated with the resource\.  
**Job**  
arn:aws:batch:*region*:*account*:job/*jobId*    
**Condition keys**    
`aws:ResourceTag/${TagKey}` \(String\)  
Filters actions based on the tags associated with the resource\.  
**Job Definition**  
arn:aws:batch:*region*:*account*:job\-definition/*definition\-name*:*revision*    
**Condition keys**    
`aws:ResourceTag/${TagKey}` \(String\)  
Filters actions based on the tags associated with the resource\.  
**Job Queue**  
arn:aws:batch:*region*:*account*:job\-queue/*queue\-name*    
**Condition keys**    
`aws:ResourceTag/${TagKey}` \(String\)  
Filters actions based on the tags associated with the resource\.  
**Condition keys**    
`aws:RequestTag/${TagKey}` \(String\)  
Filters actions based on the tags that are passed in the request\.  
`aws:TagKeys` \(String\)  
Filters actions based on the tag keys that are passed in the request\.

[https://docs.aws.amazon.com/batch/latest/APIReference/API_TerminateJob.html](https://docs.aws.amazon.com/batch/latest/APIReference/API_TerminateJob.html)  <a name="batch-supported-actions-terminatejob"></a>
Terminates a job in an AWS Batch job queue\.    
**Resource**    
**Job**  
arn:aws:batch:*region*:*account*:job/*jobId*    
**Condition keys**    
`aws:ResourceTag/${TagKey}` \(String\)  
Filters actions based on the tags associated with the resource\.

[https://docs.aws.amazon.com/batch/latest/APIReference/API_UntagResource.html](https://docs.aws.amazon.com/batch/latest/APIReference/API_UntagResource.html)  <a name="batch-supported-actions-untagresource"></a>
Untags the specified resource\.    
**Resource**    
**Compute Environment**  
arn:aws::batch:*region*:*account*:compute\-environment/*compute\-environment\-name*    
**Condition keys**    
`aws:ResourceTag/${TagKey}` \(String\)  
Filters actions based on the tags associated with the resource\.  
**Job**  
arn:aws:batch:*region*:*account*:job/*jobId*    
**Condition keys**    
`aws:ResourceTag/${TagKey}` \(String\)  
Filters actions based on the tags associated with the resource\.  
**Job Definition**  
arn:aws:batch:*region*:*account*:job\-definition/*definition\-name*:*revision*    
**Condition keys**    
`aws:ResourceTag/${TagKey}` \(String\)  
Filters actions based on the tags associated with the resource\.  
**Job Queue**  
arn:aws:batch:*region*:*account*:job\-queue/*queue\-name*    
**Condition keys**    
`aws:ResourceTag/${TagKey}` \(String\)  
Filters actions based on the tags associated with the resource\.  
**Condition keys**    
`aws:TagKeys` \(String\)  
Filters actions based on the tag keys that are passed in the request\.

[https://docs.aws.amazon.com/batch/latest/APIReference/API_UpdateComputeEnvironment.html](https://docs.aws.amazon.com/batch/latest/APIReference/API_UpdateComputeEnvironment.html)  <a name="batch-supported-actions-updatecomputeenvironment"></a>
Updates an AWS Batch compute environment\.    
**Resource**    
**Compute Environment**  
arn:aws::batch:*region*:*account*:compute\-environment/*compute\-environment\-name*    
**Condition keys**    
`aws:ResourceTag/${TagKey}` \(String\)  
Filters actions based on the tags associated with the resource\.

[https://docs.aws.amazon.com/batch/latest/APIReference/API_UpdateJobQueue.html](https://docs.aws.amazon.com/batch/latest/APIReference/API_UpdateJobQueue.html)  <a name="batch-supported-actions-updatejobqueue"></a>
Updates a job queue\.    
**Resource**    
**Job Queue**  
arn:aws:batch:*region*:*account*:job\-queue/*queue\-name*    
**Condition keys**    
`aws:ResourceTag/${TagKey}` \(String\)  
Filters actions based on the tags associated with the resource\.