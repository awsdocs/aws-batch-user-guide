# Creating a scheduling policy<a name="create-scheduling-policy"></a>

Before you can create a job queue with a scheduling policy, you must create a scheduling policy\. When you create a scheduling policy, you associate one or more fair share identifiers or fair share identifier prefixes with weights for the queue and optionally assign a decay period and compute reservation to the policy\.

**To create a scheduling policy**

1. Open the AWS Batch console at [https://console\.aws\.amazon\.com/batch/](https://console.aws.amazon.com/batch/)\.

1. From the navigation bar, select the Region to use\.

1. In the navigation pane, choose **Scheduling policies**, **Create**\.

1. For **Name**, enter a unique name for your scheduling policy\. Up to 128 letters \(uppercase and lowercase\), numbers, hyphens, and underscores are allowed\.

1. \(Optional\) For **Share decay seconds**, enter an integer value for the scheduling policy's share decay time\. A longer share decay time will use consider compute resource usage over a longer time when scheduling jobs\. This can allow jobs using a fair share identifier to temporarily use more compute resources than the weight for that fair share identifier would allow if that fair share identifier had not recently been using compute resources\.

1. \(Optional\) For **Compute reservation**, enter an integer value for the scheduling policy's compute reservation\. The compute reservation will hold some vCPUs in reserve to be used for fair share identifiers that are not currently active\.

   The reserved ratio is `(computeReservation/100)^ActiveFairShares` where *ActiveFairShares* is the number of active fair share identifiers\.

   For example, a `computeReservation` value of 50 indicates that AWS Batch should reserve 50% of the maximum available VCPU if there is only one fair share identifier, 25% if there are two fair share identifiers, and 12\.5% if there are three fair share identifiers\. A `computeReservation` value of 25 indicates that AWS Batch should reserve 25% of the maximum available VCPU if there is only one fair share identifier, 6\.25% if there are two fair share identifiers, and 1\.56% if there are three fair share identifiers\.

1. In the **Share attributes** section, you can specify the fair share identifier and weight for each fair share identifier to associate with the scheduling policy\.

   1. Choose **Add share identifier**\.

   1. For **Share identifier**, specify the fair share identifier\. If the string ends with '\*', this becomes a fair share identifier prefix used to match fair share identifiers for jobs\. All of the fair share identifiers and fair share identifier prefixes in a scheduling policy must be unique and cannot overlap\. For example, you can't have fair share identifiers prefix 'UserA\*' and fair share identifier 'UserA1' in the same scheduling policy\.

   1. For **Weight factor**, specify the relative weight for the fair share identifier\. The default value is 1\.0\. A lower value has a higher priority for compute resources\. If a fair share identifier prefix is used, jobs with fair share identifiers that start with the prefix will share the weight factor\. This effectively increases the weight factor for those jobs, lowering their individual priority but maintaining the same weight factor for the fair share identifier prefix\.

1. \(Optional\) In the **Tags** section, you can specify the key and value for each tag to associate with the scheduling policy\. For more information, see [Tagging your AWS Batch resources](using-tags.md)\.

1. Choose **Submit** to finish and create your scheduling policy\.