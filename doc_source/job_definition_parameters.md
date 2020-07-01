# Job Definition Parameters<a name="job_definition_parameters"></a>

Job definitions are split into four basic parts: the job definition name, the type of the job definition, parameter substitution placeholder defaults, and the container properties for the job\.

**Topics**
+ [Job Definition Name](#jobDefinitionName)
+ [Type](#type)
+ [Parameters](#parameters)
+ [Container Properties](#containerProperties)
+ [Node Properties](#nodeProperties)
+ [Retry Strategy](#retryStrategy)
+ [Timeout](#timeout)

## Job Definition Name<a name="jobDefinitionName"></a>

`jobDefinitionName`  
When you register a job definition, you specify a name\. Up to 128 letters \(uppercase and lowercase\), numbers, hyphens, and underscores are allowed\. The first job definition that is registered with that name is given a revision of 1\. Any subsequent job definitions that are registered with that name are given an incremental revision number\.   
Type: String  
Required: Yes

## Type<a name="type"></a>

`type`  
When you register a job definition, you specify the type of job\. For more information about multi\-node parallel jobs \(also called array jobs\), see [Creating a Multi\-node Parallel Job Definition](multi-node-job-def.md)\.  
Type: String  
Valid values: `container` \| `multinode`  
Required: Yes

## Parameters<a name="parameters"></a>

`parameters`  
When you submit a job, you can specify parameters that should replace the placeholders or override the default job definition parameters\. Parameters in job submission requests take precedence over the defaults in a job definition\. This allows you to use the same job definition for multiple jobs that use the same format, and programmatically change values in the command at submission time\.  
Type: String to string map  
Required: No  
When you register a job definition, you can use parameter substitution placeholders in the `command` field of a job's container properties\. For example:  

```
"command": [ "ffmpeg", "-i", "Ref::inputfile", "-c", "Ref::codec", "-o", "Ref::outputfile" ]
```
In the above example, there are `Ref::inputfile`, `Ref::codec`, and `Ref::outputfile` parameter substitution placeholders in the command\. The `parameters` object in the job definition allows you to set default values for these placeholders\. For example, to set a default for the `Ref::codec` placeholder, you specify the following in the job definition:  

```
"parameters" : {"codec" : "mp4"}
```
When this job definition is submitted to run, the `Ref::codec` argument in the container's command is replaced with the default value, `mp4`\.

## Container Properties<a name="containerProperties"></a>

When you register a job definition, you must specify a list of container properties that are passed to the Docker daemon on a container instance when the job is placed\. The following container properties are allowed in a job definition\. For single\-node jobs, these container properties are set at the job definition level\. For multi\-node parallel jobs, container properties are set in the [Node Properties](#nodeProperties) level, for each node group\.

`command`  
The command that is passed to the container\. This parameter maps to `Cmd` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `COMMAND` parameter to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\. For more information about the Docker `CMD` parameter, see [https://docs\.docker\.com/engine/reference/builder/\#cmd](https://docs.docker.com/engine/reference/builder/#cmd)\.  

```
"command": ["string", ...]
```
Type: String array  
Required: No

`environment`  
The environment variables to pass to a container\. This parameter maps to `Env` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--env` option to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\.  
We do not recommend using plaintext environment variables for sensitive information, such as credential data\.
Type: Array of key\-value pairs  
Required: No    
`name`  
The name of the environment variable\.  
Type: String  
Required: Yes, when `environment` is used\.  
`value`  
The value of the environment variable\.  
Type: String  
Required: Yes, when `environment` is used\.

```
"environment" : [
    { "name" : "string", "value" : "string" },
    { "name" : "string", "value" : "string" }
]
```

`image`  
The image used to start a container\. This string is passed directly to the Docker daemon\. Images in the Docker Hub registry are available by default\. You can also specify other repositories with `repository-url/image:tag`\. Up to 255 letters \(uppercase and lowercase\), numbers, hyphens, underscores, colons, periods, forward slashes, and number signs are allowed\. This parameter maps to `Image` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `IMAGE` parameter of [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\.  
Docker image architecture must match the processor architecture of the compute resources that they're scheduled on\. For example, ARM\-based Docker images can only run on ARM\-based compute resources\.
+ Images in Amazon ECR repositories use the full `registry/repository:tag` naming convention\. For example, `aws_account_id.dkr.ecr.region.amazonaws.com``/my-web-app:latest`
+ Images in official repositories on Docker Hub use a single name \(for example, `ubuntu` or `mongo`\)\.
+ Images in other repositories on Docker Hub are qualified with an organization name \(for example, `amazon/amazon-ecs-agent`\)\.
+ Images in other online repositories are qualified further by a domain name \(for example, `quay.io/assemblyline/ubuntu`\)\.
Type: String  
Required: Yes

`jobRoleArn`  
When you register a job definition, you can specify an IAM role\. The role provides the job container with permissions to call the API actions that are specified in its associated policies on your behalf\. For more information, see [IAM Roles for Tasks](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-iam-roles.html) in the *Amazon Elastic Container Service Developer Guide*\.   
Type: String  
Required: No

`memory`  
The hard limit \(in MiB\) of memory to present to the container\. If your container attempts to exceed the memory specified here, the container is killed\. This parameter maps to `Memory` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--memory` option to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\. You must specify at least 4 MiB of memory for a job\.  
If you are trying to maximize your resource utilization by providing your jobs as much memory as possible for a particular instance type, see [Compute Resource Memory Management](memory-management.md)\.
Type: Integer  
Required: Yes

`mountPoints`  
The mount points for data volumes in your container\. This parameter maps to `Volumes` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--volume` option to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\.  

```
"mountPoints": [
                {
                  "sourceVolume": "string",
                  "containerPath": "string",
                  "readOnly": true|false
                }
              ]
```
Type: Object array  
Required: No    
`sourceVolume`  
The name of the volume to mount\.  
Type: String  
Required: Yes, when `mountPoints` is used\.  
`containerPath`  
The path on the container at which to mount the host volume\.  
Type: String  
Required: Yes, when `mountPoints` is used\.  
`readOnly`  
If this value is `true`, the container has read\-only access to the volume\. If this value is `false`, then the container can write to the volume\. The default value is `false`\.  
Type: Boolean  
Required: No

`privileged`  
When this parameter is true, the container is given elevated privileges on the host container instance \(similar to the `root` user\)\. This parameter maps to `Privileged` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--privileged` option to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\.  

```
"privileged": true|false
```
Type: Boolean  
Required: No

`readonlyRootFilesystem`  
When this parameter is true, the container is given read\-only access to its root file system\. This parameter maps to `ReadonlyRootfs` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--read-only` option to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\.  

```
"readonlyRootFilesystem": true|false
```
Type: Boolean  
Required: No

`ulimits`  
A list of `ulimits` values to set in the container\. This parameter maps to `Ulimits` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--ulimit` option to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\.   

```
"ulimits": [
      {
        "name": string,
        "softLimit": integer,
        "hardLimit": integer
      }
      ...
    ]
```
Type: Object array  
Required: No    
`name`  
The `type` of the `ulimit`\.  
Type: String  
Required: Yes, when `ulimits` is used\.  
`hardLimit`  
The hard limit for the `ulimit` type\.  
Type: Integer  
Required: Yes, when `ulimits` is used\.  
`softLimit`  
The soft limit for the `ulimit` type\.  
Type: Integer  
Required: Yes, when `ulimits` is used\.

`user`  
The user name to use inside the container\. This parameter maps to `User` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--user` option to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\.  

```
"user": "string"
```
Type: String  
Required: No

`resourceRequirements`  
Indicates the number of GPUs to be reserved for your container\.  

```
"resourceRequirements" : [
                           {
                             "type": "GPU",
                             "value": "number"
                           }
                         ]
```
Type: Object array  
Required: No    
`type`  
The only supported value is `GPU`\.  
Type: String  
Required: Yes, when `resourceRequirements` is used\.  
`value`  
The number of physical GPUs each container will require\.  
Type: String  
Required: Yes, when `resourceRequirements` is used\.

`linuxParameters`  
Linux\-specific modifications that are applied to the container, such as details for device mappings\.  

```
"linuxParameters": {
                       "devices": [
                           {
                               "hostPath": "string",
                               "containerPath": "string",
                               "permissions": [
                                   "READ", "WRITE", "MKNOD"
                               ]
                           }
                       ]
                   }
```
Type: [LinuxParameters](https://docs.aws.amazon.com/batch/latest/APIReference/API_LinuxParameters.html) object  
Required: No    
`devices`  
List of devices mapped into the container\.  
Type: Array of [Device](https://docs.aws.amazon.com/batch/latest/APIReference/API_Device.html) objects  
Required: No    
`hostPath`  
Path at which the device available in the host\.  
Type: String  
Required: Yes  
`containerPath`  
Path at which the device is exposed in the container\. If this is not specified the device is exposed at the same path as the host path\.  
Type: String  
Required: No  
`permissions`  
Permissions for the device in the container\. If this is not specified the permissions are set to `READ`, `WRITE`, and `MKNOD`\.  
Type: Array of strings  
Required: No  
Valid values: `READ` \| `WRITE` \| `MKNOD`

`vcpus`  
The number of vCPUs reserved for the container\. This parameter maps to `CpuShares` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--cpu-shares` option to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\. Each vCPU is equivalent to 1,024 CPU shares\. You must specify at least one vCPU\.  
Type: Integer  
Required: Yes

`volumes`  
When you register a job definition, you can specify a list of volumes that are passed to the Docker daemon on a container instance\. The following parameters are allowed in the container properties:  

```
[
  {
    "name": "string",
    "host": {
      "sourcePath": "string"
    }
  }
]
```  
`name`  
The name of the volume\. Up to 255 letters \(uppercase and lowercase\), numbers, hyphens, and underscores are allowed\. This name is referenced in the `sourceVolume` parameter of container definition `mountPoints`\.  
Type: String  
Required: Yes  
`host`  
The contents of the `host` parameter determine whether your data volume persists on the host container instance and where it is stored\. If the `host` parameter is empty, then the Docker daemon assigns a host path for your data volume\. However, the data is not guaranteed to persist after the container associated with it stops running\.  
Type: Object  
Required: No    
`sourcePath`  
The path on the host container instance that is presented to the container\. If this parameter is empty, then the Docker daemon assigns a host path for you\.  
If the `host` parameter contains a `sourcePath` file location, then the data volume persists at the specified location on the host container instance until you delete it manually\. If the `sourcePath` value does not exist on the host container instance, the Docker daemon creates it\. If the location does exist, the contents of the source path folder are exported\.  
Type: String  
Required: No

## Node Properties<a name="nodeProperties"></a>

`nodeProperties`  
When you register a multi\-node parallel job definition, you must specify a list of node properties that define the number of nodes to use in your job, the main node index, and the different node ranges to use\. The following node properties are allowed in a job definition\. For more information, see [Multi\-node Parallel Jobs](multi-node-parallel-jobs.md)\.  
Type: [NodeProperties](https://docs.aws.amazon.com/batch/latest/APIReference/API_NodeProperties.html) object  
Required: No    
`mainNode`  
Specifies the node index for the main node of a multi\-node parallel job\.  
Type: Integer  
Required: Yes  
`numNodes`  
The number of nodes associated with a multi\-node parallel job\.  
Type: Integer  
Required: Yes  
`nodeRangeProperties`  
A list of node ranges and their properties associated with a multi\-node parallel job\.  
Type: Array of [NodeRangeProperty](https://docs.aws.amazon.com/batch/latest/APIReference/API_NodeRangeProperty.html) objects  
Required: Yes    
`targetNodes`  
The range of nodes, using node index values\. A range of 0:3 indicates nodes with index values of 0 through 3\. If the starting range value is omitted \(:n\), then 0 is used to start the range\. If the ending range value is omitted \(n:\), then the highest possible node index is used to end the range\. Your accumulative node ranges must account for all nodes \(0:n\)\. You may nest node ranges, for example 0:10 and 4:5, in which case the 4:5 range properties override the 0:10 properties\.   
Type: String  
Required: No  
`container`  
The container details for the node range\. For more information, see [Container Properties](#containerProperties)\.  
Type: [ContainerProperties](https://docs.aws.amazon.com/batch/latest/APIReference/API_ContainerProperties.html) object   
Required: No

## Retry Strategy<a name="retryStrategy"></a>

`retryStrategy`  
When you register a job definition, you can optionally specify a retry strategy to use for failed jobs that are submitted with this job definition\. By default, each job is attempted one time\. If you specify more than one attempt, the job is retried if it fails \(for example, if it returns a non\-zero exit code or the container instance is terminated\)\. For more information, see [Automated Job Retries](job_retries.md)\.  
Type: [RetryStrategy](https://docs.aws.amazon.com/batch/latest/APIReference/API_RetryStrategy.html) object  
Required: No    
`attempts`  
The number of times to move a job to the `RUNNABLE` status\. You may specify between 1 and 10 attempts\. If `attempts` is greater than one, the job is retried that many times if it fails, until it has moved to `RUNNABLE`\.  

```
"attempts": integer
```
Type: Integer  
Required: No

## Timeout<a name="timeout"></a>

`timeout`  
You can configure a timeout duration for your jobs so that if a job runs longer than that, AWS Batch terminates the job\. For more information, see [Job Timeouts](job_timeouts.md)\. If a job is terminated due to a timeout, it is not retried\. Any timeout configuration that is specified during a [SubmitJob](https://docs.aws.amazon.com/batch/latest/APIReference/API_SubmitJob.html) operation overrides the timeout configuration defined here\. For more information, see [Job Timeouts](job_timeouts.md)\.  
Type: [JobTimeout](https://docs.aws.amazon.com/batch/latest/APIReference/API_JobTimeout.html) object  
Required: No    
`attemptDurationSeconds`  
The time duration in seconds \(measured from the job attempt's `startedAt` timestamp\) after which AWS Batch terminates unfinished jobs\. The minimum value for the timeout is 60 seconds\.   
Type: Integer  
Required: No