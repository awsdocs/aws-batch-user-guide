# AWS Batch on AWS Fargate<a name="fargate"></a>

AWS Fargate is a technology that you can use with AWS Batch to run [containers](https://aws.amazon.com/what-are-containers) without having to manage servers or clusters of Amazon EC2 instances\. With AWS Fargate, you no longer have to provision, configure, or scale clusters of virtual machines to run containers\. This removes the need to choose server types, decide when to scale your clusters, or optimize cluster packing\.

When you run your jobs with Fargate resources, you package your application in containers, specify the CPU and memory requirements, define networking and IAM policies, and launch the application\. Each Fargate job has its own isolation boundary and does not share the underlying kernel, CPU resources, memory resources, or elastic network interface with another job\.

**Contents**
+ [Job definitions on Fargate](#fargate-job-definitions)
+ [Job queues on Fargate](#fargate-job-queues)
+ [Compute environments on Fargate](#fargate-compute-environments)

## Job definitions on Fargate<a name="fargate-job-definitions"></a>

AWS Batch jobs on Fargate don't support all of the job definition parameters that are available\. Some parameters are not supported at all, and others behave differently for Fargate jobs\.

The following list describes job definition parameters that are not valid or otherwise restricted in Fargate jobs\.

`platformCapabilities`  
Must be specified as `FARGATE`\.  

```
"platformCapabilities": [ "FARGATE" ]
```

`type`  
Must be specified as `container`\.  

```
"type": "container"
```

Parameters in `containerProperties`    
`executionRoleArn`  
Must be specified for jobs running on Fargate resources\. For more information, see [IAM Roles for Tasks](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-iam-roles.html) in the *Amazon Elastic Container Service Developer Guide*\.  

```
"executionRoleArn": "arn:aws:iam::123456789012:role/ecsTaskExecutionRole"
```  
`fargatePlatformConfiguration`  
\(Optional, only for Fargate job definitions\)\. Specifies the Fargate platform version, or `LATEST` for a recent platform version\. Possible values for `platformVersion` are `1.3.0`, `1.4.0`, and `LATEST` \(default\)\.  

```
"fargatePlatformConfiguration": { "platformVersion": "1.4.0" }
```

`instanceType``ulimits`  
Not applicable for jobs running on Fargate resources\.

`memory``vcpus`  
These settings must be specified in `resourceRequirements`

`privileged`  
Either don't specify this parameter, or specify `false`\.  

```
"privileged": false
```

`resourceRequirements`  
Both memory and vCPU requirements must be specified, using [supported values](job_definition_parameters.md#ContainerProperties-resourceRequirements-Fargate-memory-vcpu)\. GPU resources are not supported for jobs running on Fargate resources\.  

```
"resourceRequirements": [
  {"type": "MEMORY", "value": "512"},
  {"type": "VCPU",   "value": "0.25"}
]
```

Parameters in `linuxParameters`    
`devices``maxSwap``sharedMemorySize``swappiness``tmpfs`  
Not applicable for jobs running on Fargate resources\.

Parameters in `logConfiguration`    
`logDriver`  
Only `awslogs` and `fluentd` are supported\. For more information, see [Using the awslogs log driver](using_awslogs.md)\.

Members in `networkConfiguration`    
`assignPublicIp`  
If the private subnet does not have a NAT gateway attached to send traffic to the Internet, `[assignPublicIp](https://docs.aws.amazon.com/batch/latest/APIReference/API_NetworkConfiguration.html#Batch-Type-NetworkConfiguration-assignPublicIp)` must be "`ENABLED`"\. For more information, see For more information, see [AWS Batch execution IAM role](execution-IAM-role.md)\.

## Job queues on Fargate<a name="fargate-job-queues"></a>

AWS Batch job queues on Fargate are essentially unchanged\. The only restriction is that the compute environments listed in `computeEnvironmentOrder` must all be Fargate compute environments \(`FARGATE` or `FARGATE_SPOT`\)\. EC2 and Fargate compute environments can't be mixed\.

## Compute environments on Fargate<a name="fargate-compute-environments"></a>

AWS Batch compute environments on Fargate don't support all of the compute environment parameters that are available\. Some parameters are not supported at all, and others have specific requirements for Fargate\.

The following list describes compute environment parameters that are not valid or otherwise restricted in Fargate jobs\.

`type`  
This parameter must be `MANAGED`\.  

```
"type": "MANAGED"
```

Parameters in the `computeResources` object    
`allocationStrategy``bidPercentage``desiredvCpus``imageId``instanceTypes``ec2Configuration``ec2KeyPair``instanceRole``launchTemplate``minvCpus``placementGroup``spotIamFleetRole`  
These aren't applicable for Fargate compute environments and shouldn't be provided\.  
`subnets`  
If the subnets listed in this parameter don't have NAT gateways attached, the `assignPublicIp` parameter in the job definition must be set to `ENABLED`\.  
`tags`  
This isn't applicable for Fargate compute environments and shouldn't be provided\. To specify tags for Fargate compute environments, use the `tags` parameter that's not in the `computeResources` object\.  
`type`  
This must be either `FARGATE` or `FARGATE_SPOT`\.  

```
"type": "FARGATE_SPOT"
```