# GPU Jobs<a name="gpu-jobs"></a>

GPU jobs enable you to run jobs that use an instance's GPU\(s\)\.

The following Amazon EC2 GPU\-based instance types are supported\. For more information, see [Amazon EC2 G3 Instances](http://aws.amazon.com/ec2/instance-types/g3/), [Amazon EC2 G4 Instances](http://aws.amazon.com/ec2/instance-types/g4/), [Amazon EC2 P2 Instances](http://aws.amazon.com/ec2/instance-types/p2/), and [Amazon EC2 P3 Instances](http://aws.amazon.com/ec2/instance-types/p3/)\.


| Instance type | GPUs | GPU Memory | vCPUs | Memory | Network Bandwidth | 
| --- | --- | --- | --- | --- | --- | 
| g3s\.xlarge | 1 | 8 GiB | 4 | 30\.5 GiB | 10 Gbps | 
| g3\.4xlarge | 1 | 8 GiB | 16 | 122 GiB | Up to 10 Gbps | 
| g3\.8xlarge | 2 | 16 GiB | 32 | 244 GiB | 10 Gbps | 
| g3\.16xlarge | 4 | 32 GiB | 64 | 488 GiB | 25 Gbps | 
| g4dn\.xlarge | 1 | 16 GiB | 4 | 16 GiB | Up to 25 Gbps | 
| g4dn\.2xlarge | 1 | 16 GiB | 8 | 32 GiB | Up to 25 Gbps | 
| g4dn\.4xlarge | 1 | 16 GiB | 16 | 64 GiB | Up to 25 Gbps | 
| g4dn\.8xlarge | 1 | 16 GiB | 32 | 128 GiB | 50 Gbps | 
| g4dn\.12xlarge | 4 | 64 GiB | 48 | 192 GiB | 50 Gbps | 
| g4dn\.16xlarge | 1 | 16 GiB | 64 | 256 GiB | 50 Gbps | 
| p2\.xlarge | 1 | 12 GiB | 4 | 61 GiB | High | 
| p2\.8xlarge | 8 | 96 GiB | 32 | 488 GiB | 10 Gbps | 
| p2\.16xlarge | 16 | 192 GiB | 64 | 732 GiB | 20 Gbps | 
| p3\.2xlarge | 1 | 16 GiB | 8 | 61 GiB | Up to 10 Gbps | 
| p3\.8xlarge | 4 | 64 GiB | 32 | 244 GiB | 10 Gbps | 
| p3\.16xlarge | 8 | 128 GiB | 64 | 488 GiB | 25 Gbps | 
| p3dn\.24xlarge | 8 | 256 GiB | 96 | 768 GiB | 100 Gbps | 

The [resourceRequirements](https://docs.aws.amazon.com/batch/latest/userguide/job_definition_parameters.html#ContainerDefinition-resourceRequirements) parameter for the job definition specifies the number of GPUs to be pinned to the container and not available to any other job running on that instance for the duration of that job\. All instance types in a compute environment that will run GPU jobs should be from the `p2`, `p3`, `g3`, `g3s`, or `g4` instance families\. If this is not done a GPU job could get stuck in the `RUNNABLE` status\.

Jobs that do not use the GPUs can be run on GPU enabled instances but they may cost more to run on the GPU enabled instances than on similar non\-GPU enabled instances\. Depending on the specific vCPU, memory, and time needed, these non\-GPU jobs may block GPU jobs from running\.