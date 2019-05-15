# GPU Jobs<a name="gpu-jobs"></a>

GPU jobs enable you to run jobs that use an instance's GPU\(s\)\.

The following Amazon EC2 GPU\-based instance types are supported\. For more information, see [Amazon EC2 P2 Instances](http://aws.amazon.com/ec2/instance-types/p2/) and [Amazon EC2 P3 Instances](http://aws.amazon.com/ec2/instance-types/p3/)\.


| Instance type | GPUs | GPU Memory \(GiB\) | vCPUs | Memory \(GiB\) | Network Bandwidth | 
| --- | --- | --- | --- | --- | --- | 
| p2\.xlarge | 1 | 12 | 4 | 61 | High | 
| p2\.8xlarge | 8 | 96 | 32 | 488 | 10 Gbps | 
| p2\.16xlarge | 16 | 192 | 64 | 732 | 20 Gbps | 
| p3\.2xlarge | 1 | 16 | 8 | 61 | Up to 10 Gbps | 
| p3\.8xlarge | 4 | 64 | 32 | 244 | 10 Gbps | 
| p3\.16xlarge | 8 | 128 | 64 | 488 | 25 Gbps | 
| p3dn\.24xlarge | 8 | 256 | 96 | 768 | 100 Gbps | 

The [resourceRequirements](https://docs.aws.amazon.com/batch/latest/userguide/job_definition_parameters.html#ContainerDefinition-resourceRequirements) parameter for the job definition specifies the number of GPUs to be pinned to the container and not available to any other job running on that instance for the duration of that job\. All instance types in a compute environment that will run GPU jobs should be from the `p2` or `p3` instance families\. If this is not done a GPU job could get stuck in the `RUNNABLE` status\.

Jobs that do not use the GPUs can be run on GPU enabled instances but they may cost more to run on the GPU enabled instances than on similar non\-GPU enabled instances\. Depending on the specific vCPU, memory, and time needed, these non\-GPU jobs may block GPU jobs from running\.