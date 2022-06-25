# Using Aurora PostgreSQL extensions with Babelfish<a name="babelfish-postgres-aws-extensions"></a>

Aurora PostgreSQL provides extensions for working with other AWS services\. These are optional extensions that support various use cases, such as using Amazon S3 with your DB cluster for importing or exporting data\. 
+ To import data from an Amazon S3 bucket to your Babelfish DB cluster, you set up the `aws_s3` Aurora PostgreSQL extension\. This extension also lets you export data from your Aurora PostgreSQL DB cluster to an Amazon S3 bucket\. 
+ AWS Lambda is a compute service that lets you run code without provisioning or managing servers\. You can use Lambda functions to do things like process event notifications from your DB instance\. To learn more about Lambda, see [What is AWS Lambda?](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html) in the *AWS Lambda Developer Guide\.* To invoke Lambda functions from your Babelfish DB cluster, you set up the `aws_lambda` Aurora PostgreSQL extension\. 

To set up these extensions for your Babelfish cluster, you first need to grant permission to the internal Babelfish user to load the extensions\. After granting permission, you can then load Aurora PostgreSQL extensions\. 

## Enabling Aurora PostgreSQL extensions in your Babelfish DB cluster<a name="babelfish-postgres-aws-extensions-enabling"></a>

Before you can load the `aws_s3` or the `aws_lambda` extensions, you grant the needed privileges to your Babelfish DB cluster\. 

The procedure following uses the `psql` PostgreSQL command line tool to connect to the DB cluster\. For more information, see [Using psql to connect to the DB cluster](babelfish-connect-PostgreSQL.md#babelfish-connect-psql)\. You can also use pgAdmin\. For details, see [Using pgAdmin to connect to the DB cluster](babelfish-connect-PostgreSQL.md#babelfish-connect-pgadmin)\. 

This procedure loads both `aws_s3` and `aws_lambda`, one after the other\. You don't need to load both if you want to use only one of these extensions\. The `aws_commons` extension is required by each, and it's loaded by default as shown in the output\. 

**To set up your Babelfish DB cluster with privileges for the Aurora PostgreSQL extensions**

1. Connect to your Babelfish DB cluster\. Use the name for the "master" user \(\-U\) that you specified when you created the Babelfish DB cluster\. The default \(`postgres`\) is shown in the examples\. 

   For Linux, macOS, or Unix:

   ```
   psql -h your-Babelfish.cluster.444455556666-us-east-1.rds.amazonaws.com \
   -U postgres \
   -d babelfish_db \
   -p 5432
   ```

   For Windows:

   ```
   psql -h your-Babelfish.cluster.444455556666-us-east-1.rds.amazonaws.com ^
   -U postgres ^
   -d babelfish_db ^
   -p 5432
   ```

   The command responds with a prompt to enter the password for the user name \(\-U\)\.

   ```
   Password:
   ```

   Enter the password for the user name \(\-U\) for the DB cluster\. When you successfully connect, you see output similar to the following\.

   ```
   psql (13.4)
   SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
   Type "help" for help.
   
   postgres=>
   ```

1. Grant privileges to the internal Babelfish user to create and load extensions\.

   ```
   babelfish_db=> GRANT rds_superuser TO master_dbo;
   GRANT ROLE
   ```

1. Create and load the `aws_s3` extension\. The `aws_commons` extension is required, and it's installed automatically when the `aws_s3` is installed\.

   ```
   babelfish_db=> create extension aws_s3 cascade;
   NOTICE:  installing required extension "aws_commons"
   CREATE EXTENSION
   ```

1. Create and load the `aws_lambda` extension\.

   ```
   babelfish_db=> create extension aws_lambda cascade;
   CREATE EXTENSION
   babelfish_db=>
   ```

## Using Babelfish with Amazon S3<a name="babelfish-postgres-aws-extensions-using-aws_s3"></a>

If you don't already have an Amazon S3 bucket to use with your Babelfish DB cluster, you can create one\. For any Amazon S3 bucket that you want to use, you provide access\.

Before trying to import or export data using an Amazon S3 bucket, complete the following one\-time steps\. 

**To set up access for your Babelfish DB instance to your Amazon S3 bucket**

1. Create an Amazon S3 bucket for your Babelfish instance, if needed\. To do so, follow the instructions in [Create a bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html) in the *Amazon Simple Storage Service User Guide*\. 

1. Upload files to your Amazon S3 bucket\. To do so, follow the steps in [Add an object to a bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg/PuttingAnObjectInABucket.html) in the *Amazon Simple Storage Service User Guide\.* 

1. Set up permissions as needed:
   + To import data from Amazon S3, the Babelfish DB cluster needs permission to access the bucket\. We recommend using an AWS Identity and Access Management \(IAM\) role and attaching an IAM policy to that role for your cluster\. To do so, follow the steps in [Using an IAM role to access an Amazon S3 bucket](USER_PostgreSQL.S3Import.md#USER_PostgreSQL.S3Import.ARNRole)\. 
   + To export data from your Babelfish DB cluster, your cluster must be granted access to the Amazon S3 bucket\. As with importing, we recommend using an IAM role and policy\. To do so, follow the steps in [Setting up access to an Amazon S3 bucket](postgresql-s3-export.md#postgresql-s3-export-access-bucket)\.

You can now use Amazon S3 with the `aws_s3` extension with your Babelfish DB cluster\. 

**To import data from Amazon S3 to Babelfish and to export Babelfish data to Amazon S3**

1. Use the `aws_s3` extension with your Babelfish DB cluster\. 

   When you do, make sure to reference the tables as they exist in the context of PostgreSQL\. That is, if you want to import into a Babelfish table named `[database].[schema].[tableA]`, refer to that table as `database_schema_tableA` in the `aws_s3` function:
   + For an example of using an `aws_s3` function to import data, see [Using the aws\_s3\.table\_import\_from\_s3 function to import Amazon S3 data](USER_PostgreSQL.S3Import.md#USER_PostgreSQL.S3Import.FileFormats)\. 
   + For examples of using `aws_s3` functions to export data, see [Exporting query data using the aws\_s3\.query\_export\_to\_s3 function](postgresql-s3-export.md#postgresql-s3-export-examples)\. 

1. Make sure to reference Babelfish tables using PostgreSQL naming when using the `aws_s3` extension and Amazon S3, as shown in the following table\.


| Babelfish table | Aurora PostgreSQL table | 
| --- | --- | 
| `database.schema.table` | `database_schema_table` | 

To learn more about using Amazon S3 with Aurora PostgreSQL, see [Importing Amazon S3 data into an Aurora PostgreSQL DB cluster](USER_PostgreSQL.S3Import.md) and [Exporting data from an Aurora PostgreSQL DB cluster to Amazon S3](postgresql-s3-export.md)\. 

## Using Babelfish with AWS Lambda<a name="babelfish-postgres-aws-extensions-using-aws_lambda"></a>

After the `aws_lambda` extension is loaded in your Babelfish DB cluster but before you can invoke Lambda functions, you give Lambda access to your DB cluster by following this procedure\. 

**To set up access for your Babelfish DB cluster to work with Lambda**

This procedure uses the AWS CLI to create the IAM policy and role, and associate these with the Babelfish DB cluster\. 

1. Create an IAM policy that allows access to Lambda from your Babelfish DB cluster\.

   ```
   aws iam create-policy  --policy-name rds-lambda-policy --policy-document '{
       "Version": "2012-10-17",
       "Statement": [
           {
           "Sid": "AllowAccessToExampleFunction",
           "Effect": "Allow",
           "Action": "lambda:InvokeFunction",
           "Resource": "arn:aws:lambda:aws-region:444455556666:function:my-function"
           }
       ]
   }'
   ```

1. Create an IAM role that the policy can assume at runtime\.

   ```
   aws iam create-role  --role-name rds-lambda-role --assume-role-policy-document '{
       "Version": "2012-10-17",
       "Statement": [
           {
           "Effect": "Allow",
           "Principal": {
               "Service": "rds.amazonaws.com"
           },
           "Action": "sts:AssumeRole"
           }
       ]
   }'
   ```

1. Attach the policy to the role\.

   ```
   aws iam attach-role-policy \
       --policy-arn arn:aws:iam::444455556666:policy/rds-lambda-policy \
       --role-name rds-lambda-role --region aws-region
   ```

1. Attach the role to your Babelfish DB cluster

   ```
   aws rds add-role-to-db-cluster \
          --db-cluster-identifier my-cluster-name \
          --feature-name Lambda \
          --role-arn  arn:aws:iam::444455556666:role/rds-lambda-role   \
          --region aws-region
   ```

After you complete these tasks, you can invoke your Lambda functions\. For more information and examples of setting up AWS Lambda for Aurora PostgreSQL DB cluster with AWS Lambda, see [Step 2: Configure IAM for your Aurora PostgreSQL DB cluster and AWS Lambda](PostgreSQL-Lambda.md#PostgreSQL-Lambda-access)\. 

**To invoke a Lambda function from your Babelfish DB cluster**

AWS Lambda supports functions written in Java, Node\.js, Python, Ruby, and other languages\. If the function returns text when invoked, you can invoke it from your Babelfish DB cluster\. The following example is a placeholder python function that returns a greeting\.

```
lambda_function.py
import json
def lambda_handler(event, context):
    #TODO implement
    return {
        'statusCode': 200,
        'body': json.dumps('Hello from Lambda!')
```

Currently, Babelfish doesn't support JSON\. If your function returns JSON, you use a wrapper to handle the JSON\. For example, say that the `lambda_function.py` shown preceding is stored in Lambda as `my-function`\.

1. Connect to your Babelfish DB cluster using the `psql` client \(or the pgAdmin client\)\. For more information, see [Using psql to connect to the DB cluster](babelfish-connect-PostgreSQL.md#babelfish-connect-psql)\. 

1. Create the wrapper\. This example uses PostgreSQL's procedural language for SQL, `PL/pgSQL`\. To learn more, see [PL/pgSQL–SQL Procedural Language](https://www.postgresql.org/docs/13/plpgsql.html)\.

   ```
   create or replace function master_dbo.lambda_wrapper()
   returns text
   language plpgsql
   as
   $$
   declare
      r_status_code integer;
      r_payload text;
   begin
      SELECT payload INTO r_payload
        FROM aws_lambda.invoke(  aws_commons.create_lambda_function_arn('my-function', 'us-east-1')
                               ,'{"body": "Hello from Postgres!"}'::json );
      return r_payload ;
   end;
   $$;
   ```

   The function can now be run from the Babelfish TDS port \(1433\) or from the PostgreSQL port \(5433\)\. 

   1. To invoke \(call\) this function from your PostgreSQL port:

      ```
      SELECT * from aws_lambda.invoke(aws_commons.create_lambda_function_arn('my-function', 'us-east-1'), '{"body": "Hello from Postgres!"}'::json );
      ```

      The output is similar to the following:

      ```
      status_code |                        payload                        | executed_version | log_result
      -------------+-------------------------------------------------------+------------------+------------
               200 | {"statusCode": 200, "body": "\"Hello from Lambda!\""} | $LATEST          |
      (1 row)
      ```

   1. To invoke \(call\) this function from the TDS port, connect to the port using the SQL Server `sqlcmd` command line client\. For details, see [Using a SQL Server client to connect to your DB cluster](babelfish-connect-sqlserver.md)\. When connected, run the following:

      ```
      1> select lambda_wrapper();
      2> go
      ```

      The command returns output similar to the following:

      ```
      {"statusCode": 200, "body": "\"Hello from Lambda!\""}
      ```

To learn more about using Lambda with Aurora PostgreSQL, see [Invoking an AWS Lambda function from an Aurora PostgreSQL DB cluster](PostgreSQL-Lambda.md)\. For more information about working with Lambda functions, see [Getting started with Lambda](https://docs.aws.amazon.com/lambda/latest/dg/getting-started.html) in the *AWS Lambda Developer Guide\.*