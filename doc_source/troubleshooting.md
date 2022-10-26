# Troubleshooting AWS Batch<a name="troubleshooting"></a>

You might need to troubleshoot issues that are related to your compute environments, job queues, job definitions, or jobs\. This chapter describes how to troubleshoot and repair these issues in your AWS Batch environment\.

AWS Batch uses IAM policies, roles, and permissions, and runs on Amazon EC2, Amazon ECS, AWS Fargate, and Amazon Elastic Kubernetes Service infrastructure\. To troubleshoot issues that are related to these services, see the following:
+ [Troubleshooting IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/troubleshoot.html) in the *IAM User Guide*
+ [Amazon ECS troubleshooting](https://docs.aws.amazon.com/AmazonECS/latest/userguide/troubleshooting.html) in the *Amazon Elastic Container Service Developer Guide*
+ [Amazon EKS troubleshooting](https://docs.aws.amazon.com/eks/latest/userguide/troubleshooting.html) in the *Amazon EKS User Guide*
+ [Troubleshoot EC2 instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-troubleshoot.html) in the *Amazon EC2 User Guide for Linux Instances*

**Contents**
+ [AWS Batch](#batch-troubleshooting)
  + [`INVALID` compute environment](#invalid_compute_environment)
    + [Incorrect role name or ARN](#invalid_service_role_arn)
    + [Repairing an `INVALID` compute environment](#repairing_invalid_compute_environment)
  + [Jobs stuck in a `RUNNABLE` status](#job_stuck_in_runnable)
  + [Spot Instances not tagged on creation](#spot-instance-no-tag)
  + [Spot Instances not scaling down](#spot-fleet-not-authorized)
    + [Attach **AmazonEC2SpotFleetTaggingRole** managed policy to your Spot Fleet role in the AWS Management Console](#spot-fleet-not-authorized-console)
    + [Attach **AmazonEC2SpotFleetTaggingRole** managed policy to your Spot Fleet role with the AWS CLI](#spot-fleet-not-authorized-cli)
  + [Can't retrieve Secrets Manager secrets](#troubleshooting-cant-specify-secrets)
  + [Can't override job definition resource requirements](#override-resource-requirements)
+ [AWS Batch on Amazon EKS](#batch-eks-troubleshooting)
  + [`INVALID` compute environment](#batch_eks_invalid_compute_environment)
    + [Unsupported Kubernetes version](#invalid_kubernetes_version)
    + [Instance profile does not exist](#instance_profile_not_exist)
    + [Invalid Kubernetes namespace](#invalid_kubernetes_namespace)
    + [Deleted compute environment](#deleted_compute_environment)
    + [Nodes do not join the Amazon EKS cluster](#batch_eks_node_not_join_cluster)
  + [AWS Batch on Amazon EKS job is stuck in `RUNNABLE` status](#batch_eks_job_stuck_in_runnable)
  + [RBAC permissions or bindings are not configured properly](#batch_eks_rbac)

## AWS Batch<a name="batch-troubleshooting"></a>

### `INVALID` compute environment<a name="invalid_compute_environment"></a>

It's possible that you might have incorrectly configured a managed compute environment\. If you did, the compute environment enters an `INVALID` state and can't accept jobs for placement\. The following sections describe the possible causes and how to troubleshoot based on the cause\.

#### Incorrect role name or ARN<a name="invalid_service_role_arn"></a>

The most common cause for a compute environment to enter an `INVALID` state is that the AWS Batch service role or the Amazon EC2 Spot Fleet role has an incorrect name or Amazon Resource Name \(ARN\)\. This is more common with compute environments that are created using the AWS CLI or the AWS SDKs\. When you create a compute environment in the AWS Management Console, AWS Batch can help you choose the correct service or Spot Fleet roles\. However, if you manually enter the name or the ARN and happen to enter them incorrectly, then the resulting compute environment is also `INVALID`\.

However, if you manually enter the name or ARN for an IAM in an AWS CLI command or your SDK code, AWS Batch can't validate the string\. It must accept the bad value and attempt to create the environment\. If AWS Batch fails to create the environment, the environment moves to an `INVALID` state, and you see the following errors\.

For an invalid service role:

`CLIENT_ERROR - Not authorized to perform sts:AssumeRole (Service: AWSSecurityTokenService; Status Code: 403; Error Code: AccessDenied; Request ID: dc0e2d28-2e99-11e7-b372-7fcc6fb65fe7)`

For an invalid Spot Fleet role:

`CLIENT_ERROR - Parameter: SpotFleetRequestConfig.IamFleetRole is invalid. (Service: AmazonEC2; Status Code: 400; Error Code: InvalidSpotFleetRequestConfig; Request ID: 331205f0-5ae3-4cea-bac4-897769639f8d) Parameter: SpotFleetRequestConfig.IamFleetRole is invalid`

One common cause for this issue is if you only specify the name of an IAM role when using the AWS CLI or the AWS SDKs, instead of the full Amazon Resource Name \(ARN\)\. This is because, depending on how you created the role, the ARN might contain a `aws-service-role` path prefix\. For example, if you manually create the AWS Batch service role using the procedures in [AWS Batch service IAM role](service_IAM_role.md), your service role ARN might look like this:

```
arn:aws:iam::123456789012:role/AWSBatchServiceRole
```

However, if you created the service role as part of the console first run wizard today, your service role ARN might look like this:

```
arn:aws:iam::123456789012:role/aws-service-role/AWSBatchServiceRole
```

When you only specify the name of an IAM role when using the AWS CLI or the AWS SDKs, AWS Batch assumes that your ARN doesn't use the `aws-service-role` path prefix\. Because of this, we recommend that you specify the full ARN for your IAM roles when you create compute environments\.

To repair a compute environment that's misconfigured this way, see [Repairing an `INVALID` compute environment](#repairing_invalid_compute_environment)\.

#### Repairing an `INVALID` compute environment<a name="repairing_invalid_compute_environment"></a>

When you have a compute environment in an `INVALID` state, update it to repair the invalid parameter\. For an [Incorrect role name or ARN](#invalid_service_role_arn), update the compute environment using the correct service role\.

**To repair a misconfigured compute environment**

1. Open the AWS Batch console at [https://console\.aws\.amazon\.com/batch/](https://console.aws.amazon.com/batch/)\.

1. From the navigation bar, select the AWS Region to use\.

1. In the navigation pane, choose **Compute environments**\.

1. On the **Compute environments** page, select the radio button next to the compute environment to edit, and then choose **Edit**\.

1. On the **Update compute environment** page, for **Service role**, choose the IAM role to use with your compute environment\. The AWS Batch console only displays roles that have the correct trust relationship for compute environments\.

1. Choose **Save** to update your compute environment\.

### Jobs stuck in a `RUNNABLE` status<a name="job_stuck_in_runnable"></a>

Suppose that your compute environment contains compute resources, but your jobs don't progress beyond the `RUNNABLE` status\. Then, there's likely something preventing the jobs from actually being placed on a compute resource\. Here are some common causes for this issue:

The `awslogs` log driver isn't configured on your compute resources  
AWS Batch jobs send their log information to CloudWatch Logs\. To enable this, you must configure your compute resources to use the `awslogs` log driver\. Suppose that you base your compute resource AMI off of the Amazon ECS optimized AMI \(or Amazon Linux\)\. Then, this driver is registered by default with the `ecs-init` package\. Now suppose that you use a different base AMI\. Then, you must verify that the `awslogs` log driver is specified as an available log driver with the `ECS_AVAILABLE_LOGGING_DRIVERS` environment variable when the Amazon ECS container agent is started\. For more information, see [Compute resource AMI specification](compute_resource_AMIs.md#batch-ami-spec) and [Creating a compute resource AMI](create-batch-ami.md)\.

Insufficient resources  
If your job definitions specify more CPU or memory resources than your compute resources can allocate, then your jobs arenever placed\. For example, suppose that your job specifies 4 GiB of memory, and your compute resources have less than that available\. Then it's the case that the job can't be placed on those compute resources\. In this case, you must reduce the specified memory in your job definition or add larger compute resources to your environment\. Some memory is reserved for the Amazon ECS container agent and other critical system processes\. For more information, see [Compute Resource Memory Management](memory-management.md)\.

No internet access for compute resources  
Compute resources need access to communicate with the Amazon ECS service endpoint\. This can be through an interface VPC endpoint or through your compute resources having public IP addresses\.  
For more information about interface VPC endpoints, see [Amazon ECS Interface VPC Endpoints \(AWS PrivateLink\)](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/vpc-endpoints.html) in the *Amazon Elastic Container Service Developer Guide*\.  
If you do not have an interface VPC endpoint configured and your compute resources do not have public IP addresses, then they must use network address translation \(NAT\) to provide this access\. For more information, see [NAT gateways](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html) in the *Amazon VPC User Guide*\. For more information, see [Create a VPC](get-set-up-for-aws-batch.md#create-a-vpc)\.\.

Amazon EC2 instance limit reached  
The number of Amazon EC2 instances that your account can launch in an AWS Region is determined by your EC2 instance quota\. Certain instance types also have a per\-instance\-type quota\. For more information about your account's Amazon EC2 instance quota \(including how to request a limit increase\), see [Amazon EC2 Service Limits](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-resource-limits.html) in the *Amazon EC2 User Guide for Linux Instances*\.

Amazon ECS container agent isn't installed  
The Amazon ECS container agent must be installed on the Amazon Machine Image \(AMI\) to let AWS Batch run jobs\. The Amazon ECS container agent is installed by default on Amazon ECS optimized AMIs\. For more information about the Amazon ECS container agent, see [Amazon ECS container agent](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ECS_agent.html) in the *Amazon Elastic Container Service Developer Guide*\.

For more information, see [Why is my AWS Batch job stuck in RUNNABLE status?](https://aws.amazon.com/premiumsupport/knowledge-center/batch-job-stuck-runnable-status/) in the *AWS Knowledge Center*\.

### Spot Instances not tagged on creation<a name="spot-instance-no-tag"></a>

Spot Instance tagging for AWS Batch compute resources is supported as of October 25, 2017\. Before, the recommended IAM managed policy \(`AmazonEC2SpotFleetRole`\) for the Amazon EC2 Spot Fleet role didn't contain permissions to tag Spot Instances at launch\. The new recommended IAM managed policy is called `AmazonEC2SpotFleetTaggingRole`\. It supports tagging Spot Instances at launch\.

To fix Spot Instance tagging on creation, follow the following procedure to apply the current recommended IAM managed policy to your Amazon EC2 Spot Fleet role\. That way, any future Spot Instances that are created with that role have permissions to apply instance tags when they're created\.

**To apply the current IAM managed policy to your Amazon EC2 Spot Fleet role**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. Choose **Roles**, and choose your Amazon EC2 Spot Fleet role\.

1. Choose **Attach policy**\.

1. Select the **AmazonEC2SpotFleetTaggingRole** and choose **Attach policy**\.

1. Choose your Amazon EC2 Spot Fleet role again to remove the previous policy\.

1. Select the **x** to the right of the **AmazonEC2SpotFleetRole** policy, and choose **Detach**\.

### Spot Instances not scaling down<a name="spot-fleet-not-authorized"></a>

AWS Batch introduced the **AWSServiceRoleForBatch** service\-linked role on March 10, 2021\. If no role is specified in the `serviceRole` parameter of the compute environment, this service\-linked role is used as the service role\. However, suppose that the service\-linked role is used in an EC2 Spot compute environment, but the Spot role used doesn't include the **AmazonEC2SpotFleetTaggingRole** managed policy\. Then, the Spot Instance don't scale down\. As a result, you will receive an error with the following message: "You are not authorized to perform this operation\." Use the following steps to update the spot fleet role that you use in the `spotIamFleetRole` parameter\. For more information, see [Using service\-linked roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html) and [Creating a role to delegate permissions to an AWS Service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html) in the *IAM User Guide*\.

**Topics**
+ [Attach **AmazonEC2SpotFleetTaggingRole** managed policy to your Spot Fleet role in the AWS Management Console](#spot-fleet-not-authorized-console)
+ [Attach **AmazonEC2SpotFleetTaggingRole** managed policy to your Spot Fleet role with the AWS CLI](#spot-fleet-not-authorized-cli)

#### Attach **AmazonEC2SpotFleetTaggingRole** managed policy to your Spot Fleet role in the AWS Management Console<a name="spot-fleet-not-authorized-console"></a>

**To apply the current IAM managed policy to your Amazon EC2 Spot Fleet role**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. Choose **Roles**, and choose your Amazon EC2 Spot Fleet role\.

1. Choose **Attach policy**\.

1. Select the **AmazonEC2SpotFleetTaggingRole** and choose **Attach policy**\.

1. Choose your Amazon EC2 Spot Fleet role again to remove the previous policy\.

1. Select the **x** to the right of the **AmazonEC2SpotFleetRole** policy, and choose **Detach**\.

#### Attach **AmazonEC2SpotFleetTaggingRole** managed policy to your Spot Fleet role with the AWS CLI<a name="spot-fleet-not-authorized-cli"></a>

The example commands assume that your Amazon EC2 Spot Fleet role is named *AmazonEC2SpotFleetRole*\. If your role uses a different name, adjust the commands to match\.

**To attach the **AmazonEC2SpotFleetTaggingRole** managed policy to your Spot Fleet role**

1. To attach the **AmazonEC2SpotFleetTaggingRole** managed IAM policy to your *AmazonEC2SpotFleetRole* role, run the following command using the AWS CLI\.

   ```
   $ aws iam attach-role-policy \
       --policy-arn arn:aws:iam::aws:policy/service-role/AmazonEC2SpotFleetTaggingRole \
       --role-name AmazonEC2SpotFleetRole
   ```

1. To detach the **AmazonEC2SpotFleetRole** managed IAM policy from your *AmazonEC2SpotFleetRole* role, run the following command using the AWS CLI\.

   ```
   $ aws iam detach-role-policy \
       --policy-arn arn:aws:iam::aws:policy/service-role/AmazonEC2SpotFleetRole \
       --role-name AmazonEC2SpotFleetRole
   ```

### Can't retrieve Secrets Manager secrets<a name="troubleshooting-cant-specify-secrets"></a>

If you use an AMI with an Amazon ECS agent that's earlier than version 1\.16\.0\-1, then you must use the Amazon ECS agent configuration variable `ECS_ENABLE_AWSLOGS_EXECUTIONROLE_OVERRIDE=true` to use this feature\. You can add it to the `./etc/ecs/ecs.config` file to a new container instance when you create that instance\. Or, you can add it to an existing instance\. If you add it to an existing instance, you must restart the ECS agent after you add it\. For more information, see [Amazon ECS Container Agent Configuration](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-agent-config.html) in the *Amazon Elastic Container Service Developer Guide*\.

### Can't override job definition resource requirements<a name="override-resource-requirements"></a>

The memory and vCPU overrides that are specified in the `memory` and `vcpus` members of the [containerOverrides](https://docs.aws.amazon.com/batch/latest/APIReference/API_ContainerOverrides.html) structure, which passed to [SubmitJob](https://docs.aws.amazon.com/batch/latest/APIReference/API_SubmitJob.html), can't override the memory and vCPU requirements that are specified in the [resourceRequirements](https://docs.aws.amazon.com/batch/latest/APIReference/API_ContainerProperties.html#Batch-Type-ContainerProperties-resourceRequirements) structure in the job definition\.

If you try to override these resource requirements, you might see the following error message:

"This value was submitted in a deprecated key and may conflict with the value provided by the job definition's resource requirements\."

To correct this, specify the memory and vCPU requirements in the [resourceRequirements](https://docs.aws.amazon.com/batch/latest/APIReference/API_ContainerOverrides.html#Batch-Type-ContainerOverrides-resourceRequirements) member of the [containerOverrides](https://docs.aws.amazon.com/batch/latest/APIReference/API_ContainerOverrides.html)\. For example, if your memory and vCPU overrides are specified in the following lines\.

```
"containerOverrides": {
   "memory": 8192,
   "vcpus": 4
}
```

Change them to this:

```
"containerOverrides": {
   "resourceRequirements": [
      {
         "type": "MEMORY",
         "value": "8192"
      },
      {
         "type": "VCPU",
         "value": "4"
      }
   ],
}
```

Do the same change to the memory and vCPU requirements that are specified in the [containerProperties](https://docs.aws.amazon.com/batch/latest/APIReference/API_ContainerProperties.html) object in the job definition\. For example, if your memory and vCPU requirements are specified in the following lines\.

```
{
   "containerProperties": {
      "memory": 4096,
      "vcpus": 2,
}
```

Change them to this:

```
"containerProperties": {
   "resourceRequirements": [
      {
         "type": "MEMORY",
         "value": "4096"
      },
      {
         "type": "VCPU",
         "value": "2"
      }
   ],
}
```

## AWS Batch on Amazon EKS<a name="batch-eks-troubleshooting"></a>

**Topics**
+ [`INVALID` compute environment](#batch_eks_invalid_compute_environment)
+ [AWS Batch on Amazon EKS job is stuck in `RUNNABLE` status](#batch_eks_job_stuck_in_runnable)
+ [RBAC permissions or bindings are not configured properly](#batch_eks_rbac)

### `INVALID` compute environment<a name="batch_eks_invalid_compute_environment"></a>

It's possible that you might have incorrectly configured a managed compute environment\. If you did, the compute environment enters an `INVALID` state and can't accept jobs for placement\. The following sections describe the possible causes and how to troubleshoot based on the cause\.

#### Unsupported Kubernetes version<a name="invalid_kubernetes_version"></a>

If the compute environment was created on an unsupported Kubernetes version, AWS Batch on Amazon EKS changes the compute environment status to `INVALID`\. You see an error set in the `statusReason` parameter that resembles the following:

```
reason=CLIENT_ERROR - ... EKS Cluster version [1.xx] is unsupported
```

To resolve this issue, delete the compute environment and then re\-create it with a supported Kubernetes version\. AWS Batch on Amazon EKS currently supports the following Kubernetes versions:
+ `1.23`
+ `1.22`
+ `1.21`
+ `1.20`

**Note**  
We recommend Kubernetes version 1\.22 or later\.

#### Instance profile does not exist<a name="instance_profile_not_exist"></a>

If the specified instance profile does not exist, the AWS Batch on Amazon EKS compute environment status is changed to `INVALID`\. You see an error set in the `statusReason` parameter that resembles the following:

```
CLIENT_ERROR - Instance profile arn:aws:iam::...:instance-profile/<name> does not exist
```

To resolve this issue, specify or create a working instance profile\. For more information, see [Amazon EKS node IAM role](https://docs.aws.amazon.com/eks/latest/userguide/create-node-role.html) in the *Amazon EKS User Guide*\.

#### Invalid Kubernetes namespace<a name="invalid_kubernetes_namespace"></a>

If AWS Batch on Amazon EKS can't validate the namespace for the compute environment, the compute environment status is changed to `INVALID`\. For example, this issue can occur if the namespace doesn't exist\. 

You see an error message set in the `statusReason` parameter that resembles the following:

```
CLIENT_ERROR - Unable to validate Kubernetes Namespace
```

This issue can occur if any of the following are true:
+ The Kubernetes namespace string in `CreateComputeEnvironment` call does not exist\.
+ The required Role\-Based Access Control \(RBAC\) permissions to manage the namespace are not configured correctly\.
+ AWS Batch does not have access to the Amazon EKS Kubernetes API server endpoint\. 

For information about how to resolve this issue, see [Getting started with AWS Batch on Amazon EKS](https://docs.aws.amazon.com/batch/latest/userguide/getting-started-eks.html)\.

#### Deleted compute environment<a name="deleted_compute_environment"></a>

If you delete an Amazon EKS cluster before you delete the attached AWS Batch on Amazon EKS compute environment, the compute environment status is changed to `INVALID`\. In this scenario, the compute environment does not work properly if you re\-create the Amazon EKS cluster with the same name\.

To resolve this issue, delete and then re\-create the AWS Batch on Amazon EKS compute environment\.

#### Nodes do not join the Amazon EKS cluster<a name="batch_eks_node_not_join_cluster"></a>

AWS Batch on Amazon EKS scales down a compute environment if it determines that not all nodes joined the Amazon EKS cluster\. When AWS Batch on Amazon EKS scales down the compute environment, the compute environment status is changed to `INVALID`\.

**Note**  
AWS Batch doesn't change the compute environment status immediately so that you can debug the issue\.

You see an error message set in the `statusReason` parameter that resembles ones of the following:

`Your compute environment has been INVALIDATED and scaled down because none of the instances joined the underlying ECS Cluster. Common issues preventing instances joining are the following: VPC/Subnet configuration preventing communication to ECS, incorrect Instance Profile policy preventing authorization to ECS, or customized AMI or LaunchTemplate configurations affecting ECS agent.`

`Your compute environment has been INVALIDATED and scaled down because none of the nodes joined the underlying EKS Cluster. Common issues preventing nodes joining are the following: networking configuration preventing communication to EKS Cluster, incorrect EKS Instance Profile or Kubernetes RBAC policypreventing authorization to EKS Cluster, customized AMI or LaunchTemplate configurations affecting EKS/Kubernetes node bootstrap.`

When using a default Amazon EKS AMI, the most common causes of this issue are: 
+ The instance role is not configured properly\. For more information, see [Amazon EKS node IAM role](https://docs.aws.amazon.com/eks/latest/userguide/create-node-role.html) in the *Amazon EKS User Guide*\.
+ The subnets are not configured properly\. For more information, see [Amazon EKS VPC and subnet requirements and considerations](https://docs.aws.amazon.com/eks/latest/userguide/network_reqs.html) in the *Amazon EKS User Guide*\.
+ The security group is not configured properly\. For more information, see [Amazon EKS security group requirements and considerations](https://docs.aws.amazon.com/eks/latest/userguide/sec-group-reqs.html) in the *Amazon EKS User Guide*\.
**Note**  
You may also see an error notification in the Personal Health Dashboard \(PHD\)\.

### AWS Batch on Amazon EKS job is stuck in `RUNNABLE` status<a name="batch_eks_job_stuck_in_runnable"></a>

An `aws-auth` `ConfigMap` is automatically created and applied to your cluster when you create a managed node group or when you create a node group using `eksctl`\. It is initially created to allow nodes to join your cluster, but you also use the `aws-auth``ConfigMap` to add role\-based access control \(RBAC\) access to IAM users and roles\.

To verify that the `aws-auth` `ConfigMap` is configured correctly:

1. Retrieve the mapped roles in the `aws-auth` `ConfigMap`:

   ```
   $ kubectl get configmap -n kube-system aws-auth -o yaml
   ```

1. Verify that the `roleARN` is configured as follows:

   `rolearn: arn:aws:iam::aws_account_number:role/AWSServiceRoleForBatch`
**Note**  
You can also review the Amazon EKS control plane logs\. For more information, see [Amazon EKS control plane logging](https://docs.aws.amazon.com/eks/latest/userguide/control-plane-logs.html) in the *Amazon EKS User Guide*\.

To resolve an issue where a job is stuck in a `RUNNABLE` status, we recommend that you use `kubectl` to re\-apply the manifest\. For more information, see [Step 1: Preparing your EKS cluster for AWS Batch](getting-started-eks.md#getting-started-eks-step-1)\. Or you can use `kubectl` to manually edit the `aws-auth` `ConfigMap`\. For more information, see [Enabling IAM user and role access to your cluster](https://docs.aws.amazon.com/eks/latest/userguide/add-user-role.html) in the *Amazon EKS User Guide*\.

### RBAC permissions or bindings are not configured properly<a name="batch_eks_rbac"></a>

If you experience any RBAC permissions or binding issues, verify that the `aws-batch` Kubernetes role can access the Kubernetes namespace:

```
$ kubectl get namespace namespace
     --as=aws-batch
```

```
$ kubectl auth can-i get ns --as=aws-batch
```

You can also use the **kubectl describe** command to view the authorizations for a cluster role or Kubernetes namespace\.

```
$ kubectl describe clusterrole aws-batch-cluster-role
```

The following is example output\.

```
Name:         aws-batch-cluster-role
Labels:       <none>
Annotations:  <none>
PolicyRule:
  Resources                                      Non-Resource URLs  Resource Names  Verbs
  ---------                                      -----------------  --------------  -----
  configmaps                                     []                 []              [get list watch]
  nodes                                          []                 []              [get list watch]
  pods                                           []                 []              [get list watch]
  daemonsets.apps                                []                 []              [get list watch]
  deployments.apps                               []                 []              [get list watch]
  replicasets.apps                               []                 []              [get list watch]
  statefulsets.apps                              []                 []              [get list watch]
  clusterrolebindings.rbac.authorization.k8s.io  []                 []              [get list]
  clusterroles.rbac.authorization.k8s.io         []                 []              [get list]
  namespaces                                     []                 []              [get]
```

```
$ kubectl describe role aws-batch-compute-environment-role -n my-aws-batch-namespace
```

The following example output\.

```
Name:         aws-batch-compute-environment-role
Labels:       <none>
Annotations:  <none>
PolicyRule:
  Resources                               Non-Resource URLs  Resource Names  Verbs
  ---------                               -----------------  --------------  -----
  pods                                    []                 []              [create get list watch delete patch]
  serviceaccounts                         []                 []              [get list]
  rolebindings.rbac.authorization.k8s.io  []                 []              [get list]
  roles.rbac.authorization.k8s.io         []                 []              [get list]
```

To resolve this issue, re\-apply the RBAC permissions and `rolebinding` commands\. For more information, see [Step 1: Preparing your EKS cluster for AWS Batch](getting-started-eks.md#getting-started-eks-step-1)\.