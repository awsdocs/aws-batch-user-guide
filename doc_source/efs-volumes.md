# Amazon EFS volumes<a name="efs-volumes"></a>

Amazon Elastic File System \(Amazon EFS\) provides simple, scalable file storage for use with your AWS Batch jobs\. With Amazon EFS, storage capacity is elastic\. It scales automatically as you add and remove files\. Your applications can have the storage they need, when they need it\.

You can use Amazon EFS file systems with AWS Batch to export file system data across your fleet of container instances\. That way, your jobs have access to the same persistent storage\. However, you must configure your container instance AMI to mount the Amazon EFS file system before the Docker daemon starts\. Also, your job definitions must reference volume mounts on the container instance to use the file system\. The following sections help you get started using Amazon EFS with AWS Batch\.

## Amazon EFS volume considerations<a name="efs-volume-considerations"></a>

The following should be considered when using Amazon EFS volumes:
+ For jobs using EC2 resources, Amazon EFS file system support was added as a public preview with Amazon ECS optimized AMI version `20191212` with container agent version 1\.35\.0\. However, Amazon EFS file system support entered general availability with Amazon ECS optimized AMI version `20200319` with container agent version 1\.38\.0, which contained the Amazon EFS access point and IAM authorization features\. We recommend that you use Amazon ECS optimized AMI version `20200319` or later to take advantage of these features\. For more information, see [Amazon ECS optimized AMI versions](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-ami-versions.html) in the *Amazon Elastic Container Service Developer Guide*\.
**Note**  
If you create your own AMI, you must use container agent 1\.38\.0 or later, `ecs-init` version 1\.38\.0\-1 or later, and run the following commands on your Amazon EC2 instance\. This is all to enable the Amazon ECS volume plugin\. The commands are dependent on whether you're using Amazon Linux 2 or Amazon Linux as your base image\.  

Amazon Linux 2  

  ```
  $ yum install amazon-efs-utils
  systemctl enable --now amazon-ecs-volume-plugin
  ```

Amazon Linux  

  ```
  $ yum install amazon-efs-utils
  sudo shutdown -r now
  ```
+ For jobs using Fargate resources, Amazon EFS file system support was added when using platform version 1\.4\.0 or later\. For more information, see [AWS Fargate platform versions](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/platform_versions.html) in the *Amazon Elastic Container Service Developer Guide*\.
+ When specifying Amazon EFS volumes in jobs using Fargate resources, Fargate creates a supervisor container that is responsible for managing the Amazon EFS volume\. The supervisor container uses a small amount of the job's memory\. The supervisor container is visible when querying the task metadata version 4 endpoint\. For more information, see [Task metadata endpoint version 4](https://docs.aws.amazon.com/AmazonECS/latest/userguide/task-metadata-endpoint-v4-fargate.html) in the *Amazon Elastic Container Service User Guide for AWS Fargate*\.

## Using Amazon EFS access points<a name="efs-volume-accesspoints"></a>

Amazon EFS access points are application\-specific entry points into an EFS file system that help you to manage application access to shared datasets\. For more information about Amazon EFS access points and how to control access to them, see [Working with Amazon EFS Access Points](https://docs.aws.amazon.com/efs/latest/ug/efs-access-points.html) in the *Amazon Elastic File System User Guide*\.

Access points can enforce a user identity, including the user's POSIX groups, for all file system requests that are made through the access point\. Access points can also enforce a different root directory for the file system so that clients can only access data in the specified directory or its subdirectories\.

**Note**  
When creating an EFS access point, you specify a path on the file system to serve as the root directory\. When you reference the EFS file system with an access point ID in your AWS Batch job definition, the root directory must either be omitted or set to `/` This enforces the path that's set on the EFS access point\.

You can use an AWS Batch job IAM role to enforce that specific applications use a specific access point\. By combining IAM policies with access points, you can easily provide secure access to specific datasets for your applications\. This feature uses Amazon ECS IAM roles for task functionality\. For more information, see [IAM Roles for Tasks](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-iam-roles.html) in the *Amazon Elastic Container Service Developer Guide*\.

## Specifying an Amazon EFS file system in your job definition<a name="specify-efs-config"></a>

To use Amazon EFS file system volumes for your containers, you must specify the volume and mount point configurations in your job definition\. The following job definition JSON snippet shows the syntax for the `volumes` and `mountPoints` objects for a container:

```
{
    "containerProperties": [
        {
            "image": "amazonlinux:2",
            "command": [
                "ls",
                "-la",
                "/mount/efs"
            ],
            "mountPoints": [
                {
                    "sourceVolume": "myEfsVolume",
                    "containerPath": "/mount/efs",
                    "readOnly": true
                }
            ],
            "volumes": [
                {
                    "name": "myEfsVolume",
                    "efsVolumeConfiguration": {
                        "fileSystemId": "fs-12345678",
                        "rootDirectory": "/path/to/my/data",
                        "transitEncryption": "ENABLED",
                        "transitEncryptionPort": integer,
                        "authorizationConfig": {
                            "accessPointId": "fsap-1234567890abcdef1",
                            "iam": "ENABLED"
                        }
                    }
                }
            ]
        }
    ]
}
```

`efsVolumeConfiguration`  
Type: Object  
Required: No  
This parameter is specified when using Amazon EFS volumes\.    
`fileSystemId`  
Type: String  
Required: Yes  
The Amazon EFS file system ID to use\.  
`rootDirectory`  
Type: String  
Required: No  
The directory within the Amazon EFS file system to mount as the root directory inside the host\. If this parameter is omitted, the root of the Amazon EFS volume is used\. Specifying `/` has the same effect as omitting this parameter\. It can be up to 4,096 characters in length\.  
If an EFS access point is specified in the `authorizationConfig`, the root directory parameter must either be omitted or set to `/`\. This enforces the path that's set on the EFS access point\.  
`transitEncryption`  
Type: String  
Valid values: `ENABLED` \| `DISABLED`  
Required: No  
Determines whether to enable encryption for Amazon EFS data that's in transit between the AWS Batch host and the Amazon EFS server\. Transit encryption must be enabled if Amazon EFS IAM authorization is used\. If this parameter is omitted, the default value of `DISABLED` is used\. For more information, see [Encrypting data in transit](https://docs.aws.amazon.com/efs/latest/ug/encryption-in-transit.html) in the *Amazon Elastic File System User Guide*\.  
`transitEncryptionPort`  
Type: Integer  
Required: No  
The port to use when sending encrypted data between the AWS Batch host and the Amazon EFS server\. If you don't specify a transit encryption port, it uses the port selection strategy that the Amazon EFS mount helper uses\. The value must be between 0 and 65,535\. For more information, see [EFS Mount Helper](https://docs.aws.amazon.com/efs/latest/ug/efs-mount-helper.html) in the *Amazon Elastic File System User Guide*\.  
`authorizationConfig`  
Type: Object  
Required: No  
The authorization configuration details for the Amazon EFS file system\.    
`accessPointId`  
Type: String  
Required: No  
The access point ID to use\. If an access point is specified, the root directory value in the `efsVolumeConfiguration` must either be omitted or set to `/`\. This enforces the path that's set on the EFS access point\. If an access point is used, transit encryption must be enabled in the `EFSVolumeConfiguration`\. For more information, see [Working with Amazon EFS Access Points](https://docs.aws.amazon.com/efs/latest/ug/efs-access-points.html) in the *Amazon Elastic File System User Guide*\.  
`iam`  
Type: String  
Valid values: `ENABLED` \| `DISABLED`  
Required: No  
Determines whether to use the AWS Batch job IAM role that's defined in a job definition when mounting the Amazon EFS file system\. If enabled, transit encryption must be enabled in the `EFSVolumeConfiguration`\. If this parameter is omitted, the default value of `DISABLED` is used\. For more information about execution IAM roles, see [AWS Batch execution IAM role](execution-IAM-role.md)\.