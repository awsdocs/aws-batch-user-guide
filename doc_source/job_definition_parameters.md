# Job Definition Parameters<a name="job_definition_parameters"></a>

Job definitions are split into four basic parts: the job definition name, the type of the job definition, parameter substitution placeholder defaults, and the container properties for the job\.

**Contents**
+ [Job Definition Name](#jobDefinitionName)
+ [Type](#type)
+ [Parameters](#parameters)
+ [Container Properties](#containerProperties)
+ [Node Properties](#nodeProperties)
+ [Retry Strategy](#retryStrategy)
+ [Timeout](#timeout)
+ [Tags](#job-definition-parameters-tags)

## Job Definition Name<a name="jobDefinitionName"></a>

`jobDefinitionName`  
When you register a job definition, you specify a name\. Up to 128 letters \(uppercase and lowercase\), numbers, hyphens, and underscores are allowed\. The first job definition that is registered with that name is given a revision of 1\. Any subsequent job definitions that are registered with that name are given an incremental revision number\.   
Type: String  
Required: Yes

## Type<a name="type"></a>

`type`  
When you register a job definition, you specify the type of job\. For more information about multi\-node parallel jobs, see [Creating a Multi\-node Parallel Job Definition](multi-node-job-def.md)\.  
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
    { "name" : "envName1", "value" : "envValue1" },
    { "name" : "envName2", "value" : "envValue2" }
]
```

`executionRoleArn`  
When you register a job definition, you can specify an IAM role\. The role provides the Amazon ECS container agent with permissions to call the API actions that are specified in its associated policies on your behalf\. For more information, see [AWS Batch execution IAM role](execution-IAM-role.md)\.  
Type: String  
Required: No

`image`  
The Docker image used to start a job\. This string is passed directly to the Docker daemon\. Images in the Docker Hub registry are available by default\. You can also specify other repositories with `repository-url/image:tag`\. Up to 255 letters \(uppercase and lowercase\), numbers, hyphens, underscores, colons, periods, forward slashes, and number signs are allowed\. This parameter maps to `Image` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `IMAGE` parameter of [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\.  
Docker image architecture must match the processor architecture of the compute resources that they're scheduled on\. For example, ARM\-based Docker images can only run on ARM\-based compute resources\.
+ Images in Amazon ECR repositories use the full `registry/repository:tag` naming convention\. For example, `aws_account_id.dkr.ecr.region.amazonaws.com``/my-web-app:latest`
+ Images in official repositories on Docker Hub use a single name \(for example, `ubuntu` or `mongo`\)\.
+ Images in other repositories on Docker Hub are qualified with an organization name \(for example, `amazon/amazon-ecs-agent`\)\.
+ Images in other online repositories are qualified further by a domain name \(for example, `quay.io/assemblyline/ubuntu`\)\.
Type: String  
Required: Yes

`instanceType`  
The instance type to use for a multi\-node parallel job\. Currently all node groups in a multi\-node parallel job must use the same instance type\. This parameter is not valid for single\-node container jobs\.  
Type: String  
Required: No

`jobRoleArn`  
When you register a job definition, you can specify an IAM role\. The role provides the job container with permissions to call the API actions that are specified in its associated policies on your behalf\. For more information, see [IAM Roles for Tasks](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-iam-roles.html) in the *Amazon Elastic Container Service Developer Guide*\.   
Type: String  
Required: No

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
    ],
    "initProcessEnabled": true|false,
    "sharedMemorySize": 0,
    "tmpfs": [
        {
            "containerPath": "string",
            "size": integer,
            "mountOptions": [
                "string"
            ]
        }
    ],
    "maxSwap": integer,
    "swappiness": integer
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
`initProcessEnabled`  
If true, run an `init` process inside the container that forwards signals and reaps processes\. This parameter maps to the `--init` option to [docker run](https://docs.docker.com/engine/reference/run/)\. This parameter requires version 1\.25 of the Docker Remote API or greater on your container instance\. To check the Docker Remote API version on your container instance, log into your container instance and run the following command: `sudo docker version | grep "Server API version"`  
Type: Boolean  
Required: No  
`maxSwap`  
The total amount of swap memory \(in MiB\) a job can use\. This parameter will be translated to the `--memory-swap` option to [docker run](https://docs.docker.com/engine/reference/run/) where the value would be the sum of the container memory plus the `maxSwap` value\. For more information, see [`--memory-swap` details](https://docs.docker.com/config/containers/resource_constraints/#--memory-swap-details) in the Docker documentation\.  
If a `maxSwap` value of `0` is specified, the container will not use swap\. Accepted values are `0` or any positive integer\. If the `maxSwap` parameter is omitted, the container will use the swap configuration for the container instance it is running on\. A `maxSwap` value must be set for the `swappiness` parameter to be used\.  
Type: Integer  
Required: No  
`sharedMemorySize`  
The value for the size \(in MiB\) of the `/dev/shm` volume\. This parameter maps to the `--shm-size` option to [docker run](https://docs.docker.com/engine/reference/run/)\.  
Type: Integer  
Required: No  
`swappiness`  
This allows you to tune a container's memory swappiness behavior\. A `swappiness` value of `0` will cause swapping to not happen unless absolutely necessary\. A `swappiness` value of `100` will cause pages to be swapped very aggressively\. Accepted values are whole numbers between `0` and `100`\. If the `swappiness` parameter is not specified, a default value of `60` is used\. If a value is not specified for `maxSwap` then this parameter is ignored\. This parameter maps to the `--memory-swappiness` option to [docker run](https://docs.docker.com/engine/reference/run/)\.  
Type: Integer  
Required: No  
`tmpfs`  
The container path, mount options, and size of the tmpfs mount\.  
Type: Array of [Tmpfs](https://docs.aws.amazon.com/batch/latest/APIReference/API_Tmpfs.html) objects  
Required: No    
`containerPath`  
The absolute file path in the container where the tmpfs volume is to be mounted\.  
Type: String  
Required: Yes  
`mountOptions`  
The list of tmpfs volume mount options\.  
Valid values: "`defaults`" \| "`ro`" \| "`rw`" \| "`suid`" \| "`nosuid`" \| "`dev`" \| "`nodev`" \| "`exec`" \| "`noexec`" \| "`sync`" \| "`async`" \| "`dirsync`" \| "`remount`" \| "`mand`" \| "`nomand`" \| "`atime`" \| "`noatime`" \| "`diratime`" \| "`nodiratime`" \| "`bind`" \| "`rbind`" \| "`unbindable`" \| "`runbindable`" \| "`private`" \| "`rprivate`" \| "`shared`" \| "`rshared`" \| "`slave`" \| "`rslave`" \| "`relatime`" \| "`norelatime`" \| "`strictatime`" \| "`nostrictatime`" \| "`mode`" \| "`uid`" \| "`gid`" \| "`nr_inodes`" \| "`nr_blocks`" \| "`mpol`"  
Type: Array of strings  
Required: No  
`size`  
The size \(in MiB\) of the tmpfs volume\.  
Type: Integer  
Required: Yes

`logConfiguration`  
The log configuration specification for the job\.  

```
"logConfiguration": {
    "devices": [
        {
            "logDriver": "string",
            "options": {
                "optionName1" : "optionValue1",
                "optionName2" : "optionValue2"
            }
            "secretOptions": [
              {
                  "name" : "secretOptionName1",
                  "valueFrom" : "secretOptionArn1"
              },
              {
                  "name" : "secretOptionName2",
                  "valueFrom" : "secretOptionArn2"
              }
            ]
        }
    ]
}
```
Type: [LogConfiguration](https://docs.aws.amazon.com/batch/latest/APIReference/API_LogConfiguration.html) object  
Required: No    
`logDriver`  
The log driver to use for the job\. By default, AWS Batch enables the `awslogs` log driver   
This parameter maps to `LogConfig` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--log-driver` option to [docker run](https://docs.docker.com/engine/reference/run/)\. By default, jobs use the same logging driver that the Docker daemon uses\. However the job may use a different logging driver than the Docker daemon by specifying a log driver with this parameter in the job definition\. To use a different logging driver for a job, the log system must be configured properly on the container instance in the compute environment \(or on a different log server for remote logging options\)\. For more information on the options for different supported log drivers, see [Configure logging drivers](https://docs.docker.com/engine/admin/logging/overview/) in the Docker documentation\.  
AWS Batch currently supports a subset of the logging drivers available to the Docker daemon\. Additional log drivers may be available in future releases of the Amazon ECS container agent\.
This parameter requires version 1\.18 of the Docker Remote API or greater on your container instance\. To check the Docker Remote API version on your container instance, log into your container instance and run the following command: `sudo docker version | grep "Server API version"`  
The Amazon ECS container agent running on a container instance must register the logging drivers available on that instance with the `ECS_AVAILABLE_LOGGING_DRIVERS` environment variable before containers placed on that instance can use these log configuration options\. For more information, see [Amazon ECS Container Agent Configuration](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-agent-config.html) in the *Amazon Elastic Container Service Developer Guide*\.  
`awslogs`  
Specifies the Amazon CloudWatch Logs logging driver\. For more information, see [Using the awslogs Log Driver](using_awslogs.md) and [Amazon CloudWatch Logs logging driver](https://docs.docker.com/config/containers/logging/awslogs/) in the Docker documentation\.  
`fluentd`  
Specifies the Fluentd logging driver\. For more information, including usage and options, see [Fluentd logging driver](https://docs.docker.com/config/containers/logging/fluentd/) in the Docker documentation\.  
`gelf`  
Specifies the Graylog Extended Format \(GELF\) logging driver\. For more information, including usage and options, see [Graylog Extended Format logging driver](https://docs.docker.com/config/containers/logging/gelf/) in the Docker documentation\.  
`journald`  
Specifies the journald logging driver\. For more information, including usage and options, see [Journald logging driver](https://docs.docker.com/config/containers/logging/journald/) in the Docker documentation\.  
`json-file`  
Specifies the JSON file logging driver\. For more information, including usage and options, see [JSON File logging driver](https://docs.docker.com/config/containers/logging/json-file/) in the Docker documentation\.  
`splunk`  
Specifies the Splunk logging driver\. For more information, including usage and options, see [Splunk logging driver](https://docs.docker.com/config/containers/logging/splunk/) in the Docker documentation\.  
`syslog`  
Specifies the syslog logging driver\. For more information, including usage and options, see [Syslog logging driver](https://docs.docker.com/config/containers/logging/syslog/) in the Docker documentation\.
Type: String  
Required: Yes  
Valid values: `awslogs` \| `fluentd` \| `gelf` \| `journald` \| `json-file` \| `splunk` \| `syslog`  
`options`  
Log configuration options to send to a log driver for the job\.  
Type: String to string map  
Required: No  
`secretOptions`  
An object representing the secret to pass to the log configuration\. For more information, see [Specifying sensitive data](specifying-sensitive-data.md)\.  
Type: object array  
Required: No    
`name`  
The name of the log driver option to set in the job\.  
Type: String  
Required: Yes  
`valueFrom`  
The ARN of the secret to expose to the log configuration of the container\.  
Type: String  
Required: Yes

`memory`  
The hard limit \(in MiB\) of memory to present to the container\. If your container attempts to exceed the memory specified here, the container is killed\. This parameter maps to `Memory` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--memory` option to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\. You must specify at least 4 MiB of memory for a job\. This is required but can be specified in several places for multi\-node parallel \(MNP\) jobs; it must be specified for each node at least once\.  
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

`resourceRequirements`  
Indicates the number of GPUs to be reserved for your job\.  

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

`secrets`  
The secrets for the job that will be exposed as environment variables\. For more information, see [Specifying sensitive data](specifying-sensitive-data.md)\.  

```
"secrets": [
    {
      "name": "secretName1",
      "valueFrom": "secretArn1"
    },
    {
      "name": "secretName2",
      "valueFrom": "secretArn2"
    }
    ...
]
```
Type: Object array  
Required: No    
`name`  
The name of the environment variable that will contain the secret\.  
Type: String  
Required: Yes, when `secrets` is used\.  
`valueFrom`  
The ARN of the secret\.  
Type: String  
Required: Yes, when `secrets` is used\.

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

`vcpus`  
The number of vCPUs reserved for the container\. This parameter maps to `CpuShares` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--cpu-shares` option to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\. Each vCPU is equivalent to 1,024 CPU shares\. You must specify at least one vCPU\. This is required but can be specified in several places for multi\-node parallel \(MNP\) jobs; it must be specified for each node at least once\.  
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
`evaluateOnExit`  
Array of up to 5 objects that specify conditions under which the job should be retried or failed\. If this parameter is specified, then the `attempts` parameter must also be specified\.  

```
"evaluateOnExit": [
   {
      "action": "string",
      "onExitCode": "string",
      "onReason": "string",
      "onStatusReason": "string"
   }
]
```
Type: Array of [EvaluateOnExit](https://docs.aws.amazon.com/batch/latest/APIReference/API_EvaluateOnExit.html) objects  
Required: No    
`action`  
Specifies the action to take if all of the specified conditions \(`onStatusReason`, `onReason`, and `onExitCode`\) are met\. The values are not case sensitive\.  
Type: String  
Required: Yes  
Valid values: `RETRY` \| `EXIT`  
`onExitCode`  
Contains a glob pattern to match against the decimal representation of the `ExitCode` returned for a job\. The patten can be up to 512 characters long, can contain only numbers, and can optionally end with an asterisk \(\*\) so that only the start of the string needs to be an exact match\.  
Type: String  
Required: No  
`onReason`  
Contains a glob pattern to match against the `Reason` returned for a job\. The patten can be up to 512 characters long, can contain letters, numbers, periods \(\.\), colons \(:\), and whitespace \(spaces, tabs\), and can optionally end with an asterisk \(\*\) so that only the start of the string needs to be an exact match\.  
Type: String  
Required: No  
`onStatusReason`  
Contains a glob pattern to match against the `StatusReason` returned for a job\. The patten can be up to 512 characters long, can contain letters, numbers, periods \(\.\), colons \(:\), and whitespace \(spaces, tabs\)\. and can optionally end with an asterisk \(\*\) so that only the start of the string needs to be an exact match\.  
Type: String  
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

## Tags<a name="job-definition-parameters-tags"></a>

`tags`  
Key\-value pair tags to associate with the job definition\. For more information, see [Tagging your AWS Batch resources](using-tags.md)\.  
Type: String to string map  
Required: No