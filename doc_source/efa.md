# Elastic Fabric Adapter<a name="efa"></a>

An Elastic Fabric Adapter \(EFA\) is a network device to accelerate High Performance Computing \(HPC\) applications\. AWS Batch supports applications that use EFA if the following conditions are met\.
+ Compute environment contains only supported instance types \(`c5n.18xlarge`, `c5n.metal`, `i3en.24xlarge`, `m5dn.24xlarge`, `m5n.24xlarge`, `r5dn.24xlarge`, `r5n.24xlarge`, and `p3dn.24xlarge`\)\.
+ The OS in the AMI supports EFA: Amazon Linux, Amazon Linux 2, Red Hat Enterprise Linux 7\.6, CentOS 7\.6, Ubuntu 16\.04, Ubuntu 18\.04\.
+ The AMI has the EFA driver loaded\.
+ The security group for the EFA must allows all inbound and outbound traffic to and from the security group itself\.
+ All instances that use an EFA should be in the same cluster placement group\.
+ The job definition must include a `devices` member with `hostPath` set to `/dev/infiniband/uverbs0` to allow the EFA device to be passed through to the container\. If `containerPath` is specified it must also be set to `/dev/infiniband/uverbs0`\. If `permissions` is set it must be set to `READ` \| `WRITE` \| `MKNOD`\.

  The location of the [LinuxParameters](https://docs.aws.amazon.com/batch/latest/APIReference/API_LinuxParameters.html) member will be different for multi\-node parallel jobs and single\-node container jobs\. The examples below demonstrate the differences but are missing required values\.  
**Example Example for multi\-node parallel job**  

  ```
  {
    "jobDefinitionName": "EFA-MNP-JobDef",
    "type": "multinode",
    "nodeProperties": {
      ...
      "nodeRangeProperties": [
        {
          ...
          "container": {
            ...
            "linuxParameters": {
              "devices": [
                {
                  "hostPath": "/dev/infiniband/uverbs0",
                  "containerPath": "/dev/infiniband/uverbs0",
                  "permissions": [
                      "READ", "WRITE", "MKNOD"
                  ]
                },
              ],
            },
          },
        },
      ],
    },
  }
  ```  
**Example Example for single\-node container job**  

  ```
  {
    "jobDefinitionName": "EFA-Container-JobDef",
    "type": "container",
    ...
    "containerProperties": {
      ...
      "linuxParameters": {
        "devices": [
          {
            "hostPath": "/dev/infiniband/uverbs0",
          },
        ],
      },
    },
  }
  ```

For more information about EFA, see [Elastic Fabric Adapter](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) in *Amazon EC2 User Guide for Linux Instances*\.