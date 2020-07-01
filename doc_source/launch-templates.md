# Launch Template Support<a name="launch-templates"></a>

AWS Batch supports using Amazon EC2 launch templates with your compute environments\. Launch template support allows you to modify the default configuration of your AWS Batch compute resources without requiring you to create customized AMIs\.

You must create a launch template before you can associate it with a compute environment\. You can create a launch template in the Amazon EC2 console, or you can use the AWS CLI or an AWS SDK\. For example, the JSON file below represents a launch template that resizes the Docker data volume for the default AWS Batch compute resource AMI and also sets it to be encrypted\.

```
{
    "LaunchTemplateName": "increase-container-volume-encrypt",
    "LaunchTemplateData": {
        "BlockDeviceMappings": [
            {
                "DeviceName": "/dev/xvdcz",
                "Ebs": {
                    "Encrypted": true,
                    "VolumeSize": 100,
                    "VolumeType": "gp2"
                }
            }
        ]
    }
}
```

You can create the above launch template by saving the JSON to a file called `lt-data.json` and running the following AWS CLI command:

```
aws ec2 --region <region> create-launch-template --cli-input-json file://lt-data.json
```

For more information about launch templates, see [Launching an Instance from a Launch Template](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-launch-templates.html) in the *Amazon EC2 User Guide for Linux Instances*\.

If you use a launch template to create your compute environment, you can move the following existing compute environment parameters to your launch template:

**Note**  
If any of these parameters \(with the exception of Amazon EC2 tags\) are specified both in the launch template and in the compute environment configuration, the compute environment parameters take precedence\. Amazon EC2 tags are merged between the launch template and the compute environment configuration\. If there is a collision on the tag's key, then the value in the compute environment configuration takes precedence\.
+ Amazon EC2 key pair
+ Amazon EC2 AMI ID
+ Security group IDs
+ Amazon EC2 tags

The following launch template parameters are **ignored** by AWS Batch:
+ Instance type \(specify your desired instance types when you create your compute environment\)
+ Instance role \(specify your desired instance role when you create your compute environment\)
+ Network interface subnets \(specify your desired subnets when you create your compute environment\)
+ Instance market options \(AWS Batch must control Spot Instance configuration\)
+ Disable API termination \(AWS Batch must control instance lifecycle\)

AWS Batch does not support updating a compute environment with a new launch template version\. If you update your launch template, you must create a new compute environment with the new template for the changes to take effect\. 

## Amazon EC2 User Data in Launch Templates<a name="lt-user-data"></a>

You can supply Amazon EC2 user data in your launch template that is executed by [cloud\-init](https://cloudinit.readthedocs.io/en/latest/index.html) when your instances launch\. Your user data can perform common configuration scenarios, including but not limited to:
+ [Including users or groups](https://cloudinit.readthedocs.io/en/latest/topics/examples.html#including-users-and-groups)
+ [Installing packages](https://cloudinit.readthedocs.io/en/latest/topics/examples.html#install-arbitrary-packages)
+ [Creating partitions and file systems](https://cloudinit.readthedocs.io/en/latest/topics/examples.html#create-partitions-and-filesystems)

Amazon EC2 user data in launch templates must be in the [MIME multi\-part archive](https://cloudinit.readthedocs.io/en/latest/topics/format.html#mime-multi-part-archive) format, because your user data is merged with other AWS Batch user data that is required to configure your compute resources\. You can combine multiple user data blocks together into a single MIME multi\-part file\. For example, you might want to combine a cloud boothook that configures the Docker daemon with a user data shell script that writes configuration information for the Amazon ECS container agent\.

If you are using AWS CloudFormation, the [AWS::CloudFormation::Init](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-init.html) type can be used with the [cfn\-init](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-init.html) helper script to perform common configuration scenarios\.

A MIME multi\-part file consists of the following components:
+ The content type and part boundary declaration: `Content-Type: multipart/mixed; boundary="==BOUNDARY=="`
+ The MIME version declaration: `MIME-Version: 1.0`
+ One or more user data blocks, which contain the following components:
  + The opening boundary, which signals the beginning of a user data block: `--==BOUNDARY==`
  + The content type declaration for the block: `Content-Type: text/cloud-config; charset="us-ascii"`\. For more information about content types, see the [Cloud\-Init documentation](https://cloudinit.readthedocs.io/en/latest/topics/format.html)\. 
  + The content of the user data, for example, a list of shell commands or `cloud-init` directives
+ The closing boundary, which signals the end of the MIME multi\-part file: `--==BOUNDARY==--`

Below are some example MIME multi\-part files that you can use to create your own\.

**Note**  
If you add user data to a launch template in the Amazon EC2 console, you can paste it in as plain text, or upload from a file\. If you use the AWS CLI or an AWS SDK, you must first `base64` encode the user data and submit that string as the value of the `UserData` parameter when you call [CreateLaunchTemplate](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateLaunchTemplate.html), as shown in the JSON below\.  

```
{
    "LaunchTemplateName": "base64-user-data",
    "LaunchTemplateData": {
        "UserData": "ewogICAgIkxhdW5jaFRlbXBsYXRlTmFtZSI6ICJpbmNyZWFzZS1jb250YWluZXItdm9sdW..."
    }
}
```

**Topics**
+ [Example: Mount an existing Amazon EFS file system](#example-mount-an-existing-amazon-efs-file-system)
+ [Example: Override default Amazon ECS container agent configuration](#example-override-default-amazon-ecs-container-agent-configuration)
+ [Example: Mount an existing Amazon FSx for Lustre file system](#example-mount-an-existing-amazon-fsx-for-lustre-file-system)

### Example: Mount an existing Amazon EFS file system<a name="example-mount-an-existing-amazon-efs-file-system"></a>

**Example**  
This example MIME multi\-part file configures the compute resource to install the `amazon-efs-utils` package and mount an existing Amazon EFS file system at `/mnt/efs`\.  

```
MIME-Version: 1.0
Content-Type: multipart/mixed; boundary="==MYBOUNDARY=="

--==MYBOUNDARY==
Content-Type: text/cloud-config; charset="us-ascii"

packages:
- amazon-efs-utils

runcmd:
- file_system_id_01=fs-abcdef123
- efs_directory=/mnt/efs

- mkdir -p ${efs_directory}
- echo "${file_system_id_01}:/ ${efs_directory} efs tls,_netdev" >> /etc/fstab
- mount -a -t efs defaults

--==MYBOUNDARY==--
```

### Example: Override default Amazon ECS container agent configuration<a name="example-override-default-amazon-ecs-container-agent-configuration"></a>

**Example**  
This example MIME multi\-part file overrides the default Docker image cleanup settings for a compute resource\.  

```
MIME-Version: 1.0
Content-Type: multipart/mixed; boundary="==MYBOUNDARY=="

--==MYBOUNDARY==
Content-Type: text/x-shellscript; charset="us-ascii"

#!/bin/bash
echo ECS_IMAGE_CLEANUP_INTERVAL=60m >> /etc/ecs/ecs.config
echo ECS_IMAGE_MINIMUM_CLEANUP_AGE=60m >> /etc/ecs/ecs.config

--==MYBOUNDARY==--
```

### Example: Mount an existing Amazon FSx for Lustre file system<a name="example-mount-an-existing-amazon-fsx-for-lustre-file-system"></a>

**Example**  
This example MIME multi\-part file configures the compute resource to install the `lustre2.10` package from the Extras Library and mount an existing Amazon FSx for Lustre file system at `/scratch`\. This example is for Amazon Linux 2\. For installation instructions for other Linux distributions, see [Installing the Lustre Client](https://docs.aws.amazon.com/fsx/latest/LustreGuide/install-lustre-client.html) in the *Amazon FSx for Lustre User Guide*\.  

```
MIME-Version: 1.0
Content-Type: multipart/mixed; boundary="==MYBOUNDARY=="

--==MYBOUNDARY==
Content-Type: text/cloud-config; charset="us-ascii"

runcmd:
- file_system_id_01=fs-0abcdef1234567890
- region=us-east-2
- fsx_directory=/scratch
- amazon-linux-extras install -y lustre2.10
- mkdir -p ${fsx_directory}
- mount -t lustre ${file_system_id_01}.fsx.${region}.amazonaws.com@tcp:fsx ${fsx_directory}

--==MYBOUNDARY==--
```
In the [volumes](https://docs.aws.amazon.com/batch/latest/APIReference/API_ContainerProperties.html#Batch-Type-ContainerProperties-volumes) and [mountPoints](https://docs.aws.amazon.com/batch/latest/APIReference/API_ContainerProperties.html#Batch-Type-ContainerProperties-mountPoints) members of the container properties the mount points must be mapped into the container\.  

```
{
    "volumes": [
        {
            "host": {
                "sourcePath": "/scratch"
            },
            "name": "Scratch"
        }
    ],
    "mountPoints": [
        {
            "containerPath": "/scratch",
            "sourceVolume": "Scratch"
        }
    ],
}
```