# Tagging your AWS Batch resources<a name="using-tags"></a>

To help you manage your AWS Batch resources, you can assign your own metadata to each resource in the form of *tags*\. This topic describes tags and shows you how to create them\.

**Topics**
+ [Tag basics](#tag-basics)
+ [Tagging your resources](#tag-resources)
+ [Tag restrictions](#tag-restrictions)
+ [Working with tags using the console](#tag-resources-console)
+ [Working with tags using the CLI or API](#tag-resources-api-sdk)

## Tag basics<a name="tag-basics"></a>

A tag is a label that you assign to an AWS resource\. Each tag consists of a *key* and an optional *value*, both of which you define\.

Tags enable you to categorize your AWS resources by, for example, purpose, owner, or environment\. When you have many resources of the same type, you can quickly identify a specific resource based on the tags you've assigned to it\. For example, you can define a set of tags for your AWS Batch services to help you track each service's owner and stack level\. We recommend that you devise a consistent set of tag keys for each resource type\.

Tags are not automatically assigned to your resources\. After you add a tag, you can edit tag keys and values or remove tags from a resource at any time\. If you delete a resource, any tags for the resource are also deleted\.

Tags don't have any semantic meaning to AWS Batch and are interpreted strictly as a string of characters\. You can set the value of a tag to an empty string, but you can't set the value of a tag to null\. If you add a tag that has the same key as an existing tag on that resource, the new value overwrites the old value\.

You can work with tags using the AWS Management Console, the AWS CLI, and the AWS Batch API\.

If you're using AWS Identity and Access Management \(IAM\), you can control which users in your AWS account have permission to create, edit, or delete tags\.

## Tagging your resources<a name="tag-resources"></a>

You can tag new or existing AWS Batch compute environments, jobs, job definitions, and job queues\.

If you're using the AWS Batch console, you can apply tags to new resources when they are created or to existing resources at any time using the **Tags** tab on the relevant resource page\.

If you're using the AWS Batch API, the AWS CLI, or an AWS SDK, you can apply tags to new resources using the `tags` parameter on the relevant API action or to existing resources using the `TagResource` API action\. For more information, see [TagResource](https://docs.aws.amazon.com/batch/latest/APIReference/API_TagResource.html)\.

Some resource\-creating actions enable you to specify tags for a resource when the resource is created\. If tags cannot be applied during resource creation, the resource creation process fails\. This ensures that resources you intended to tag on creation are either created with specified tags or not created at all\. If you tag resources at the time of creation, you don't need to run custom tagging scripts after resource creation\.

The following table describes the AWS Batch resources that can be tagged, and the resources that can be tagged on creation\.


**Tagging support for AWS Batch resources**  

| Resource | Supports tags | Supports tag propagation | Supports tagging on creation \(AWS Batch API, AWS CLI, AWS SDK\) | 
| --- | --- | --- | --- | 
|  AWS Batch compute environments  |  Yes  | No\. Compute environment tags do not propagate to any other resources\. Tags for the resources are specified in the tags member of the computeResources object passed in the [CreateComputeEnvironment](https://docs.aws.amazon.com/batch/latest/APIReference/API_CreateComputeEnvironment.html) API operation\. |  Yes  | 
|  AWS Batch jobs  |  Yes  | No\. Tags do not propagate to child jobs for array or multi\-node parallel \(MNP\) jobs\. |  Yes  | 
|  AWS Batch job definitions  |  Yes  | No\. |  Yes  | 
|  AWS Batch job queues  |  Yes  | No\. |  Yes  | 

## Tag restrictions<a name="tag-restrictions"></a>

The following basic restrictions apply to tags:
+ Maximum number of tags per resource – 50
+ For each resource, each tag key must be unique, and each tag key can have only one value\.
+ Maximum key length – 128 Unicode characters in UTF\-8
+ Maximum value length – 256 Unicode characters in UTF\-8
+ If your tagging schema is used across multiple AWS services and resources, remember that other services may have restrictions on allowed characters\. Generally allowed characters are letters, numbers, spaces representable in UTF\-8, and the following characters: \+ \- = \. \_ : / @\.
+ Tag keys and values are case sensitive\.
+ Don't use `aws:`, `AWS:`, or any upper or lowercase combination of such as a prefix for either keys or values, as it is reserved for AWS use\. You can't edit or delete tag keys or values with this prefix\. Tags with this prefix do not count against your tags\-per\-resource limit\.

## Working with tags using the console<a name="tag-resources-console"></a>

Using the AWS Batch console, you can manage the tags associated with new or existing compute environments, jobs, job definitions, and job queues\.

### Adding tags on an individual resource on creation<a name="adding-tags-creation"></a>

You can add tags to AWS Batch compute environments, jobs, job definitions, and job queues when you create them\.

### Adding and deleting tags on an individual resource<a name="adding-or-deleting-tags"></a>

AWS Batch allows you to add or delete tags associated with your clusters directly from the resource's page\. 

**To add or delete a tag on an individual resource**

1.  Open the AWS Batch console at [https://console\.aws\.amazon\.com/batch/](https://console.aws.amazon.com/batch/)\.

1. From the navigation bar, choose the Region to use\.

1. In the navigation pane, choose a resource type \(for example, **Job Queues**\)\.

1. Choose a specific resource, then choose **Edit tags**\.

1. Add or delete your tags as necessary\.
   + To add a tag — specify the key and value in the empty text boxes at the end of the list\.
   + To delete a tag — choose the ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/batch/latest/userguide/images/DeleteIcon.png) button next to the tag\.

1. Repeat this process for each tag you want to add or delete, and then choose **Edit tags** to finish\.

## Working with tags using the CLI or API<a name="tag-resources-api-sdk"></a>

Use the following AWS CLI commands or AWS Batch API operations to add, update, list, and delete the tags for your resources\.


**Tagging support for AWS Batch resources**  

| Task | API action | AWS CLI | AWS Tools for Windows PowerShell | 
| --- | --- | --- | --- | 
|  Add or overwrite one or more tags\.  |  [TagResource](https://docs.aws.amazon.com/batch/latest/APIReference/API_TagResource.html)  |  [tag\-resource](https://docs.aws.amazon.com/cli/latest/reference/batch/tag-resource.html)  |  [Add\-BATResourceTag](https://docs.aws.amazon.com/powershell/latest/reference/items/Add-BATResourceTag.html)  | 
|  Delete one or more tags\.  |  [UntagResource](https://docs.aws.amazon.com/batch/latest/APIReference/API_UntagResource.html)  |  [untag\-resource](https://docs.aws.amazon.com/cli/latest/reference/batch/untag-resource.html)  |  [Remove\-BATResourceTag](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-BATResourceTag.html)  | 
| List tags for a resource |  [ListTagsForResource](https://docs.aws.amazon.com/batch/latest/APIReference/API_ListTagsForResource.html)  |  [list\-tags\-for\-resource](https://docs.aws.amazon.com/cli/latest/reference/batch/list-tags-for-resource.html)  |  [Get\-BATResourceTag](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-BATResourceTag.html)  | 

The following examples show how to tag or untag resources using the AWS CLI\.

**Example 1: Tag an existing resource**  
The following command tags an existing resource\.

```
aws batch tag-resource --resource-arn resource_ARN --tags team=devs
```

**Example 2: Untag an existing resource**  
The following command deletes a tag from an existing resource\.

```
aws batch untag-resource --resource-arn resource_ARN --tag-keys tag_key
```

**Example 3: List tags for a resource**  
The following command lists the tags associated with an existing resource\.

```
aws batch list-tags-for-resource --resource-arn resource_ARN
```

Some resource\-creating actions enable you to specify tags when you create the resource\. The following actions support tagging on creation\.


| Task | API action | AWS CLI | AWS Tools for Windows PowerShell | 
| --- | --- | --- | --- | 
| Create a compute environment | [CreateComputeEnvironment](https://docs.aws.amazon.com/batch/latest/APIReference/API_CreateComputeEnvironment.html) | [create\-compute\-environment](https://docs.aws.amazon.com/cli/latest/reference/batch/create-compute-environment.html) | [New\-BATComputeEnvironment](https://docs.aws.amazon.com/powershell/latest/reference/items/New-BATComputeEnvironment.html) | 
| Create a job queue | [CreateJobQueue](https://docs.aws.amazon.com/batch/latest/APIReference/API_CreateJobQueue.html) | [create\-job\-queue](https://docs.aws.amazon.com/cli/latest/reference/batch/create-job-queue.html) | [New\-BATJobQueue](https://docs.aws.amazon.com/powershell/latest/reference/items/New-BATJobQueue.html) | 
| Register a job definition | [RegisterJobDefinition](https://docs.aws.amazon.com/batch/latest/APIReference/API_RegisterJobDefinition.html) | [register\-job\-definition](https://docs.aws.amazon.com/cli/latest/reference/batch/register-job-definition.html) | [Register\-BATJobDefinition](https://docs.aws.amazon.com/powershell/latest/reference/items/Register-BATJobDefinition.html) | 
| Submit a job | [SubmitJob](https://docs.aws.amazon.com/batch/latest/APIReference/API_SubmitJob.html) | [submit\-job](https://docs.aws.amazon.com/cli/latest/reference/batch/submit-job.html) | [Submit\-BATJob](https://docs.aws.amazon.com/powershell/latest/reference/items/Submit-BATJob.html) | 