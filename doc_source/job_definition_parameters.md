# Job definition parameters<a name="job_definition_parameters"></a>

Job definitions are split into several parts:
+ the job definition name
+ the type of the job definition
+ the parameter substitution placeholder defaults
+ the container properties for the job
+ the Amazon EKS properties for the job definition that are necessary for jobs run on Amazon EKS resources
+ the node properties that are necessary for a multi\-node parallel job
+ the platform capabilities that are necessary for jobs run on Fargate resources
+ the default tag propagation details of the job definition
+ the default retry strategy for the job definition
+ the default scheduling priority for the job definition
+ the default tags for the job definition
+ the default timeout for the job definition

**Contents**
+ [Job definition name](#jobDefinitionName)
+ [Type](#type)
+ [Parameters](#parameters)
+ [Container properties](#containerProperties)
+ [Amazon EKS properties](#job-definition-parameters-eks-properties)
+ [Platform capabilities](#job-definition-parameters-platform-capabilities)
+ [Propagate tags](#job-definition-parameters-propagate-tags)
+ [Node properties](#nodeProperties)
+ [Retry strategy](#retryStrategy)
+ [Scheduling priority](#job-definition-parameters-schedulingPriority)
+ [Tags](#job-definition-parameters-tags)
+ [Timeout](#timeout)

## Job definition name<a name="jobDefinitionName"></a>

`jobDefinitionName`  
When you register a job definition, you specify a name\. The name can be up to 128 characters in length\. It can contain uppercase and lowercase letters, numbers, hyphens \(\-\), and underscores \(\_\)\. The first job definition that's registered with that name is given a revision of 1\. Any subsequent job definitions that are registered with that name are given an incremental revision number\.   
Type: String  
Required: Yes

## Type<a name="type"></a>

`type`  
When you register a job definition, you specify the type of job\. If the job runs on Fargate resources, then `multinode` isn't supported\. For more information about multi\-node parallel jobs, see [Creating a multi\-node parallel job definition](create-multi-node-job-def.md)\.  
Type: String  
Valid values: `container` \| `multinode`  
Required: Yes

## Parameters<a name="parameters"></a>

`parameters`  
When you submit a job, you can specify parameters that replace the placeholders or override the default job definition parameters\. Parameters in job submission requests take precedence over the defaults in a job definition\. This means that you can use the same job definition for multiple jobs that use the same format\. You can also programmatically change values in the command at submission time\.  
Type: String to string map  
Required: No  
When you register a job definition, you can use parameter substitution placeholders in the `command` field of a job's container properties\. The syntax is as follows\.  

```
"command": [
    "ffmpeg",
    "-i",
    "Ref::inputfile",
    "-c",
    "Ref::codec",
    "-o",
    "Ref::outputfile"
]
```
In the above example, there are `Ref::inputfile`, `Ref::codec`, and `Ref::outputfile` parameter substitution placeholders in the command\. You can use the `parameters` object in the job definition to set default values for these placeholders\. For example, to set a default for the `Ref::codec` placeholder, you specify the following in the job definition:  

```
"parameters" : {"codec" : "mp4"}
```
When this job definition is submitted to run, the `Ref::codec` argument in the command for the container is replaced with the default value, `mp4`\.

## Container properties<a name="containerProperties"></a>

When you register a job definition, specify a list of container properties that are passed to the Docker daemon on a container instance when the job is placed\. The following container properties are allowed in a job definition\. For single\-node jobs, these container properties are set at the job definition level\. For multi\-node parallel jobs, container properties are set in the [Node properties](#nodeProperties) level, for each node group\.

`command`  
The command that's passed to the container\. This parameter maps to `Cmd` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `COMMAND` parameter to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\. For more information about the Docker `CMD` parameter, see [https://docs\.docker\.com/engine/reference/builder/\#cmd](https://docs.docker.com/engine/reference/builder/#cmd)\.  

```
"command": ["string", ...]
```
Type: String array  
Required: No

`environment`  
The environment variables to pass to a container\. This parameter maps to `Env` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--env` option to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\.  
We don't recommend that you use plaintext environment variables for sensitive information, such as credential data\.
Environment variables must not start with `AWS_BATCH`\. This naming convention is reserved for variables that are set by the AWS Batch service\.
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
When you register a job definition, you can specify an IAM role\. The role provides the Amazon ECS container agent with permissions to call the API actions that are specified in its associated policies on your behalf\. Jobs that run on Fargate resources must provide an execution role\. For more information, see [AWS Batch execution IAM role](execution-IAM-role.md)\.  
Type: String  
Required: No

`fargatePlatformConfiguration`  
The platform configuration for jobs that run on Fargate resources\. Jobs that run on EC2 resources must not specify this parameter\.  
Type: [FargatePlatformConfiguration](https://docs.aws.amazon.com/batch/latest/APIReference/API_FargatePlatformConfiguration.html) object  
Required: No    
`platformVersion`  
The AWS Fargate platform version use for the jobs, or `LATEST` to use a recent, approved version of the AWS Fargate platform\.  
Type: String  
Default: `LATEST`  
Required: No

`image`  
The image used to start a job\. This string is passed directly to the Docker daemon\. Images in the Docker Hub registry are available by default\. You can also specify other repositories with `repository-url/image:tag`\. Up to 255 letters \(uppercase and lowercase\), numbers, hyphens, underscores, colons, periods, forward slashes, and number signs are allowed\. This parameter maps to `Image` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `IMAGE` parameter of [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\.  
Docker image architecture must match the processor architecture of the compute resources that they're scheduled on\. For example, Arm based Docker images can only run on Arm based compute resources\.
+ Images in Amazon ECR Public repositories use the full `registry/repository[:tag]` or `registry/repository[@digest]` naming conventions \(for example, `public.ecr.aws/registry_alias/my-web-app:latest`\)\.
+ Images in Amazon ECR repositories use the full `registry/repository:[tag]` naming convention\. For example, `aws_account_id.dkr.ecr.region.amazonaws.com``/my-web-app:latest`\.
+ Images in official repositories on Docker Hub use a single name \(for example, `ubuntu` or `mongo`\)\.
+ Images in other repositories on Docker Hub are qualified with an organization name \(for example, `amazon/amazon-ecs-agent`\)\.
+ Images in other online repositories are qualified further by a domain name \(for example, `quay.io/assemblyline/ubuntu`\)\.
Type: String  
Required: Yes

`instanceType`  
The instance type to use for a multi\-node parallel job\. All node groups in a multi\-node parallel job must use the same instance type\. This parameter isn't valid for single\-node container jobs or for jobs that run on Fargate resources\.  
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
List of devices mapped into the container\. This parameter maps to `Devices` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--device` option to [docker run](https://docs.docker.com/engine/reference/run/)\.  
This parameter isn't applicable to jobs that run on Fargate resources\.
Type: Array of [Device](https://docs.aws.amazon.com/batch/latest/APIReference/API_Device.html) objects  
Required: No    
`hostPath`  
Path where the device available in the host container instance is\.  
Type: String  
Required: Yes  
`containerPath`  
Path where the device is exposed in the container is\. If this isn't specified, the device is exposed at the same path as the host path\.  
Type: String  
Required: No  
`permissions`  
Permissions for the device in the container\. If this isn't specified the permissions are set to `READ`, `WRITE`, and `MKNOD`\.  
Type: Array of strings  
Required: No  
Valid values: `READ` \| `WRITE` \| `MKNOD`  
`initProcessEnabled`  
If true, run an `init` process inside the container that forwards signals and reaps processes\. This parameter maps to the `--init` option to [docker run](https://docs.docker.com/engine/reference/run/)\. This parameter requires version 1\.25 of the Docker Remote API or greater on your container instance\. To check the Docker Remote API version on your container instance, log into your container instance and run the following command: `sudo docker version | grep "Server API version"`  
Type: Boolean  
Required: No  
`maxSwap`  
The total amount of swap memory \(in MiB\) a job can use\. This parameter is translated to the `--memory-swap` option to [docker run](https://docs.docker.com/engine/reference/run/) where the value is the sum of the container memory plus the `maxSwap` value\. For more information, see [`--memory-swap` details](https://docs.docker.com/config/containers/resource_constraints/#--memory-swap-details) in the Docker documentation\.  
If a `maxSwap` value of `0` is specified, the container doesn't use swap\. Accepted values are `0` or any positive integer\. If the `maxSwap` parameter is omitted, the container uses the swap configuration for the container instance that it runs on\. A `maxSwap` value must be set for the `swappiness` parameter to be used\.  
This parameter isn't applicable to jobs that run on Fargate resources\.
Type: Integer  
Required: No  
`sharedMemorySize`  
The value for the size \(in MiB\) of the `/dev/shm` volume\. This parameter maps to the `--shm-size` option to [docker run](https://docs.docker.com/engine/reference/run/)\.  
This parameter isn't applicable to jobs that run on Fargate resources\.
Type: Integer  
Required: No  
`swappiness`  
You can use this to tune a container's memory swappiness behavior\. A `swappiness` value of `0` causes swapping to not happen unless absolutely necessary\. A `swappiness` value of `100` causes pages to be swapped aggressively\. Accepted values are whole numbers between `0` and `100`\. If the `swappiness` parameter isn't specified, a default value of `60` is used\. If a value isn't specified for `maxSwap`, then this parameter is ignored\. If `maxSwap` is set to 0, the container doesn't use swap\. This parameter maps to the `--memory-swappiness` option to [docker run](https://docs.docker.com/engine/reference/run/)\.  
Consider the following when you use a per\-container swap configuration\.  
+ Swap space must be enabled and allocated on the container instance for the containers to use\.
**Note**  
The Amazon ECS optimized AMIs don't have swap enabled by default\. You must enable swap on the instance to use this feature\. For more information, see [Instance Store Swap Volumes](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-store-swap-volumes.html) in the *Amazon EC2 User Guide for Linux Instances* or [How do I allocate memory to work as swap space in an Amazon EC2 instance by using a swap file?](http://aws.amazon.com/premiumsupport/knowledge-center/ec2-memory-swap-file/)\.
+ The swap space parameters are only supported for job definitions using EC2 resources\.
+ If the `maxSwap` and `swappiness` parameters are omitted from a job definition, each container has a default `swappiness` value of 60\. The total swap usage is limited to two times the memory reservation of the container\.
This parameter isn't applicable to jobs that run on Fargate resources\.
Type: Integer  
Required: No  
`tmpfs`  
The container path, mount options, and size of the tmpfs mount\.  
Type: Array of [Tmpfs](https://docs.aws.amazon.com/batch/latest/APIReference/API_Tmpfs.html) objects  
This parameter isn't applicable to jobs that run on Fargate resources\.
Required: No    
`containerPath`  
The absolute file path in the container where the tmpfs volume is mounted\.  
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
This parameter maps to `LogConfig` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--log-driver` option to [docker run](https://docs.docker.com/engine/reference/run/)\. By default, containers use the same logging driver that the Docker daemon uses\. However, the container can use a different logging driver than the Docker daemon by specifying a log driver with this parameter in the container definition\. To use a different logging driver for a container, the log system must be either configured on the container instance or on another log server to provide remote logging options\. For more information about the options for different supported log drivers, see [Configure logging drivers](https://docs.docker.com/engine/admin/logging/overview/) in the Docker documentation\.  
AWS Batch currently supports a subset of the logging drivers available to the Docker daemon \(shown in the [LogConfiguration](https://docs.aws.amazon.com/batch/latest/APIReference/API_LogConfiguration.html) data type\)\.
This parameter requires version 1\.18 of the Docker Remote API or greater on your container instance\. To check the Docker Remote API version on your container instance, log into your container instance and run the following command: `sudo docker version | grep "Server API version"`   

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
The log driver to use for the job\. By default, AWS Batch enables the `awslogs` log driver\. The valid values that are listed for this parameter are log drivers that the Amazon ECS container agent can communicate with by default\.  
This parameter maps to `LogConfig` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--log-driver` option to [docker run](https://docs.docker.com/engine/reference/run/)\. By default, jobs use the same logging driver that the Docker daemon uses\. However, the job can use a different logging driver than the Docker daemon by specifying a log driver with this parameter in the job definition\. If you want to specify another logging driver for a job, the log system must be configured on the container instance in the compute environment\. Or, alternatively, configure it on another log server to provide remote logging options\. For more information about the options for different supported log drivers, see [Configure logging drivers](https://docs.docker.com/engine/admin/logging/overview/) in the Docker documentation\.  
AWS Batch currently supports a subset of the logging drivers that are available to the Docker daemon\. Additional log drivers might be available in future releases of the Amazon ECS container agent\.
The supported log drivers are `awslogs`, `fluentd`, `gelf`, `json-file`, `journald`, `logentries`, `syslog`, and `splunk`\.  
Jobs that run on Fargate resources are restricted to the `awslogs` and `splunk` log drivers\.
This parameter requires version 1\.18 of the Docker Remote API or greater on your container instance\. To check the Docker Remote API version on your container instance, log into your container instance and run the following command: `sudo docker version | grep "Server API version"`  
The Amazon ECS container agent that runs on a container instance must register the logging drivers that are available on that instance with the `ECS_AVAILABLE_LOGGING_DRIVERS` environment variable\. Otherwise, the containers placed on that instance can't use these log configuration options\. For more information, see [Amazon ECS Container Agent Configuration](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-agent-config.html) in the *Amazon Elastic Container Service Developer Guide*\.  
`awslogs`  
Specifies the Amazon CloudWatch Logs logging driver\. For more information, see [Using the awslogs log driver](using_awslogs.md) and [Amazon CloudWatch Logs logging driver](https://docs.docker.com/config/containers/logging/awslogs/) in the Docker documentation\.  
`fluentd`  
Specifies the Fluentd logging driver\. For more information including usage and options, see [Fluentd logging driver](https://docs.docker.com/config/containers/logging/fluentd/) in the Docker documentation\.  
`gelf`  
Specifies the Graylog Extended Format \(GELF\) logging driver\. For more information including usage and options, see [Graylog Extended Format logging driver](https://docs.docker.com/config/containers/logging/gelf/) in the Docker documentation\.  
`journald`  
Specifies the journald logging driver\. For more information including usage and options, see [Journald logging driver](https://docs.docker.com/config/containers/logging/journald/) in the Docker documentation\.  
`json-file`  
Specifies the JSON file logging driver\. For more information including usage and options, see [JSON File logging driver](https://docs.docker.com/config/containers/logging/json-file/) in the Docker documentation\.  
`splunk`  
Specifies the Splunk logging driver\. For more information including usage and options, see [Splunk logging driver](https://docs.docker.com/config/containers/logging/splunk/) in the Docker documentation\.  
`syslog`  
Specifies the syslog logging driver\. For more information including usage and options, see [Syslog logging driver](https://docs.docker.com/config/containers/logging/syslog/) in the Docker documentation\.
Type: String  
Required: Yes  
Valid values: `awslogs` \| `fluentd` \| `gelf` \| `journald` \| `json-file` \| `splunk` \| `syslog`  
If you have a custom driver that's not listed earlier that you would like to work with the Amazon ECS container agent, you can fork the Amazon ECS container agent project that's [available on GitHub](https://github.com/aws/amazon-ecs-agent) and customize it to work with that driver\. We encourage you to submit pull requests for changes that you want to have included\. However, Amazon Web Services doesn't currently support requests that run modified copies of this software\.  
`options`  
Log configuration options to send to a log driver for the job\.  
This parameter requires version 1\.19 of the Docker Remote API or greater on your container instance\.  
Type: String to string map  
Required: No  
`secretOptions`  
An object that represents the secret to pass to the log configuration\. For more information, see [Specifying sensitive data](specifying-sensitive-data.md)\.  
Type: object array  
Required: No    
`name`  
The name of the log driver option to set in the job\.  
Type: String  
Required: Yes  
`valueFrom`  
The Amazon Resource Name \(ARN\) of the secret to expose to the log configuration of the container\. The supported values are either the full ARN of the Secrets Manager secret or the full ARN of the parameter in the SSM Parameter Store\.  
If the SSM Parameter Store parameter exists in the same AWS Region as the task that you're launching, then you can use either the full ARN or name of the parameter\. If the parameter exists in a different Region, then the full ARN must be specified\.
Type: String  
Required: Yes

`memory`  
*This parameter is deprecated, use `resourceRequirements` instead\.*  
The number of MiB of memory reserved for the job\.  
As an example for how to use `resourceRequirements`, if your job definition contains syntax that's similar to the following\.  

```
"containerProperties": {
  "memory": 512
}
```
The equivalent syntax using `resourceRequirements` is as follows\.  

```
"containerProperties": {
  "resourceRequirements": [
    {
      "type": "MEMORY",
      "value": "512"
    }
  ]
}
```
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
The path on the container where to mount the host volume\.  
Type: String  
Required: Yes, when `mountPoints` is used\.  
`readOnly`  
If this value is `true`, the container has read\-only access to the volume\. If this value is `false`, then the container can write to the volume\.  
Type: Boolean  
Required: No  
Default: False

`networkConfiguration`  
The network configuration for jobs that run on Fargate resources\. Jobs that run on EC2 resources must not specify this parameter\.  

```
"networkConfiguration": { 
   "assignPublicIp": "string"
}
```
Type: Object array  
Required: No    
`assignPublicIp`  
Indicates whether the job has a public IP address\. This is required if the job needs outbound network access\.  
Type: String  
Valid values: `ENABLED` \| `DISABLED`  
Required: No  
Default: `DISABLED`

`privileged`  
When this parameter is true, the container is given elevated permissions on the host container instance \(similar to the `root` user\)\. This parameter maps to `Privileged` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--privileged` option to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\. This parameter isn't applicable to jobs that run on Fargate resources\. Don't provide it or specify it as false\.  

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
The type and amount of a resource to assign to a container\. The supported resources include `GPU`, `MEMORY`, and `VCPU`\.  

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
The type of resource to assign to a container\. The supported resources include `GPU`, `MEMORY`, and `VCPU`\.  
Type: String  
Required: Yes, when `resourceRequirements` is used\.  
`value`  
The quantity of the specified resource to reserve for the container\. The values vary based on the `type` specified\.    
type="GPU"  
The number of physical GPUs to reserve for the container\. The number of GPUs reserved for all containers in a job cannot exceed the number of available GPUs on the compute resource that the job is launched on\.  
type="MEMORY"  
The hard limit \(in MiB\) of memory to present to the container\. If your container attempts to exceed the memory specified here, the container is killed\. This parameter maps to `Memory` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--memory` option to [docker run](https://docs.docker.com/engine/reference/run/)\. You must specify at least 4 MiB of memory for a job\. This is required but can be specified in several places for multi\-node parallel \(MNP\) jobs\. It must be specified for each node at least once\. This parameter maps to `Memory` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--memory` option to [docker run](https://docs.docker.com/engine/reference/run/)\.  
If you're trying to maximize your resource utilization by providing your jobs as much memory as possible for a particular instance type, see [Compute Resource Memory Management](memory-management.md)\.
For jobs that run on Fargate resources, then `value` must match one of the supported values\. Moreover, the `VCPU` values must be one of the values that's supported for that memory value\.      
<a name="Fargate-memory-vcpu"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/batch/latest/userguide/job_definition_parameters.html)  
type="VCPU"  
The number of vCPUs reserved for the job\. This parameter maps to `CpuShares` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--cpu-shares` option to [docker run](https://docs.docker.com/engine/reference/run/)\. Each vCPU is equivalent to 1,024 CPU shares\. For jobs that run on EC2 resources, you must specify at least one vCPU\. This is required but can be specified in several places\. It must be specified for each node at least once\.  
For jobs that run on Fargate resources, `value` must match one of the supported values and the `MEMORY` values must be one of the values that's supported for that VCPU value\. The supported values are 0\.25, 0\.5, 1, 2, 4, 8, and 16\.  
The default for the Fargate On\-Demand vCPU resource count quota is 6 vCPUs\. For more information about Fargate quotas, see [AWS Fargate quotas ](https://docs.aws.amazon.com/general/latest/gr/ecs-service.html#service-quotas-fargate)in the *Amazon Web Services General Reference*\.
Type: String  
Required: Yes, when `resourceRequirements` is used\.

`secrets`  
The secrets for the job that are exposed as environment variables\. For more information, see [Specifying sensitive data](specifying-sensitive-data.md)\.  

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
The name of the environment variable that contains the secret\.  
Type: String  
Required: Yes, when `secrets` is used\.  
  
`valueFrom`  
The secret to expose to the container\. The supported values are either the full Amazon Resource Name \(ARN\) of the Secrets Manager secret or the full ARN of the parameter in the SSM Parameter Store\.  
If the SSM Parameter Store parameter exists in the same AWS Region as the job you're launching, then you can use either the full ARN or name of the parameter\. If the parameter exists in a different Region, then the full ARN must be specified\.
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
*This parameter is deprecated, use `resourceRequirements` instead\.*  
The number of vCPUs reserved for the container\.  
As an example for how to use `resourceRequirements`, if your job definition contains lines similar to this:  

```
"containerProperties": {
  "vcpus": 2
}
```
The equivalent lines using `resourceRequirements` is as follows\.  

```
"containerProperties": {
  "resourceRequirements": [
    {
      "type": "VCPU",
      "value": "2"
    }
  ]
}
```
Type: Integer  
Required: Yes

`volumes`  
When you register a job definition, you can specify a list of volumes that are passed to the Docker daemon on a container instance\. The following parameters are allowed in the container properties:  

```
"volumes": [
  {
    "name": "string",
    "host": {
      "sourcePath": "string"
    },
    "efsVolumeConfiguration": {
      "authorizationConfig": {
        "accessPointId": "string",
        "iam": "string"
      },
      "fileSystemId": "string",
      "rootDirectory": "string",
      "transitEncryption": "string",
      "transitEncryptionPort": number
    }
  }
]
```  
`name`  
The name of the volume\. Up to 255 letters \(uppercase and lowercase\), numbers, hyphens, and underscores are allowed\. This name is referenced in the `sourceVolume` parameter of container definition `mountPoints`\.  
Type: String  
Required: No  
`host`  
The contents of the `host` parameter determine whether your data volume persists on the host container instance and where it's stored\. If the `host` parameter is empty, then the Docker daemon assigns a host path for your data volume\. However, the data isn't guaranteed to persist after the container associated with it stops running\.  
This parameter isn't applicable to jobs that run on Fargate resources\.
Type: Object  
Required: No    
`sourcePath`  
The path on the host container instance that's presented to the container\. If this parameter is empty, then the Docker daemon assigns a host path for you\.  
If the `host` parameter contains a `sourcePath` file location, then the data volume persists at the specified location on the host container instance until you delete it manually\. If the `sourcePath` value doesn't exist on the host container instance, the Docker daemon creates it\. If the location does exist, the contents of the source path folder are exported\.  
Type: String  
Required: No  
`efsVolumeConfiguration`  
This parameter is specified when you're using an Amazon Elastic File System file system for task storage\. For more information, see [Amazon EFS volumes](efs-volumes.md)\.  
Type: Object  
Required: No    
`authorizationConfig`  
The authorization configuration details for the Amazon EFS file system\.  
Type: String  
Required: No    
`accessPointId`  
The Amazon EFS access point ID to use\. If an access point is specified, the root directory value that's specified in the `EFSVolumeConfiguration` must either be omitted or set to `/`\. This enforces the path that's set on the EFS access point\. If an access point is used, transit encryption must be enabled in the `EFSVolumeConfiguration`\. For more information, see [Working with Amazon EFS Access Points](https://docs.aws.amazon.com/efs/latest/ug/efs-access-points.html) in the *Amazon Elastic File System User Guide*\.  
Type: String  
Required: No  
`iam`  
Determines whether to use the AWS Batch job IAM role defined in a job definition when mounting the Amazon EFS file system\. If enabled, transit encryption must be enabled in the `EFSVolumeConfiguration`\. If this parameter is omitted, the default value of `DISABLED` is used\. For more information, see [Using Amazon EFS access points](efs-volumes.md#efs-volume-accesspoints)\.  
Type: String  
Valid values: `ENABLED` \| `DISABLED`  
Required: No  
`fileSystemId`  
The Amazon EFS file system ID to use\.  
Type: String  
Required: No  
`rootDirectory`  
The directory within the Amazon EFS file system to mount as the root directory inside the host\. If this parameter is omitted, the root of the Amazon EFS volume is used\. If you specify `/`, it has the same effect as omitting this parameter\. The maximum length is 4,096 characters\.  
If an EFS access point is specified in the `authorizationConfig`, the root directory parameter must either be omitted or set to `/`\. This enforces the path that's set on the Amazon EFS access point\.
Type: String  
Required: No  
`transitEncryption`  
Determines whether to enable encryption for Amazon EFS data in transit between the Amazon ECS host and the Amazon EFS server\. Transit encryption must be enabled if Amazon EFS IAM authorization is used\. If this parameter is omitted, the default value of `DISABLED` is used\. For more information, see [Encrypting data in transit](https://docs.aws.amazon.com/efs/latest/ug/encryption-in-transit.html) in the *Amazon Elastic File System User Guide*\.  
Type: String  
Valid values: `ENABLED` \| `DISABLED`  
Required: No  
`transitEncryptionPort`  
The port to use when sending encrypted data between the Amazon ECS host and the Amazon EFS server\. If you don't specify a transit encryption port, it uses the port selection strategy that the Amazon EFS mount helper uses\. The value must be between 0 and 65,535\. For more information, see [EFS Mount Helper](https://docs.aws.amazon.com/efs/latest/ug/efs-mount-helper.html) in the *Amazon Elastic File System User Guide*\.  
Type: Integer  
Required: No

## Amazon EKS properties<a name="job-definition-parameters-eks-properties"></a>

An object with various properties that are specific to Amazon EKS based jobs\. This must not be specified for Amazon ECS based job definitions\.

`podProperties`  
The properties for the Kubernetes pod resources of a job\.  
Type: [EksPodProperties](https://docs.aws.amazon.com/batch/latest/APIReference/API_EksPodProperties.html) object  
Required: No    
`containers`  
The properties of the container that's used on the Amazon EKS pod\.  
Type: [EksContainer](https://docs.aws.amazon.com/batch/latest/APIReference/API_EksContainer.html) object  
Required: No    
`args`  
An array of arguments to the entrypoint\. If this isn't specified, the `CMD` of the container image is used\. This corresponds to the `args` member in the [Entrypoint](https://kubernetes.io/docs/reference/kubernetes-api/workload-resources/pod-v1/#entrypoint) portion of the [Pod](https://kubernetes.io/docs/reference/kubernetes-api/workload-resources/pod-v1/) in Kubernetes\. Environment variable references are expanded using the container's environment\.  
If the referenced environment variable doesn't exist, the reference in the command isn't changed\. For example, if the reference is to "`$(NAME1)`" and the `NAME1` environment variable doesn't exist, the command string will remain "`$(NAME1)`\." `$$` is replaced with `$`, and the resulting string isn't expanded\. For example, `$$(VAR_NAME)` is passed as `$(VAR_NAME)` whether or not the `VAR_NAME` environment variable exists\. For more information, see [CMD](https://docs.docker.com/engine/reference/builder/#cmd) in the *Dockerfile reference* and [Define a command and arguments for a pod](https://kubernetes.io/docs/tasks/inject-data-application/define-command-argument-container/) in the *Kubernetes documentation*\.  
Type: Array of strings  
Required: No  
`command`  
The entrypoint for the container\. This isn't run within a shell\. If this isn't specified, the `ENTRYPOINT` of the container image is used\. Environment variable references are expanded using the container's environment\.  
If the referenced environment variable doesn't exist, the reference in the command isn't changed\. For example, if the reference is to "`$(NAME1)`" and the `NAME1` environment variable doesn't exist, the command string will remain "`$(NAME1)`\." `$$` is replaced with `$` and the resulting string isn't expanded\. For example, `$$(VAR_NAME)` will be passed as `$(VAR_NAME)` whether or not the `VAR_NAME` environment variable exists\. The entrypoint can't be updated\. For more information, see [ENTRYPOINT](https://docs.docker.com/engine/reference/builder/#entrypoint) in the *Dockerfile reference* and [Define a command and arguments for a container](https://kubernetes.io/docs/tasks/inject-data-application/define-command-argument-container/) and [Entrypoint](https://kubernetes.io/docs/reference/kubernetes-api/workload-resources/pod-v1/#entrypoint) in the *Kubernetes documentation*\.  
Type: Array of strings  
Required: No  
`env`  
The environment variables to pass to a container\.  
Environment variables cannot start with "`AWS_BATCH`"\. This naming convention is reserved for variables that AWS Batch sets\.
Type: Array of [EksContainerEnvironmentVariable](https://docs.aws.amazon.com/batch/latest/APIReference/API_EksContainerEnvironmentVariable.html) objects  
Required: No    
`name`  
The name of the environment variable\.  
Type: String  
Required: Yes  
`value`  
The value of the environment variable\.  
Type: String  
Required: No  
`image`  
The Docker image used to start the container\.  
Type: String  
Required: Yes  
`imagePullPolicy`  
The image pull policy for the container\. Supported values are `Always`, `IfNotPresent`, and `Never`\. This parameter defaults to `IfNotPresent`\. However, if the `:latest` tag is specified, it defaults to `Always`\. For more information, see [Updating images](https://kubernetes.io/docs/concepts/containers/images/#updating-images) in the *Kubernetes documentation*\.  
Type: String  
Required: No  
`name`  
The name of the container\. If the name isn't specified, the default name "`Default`" is used\. Each container in a pod must have a unique name\.  
Type: String  
Required: No  
`resources`  
The type and amount of resources to assign to a container\. The supported resources include `memory`, `cpu`, and `nvidia.com/gpu`\. For more information, see [Resource management for pods and containers](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/) in the *Kubernetes documentation*\.  
Type: [EksContainerResourceRequirements](https://docs.aws.amazon.com/batch/latest/APIReference/API_EksContainerResourceRequirements.html) object  
Required: No    
`limits`  
The type and quantity of the resources to reserve for the container\. The values vary based on the `name` that's specified\. Resources can be requested using either the `limits` or the `requests` objects\.    
memory  
The memory hard limit \(in MiB\) for the container, using whole integers, with a "Mi" suffix\. If your container attempts to exceed the memory specified, the container is terminated\. You must specify at least 4 MiB of memory for a job\. `memory` can be specified in `limits`, `requests`, or both\. If `memory` is specified in both places, then the value that's specified in `limits` must be equal to the value that's specified in `requests`\.  
To maximize your resource utilization, provide your jobs with as much memory as possible for the specific instance type that you are using\. To learn how, see [Compute Resource Memory Management](memory-management.md)\.  
cpu  
The number of CPUs that's reserved for the container\. Values must be an even multiple of `0.25`\. `cpu` can be specified in `limits`, `requests`, or both\. If `cpu` is specified in both places, then the value that's specified in `limits` must be at least as large as the value that's specified in `requests`\.  
nvidia\.com/gpu  
The number of GPUs that's reserved for the container\. Values must be a whole integer\. `memory` can be specified in `limits`, `requests`, or both\. If `memory` is specified in both places, then the value that's specified in `limits` must be equal to the value that's specified in `requests`\.
Type: String to string map  
Value Length Constraints: Minimum length of 1\. Maximum length of 256\.  
Required: No  
`requests`  
The type and quantity of the resources to request for the container\. The values vary based on the `name` that's specified\. Resources can be requested by using either the `limits` or the `requests` objects\.    
memory  
The memory hard limit \(in MiB\) for the container, using whole integers, with a "Mi" suffix\. If your container attempts to exceed the memory specified, the container is terminated\. You must specify at least 4 MiB of memory for a job\. `memory` can be specified in `limits`, `requests`, or both\. If `memory` is specified in both, then the value that's specified in `limits` must be equal to the value that's specified in `requests`\.  
If you're trying to maximize your resource utilization by providing your jobs as much memory as possible for a particular instance type, see [Compute Resource Memory Management](memory-management.md)\.  
cpu  
The number of CPUs that are reserved for the container\. Values must be an even multiple of `0.25`\. `cpu` can be specified in `limits`, `requests`, or both\. If `cpu` is specified in both, then the value that's specified in `limits` must be at least as large as the value that's specified in `requests`\.  
nvidia\.com/gpu  
The number of GPUs that are reserved for the container\. Values must be a whole integer\. `nvidia.com/gpu` can be specified in `limits`, `requests`, or both\. If `nvidia.com/gpu` is specified in both, then the value that's specified in `limits` must be equal to the value that's specified in `requests`\.
Type: String to string map  
Value Length Constraints: Minimum length of 1\. Maximum length of 256\.  
Required: No  
`securityContext`  
The security context for a job\. For more information, see [Configure a security context for a pod or container](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/) in the *Kubernetes documentation*\.  
Type: [EksContainerSecurityContext](https://docs.aws.amazon.com/batch/latest/APIReference/API_EksContainerSecurityContext.html) object  
Required: No    
`privileged`  
When this parameter is `true`, the container is given elevated permissions on the host container instance\. The level of permissions is similar to the `root` user permissions\. The default value is `false`\. This parameter maps to `privileged` policy in the [Privileged pod security policies](https://kubernetes.io/docs/concepts/security/pod-security-policy/#privileged) in the *Kubernetes documentation*\.  
Type: Boolean  
Required: No  
`readOnlyRootFilesystem`  
When this parameter is `true`, the container is given read\-only access to its root file system\. The default value is `false`\. This parameter maps to `ReadOnlyRootFilesystem` policy in the [Volumes and file systems pod security policies](https://kubernetes.io/docs/concepts/security/pod-security-policy/#volumes-and-file-systems) in the *Kubernetes documentation*\.  
Type: Boolean  
Required: No  
`runAsGroup`  
When this parameter is specified, the container is run as the specified group ID \(`gid`\)\. If this parameter isn't specified, the default is the group that's specified in the image metadata\. This parameter maps to `RunAsGroup` and `MustRunAs` policy in the [Users and groups pod security policies](https://kubernetes.io/docs/concepts/security/pod-security-policy/#users-and-groups) in the *Kubernetes documentation*\.  
Type: Long  
Required: No  
`runAsNonRoot`  
When this parameter is specified, the container is run as a user with a `uid` other than 0\. If this parameter isn't specified, so such rule is enforced\. This parameter maps to `RunAsUser` and `MustRunAsNonRoot` policy in the [Users and groups pod security policies](https://kubernetes.io/docs/concepts/security/pod-security-policy/#users-and-groups) in the *Kubernetes documentation*\.  
Type: Long  
Required: No  
`runAsUser`  
When this parameter is specified, the container is run as the specified user ID \(`uid`\)\. If this parameter isn't specified, the default is the user that's specified in the image metadata\. This parameter maps to `RunAsUser` and `MustRanAs` policy in the [Users and groups pod security policies](https://kubernetes.io/docs/concepts/security/pod-security-policy/#users-and-groups) in the *Kubernetes documentation*\.  
Type: Long  
Required: No  
`volumeMounts`  
The volume mounts for a container for an Amazon EKS job\. For more information about volumes and volume mounts in Kubernetes, see [Volumes](https://kubernetes.io/docs/concepts/storage/volumes/) in the *Kubernetes documentation*\.  
Type: Array of [EksContainerVolumeMount](https://docs.aws.amazon.com/batch/latest/APIReference/API_EksContainerVolumeMount.html) objects  
Required: No    
`mountPath`  
The path on the container where the volume is mounted\.  
Type: String  
Required: No  
`name`  
The name the volume mount\. This must match the name of one of the volumes in the pod\.  
Type: String  
Required: No  
`readOnly`  
If this value is `true`, the container has read\-only access to the volume\. Otherwise, the container can write to the volume\. The default value is `false`\.  
Type: Boolean  
Required: No  
`dnsPolicy`  
The DNS policy for the pod\. The default value is `ClusterFirst`\. If the `hostNetwork` parameter is not specified, the default is `ClusterFirstWithHostNet`\. `ClusterFirst` indicates that any DNS query that does not match the configured cluster domain suffix is forwarded to the upstream nameserver inherited from the node\. If no value was specified for `dnsPolicy` in the [RegisterJobDefinition](https://docs.aws.amazon.com/batch/latest/APIReference/API_RegisterJobDefinition.html) API operation, then no value is returned for `dnsPolicy` by either of [DescribeJobDefinitions](https://docs.aws.amazon.com/batch/latest/APIReference/API_DescribeJobDefinitions.html) or [DescribeJobs](https://docs.aws.amazon.com/batch/latest/APIReference/API_DescribeJobs.html) API operations\. The pod spec setting will contain either `ClusterFirst` or `ClusterFirstWithHostNet`, depending on the value of the `hostNetwork` parameter\. For more information, see [Pod's DNS policy](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) in the *Kubernetes documentation*\.  
Valid values: `Default` \| `ClusterFirst` \| `ClusterFirstWithHostNet`  
Type: String  
Required: No  
`hostNetwork`  
Indicates if the pod uses the hosts' network IP address\. The default value is `true`\. Setting this to `false` enables the Kubernetes pod networking model\. Most AWS Batch workloads are egress\-only and don't require the overhead of IP allocation for each pod for incoming connections\. For more information, see [Host namespaces](https://kubernetes.io/docs/concepts/security/pod-security-policy/#host-namespaces) and [Pod networking](https://kubernetes.io/docs/concepts/workloads/pods/#pod-networking) in the *Kubernetes documentation*\.  
Type: Boolean  
Required: No  
`serviceAccountName`  
The name of the service account that's used to run the pod\. For more information, see [Kubernetes service accounts](https://docs.aws.amazon.com/eks/latest/userguide/service-accounts.html) and [Configure a Kubernetes service account to assume an IAM role](https://docs.aws.amazon.com/eks/latest/userguide/associate-service-account-role.html) in the *Amazon EKS User Guide* and [Configure service accounts for pods](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/) in the *Kubernetes documentation*\.  
Type: String  
Required: No  
`volumes`  
Specifies the volumes for a job definition that uses Amazon EKS resources\.  
Type: Array of [EksVolume](https://docs.aws.amazon.com/batch/latest/APIReference/API_EksVolume.html) objects  
Required: No    
emptyDir  
Specifies the configuration of a Kubernetes `emptyDir` volume\. An `emptyDir` volume is first created when a pod is assigned to a node\. It exists as long as that pod runs on that node\. The `emptyDir` volume is initially empty\. All containers in the pod can read and write the files in the `emptyDir` volume\. However, the `emptyDir` volume can be mounted at the same or different paths in each container\. When a pod is removed from a node for any reason, the data in the `emptyDir` is deleted permanently\. For more information, see [emptyDir](https://kubernetes.io/docs/concepts/storage/volumes/#emptydir) in the *Kubernetes documentation*\.  
Type: [EksEmptyDir](https://docs.aws.amazon.com/batch/latest/APIReference/API_EksEmptyDir.html) object  
Required: No    
medium  
The medium to store the volume\. The default value is an empty string, which uses the storage of the node\.    
""  
**\(Default\)** Use the disk storage of the node\.  
"Memory"  
Use the `tmpfs` volume that's backed by the RAM of the node\. Contents of the volume are lost when the node reboots, and any storage on the volume counts against the container's memory limit\.
Type: String  
Required: No  
sizeLimit  
The maximum size of the volume\. By default, there's no maximum size defined\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 256\.  
Required: No  
hostPath  
Specifies the configuration of a Kubernetes `hostPath` volume\. A `hostPath` volume mounts an existing file or directory from the host node's filesystem into your pod\. For more information, see [hostPath](https://kubernetes.io/docs/concepts/storage/volumes/#hostpath) in the *Kubernetes documentation*\.  
Type: [EksHostPath](https://docs.aws.amazon.com/batch/latest/APIReference/API_EksHostPath.html) object  
Required: No    
path  
The path of the file or directory on the host to mount into containers on the pod\.  
Type: String  
Required: No  
name  
The name of the volume\. The name must be allowed as a DNS subdomain name\. For more information, see [DNS subdomain names](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-subdomain-names) in the *Kubernetes documentation*\.  
Type: String  
Required: Yes  
secret  
Specifies the configuration of a Kubernetes `secret` volume\. For more information, see [secret](https://kubernetes.io/docs/concepts/storage/volumes/#secret) in the *Kubernetes documentation*\.  
Type: [EksSecret](https://docs.aws.amazon.com/batch/latest/APIReference/API_EksSecret.html) object  
Required: No    
optional  
Specifies whether the secret or the secret's keys must be defined\.  
Type: Boolean  
Required: No  
secretName  
The name of the secret\. The name must be allowed as a DNS subdomain name\. For more information, see [DNS subdomain names](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-subdomain-names) in the *Kubernetes documentation*\.  
Type: String  
Required: Yes

## Platform capabilities<a name="job-definition-parameters-platform-capabilities"></a>

`platformCapabilities`  
The platform capabilities that's required by the job definition\. If no value is specified, it defaults to `EC2`\. For jobs that run on Fargate resources, `FARGATE` is specified\.  
If the job runs on Amazon EKS resources, then you must not specify `platformCapabilities`\.
Type: String  
Valid values: `EC2` \| `FARGATE`  
Required: No

## Propagate tags<a name="job-definition-parameters-propagate-tags"></a>

`propagateTags`  
Specifies whether to propagate the tags from the job or job definition to the corresponding Amazon ECS task\. If no value is specified, the tags aren't propagated\. Tags can only be propagated to the tasks when the task is created\. For tags with the same name, job tags are given priority over job definitions tags\. If the total number of combined tags from the job and job definition is over 50, the job's moved to the `FAILED` state\.  
If the job runs on Amazon EKS resources, then you must not specify `propagateTags`\.
Type: Boolean  
Required: No

## Node properties<a name="nodeProperties"></a>

`nodeProperties`  
When you register a multi\-node parallel job definition, you must specify a list of node properties\. These node properties define the number of nodes to use in your job, the main node index, and the different node ranges to use\. If the job runs on Fargate resources, then you can't specify `nodeProperties`\. Instead, use `containerProperties`\. The following node properties are allowed in a job definition\. For more information, see [Multi\-node parallel jobs](multi-node-parallel-jobs.md)\.  
If the job runs on Amazon EKS resources, then you must not specify `nodeProperties`\.
Type: [NodeProperties](https://docs.aws.amazon.com/batch/latest/APIReference/API_NodeProperties.html) object  
Required: No    
`mainNode`  
Specifies the node index for the main node of a multi\-node parallel job\. This node index value must be smaller than the number of nodes\.  
Type: Integer  
Required: Yes  
`numNodes`  
The number of nodes that are associated with a multi\-node parallel job\.  
Type: Integer  
Required: Yes  
`nodeRangeProperties`  
A list of node ranges and their properties that are associated with a multi\-node parallel job\.  
Type: Array of [NodeRangeProperty](https://docs.aws.amazon.com/batch/latest/APIReference/API_NodeRangeProperty.html) objects  
Required: Yes    
`targetNodes`  
The range of nodes, using node index values\. A range of `0:3` indicates nodes with index values of `0` through `3`\. If the starting range value is omitted \(`:n`\), then 0 is used to start the range\. If the ending range value is omitted \(`n:`\), then the highest possible node index is used to end the range\. Your accumulative node ranges must account for all nodes \(`0:n`\)\. You can nest node ranges, for example `0:10` and `4:5`\. For this case, the `4:5` range properties override the `0:10` properties\.   
Type: String  
Required: No  
`container`  
The container details for the node range\. For more information, see [Container properties](#containerProperties)\.  
Type: [ContainerProperties](https://docs.aws.amazon.com/batch/latest/APIReference/API_ContainerProperties.html) object   
Required: No

## Retry strategy<a name="retryStrategy"></a>

`retryStrategy`  
When you register a job definition, you can optionally specify a retry strategy to use for failed jobs that are submitted with this job definition\. Any retry strategy that's specified during a [SubmitJob](https://docs.aws.amazon.com/batch/latest/APIReference/API_SubmitJob.html) operation overrides the retry strategy defined here\. By default, each job is attempted one time\. If you specify more than one attempt, the job is retried if it fails\. Examples of a fail attempt include the job returns a non\-zero exit code or the container instance is terminated\. For more information, see [Automated job retries](job_retries.md)\.  
Type: [RetryStrategy](https://docs.aws.amazon.com/batch/latest/APIReference/API_RetryStrategy.html) object  
Required: No    
`attempts`  
The number of times to move a job to the `RUNNABLE` status\. You can specify between 1 and 10 attempts\. If `attempts` is greater than one, the job is retried that many times if it fails, until it has moved to `RUNNABLE`\.  

```
"attempts": integer
```
Type: Integer  
Required: No  
`evaluateOnExit`  
Array of up to 5 objects that specify conditions under which the job is retried or failed\. If this parameter is specified, then the `attempts` parameter must also be specified\. If `evaluateOnExit` is specified but none of the entries match, then the job is retried\.  

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
Specifies the action to take if all of the specified conditions \(`onStatusReason`, `onReason`, and `onExitCode`\) are met\. The values aren't case sensitive\.  
Type: String  
Required: Yes  
Valid values: `RETRY` \| `EXIT`  
`onExitCode`  
Contains a glob pattern to match against the decimal representation of the `ExitCode` that's returned for a job\. The pattern can be up to 512 characters in length\. It can contain only numbers\. It cannot contain letters or special characters\. It can optionally end with an asterisk \(\*\) so that only the start of the string needs to be an exact match\.  
Type: String  
Required: No  
`onReason`  
Contains a glob pattern to match against the `Reason` that's returned for a job\. The pattern can be up to 512 characters in length\. It can contain letters, numbers, periods \(\.\), colons \(:\), and white space \(spaces, tabs\)\. It can optionally end with an asterisk \(\*\) so that only the start of the string needs to be an exact match\.  
Type: String  
Required: No  
`onStatusReason`  
Contains a glob pattern to match against the `StatusReason` that's returned for a job\. The pattern can be up to 512 characters in length\. It can contain letters, numbers, periods \(\.\), colons \(:\), and white space \(spaces, tabs\)\. It can optionally end with an asterisk \(\*\) so that only the start of the string needs to be an exact match\.  
Type: String  
Required: No

## Scheduling priority<a name="job-definition-parameters-schedulingPriority"></a>

`schedulingPriority`  
The scheduling priority for jobs that are submitted with this job definition\. This only affects jobs in job queues with a fair share policy\. Jobs with a higher scheduling priority are scheduled before jobs with a lower scheduling priority\.  
The minimum supported value is 0 and the maximum supported value is 9999\.  
Type: Integer  
Required: No

## Tags<a name="job-definition-parameters-tags"></a>

`tags`  
Key\-value pair tags to associate with the job definition\. For more information, see [Tagging your AWS Batch resources](using-tags.md)\.  
Type: String to string map  
Required: No

## Timeout<a name="timeout"></a>

`timeout`  
You can configure a timeout duration for your jobs so that if a job runs longer than that, AWS Batch terminates the job\. For more information, see [Job timeouts](job_timeouts.md)\. If a job is terminated because of a timeout, it isn't retried\. Any timeout configuration that's specified during a [SubmitJob](https://docs.aws.amazon.com/batch/latest/APIReference/API_SubmitJob.html) operation overrides the timeout configuration defined here\. For more information, see [Job timeouts](job_timeouts.md)\.  
Type: [JobTimeout](https://docs.aws.amazon.com/batch/latest/APIReference/API_JobTimeout.html) object  
Required: No    
`attemptDurationSeconds`  
The time duration in seconds \(measured from the job attempt's `startedAt` timestamp\) after AWS Batch terminates unfinished jobs\. The minimum value for the timeout is 60 seconds\.  
For array jobs, the timeout applies to the child jobs, not to the parent array job\.  
For multi\-node parallel \(MNP\) jobs, the timeout applies to the whole job, not to the individual nodes\.  
Type: Integer  
Required: No