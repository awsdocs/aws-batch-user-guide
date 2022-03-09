# Creating a job definition<a name="create-job-definition"></a>

Before you can run jobs in AWS Batch, you must create a job definition\. This process varies slightly for single\-node and multi\-node parallel jobs\. This topic covers creating a job definition for an AWS Batch job that's not a multi\-node parallel job\.

To create a multi\-node parallel job definition, see [Creating a multi\-node parallel job definition](multi-node-job-def.md)\. For more information about multi\-node parallel jobs, see [Multi\-node Parallel Jobs](multi-node-parallel-jobs.md)\.

**To create a new job definition**

1. Open the AWS Batch console at [https://console\.aws\.amazon\.com/batch/](https://console.aws.amazon.com/batch/)\.

1. From the navigation bar, select the Region to use\.

1. In the navigation pane, choose **Job definitions**, **Create**\.

1. For **Name**, enter a unique name for your job definition\. The name can be up to 128 characters in length\. It can contain uppercase and lowercase letters, numbers, hyphens \(\-\), and underscores \(\_\)\.

1. For **Platform**, choose **EC2** if the job runs on EC2 instances, or **Fargate** if the job runs on AWS Fargate capacity\. For more information, see [AWS Batch on AWS Fargate](fargate.md)\.

1. In the **Retry Strategies** section, you can specify the number of times to retry a job\. You can also create conditions to decide whether a failed job should be retried\. These conditions are based on string matching of the error code and reasons that are listed for the job attempt\. For more information, see [Automated Job Retries](job_retries.md)\.

   1. For **Job attempts**, specify the number of times to attempt your job if it fails\. This number must be between one \(1\) and ten \(10\), inclusive\.

   1. \(Optional\) Select **Add evaluate on exit** to add up to five \(5\) conditions to match string patterns with the exit code, status reason, and reason that are returned in the job attempt\. For each set of conditions, **Action** must be set to either **Retry** \(to retry until the number of job attempts has been reached\), or **Exit** to stop retrying the job\.

1. \(Optional\) For **Execution timeout**, specify the maximum number of seconds that you want to allow your job attempts to run\. If an attempt exceeds the timeout duration, it's stopped and the status moves to `FAILED`\. For more information, see [Job Timeouts](job_timeouts.md)\.

1. For **Multi\-node parallel**, leave this box unchecked\. To create a multi\-node parallel job definition instead, see [Creating a multi\-node parallel job definition](multi-node-job-def.md)\. 

1. In **Container properties**, you can specify properties that are passed to the Docker daemon when the job is placed\.

   1. For **Image**, choose the Docker image to use for your job\. Images in the Docker Hub registry are available by default\. You can also specify other repositories with `repository-url/image:tag`\. Up to 255 letters \(uppercase and lowercase\), numbers, hyphens, underscores, colons, periods, forward slashes, and number signs are allowed\. This parameter maps to `Image` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `IMAGE` parameter of [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\.
**Note**  
Docker image architecture must match the processor architecture of the compute resources that they're scheduled on\. For example, ARM\-based Docker images can only run on ARM\-based compute resources\.
      + Images in Amazon ECR Public repositories use the full `registry/repository[:tag]` or `registry/repository[@digest]` naming conventions\. For example, `public.ecr.aws/registry_alias/my-web-app:latest`\.
      + Images in Amazon ECR repositories use the full `registry/repository[:tag]` naming convention\. For example, `aws_account_id.dkr.ecr.region.amazonaws.com``/my-web-app:latest`\.
      + Images in official repositories on Docker Hub use a single name \(for example, `ubuntu` or `mongo`\)\.
      + Images in other repositories on Docker Hub are qualified with an organization name \(for example, `amazon/amazon-ecs-agent`\)\.
      + Images in other online repositories are qualified further by a domain name \(for example, `quay.io/assemblyline/ubuntu`\)\.

   1. For **Command**, specify the command to pass to the container\. For simple commands, you can type the command as you would at a command prompt in the **Space delimited** tab\. Then, verify that the JSON result \(which is passed to the Docker daemon\) is correct\. For more complicated commands \(for example, with special characters\), you can switch to the **JSON** tab and enter the string array equivalent there\.

      This parameter maps to `Cmd` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `COMMAND` parameter to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\. For more information about the Docker `CMD` parameter, go to [https://docs\.docker\.com/engine/reference/builder/\#cmd](https://docs.docker.com/engine/reference/builder/#cmd)\.
**Note**  
You can use default values for parameter substitution as well as placeholders in your command\. For more information, see [Parameters](job_definition_parameters.md#parameters)\.

   1. For **vCPUs**, specify the number of vCPUs to reserve for the container\. This parameter maps to `CpuShares` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--cpu-shares` option to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\. Each vCPU is equivalent to 1,024 CPU shares\. You must specify at least one vCPU\.

   1. For **Memory**, specify the hard limit \(in MiB\) of memory to present to the job's container\. If your container attempts to exceed the memory specified here, the container is killed\. This parameter maps to `Memory` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--memory` option to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\. You must specify at least 4 MiB of memory for a job\.
**Note**  
You can maximize your resource utilization by prioritizing memory for jobs of a specific instance type\. For instructions, see [Compute Resource Memory Management](memory-management.md)\.

   1. \(Optional\) For **Number of GPUs**, specify the number of GPUs your job uses\.

      The job runs on a container with the specified number of GPUs pinned to that container\.

   1. In the **Additional configuration** section, you can specify additional parameters to be used with the container\.

      1. \(Optional\) For **Job role**, you can specify an IAM role that provides the container in your job with permissions to use the AWS APIs\. This feature uses Amazon ECS IAM roles for task functionality\. For more information, including configuration prerequisites, see [IAM Roles for Tasks](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-iam-roles.html) in the *Amazon Elastic Container Service Developer Guide*\.
**Note**  
A job role is required for jobs that are running on Fargate resources\.
**Note**  
Only roles that have the **Amazon Elastic Container Service Task Role** trust relationship are shown here\. For more information about creating an IAM role for your AWS Batch jobs, see [Creating an IAM Role and Policy for your Tasks](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-iam-roles.html#create_task_iam_policy_and_role) in the *Amazon Elastic Container Service Developer Guide*\.

      1. For **Execution role**, you can specify an IAM role that grants the Amazon ECS container and Fargate agents permission to make AWS API calls on your behalf\. This feature uses Amazon ECS IAM roles for task functionality\. For more information, including configuration prerequisites, see [Amazon ECS task execution IAM roles](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_execution_IAM_role.html) in the *Amazon Elastic Container Service Developer Guide*\.
**Note**  
An execution role is required for jobs running on Fargate resources\.

      1. \(Optional, only for jobs running on Fargate resources\) In the **Assign public IP** section, select **Enable** to give the job a public IP address\. For a job that's running in a private subnet to send outbound traffic to the internet, the private subnet requires a NAT gateway be attached to route requests to the internet\. You might want to do this so that you can pull container images\. For more information, see [Amazon ECS task networking](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-networking.html) in the *Amazon Elastic Container Service Developer Guide*\.

      1. \(Optional\) In the **Mount points** section, you can configure mount points for your job's container to access\.

         1. For **Container path**, enter the path on the container at which to mount the host volume\.

         1. For **Source volume**, enter the name of the volume to mount\.

         1. To make the volume read\-only for the container, choose **Read\-only**\.

      1. \(Optional, only for jobs running on EC2 resources\) In the **Ulimits** section, you can configure any `ulimit` values to use for your job's container\.

         1. Choose **Add limit**\.

         1. For **Limit name**, choose a ulimit to apply\.

         1. For **Soft limit**, choose the soft limit to apply for the ulimit type\.

         1. For **Hard limit**, choose the hard limit to apply for the ulimit type\.

      1. \(Optional\) In the **Environment variables** section, you can specify environment variables to pass to your job's container\. This parameter maps to `Env` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--env` option to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\.
**Important**  
We don't recommend that you use plaintext environment variables for sensitive information, such as credential data\.

         1. Choose **Add environment variable**\.

         1. For **Key**, specify the key for your environment variable\.
**Note**  
Environment variables must not start with `AWS_BATCH`\. This naming convention is reserved for variables that are set by the AWS Batch service\.

         1. For **Value**, specify the value for your environment variable\.

      1. \(Optional\) In the **Volumes** section, you can specify data volumes for your job to pass to your job's container\. To add a volume, select **Add volume**\.

         1. For **Name**, enter a name for your volume\. The name can be up to 255 characters in length\. It can contain uppercase and lowercase letters, numbers, hyphens \(\-\), and underscores \(\_\)\.

         1. \(Optional\) To use an Amazon EFS file system, select **Enable EFS**

            1. For **Filesystem ID**, enter the file system ID\.

            1. \(Optional\) For **Root directory**, enter the directory within the Amazon EFS file system to mount as the root directory inside the host\. If this parameter is omitted, the root of the Amazon EFS volume is used\. Specifying `/` has the same effect as omitting this parameter\.

         1. \(Optional, only for jobs running on EC2 resources\) For **Source Path**, enter the path on the host instance to present to the container\. If you leave this field empty, then the Docker daemon assigns a host path for you\. If you specify a source path, then the data volume persists at the specified location on the host container instance until you delete it\. If the source path doesn't exist on the host container instance, the Docker daemon creates it\. If the location does exist, the contents of the source path folder are exported to the container\.

         1. \(Optional\) To use transit encryption, select **Enable transit encryption**\. Transit encryption enables encryption for Amazon EFS data in transit between the AWS Batch host and the Amazon EFS server\. Transit encryption must be enabled if Amazon EFS IAM authorization is used\. For more information, see [Encrypting data in transit](https://docs.aws.amazon.com/efs/latest/ug/encryption-in-transit.html) in the *Amazon Elastic File System User Guide*\.

            1. \(Optional\) For **Transit encryption port**, enter the port to use when sending encrypted data between the AWS Batch host and the Amazon EFS server\. If you don't specify a transit encryption port, it uses the port selection strategy that the Amazon EFS mount helper uses\. The value must be between 0 and 65,535\. For more information, see [EFS Mount Helper](https://docs.aws.amazon.com/efs/latest/ug/efs-mount-helper.html) in the *Amazon Elastic File System User Guide*\.

            1. \(Optional\) For **Access point ID**, enter the access point ID to use\. If an access point is specified, the root directory value must either be omitted or set to `/`\. For more information, see [Working with Amazon EFS Access Points](https://docs.aws.amazon.com/efs/latest/ug/efs-access-points.html) in the *Amazon Elastic File System User Guide*\.

            1. \(Optional\) To use the execution role when mounting the Amazon EFS file system, select **Use selected job role**\. For more information, see [AWS Batch execution IAM role](execution-IAM-role.md)\.

      1. \(Optional\) In the **Security** section, you can configure security options for your job's container\.

         1. To give your job's container elevated permissions on the host instance \(similar to the `root` user\), select **Enable privileged mode**\. This parameter maps to `Privileged` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--privileged` option to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\.

         1. For **User**, enter the user name to use inside the container\. This parameter maps to `User` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--user` option to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\.

      1. \(Optional\) In the **Linux Parameters** section, you can configure any device mappings to use for your job's container\. This allows the container to be able to access a device on the host instance\.

         1. \(Optional\) In the **Devices** section, choose **Add device**\.

            1. \(Optional\) In the **Devices** section, to add a device choose **Add device**\.

            1. For **Host path**, specify the path of a device in the host instance\.

            1. For **Container path**, specify the path of in the container instance to expose the device mapped to the host instance\. If this is left blank \(unspecified\), then the host path is used in the container\.

            1. For **Permissions**, choose one or more permissions to apply to the device in the container\. The available permissions are `READ`, `WRITE`, and `MKNOD`\.

         1. \(Optional\) In the **Shared memory size** section, enter the size \(in MiB\) of the `/dev/shm` volume\.

         1. \(Optional\) In the **Max swap size** section, enter the total amount of swap memory \(in MiB\) that the container can use\.

         1. \(Optional\) In the **Swappiness** section, enter a value between 0 and 100 to indicate the swappiness behavior of the container\. If it's not specified and swapping is enabled, the default value is 60\. For more information, see [swappiness](job_definition_parameters.md#ContainerProperties-linuxParameters-swappiness) in [Job definition parameters](job_definition_parameters.md)\.

         1. \(Optional\) In the **Tmpfs** section, to add a tmpfs mount, choose **Add tmpfs**\.

            1. In the **Container path** field, enter the absolute file path in the container where the tmpfs volume is mounted\.

            1. In the **Size** field, enter size \(in MiB\) of the tmpfs volume\.

            1. \(Optional\) In the **Mount options** field, enter the mount options\. For more information, including the list of available mount options, see [mountOptions](job_definition_parameters.md#ContainerProperties-linuxParameters-tmpfs-mountOptions) in [Job definition parameters](job_definition_parameters.md)\.

      1. \(Optional\) In the **Log configuration** section, you can configure the log driver to use for your job's container\. By default, the `awslogs` log driver is used\.

         1. In the **Log driver** section, select the log driver to use\. For more information about the available log drivers, see [logDriver](job_definition_parameters.md#ContainerProperties-logConfiguration-logDriver) in [Job definition parameters](job_definition_parameters.md)\.

         1. \(Optional\) In the **Options** section, select **Add option** to add an option\.

            1. In the **Name** field, enter the name of the option\. The options available vary by log driver\. For more information, see the log driver documentation\.

            1. In the **Value** field, enter the value of the option\.

         1. \(Optional\) In the **Secrets** section, select **Add secret** to add a secret\.

            1. In the **Name** field, enter the name of the secret\. For more information, see [secretOptions](job_definition_parameters.md#ContainerProperties-logConfiguration-secretOptions) in [Job definition parameters](job_definition_parameters.md)\.

            1. In the **Value** field, enter the ARN of the secret\.

1. \(Optional\) In the **Parameters** section, you can specify parameter substitution default values and placeholders to use in the command that your job's container runs when it starts\. For more information, see [Parameters](job_definition_parameters.md#parameters)\.

   1. Choose **Add parameter**\.

   1. For **Key**, specify the key for your parameter\.

   1. For **Value**, specify the value for your parameter\.

1. \(Optional\) In the **Tags** section, you can specify the key and value for each tag to associate with the job definition\. For more information, see [Tagging your AWS Batch resources](using-tags.md)\.

1. Choose **Create job definition**\.