# Creating a job queue<a name="create-job-queue"></a>

Before you can submit jobs in AWS Batch, you must create a job queue\. When you create a job queue, you associate one or more compute environments to the queue and assign an order of preference for the compute environments\.

You also set a priority to the job queue that determines the order in which the AWS Batch scheduler places jobs onto its associated compute environments\. For example, if a compute environment is associated with more than one job queue, the job queue with a higher priority is given preference for scheduling jobs to that compute environment\.

**To create a job queue**

1. Open the AWS Batch console at [https://console\.aws\.amazon\.com/batch/](https://console.aws.amazon.com/batch/)\.

1. From the navigation bar, select the Region to use\.

1. In the navigation pane, choose **Job queues**, **Create**\.

1. For **Job queue name**, enter a unique name for your job queue\. Up to 128 letters \(uppercase and lowercase\), numbers, and underscores are allowed\.

1. For **Priority**, enter an integer value for the job queue's priority\. Job queues with a higher priority \(or a higher integer value for the `priority` parameter\) are evaluated first when associated with the same compute environment\. Priority is determined in descending order, for example, a job queue with a priority value of 10 is given scheduling preference over a job queue with a priority value of 1\.

1. \(Optional\) Expand **Additional configuration**\.

   1. For **State, ** select **Enabled** so that your job queue can accept job submissions\.

1. \(Optional\) In the **Tags** section, you can specify the key and value for each tag to associate with the job queue\. For more information, see [Tagging your AWS Batch resources](using-tags.md)\.

1. In the **Connected compute environments** section, select one or more compute environments from the list to associate with the job queue, in the order that the queue should attempt placement\. The job scheduler uses compute environment order to determine which compute environment should start a given job\. Compute environments must be in the `VALID` state before you can associate them with a job queue\. You can associate up to three compute environments with a job queue\.
**Note**  
All compute environments that are associated with a job queue must share the same provisioning model, either EC2 \(**On\-Demand** and **Spot**\) or Fargate \(**Fargate** and **Fargate Spot**\)\. AWS Batch doesn't support mixing provisioning models in a single job queue\.
**Note**  
All compute environments that are associated with a job queue must share the same architecture\. AWS Batch doesn't support mixing compute environment architecture types in a single job queue\.

   You can change the order of compute environments by choosing the up and down arrows next to the **Order** column in the table\.

1. Choose **Create** to finish and create your job queue\.