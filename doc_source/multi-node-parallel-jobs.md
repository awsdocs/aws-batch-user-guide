# Multi\-node parallel jobs<a name="multi-node-parallel-jobs"></a>

You can use multi\-node parallel jobs to run single jobs that span multiple Amazon EC2 instances\. With AWS Batch multi\-node parallel jobs, you can run large\-scale, high\-performance computing applications and distributed GPU model training without the need to launch, configure, and manage Amazon EC2 resources directly\. An AWS Batch multi\-node parallel job is compatible with any framework that supports IP\-based, internode communication\. Examples include Apache MXNet, TensorFlow, Caffe2, or Message Passing Interface \(MPI\)\.

Multi\-node parallel jobs are submitted as a single job\. However, your job definition \(or job submission node overrides\) specifies the number of nodes to create for the job and what node groups to create\. Each multi\-node parallel job contains a **main node**, which is launched first\. After the main node is up, the child nodes are launched and started\. The job is finished only if the main node exits\. All child nodes are then stopped\. For more information, see [Node groups](#mnp-node-groups)\.

Multi\-node parallel job nodes are single\-tenant\. This means that only a single job container is run on each Amazon EC2 instance\.

The final job status \(`SUCCEEDED` or `FAILED`\) is determined by the final job status of the main node\. To get the status of a multi\-node parallel job, describe the job by using the job ID that was returned when you submitted the job\. If you need the details for child nodes, describe each child node individually\. You can address nodes using the `#N` notation \(starting with 0\)\. For example, to access the details of the second node of a job, describe *aws\_batch\_job\_id*\#1 using the AWS Batch [DescribeJobs](https://docs.aws.amazon.com/batch/latest/APIReference/API_DescribeJobs.html) API operation\. The `started`, `stoppedAt`, `statusReason`, and `exit` information for a multi\-node parallel job is populated from the main node\.

If you specify job retries, a main node failure causes another attempt to occur\. Child node failures don't cause more attempts to occur\. Each new attempt of a multi\-node parallel job updates the corresponding attempt of its associated child nodes\. 

To run multi\-node parallel jobs on AWS Batch, your application code must contain the frameworks and libraries that are necessary for distributed communication\.

## Environment variables<a name="mnp-env-vars"></a>

At runtime, each node is configured the standard environment variables that all AWS Batch jobs receive\. In addition, the nodes are configured with the following environment variables that are specific to multi\-node parallel jobs:

`AWS_BATCH_JOB_MAIN_NODE_INDEX`  
This variable is set to the index number of the job's main node\. Your application code can compare the `AWS_BATCH_JOB_MAIN_NODE_INDEX` to the `AWS_BATCH_JOB_NODE_INDEX` on an individual node to determine if it's the main node\.

`AWS_BATCH_JOB_MAIN_NODE_PRIVATE_IPV4_ADDRESS`  
This variable is only set in multi\-node parallel job child nodes\. This variable isn't present on the main node\. This variable is set to the private IPv4 address of the job's main node\. Your child node's application code can use this address to communicate with the main node\.

`AWS_BATCH_JOB_NODE_INDEX`  
This variable is set to the node index number of the node\. The node index begins at 0, and each node receives a unique index number\. For example, a multi\-node parallel job with 10 children has index values of 0\-9\.

`AWS_BATCH_JOB_NUM_NODES`  
This variable is set to the number of nodes that you have requested for your multi\-node parallel job\.

## Node groups<a name="mnp-node-groups"></a>

A node group is an identical group of job nodes that all share the same container properties\. You can use AWS Batch to specify up to five distinct node groups for each job\.

Each group can have its own container images, commands, environment variables, and so on\. For example, you can submit a job that requires a single `c5.xlarge` instance for the main node and five `c5.xlarge` instance child nodes\. Each of these distinct node groups may specify different container images or commands to run for each job\. 

Alternatively, all of the nodes in your job can use a single node group\. Moreover, your application code can differentiate node roles such as the main node and child node\. It does this by comparing the `AWS_BATCH_JOB_MAIN_NODE_INDEX` environment variable against its own value for `AWS_BATCH_JOB_NODE_INDEX`\. You can have up to 1,000 nodes in a single job\. This is the default limit for instances in an Amazon ECS cluster\. You can [request to increase this limit](https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html)\.

**Note**  
Currently all node groups in a multi\-node parallel job must use the same instance type\.

## Job lifecycle<a name="job-lifecycle"></a>

When you submit a multi\-node parallel job, the job enters the `SUBMITTED` status\. Then, the job waits for any job dependencies to finish\. The job also moves to the `RUNNABLE` status\. Last, AWS Batch provisions the instance capacity that's required to run your job and launches these instances\.

Each multi\-node parallel job contains a **main node**\. The main node is a single subtask that AWS Batch monitors to determine the outcome of the submitted multi node job\. The main node is launched first and it moves to the `STARTING` status\. The timeout value specified in the `attemptDurationSeconds` parameter applies to the whole job and not to the nodes\.

When the main node reaches the `RUNNING` status after the node's container is running, the child nodes are launched and they also move to the `STARTING` status\. The child nodes come up in random order\. There are no guarantees on the timing or ordering of child node launch\. To ensure that the all the nodes of the jobs are in the `RUNNING` status after the node's container is running, your application code can query the AWS Batch API to get the main node and child node information\. Alternatively, the application code can wait until all nodes are online before starting any distributed processing task\. The private IP address of the main node is available as the `AWS_BATCH_JOB_MAIN_NODE_PRIVATE_IPV4_ADDRESS` environment variable in each child node\. Your application code may use this information to coordinate and communicate data between each task\.

As individual nodes exit, they move to `SUCCEEDED` or `FAILED`, depending on their exit code\. If the main node exits, the job is considered finished, and all of the child nodes are stopped\. If a child node dies, AWS Batch doesn't take any action on the other nodes in the job\. If you don't want your job to continue with a reduced number of nodes, you must factor this into your application code\. Doing this terminates or cancels the job\.

## Compute environment considerations<a name="mnp-ce"></a>

There are several things to consider when configuring compute environments to run multi\-node parallel jobs with AWS Batch\.
+ Multi\-node parallel jobs aren't supported on `UNMANAGED` compute environments\.
+ If you want to submit multi\-node parallel jobs to a compute environment, create a *cluster* placement group in a single Availability Zone and associate it with your compute resources\. This keeps your multi\-node parallel jobs on a logical grouping of instances close with high network flow potential\. For more information, see [Placement Groups](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/placement-groups.html) in the *Amazon EC2 User Guide for Linux Instances*\.
+ Multi\-node parallel jobs aren't supported on compute environments that use Spot Instances\.
+ AWS Batch multi\-node parallel jobs use the Amazon ECS `awsvpc` network mode, which gives your multi\-node parallel job containers the same networking properties as Amazon EC2 instances\. Each multi\-node parallel job container gets its own elastic network interface, a primary private IP address, and an internal DNS hostname\. The network interface is created in the same VPC subnet as its host compute resource\. Any security groups that are applied to your compute resources are also applied to it\. For more information, see [Task Networking with the awsvpc Network Mode](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-networking.html) in the *Amazon Elastic Container Service Developer Guide*\.
+ Your compute environment might have no more than five security groups associated with it\.
+ The `awsvpc` network mode doesn't provide the elastic network interfaces for multi\-node parallel jobs with public IP addresses\. To access the internet, your compute resources must be launched in a private subnet that is configured to use a NAT gateway\. For more information, see [NAT Gateways](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) in the *Amazon VPC User Guide*\. Internode communication must use the private IP address or DNS hostname for the node\. Multi\-node parallel jobs that run on compute resources within public subnets don't have outbound network access\. To create a VPC with private subnets and a NAT gateway, see [Creating a virtual private cloud ](create-public-private-vpc.md)\.
+ The elastic network interfaces that are created and attached to your compute resources can't be detached manually or modified by your account\. This is to prevent the accidental deletion of an elastic network interface that's associated with a running job\. To release the elastic network interfaces for a task, terminate the job\.
+ Your compute environment must have enough maximum vCPUs to support your multi\-node parallel job\.
+ Your Amazon EC2 instance quota include the number of instances that's required to run your job\. For example, suppose that your job requires 30 instances, but your account can only run 20 instances in a Region\. Then, your job will get stuck in `RUNNABLE` status\.
+ If you specify an instance type for a node group in a multi\-node parallel job, your compute environment must launch that instance type\.