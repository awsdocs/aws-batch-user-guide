# Getting Started with AWS Batch<a name="Batch_GetStarted"></a>

You can use the AWS Batch first\-run wizard to get started quickly with AWS Batch\. After you complete the Prerequisites, you can use the AWS Batch first\-run wizard to create a compute environment, create a job definition and a job queue in a few steps\. 

You can also submit a sample "Hello World" job in the AWS Batch first\-run wizard to test your configuration\. If you already have a Docker image that you want to launch in AWS Batch, you can use that image to create a job definition\. 

## Step 1: Prerequisites<a name="first-run-step-1"></a>

Do the following before you start the AWS Batch first\-run wizard:
+ Complete the steps that are described in [Setting Up with AWS Batch](get-set-up-for-aws-batch.md)\.
+ Verify that your AWS account has the [required permissions](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html)\.

## Step 2: Create a compute environment<a name="first-run-step-2"></a>

A compute environment is a reference to your Amazon EC2 instances\. The settings and constraints in the compute environment tell AWS Batch how to configure and automatically launch the Amazon EC2 instance\. 

To create the compute environment:
**Note**  
Currently, you can only create managed compute environments in the first\-run wizard\. To create an unmanaged compute environment, see [Creating a compute environment](create-compute-environment.md)\.

1. Open the [AWS Batch console first\-run wizard](https://console.aws.amazon.com/batch/home#wizard)\.

1. In the **Compute environment configuration** section: 

   1. For **Compute environment name**, enter a custom name\.

   1. For **Service role**, choose a service role that has permissions to call other AWS services on your behalf\. If you don't have a service role that can call other AWS services, a role is created on your behalf\.

1.  In the **Instance configuration** section:

   1. For **Provisioning model**, choose **Fargate**, **Fargate Spot**, **On\-demand**, or **Spot**\.

   1. \(**Spot** only\) For **Maximum % on\-demand price**, enter the maximum percentage of On\-demand pricing that you want to pay for Spot resources\.

   1. \(**Spot** and **On\-demand** only\) For **Minimum vCPUs**, enter the minimum number of vCPUs that the instance uses\. 
**Tip**  
If you set **Minimum vCPUs** to **0**, no instance time is used when no work is available\.

   1. \(All provisioning models\) For **Maximum vCPUs**, enter the maximum number of vCPUs that the instance uses\.

   1. \(Optional, **Spot **and **On\-demand **only\) For **Desired vCPUs**, enter the desired number of vCPUs that the instance uses\.

   1. \(**Spot **and **On\-demand **only\) For **Allowed instance types**, choose the instance types that the instance uses\.
**Important**  
By default, **optimal** is chosen\. The **optimal** setting chooses the best fit of M4, C4, and R4 instance types that are available in your AWS Region\. To use a custom set of instance types, remove the **optimal** setting, and then choose the instance types that you want\.

   1. \(**Spot **and **On\-demand **only\) For **Allocation strategy**, choose **BEST\_FIT\_PROGRESSIVE** for On\-Demand or **SPOT\_CAPACITY\_OPTIMIZED** for Spot\.

1. In the **Networking** section:

   1. For **VPC\_ID**, choose an Amazon VPC\.

   1. For **Subnets**, the subnets for your AWS account are listed by default\. If you want to create a custom set of subnets, choose **Clear subnets**, and then choose the subnets you want\.
**Important**  
Compute resources must communicate with the Amazon ECS VPC endpoint through a VPC endpoint or multiple public IP address\. For more information, see [Amazon ECS interface VPC endpoints \([AWS PrivateLink](get-set-up-for-aws-batch.md#create-a-vpc)\)](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/vpc-endpoints.html)\. If your instance doesn't have a VPC endpoint configured or a public IP address, you can use network address translation \(NAT\)\. For more information about NAT, see [NAT gateways](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) and [Tutorial: Creating a VPC with Public and Private Subnets for Your Compute Environments](https://docs.aws.amazon.com/batch/latest/userguide/create-public-private-vpc.html)\.

   1. \(Optional\) Expand **Additional settings: Security groups, placement group**:

      1. For **Security groups**, choose the Amazon EC2 security groups that you want to associate with the instance\. If you want to create a custom set of security groups, choose **Clear security groups**\. Then, choose the security groups that you want\.

      1. \(Optional, **On\-demand** and **Spot** only\) For **Placement group**, enter a placement group name if you create multi\-node parallel jobs\.

1. Choose **Next**\.

## Step 3: Create a job queue<a name="first-run-step-3"></a>

A job queue stores your submitted jobs until the AWS Batch Scheduler runs the job on a resource in your compute environment\. To create a job queue:

1. In the **Job queue configuration** section:

   1. For **Job queue name**, enter a custom name\.

   1. For **Priority**, enter an integer between 0 and 100 for the job queue\. 
**Important**  
Higher integer values are assigned a higher priority by the AWS Batch Scheduler\.

   1. \(Optional\) If you have an AWS scheduling policy that you want to apply to the job queue:

      1. Turn on **Scheduling policy ARN**\.

      1. Choose the Amazon Resource Name \(ARN\) that you want\.

   1. \(Optional\) Expand **Additional configuration**\.

       For **State**, choose a job queue state\.

1. \(Optional\) In the **Tags** section::

   1. Choose **Add tag**\. Enter a key name\-value pair, and then choose **Add tag**\.
**Important**  
If you choose **Add tag**, you must enter a key\-value pair and choose **Add tag** again or choose **Remove tag**\.

1. Choose **Next**\.

## Step 4: Create a job definition<a name="first-run-step-4"></a>

To create the job definition:

1. In the **General configuration** section:

   1. For **Name**, enter a custom job definition name\.

   1. For **Execution timeout**, enter the amount of time, in seconds, that an unfinished job terminates after\. 
**Important**  
The minimum timeout is 60 seconds\.

   1. \(Optional\) Expand **Additional tags configuration**\.

   1. A tag is a label assigned to a resource\. To add a tag, choose **Add tag**\. Enter a key\-value pair, and then choose **Add tag** again\.
**Important**  
If you choose **Add tag**, you must enter a key\-value pair and choose **Add tag** again or choose **Remove tag**\.

   1. \(Optional\) Turn on **Propagate tags** to propagate tags to the Amazon Elastic Container Service task\.

1. In the **Platform compatibility** section:

   1. For **Execution role**, choose a task execution role that lets Amazon Elastic Container Service \(Amazon ECS\) agents make AWS calls on your behalf\. For example, you can choose **ecsTaskExecutionRole**\.

1. In the **Job configuration** section:

   1. For **Image**, enter the name of the image that's used to launch the container\. By default, all the images in the Docker Hub registry are available\. You can also specify other repositories in *repository\-url/image:tag* format\. The parameter can be up to 255 characters in length\. It can contain uppercase and lowercase letters, numbers, hyphens \(\-\), underscores \(\_\), colons \(:\), periods \(\.\), forward slashes \(/\), and number signs \(\#\)\. The parameter maps to `Image` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `IMAGE` parameter of [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\.
**Note**  
Docker image architecture must match the processor architecture of the compute resources that they're scheduled on\. For example, ARM\-based Docker images can only run on ARM\-based compute resources\.
      + Images in Amazon ECR Public repositories use the full `registry/repository[:tag]` or `registry/repository[@digest]` naming conventions \(for example, `public.ecr.aws/registry_alias/my-web-app:latest`\)\.
      + Images in Amazon ECR repositories use the full `registry/repository:tag` naming convention \(for example, `aws_account_id.dkr.ecr.region.amazonaws.com``/my-web-app:latest`\)\.
      + Images in official repositories on Docker Hub use a single name \(for example, `ubuntu` or `mongo`\)\.
      + Images in other repositories on Docker Hub are qualified with an organization name \(for example, `amazon/amazon-ecs-agent`\)\.
      + Images in other online repositories are qualified further by a domain name \(for example, `quay.io/assemblyline/ubuntu`\)\.

   1. For **Command syntax**, choose **Bash** or **JSON**\.

   1. For **Command**, specify the command to pass to the container\. This parameter maps to `Cmd` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `COMMAND` parameter to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\. For more information about the Docker `CMD` parameter, see [https://docs\.docker\.com/engine/reference/builder/\#cmd](https://docs.docker.com/engine/reference/builder/#cmd)\.
**Note**  
You can use parameter substitution default values and placeholders in your command\. For more information, see [Parameters](job_definition_parameters.md#parameters)\.
**Tip**  
 Choose **Info** to review Bash and JSON code examples\.

   1. For **vCPUs**, specify the number of vCPUs to reserve for the container\. This parameter maps to `CpuShares` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--cpu-shares` option to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\. Each vCPU is equivalent to 1,024 CPU shares\.

   1. For **Memory**, specify the hard limit \(in MiB\) of memory to present to the job container\. If your container attempts to exceed the memory specified here, the container is stopped\. This parameter maps to `Memory` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--memory` option to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\.

   1. \(Optional\) Turn on **Environment variables configuration** to add environment variables that are passed to the container\. This parameter maps to `Env` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--env` option to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\.

   1. \(Optional\) Turn on **Job role configuration** to add IAM roles that have permissions to the AWS APIs\. This feature uses Amazon ECS IAM roles for task functionality\. For more information, see [IAM Roles for Tasks](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-iam-roles.html) in the *Amazon Elastic Container Service Developer Guide*\.
**Note**  
A job role is required for jobs that runs on Fargate resources\.
**Note**  
Only roles that have the **Amazon Elastic Container Service Task Role** trust relationship are shown here\. For instructions on how to create an IAM role for your AWS Batch jobs, see [Creating an IAM Role and Policy for your Tasks](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-iam-roles.html#create_task_iam_policy_and_role) in the *Amazon Elastic Container Service Developer Guide*\.

   1. \(Optional\) Turn on **Security configuration** to add user names for use in the container\.

      To give your job container elevated permissions on the host instance \(similar to the `root` user\), turn on **Privileged**\. This parameter maps to `Privileged` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--privileged` option to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\.

      For **User**, enter the user name to use inside the container\. This parameter maps to `User` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--user` option to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\.

   1. \(Optional\) Turn on **Secrets** to add secrets as a name\-value pair\. These secrets are exposed in the container\. For more information, see [secretOptions](job_definition_parameters.md#ContainerProperties-logConfiguration-secretOptions) in [Job definition parameters](job_definition_parameters.md)\.

   1. \(Optional\) Turn on **Enable read only filesystem** to remove write access to the volume\.

   1. \(Optional\) Turn on **Mount points configuration** to add mount points for data volumes\. You must specify the source volume and container path\. These mount points are passed to the Docker daemon on a container instance\.

   1. \(Optional, **On\-demand** and **Spot** only\) Turn on **Ulimits configuration** to configure a list of ulimit values\. Enter a name, soft limit, and hard limit, and then choose **Add ulimit**\.

   1. \(Optional\) Choose **Volumes configuration** to create a list of volumes to pass to the container\. Enter a **Name** and **Source path** for the volume, and then choose **Add volume**\. 

   1. \(Optional\) Turn on **Linux parameter configuration** to add devices or turn on Linux specific settings such as **Init process enabled**:

      1. \(Optional\) Turn on **Init process enabled** to run an init process inside the container\. This process forwards signals and reaps processes\.

      1. \(Optional, **Spot **and **On\-demand** only\) For **Devices**, choose **Add device**\.

         1. \(Optional, **Spot** and **On\-demand** only\) For **Devices**, choose **Add device** to add a device

         1. \(Optional, **Spot** and **On\-demand** only\) For **Container path**, specify the path of in the container instance to expose the device mapped to the host instance\. If this is kept blank \(unspecified\), then the host path is used in the container\.

         1. \(Optional, **Spot** and **On\-demand** only\) For **Host path**, specify the path of a device in the host instance\.

         1. \(Optional, **Spot** and On\-demand only\) For **Permissions**, choose one or more permissions to apply to the device in the container\. The available permissions are `READ`, `WRITE`, and `MKNOD`\.

      1. \(Optional, **Spot** and **On\-demand** only\) For **Shared memory size**, enter the size \(in MiB\) of the `/dev/shm` volume\.

      1. \(Optional, **Spot** and **On\-demand** only\) I For **Max swap size**, enter the total amount of swap memory \(in MiB\) that the container can use\.

      1. \(Optional, **Spot** and **On\-demand** only\) For **Swappiness** enter a value between 0 and 100 to indicate the swappiness behavior of the container\. If it's not specified and swapping is enabled, the default value is 60\. For more information, see [swappiness](job_definition_parameters.md#ContainerProperties-linuxParameters-swappiness) in [Job definition parameters](job_definition_parameters.md)\.

      1. \(Optional, **Spot** and **On\-demand** only\) For **Tmpfs**, choose **Add tmpfs** to add a `tmpfs` mount\.

      1. \(Optional, **Spot** and **On\-demand** only\) For **Container path**, enter the absolute file path in the container where the tmpfs volume is mounted\.

      1. \(Optional, **Spot** and **On\-demand **only\) In the **Size** field, enter size \(in MiB\) of the `tmpfs` volume\.

      1. \(Optional\) For **Mount options**, enter the mount options\. For more information, including the list of available mount options, see [mountOptions](job_definition_parameters.md#ContainerProperties-linuxParameters-tmpfs-mountOptions) in [Job definition parameters](job_definition_parameters.md)\.

   1. \(Optional\) Turn on **Log configuration** to add a custom log driver to the container:
**Note**  
By default, the `awslogs` log driver is used\.

      1. \(Optional\) For **Log driver**, choose a log driver to use\. For more information about the available log drivers, see [logDriver](job_definition_parameters.md#ContainerProperties-logConfiguration-logDriver) in [Job definition parameters](job_definition_parameters.md)\.

      1. \(Optional\) For **Options**, choose **Add option** to add an option\. Enter a name\-value pair, and then choose **Add option**\.

      1. \(Optional\) Turn on **Secrets**\. Enter a name\-value pair, and then choose **Add** to add a secret\.
**Tip**  
For more information, see [secretOptions](job_definition_parameters.md#ContainerProperties-logConfiguration-secretOptions) in [Job definition parameters](job_definition_parameters.md)

1. \(Optional\) You can add parameters to the job definition as key\-value mappings to override the job definition defaults\. To add a parameter:

   1. For **Parameters**, choose **Add parameter**\. Enter a key\-value pair and then choose **Add parameter** again\.
**Important**  
If you choose **Add parameter**, you must configure at least one parameter or choose **Remove parameter**\.

1. \(Optional\) In the **Retry Strategies** section, you can configure the number of times a failed job is resubmitted\. You can also configure exit codes and status reasons to help troubleshoot a failed instance\. To configure a retry strategy:

   1. For **Job attempts**, enter the number of times to resubmit a failed job\.

   1. \(Optional\) Choose **Add evaluate on exit**\. Enter at least one parameter value, and then choose an **Action**\.
**Important**  
If you choose **Add evaluate on exit**, you must configure at least one parameter and choose an **Action** or choose **Remove evaluate on exit**\.

1. Choose **Next**\.

## Step 5: Create a job<a name="first-run-step-5"></a>

Jobs are units of work performed by AWS Batch\. Jobs are launched as containerized applications on Amazon Elastic Container Service container instances in an Amazon ECS cluster\.

To create a job:

1. In the **General configuration** section:

   1. For **Name**, enter a custom name\.

   1. For **Execution timeout**, enter a time duration, in seconds, before an unfinished job is terminated\. The minimum timeout is 60 seconds\.

   1. \(Optional\) Turn on **Array job** to spread the job across multiple hosts\. Enter the number of hosts in **Array size**\.

   1. \(Optional\) If the job has any dependencies, turn on **Job dependencies**\. Then enter the **Job id** of the dependency and choose **Add**\.

   1. \(Optional\) Expand **Additional tags configuration**\. From **Tags**, choose **Add tag** to add a tag\. Enter a tag key and optional value, then choose **Add tag** again\.
**Important**  
If you choose **Add tag**, you must enter a key\-value pair and choose **Add tag** again or choose **Remove tag**\.

   1. \(Optional\) Turn on ** Propagate tags** to propagate tags to the ECS task\.

1. In the **Job configuration** section:

   1. For **Command syntax**, choose **Bash** or **JSON**\. 

   1. For **Command**, specify the command to pass to the container\. This parameter maps to `Cmd` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `COMMAND` parameter to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\. For more information about the Docker `CMD` parameter, see [https://docs\.docker\.com/engine/reference/builder/\#cmd](https://docs.docker.com/engine/reference/builder/#cmd)\.
**Note**  
You can use parameter substitution default values and placeholders in your command\. For more information, see [Parameters](job_definition_parameters.md#parameters)\.
**Tip**  
Choose **Info** to review Bash and JSON code examples\.

   1. For **vCPUs**, enter the number of vCPUs to reserve for the container\.

   1. For **Memory**, enter the memory limit available to the container\.

   1. For **Number of GPUs**, enter the number of graphical processing units \(GPUs\) to reserve for the container\.

   1. \(Optional\) Turn on **Environment variables configuration** to pass additional environment variables to the container\. Enter a name and value, then choose **Add**\.

1. In the **Parameters** section, you can add additional parameters that are passed to the job\. These parameters replace the parameter values in the job definition\. In the **Parameters** section:

   1. \(Optional\) For **Parameters**, choose **Add parameter**\.

   1. Enter the parameter **Name** and **Value**, then choose **Add parameter**\.
**Important**  
If you choose **Add parameter**, you must enter a name\-value pair and choose **Add parameter** or choose **Remove parameter**\.

1. In the **Retry Strategies** section, you can configure the number of times a failed job is resubmitted\. You can also configure exit codes and status reasons to help troubleshoot a failed instance\. To configure a retry strategy:

   1. For **Job attempts**, enter the number of times to resubmit a failed job\.

   1. \(Optional\) Choose **Add evaluate on exit**\. Enter an **Exit code**, **Status reason**, and **Reason**\. Then, choose an **Action**\.
**Important**  
If you choose **Add evaluate on exit**, you must configure at least one parameter and choose an **Action** or choose **Remove evaluate on exit**\.

1. Choose **Next**\.

## Step 6: Review and create<a name="first-run-step-6"></a>

For **Review and create**, review the configuration steps\. If you need to make changes, choose **Edit**\. When you are happy with the configuration, choose **Create**\.