# To create a GPU\-based job on EKS resources<a name="run-eks-gpu-workload"></a>

This section covers how to run an Amazon EKS GPU workload on AWS Batch\.

**Contents**
+ [To create GPU\-based Kubernetes cluster on Amazon EKS](#create-gpu-cluster-eks)
+ [To create an Amazon EKS GPU job definition](#create-eks-gpu-job-definition)
+ [To run a GPU job in your Amazon EKS cluster](#run-gpu-job-eks-cluster)

## To create GPU\-based Kubernetes cluster on Amazon EKS<a name="create-gpu-cluster-eks"></a>

Before you create a GPU\-based Kubernetes cluster on Amazon EKS, you must have completed the steps in [Getting started with AWS Batch on Amazon EKS](getting-started-eks.md)\. In addition, also consider the following:
+ AWS Batch supports instance types with NVIDIA GPUs\.
+ By default, AWS Batch selects the Amazon EKS accelerated AMI with the Kubernetes version that matches your Amazon EKS cluster control plane version\.

```
$ cat <<EOF > ./batch-eks-gpu-ce.json
{
  "computeEnvironmentName": "My-Eks-GPU-CE1",
  "type": "MANAGED",
  "state": "ENABLED",
  "eksConfiguration": {
    "eksClusterArn": "arn:aws:eks:<region>:<account>:cluster/<cluster-name>",
    "kubernetesNamespace": "my-aws-batch-namespace"
  },
  "computeResources": {
    "type": "EC2",
    "allocationStrategy": "BEST_FIT_PROGRESSIVE",
    "minvCpus": 0,
    "maxvCpus": 1024,
    "instanceTypes": [
      "p3dn.24xlarge",
      "p4d.24xlarge"
    ],
    "subnets": [
        "<eks-cluster-subnets-with-access-to-internet-for-image-pull>"
    ],
    "securityGroupIds": [
        "<eks-cluster-sg>"
    ],
    "instanceRole": "<eks-instance-profile>"
  }
}
EOF

$ aws batch create-compute-environment --cli-input-json file://./batch-eks-gpu-ce.json
```

AWS Batch doesn’t manage the NVIDIA GPU device plugin on your behalf\. You must install this plugin into your Amazon EKS cluster and allow it to target the AWS Batch nodes\. For more information, see [Enabling GPU Support in Kubernetes](https://github.com/NVIDIA/k8s-device-plugin#enabling-gpu-support-in-kubernetes) on GitHub\.

To configure the NVIDIA device plugin \(`DaemonSet`\) to target the AWS Batch nodes, run the following commands\.

```
# pull nvidia daemonset spec
$ curl -O https://raw.githubusercontent.com/NVIDIA/k8s-device-plugin/v0.12.2/nvidia-device-plugin.yml
# using your favorite editor, add Batch node toleration
# this will allow the DaemonSet to run on Batch nodes
- key: "batch.amazonaws.com/batch-node"
  operator: "Exists"

$ kubectl apply -f nvidia-device-plugin.yml
```

We do not recommend that you mix compute\-based \(CPU and memory\) workloads with GPU\-based workloads in the same pairings of compute environment and job queue\. This is because compute jobs can use up GPU capacity\.

```
```

To attach job queues, run the following commands\.

```
$ cat <<EOF > ./batch-eks-gpu-jq.json
 {
    "jobQueueName": "My-Eks-GPU-JQ1",
    "priority": 10,
    "computeEnvironmentOrder": [
      {
        "order": 1,
        "computeEnvironment": "My-Eks-GPU-CE1"
      }
    ]
  }
EOF

$ aws batch create-job-queue --cli-input-json file://./batch-eks-gpu-jq.json
```

## To create an Amazon EKS GPU job definition<a name="create-eks-gpu-job-definition"></a>

Only `nvidia.com/gpu` is supported at this time and resource value that you set must be a whole number\. You can’t use fractions of GPU\. For more information, see [Schedule GPUs](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/) in the *Kubernetes documentation*\.

To register a GPU job definition for Amazon EKS, run the following commands\.

```
$ cat <<EOF > ./batch-eks-gpu-jd.json
{
    "jobDefinitionName": "MyGPUJobOnEks_Smi",
    "type": "container",
    "eksProperties": {
        "podProperties": {
            "hostNetwork": true,
            "containers": [
                {
                    "image": "nvcr.io/nvidia/cuda:10.2-runtime-centos7",
                    "command": ["nvidia-smi"],
                    "resources": {
                        "limits": {
                            "cpu": "1",
                            "memory": "1024Mi",
                            "nvidia.com/gpu": "1"
                        }
                    }
                }
            ]
        }
    }
}
EOF

$ aws batch register-job-definition --cli-input-json file://./batch-eks-gpu-jd.json
```

## To run a GPU job in your Amazon EKS cluster<a name="run-gpu-job-eks-cluster"></a>

The GPU resource is non\-compressible\. AWS Batch creates a pod spec for GPU jobs where the value of **request** equals the value of **limits**\. This is a Kubernetes requirement\.

To submit a GPU job, run the following commands\.

```
$ aws batch submit-job --job-queue My-Eks-GPU-JQ1 --job-definition MyGPUJobOnEks_Smi --job-name My-Eks-GPU-Job

# locate information that can help debug or find logs (if using Amazon CloudWatch Logs with Fluent Bit)
$ aws batch describe-jobs --job <job-id> | jq '.jobs[].eksProperties.podProperties | {podName, nodeName}'
{
  "podName": "aws-batch.f3d697c4-3bb5-3955-aa6c-977fcf1cb0ca",
  "nodeName": "ip-192-168-59-101.ec2.internal"
}
```