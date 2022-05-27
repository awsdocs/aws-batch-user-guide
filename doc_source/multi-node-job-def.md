# Creating a multi\-node parallel job definition<a name="multi-node-job-def"></a>

Before you can run jobs in AWS Batch, you must create a job definition\. This process varies slightly for single\-node and multi\-node parallel jobs\. This topic covers creating a job definition for an AWS Batch multi\-node parallel job\. For more information, see [Multi\-node Parallel Jobs](multi-node-parallel-jobs.md)\.

**Note**  
AWS Fargate doesn't support multi\-node parallel jobs\.

To create a single\-node job definition, see [Creating a single\-node job definition ](create-job-definition.md)\.

**To create a multi\-node parallel job definition**

1. Open the AWS Batch console at [https://console\.aws\.amazon\.com/batch/](https://console.aws.amazon.com/batch/)\.

1. From the navigation bar, select the Region to use\.

1. In the navigation pane, choose **Job definitions**, **Create**\.

1. For **Name**, enter a unique name for your job definition\. Up to 128 letters \(uppercase and lowercase\), numbers, hyphens, and underscores are allowed\.

1. For **Platform**, choose **EC2**\.

1. In the **Retry Strategies** section, you can specify the number of times to retry a job\. You can also create conditions to decide whether a failed job should be retried\. This is based on string matching of the error code and reasons listed for the job attempt\. For more information, see [Automated Job Retries](job_retries.md)\.

   1. For **Job attempts**, specify the number of times to attempt your job \(in case it fails\)\. This number must be between one \(1\) and ten \(10\), inclusive\.

   1. \(Optional\) Select **Add evaluate on exit** to add up to five \(5\) conditions to match string patterns with the exit code, status reason, and reason that's returned in the job attempt\. For each set of conditions, **Action** must be set to either **Retry** \(to retry until the number of job attempts has been reached\), or **Exit** to stop retrying the job\.

1. \(Optional\) For **Execution timeout**, specify the maximum number of seconds you would like to allow your job attempts to run\. If an attempt exceeds the timeout duration, it is stopped and the status moves to `FAILED`\. For more information, see [Job Timeouts](job_timeouts.md)\.

1. For **Multi\-node parallel**, select **Enable multi\-node parallel** and then complete the following substeps\. To create a single node parallel job definition instead, see [Creating a single\-node job definition ](create-job-definition.md)\.

   1. <a name="num-node-step"></a>For **Number of nodes**, enter the total number of nodes to use for your job\.

   1. For **Main node**, enter the node index to use for the main node\. The default main node index is `0`\.

   1. Select **Add node range**\. This creates a **Node range** section\.

      1. <a name="target-node-step"></a>For **Target nodes**, specify the range for your node group, using `range_start:range_end` notation\.

         You can create up to five node ranges for the number of nodes you specified for your job\. Node ranges use the index value for a node, and the node index begins at 0\. The range end index value of your final node group should be the number of nodes you specified in [Step 1](#num-node-step), minus one\. For example, if you specified 10 nodes, and you want to use a single node group, then your end range should be 9\.

      1. In **Container properties**, you can specify properties that are passed to the Docker daemon for the nodes in the node range\.

         1. For **Image**, choose the Docker image to use for your job\. Images in the Docker Hub registry are available by default\. You can also specify other repositories with `repository-url/image:tag`\. Up to 255 letters \(uppercase and lowercase\), numbers, hyphens, underscores, colons, periods, forward slashes, and number signs are allowed\. This parameter maps to `Image` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `IMAGE` parameter of [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\.
**Note**  
Docker image architecture must match the processor architecture of the compute resources that they're scheduled on\. For example, ARM\-based Docker images can only run on ARM\-based compute resources\.
            + Images in Amazon ECR Public repositories use the full `registry/repository[:tag]` or `registry/repository[@digest]` naming conventions \(for example, `public.ecr.aws/registry_alias/my-web-app:latest`\)\.
            + Images in Amazon ECR repositories use the full `registry/repository[:tag]` naming convention\. For example, `aws_account_id.dkr.ecr.region.amazonaws.com``/my-web-app:latest`
            + Images in official repositories on Docker Hub use a single name \(for example, `ubuntu` or `mongo`\)\.
            + Images in other repositories on Docker Hub are qualified with an organization name \(for example, `amazon/amazon-ecs-agent`\)\.
            + Images in other online repositories are qualified further by a domain name \(for example, `quay.io/assemblyline/ubuntu`\)\.

         1. For **Command**, specify the command to pass to the container\. For simple commands, you can type the command as you would at a command prompt in the **Space delimited** tab\. Then, verify that the JSON result \(which is passed to the Docker daemon\) is correct\. For more complicated commands \(for example, with special characters\), you can switch to the **JSON** tab and enter the string array equivalent there\.

            This parameter maps to `Cmd` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `COMMAND` parameter to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\. For more information about the Docker `CMD` parameter, go to [https://docs\.docker\.com/engine/reference/builder/\#cmd](https://docs.docker.com/engine/reference/builder/#cmd)\.
**Note**  
You can use default values for parameter substitution and placeholders in your command\. For more information, see [Parameters](job_definition_parameters.md#parameters)\.

         1. For **vCPUs**, specify the number of vCPUs to reserve for the container\. This parameter maps to `CpuShares` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--cpu-shares` option to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\. Each vCPU is equivalent to 1,024 CPU shares\. You must specify at least one vCPU\.

         1. For **Memory**, specify the hard limit \(in MiB\) of memory to present to the job's container\. If your container attempts to exceed the memory specified here, the container is killed\. This parameter maps to `Memory` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--memory` option to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\. You must specify at least 4 MiB of memory for a job\.
**Note**  
If you're trying to maximize your resource utilization by providing your jobs as much memory as possible for a particular instance type, see [Compute Resource Memory Management](memory-management.md)\.

         1. \(Optional\) For **Number of GPUs**, specify the number of GPUs your job uses\.

            The job runs on a container with the specified number of GPUs pinned to that container\.

         1. In the **Additional configuration** section, you can specify additional parameters to be used with the container\.

            1. \(Optional\) For **Job role**, you can specify an IAM role that provides the container in your job with permissions to use the AWS APIs\. This feature uses Amazon ECS IAM roles for task functionality\. For more information, including configuration prerequisites, see [IAM Roles for Tasks](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-iam-roles.html) in the *Amazon Elastic Container Service Developer Guide*\.
**Note**  
A job role is required for jobs that are running on Fargate resources\.
**Note**  
Only roles that have the **Amazon Elastic Container Service Task Role** trust relationship are shown here\. For more information about creating an IAM role for your AWS Batch jobs, see [Creating an IAM Role and Policy for your Tasks](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-iam-roles.html#create_task_iam_policy_and_role) in the *Amazon Elastic Container Service Developer Guide*\.

            1. \(Optional\) In the **Volumes** section, you can specify data volumes for your job to pass to your job's container\.

               1. For **Name**, enter a name for your volume\. Up to 255 letters \(uppercase and lowercase\), numbers, hyphens, and underscores are allowed\.

               1. \(Optional\) For **Source Path**, enter the path on the host instance to present to the container\. If you leave this field empty, then the Docker daemon assigns a host path for you\. If you specify a source path, then the data volume persists at the specified location on the host container instance until you delete it manually\. If the source path doesn't exist on the host container instance, the Docker daemon creates it\. If the location does exist, the contents of the source path folder are exported to the container\.

            1. \(Optional\) In the **Mount points** section, you can configure mount points for your job's container to access\.

               1. For **Container path**, enter the path on the container at which to mount the host volume\.

               1. For **Source volume**, enter the name of the volume to mount\.

               1. To make the volume read\-only for the container, choose **Read\-only**\.

            1. \(Optional\) In the **Ulimits** section, you can configure any `ulimit` values to use for your job's container\.

               1. Choose **Add limit**\.

               1. For **Limit name**, choose a ulimit to apply\.

               1. For **Soft limit**, choose the soft limit to apply for the ulimit type\.

               1. For **Hard limit**, choose the hard limit to apply for the ulimit type\.

            1. \(Optional\) In the **Environment variables** section, you can specify environment variables to pass to your job's container\. This parameter maps to `Env` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--env` option to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\.
**Important**  
We don't recommend using plaintext environment variables for sensitive information, such as credential data\.

               1. Choose **Add environment variable**\.

               1. For **Key**, specify the key for your environment variable\.
**Note**  
Environment variables must not start with `AWS_BATCH`; this naming convention is reserved for variables that are set by the AWS Batch service\.

               1. For **Value**, specify the value for your environment variable\.

            1. \(Optional\) In the **Security** section, you can configure security options for your job's container\.

               1. To give your job's container elevated privileges on the host instance \(similar to the `root` user\), select **Privileged**\. This parameter maps to `Privileged` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--privileged` option to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\.

               1. For **User**, enter the user name to use inside the container\. This parameter maps to `User` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--user` option to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\.

            1. \(Optional\) In the **Linux Parameters** section, you can configure any device mappings to use for your job's container so that the container can access a device on the host instance\.

               1. In the **Devices** section, choose **Add device**\.

               1. For **Host path**, specify the path of a device in the host instance\.

               1. For **Container path**, specify the path of in the container instance to expose the device mapped to the host instance\. If this is left blank, the host path is used in the container\.

               1. For **Permissions**, choose one or more permissions to apply to the device in the container\. The available permissions are `READ`, `WRITE`, and `MKNOD`\.

1. Return to [Step 1](#target-node-step) and repeat for each node group to configure for your job\.

1. \(Optional\) In the **Parameters** section, you can specify parameter substitution default values and placeholders to use in the command that your job's container runs when it starts\. For more information, see [Parameters](job_definition_parameters.md#parameters)\.

   1. Choose **Add parameter**\.

   1. For **Key**, specify the key for your parameter\.

   1. For **Value**, specify the value for your parameter\.

1. \(Optional\) In the **Tags** section, you can specify the key and value for each tag to associate with the job definition\. For more information, see [Tagging your AWS Batch resources](using-tags.md)\.

1. Choose **Create job definition**\.