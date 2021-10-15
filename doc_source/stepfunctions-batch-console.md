# Orchestrate AWS Batch jobs with Step Functions state machines in the AWS Batch console<a name="stepfunctions-batch-console"></a>

You can use the AWS Batch console to view details about your Step Functions state machines and the functions that they use\.

**Topics**
+ [Viewing state machine details](#stepfunctions-configure)
+ [Editing a state machine](#stepfunctions-edit)
+ [Running a state machine](#stepfunctions-run)

## Viewing state machine details<a name="stepfunctions-configure"></a>

The AWS Batch console displays a list of your state machines in the current AWS Region that contain at least one workflow step that submits a AWS Batch job\.

Choose a state machine to view a graphical representation of the workflow\. Steps highlighted in blue represent AWS Batch jobs\. Use the graph controls to zoom in, zoom out, and center the graph\.

**Note**  
When a AWS Batch job is [dynamically referenced with JsonPath](https://docs.aws.amazon.com/step-functions/latest/dg/amazon-states-language-paths.html) in the state machine definition, the function details cannot be shown in the AWS Batch console\. Instead, the job name is listed as a **Dynamic reference**, and the corresponding steps in the graph are grayed out\.

**To view state machine details**

1. Open the AWS Batch console [Workflow orchestration powered by Step Functions page](https://console.aws.amazon.com/batch/home#stepfunctions)\.

1. Choose a state machine\.

   The AWS Batch console opens the **Details** page\.

For more information, see [Step Functions](https://docs.aws.amazon.com/step-functions/latest/dg/welcome.html) in the *AWS Step Functions Developer Guide*\.

## Editing a state machine<a name="stepfunctions-edit"></a>

When you want to edit a state machine, AWS Batch opens the **Edit definition** page of the Step Functions console\.

**To edit a state machine**

1. Open the AWS Batch console [Workflow orchestration powered by Step Functions page](https://console.aws.amazon.com/batch/home#stepfunctions)\.

1. Choose a state machine\.

1. Choose **Edit**\.

   The Step Functions console opens the **Edit definition** page\.

1. Edit the state machine and choose **Save**\.

For more information about editing state machines, see [Step Functions state machine language](https://docs.aws.amazon.com/step-functions/latest/dg/concepts-amazon-states-language.html) in the *AWS Step Functions Developer Guide*\.

## Running a state machine<a name="stepfunctions-run"></a>

When you want to run a state machine, AWS Batch opens the **New execution** page of the Step Functions console\.

**To run a state machine**

1. Open the AWS Batch console [Workflow orchestration powered by Step Functions page](https://console.aws.amazon.com/batch/home#stepfunctions)\.

1. Choose a state machine\.

1. Choose **Execute**\.

   The Step Functions console opens the **New execution** page\.

1. \(Optional\) Edit the state machine and choose **Start execution**\.

For more information about running state machines, see [Step Functions state machine execution concepts](https://docs.aws.amazon.com/step-functions/latest/dg/concepts-state-machine-executions.html) in the *AWS Step Functions Developer Guide*\.