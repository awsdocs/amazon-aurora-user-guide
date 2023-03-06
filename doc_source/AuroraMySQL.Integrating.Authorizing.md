# Authorizing Amazon Aurora MySQL to access other AWS services on your behalf<a name="AuroraMySQL.Integrating.Authorizing"></a>

For your Aurora MySQL DB cluster to access other services on your behalf, create and configure an AWS Identity and Access Management \(IAM\) role\. This role authorizes database users in your DB cluster to access other AWS services\. For more information, see [Setting up IAM roles to access AWS services](AuroraMySQL.Integrating.Authorizing.IAM.md)\.

You must also configure your Aurora DB cluster to allow outbound connections to the target AWS service\. For more information, see [Enabling network communication from Amazon Aurora MySQL to other AWS services](AuroraMySQL.Integrating.Authorizing.Network.md)\.

If you do so, your database users can perform these actions using other AWS services:
+ Synchronously or asynchronously invoke an AWS Lambda function using the native functions `lambda_sync` or `lambda_async`\. Or, asynchronously invoke an AWS Lambda function using the `mysql.lambda_async` procedure\. For more information, see [Invoking a Lambda function with an Aurora MySQL native function](AuroraMySQL.Integrating.Lambda.md#AuroraMySQL.Integrating.NativeLambda)\.
+ Load data from text or XML files stored in an Amazon S3 bucket into your DB cluster by using the `LOAD DATA FROM S3` or `LOAD XML FROM S3` statement\. For more information, see [Loading data into an Amazon Aurora MySQL DB cluster from text files in an Amazon S3 bucket](AuroraMySQL.Integrating.LoadFromS3.md)\.
+ Save data from your DB cluster into text files stored in an Amazon S3 bucket by using the `SELECT INTO OUTFILE S3` statement\. For more information, see [Saving data from an Amazon Aurora MySQL DB cluster into text files in an Amazon S3 bucket](AuroraMySQL.Integrating.SaveIntoS3.md)\.
+ Export log data to Amazon CloudWatch Logs MySQL\. For more information, see [Publishing Amazon Aurora MySQL logs to Amazon CloudWatch Logs](AuroraMySQL.Integrating.CloudWatch.md)\.
+ Automatically add or remove Aurora Replicas with Application Auto Scaling\. For more information, see [Using Amazon Aurora Auto Scaling with Aurora replicas](Aurora.Integrating.AutoScaling.md)\.

## Related topics<a name="AuroraMySQL.Integrating.Authorizing.RelatedTopics"></a>
+ [Integrating Aurora with other AWS services](Aurora.Integrating.md)
+ [Managing an Amazon Aurora DB cluster](CHAP_Aurora.md)