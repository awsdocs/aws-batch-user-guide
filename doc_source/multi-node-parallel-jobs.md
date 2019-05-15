# Multi\-node Parallel Jobs<a name="multi-node-parallel-jobs"></a>

Multi\-node parallel jobs enable you to run single jobs that span multiple Amazon EC2 instances\. With AWS Batch multi\-node parallel jobs, you can run large\-scale, tightly coupled, high performance computing applications and distributed GPU model training without the need to launch, configure, and manage Amazon EC2 resources directly\. An AWS Batch multi\-node parallel job is compatible with any framework that supports IP\-based, internode communication, such as Apache MXNet, TensorFlow, Caffe2, or Message Passing Interface \(MPI\)\.

Multi\-node parallel jobs are submitted as a single job\. However, your job definition \(or job submission node overrides\) specifies the number of nodes to create for the job and what node groups to create\. Each multi\-node parallel job contains a **main node**, which is launched first\. After the main node is up, the child nodes are launched and started\. If the main node exits, the job is considered finished, and the child nodes are stopped\. For more information, see [Node Groups](#mnp-node-groups)\.

Multi\-node parallel job nodes are single\-tenant, meaning that only a single job container is run on each Amazon EC2 instance\.

The final job status \(`SUCCEEDED` or `FAILED`\) is determined by the final job status of the main node\. To get the status of a multi\-node parallel job, you can describe the job using the job ID that was returned when you submitted the job\. If you need the details for child nodes, then you must describe each child node individually\. Nodes are addressed using `#N` notation\. For example, to access the details of the second node of a job, you need to describe *aws\_batch\_job\_id*\#2 using the AWS Batch [DescribeJobs](https://docs.aws.amazon.com/batch/latest/APIReference/API_DescribeJobs.html) API action\. The `started`, `stoppedAt`, `statusReason`, and `exit` information for a multi\-node parallel job is populated from the main node\.

If you specify job retries, then a main node failure triggers another attempt; child node failures do not\. Each new attempt of a multi\-node parallel job updates the corresponding attempt of its associated child nodes\. 

To run multi\-node parallel jobs on AWS Batch, your application code must contain the frameworks and libraries necessary for distributed communication\.

## Environment Variables<a name="mnp-env-vars"></a>

At runtime, in addition to the standard environment variables that all AWS Batch jobs receive, each node is configured with the following environment variables that are specific to multi\-node parallel jobs:

`AWS_BATCH_JOB_MAIN_NODE_INDEX`  
This variable is set to the index number of the job's main node\. Your application code can compare the `AWS_BATCH_JOB_MAIN_NODE_INDEX` to the `AWS_BATCH_JOB_NODE_INDEX` on an individual node to determine if it is the main node\.

`AWS_BATCH_JOB_MAIN_NODE_PRIVATE_IPV4_ADDRESS`  
This variable is only set in multi\-node parallel job child nodes \(it is not present on the main node\)\. This variable is set to the private IPv4 address of the job's main node\. Your child node's application code can use this address to communicate with the main node\.

`AWS_BATCH_JOB_NODE_INDEX`  
This variable is set to the node index number of the node\. The node index begins at 0, and each node receives a unique index number\. For example, a multi\-node parallel job with 10 children has index values of 0\-9\.

`AWS_BATCH_JOB_NUM_NODES`  
This variable is set to the number of nodes that you have requested for your multi\-node parallel job\.

## Node Groups<a name="mnp-node-groups"></a>

A node group is an identical group of job nodes that all share the same container properties\. AWS Batch lets you specify up to five distinct node groups for each job\.

Each group can have its own container images, commands, environment variables, and so on\. For example, you can submit a job that requires a single `c4.xlarge` instance for the main node, and five `c4.xlarge` instance child nodes; each of these distinct node groups may specify different container images or commands to run for each job\. 

Alternatively, all of the nodes in your job can use a single node group, and your application code can differentiate node roles \(main node vs\. child node\) by comparing the `AWS_BATCH_JOB_MAIN_NODE_INDEX` environment variable against its own value for `AWS_BATCH_JOB_NODE_INDEX`\. You may have up to 1000 nodes in a single job\. This is the default limit for instances in an Amazon ECS cluster, which can be increased [on request](https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html)\.

**Note**  
Currently all node groups in a multi\-node parallel job must use the same instance type\.

## Job Lifecycle<a name="job-lifecycle"></a>

When you submit a multi\-node parallel job, the job enters the `SUBMITTED` status, and it waits for any job dependencies to finish\. Then the job moves to the `RUNNABLE` status, and AWS Batch provisions the instance capacity required to run your job and launches these instances\.

Each multi\-node parallel job contains a **main node**\. The main node is a single subtask that AWS Batch monitors to determine the outcome of the submitted multi node job\. The main node is launched first and it moves to the `STARTING` status\.

When the main node reaches the `RUNNING` status \(after the node's container is running\), the child nodes are launched and they also move to the `STARTING` status\. The child nodes come up in random order\. There are no guarantees on the timing or ordering of child node launch\. To ensure that the all the nodes of the jobs are in the `RUNNING` status \(after the node's container is running\), your application code can either query the AWS Batch API to get the main node and child node information, or coordinate within the application code to wait until all nodes are online before starting any distributed processing task\. The private IP address of the main node is available as the `AWS_BATCH_JOB_MAIN_NODE_PRIVATE_IPV4_ADDRESS` environment variable in each child node\. Your application code may use this information to coordinate and communicate data between each task\.

As individual nodes exit, they move to `SUCCEEDED` or `FAILED`, depending on their exit code\. If the main node exits, the job is considered finished, and all of the child nodes are stopped\. If a child node dies, AWS Batch does not take any action on the other nodes in the job\. If you do not want your job to continue with a reduced number of nodes, you must factor this into your application code to terminate or cancel the job\.

## Compute Environment Considerations<a name="mnp-ce"></a>

There are several things to consider when configuring compute environments to run multi\-node parallel jobs with AWS Batch\.
+ If you intend to submit multi\-node parallel jobs to a compute environment, consider creating a *cluster* placement group in a single Availability Zone and associating it with your compute resources\. This keeps your multi\-node parallel jobs on a logical grouping of instances in close proximity with high network flow potential\. For more information, see [Placement Groups](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/placement-groups.html) in the *Amazon EC2 User Guide for Linux Instances*\.
+ Multi\-node parallel jobs are not supported on compute environments that use Spot Instances\.
+ AWS Batch multi\-node parallel jobs use the Amazon ECS `awsvpc` network mode, which gives your multi\-node parallel job containers the same networking properties as Amazon EC2 instances\. Each multi\-node parallel job container gets its own elastic network interface, a primary private IP address, and an internal DNS hostname\. The network interface is created in the same VPC subnet as its host compute resource\. Any security groups that are applied to your compute resources are also applied to it\. For more information, see [Task Networking with the awsvpc Network Mode](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-networking.html) in the *Amazon Elastic Container Service Developer Guide*\.
+ Your compute environment may have no more than five security groups associated with it\.
+ The elastic network interfaces that are created and attached to your compute resources cannot be detached manually or modified by your account\. This is to prevent the accidental deletion of an elastic network interface that is associated with a running job\. To release the elastic network interfaces for a task, terminate the job\.
+ Your compute environment must have enough maximum vCPUs to support your multi\-node parallel job\.
+ Your Amazon EC2 instance limits must be able to satisfy the number of instances required to run your job\. For example, if your job requires 30 instances, but your account can only run 20 instances in a Region, your job gets stuck in the `RUNNABLE` status\.
+ If you specify an instance type for a node group in a multi\-node parallel job, your compute environment must be able to launch that instance type\.