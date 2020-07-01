# Array Jobs<a name="array_jobs"></a>

An array job is a job that shares common parameters, such as the job definition, vCPUs, and memory\. It runs as a collection of related, yet separate, basic jobs that may be distributed across multiple hosts and may run concurrently\. Array jobs are the most efficient way to execute extremely parallel jobs such as Monte Carlo simulations, parametric sweeps, or large rendering jobs\.

AWS Batch array jobs are submitted just like regular jobs\. However, you specify an array size \(between 2 and 10,000\) to define how many child jobs should run in the array\. If you submit a job with an array size of 1000, a single job runs and spawns 1000 child jobs\. The array job is a reference or pointer to manage all the child jobs\. This allows you to submit large workloads with a single query\. 

When you submit an array job, the parent array job gets a normal AWS Batch job ID\. Each child job has the same base ID, but the array index for the child job is appended to the end of the parent ID, such as `example_job_ID:0` for the first child job of the array\. 

At runtime, the `AWS_BATCH_JOB_ARRAY_INDEX` environment variable is set to the container's corresponding job array index number\. The first array job index is numbered `0`, and subsequent attempts are in ascending order \(1, 2, 3, and so on\)\. You can use this index value to control how your array job children are differentiated\. For more information, see [Tutorial: Using the Array Job Index to Control Job Differentiation](array_index_example.md)\.

For array job dependencies, you can specify a type for a dependency, such as `SEQUENTIAL` or `N_TO_N`\. You can specify a `SEQUENTIAL` type dependency \(without specifying a job ID\) so that each child array job completes sequentially, starting at index 0\. For example, if you submit an array job with an array size of 100, and specify a dependency with type `SEQUENTIAL`, 100 child jobs are spawned sequentially, where the first child job must succeed before the next child job starts\. The figure below shows Job A, an array job with an array size of 10\. Each job in Job A's child index is dependent on the previous child job\. Job A:1 can't start until job A:0 finishes\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/batch/latest/userguide/images/sequential-dep.png)

You can also specify an `N_TO_N` type dependency with a job ID for array jobs so that each index child of this job must wait for the corresponding index child of each dependency to complete before it can begin\. The figure below shows Job A and Job B, two array jobs with an array size of 10,000 each\. Each job in Job B's child index is dependent on the corresponding index in Job A\. Job B:1 can't start until job A:1 finishes\. 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/batch/latest/userguide/images/n-to-n-dep.png)

If you cancel or terminate a parent array job, all of the child jobs are cancelled or terminated with it\. You can cancel or terminate individual child jobs \(which moves them to the `FAILED` status\) without affecting the other child jobs\. However, if a child array job fails \(on its own or by cancelling/terminating manually\), the parent job also fails\.