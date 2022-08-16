# GPU jobs<a name="gpu-jobs"></a>

GPU jobs help you to run jobs that use an instance's GPUs\.

The following Amazon EC2 GPU\-based instance types are supported\. For more information, see [Amazon EC2 G3 Instances](http://aws.amazon.com/ec2/instance-types/g3/), [Amazon EC2 G4 Instances](http://aws.amazon.com/ec2/instance-types/g4/), [Amazon EC2 G5 Instances](http://aws.amazon.com/ec2/instance-types/g5/), [Amazon EC2 P2 Instances](http://aws.amazon.com/ec2/instance-types/p2/), [Amazon EC2 P3 Instances](http://aws.amazon.com/ec2/instance-types/p3/), and [Amazon EC2 P4d Instances](http://aws.amazon.com/ec2/instance-types/p4/)\.


| Instance type | GPUs | GPU memory | vCPUs | Memory | Network bandwidth | 
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
| g5\.xlarge | 1 | 24 GiB | 4 | 16 GiB | Up to 10 Gbps | 
| g5\.2xlarge | 1 | 24 GiB | 8 | 32 GiB | Up to 10 Gbps | 
| g5\.4xlarge | 1 | 24 GiB | 16 | 64 GiB | Up to 25 Gbps | 
| g5\.8xlarge | 1 | 24 GiB | 32 | 128 GiB | 25 Gbps | 
| g5\.16xlarge | 1 | 24 GiB | 64 | 256 GiB | 25 Gbps | 
| g5\.12xlarge | 4 | 96 GiB | 48 | 192 GiB | 40 Gbps | 
| g5\.24xlarge | 4 | 96 GiB | 96 | 384 GiB | 50 Gbps | 
| g5\.48xlarge | 8 | 192 GiB | 192 | 768 GiB | 100 Gbps | 
| p2\.xlarge | 1 | 12 GiB | 4 | 61 GiB | High | 
| p2\.8xlarge | 8 | 96 GiB | 32 | 488 GiB | 10 Gbps | 
| p2\.16xlarge | 16 | 192 GiB | 64 | 732 GiB | 20 Gbps | 
| p3\.2xlarge | 1 | 16 GiB | 8 | 61 GiB | Up to 10 Gbps | 
| p3\.8xlarge | 4 | 64 GiB | 32 | 244 GiB | 10 Gbps | 
| p3\.16xlarge | 8 | 128 GiB | 64 | 488 GiB | 25 Gbps | 
| p3dn\.24xlarge | 8 | 256 GiB | 96 | 768 GiB | 100 Gbps | 
| p4d\.24xlarge | 8 | 320 GiB | 96 | 1152 GiB | 4x100 Gbps | 

**Note**  
Only instance types that support a NVIDIA GPU and use an x86\_64 architecture are supported for GPU jobs in AWS Batch\. For example, the [http://aws.amazon.com/ec2/instance-types/g4/#Amazon_EC2_G4ad_instances](http://aws.amazon.com/ec2/instance-types/g4/#Amazon_EC2_G4ad_instances) and [http://aws.amazon.com/ec2/instance-types/g5g/](http://aws.amazon.com/ec2/instance-types/g5g/) instance families are not supported\.

The [resourceRequirements](job_definition_parameters.md#ContainerProperties-resourceRequirements) parameter for the job definition specifies the number of GPUs to be pinned to the container\. This number of GPUs isn't available to any other job that runs on that instance for the duration of that job\. All instance types in a compute environment that run GPU jobs must be from the `p2`, `p3`, `p4`, `g3`, `g3s`, `g4`, or `g5` instance families\. If this isn't done a GPU job might get stuck in the `RUNNABLE` status\.

Jobs that don't use the GPUs can be run on GPU instances\. However, they might cost more to run on the GPU instances than on similar non\-GPU instances\. Depending on the specific vCPU, memory, and time needed, these non\-GPU jobs might block GPU jobs from running\.