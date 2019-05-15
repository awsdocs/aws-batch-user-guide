# Compute Resource Memory Management<a name="memory-management"></a>

When the Amazon ECS container agent registers a compute resource into a compute environment, the agent must determine how much memory the compute resource has available to reserve for your jobs\. Because of platform memory overhead and memory occupied by the system kernel, this number is different than the installed memory amount that is advertised for Amazon EC2 instances\. For example, an `m4.large` instance has 8 GiB of installed memory\. However, this does not always translate to exactly 8192 MiB of memory available for jobs when the compute resource registers\.

If you specify 8192 MiB for the job, and none of your compute resources have 8192 MiB or greater of memory available to satisfy this requirement, then the job cannot be placed in your compute environment\. If you are using a managed compute environment, then AWS Batch must launch a larger instance type to accommodate the request\.

 The default AWS Batch compute resource AMI also reserves 32 MiB of memory for the Amazon ECS container agent and other critical system processes\. This memory is not available for job allocation\. For more information, see [Reserving System Memory](#ecs-reserved-memory)\.

The Amazon ECS container agent uses the Docker `ReadMemInfo()` function to query the total memory available to the operating system\. Linux provides command line utilities to determine the total memory\.

**Example \- Determine Linux total memory**  
The free command returns the total memory that is recognized by the operating system\.  

```
$ free -b
```
Example output for an `m4.large` instance running the Amazon ECS\-optimized Amazon Linux AMI\.  

```
             total       used       free     shared    buffers     cached
Mem:    8373026816  348180480 8024846336      90112   25534464  205418496
-/+ buffers/cache:  117227520 8255799296
```
This instance has 8373026816 bytes of total memory, which translates to 7985 MiB available for tasks\.

## Reserving System Memory<a name="ecs-reserved-memory"></a>

If you occupy all of the memory on a compute resource with your jobs, then it is possible that your jobs will contend with critical system processes for memory and possibly trigger a system failure\. The Amazon ECS container agent provides a configuration variable called `ECS_RESERVED_MEMORY`, which you can use to remove a specified number of MiB of memory from the pool that is allocated to your jobs\. This effectively reserves that memory for critical system processes\.

The default AWS Batch compute resource AMI reserves 32 MiB of memory for the Amazon ECS container agent and other critical system processes\.

## Viewing Compute Resource Memory<a name="viewing-memory"></a>

You can view how much memory a compute resource registers with in the Amazon ECS console \(or with the [DescribeContainerInstances](https://docs.aws.amazon.com/AmazonECS/latest/APIReference/API_DescribeContainerInstances.html) API operation\)\. If you are trying to maximize your resource utilization by providing your jobs as much memory as possible for a particular instance type, you can observe the memory available for that compute resource and then assign your jobs that much memory\.

**To view compute resource memory**

1. Open the Amazon ECS console at [https://console\.aws\.amazon\.com/ecs/](https://console.aws.amazon.com/ecs/)\.

1. Choose the cluster that hosts your compute resources to view\. The cluster name for your compute environment begins with your compute environment name\.

1. Choose **ECS Instances**, and select a compute resource from the **Container Instance** column to view\.

1. The **Resources** section shows the registered and available memory for the compute resource\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/batch/latest/userguide/images/instance-memory.png)

   The **Registered** memory value is what the compute resource registered with Amazon ECS when it was first launched, and the **Available** memory value is what has not already been allocated to jobs\.