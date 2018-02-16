# Array Jobs<a name="array_jobs"></a>

An array job is a job that shares common parameters, such as the job definition, vCPUs, and memory\. It runs as a collection of related, yet separate, basic jobs that may be distributed across multiple hosts and may run concurrently\. Array jobs are the most efficient way to execute embarrassingly parallel jobs such as Monte Carlo simulations, parametric sweeps, or large rendering jobs\.

AWS Batch array jobs are submitted just like regular jobs\. However, you specify an array size \(between 2 and 10,000\) to define how many child jobs should run in the array\. If you submit a job with an array size of 1000, a single job runs and spawns 1000 child jobs\. The array job is a reference or pointer to manage all the child jobs\. This allows you to submit large workloads with a single query\. 

When you submit an array job, the parent array job gets a normal AWS Batch job ID\. Each child job has the same base ID, but the array index for the child job is appended to the end of the parent ID, such as `example_job_ID:0` for the first child job of the array\. 

At runtime, the `AWS_BATCH_JOB_ARRAY_INDEX` environment variable is set to the container's corresponding job array index number\. The first array job index is numbered `0`, and subsequent attempts are in ascending order \(1, 2, 3, and so on\)\.

For array job dependencies, you can specify a type for a dependency, such as `SEQUENTIAL` or `N_TO_N`\. You can specify a `SEQUENTIAL` type dependency \(without specifying a job ID\) so that each child array job completes sequentially, starting at index 0\. For example, if you submit an array job with an array size of 100, and specify a dependency with type `SEQUENTIAL`, 100 child jobs are spawned sequentially, where the first child job must succeed before the next child job starts\. The figure below shows Job A, an array job with an array size of 10\. Each job in Job A's child index is dependent on the previous child job\. Job A:1 can't start until job A:0 finishes\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/batch/latest/userguide/images/sequential-dep.png)

You can also specify an `N_TO_N` type dependency with a job ID for array jobs so that each index child of this job must wait for the corresponding index child of each dependency to complete before it can begin\. The figure below shows Job A and Job B, two array jobs with an array size of 4 each\. Each job in Job B's child index is dependent on the corresponding index in Job A\. Job B:1 can't start until job A:1 finishes\. 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/batch/latest/userguide/images/n-to-n-dep.png)

If you cancel or terminate a parent array job, all of the child jobs are cancelled or terminated with it\. You can cancel or terminate individual child jobs \(which moves them to the `FAILED` status\) without affecting the other child jobs\. However, if a child array job fails \(on its own or by cancelling/terminating manually\), the parent job also fails\.

## Example Array Job Workflow<a name="example_array_job"></a>

A common workflow for AWS Batch customers is to run a prerequisite setup job, run a series of commands against a large number of input tasks, and then conclude with a job that aggregates results and writes summary data to Amazon S3, DynamoDB, Amazon Redshift, or Aurora\.

For example:

+ `JobA`: A standard, non\-array job that performs a quick listing and metadata validation of objects in an Amazon S3 bucket, `BucketA`\. The [SubmitJob](http://docs.aws.amazon.com/batch/latest/APIReference/API_SubmitJob.html) JSON syntax is shown below\.

  ```
  {
      "jobName": "JobA",
      "jobQueue": "ProdQueue",
      "jobDefinition": "JobA-list-and-validate:1"
  }
  ```

+ `JobB`: An array job with 10,000 copies that is dependent upon `JobA`, that runs CPU\-intensive commands against each object in `BucketA` and uploads results to `BucketB`\. The [SubmitJob](http://docs.aws.amazon.com/batch/latest/APIReference/API_SubmitJob.html) JSON syntax is shown below\.

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

+ `JobC`: Another 10,000 copy array job that is dependent upon `JobB` with an `N_TO_N` dependency model, that runs memory\-intensive commands against each item in `BucketB`, writes metadata to DynamoDB, and uploads the resulting output to `BucketC`\. The [SubmitJob](http://docs.aws.amazon.com/batch/latest/APIReference/API_SubmitJob.html) JSON syntax is shown below\.

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

+ `JobD`: An array job that performs 10 validation steps that each need to query DynamoDB and may interact with any of the above Amazon S3 buckets\. Each of the steps in `JobD` run the same command, but the behavior is different based on the value of the `AWS_BATCH_JOB_ARRAY_INDEX` environment variable within the job's container\. These validation steps run sequentially \(for example, `JobD:0`, then `JobD:1`, and so on\. The [SubmitJob](http://docs.aws.amazon.com/batch/latest/APIReference/API_SubmitJob.html) JSON syntax is shown below\.

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

+ `JobE`: A final, non\-array job that performs some simple cleanup operations and sends an Amazon SNS notification with a message that the pipeline has completed and a link to the output URL\. The [SubmitJob](http://docs.aws.amazon.com/batch/latest/APIReference/API_SubmitJob.html) JSON syntax is shown below\.

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