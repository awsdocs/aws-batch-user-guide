# What Is AWS Batch?<a name="what-is-batch"></a>

AWS Batch enables you to run batch computing workloads on the AWS Cloud\. Batch computing is a common way for developers, scientists, and engineers to access large amounts of compute resources, and AWS Batch removes the undifferentiated heavy lifting of configuring and managing the required infrastructure, similar to traditional batch computing software\. This service can efficiently provision resources in response to jobs submitted in order to eliminate capacity constraints, reduce compute costs, and deliver results quickly\.

As a fully managed service, AWS Batch enables you to run batch computing workloads of any scale\. AWS Batch automatically provisions compute resources and optimizes the workload distribution based on the quantity and scale of the workloads\. With AWS Batch, there is no need to install or manage batch computing software, which allows you to focus on analyzing results and solving problems\.

## Components of AWS Batch<a name="batch_components"></a>

AWS Batch is a regional service that simplifies running batch jobs across multiple Availability Zones within a region\. You can create AWS Batch compute environments within a new or existing VPC\. After a compute environment is up and associated with a job queue, you can define job definitions that specify which Docker container images to run your jobs\. Container images are stored in and pulled from container registries, which may exist within or outside of your AWS infrastructure\.

### Jobs<a name="component_job"></a>

A unit of work \(such as a shell script, a Linux executable, or a Docker container image\) that you submit to AWS Batch\. It has a name, and runs as a containerized application on an Amazon EC2 instance in your compute environment, using parameters that you specify in a job definition\. Jobs can reference other jobs by name or by ID, and can be dependent on the successful completion of other jobs\. For more information, see [Jobs](jobs.md)\.

### Job Definitions<a name="component_job_definition"></a>

A job definition specifies how jobs are to be run; you can think of it as a blueprint for the resources in your job\. You can supply your job with an IAM role to provide programmatic access to other AWS resources, and you specify both memory and CPU requirements\. The job definition can also control container properties, environment variables, and mount points for persistent storage\. Many of the specifications in a job definition can be overridden by specifying new values when submitting individual Jobs\. For more information, see [Job Definitions](job_definitions.md)

### Job Queues<a name="component_job_queue"></a>

When you submit an AWS Batch job, you submit it to a particular job queue, where it resides until it is scheduled onto a compute environment\. You associate one or more compute environments with a job queue, and you can assign priority values for these compute environments and even across job queues themselves\. For example, you could have a high priority queue that you submit time\-sensitive jobs to, and a low priority queue for jobs that can run anytime when compute resources are cheaper\.

### Compute Environment<a name="component_compute_environment"></a>

A compute environment is a set of managed or unmanaged compute resources that are used to run jobs\. Managed compute environments allow you to specify desired instance types at several levels of detail\. You can set up compute environments that use a particular type of instance, a particular model such as `c4.2xlarge` or `m4.10xlarge`, or simply specify that you want to use the newest instance types\. You can also specify the minimum, desired, and maximum number of vCPUs for the environment, along with the amount you are willing to pay for a Spot Instance as a percentage of the On\-Demand Instance price and a target set of VPC subnets\. AWS Batch will efficiently launch, manage, and terminate EC2 instances as needed\. You can also manage your own compute environments\. In this case you are responsible for setting up and scaling the instances in an Amazon ECS cluster that AWS Batch creates for you\. For more information, see [Compute Environments](compute_environments.md)\.

## Getting Started<a name="intro_getting_started"></a>

Get started with AWS Batch by creating a job definition, compute environment, and a job queue in the AWS Batch console\. 

The AWS Batch first\-run wizard gives you the option of creating a compute environment and a job queue and submitting a sample hello world job\. If you already have a Docker image you would like to launch in AWS Batch, you can create a job definition with that image and submit that to your queue instead\. For more information, see [Getting Started with AWS Batch](Batch_GetStarted.md)\.