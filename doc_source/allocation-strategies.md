# Allocation Strategies<a name="allocation-strategies"></a>

When a managed compute environment is created, AWS Batch will select an instance type from the `[instanceTypes](https://docs.aws.amazon.com/batch/latest/APIReference/API_ComputeResource.html#Batch-Type-ComputeResource-instanceTypes)` specified that best fits the needs of the jobs with a preference for the lowest\-cost instance type\. The allocation strategy defines behavior when AWS Batch cannot launch another instance of the current instance type\.

`BEST_FIT`  
AWS Batch will wait for additional instances to be available\. If there are not enough instances available, or if the user is hitting [Amazon EC2 service limits](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-resource-limits.html) then additional jobs will not be run until currently running jobs have completed\. This allocation strategy keeps costs lower but can limit scaling\.

`BEST_FIT_PROGRESSIVE`  
AWS Batch will select an additional instance type that is large enough to meet the requirements of the jobs in the queue, with a preference for an instance type with a lower cost\.

`SPOT_CAPACITY_OPTIMIZED`  
AWS Batch will select an additional instance type that is large enough to meet the requirements of the jobs in the queue, with a preference for an instance type that is less likely to be interrupted\. This allocation strategy is only available for Spot Instance compute resources\.

With both `BEST_FIT_PROGRESSIVE` and `SPOT_CAPACITY_OPTIMIZED` strategies, AWS Batch may need to go above `maxvCpus` to meet your capacity requirements\. In this event, AWS Batch will never go above `maxvCpus` by more than a single instance\.