# Creating a Job Queue<a name="create-job-queue"></a>

Before you can submit jobs in AWS Batch, you must create a job queue\. When you create a job queue, you associate one or more compute environments to the queue and assign an order of preference for the compute environments\.

You also set a priority to the job queue that determines the order in which the AWS Batch scheduler places jobs onto its associated compute environments\. For example, if a compute environment is associated with more than one job queue, the job queue with a higher priority is given preference for scheduling jobs to that compute environment\.

**To create a job queue**

1. Open the AWS Batch console at [https://console\.aws\.amazon\.com/batch/](https://console.aws.amazon.com/batch/)\.

1. From the navigation bar, select the region to use\.

1. In the navigation pane, choose **Job queues**, **Create queue**\.

1. For **Queue name**, enter a unique name for your job queue\.

1. Ensure that **Enable job queue** is selected so that your job queue can accept job submissions\.

1. For **Priority**, enter an integer value for the job queue's priority\. Job queues with a higher priority \(or a higher integer value for the `priority` parameter\) are evaluated first when associated with the same compute environment\. Priority is determined in descending order, for example, a job queue with a priority value of 10 is given scheduling preference over a job queue with a priority value of 1\.

1. In the **Connected compute environments for this queue** section, select one or more compute environments from the list to associate with the job queue, in the order that the queue should attempt placement\. The job scheduler uses compute environment order to determine which compute environment should execute a given job\. Compute environments must be in the `VALID` state before you can associate them with a job queue\. You can associate up to three compute environments with a job queue\.
**Note**  
All compute environments that are associated with a job queue must share the same architecture\. AWS Batch doesn't support mixing compute environment architecture types in a single job queue\.

   You can change the order of compute environments by choosing the up and down arrows next to the **Order** column in the table\.

1. Choose **Create** to finish and create your job queue\.