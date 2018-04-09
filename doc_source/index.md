# AWS Batch User Guide

-----
*****Copyright &copy; 2018 Amazon Web Services, Inc. and/or its affiliates. All rights reserved.*****

-----
Amazon's trademarks and trade dress may not be used in 
     connection with any product or service that is not Amazon's, 
     in any manner that is likely to cause confusion among customers, 
     or in any manner that disparages or discredits Amazon. All other 
     trademarks not owned by Amazon are the property of their respective
     owners, who may or may not be affiliated with, connected to, or 
     sponsored by Amazon.

-----
## Contents
+ [What Is AWS Batch?](what-is-batch.md)
+ [Setting Up with AWS Batch](get-set-up-for-aws-batch.md)
+ [Getting Started with AWS Batch](Batch_GetStarted.md)
+ [Jobs](jobs.md)
   + [Submitting a Job](submit_job.md)
   + [Job States](job_states.md)
   + [Automated Job Retries](job_retries.md)
   + [Job Dependencies](job_dependencies.md)
   + [Job Timeouts](job_timeouts.md)
   + [Array Jobs](array_jobs.md)
+ [Job Definitions](job_definitions.md)
   + [Creating a Job Definition](create-job-definition.md)
      + [Job Definition Template](job-definition-template.md)
   + [Job Definition Parameters](job_definition_parameters.md)
   + [Example Job Definitions](example-job-definitions.md)
+ [Job Queues](job_queues.md)
   + [Creating a Job Queue](create-job-queue.md)
      + [Job Queue Template](job-queue-template.md)
   + [Job Queue Parameters](job_queue_parameters.md)
+ [Job Scheduling](job_scheduling.md)
+ [Compute Environments](compute_environments.md)
   + [Compute Resource AMIs](compute_resource_AMIs.md)
      + [Creating a Compute Resource AMI](create-batch-ami.md)
      + [Creating a GPU Workload AMI](batch-gpu-ami.md)
   + [Creating a Compute Environment](create-compute-environment.md)
      + [Compute Environment Template](compute-environment-template.md)
   + [Compute Environment Parameters](compute_environment_parameters.md)
   + [Compute Resource Memory Management](memory-management.md)
+ [AWS Batch IAM Policies, Roles, and Permissions](IAM_policies.md)
   + [Policy Structure](iam-policy-structure.md)
   + [AWS Batch Managed Policy](batch_managed_policies.md)
   + [Creating AWS Batch IAM Policies](batch_IAM_user_policies.md)
   + [AWS Batch Service IAM Role](service_IAM_role.md)
   + [Amazon ECS Instance Role](instance_IAM_role.md)
   + [Amazon EC2 Spot Fleet Role](spot_fleet_IAM_role.md)
   + [CloudWatch Events IAM Role](CWE_IAM_role.md)
+ [AWS Batch Event Stream for CloudWatch Events](cloudwatch_event_stream.md)
   + [AWS Batch Events](batch_cwe_events.md)
   + [AWS Batch Jobs as CloudWatch Events Targets](batch-cwe-target.md)
   + [Tutorial: Listening for AWS Batch CloudWatch Events](batch_cwet.md)
   + [Tutorial: Sending Amazon Simple Notification Service Alerts for Failed Job Events](batch_sns_tutorial.md)
+ [Logging AWS Batch API Calls with AWS CloudTrail](logging-using-cloudtrail.md)
+ [Tutorial: Creating a VPC with Public and Private Subnets for Your Compute Environments](create-public-private-vpc.md)
+ [AWS Batch Service Limits](service_limits.md)
+ [Troubleshooting AWS Batch](troubleshooting.md)
+ [Document History](document_history.md)
+ [AWS Glossary](glossary.md)