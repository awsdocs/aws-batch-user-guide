# Getting Started with AWS Batch<a name="Batch_GetStarted"></a>

Get started with AWS Batch by creating a job definition, compute environment, and a job queue in the AWS Batch console\. 

The AWS Batch first\-run wizard gives you the option of creating a compute environment and a job queue and submitting a sample hello world job\. If you already have a Docker image you would like to launch in AWS Batch, you can create a job definition with that image and submit that to your queue instead\.

**Important**  
Before you begin, be sure that you've completed the steps in [Setting Up with AWS Batch](get-set-up-for-aws-batch.md) and that your AWS user has the required permissions \(admin users do not need to worry about permissions issues\)\. For more information, see [Creating Your First IAM Admin User and Group](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html) in the *IAM User Guide\.*

## Step 1: Define a Job<a name="first-run-step-1"></a>

In this section, you choose to define your job definition or move ahead to creating a compute environment and job queue without a job definition\.

**To configure job options**

1. Open the AWS Batch console first\-run wizard at [https://console\.aws\.amazon\.com/batch/home\#/wizard](https://console.aws.amazon.com/batch/home#/wizard)\.

1. To create an AWS Batch job definition, compute environment, and job queue and then submit your job, choose **Using Amazon EC2**\. To only create the compute environment and job queue without submitting a job, choose **No job submission**\.

1. If you chose to create a job definition, then complete the next four sections of the first\-run wizard, **Job run\-time**, **Environment**, **Parameters**, and **Environment variables** and then choose **Next**\. If you are not creating a job definition, choose **Next** and move on to [Step 2: Configure the Compute Environment and Job Queue](#first-run-step-2)\.

**To specify job run time**

1. If you are creating a new job definition, for **Job definition name**, specify a name for your job definition\.

1. \(Optional\) For **Job role**, you can specify an IAM role that provides the container in your job with permissions to use the AWS APIs\. This feature uses Amazon ECS IAM roles for task functionality\. For more information about this feature, including configuration prerequisites, see [IAM Roles for Tasks](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-iam-roles.html) in the *Amazon Elastic Container Service Developer Guide*\.
**Note**  
Only roles that have the **Amazon Elastic Container Service Task Role** trust relationship are shown here\. For more information about creating an IAM role for your AWS Batch jobs, see [Creating an IAM Role and Policy for your Tasks](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-iam-roles.html#create_task_iam_policy_and_role) in the *Amazon Elastic Container Service Developer Guide*\.

1. For **Container image**, choose the Docker image to use for your job\. Images in the Docker Hub registry are available by default\. You can also specify other repositories with `repository-url/image:tag`\. Up to 255 letters \(uppercase and lowercase\), numbers, hyphens, underscores, colons, periods, forward slashes, and number signs are allowed\. This parameter maps to `Image` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `IMAGE` parameter of [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\.
**Note**  
Docker image architecture must match the processor architecture of the compute resources that they're scheduled on\. For example, ARM\-based Docker images can only run on ARM\-based compute resources\.
   + Images in Amazon ECR repositories use the full `registry/repository:tag` naming convention\. For example, `aws_account_id.dkr.ecr.region.amazonaws.com``/my-web-app:latest`
   + Images in official repositories on Docker Hub use a single name \(for example, `ubuntu` or `mongo`\)\.
   + Images in other repositories on Docker Hub are qualified with an organization name \(for example, `amazon/amazon-ecs-agent`\)\.
   + Images in other online repositories are qualified further by a domain name \(for example, `quay.io/assemblyline/ubuntu`\)\.

**To specify resources for your environment**

1. For **Command**, specify the command to pass to the container\. This parameter maps to `Cmd` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `COMMAND` parameter to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\. For more information about the Docker `CMD` parameter, go to [https://docs\.docker\.com/engine/reference/builder/\#cmd](https://docs.docker.com/engine/reference/builder/#cmd)\.
**Note**  
You can use parameter substitution default values and placeholders in your command\. For more information, see [Parameters](job_definition_parameters.md#parameters)\.

1. For **vCPUs**, specify the number of vCPUs to reserve for the container\. This parameter maps to `CpuShares` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--cpu-shares` option to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\. Each vCPU is equivalent to 1,024 CPU shares\.

1. For **Memory**, specify the hard limit \(in MiB\) of memory to present to the job's container\. If your container attempts to exceed the memory specified here, the container is killed\. This parameter maps to `Memory` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--memory` option to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\.

1. For **Job attempts**, specify the maximum number of times to attempt your job \(in case it fails\)\. For more information, see [Automated Job Retries](job_retries.md)\.

**Parameters**

You can optionally specify parameter substitution default values and placeholders in your command\. For more information, see [Parameters](job_definition_parameters.md#parameters)\.

1. For **Key**, specify the key for your parameter\.

1. For **Value**, specify the value for your parameter\.

**To specify environment variables**

You can optionally specify environment variables to pass to your job's container\. This parameter maps to `Env` in the [Create a container](https://docs.docker.com/engine/api/v1.38/#operation/ContainerCreate) section of the [Docker Remote API](https://docs.docker.com/engine/api/v1.38/) and the `--env` option to [https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)\.
**Important**  
We do not recommend using plaintext environment variables for sensitive information, such as credential data\.

1. For **Key**, specify the key for your environment variable\.

1. For **Value**, specify the value for your environment variable\.

## Step 2: Configure the Compute Environment and Job Queue<a name="first-run-step-2"></a>

A compute environment is a way to reference your compute resources \(Amazon EC2 instances\): the settings and constraints that tell AWS Batch how instances should be configured and automatically launched\. You submit your jobs to a job queue that stores jobs until the AWS Batch scheduler runs the job on a compute resource within your compute environment\.

**Note**  
At this time, you can only create a managed compute environment in the first run wizard\. To create an unmanaged compute environment, see [Creating a Compute Environment](create-compute-environment.md)\.

**To configure your compute environment type**

1. For **Compute environment name**, specify a unique name for your compute environment\.

1. For **Service role**, choose to create a new role or use an existing role that allows the AWS Batch service to make calls to the required AWS APIs on your behalf\. For more information, see [AWS Batch Service IAM Role](service_IAM_role.md)\. If you choose to create a new role, the required role \(`AWSBatchServiceRole`\) is created for you\.

1. For **EC2 instance role**, choose to create a new role or use an existing role that allows the Amazon ECS container instances that are created for your compute environment to make calls to the required AWS APIs on your behalf\. For more information, see [Amazon ECS Instance Role](instance_IAM_role.md)\. If you choose to create a new role, the required role \(`ecsInstanceRole`\) is created for you\.

**To configure your instances**

1. For **Provisioning model**, choose **On\-Demand** to launch Amazon EC2 On\-Demand instances or **Spot** to use Amazon EC2 Spot Instances\.

1. If you chose to use Amazon EC2 Spot Instances:

   1. For **Maximum bid price**, choose the maximum percentage that a Spot Instance price must be when compared with the On\-Demand price for that instance type before instances are launched\. For example, if your bid percentage is 20%, then the Spot price must be below 20% of the current On\-Demand price for that EC2 instance\. You always pay the lowest \(market\) price and never more than your maximum percentage\.

   1. For **Spot fleet role**, choose to create a new role or use an existing Amazon EC2 Spot Fleet IAM role to apply to your Spot compute environment\. If you choose to create a new role, the required role \(`aws-ec2-spot-fleet-role`\) is created for you\. For more information, see [Amazon EC2 Spot Fleet Role](spot_fleet_IAM_role.md)\.

1. For **Allowed instance types**, choose the Amazon EC2 instance types that may launched\. You can specify instance families to launch any instance type within those families \(for example, `c5`, `c5n`, or `p3`\), or you can specify specific sizes within a family \(such as `c5.8xlarge`\)\. Note that metal instance types are not in the instance families \(for example `c5` does not include `c5.metal`\.\) You can also choose `optimal` to pick instance types \(from the C, M, and R instance families\) on the fly that match the demand of your job queues\.
**Note**  
When you create a compute environment, the instance types that you select for the compute environment must share the same architecture\. For example, you can't mix x86 and ARM instances in the same compute environment\.

1. For **Minimum vCPUs**, choose the minimum number of EC2 vCPUs that your compute environment should maintain, regardless of job queue demand\.

1. For **Desired vCPUs**, choose the number of EC2 vCPUs with which your compute environment should launch\. As your job queue demand increases, AWS Batch can increase the desired number of vCPUs in your compute environment and add EC2 instances, up to the maximum vCPUs, and as demand decreases, AWS Batch can decrease the desired number of vCPUs in your compute environment and remove instances, down to the minimum vCPUs\.

1. For **Maximum vCPUs**, choose the maximum number of EC2 vCPUs that your compute environment can scale out to, regardless of job queue demand\.

**To set up your networking**

Compute resources are launched into the VPC and subnets that you specify here\. This allows you to control the network isolation of AWS Batch compute resources\.
**Important**  
Compute resources need access to communicate with the Amazon ECS service endpoint\. This can be through an interface VPC endpoint or through your compute resources having public IP addresses\.  
For more information about interface VPC endpoints, see [Amazon ECS Interface VPC Endpoints \(AWS PrivateLink\)](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/vpc-endpoints.html) in the *Amazon Elastic Container Service Developer Guide*\.  
If you do not have an interface VPC endpoint configured and your compute resources do not have public IP addresses, then they must use network address translation \(NAT\) to provide this access\. For more information, see [NAT Gateways](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) in the *Amazon VPC User Guide*\. For more information, see [Tutorial: Creating a VPC with Public and Private Subnets for Your Compute Environments](create-public-private-vpc.md)\.

1. For **VPC Id**, choose a VPC into which to launch your instances\.

1. For **Subnets**, choose which subnets in the selected VPC should host your instances\. By default, all subnets within the selected VPC are chosen\.

1. For **Security groups**, choose a security group to attach to your instances\. By default, the default security group for your VPC is chosen\.

**To tag your instances**

You can optionally apply key\-value pair tags to instances that are launched in your compute environment\. For example, you can specify `"Name": "AWS Batch Instance - C4OnDemand"` as a tag so that each instance in your compute environment has that name \(this is helpful for recognizing your AWS Batch instances in the Amazon EC2 console\)\. By default, the compute environment name is used to tag your instances\.

1. For **Key**, specify the key for your tag\.

1. For **Value**, specify the value for your tag\.

**To set up your job queue**

You submit your jobs to a job queue which stores jobs until the AWS Batch scheduler runs the job on a compute resource within your compute environment\.
+ For **Job queue name**, choose a unique name for your job queue\.

**To review and create**

The **Connected compute environments for this job queue** section shows that your new compute environment is associated with your new job queue and its order\. Later, you can associate other compute environments with the job queue\. The job scheduler uses the compute environment order to determine which compute environment should execute a given job\. Compute environments must be in the `VALID` state before you can associate them with a job queue\. You can associate up to three compute environments with a job queue\.
+ Review the compute environment and job queue configuration and choose **Create** to create your compute environment\.