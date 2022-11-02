# Allocation strategies<a name="allocation-strategies"></a>

When a managed compute environment is created, AWS Batch selects instance types from the `[instanceTypes](https://docs.aws.amazon.com/batch/latest/APIReference/API_ComputeResource.html#Batch-Type-ComputeResource-instanceTypes)` specified that best fit the needs of the jobs\. The allocation strategy defines behavior when AWS Batch needs additional capacity\. This parameter isn't applicable to jobs that run on Fargate resources\. Don't specify this parameter\.

`BEST_FIT` \(default\)  
AWS Batch selects an instance type that best fits the needs of the jobs with a preference for the lowest\-cost instance type\. If additional instances of the selected instance type aren't available, AWS Batch waits for the additional instances to be available\. If there aren't enough instances available, or if the user is reaching the [Amazon EC2 service quotas](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-resource-limits.html), then additional jobs don't run until currently running jobs are complete\. This allocation strategy keeps costs lower but can limit scaling\. If you're using Spot Fleets with `BEST_FIT`, the Spot Fleet IAM Role must be specified\. `BEST_FIT` isn't supported when updating compute environments\. For more information, see [Updating compute environments](updating-compute-environments.md)\.

`BEST_FIT_PROGRESSIVE`  
AWS Batch selects additional instance types that are large enough to meet the requirements of the jobs in the queue\. Instance types with a lower cost for each unit vCPU are preferred\. If additional instances of the previously selected instance types aren't available, AWS Batch selects new instance types\.

`SPOT_CAPACITY_OPTIMIZED`  
AWS Batch selects one or more instance types that are large enough to meet the requirements of the jobs in the queue\. Instance types that are less likely to be interrupted are preferred\. This allocation strategy is only available for Spot Instance compute resources\.

The `BEST_FIT_PROGRESSIVE` and `SPOT_CAPACITY_OPTIMIZED` strategies use On\-Demand or Spot Instances, and the `BEST_FIT` strategy uses Spot Instances\. However, AWS Batch might need to exceed `maxvCpus` to meet your capacity requirements\. In this event, AWS Batch never exceeds `maxvCpus` by more than a single instance\.