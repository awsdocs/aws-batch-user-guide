# Job queue parameters<a name="job_queue_parameters"></a>

Job queues are split into four basic components: the name, state, and priority of the job queue, and the compute environment order\.

**Topics**
+ [Job queue name](#job_queue_name)
+ [Priority](#job_queue_priority)
+ [Scheduling policy](#job_queue_scheduling_policy)
+ [State](#job_queue_state)
+ [Compute environment order](#job_queue_compute_environment_order)
+ [Tags](#job_queue_tags)

## Job queue name<a name="job_queue_name"></a>

`jobQueueName`  
The name for your job queue\. Up to 128 letters \(uppercase and lowercase\), numbers, and underscores are allowed\.  
Type: String  
Required: Yes

## Priority<a name="job_queue_priority"></a>

`priority`  
The priority of the job queue\. Job queues with a higher priority \(or a higher integer value for the `priority` parameter\) are evaluated first when associated with same compute environment\. Priority is determined in descending order, for example, a job queue with a priority value of 10 is given scheduling preference over a job queue with a priority value of 1\. All of the compute environments must be either EC2 \(`EC2` or `SPOT`\) or Fargate \(`FARGATE` or `FARGATE_SPOT`\); EC2 and Fargate compute environments can't be mixed\.  
Type: Integer  
Required: Yes

## Scheduling policy<a name="job_queue_scheduling_policy"></a>

`schedulingPolicyArn`  
The Amazon Resource Name \(ARN\) of the scheduling policy for the job queue\. Job queues that don't have a scheduling policy are scheduled in a first\-in, first\-out \(FIFO\) model\. After a job queue has a scheduling policy, it can be replaced but can't be removed\. A job queue without a scheduling policy is scheduled as a FIFO job queue and can't have a scheduling policy added\. Jobs queues with a scheduling policy can have a maximum of 500 active fair share identifiers\. When the limit has been reached, submissions of any jobs that add a new fair share identifier fail\.  
Type: String  
Required: No

## State<a name="job_queue_state"></a>

`state`  
The state of the job queue\. If the job queue state is `ENABLED` \(the default value\), it can accept jobs\. If the job queue state is `DISABLED`, new jobs can't be added to the queue, but jobs already in the queue can finish\.  
Type: String  
Valid values: `ENABLED` \| `DISABLED`  
Required: No

## Compute environment order<a name="job_queue_compute_environment_order"></a>

`computeEnvironmentOrder`  
The set of compute environments mapped to a job queue and their order relative to each other\. The job scheduler uses this parameter to determine which compute environment should run a specific job\. Compute environments must be in the `VALID` state before you can associate them with a job queue\. You can associate up to three compute environments with a job queue\. All of the compute environments must be either EC2 \(`EC2` or `SPOT`\) or Fargate \(`FARGATE` or `FARGATE_SPOT`\); EC2 and Fargate compute environments can't be mixed\.  
All compute environments that are associated with a job queue must share the same architecture\. AWS Batch doesn't support mixing compute environment architecture types in a single job queue\.
Type: Array of [ComputeEnvironmentOrder](https://docs.aws.amazon.com/batch/latest/APIReference/API_ComputeEnvironmentOrder.html) objects  
Required: Yes    
`computeEnvironment`  
The Amazon Resource Name \(ARN\) of the compute environment\.  
Type: String  
Required: Yes  
`order`  
The order of the compute environment\. Compute environments are tried in ascending order\. For example, if two compute environments are associated with a job queue, the compute environment with a lower `order` integer value is tried for job placement first\.

## Tags<a name="job_queue_tags"></a>

`tags`  
Key\-value pair tags to associate with the job queue\. For more information, see [Tagging your AWS Batch resources](using-tags.md)\.  
Type: String to string map  
Required: No