# Getting started with AWS Batch on Amazon EKS<a name="getting-started-eks"></a>

AWS Batch on Amazon EKS is a managed service for scheduling and scaling batch workloads into existing EKS clusters\. AWS Batch doesn't create, administer, or perform lifecycle operations of your EKS clusters on your behalf\. AWS Batch orchestration scales up and down nodes managed by AWS Batch and run pods on those nodes\.

AWS Batch doesn't touch nodes, auto scaling node groups or pods lifecycles that aren't associated with AWS Batch compute environments within your EKS cluster\. For AWS Batch to operate effectively, its [service\-linked role](using-service-linked-roles.md#slr-permissions) needs Kubernetes role\-based access control \(RBAC\) permissions in your existing EKS cluster\. For more information, see [Using RBAC Authorization](https://kubernetes.io/docs/reference/access-authn-authz/rbac/) in the *Kubernetes documentation*\.

AWS Batch requires a Kubernetes namespace where it can scope pods as AWS Batch jobs into\. We recommend a dedicated namespace to isolate the AWS Batch pods from your other cluster workloads\.

After AWS Batch has been given RBAC access and a namespace has been established, you can associate that EKS cluster to an AWS Batch compute environment using the [CreateComputeEnvironment](https://docs.aws.amazon.com/batch/latest/APIReference/API_CreateComputeEnvironment.html) API operation\. A job queue can be associated with this new EKS compute environment\. AWS Batch jobs are submitted to the job queue based on an EKS job definition using the [SubmitJob](https://docs.aws.amazon.com/batch/latest/APIReference/API_SubmitJob.html) API operation\. AWS Batch then launches AWS Batch managed nodes and place jobs from job queue as Kubernetes pods into the EKS cluster associated with an AWS Batch compute environment\.

The following sections cover how to get set up for AWS Batch on Amazon EKS\.

**Contents**
+ [Prerequisites](#getting-started-eks-prerequisites)
+ [Step 1: Preparing your EKS cluster for AWS Batch](#getting-started-eks-step-1)
+ [Step 2: Creating an Amazon EKS compute environment](#getting-started-eks-step-2)
+ [Step 3: Create a job queue and attach the compute environment](#getting-started-eks-step-3)
+ [Step 4: Create a job definition](#getting-started-eks-step-4)
+ [Step 5: Submit a job](#getting-started-eks-step-5)
+ [\(Optional\) Submit a job with overrides](#getting-started-eks-step-6)

## Prerequisites<a name="getting-started-eks-prerequisites"></a>

Before starting this tutorial, you must install and configure the following tools and resources that you need to create and manage both AWS Batch and Amazon EKS resources\.
+ **AWS CLI** – A command line tool for working with AWS services, including Amazon EKS\. This guide requires that you use version 2\.8\.6 or later or 1\.26\.0 or later\. For more information, see [Installing, updating, and uninstalling the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html) in the AWS Command Line Interface User Guide\. After installing the AWS CLI, we recommend that you also configure it\. For more information, see [Quick configuration with `aws configure`](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html#cli-configure-quickstart-config) in the AWS Command Line Interface User Guide\.
+ **`kubectl`** – A command line tool for working with Kubernetes clusters\. This guide requires that you use version `1.23` or later\. For more information, see [Installing or updating `kubectl`](https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html) in the *Amazon EKS User Guide*\.
+ **`eksctl`** – A command line tool for working with EKS clusters that automates many individual tasks\. This guide requires that you use version `0.115.0` or later\. For more information, see [Installing or updating `eksctl`](https://docs.aws.amazon.com/eks/latest/userguide/eksctl.html) in the *Amazon EKS User Guide*\.
+ **Required IAM permissions** – The IAM security principal that you're using must have permissions to work with Amazon EKS IAM roles and service linked roles, AWS CloudFormation, and a VPC and related resources\. For more information, see [Actions, resources, and condition keys for Amazon Elastic Kubernetes Service](https://docs.aws.amazon.com/service-authorization/latest/reference/list_amazonelastickubernetesservice.html) and [Using service\-linked roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html) in the IAM User Guide\. You must complete all steps in this guide as the same user\.
+ **Creating an EKS cluster** – For more information, see [Getting started with Amazon EKS – `eksctl`](https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html) in the *Amazon EKS User Guide*\.
**Note**  
AWS Batch only supports EKS clusters with API server endpoints that have public access, accessible to the public internet\. By default, EKS clusters API server endpoints have public access\. For more information, see [Amazon EKS cluster endpoint access control](https://docs.aws.amazon.com/eks/latest/userguide/cluster-endpoint.html) in the *Amazon EKS User Guide*\.
**Note**  
AWS Batch doesn't provide managed\-node orchestration for CoreDNS or other deployment pods\. If you need CoreDNS, see [Adding the CoreDNS Amazon EKS add\-on](https://docs.aws.amazon.com/eks/latest/userguide/managing-coredns.html#adding-coredns-eks-add-on) in the *Amazon EKS User Guide*\. Or, use `eksctl create cluster create` to create the cluster, it includes CoreDNS by default\.
+ **Permissions** – Users calling the [CreateComputeEnvironment](https://docs.aws.amazon.com/batch/latest/APIReference/API_CreateComputeEnvironment.html) API operation to create a compute environment that uses EKS resources require permissions to the `eks:DescribeCluster` API operation\. Using the AWS Management Console to create a compute resource using EKS resources requires permissions to both `eks:DescribeCluster` and `eks:ListClusters`\.

## Step 1: Preparing your EKS cluster for AWS Batch<a name="getting-started-eks-step-1"></a>

All steps are required\.

1. 

**Create a dedicated namespace for AWS Batch jobs**

   Use `kubectl` to create a new namespace\.

   ```
   $ namespace=my-aws-batch-namespace
   $ cat - <<EOF | kubectl create -f -
   {
     "apiVersion": "v1",
     "kind": "Namespace",
     "metadata": {
       "name": "${namespace}",
       "labels": {
         "name": "${namespace}"
       }
     }
   }
   EOF
   namespace/my-aws-batch-namespace created
   ```

1. 

**Enable access via role\-based access control \(RBAC\)**

   Use `kubectl` to create a Kubernetes role for the cluster to allow AWS Batch to watch nodes and pods, and to bind the role\. You must do this once for each EKS cluster\.

   ```
   $ cat - <<EOF | kubectl apply -f -
   apiVersion: rbac.authorization.k8s.io/v1
   kind: ClusterRole
   metadata:
     name: aws-batch-cluster-role
   rules:
     - apiGroups: [""]
       resources: ["namespaces"]
       verbs: ["get"]
     - apiGroups: [""]
       resources: ["nodes"]
       verbs: ["get", "list", "watch"]
     - apiGroups: [""]
       resources: ["pods"]
       verbs: ["get", "list", "watch"]
     - apiGroups: [""]
       resources: ["configmaps"]
       verbs: ["get", "list", "watch"]
     - apiGroups: ["apps"]
       resources: ["daemonsets", "deployments", "statefulsets", "replicasets"]
       verbs: ["get", "list", "watch"]
     - apiGroups: ["rbac.authorization.k8s.io"]
       resources: ["clusterroles", "clusterrolebindings"]
       verbs: ["get", "list"]
   ---
   apiVersion: rbac.authorization.k8s.io/v1
   kind: ClusterRoleBinding
   metadata:
     name: aws-batch-cluster-role-binding
   subjects:
   - kind: User
     name: aws-batch
     apiGroup: rbac.authorization.k8s.io
   roleRef:
     kind: ClusterRole
     name: aws-batch-cluster-role
     apiGroup: rbac.authorization.k8s.io
   EOF
   clusterrole.rbac.authorization.k8s.io/aws-batch-cluster-role created
   clusterrolebinding.rbac.authorization.k8s.io/aws-batch-cluster-role-binding created
   ```

   Create namespace\-scoped Kubernetes role for AWS Batch to manage and lifecycle pods and bind it\. You must do this once for each unique namespace\.

   ```
   $ namespace=my-aws-batch-namespace
   $ cat - <<EOF | kubectl apply -f - --namespace "${namespace}"
   apiVersion: rbac.authorization.k8s.io/v1
   kind: Role
   metadata:
     name: aws-batch-compute-environment-role
     namespace: ${namespace}
   rules:
     - apiGroups: [""]
       resources: ["pods"]
       verbs: ["create", "get", "list", "watch", "delete", "patch"]
     - apiGroups: [""]
       resources: ["serviceaccounts"]
       verbs: ["get", "list"]
     - apiGroups: ["rbac.authorization.k8s.io"]
       resources: ["roles", "rolebindings"]
       verbs: ["get", "list"]
   ---
   apiVersion: rbac.authorization.k8s.io/v1
   kind: RoleBinding
   metadata:
     name: aws-batch-compute-environment-role-binding
     namespace: ${namespace}
   subjects:
   - kind: User
     name: aws-batch
     apiGroup: rbac.authorization.k8s.io
   roleRef:
     kind: Role
     name: aws-batch-compute-environment-role
     apiGroup: rbac.authorization.k8s.io
   EOF
   role.rbac.authorization.k8s.io/aws-batch-compute-environment-role created
   rolebinding.rbac.authorization.k8s.io/aws-batch-compute-environment-role-binding created
   ```

   Update Kubernetes `aws-auth` conﬁguration map to map the preceding RBAC permissions to the AWS Batch service\-linked role\.

   ```
   $ eksctl create iamidentitymapping \
       --cluster my-cluster-name \
       --arn "arn:aws:iam::<your-account>:role/AWSServiceRoleForBatch" \
       --username aws-batch
   2022-10-25 20:19:57 [ℹ]  adding identity "arn:aws:iam::<your-account>:role/AWSServiceRoleForBatch" to auth ConfigMap
   ```
**Note**  
The path `aws-service-role/batch.amazonaws.com/` has been removed from the ARN of the service\-linked role\. This is because of an issue with the `aws-auth` configuration map\. For more information, see [Roles with paths do not work when the path is included in their ARN in the aws\-authconfigmap](https://github.com/kubernetes-sigs/aws-iam-authenticator/issues/268)\.

## Step 2: Creating an Amazon EKS compute environment<a name="getting-started-eks-step-2"></a>

AWS Batch compute environments define compute resource parameters to meet your batch workload needs\. In a managed compute environment, AWS Batch helps you to manage the capacity and instance types of the compute resources \(Kubernetes nodes\) within your Amazon EKS cluster\. This is based on the compute resource specification that you define when you create the compute environment\. You can use EC2 On\-Demand Instances or EC2 Spot Instances\.

Now that the **AWSServiceRoleForBatch** service\-linked role has access to your Amazon EKS cluster, you can create AWS Batch resources\. First, create a compute environment that points to your Amazon EKS cluster\.

```
$ cat <<EOF > ./batch-eks-compute-environment.json
{
  "computeEnvironmentName": "My-Eks-CE1",
  "type": "MANAGED",
  "state": "ENABLED",
  "eksConfiguration": {
    "eksClusterArn": "arn:aws:eks:<region>:123456789012:cluster/<cluster-name>",
    "kubernetesNamespace": "my-aws-batch-namespace"
  },
  "computeResources": {
    "type": "EC2",
    "allocationStrategy": "BEST_FIT_PROGRESSIVE",
    "minvCpus": 0,
    "maxvCpus": 128,
    "instanceTypes": [
        "m5"
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
$ aws batch create-compute-environment --cli-input-json file://./batch-eks-compute-environment.json
```

**Notes**
+ The `serviceRole` parameter should not be specified, then the AWS Batch service\-linked role will be used\. AWS Batch on Amazon EKS only supports the AWS Batch service\-linked role\.
+ Only `BEST_FIT_PROGRESSIVE` and `SPOT_CAPACITY_OPTIMIZED` allocation strategies are supported for EKS compute environments\.
+ For the `instanceRole`, see [Creating the Amazon EKS node IAM role](https://docs.aws.amazon.com/eks/latest/userguide/create-node-role.html#create-worker-node-role) in the *Amazon EKS User Guide*\. If you're using pod networking, see [Configuring the Amazon VPC CNI plugin for Kubernetes to use IAM roles for service accounts](https://docs.aws.amazon.com/eks/latest/userguide/cni-iam-role.html) in the *Amazon EKS User Guide*\.
+ A way to get working subnets for the `subnets` parameter is to use the Amazon EKS managed node groups public subnets that were created by `eksctl` when creating an Amazon EKS cluster\. Otherwise, use subnets that have a network path that supports pulling images\.
+ The `securityGroupIds` parameter can use the same security group as the Amazon EKS cluster\. This command retrieves the security group ID for the cluster\.

  ```
  $ aws eks describe-cluster \
      --name <cluster-name> \
      --query cluster.resourcesVpcConfig.clusterSecurityGroupId
  ```
+ Maintenance of an EKS compute environment is a shared responsibility\. For more information, see [Shared responsibility of the Kubernetes nodes](compute-environments-eks.md#eks-ce-shared-responsibility)\.

**Important**  
It's important to confirm that the compute environment is healthy before proceeding\. The [DescribeComputeEnvironments](https://docs.aws.amazon.com/batch/latest/APIReference/API_DescribeComputeEnvironments.html) API operation can be used to do this\.  

```
$ aws batch describe-compute-environments --compute-environments My-Eks-CE1
```
Confirm that the `status` parameter is not `INVALID`\. If it is, look at the `statusReason` parameter for the cause\. For more information, see [Troubleshooting AWS Batch](troubleshooting.md)\.

## Step 3: Create a job queue and attach the compute environment<a name="getting-started-eks-step-3"></a>

```
$ aws batch describe-compute-environments --compute-environments My-Eks-CE1
```

Jobs submitted to this new job queue are run as pods on AWS Batch managed nodes that joined the Amazon EKS cluster that's associated with your compute environment\.

```
$ cat <<EOF > ./batch-eks-job-queue.json
 {
    "jobQueueName": "My-Eks-JQ1",
    "priority": 10,
    "computeEnvironmentOrder": [
      {
        "order": 1,
        "computeEnvironment": "My-Eks-CE1"
      }
    ]
  }
EOF
$ aws batch create-job-queue --cli-input-json file://./batch-eks-job-queue.json
```

## Step 4: Create a job definition<a name="getting-started-eks-step-4"></a>

```
$ cat <<EOF > ./batch-eks-job-definition.json
{
  "jobDefinitionName": "MyJobOnEks_Sleep",
  "type": "container",
  "eksProperties": {
    "podProperties": {
      "hostNetwork": true,
      "containers": [
        {
          "image": "public.ecr.aws/amazonlinux/amazonlinux:2",
          "command": [
            "sleep",
            "60"
          ],
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
EOF
$ aws batch register-job-definition --cli-input-json file://./batch-eks-job-definition.json
```

**Notes**
+ Only single container jobs are supported\.
+ There are considerations for the `cpu` and `memory` parameters\. For more information, see [Memory and vCPU considerations for AWS Batch on Amazon EKS](memory-cpu-batch-eks.md)\.

## Step 5: Submit a job<a name="getting-started-eks-step-5"></a>

```
$ aws batch submit-job --job-queue My-Eks-JQ1 \
    --job-definition MyJobOnEks_Sleep --job-name My-Eks-Job1
$ aws batch describe-jobs --job <jobId-from-submit-response>
```

**Notes**
+ Only single container jobs are supported\.
+ Make sure you're familiar with all the relevant considerations for the `cpu` and `memory` parameters\. For more information, see [Memory and vCPU considerations for AWS Batch on Amazon EKS](memory-cpu-batch-eks.md)\.
+ For more information about running jobs on EKS resources, see [Amazon EKS jobs](eks-jobs.md)\.

## \(Optional\) Submit a job with overrides<a name="getting-started-eks-step-6"></a>

This job overrides the command passed to the container\.

```
$ cat <<EOF > ./submit-job-override.json
{
  "jobName": "EksWithOverrides",
  "jobQueue": "My-Eks-JQ1",
  "jobDefinition": "MyJobOnEks_Sleep",
  "eksPropertiesOverride": {
    "podProperties": {
      "containers": [
        {
          "command": [
            "/bin/sh"
          ],
          "args": [
            "-c",
            "echo hello world"
          ]
        }
      ]
    }
  }
}
EOF
$ aws batch submit-job --cli-input-json file://./submit-job-override.json
```

**Notes**
+ AWS Batch aggressively cleans up the pods after the jobs complete to reduce the load to Kubernetes\. To examine the details of a job, logging must be configured\. For more information, see [Use CloudWatch Logs to monitor AWS Batch on Amazon EKS jobs](batch-eks-cloudwatch-logs.md)\.
+ For improved visibility into the details of the operations, enable Amazon EKS control plane logging\. For more information, see [Amazon EKS control plane logging](https://docs.aws.amazon.com/eks/latest/userguide/control-plane-logs.html) in the *Amazon EKS User Guide*\.
+ Daemonsets and kubelets overhead affects available vCPU and memory resources, specifically scaling and job placement\. For more information, see [Memory and vCPU considerations for AWS Batch on Amazon EKS](memory-cpu-batch-eks.md)\.