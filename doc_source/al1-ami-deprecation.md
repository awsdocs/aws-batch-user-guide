# Amazon Linux deprecation<a name="al1-ami-deprecation"></a>

AWS Batch will end support for the Amazon Linux AMI on June 30, 2023\. Standard support for Amazon Linux ended on December 31, 2020\. Maintenance support for Amazon Linux ends on June 30, 2023\. For more information about the Amazon Linux end\-of\-life, see [ Update on Amazon Linux AMI end\-of\-life](http://aws.amazon.com/blogs/blogs/aws/update-on-amazon-linux-ami-end-of-life/)

The Amazon Linux 2 AMI is the default image type for AWS Batch compute environments\. We recommend that you update existing Amazon Linux based compute environments to Amazon Linux 2 so that you continue to receive security and other updates\.

We will send reminders about this change starting on December 31, 2022\. If you use AWS Batch with managed Amazon Linux based compute environments, you will receive reminders about the Amazon Linux deprecation in email and in your Personal Health Dashboard \(PHD\)\.

On March 31, 2023, if you use managed Amazon Linux\-based compute environments or custom Amazon Linux\-based AMIs, you will receive reminders about this change in email and in your PHD\. You can't use the AWS Batch console or API to create new Amazon Linux based compute environments\.

On June 30, 2023, AWS Batch will end support for existing Amazon Linux based compute environments\. The status of existing AWS Batch Amazon Linux based compute environments will be moved to `INVALID`\.