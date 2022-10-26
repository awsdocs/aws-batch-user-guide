# Amazon EKS compute environments<a name="compute-environments-eks"></a>

[Getting started with AWS Batch on Amazon EKS](getting-started-eks.md) provides a short guide to creating EKS compute environments\. This section provides more details on EKS compute environments\.

**Topics**
+ [Default AMI selection](#eks-ce-ami-selection)
+ [Updating Kubernetes version of compute environment](#updating-k8s-version-ce)
+ [Shared responsibility of the Kubernetes nodes](#eks-ce-shared-responsibility)
+ [Running a DaemonSet on AWS Batch managed nodes](#daemonset-on-batch-eks-nodes)
+ [Customizing with launch templates](#eks-launch-templates)

## Default AMI selection<a name="eks-ce-ami-selection"></a>

When you create an EKS compute environment, you don't need to specify an Amazon Machine Image \(AMI\)\. AWS Batch selects an Amazon EKS optimized AMI based on the Kubernetes version and instance types that are specified in your [CreateComputeEnvironment](https://docs.aws.amazon.com/batch/latest/APIReference/API_CreateComputeEnvironment.html) request\. In general, we recommend that you use the default AMI selection\. For more information about Amazon EKS optimized AMIs, see[Amazon EKS optimized Amazon Linux AMIs](https://docs.aws.amazon.com/eks/latest/userguide/eks-optimized-ami.html) in the *Amazon EKS User Guide*\.

Run the following command to see which AMI type AWS Batch is selected for your EKS compute environment\. This following example is a non\-GPU instance type\.

```
# compute CE example: indicates Batch has chosen the AL2 x86 or ARM EKS 1.22 AMI, depending on instance types
$ aws batch describe-compute-environments --compute-environments My-Eks-CE1 \
    | jq '.computeEnvironments[].computeResources.ec2Configuration'
[
  {
    "imageType": "EKS_AL2",
    "imageKubernetesVersion": "1.22"
  }
]
```

This following example is a GPU instance type\.

```
# GPU CE example: indicates Batch has choosen the AL2 x86 EKS Accelerated 1.22 AMI
$ aws batch describe-compute-environments --compute-environments My-Eks-GPU-CE \
    | jq '.computeEnvironments[].computeResources.ec2Configuration'
[
  {
    "imageType": "EKS_AL2_NVIDIA",
    "imageKubernetesVersion": "1.22"
  }
]
```

## Updating Kubernetes version of compute environment<a name="updating-k8s-version-ce"></a>

With AWS Batch, you can update the Kubernetes version of a compute environment to support EKS cluster upgrades\. The Kubernetes version of a compute environment is the EKS AMI version for the Kubernetes nodes that AWS Batch launches to run jobs\. You can perform a Kubernetes version upgrade on their EKS nodes before or after you update the version of EKS cluster’s control\-plane\. We recommend that you update the nodes after upgrading the control plane\. For more information, see [Updating an Amazon EKS cluster Kubernetes version](https://docs.aws.amazon.com/eks/latest/userguide/update-cluster.html) in the *Amazon EKS User Guide*\.

To upgrade the Kubernetes version of a compute environment, use the [https://docs.aws.amazon.com/batch/latest/APIReference/API_UpdateComputeEnvironment.html](https://docs.aws.amazon.com/batch/latest/APIReference/API_UpdateComputeEnvironment.html) API operation\.

```
$ aws batch update-compute-environment \
    --compute-environment <compute-environment-name> \
    --compute-resources \
      'ec2Configuration=[{imageType=EKS_AL2,imageKubernetesVersion=1.23}]'
```

## Shared responsibility of the Kubernetes nodes<a name="eks-ce-shared-responsibility"></a>

Maintenance of the compute environments is a shared responsibility\.
+ Don't change or remove AWS Batch nodes, labels, taints, namespaces, launch templates, or auto scaling groups\. Don't add taints to AWS Batch managed nodes\. If you make any of these changes, your compute environment cannot be supported and failures, including idle instances, will occur\.
+ Don't target your pods to AWS Batch managed nodes\. If you target your pods to the managed nodes, broken scaling and stuck job queues will occur\. Run workloads that don't use AWS Batch on self\-managed nodes or managed node groups\. For more information, see [Managed node groups](https://docs.aws.amazon.com/eks/latest/userguide/managed-node-groups.html) in the *Amazon EKS User Guide*\.
+ You can target a DaemonSet to run on AWS Batch managed nodes\. For more information, see [Running a DaemonSet on AWS Batch managed nodes](#daemonset-on-batch-eks-nodes)\.

AWS Batch doesn't automatically update compute environment AMIs\. It's your responsibility to update them\. Run the following command to update your AMIs to the latest AMI version\.

```
$ aws batch update-compute-environment \
    --compute-environment <compute-environment-name> \
    --compute-resources 'updateToLatestImageVersion=true'
```

AWS Batch doesn't automatically upgrade the Kubernetes version\. Run the following command to update the Kubernetes version of your computer environment to *1\.23*\.

```
$ aws batch update-compute-environment \
    --compute-environment <compute-environment-name> \
    --compute-resources \
      'ec2Configuration=[{imageType=EKS_AL2,imageKubernetesVersion=1.23}]'
```

When updating to a more recent AMI or the Kubernetes version, you can specify whether to terminate jobs when they're updated \(`terminateJobsOnUpdate`\) and how long to wait for before an instance is replaced if running jobs don't finish \(`jobExecutionTimeoutMinutes`\.\) For more information, see [Updating compute environments](updating-compute-environments.md) and the infrastructure update policy \([https://docs.aws.amazon.com/batch/latest/APIReference/API_UpdatePolicy.html](https://docs.aws.amazon.com/batch/latest/APIReference/API_UpdatePolicy.html)\) set in the [https://docs.aws.amazon.com/batch/latest/APIReference/API_UpdateComputeEnvironment.html](https://docs.aws.amazon.com/batch/latest/APIReference/API_UpdateComputeEnvironment.html) API operation\.

## Running a DaemonSet on AWS Batch managed nodes<a name="daemonset-on-batch-eks-nodes"></a>

AWS Batch sets taints on AWS Batch managed Kubernetes nodes\. You can target a DaemonSet to run on AWS Batch managed nodes with the following `tolerations`\.

```
tolerations:
- key: "batch.amazonaws.com/batch-node"
  operator: "Exists"
```

Another way to do this is with the following `tolerations`\.

```
tolerations:
- key: "batch.amazonaws.com/batch-node"
  operator: "Exists"
  effect: "NoSchedule"
- key: "batch.amazonaws.com/batch-node"
  operator: "Exists"
  effect: "NoExecute"
```

## Customizing with launch templates<a name="eks-launch-templates"></a>

AWS Batch on Amazon EKS supports launch templates\. There are constraints on what your launch template can do\.

**Important**  
AWS Batch runs `/etc/eks/bootstrap.sh`\. Don't run `/etc/eks/bootstrap.sh` in your launch template or cloud\-init user\-data scripts\. You can add additional parameters besides the `--kubelet-extra-args` parameter to [bootstrap\.sh](https://github.com/awslabs/amazon-eks-ami/blob/master/files/bootstrap.sh)\. To do this, set the `AWS_BATCH_KUBELET_EXTRA_ARGS` variable in the `/etc/aws-batch/batch.config` file\. See the following example for details\.

**Note**  
If the launch template is changed after [CreateComputeEnvironment](https://docs.aws.amazon.com/batch/latest/APIReference/API_CreateComputeEnvironment.html) is called, [https://docs.aws.amazon.com/batch/latest/APIReference/API_UpdateComputeEnvironment.html](https://docs.aws.amazon.com/batch/latest/APIReference/API_UpdateComputeEnvironment.html) must be called to evaluate the version of the launch template for replacement\.

**Topics**
+ [Adding `kubelet` extra arguments](#kubelet-extra-args)
+ [Mounting an Amazon EFS volume](#mounting-efs-volume)
+ [IPv6 support](#eks-ipv6-support)

### Adding `kubelet` extra arguments<a name="kubelet-extra-args"></a>

AWS Batch supports adding extra arguments to the `kubelet` command\. For the list of supported parameters, see [https://kubernetes.io/docs/reference/command-line-tools-reference/kubelet/](https://kubernetes.io/docs/reference/command-line-tools-reference/kubelet/) in the *Kubernetes documentation*\. In the following example, `--node-labels mylabel=helloworld` is added to the `kubelet` command line\.

```
MIME-Version: 1.0
Content-Type: multipart/mixed; boundary="==MYBOUNDARY=="

--==MYBOUNDARY==
Content-Type: text/x-shellscript; charset="us-ascii"

#!/bin/bash
mkdir -p /etc/aws-batch

echo AWS_BATCH_KUBELET_EXTRA_ARGS=\"--node-labels mylabel=helloworld\" >> /etc/aws-batch/batch.config

--==MYBOUNDARY==--
```

### Mounting an Amazon EFS volume<a name="mounting-efs-volume"></a>

You can use launch templates to mount volumes to the node\. In the following example, the `cloud-config` `packages` and `runcmd` settings are used\. For more information, see [Cloud config examples](https://cloudinit.readthedocs.io/en/latest/topics/examples.html) in the *cloud\-init documentation*\.

```
MIME-Version: 1.0
Content-Type: multipart/mixed; boundary="==MYBOUNDARY=="

--==MYBOUNDARY==
Content-Type: text/cloud-config; charset="us-ascii"

packages:
- amazon-efs-utils

runcmd:
- file_system_id_01=fs-abcdef123
- efs_directory=/mnt/efs

- mkdir -p ${efs_directory}
- echo "${file_system_id_01}:/ ${efs_directory} efs _netdev,noresvport,tls,iam 0 0" >> /etc/fstab
- mount -t efs -o tls ${file_system_id_01}:/ ${efs_directory}

--==MYBOUNDARY==--
```

To use this volume in the job, it must be added in the [eksProperties](https://docs.aws.amazon.com/batch/latest/APIReference/API_EksProperties.html) parameter to [RegisterJobDefinition](https://docs.aws.amazon.com/batch/latest/APIReference/API_RegisterJobDefinition.html)\. The following example is a large portion of the job definition\.

```
{
    "jobDefinitionName": "MyJobOnEks_EFS",
    "type": "container",
    "eksProperties": {
        "podProperties": {
            "containers": [
                {
                    "image": "public.ecr.aws/amazonlinux/amazonlinux:2",
                    "command": ["ls", "-la", "/efs"],
                    "resources": {
                        "limits": {
                            "cpu": "1",
                            "memory": "1024Mi"
                        }
                    },
                    "volumeMounts": [
                        {
                            "name": "efs-volume",
                            "mountPath": "/efs"
                        }
                    ]
                }
            ],
            "volumes": [
                {
                    "name": "efs-volume",
                    "hostPath": {
                        "path": "/mnt/efs"
                    }
                }
            ]
        }
    }
}
```

In the node, the Amazon EFS volume is mounted in the `/mnt/efs` directory\. In the container for the EKS job, the volume is mounted in the `/efs` directory\.

### IPv6 support<a name="eks-ipv6-support"></a>

AWS Batch supports Amazon EKS clusters that have IPv6 addresses\. No customizations are required for AWS Batch support\. However, before you begin, we recommend that you review the considerations and conditions that are outlined in [Assigning IPv6 addresses to pods and services](https://docs.aws.amazon.com/eks/latest/userguide/cni-ipv6.html) in the *Amazon EKS User Guide*\.