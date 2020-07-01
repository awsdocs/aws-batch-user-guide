# Tutorial: Using the Array Job Index to Control Job Differentiation<a name="array_index_example"></a>

This tutorial shows how to use the `AWS_BATCH_JOB_ARRAY_INDEX` environment variable \(that each child job is assigned\) to differentiate the child jobs\. The example works by using the child job's index number to read a specific line in a file and substitute the parameter associated with that line number with a command inside the job's container\. The result is that you can have multiple AWS Batch jobs running the same Docker image and command arguments, but the results are different because the array job index is used as a modifier\.

In this tutorial, you create a text file that has all of the colors of the rainbow, each on its own line\. Then, you create an entrypoint script for a Docker container that converts the index into a value that can be used for a line number in the color file \(the index starts at zero, but line numbers start at one\)\. Create a Dockerfile that copies the color and index files to the container image and sets the image's `ENTRYPOINT` to the entrypoint script\. The Dockerfile and resources are built to a Docker image that is pushed to Amazon ECR\. You then register a job definition that uses your new container image, submit an AWS Batch array job with that job definition, and view the results\.

## Prerequisites<a name="array-tutorial-prereqs"></a>

This tutorial has the following prerequisites:
+ An AWS Batch compute environment\. For more information, see [Creating a Compute Environment](create-compute-environment.md)\.
+ An AWS Batch job queue and associated compute environment\. For more information, see [Creating a Job Queue](create-job-queue.md)\.
+ The AWS CLI installed on your local system\. For more information, see [Installing the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/installing.html) in the *AWS Command Line Interface User Guide*\.
+ Docker installed on your local system\. For more information, see [About Docker CE](https://docs.docker.com/install/) in the Docker documentation\.

## Step 1: Build a Container Image<a name="build-index-container"></a>

Although you can use the `AWS_BATCH_JOB_ARRAY_INDEX` in a job definition in the command parameter, it's easier and more powerful to create a container image that uses the variable in an entrypoint script\. This section describes how to create such a container image\.

**To build your Docker container image**

1. Create a new directory to use as your Docker image workspace and navigate to it\.

1. Create a file called `colors.txt` in your workspace directory and paste the contents below into it\.

   ```
   red
   orange
   yellow
   green
   blue
   indigo
   violet
   ```

1. Create a file called `print-color.sh` in your workspace directory and paste the contents below into it\.
**Note**  
The `LINE` variable is set to the `AWS_BATCH_JOB_ARRAY_INDEX` \+ 1 because the array index starts at 0, but line numbers start at 1\. The `COLOR` variable is set to the color in `colors.txt` that is associated with its line number\.

   ```
   #!/bin/sh
   LINE=$((AWS_BATCH_JOB_ARRAY_INDEX + 1))
   COLOR=$(sed -n ${LINE}p /tmp/colors.txt)
   echo My favorite color of the rainbow is $COLOR.
   ```

1. Create a file called `Dockerfile` in your workspace directory and paste the contents below into it\. This Dockerfile copies the previous files to your container and sets the entrypoint script to run when the container starts\.

   ```
   FROM busybox
   COPY print-color.sh /tmp/print-color.sh
   COPY colors.txt /tmp/colors.txt
   RUN chmod +x /tmp/print-color.sh
   ENTRYPOINT /tmp/print-color.sh
   ```

1. Build your Docker image:

   ```
   docker build -t print-color .
   ```

1. Test your container with the following script\. This script sets the `AWS_BATCH_JOB_ARRAY_INDEX` variable to 0 locally and then increments it to simulate what an array job with seven children would do\.

   ```
   AWS_BATCH_JOB_ARRAY_INDEX=0
   while [ $AWS_BATCH_JOB_ARRAY_INDEX -le 6 ]
   do
       docker run -e AWS_BATCH_JOB_ARRAY_INDEX=$AWS_BATCH_JOB_ARRAY_INDEX print-color
       AWS_BATCH_JOB_ARRAY_INDEX=$((AWS_BATCH_JOB_ARRAY_INDEX + 1))
   done
   ```

   Output:

   ```
   My favorite color of the rainbow is red.
   My favorite color of the rainbow is orange.
   My favorite color of the rainbow is yellow.
   My favorite color of the rainbow is green.
   My favorite color of the rainbow is blue.
   My favorite color of the rainbow is indigo.
   My favorite color of the rainbow is violet.
   ```

## Step 2: Push Your Image to Amazon ECR<a name="push-array-image"></a>

Now that you have built and tested your Docker container, you must push it to an image repository\. This example uses Amazon ECR, but you can also choose to use another registry, such as DockerHub\.

1. Create an Amazon ECR image repository to store your container image\. For the sake of brevity, this example uses the AWS CLI, but you can also use the AWS Management Console\. For more information, see [Creating a Repository](https://docs.aws.amazon.com/AmazonECR/latest/userguide/repository-create.html) in the *Amazon Elastic Container Registry User Guide*\.

   ```
   aws ecr create-repository --repository-name print-color
   ```

1. Tag your `print-color` image with your Amazon ECR repository URI that was returned from the previous step\.

   ```
   docker tag print-color aws_account_id.dkr.ecr.region.amazonaws.com/print-color
   ```

1. Login to your Amazon ECR registry\. For more information, see [Registry Authentication](https://docs.aws.amazon.com/AmazonECR/latest/userguide/Registries.html#registry_auth) in the *Amazon Elastic Container Registry User Guide*\.

   ```
   aws ecr get-login-password --region region | docker login --username AWS \
       --password-stdin aws_account_id.dkr.ecr.region.amazonaws.com
   ```

1. Push your image to Amazon ECR:

   ```
   docker push aws_account_id.dkr.ecr.region.amazonaws.com/print-color
   ```

## Step 3: Create and Register a Job Definition<a name="create-array-job-def"></a>

Now that your Docker image is in an image registry, you can specify it in an AWS Batch job definition and use it later to run an array job\. For the sake of brevity, this example uses the AWS CLI, but you can also use the AWS Management Console\. For more information, see [Creating a Job Definition](create-job-definition.md)\.

**To create a job definition**

1. Create a file called `print-color-job-def.json` in your workspace directory and paste the contents below into it\. Replace the image repository URI with your own image's URI\.

   ```
   {
     "jobDefinitionName": "print-color",
     "type": "container",
     "containerProperties": {
       "image": "aws_account_id.dkr.ecr.region.amazonaws.com/print-color",
       "vcpus": 1,
       "memory": 250
     }
   }
   ```

1. Register the job definition with AWS Batch:

   ```
   aws batch register-job-definition --cli-input-json file://print-color-job-def.json
   ```

## Step 4: Submit an AWS Batch Array Job<a name="submit-array-job"></a>

After you have registered your job definition, you can submit an AWS Batch array job that uses your new container image\.

**To submit an AWS Batch array job**

1. Create a file called `print-color-job.json` in your workspace directory and paste the contents below into it\.
**Note**  
This example assumes the default job queue name that is created by the AWS Batch first\-run wizard\. If your job queue name is different, replace the `first-run-job-queue` name with your job queue name\.

   ```
   {
     "jobName": "print-color",
     "jobQueue": "first-run-job-queue",
     "arrayProperties": {
       "size": 7
     },
     "jobDefinition": "print-color"
   }
   ```

1. Submit the job to your AWS Batch job queue\. Note the job ID that is returned in the output\.

   ```
   aws batch submit-job --cli-input-json file://print-color-job.json
   ```

1. Describe the job's status and wait for the job to move to `SUCCEEDED`\.

## Step 5: View Your Array Job Logs<a name="array-tutorial-logs"></a>

After your job reaches the `SUCCEEDED` status, you can view the CloudWatch Logs from the job's container\.

**To view your job's logs in CloudWatch Logs**

1. Open the AWS Batch console at [https://console\.aws\.amazon\.com/batch/](https://console.aws.amazon.com/batch/)\.

1. In the left navigation pane, choose **Jobs**\.

1. For **Job queue**, select a queue\. 

1. In the **Status** section, choose **succeeded**\.

1. To display all of the child jobs for your array job, select the job ID that was returned in the previous section\.

1. To see the logs from the job's container, select one of the child jobs and choose **View logs**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/batch/latest/userguide/images/array-logs.png)

1. View the other child job's logs\. Each job returns a different color of the rainbow\.