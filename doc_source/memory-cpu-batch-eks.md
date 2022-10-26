# Memory and vCPU considerations for AWS Batch on Amazon EKS<a name="memory-cpu-batch-eks"></a>

In AWS Batch on Amazon Elastic Kubernetes Service, you can specify the resources that are made available to a container\. For example, you can specify `requests` or `limits` values for vCPU and memory resources:

The following are constraints for specifying vCPU resources:
+ At least one vCPU `requests` or `limits` value must be specified\.
+ One vCPU unit is equivalent to one physical or virtual core\. 
+ The vCPU value must be entered in whole numbers or in increments of \.25\. 
+ The smallest valid vCPU value is \.25\.
+ The `requests` value must be less than or equal to the `limits` value if both are specified\. This lets you configure both soft and hard vCPU configurations\.
+ vCPU values can't be specified in milliCPU form\. For example, `100m` isn't a valid value\.
+ AWS Batch uses the `requests` value for scaling decisions\. If a `requests` value is not specified, the `limits` value is copied to the `requests` value\.

The following are constraints for specifying memory resources:
+ At least one memory `requests `or `limits` value must be specified\.
+ Memory values must be in mebibytes \(MiBs\)\.
+ The `requests` value must be equal to the `limits` value if both are specified\.
+ AWS Batch uses the `requests` value for scaling decisions\. If a `requests` value is not specified, the `limits` value is copied to the `requests` value\.

The following are constraints for specifying GPU resources:
+ The `requests` value must be equal to the `limits` value if both are specified\.
+ AWS Batch uses the `requests` value for scaling decisions\. If a `requests` value is not specified, the `limits` value is copied to the `requests` value\.

## Example job definitions<a name="memory-cpu-batch-eks-example-job-definition"></a>

The following AWS Batch on Amazon EKS job definition configures soft vCPU shares\. This lets AWS Batch on Amazon EKS use all of the vCPU capacity for the instance type\. However, if there are other jobs running, the job is allocated a maximum of `2` vCPUs\. Memory is limited to 2 GB:

```
{
    "jobDefinitionName": "MyJobOnEks_Sleep",
    "type": "container",
    "eksProperties": {
        "podProperties": {
            "containers": [
                {
                    "image": "public.ecr.aws/amazonlinux/amazonlinux:2",
                    "command": ["sleep", "60"],
                    "resources": {
                        "requests": {
                            "cpu": "2",
                            "memory": "2048Mi"
                        }
                    }
                }
            ]
        }
    }
}
```

The following AWS Batch on Amazon EKS job definition has a `request` value of `1` and allocates a maximum of `4` vCPUs to the job:

```
{
    "jobDefinitionName": "MyJobOnEks_Sleep",
    "type": "container",
    "eksProperties": {
        "podProperties": {
            "containers": [
                {
                    "image": "public.ecr.aws/amazonlinux/amazonlinux:2",
                    "command": ["sleep", "60"],
                    "resources": {
                        "requests": {
                            "cpu": "1"
                        },
                        "limits": {
                            "cpu": "4",
                            "memory": "2048Mi"
                        }
                    }
                }
            ]
        }
    }
}
```

The following AWS Batch on Amazon EKS job definition sets a vCPU `limits` value of `1` and a memory `limits` value of 1 GB:

```
{
    "jobDefinitionName": "MyJobOnEks_Sleep",
    "type": "container",
    "eksProperties": {
        "podProperties": {
            "containers": [
                {
                    "image": "public.ecr.aws/amazonlinux/amazonlinux:2",
                    "command": ["sleep", "60"],
                    "resources": {
                        "limits": {
                            "cpu": "1",
                            "memory": "1024Mi"
                        }
                    }
                }
            ]
        }
    }
}
```

 When AWS Batch translates an AWS Batch on Amazon EKS job into an Amazon EKS pod, AWS Batch copies the`limits` value to the `requests` value if a `requests` value is not specified\. When you submit the example job definition above, the pod `spec` is:

```
apiVersion: v1
kind: Pod
...
spec:
  ...
  containers:
    - command:
        - sleep
        - 60
      image: public.ecr.aws/amazonlinux/amazonlinux:2
      resources:
        limits:
          cpu: 1
          memory: 1024Mi
        requests:
          cpu: 1
          memory: 1024Mi
      ...
```

## Node CPU and memory reservations<a name="memory-cpu-batch-eks-node-cpu-memory-reservations"></a>

 AWS Batch relies on the default logic of the `bootstrap.sh` file for vCPU and memory reservations\. For more information about the `bootstrap.sh` file, see [bootstrap\.sh](https://github.com/awslabs/amazon-eks-ami/blob/master/files/bootstrap.sh)\. Consider the following examples when you size your vCPU and memory resources\. 

**Note**  
If no instances are running, vCPU and memory reservations can initially affect AWS Batch scaling logic and decision making\. After the instances are running, AWS Batch adjusts the initial allocations\.

## Node CPU reservation example<a name="memory-cpu-batch-eks-node-cpu-reservations"></a>

The CPU reservation value is calculated in millicores using the total number of vCPUs that are available to the instance:


| vCPU number | Percentage reserved | 
| --- | --- | 
| 1 | 6% | 
| 2 | 1% | 
| 3\-4 | 0\.5% | 
| 4 and above | 0\.25% | 

Using the above values:
+ The CPU reservation value for a `c5.large` instance with 2 vCPUs is: *\(1\*60\) \+ \(1\*10\) = 70m*\.
+ The CPU reservation value for a `c5.24xlarge` instance with 96 vCPUs is: \(1\*60\) \+ \(1\*10\) \+ \(2\*5\) \+ \(92\*2\.5\) = 310m\.

In this example, there are1930 \(2000\-70\) millicore vCPU units available to run jobs on a `c5.large` instance\. If your job requires `2` \(2\*1000m\) vCPU units, the job will not fit on a single `c5.large` instance\. However, a job that requires `1.75` vCPU units will fit\.

## Node memory reservation example<a name="memory-cpu-batch-eks-node-memory-reservations"></a>

The memory reservation value is calculated in mebibytes using:
+ The instance capacity in mebibytes\. For example, an 8 GB instance is 7,748 MiB\.
+ The `kubeReserved` value\. The `kubeReserved` value is the amount of memory to reserve for system daemons\. The `kubeReserved` value is calculated as: *\(\(11 \* maximum number of pods that is supported by the instance type\) \+ 255\)*\. For information about the maximum number of pods supported by an instance type, see [eni\-max\-pods\.txt](https://github.com/awslabs/amazon-eks-ami/blob/master/files/eni-max-pods.txt) 
+ The `HardEvictionLimit` value\. The instance attempts to evict pods when available memory drops below the `HardEvictionLimit` value\.

The formula to calculate the allocatable memory is: \(*instance\_capacity\_in\_MiB*\) \- \(11 \* \(*maximum\_number\_of\_pods*\)\) \- 255 \- \(*`HardEvictionLimit` value\.*\)\)\.

 A `c5.large` instance supports a maximum of 29 pods\. For an 8 GB `c5.large` instance with a `HardEvictionLimit` value of 100 MiB, the allocatable memory is: *\(7748 \- \(11 \* 29\) \-255 \-100\) = 7074 MiB*\. In this example, an 8,192 MiB job would not fit on this instance even though it is an 8 gibibyte \(GiB\) instance\.

## DaemonSets<a name="memory-cpu-batch-eks-reservations-daemonset-scaling"></a>

Consider the following when you use DaemonSets:
+ If no AWS Batch on Amazon EKS instances are running, DaemonSets can initially affect AWS Batch scaling logic and decision making\. AWS Batch initially allocates \.5 vCPU units and 500 MiB for expected DaemonSets\. After the instances are running, AWS Batch adjusts the initial allocations\.
+ AWS Batch on Amazon EKS jobs will have fewer resources if a DaemonSet defines vCPU or memory limits\. We recommend that you keep the number of DaemonSets assigned to AWS Batch jobs as low as possible\.