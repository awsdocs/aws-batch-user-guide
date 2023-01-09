# Creating an Amazon EKS job queue<a name="create-job-queue-eks"></a>

**To create an Amazon EKS job queue**

1. Open the AWS Batch console at [https://console\.aws\.amazon\.com/batch/](https://console.aws.amazon.com/batch/)\.

1. From the navigation bar, select the AWS Region to use\.

1. In the navigation pane, choose **Job queues**\.

1. Choose **Create**\.

1. For **Orchestration type**, choose **Amazon Elastic Kubernetes Service \(Amazon EKS\)**\.

1. For **Name**, enter a unique name for your job queue\. The name can be up to 128 characters long, and can contain uppercase and lowercase letters, numbers, and underscores \(\_\)\.

1. For **Priority**, enter an integer value for the job queue's priority\. Job queues with a higher priority are run before lower priority job queues that are associated with the same compute environment\. Priority is determined in descending order\. For example, a job queue with a priority value of 10 is given scheduling preference over a job queue with a priority value of 1\.

1. \(Optional\) For **Scheduling policy Amazon Resource Name \(ARN\)**, choose an existing scheduling policy\.

1. For **Connected compute environments**, select one or more compute environments from the list to associate with the job queue\. Select compute environments in the order that you want the queue to attempt job queue placement\. The job scheduler uses the order that you select compute environments in to determine which compute environment starts a given job\. Before you can associate them with a job queue, compute environments must be in the `VALID` state\. You can associate up to three compute environments with a job queue\.
**Note**  
All compute environments that are associated with a job queue must share the same provisioning model\. AWS Batch doesn't support mixing provisioning models in a single job queue\.
**Note**  
All compute environments that are associated with a job queue must share the same architecture\. AWS Batch doesn't support mixing compute environment architecture types in a single job queue\.

1. For **Compute environment order**, choose the up and down arrows to configure order that you want\.

1. Choose **Create job queue** to finish and create your job queue\.