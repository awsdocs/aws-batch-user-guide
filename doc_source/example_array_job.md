# Example Array Job Workflow<a name="example_array_job"></a>

A common workflow for AWS Batch customers is to run a prerequisite setup job, run a series of commands against a large number of input tasks, and then conclude with a job that aggregates results and writes summary data to Amazon S3, DynamoDB, Amazon Redshift, or Aurora\.

For example:
+ `JobA`: A standard, non\-array job that performs a quick listing and metadata validation of objects in an Amazon S3 bucket, `BucketA`\. The [SubmitJob](https://docs.aws.amazon.com/batch/latest/APIReference/API_SubmitJob.html) JSON syntax is shown below\.

  ```
  {
      "jobName": "JobA",
      "jobQueue": "ProdQueue",
      "jobDefinition": "JobA-list-and-validate:1"
  }
  ```
+ `JobB`: An array job with 10,000 copies that is dependent upon `JobA`, that runs CPU\-intensive commands against each object in `BucketA` and uploads results to `BucketB`\. The [SubmitJob](https://docs.aws.amazon.com/batch/latest/APIReference/API_SubmitJob.html) JSON syntax is shown below\.

  ```
  {
      "jobName": "JobB",
      "jobQueue": "ProdQueue",
      "jobDefinition": "JobB-CPU-Intensive-Processing:1",
      "containerOverrides": {
          "vcpus": 32,
          "memory": 4096
     }
      "arrayProperties": {
          "size": 10000
      },
      "dependsOn": [
          {
              "jobId": "JobA_job_ID"
    }
      ]
  }
  ```
+ `JobC`: Another 10,000 copy array job that is dependent upon `JobB` with an `N_TO_N` dependency model, that runs memory\-intensive commands against each item in `BucketB`, writes metadata to DynamoDB, and uploads the resulting output to `BucketC`\. The [SubmitJob](https://docs.aws.amazon.com/batch/latest/APIReference/API_SubmitJob.html) JSON syntax is shown below\.

  ```
  {
      "jobName": "JobC",
      "jobQueue": "ProdQueue",
      "jobDefinition": "JobC-Memory-Intensive-Processing:1",
      "containerOverrides": {
          "vcpus": 1,
          "memory": 32768
     }
      "arrayProperties": {
          "size": 10000
      },
      "dependsOn": [
          {
              "jobId": "JobB_job_ID",
              "type": "N_TO_N"
          }
      ]
  }
  ```
+ `JobD`: An array job that performs 10 validation steps that each need to query DynamoDB and may interact with any of the above Amazon S3 buckets\. Each of the steps in `JobD` run the same command, but the behavior is different based on the value of the `AWS_BATCH_JOB_ARRAY_INDEX` environment variable within the job's container\. These validation steps run sequentially \(for example, `JobD:0`, then `JobD:1`, and so on\. The [SubmitJob](https://docs.aws.amazon.com/batch/latest/APIReference/API_SubmitJob.html) JSON syntax is shown below\.

  ```
  {
      "jobName": "JobD",
      "jobQueue": "ProdQueue",
      "jobDefinition": "JobD-Sequential-Validation:1",
      "containerOverrides": {
          "vcpus": 1,
          "memory": 32768
     }
      "arrayProperties": {
          "size": 10
      },
      "dependsOn": [
          {
              "jobId": "JobC_job_ID"
          },
          {
              "type": "SEQUENTIAL"
          },
   
      ]
  }
  ```
+ `JobE`: A final, non\-array job that performs some simple cleanup operations and sends an Amazon SNS notification with a message that the pipeline has completed and a link to the output URL\. The [SubmitJob](https://docs.aws.amazon.com/batch/latest/APIReference/API_SubmitJob.html) JSON syntax is shown below\.

  ```
  {
      "jobName": "JobE",
      "jobQueue": "ProdQueue",
      "jobDefinition": "JobE-Cleanup-and-Notification:1",
      "parameters": {
          "SourceBucket": "s3://JobD-Output-Bucket",
          "Recipient": "pipeline-notifications@mycompany.com"
      },
      "dependsOn": [
          {
              "jobId": "JobD_job_ID"
          }
      ]
  }
  ```