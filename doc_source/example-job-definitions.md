# Example Job Definitions<a name="example-job-definitions"></a>

The following example job definitions illustrate how to use common patterns such as environment variables, parameter substitution, and volume mounts\.

## Use Environment Variables<a name="example-use-envvars"></a>

The following example job definition uses environment variables to specify a file type and Amazon S3 URL\. This particular example is from the [Creating a Simple "Fetch & Run" AWS Batch Job](https://aws.amazon.com/blogs/compute/creating-a-simple-fetch-and-run-aws-batch-job/) compute blog post\. The [https://github.com/awslabs/aws-batch-helpers/blob/master/fetch-and-run/fetch_and_run.sh](https://github.com/awslabs/aws-batch-helpers/blob/master/fetch-and-run/fetch_and_run.sh) script that is described in the blog post uses these environment variables to download the `myjob.sh` script from S3 and declare its file type\.

Although the command and environment variables are hard\-coded into the job definition in this example, you can submit a job with this definition and specify command and environment variable overrides to make the job definition more versatile\.

```
{
    "jobDefinitionName": "fetch_and_run",
    "type": "container",
    "containerProperties": {
        "image": "123456789012.dkr.ecr.us-east-1.amazonaws.com/fetch_and_run",
        "vcpus": 2,
        "memory": 2000,
        "command": [
            "myjob.sh",
            "60"
        ],
        "jobRoleArn": "arn:aws:iam::123456789012:role/AWSBatchS3ReadOnly",
        "environment": [
            {
                "name": "BATCH_FILE_S3_URL",
                "value": "s3://my-batch-scripts/myjob.sh"
            },
            {
                "name": "BATCH_FILE_TYPE",
                "value": "script"
            }
        ],
        "user": "nobody"
    }
}
```

## Using Parameter Substitution<a name="example-use-parameters"></a>

The following example job definition illustrates how to allow for parameter substitution and to set default values\.

The `Ref::` declarations in the `command` section are used to set placeholders for parameter substitution\. When you submit a job with this job definition, you specify the parameter overrides to fill in those values, such as the `inputfile` and `outputfile`\. The `parameters` section below sets a default for `codec`, but you can override that parameter as needed\.

For more information, see [Parameters](job_definition_parameters.md#parameters)\.

```
{
    "jobDefinitionName": "ffmpeg_parameters",
    "type": "container",
    "parameters": {"codec": "mp4"},
    "containerProperties": {
        "image": "my_repo/ffmpeg",
        "vcpus": 2,
        "memory": 2000,
        "command": [
            "ffmpeg",
            "-i",
            "Ref::inputfile",
            "-c",
            "Ref::codec",
            "-o",
            "Ref::outputfile"
        ],
        "jobRoleArn": "arn:aws:iam::123456789012:role/ECSTask-S3FullAccess",
        "user": "nobody"
    }
}
```

## Test GPU Functionality<a name="example-test-gpu"></a>

The following example job definition tests if the GPU workload AMI described in [Using a GPU Workload AMI](batch-gpu-ami.md) is configured properly\. This example job definition runs the Tensorflow deep MNIST classifier [example](https://github.com/tensorflow/tensorflow/blob/r1.8/tensorflow/examples/tutorials/mnist/mnist_deep.py) from GitHub\.

```
{
    "containerProperties": {
        "image": "tensorflow/tensorflow:1.8.0-devel-gpu",
        "vcpus": 8,
        "command": [
            "sh",
            "-c",
            "cd /tensorflow/tensorflow/examples/tutorials/mnist; python mnist_deep.py"
        ],
        "memory": 32000
    },
    "type": "container",
    "jobDefinitionName": "tensorflow_mnist_deep"
}
```

You can create a file with the JSON text above called `tensorflow_mnist_deep.json` and then register an AWS Batch job definition with the following command:

```
aws batch register-job-definition --cli-input-json file://tensorflow_mnist_deep.json
```

## Multi\-node Parallel Job<a name="example-mnp-job-definition"></a>

The following example job definition illustrates a multi\-node parallel job\. For more details, see [Building a tightly coupled molecular dynamics workflow with multi\-node parallel jobs in AWS Batch](http://aws.amazon.com/blogs/compute/building-a-tightly-coupled-molecular-dynamics-workflow-with-multi-node-parallel-jobs-in-aws-batch/) in the *AWS Compute* blog\.

```
{
  "jobDefinitionName": "gromacs-jobdef",
  "jobDefinitionArn": "arn:aws:batch:us-east-2:123456789012:job-definition/gromacs-jobdef:1",
  "revision": 6,
  "status": "ACTIVE",
  "type": "multinode",
  "parameters": {},
  "nodeProperties": {
    "numNodes": 2,
    "mainNode": 0,
    "nodeRangeProperties": [
      {
        "targetNodes": "0:1",
        "container": {
          "image": "123456789012.dkr.ecr.us-east-2.amazonaws.com/gromacs_mpi:latest",
          "vcpus": 8,
          "memory": 24000,
          "command": [],
          "jobRoleArn": "arn:aws:iam::123456789012:role/ecsTaskExecutionRole",
          "ulimits": [],
          "instanceType": "p3.2xlarge"
        }
      }
    ]
  }
}
```