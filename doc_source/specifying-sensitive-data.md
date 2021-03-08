# Specifying sensitive data<a name="specifying-sensitive-data"></a>

With AWS Batch, you can inject sensitive data into your jobs by storing your sensitive data in either AWS Secrets Manager secrets or AWS Systems Manager Parameter Store parameters, and then reference them in your job definition\.

Secrets can be exposed to a job in the following ways:
+ To inject sensitive data into your containers as environment variables, use the `secrets` job definition parameter\.
+ To reference sensitive information in the log configuration of a job, use the `secretOptions` job definition parameter\.

**Topics**
+ [Specifying sensitive data using Secrets Manager](specifying-sensitive-data-secrets.md)
+ [Specifying sensitive data using Systems Manager Parameter Store](specifying-sensitive-data-parameters.md)