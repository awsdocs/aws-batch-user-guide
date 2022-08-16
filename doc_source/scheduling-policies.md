# Scheduling policies<a name="scheduling-policies"></a>

Scheduling policies allow compute resources in a job queue to be allocated in a more equitable manner between different users or workloads\. Different workloads or users are assigned different fair share identifiers\. AWS Batch assigns each fair share identifier a share based on the total weight of all recently used fair share identifiers, which defines the amount of the total resources available for use by jobs with that fair share identifier\. Time can be added to the fair share analysis by assigning a share decay time to the policy\. A long decay time gives more weight to time and less to the defined weight\. Compute resources can be held in reserve for fair share identifiers that are not active by specifying a compute reservation\.

**Topics**
+ [Creating a scheduling policy](create-scheduling-policy.md)
+ [Scheduling policy parameters](scheduling-policy-parameters.md)