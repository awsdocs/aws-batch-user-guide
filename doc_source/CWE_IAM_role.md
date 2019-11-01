# CloudWatch Events IAM Role<a name="CWE_IAM_role"></a>

Amazon CloudWatch Events delivers a near\-real time stream of system events that describe changes in Amazon Web Services resources\. AWS Batch jobs are available as CloudWatch Events targets\. Using simple rules that you can quickly set up, you can match events and submit AWS Batch jobs in response to them\. Before you can submit AWS Batch jobs with CloudWatch Events rules and targets, CloudWatch Events must have permissions to run AWS Batch jobs on your behalf\.

**Note**  
When you create a rule in the CloudWatch Events console that specifies an AWS Batch queue as a target, you are provided with an opportunity to create this role\. For an example walkthrough, see [AWS Batch Jobs as CloudWatch Events Targets](batch-cwe-target.md)\.

The trust relationship for your CloudWatch Events IAM role must provide the `events.amazonaws.com` service principal the ability to assume the role, as shown below\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "",
      "Effect": "Allow",
      "Principal": {
        "Service": "events.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

The policy attached to your CloudWatch Events IAM role should allow `batch:SubmitJob` permissions on your resources\. AWS Batch provides the `AWSBatchServiceEventTargetRole` managed policy to provide these permissions, which are shown below\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "batch:SubmitJob"
       ],
      "Resource": "*"
    }
  ]
}
```