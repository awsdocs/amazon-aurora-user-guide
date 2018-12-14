# Using the Data API for Aurora Serverless<a name="data-api"></a>


|  | 
| --- |
| The Data API is in beta for Aurora Serverless and is subject to change\. | 

You can access your Aurora Serverless DB cluster using a built\-in Data API\. Using this API, you can access Aurora Serverless with web services–based applications, including AWS Lambda and AWS AppSync\. 

The Data API doesn't require a persistent connection to the DB cluster\. Instead, it provides a secure HTTP endpoint and integration with AWS SDKs\. You can use the endpoint to execute SQL statements in the same way as you do through the MySQL native protocol, but without managing connections\. The API returns query results in JSON format\. All calls to the Data API are synchronous\. A call times out and is terminated in one minute if it's not finished processing\.

The API uses database credentials stored in AWS Secrets Manager, so you don't need to pass credentials in the API calls\. If you are using AWS Lambda, the API provides a secure way for you to access your DB cluster without configuring a Lambda function to access resources in an Amazon VPC\. For more information about AWS Secrets Manager, see [What Is AWS Secrets Manager?](https://docs.aws.amazon.com/secretsmanager/latest/userguide/intro.html) in the *AWS Secrets Manager User Guide*\.

When you enable the Data API, you can also use the query editor for Aurora Serverless\. For more information, see [Using the Query Editor for Aurora Serverless](query-editor.md)\.

## Availability of the Data API<a name="data-api.regions"></a>

Currently, the Data API is only available in the US East \(N\. Virginia\) Region\. Link to `rds-data.us-east-1.amazonaws.com` using the HTTPS protocol to access the Data API\. 

## Enabling the Data API<a name="data-api.enabling"></a>

To use the Data API, you must enable it for your Aurora Serverless DB cluster\. You can enable the Data API when you modify the DB cluster\.

You can enable the Data API by using the RDS console when you modify an Aurora Serverless DB cluster\. You do so by enabling the Data API in the RDS console's **Network & Security** section\.

The following screenshot shows the enabled **Data API**\.

![\[Enabling the Data API for an Aurora Serverless DB cluster with console\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/web-server-api-endpoint.png)

For instructions, see [Modifying an Aurora Serverless DB Cluster](aurora-serverless.modifying.md)\.

## Storing Database Credentials in AWS Secrets Manager<a name="data-api.secrets"></a>

When you call the Data API, you can pass credentials for the Aurora Serverless DB cluster by using a secret in AWS Secrets Manager\. To pass credentials in this way, you specify the name of the secret or the Amazon Resource Name \(ARN\) of the secret\.

**To store DB cluster credentials in a secret**

1. Use AWS Secrets Manager to create a secret that contains credentials for the Aurora DB cluster\.

   For instructions, see [Creating a Basic Secret](https://docs.aws.amazon.com/secretsmanager/latest/userguide/manage_create-basic-secret.html) in the *AWS Secrets Manager User Guide*\.

1. Use the AWS Secrets Manager console to view the details for the secret you created, or run the `describe-secret` AWS CLI command\.

   Note the name and ARN of the secret\. You can use them in calls to the Data API\.

## Calling the Data API<a name="data-api.calling"></a>

After you enable the Data API for an Aurora Serverless DB cluster, you can call the Data API to run SQL statements against the cluster\.

You can call the Data API from applications or using the AWS Command Line Interface \(AWS CLI\)\.

To use the AWS CLI, use the `aws rds-data execute-sql` command, and specify the following options:
+ `--db-cluster-or-instance-arn` – The Amazon Resource Name \(ARN\) of the Aurora Serverless DB cluster\.
+ `--database` – The name of the database to run the SQL statement on\.
+ `--schema` – The name of the schema to run the SQL statement on\.
+ `--aws-secret-store-arn` – The name or ARN of the secret that enables access to the DB cluster\.
+ `--sql-statements` – One or more SQL statements to run against the DB cluster\.

  You can separate SQL statements with a semicolon \(`;`\)\. Any valid SQL statement is permitted including data definition, data manipulation, and commit statements\.
+ `--region` – The AWS Region that contains the Aurora Serverless DB cluster\.
+ `--no-verify-ssl` – Overrides the default behavior of verifying SSL certificates\.
+ `--endpoint-url` – The URL of the HTTP endpoint for the RDS API in the given AWS Region\. For the US East \(N\. Virginia\) Region, the URL is `https://rds-data.us-east-1.amazonaws.com`\.
+ `--profile` – A specific profile from your credential file\.

The DB cluster returns a response for each call\.

**Note**  
In the response, the row limit is 1,000 rows, and the size limit is 1 MB\.

For example, the following AWS CLI command runs a single SQL statement\.

For Linux, OS X, or Unix:

```
aws rds-data execute-sql --db-cluster-or-instance-arn arn:aws:rds:us-east-1:123456789012:cluster:mydbcluster --schema "" \
--database "mydatabase" --aws-secret-store-arn "arn:aws:secretsmanager:us-east-1:123456789012:secret:mysecret" \
--sql-statements "select * from mytable" --region us-east-1 --no-verify-ssl \
--endpoint-url https://rds-data.us-east-1.amazonaws.com --profile myprofile
```

For Windows:

```
aws rds-data execute-sql --db-cluster-or-instance-arn arn:aws:rds:us-east-1:123456789012:cluster:mydbcluster --schema "" ^
--database "mydatabase" --aws-secret-store-arn "arn:aws:secretsmanager:us-east-1:123456789012:secret:mysecret" ^
--sql-statements "select * from mytable" --region us-east-1 --no-verify-ssl ^
--endpoint-url https://rds-data.us-east-1.amazonaws.com --profile myprofile
```

The following AWS CLI command runs multiple SQL statements and commits the changes\.

For Linux, OS X, or Unix:

```
aws rds-data execute-sql --db-cluster-or-instance-arn arn:aws:rds:us-east-1:123456789012:cluster:mydbcluster --schema "" \
--database "mydatabase" --aws-secret-store-arn "arn:aws:secretsmanager:us-east-1:123456789012:secret:mysecret" \
--sql-statements "create table mytable(c1 INT, c2 VARCHAR(40));insert into mytable(c1,c2)values(1,"Data1");commit;" \
--region us-east-1 --no-verify-ssl --endpoint-url https://rds-data.us-east-1.amazonaws.com \
--profile myprofile
```

For Windows:

```
aws rds-data execute-sql --db-cluster-or-instance-arn arn:aws:rds:us-east-1:123456789012:cluster:mydbcluster --schema "" ^
--database "mydatabase" --aws-secret-store-arn "arn:aws:secretsmanager:us-east-1:123456789012:secret:mysecret" ^
--sql-statements "create table mytable(c1 INT, c2 VARCHAR(40));insert into mytable(c1,c2)values(1,"Data1");commit;" ^
--region us-east-1 --no-verify-ssl --endpoint-url https://rds-data.us-east-1.amazonaws.com ^
--profile myprofile
```