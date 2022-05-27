# EventBridge IAM role<a name="CWE_IAM_role"></a>

Amazon EventBridge delivers a near\-real time stream of system events that describe changes in Amazon Web Services resources\. AWS Batch jobs are available as EventBridge targets\. Using simple rules that you can quickly set up, you can match events and submit AWS Batch jobs in response to them\. Before you can submit AWS Batch jobs with EventBridge rules and targets, EventBridge must have permissions to run AWS Batch jobs on your behalf\.

**Note**  
When you create a rule in the EventBridge console that specifies an AWS Batch queue as a target, you're provided with an opportunity to create this role\. For an example walkthrough, see [AWS Batch Jobs as EventBridge Targets](batch-cwe-target.md)\.

The trust relationship for your EventBridge IAM role must provide the `events.amazonaws.com` service principal the ability to assume the role\.

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

Make sure that the policy that's attached to your EventBridge IAM role allows `batch:SubmitJob` permissions on your resources\. In the following example, AWS Batch provides the `AWSBatchServiceEventTargetRole` managed policy to provide these permissions\.

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