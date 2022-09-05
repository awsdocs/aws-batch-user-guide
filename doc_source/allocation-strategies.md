# Allocation strategies<a name="allocation-strategies"></a>

When a managed compute environment is created, AWS Batch selects instance types from the `[instanceTypes](https://docs.aws.amazon.com/batch/latest/APIReference/API_ComputeResource.html#Batch-Type-ComputeResource-instanceTypes)` specified that best fit the needs of the jobs\. The allocation strategy defines behavior when AWS Batch needs additional capacity\. This parameter isn't applicable to jobs running on Fargate resources, and shouldn't be specified\.

`BEST_FIT` \(default\)  
AWS Batch selects an instance type that best fits the needs of the jobs with a preference for the lowest\-cost instance type\. If additional instances of the selected instance type aren't available, AWS Batch waits for the additional instances to be available\. If there aren't enough instances available, or if the user is hitting [Amazon EC2 service limits](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-resource-limits.html) then additional jobs don't run until currently running jobs have completed\. This allocation strategy keeps costs lower but can limit scaling\. If you're using Spot Fleets with `BEST_FIT`, the Spot Fleet IAM Role must be specified\. `BEST_FIT` isn't supported when updating compute environments\. For more information, see [Updating compute environments](updating-compute-environments.md)\.

`BEST_FIT_PROGRESSIVE`  
AWS Batch selects additional instance types that are large enough to meet the requirements of the jobs in the queue\. It has a preference for instance types with a lower cost for each unit vCPU\. If additional instances of the previously selected instance types aren't available, AWS Batch selects new instance types\.

`SPOT_CAPACITY_OPTIMIZED`  
AWS Batch selects one or more instance types that are large enough to meet the requirements of the jobs in the queue, with a preference for instance types that are less likely to be interrupted\. This allocation strategy is only available for Spot Instance compute resources\.

With both `BEST_FIT_PROGRESSIVE` and `SPOT_CAPACITY_OPTIMIZED` strategies, AWS Batch might need to exceed `maxvCpus` to meet your capacity requirements\. In this event, AWS Batch never exceeds `maxvCpus` by more than a single instance\.