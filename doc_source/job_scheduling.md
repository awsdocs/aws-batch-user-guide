# Job scheduling<a name="job_scheduling"></a>

The AWS Batch scheduler evaluates when, where, and how to run jobs that are submitted to a job queue\. If you don’t specify a scheduling policy when you create a job queue, the AWS Batch job scheduler defaults to a first\-in, first\-out \(FIFO\) strategy\. A FIFO strategy might cause important jobs to get “stuck” behind jobs that were submitted earlier\. By specifying a different scheduling policy, you can allocate compute resources according to your specific needs\. 

**Note**  
If you want to schedule the specific order that jobs are run in, use the `[dependsOn](https://docs.aws.amazon.com/batch/latest/APIReference/API_SubmitJob.html#Batch-SubmitJob-request-dependsOn)` parameter in [SubmitJob](https://docs.aws.amazon.com/batch/latest/APIReference/API_SubmitJob.html) to specify the dependencies for each job\.

If you create a scheduling policy and attach it to a job queue, fair share scheduling is turned on\. If the job queue has a scheduling policy, the scheduling policy determines the order that jobs are run in\. For more information, see [Scheduling policies](scheduling-policies.md)\.

## Share identifiers<a name="share-identifiers"></a>

You can use share identifiers to tag jobs and differentiate between users and workloads\. The AWS Batch scheduler tracks usage for each fair share identifier by using the `vcpu_used_over_time * overridden_ weight_factor`\) formula\. The scheduler picks jobs with the lowest usage from the share identifier\. You can use a fair share identifier without overriding it\.

**Note**  
Share identifiers are unique within a job queue and are not aggregated across job queues\. 

You can set scheduling priority to configure the order that jobs are run in on a share identifier\. Jobs with a higher scheduling priority are scheduled first\. If you don’t specify a scheduling policy, all jobs that are submitted to the job queue are scheduled in FIFO order\. When you submit a job, you can’t specify a share identifier or scheduling priority\.

**Note**  
Attached compute resources are allocated equally among all share identifiers unless explicitly overridden\.

## Fair share scheduling<a name="fair-share-scheduling"></a>

Fair share scheduling provides a set of controls to help schedule jobs\.

**Note**  
For more information about scheduling policy parameters, see [Scheduling policy parameters](scheduling-policy-parameters.md)\.
+ **Share decay seconds –** The period of time \(in seconds\) that the AWS Batch scheduler uses to calculate a fair share percentage for each fair share identifier\. A value of zero indicates that only current usage is measured\. A longer decay time gives more weight to time\.
+ **Compute reservation –** Prevents jobs in a single share identifier from using up all the resources that are attached to the job queue\. The reserved ratio is `computeReservation/100)^ActiveFairShares` where ActiveFairShares is the number of active fair share identifiers\. 
**Note**  
If a share identifier has jobs in a pre\-RUNNING state, it's considered active\. After the *`decay period specified in the scheduling policy + time in the order minutes`* expires, a share identifier is considered inactive\.
+ **Weight factor –** The weight factor for the share identifier\. The default value is 1\. A lower value lets jobs from the share identifier run or gives additional runtime to the share identifier\. For example, jobs that use a share identifier with a weight factor of 0\.125 \(1/8\) are assigned eight times the compute resources of jobs that use a share identifier with a weight factor of 1\.
**Note**  
You only need to define this attribute when you need to update the default weight factor of 1\.