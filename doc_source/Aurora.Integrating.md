# Integrating Aurora with Other AWS Services<a name="Aurora.Integrating"></a>

Integrate Amazon Aurora with other AWS services so that you can extend your Aurora DB cluster to use additional capabilities in the AWS Cloud\. 

**Topics**
+ [Integrating AWS Services with Amazon Aurora MySQL](#Aurora.Integrating.AuroraMySQL)
+ [Integrating AWS Services with Amazon Aurora PostgreSQL](#Aurora.Integrating.AuroraPostgreSQL)
+ [Using Amazon Aurora Auto Scaling with Aurora Replicas](Aurora.Integrating.AutoScaling.md)
+ [Using Machine Learning \(ML\) Capabilities with Amazon Aurora](aurora-ml.md)

## Integrating AWS Services with Amazon Aurora MySQL<a name="Aurora.Integrating.AuroraMySQL"></a>

Amazon Aurora MySQL integrates with other AWS services so that you can extend your Aurora MySQL DB cluster to use additional capabilities in the AWS Cloud\. Your Aurora MySQL DB cluster can use AWS services to do the following:
+ Synchronously or asynchronously invoke an AWS Lambda function using the native functions `lambda_sync` or `lambda_async`\. Or, asynchronously invoke an AWS Lambda function using the `mysql.lambda_async` procedure\.
+ Load data from text or XML files stored in an Amazon S3 bucket into your DB cluster using the `LOAD DATA FROM S3` or `LOAD XML FROM S3` command\.
+ Save data to text files stored in an Amazon S3 bucket from your DB cluster using the `SELECT INTO OUTFILE S3` command\.
+ Automatically add or remove Aurora Replicas with Application Auto Scaling\. For more information, see [Using Amazon Aurora Auto Scaling with Aurora Replicas](Aurora.Integrating.AutoScaling.md)\.

For more information about integrating Aurora MySQL with other AWS services, see [Integrating Amazon Aurora MySQL with Other AWS Services](AuroraMySQL.Integrating.md)\.

## Integrating AWS Services with Amazon Aurora PostgreSQL<a name="Aurora.Integrating.AuroraPostgreSQL"></a>

Amazon Aurora PostgreSQL integrates with other AWS services so that you can extend your Aurora PostgreSQL DB cluster to use additional capabilities in the AWS Cloud\. Your Aurora PostgreSQL DB cluster can use AWS services to do the following:
+ Quickly collect, view, and assess performance on your relational database workloads with Performance Insights\.
+ Automatically add or remove Aurora Replicas with Aurora Auto Scaling\. For more information, see [Using Amazon Aurora Auto Scaling with Aurora Replicas](Aurora.Integrating.AutoScaling.md)\.

For more information about integrating Aurora PostgreSQL with other AWS services, see [Integrating Amazon Aurora PostgreSQL with Other AWS Services](AuroraPostgreSQL.Integrating.md)\.