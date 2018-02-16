# Example Job Definitions<a name="example-job-definitions"></a>

The following example job definitions illustrate how to use common patterns such as environment variables, parameter substitution, and volume mounts\.

## Use Environment Variables<a name="example-use-envvars"></a>

The following example job definition uses environment variables to specify a file type and Amazon S3 URL\. This particular example is from the [Creating a Simple "Fetch & Run" AWS Batch Job](https://aws.amazon.com/blogs/compute/creating-a-simple-fetch-and-run-aws-batch-job/) compute blog post\. The [https://github.com/awslabs/aws-batch-helpers/blob/master/fetch-and-run/fetch_and_run.sh](https://github.com/awslabs/aws-batch-helpers/blob/master/fetch-and-run/fetch_and_run.sh) script that is described in the blog post uses these environment variables to download the `myjob.sh` script from S3 and declare its file type\.

Although the command and environment variables are hard coded into the job definition in this example, you can submit a job with this definition and specify command and environment variable overrides to make the job definition more versatile\.

```
{
    "jobDefinitionName": "fetch_and_run",
    "type": "container",
    "containerProperties": {
        "image": "012345678910.dkr.ecr.us-east-1.amazonaws.com/fetch_and_run",
        "vcpus": 2,
        "memory": 2000,
        "command": [
            "myjob.sh",
            "60"
        ],
        "jobRoleArn": "arn:aws:iam::012345678910:role/AWSBatchS3ReadOnly",
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

The `Ref::` declarations in the `command` section are used to set placeholders for parameter substitution\. When you submit a job with this job definition, you specify the parameter overrides to fill in those values, such as the `inputfile` and `outputfile`\. The `parameters` section below sets a default for the `codec`, but you can override that parameter as well if you need to\.

For more information, see [Parameters](job_definition_parameters.md#parameters)\.

```
{
    "jobDefinitionName": "ffmpeg_parameters",
    "type": "container",
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
        "jobRoleArn": "arn:aws:iam::012345678910:role/ECSTask-S3FullAccess",
        "parameters": {"codec": "mp4"},
        "user": "nobody"
    }
}
```

## Test GPU Functionality<a name="example-test-gpu"></a>

The following example job definition tests if the GPU workload AMI described in [Creating a GPU Workload AMI](batch-gpu-ami.md) is configured properly\. The `volumes` and `mountPoints` sections must be configured to create a Docker volume that mounts the host path `/var/lib/nvidia-docker/volumes/nvidia_driver/latest` at `/usr/local/nvidia` on the container\. The container must also be privileged to access the GPU hardware\.

```
{
    "containerProperties": {
        "mountPoints": [{
            "sourceVolume": "nvidia",
            "readOnly": false,
            "containerPath": "/usr/local/nvidia"
        }],
        "image": "nvidia/cuda:9.0-cudnn7-devel",
        "vcpus": 2,
        "command": ["nvidia-smi"],
        "volumes": [{
            "host": {"sourcePath": "/var/lib/nvidia-docker/volumes/nvidia_driver/latest"},
            "name": "nvidia"
        }],
        "memory": 2000,
        "privileged": true,
        "ulimits": []
    },
    "type": "container",
    "jobDefinitionName": "nvidia-smi"
}
```

You can create a file with the JSON text above called `nvidia-smi.json` and then register an AWS Batch job definition with the following command:

```
aws batch register-job-definition --cli-input-json file://nvidia-smi.json
```

The image below shows what the volume and mount points should look like in the AWS Management Console\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/batch/latest/userguide/images/nvidia-smi-volume.png)