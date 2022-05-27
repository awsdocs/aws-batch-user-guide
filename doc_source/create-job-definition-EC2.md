# Creating a single\-node job definition on Amazon EC2 resources<a name="create-job-definition-EC2"></a>

**To create a new job definition on Amazon EC2 resources:**

1. Open the AWS Batch console at [AWS Batch console first\-run wizard](https://console.aws.amazon.com/batch/home#wizard)\.

1. From the navigation bar, choose the AWS Region to use\.

1. In the left navigation pane, choose **Job definitions**\.

1. Choose **Create**\.

1. For job type, choose **Single\-node**\.

1. For **Name**, enter a unique name for your job definition\. The name can be up to 128 characters in length\. It can contain uppercase and lowercase letters, numbers, hyphens \(\-\), and underscores \(\_\)\.

1. For **Execution timeout**, enter the timeout value \(in seconds\)\. The execution timeout is the length of time before an unfinished job is terminated\. If an attempt exceeds the timeout duration, it's stopped and the status moves to `FAILED`\. For more information, see [Job Timeouts](job_timeouts.md)\. The minimum value is 60 seconds\.

1. \(Optional\) Turn on **Scheduling priority**\. Enter a scheduling priority value between 0 and 100\. Higher values are given higher priority\.

1. \(Optional\) Expand **Additional tags configuration** and then choose **Add tag** to add tags to the resource\.

1. \(Optional\) Turn on **Propagate tags** to propagate tags from the job and job definition to the Amazon ECS task\.

1. Foror **Platform type**, choose **EC2**\.

1. For **Execution role**, specify an IAM role that grants the Amazon ECS container agents permission to make AWS API calls on your behalf\. This feature uses Amazon ECS IAM roles for task functionality\. For more information, see [Amazon ECS task execution IAM roles](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_execution_IAM_role.html) in the *Amazon Elastic Container Service Developer Guide*\. 

1. In the **Job Configuration** section:

   1. For **Image**, choose the Docker image to use for your job\. Images in the Docker Hub registry are available by default\. You can also specify other repositories with `repository-url/image:tag`\. The name can be up to 225 characters in length\. It can contain uppercase and lowercase letters, numbers, hyphens \(\-\), underscores \(\_\), colons \(:\), forward slashes \(/\), and number signs \(\#\)\. This parameter maps to `Image` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `IMAGE` parameter of [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\.
**Note**  
Docker image architecture must match the processor architecture of the compute resources that they're scheduled on\. For example, ARM\-based Docker images can only run on ARM\-based compute resources\.
      + Images in Amazon ECR Public repositories use the full `registry/repository[:tag]` or `registry/repository[@digest]` naming conventions\. For example, `public.ecr.aws/registry_alias/my-web-app:latest`\.
      + Images in Amazon ECR repositories use the full `registry/repository[:tag]` naming convention\. For example, `aws_account_id.dkr.ecr.region.amazonaws.com``/my-web-app:latest`\.
      + Images in official repositories on Docker Hub use a single name \(for example, `ubuntu` or `mongo`\)\.
      + Images in other repositories on Docker Hub are qualified with an organization name \(for example, `amazon/amazon-ecs-agent`\)\.
      + Images in other online repositories are qualified further by a domain name \(for example, `quay.io/assemblyline/ubuntu`\)\.

   1. For **Command syntax**, choose **Bash** or **JSON**\.

   1. For **Command**, choose **Bash** or **JSON** and then specify the command to pass to the container\. For simple commands, you can type the command as you do for a command prompt\. Then, verify that the JSON result is correct\. It's passed to the Docker daemon\. For more complicated commands \(for example, with special characters\), you can use **JSON** syntax\.
**Tip**  
Choose **Info** to view Bash and JSON code samples\.

      This parameter maps to `Cmd` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `COMMAND` parameter to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\. For more information about the Docker `CMD` parameter, see [https://docs\.docker\.com/engine/reference/builder/\#cmd](https://docs.docker.com/engine/reference/builder/#cmd)\.
**Note**  
You can use default values for parameter substitution and placeholders in your command\. For more information, see [Parameters](job_definition_parameters.md#parameters)\.

   1. For **vCPUs**, enter the number of vCPUs to reserve for the container\. This parameter maps to `CpuShares` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--cpu-shares` option to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\. Each vCPU is equivalent to 1,024 CPU shares\. You must specify at least one vCPU\.

   1. For **Memory**, enter the memory limit available to the container\. If your container attempts to exceed the memory specified here, the container is stopped\. This parameter maps to `Memory` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--memory` option to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\. You must specify at least 4 MiB of memory for a job\.
**Note**  
You can maximize your resource utilization by prioritizing memory for jobs of a specific instance type\. For instructions, see [Compute Resource Memory Management](memory-management.md)\.

   1. For **Number of GPUs**, choose the number of GPUs to reserve for the container\.

   1. \(Optional\) Turn on **Environment variables configuration** to add environment variables as name\-value pairs\. These variables are passed to the container\.

   1. \(Optional\) Turn on **Job Role configuration** to add IAM roles that have permissions to the AWS APIs\. This feature uses Amazon ECS IAM roles for task functionality\. For more information, see [IAM Roles for Tasks](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-iam-roles.html) in the *Amazon Elastic Container Service Developer Guide*\.
**Note**  
Only roles that have the **Amazon Elastic Container Service Task Role** trust relationship are shown here\. For more information about creating an IAM role for your AWS Batch jobs, see [Creating an IAM Role and Policy for your Tasks](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-iam-roles.html#create_task_iam_policy_and_role) in the *Amazon Elastic Container Service Developer Guide*\.

   1. \(Optional\) Turn on **Security configuration** to add user names for use in the container\.

      To give your job's container elevated permissions on the host instance \(similar to the `root` user\), drag the **Privileged** slider to the right\. This parameter maps to `Privileged` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--privileged` option to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\.

      For **User**, enter the user name to use inside the container\. This parameter maps to `User` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--user` option to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\.

   1. \(Optional\) Turn on **Secrets** to add secrets as a name\-value pair\. These secrets are exposed in the container\. For more information, see [secretOptions](job_definition_parameters.md#ContainerProperties-logConfiguration-secretOptions) in [Job definition parameters](job_definition_parameters.md)

   1. \(Optional\) Turn on **Enable read only filesystem** to remove write access to the volume\.

   1. \(Optional\) Turn on **Mount points configuration** to add mount points for data volumes\. You must specify the source volume and container path\. These mount points are passed to the Docker daemon on a container instance\.

   1. \(Optional\) Turn on **Ulimits configuration** slider to add a ulimits for the container\. Enter a name, soft limit, and hard limit, and then choose **Add ulimit**\.

   1. \(Optional\) Turn on **Volumes configuration** to create a list of volumes to pass to the container\. Enter **Name** and **Source path** for the volume and then choose **Add volume**\. 

   1. \(Optional\) Turn on **Linux parameter configuration** to add devices or turn on Linux specific settings such as **Init process enabled**:

      1. \(Optional\) Turn on **Init process enabled** to run an init process inside the container\. This process forwards signals and reaps processes\.

      1. \(Optional\) For **Devices**, choose **Add device**\.

         1. \(Optional\) For **Devices**, choose **Add device** to add a device

         1. \(Optional\) For **Container path**, specify the path of in the container instance to expose the device mapped to the host instance\. If this is left blank \(unspecified\), then the host path is used in the container\.

         1. \(Optional\) For **Host path**, specify the path of a device in the host instance\.

         1. \(Optional\) For **Permissions**, choose one or more permissions to apply to the device in the container\. The available permissions are `READ`, `WRITE`, and `MKNOD`\.

      1. \(Optional\) For **Shared memory size**, enter the size \(in MiB\) of the `/dev/shm` volume\.

      1. \(Optional\) I For **Max swap size**, enter the total amount of swap memory \(in MiB\) that the container can use\.

      1. \(Optional\) For **Swappiness** enter a value between 0 and 100 to indicate the swappiness behavior of the container\. If it's not specified and swapping is enabled, the default value is 60\. For more information, see [swappiness](job_definition_parameters.md#ContainerProperties-linuxParameters-swappiness) in [Job definition parameters](job_definition_parameters.md)\.

      1. \(Optional\) For **Tmpfs**, to add a `` mount, choose **Add tmpfs**\.

      1. \(Optional\) For **Container path**, enter the absolute file path in the container where the tmpfs volume is mounted\.

      1. \(Optional\) In the **Size** field, enter size \(in MiB\) of the tmpfs volume\.

      1. \(Optional\) For **Mount options**, enter the mount options\. For more information, including the list of available mount options, see [mountOptions](job_definition_parameters.md#ContainerProperties-linuxParameters-tmpfs-mountOptions) in [Job definition parameters](job_definition_parameters.md)\.

   1. \(Optional\) Turn on **Log configuration** to add a customer log driver to the container:
**Note**  
By default, the `awslogs` log driver is used\.

      1. For **Log driver**, choose the log driver to use\. For more information about the available log drivers, see [logDriver](job_definition_parameters.md#ContainerProperties-logConfiguration-logDriver) in [Job definition parameters](job_definition_parameters.md)\.

      1. \(Optional\) For **Options**, choose **Add option** to add an option\. Enter a name\-value pair, and then choose **Add option**\.

      1. \(Optional\) Turn on **Secrets**\. Enter a name\-value pair and then choose **Add** to add a secret\.
**Tip**  
For more information, see [secretOptions](job_definition_parameters.md#ContainerProperties-logConfiguration-secretOptions) in [Job definition parameters](job_definition_parameters.md)\.

1.  \(Optional\) You can add parameters to the job definition as name\-value mappings to override the job definition defaults\. To add a parameter:

   1. For **Parameters**, enter a name\-value pair, then choose **Add parameter**\.
**Important**  
If you choose **Add parameter**, you must configure at least one parameter or choose **Remove parameter**

1. \(Optional\) In the **Retry Strategies** section, configure the number of times a failed job is resubmitted\. You can also configure exit codes and status reasons to help troubleshoot a failed instance\. To configure a retry strategy:

   1. For **Job attempts**, enter the number of times to resubmit a failed job\.

   1. Choose **Add evaluate on exit**\. Enter at least one parameter value and then choose an **Action**\. For each set of conditions, **Action** must be set to either **Retry** \(to retry until the number of job attempts has been reached\) or **Exit** \(to stop retrying the job\)\.
**Important**  
If you choose **Add evaluate on exit**, you must configure at least one parameter and choose an **Action** or choose **Remove evaluate on exit**\.

1. Choose **Create**\.