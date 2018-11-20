# Supported Resource\-Level Permissions for AWS Batch API Actions<a name="batch-supported-iam-actions-resources"></a>

The term *resource\-level permissions* refers to the ability to specify the resources on which users are allowed to perform actions\. AWS Batch has partial support for resource\-level permissions\. For certain AWS Batch actions, you can control when users are allowed to use those actions based on conditions that have to be fulfilled, or specific resources that users are allowed to use\. For example, you can grant users permissions to submit jobs, but only to a specific job queue and only with a specific job definition\. 

The following table describes the AWS Batch API actions that currently support resource\-level permissions, as well as the supported resources, resource ARNs, and condition keys for each action\.

**Important**  
If an AWS Batch API action is not listed in this table, then it does not support resource\-level permissions\. If an AWS Batch API action does not support resource\-level permissions, you can grant users permission to use the action, but you have to specify a \* wildcard for the resource element of your policy statement\.


| API action | Resource | Condition keys | 
| --- | --- | --- | 
|  RegisterJobDefinition  |  Job Definition arn:aws:batch:*region*:*account*:job\-definition/\* arn:aws:batch:*region*:*account*:job\-definition/definition\-name:revision  |  batch:User batch:Privileged batch:Image  | 
|  DeregisterJobDefinition  |  Job Definition arn:aws:batch:*region*:*account*:job\-definition/\* arn:aws:batch:*region*:*account*:job\-definition/definition\-name:revision  |  N/A  | 
|  SubmitJob  |  Job Definition arn:aws:batch:*region*:*account*:job\-definition/\* arn:aws:batch:*region*:*account*:job\-definition/definition\-name:revision Job Queue arn:aws:batch:*region*:*account*:job\-queue/\* arn:aws:batch:*region*:*account*:job\-queue/queue\-name  |  N/A  | 