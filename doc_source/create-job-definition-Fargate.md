# Creating a single\-node job definition on AWS Fargate resources<a name="create-job-definition-Fargate"></a>

**To create a new job definition on AWS Fargate resources:**

1. Open the AWS Batch console at [https://console\.aws\.amazon\.com/batch/](https://console.aws.amazon.com/batch/)\.

1. From the top navigation bar, choose the AWS Region to use\.

1. In the left navigation pane, choose **Job definitions**\.

1. Choose **Create**\.

1. For **Orchestration type**, choose **Fargate**\. For more information, see [AWS Batch on AWS Fargate](fargate.md)\.

1. For **Name**, enter a unique name for your job definition\. The name can be up to 128 characters in length\. It can contain uppercase and lowercase letters, numbers, hyphens \(\-\), and underscores \(\_\)\.

1. \(Optional\) For **Execution timeout**, enter the timeout value \(in seconds\)\. The execution timeout is the length of time before an unfinished job is terminated\. If an attempt exceeds the timeout duration, the attempt is stopped and moves to a `FAILED` status\. For more information, see [Job timeouts](job_timeouts.md)\. The minimum value is 60 seconds\.

1. \(Optional\) Turn on **Scheduling priority**\. Enter a scheduling priority value between 0 and 100\. Higher values are given higher priority over lower values\.

1. \(Optional\) Expand **Tags**, and then choose **Add tag** to add tags to the resource\. Turn on **Propagate tags** to propagate tags from the job and job definition\.

1. In the **Fargate platform configuration** section:

   1. For **Fargate platform version**, enter `LATEST` or a specific runtime environment version\.

   1. \(Optional\) Turn on **Assign public IP** to assign a public IP address to a Fargate job network interface\. For a job that's running in a private subnet to send outbound traffic to the internet, the private subnet requires a NAT gateway be attached to route requests to the internet\. You might want to do this so that you can pull container images\. For more information, see [Amazon ECS task networking](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-networking.html) in the *Amazon Elastic Container Service Developer Guide*\.

   1. For **Execution role**, specify an IAM role that grants the Amazon ECS container and Fargate agents permission to make AWS API calls on your behalf\. This feature uses Amazon ECS IAM roles for task functionality\. For more information including configuration prerequisites, see [Amazon ECS task execution IAM roles](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_execution_IAM_role.html) in the *Amazon Elastic Container Service Developer Guide*\. 

   1. For **Job attempts**, enter the number of times that AWS Batch attempts to move the job to a `RUNNABLE` status\. Enter a number between 1 and 10\.

   1. Optional\) For **Retry strategy conditions**, choose **Add evaluate on exit**\. Enter at least one parameter value and then choose an **Action**\. For each set of conditions, **Action** must be set to either **Retry** or **Exit**\. These actions mean the following:
      + **Retry** – AWS Batch retries until the number of job attempts that you specified is reached\.
      + **Exit** – AWS Batch stops retrying the job\.
**Important**  
If you choose **Add evaluate on exit**, you must configure at least one parameter and choose an **Action** or choose **Remove evaluate on exit**\.

1. Choose **Next page**\.

1. In the **Container configuration** section:

   1. For **Image**, choose the Docker image to use for your job\. By default, images in the Docker Hub registry are available\. You can also specify other repositories with `repository-url/image:tag`\. The name can be up to 225 characters in length\. It can contain uppercase and lowercase letters, numbers, hyphens \(\-\), underscores \(\_\), colons \(:\), periods \(\.\), forward slashes \(/\), and number signs \(\#\)\. This parameter maps to `Image` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `IMAGE` parameter of [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\.
**Note**  
Docker image architecture must match the processor architecture of the compute resources that they're scheduled on\. For example, Arm based Docker images can only run on Arm based compute resources\.
      + Images in Amazon ECR Public repositories use the full `registry/repository[:tag]` or `registry/repository[@digest]` naming conventions \(for example, `public.ecr.aws/registry_alias/my-web-app:latest`\)\.
      + Images in Amazon ECR repositories use the full `registry/repository[:tag]` naming convention \(for example, `aws_account_id.dkr.ecr.region.amazonaws.com``/my-web-app:latest`\)\.
      + Images in official repositories on Docker Hub use a single name \(for example, `ubuntu` or `mongo`\)\.
      + Images in other repositories on Docker Hub are qualified with an organization name \(for example, `amazon/amazon-ecs-agent`\)\.
      + Images in other online repositories are qualified further by a domain name \(for example, `quay.io/assemblyline/ubuntu`\)\.

   1. For **Command syntax**, choose **Bash** or **JSON**\.

   1. For **Command**, specify the command to pass to the container\. For simple commands, enter the command as you do for a command prompt and then verify that the JSON result is correct\. It's passed to the Docker daemon\. For more complicated commands \(for example, with special characters\), use **JSON** syntax\.
**Tip**  
Choose **Info** to view Bash and JSON code samples\.

      This parameter maps to `Cmd` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `COMMAND` parameter to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\. For more information about the Docker `CMD` parameter, see [https://docs\.docker\.com/engine/reference/builder/\#cmd](https://docs.docker.com/engine/reference/builder/#cmd)\.
**Note**  
You can use default values for parameter substitution and placeholders in your command\. For more information, see [Parameters](job_definition_parameters.md#parameters)\.

   1. \(Optional\) Add parameters to the job definition as name\-value mappings to override the job definition defaults\. To add a parameter:

      1. For **Parameters**, choose **Add parameters**, enter a name\-value pair, then choose **Add parameter**\.
**Important**  
If you choose **Add parameter**, you must either configure at least one parameter or choose **Remove parameter**

   1. In the **Environment configuration** section:

      

      1. For **Job Role configuration**, choose an IAM role that has permissions to the AWS APIs\. This feature uses Amazon ECS IAM roles for task functionality\. For more information, see [IAM Roles for Tasks](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-iam-roles.html) in the *Amazon Elastic Container Service Developer Guide*\.
**Note**  
A job role is required for jobs that runs on Fargate resources\.
**Note**  
Only roles that have the **Amazon Elastic Container Service Task Role** trust relationship are shown here\. For more information about how to create an IAM role for your AWS Batch jobs, see [Creating an IAM Role and Policy for your Tasks](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-iam-roles.html#create_task_iam_policy_and_role) in the *Amazon Elastic Container Service Developer Guide*\.

      1. For **vCPUs**, enter the number of vCPUs to reserve for the container\. This parameter maps to `CpuShares` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--cpu-shares` option to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\. Each vCPU is equivalent to 1,024 CPU shares\. You must specify at least one vCPU\.

      1. For **Memory**, enter the memory limit that's available to the container\. If your container attempts to exceed the memory specified here, the container is stopped\. This parameter maps to `Memory` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--memory` option to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\. You must specify at least 4 MiB of memory for a job\.
**Note**  
To maximize your resource utilization, prioritze memory for jobs of a specific instance type\. For more information, see [Compute Resource Memory Management](memory-management.md)\.

   1. \(Optional\) For **Environment variables**, choose **Add environment variable** to add environment variables as name\-value pairs\. These variables are passed to the container\.

   1. \(Optional\) For **Secrets**, choose **Add secret** to add secrets as a name\-value pairs\. These secrets are exposed in the container\. For more information, see [secretOptions](job_definition_parameters.md#ContainerProperties-logConfiguration-secretOptions) in [Job definition parameters](job_definition_parameters.md)\.

   1. Choose **Next page**\.

1. \(Optional\) In the** Linux configuration** section:

   1. For **User**, enter a user name to use inside the container\.

   1. Turn on **Enable init process** to run an init process inside the container\. This process forwards signals and reaps processes\.

   1. Turn on **Enable read only filesystem** to remove write access to the volume\.

   1. \(Optional\) Expand **Additional configuration**\.

   1. For **Mount points configuration**, choose **Add mount points configuration** to add mount points for data volumes\. You must specify the source volume and container path\. These mount points are passed to the Docker daemon on a container instance\.

   1. For **Volumes configuration**, choose **Add volume** to create a list of volumes to pass to the container\. Enter a **Name** and **Source path** for the volume, and then choose **Add volume**\. 

   1. In the **Logging configuration** section:

      1. \(Optional\) For **Log driver**, choose the log driver to use\. For more information about the available log drivers, see [logDriver](job_definition_parameters.md#ContainerProperties-logConfiguration-logDriver) in [Job definition parameters](job_definition_parameters.md)\.
**Note**  
By default, the `awslogs` log driver is used\.

      1. \(Optional\) For **Options**, choose **Add option** to add an option\. Enter a name\-value pair, and then choose **Add option**\.

      1. \(Optional\) For **Secrets**, choose **Add secret** to add a secret\. Then, enter a name\-value pair, and choose **Add secret**\.
**Tip**  
For more information, see [secretOptions](job_definition_parameters.md#ContainerProperties-logConfiguration-secretOptions) in [Job definition parameters](job_definition_parameters.md)\.

1. Choose **Next page**\.

1. For **Job definition review**, review the configuration steps\. If you need to make changes, choose **Edit**\. When you're finished, choose **Create job definition**\.