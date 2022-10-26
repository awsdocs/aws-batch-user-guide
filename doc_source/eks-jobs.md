# Amazon EKS jobs<a name="eks-jobs"></a>

A job is the smallest unit of work in AWS Batch\. An AWS Batch job on Amazon EKS has a one\-to\-one mapping to a Kubernetes pod\. An AWS Batch job definition is a template for an AWS Batch job\. When you submit an AWS Batch job, you reference a job definition, target a job queue, and provide a name for a job\. In the job definition of an AWS Batch job on Amazon EKS, the [eksProperties](https://docs.aws.amazon.com/batch/latest/APIReference/API_EksProperties.html) parameter defines the set of parameters that an AWS Batch on Amazon EKS job supports\. In a [SubmitJob](https://docs.aws.amazon.com/batch/latest/APIReference/API_SubmitJob.html) request, the [eksPropertiesOverride](https://docs.aws.amazon.com/batch/latest/APIReference/API_EksPropertiesOverride.html) parameter allows for overrides to some common parameters\. This way, you can use templates of job definitions for multiple jobs\. When a job is dispatched to your Amazon EKS cluster, AWS Batch transforms the job into a `podspec` \(`Kind: Pod`\)\. The `podspec` uses some additional AWS Batch parameters to ensure that jobs are scaled and scheduled correctly\. AWS Batch combines labels and taints to ensure jobs run only on AWS Batch managed nodes and that other pods don't run on those nodes\.

**Important**  
If the `hostNetwork` parameter isn't explicitly set in an Amazon EKS job definition, the pod networking mode in AWS Batch defaults to host mode\. More specifically, the following settings are applied: `hostNetwork=true` and `dnsPolicy=ClusterFirstWithHostNet`\.
AWS Batch cleans up job pods soon after a pod completes its job\. To see pod application logs, configure a logging service for your cluster\. For more information, see [Use CloudWatch Logs to monitor AWS Batch on Amazon EKS jobs](batch-eks-cloudwatch-logs.md)\.

## Map a running job to a pod and a node<a name="eks-jobs-map-running-job"></a>

The `podProperties` of a running job have `podName` and `nodeName` parameters set for the current job attempt\. Use the [DescribeJobs](https://docs.aws.amazon.com/batch/latest/APIReference/API_DescribeJobs.html) API operation to view these parameters\.

The following is example output\.

```
$ aws batch describe-jobs --job 2d044787-c663-4ce6-a6fe-f2baf7e51b04
{
 "jobs": [
  {
   "status": "RUNNING",
   "jobArn": "arn:aws:batch:us-east-1:123456789012:job/2d044787-c663-4ce6-a6fe-f2baf7e51b04",
   "jobDefinition": "arn:aws:batch:us-east-1:123456789012:job-definition/MyJobOnEks_SleepWithRequestsOnly:1",
   "jobQueue": "arn:aws:batch:us-east-1:123456789012:job-queue/My-Eks-JQ1",
   "jobId": "2d044787-c663-4ce6-a6fe-f2baf7e51b04",
   "eksProperties": {
    "podProperties": {
     "nodeName": "ip-192-168-55-175.ec2.internal",
     "containers": [
      {
       "image": "public.ecr.aws/amazonlinux/amazonlinux:2",
       "resources": {
        "requests": {
         "cpu": "1",
         "memory": "1024Mi"
        }
       }
      }
     ],
     "podName": "aws-batch.b0aca953-ba8f-3791-83e2-ed13af39428c"
    }
   }
  }
 ]
}
```

For a job with retries enabled, the `podName` and `nodeName` of every completed attempt is in the `eksAttempts` list parameter of the [DescribeJobs](https://docs.aws.amazon.com/batch/latest/APIReference/API_DescribeJobs.html) API operation\. The `podName` and `nodeName` of the current running attempt are in the `podProperties` object\.

## How to map a running pod back to its job<a name="eks-jobs-map-running-pod-to-job"></a>

A pod has labels that indicate the `jobId` and `uuid` of the compute environment that it belongs to\. AWS Batch injects environment variables so the job’s runtime can reference job information\. For more information, see [AWS Batch job environment variables](job_env_vars.md)\. You can view this information by running the following command\. The output is as follows\.

```
$ kubectl describe pod aws-batch.14638eb9-d218-372d-ba5c-1c9ab9c7f2a1 -n my-aws-batch-namespace
Name:         aws-batch.14638eb9-d218-372d-ba5c-1c9ab9c7f2a1
Namespace:    my-aws-batch-namespace
Priority:     0
Node:         ip-192-168-45-88.ec2.internal/192.168.45.88
Start Time:   Wed, 26 Oct 2022 00:30:48 +0000
Labels:       batch.amazonaws.com/compute-environment-uuid=5c19160b-d450-31c9-8454-86cf5b30548f
              batch.amazonaws.com/job-id=f980f2cf-6309-4c77-a2b2-d83fbba0e9f0
              batch.amazonaws.com/node-uid=a4be5c1d-9881-4524-b967-587789094647
...
Status:       Running
IP:           192.168.45.88
IPs:
  IP:  192.168.45.88
Containers:
  default:
    Image:         public.ecr.aws/amazonlinux/amazonlinux:2
    ...
    Environment:
      AWS_BATCH_JOB_KUBERNETES_NODE_UID:  a4be5c1d-9881-4524-b967-587789094647
      AWS_BATCH_JOB_ID:                   f980f2cf-6309-4c77-a2b2-d83fbba0e9f0
      AWS_BATCH_JQ_NAME:                  My-Eks-JQ1
      AWS_BATCH_JOB_ATTEMPT:              1
      AWS_BATCH_CE_NAME:                  My-Eks-CE1

...
```

**Features that AWS Batch Amazon EKS jobs support**

These are the AWS Batch specific features that are also common to Kubernetes jobs that run on Amazon EKS:
+ [Job dependencies](job_dependencies.md)
+ [Array jobs](array_jobs.md)
+ [Job timeouts](job_timeouts.md)
+ [Automated job retries](job_retries.md)
+ [Fair share scheduling](job_scheduling.md#fair-share-scheduling)

**Kubernetes`Secrets` and `ServiceAccounts`**  
AWS Batch supports referencing Kubernetes `Secrets` and `ServiceAccounts`\. You can configure pods to use Amazon EKS IAM roles for service accounts\. For more information, see [Configuring pods to use a Kubernetes service account](https://docs.aws.amazon.com/eks/latest/userguide/pod-configuration.html) in the [https://docs.aws.amazon.com/eks/latest/userguide/](https://docs.aws.amazon.com/eks/latest/userguide/)\.

**Related documents**
+ [Memory and vCPU considerations for AWS Batch on Amazon EKS](memory-cpu-batch-eks.md)
+ [To create a GPU\-based job on EKS resources](run-eks-gpu-workload.md)
+ [Jobs stuck in a `RUNNABLE` status](troubleshooting.md#job_stuck_in_runnable)