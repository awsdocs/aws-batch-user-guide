# Creating a job queue<a name="create-job-queue"></a>

Before you can submit jobs in AWS Batch, you must create a job queue\. When you create a job queue, you associate one or more compute environments to the queue and assign an order of preference\.

You also set priority to the job queue that determines the order that the AWS Batch scheduler places jobs\. This means that, if a compute environment is associated with more than one job queue, the job queue with a higher priority is given preference\.