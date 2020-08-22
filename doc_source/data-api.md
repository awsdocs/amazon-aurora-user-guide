# Using the Data API for Aurora Serverless<a name="data-api"></a>

You can access your Aurora Serverless DB cluster using the built\-in Data API\. Using this API, you can access Aurora Serverless with web services–based applications, including AWS Lambda, AWS AppSync, and AWS Cloud9\. For more information on these applications, see details at [AWS Lambda](https://aws.amazon.com/lambda/), [AWS AppSync](https://aws.amazon.com/appsync/), and [AWS Cloud9](https://aws.amazon.com/cloud9/)\. 

The Data API doesn't require a persistent connection to the DB cluster\. Instead, it provides a secure HTTP endpoint and integration with AWS SDKs\. You can use the endpoint to run SQL statements without managing connections\. All calls to the Data API are synchronous\. By default, a call times out and is terminated in 45 seconds if it's not finished processing\. You can use the `continueAfterTimeout` parameter to continue running the SQL statement after the call times out\.

The API uses database credentials stored in AWS Secrets Manager, so you don't need to pass credentials in the API calls\. The API also provides a more secure way to use AWS Lambda\. It enables you to access your DB cluster without your needing to configure a Lambda function to access resources in a virtual private cloud \(VPC\)\. For more information about AWS Secrets Manager, see [What Is AWS Secrets Manager?](https://docs.aws.amazon.com/secretsmanager/latest/userguide/intro.html) in the *AWS Secrets Manager User Guide*\.

**Note**  
When you enable the Data API, you can also use the query editor for Aurora Serverless\. For more information, see [Using the Query Editor for Aurora Serverless](query-editor.md)\.

## Availability of the Data API<a name="data-api.regions"></a>

The Data API is only available for the following Aurora Serverless DB clusters:
+ Aurora with MySQL version 5\.6 compatibility
+ Aurora with MySQL version 5\.7 compatibility
+ Aurora with PostgreSQL version 10\.7 compatibility

The following table shows the AWS Regions where the Data API is currently available for Aurora Serverless\. Use the HTTPS protocol to access the Data API in these AWS Regions\.


| Region | Link | 
| --- | --- | 
| Asia Pacific \(Singapore\) | rds\-data\.ap\-southeast\-1\.amazonaws\.com | 
| Asia Pacific \(Sydney\) | rds\-data\.ap\-southeast\-2\.amazonaws\.com | 
| Asia Pacific \(Tokyo\) | rds\-data\.ap\-northeast\-1\.amazonaws\.com | 
| Asia Pacific \(Mumbai\) | rds\-data\.ap\-south\-1\.amazonaws\.com | 
| Europe \(Frankfurt\) | rds\-data\.eu\-central\-1\.amazonaws\.com | 
| Europe \(Ireland\) | rds\-data\.eu\-west\-1\.amazonaws\.com | 
| Europe \(London\) | rds\-data\.eu\-west\-2\.amazonaws\.com | 
| Europe \(Paris\) | rds\-data\.eu\-west\-3\.amazonaws\.com | 
| US East \(N\. Virginia\) | rds\-data\.us\-east\-1\.amazonaws\.com | 
| US East \(Ohio\) | rds\-data\.us\-east\-2\.amazonaws\.com | 
| US West \(N\. California\) | rds\-data\.us\-west\-1\.amazonaws\.com | 
| US West \(Oregon\) | rds\-data\.us\-west\-2\.amazonaws\.com | 

## Authorizing Access to the Data API<a name="data-api.access"></a>

A user must be authorized to access the Data API\. You can authorize a user to access the Data API by adding the `AmazonRDSDataFullAccess` policy, a predefined AWS Identity and Access Management \(IAM\) policy, to that user\.

You can also create an IAM policy that grants access to the Data API\. After you create the policy, add it to each user that requires access to the Data API\.

The following policy provides the minimum required permissions for a user to access the Data API\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "SecretsManagerDbCredentialsAccess",
            "Effect": "Allow",
            "Action": [
                "secretsmanager:GetSecretValue",
                "secretsmanager:PutResourcePolicy",
                "secretsmanager:PutSecretValue",
                "secretsmanager:DeleteSecret",
                "secretsmanager:DescribeSecret",
                "secretsmanager:TagResource"
            ],
            "Resource": "arn:aws:secretsmanager:*:*:secret:rds-db-credentials/*"
        },
        {
            "Sid": "RDSDataServiceAccess",
            "Effect": "Allow",
            "Action": [
                "secretsmanager:CreateSecret",
                "secretsmanager:ListSecrets",
                "secretsmanager:GetRandomPassword",
                "tag:GetResources",
                "rds-data:BatchExecuteStatement",
                "rds-data:BeginTransaction",
                "rds-data:CommitTransaction",
                "rds-data:ExecuteStatement",
                "rds-data:RollbackTransaction"
            ],
            "Resource": "*"
        }
    ]
}
```

For information about creating an IAM policy, see [Creating IAM Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html) in the *IAM User Guide*\.

For information about adding an IAM policy to a user, see [Adding and Removing IAM Identity Permissions](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_manage-attach-detach.html) in the *IAM User Guide*\.

## Enabling the Data API<a name="data-api.enabling"></a>

To use the Data API, enable it for your Aurora Serverless DB cluster\. You can enable the Data API when you create or modify the DB cluster\.

### Console<a name="data-api.enabling.console"></a>

You can enable the Data API by using the RDS console when you create or modify an Aurora Serverless DB cluster\. When you create an Aurora Serverless DB cluster, you do so by enabling the Data API in the RDS console's **Connectivity** section\. When you modify an Aurora Serverless DB cluster, you do so by enabling the Data API in the RDS console's **Network & Security** section\.

The following screenshot shows the enabled **Data API** when modifying an Aurora Serverless DB cluster\.

![\[Enabling the Data API for an Aurora Serverless DB cluster with console\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/web-server-api-endpoint.png)

For instructions, see [Creating an Aurora Serverless DB Cluster](aurora-serverless.create.md) and [Modifying an Aurora Serverless DB Cluster](aurora-serverless.modifying.md)\.

### AWS CLI<a name="data-api.enabling.cli"></a>

When you create or modify an Aurora Serverless DB cluster using AWS CLI commands, the Data API is enabled when you specify `-enable-http-endpoint`\.

You can specify the `-enable-http-endpoint` using the following AWS CLI commands:
+  [create\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) 
+  [modify\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) 

The following example modifies `sample-cluster` to enable the Data API\.

For Linux, macOS, or Unix:

```
aws rds modify-db-cluster \
    --db-cluster-identifier sample-cluster \
    --enable-http-endpoint
```

For Windows:

```
aws rds modify-db-cluster ^
    --db-cluster-identifier sample-cluster ^
    --enable-http-endpoint
```

### RDS API<a name="data-api.enabling.api"></a>

When you create or modify an Aurora Serverless DB cluster using Amazon RDS API operations, you enable the Data API by setting the `EnableHttpEndpoint` value to `true`\.

You can set the `EnableHttpEndpoint` value using the following API operations:
+  [CreateDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) 
+  [ModifyDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) 

## Storing Database Credentials in AWS Secrets Manager<a name="data-api.secrets"></a>

When you call the Data API, you can pass credentials for the Aurora Serverless DB cluster by using a secret in AWS Secrets Manager\. To pass credentials in this way, you specify the name of the secret or the Amazon Resource Name \(ARN\) of the secret\.

**To store DB cluster credentials in a secret**

1. Use AWS Secrets Manager to create a secret that contains credentials for the Aurora DB cluster\.

   For instructions, see [Creating a Basic Secret](https://docs.aws.amazon.com/secretsmanager/latest/userguide/manage_create-basic-secret.html) in the *AWS Secrets Manager User Guide*\.

1. Use the AWS Secrets Manager console to view the details for the secret you created, or run the `aws secretsmanager describe-secret` AWS CLI command\.

   Note the name and ARN of the secret\. You can use them in calls to the Data API\.

## Creating an Amazon VPC Endpoint \(AWS PrivateLink\) for the Data API<a name="data-api.vpc-endpoint"></a>

Amazon Virtual Private Cloud \(Amazon VPC\) enables you to launch AWS resources, such as Aurora DB clusters and applications, into a virtual private cloud \(VPC\)\. AWS PrivateLink provides private connectivity between Amazon VPCs and AWS services securely on the Amazon network\. Using AWS PrivateLink, you can create Amazon VPC endpoints, which enable you to connect to services across different accounts and VPCs based on Amazon VPC\. For more information about AWS PrivateLink, see [VPC Endpoint Services \(AWS PrivateLink\)](https://docs.aws.amazon.com/vpc/latest/userguide/endpoint-service.html) in the *Amazon Virtual Private Cloud User Guide*\.

You can call the Data API with Amazon VPC endpoints\. Using an Amazon VPC endpoint keeps traffic between applications in your Amazon VPC and the Data API in the AWS network, without using public IP addresses\. Amazon VPC endpoints can help you meet compliance and regulatory requirements related to limiting public internet connectivity\. For example, if you use an Amazon VPC endpoint, you can keep traffic between an application running on an Amazon EC2 instance and the Data API in the VPCs that contain them\.

After you create the Amazon VPC endpoint, you can start using it without making any code or configuration changes in your application\.

**To create an Amazon VPC endpoint for the Data API**

1. Sign in to the AWS Management Console and open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. Choose **Endpoints**, and then choose **Create Endpoint**\.

1. On the **Create Endpoint** page, for **Service category**, choose **AWS services**\. For **Service Name**, choose **rds\-data**\.  
![\[Create an Amazon VPC endpoint for the Data API\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/data-api-create-endpoint.png)

1. For **VPC**, choose the VPC to create the endpoint in\.

   Choose the VPC that contains the application that makes Data API calls\.

1. For **Subnets**, choose the subnet for each Availability Zone \(AZ\) used by the AWS service that is running your application\.  
![\[Choose subnets for the Amazon VPC endpoint\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/data-api-vpc-endpoint-subnets.png)

   To create an Amazon VPC endpoint, specify the private IP address range in which the endpoint will be accessible\. To do this, choose the subnet for each Availability Zone\. Doing so restricts the VPC endpoint to the private IP address range specific to each Availability Zone and also creates an Amazon VPC endpoint in each Availability Zone\.

1. For **Enable DNS name**, select **Enable for this endpoint**\.  
![\[Enable DNS name for the Amazon VPC endpoint\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/data-api-vpc-endpoint-enable-endpoint.png)

   Private DNS resolves the standard Data API DNS hostname \(`https://rds-data.region.amazonaws.com`\) to the private IP addresses associated with the DNS hostname specific to your Amazon VPC endpoint\. As a result, you can access the Data API VPC endpoint using the AWS CLI or AWS SDKs without making any code or configuration changes to update the Data API endpoint URL\.

1. For **Security group**, choose a security group to associate with the Amazon VPC endpoint\.

   Choose the security group that allows access to the AWS service that is running your application\. For example, if an Amazon EC2 instance is running your application, choose the security group that allows access to the Amazon EC2 instance\. The security group enables you to control the traffic to the Amazon VPC endpoint from resources in your VPC\.

1. For **Policy**, choose **Full Access** to allow anyone inside the Amazon VPC to access the Data API through this endpoint\. Or choose **Custom** to specify a policy that limits access\.

   If you choose **Custom**, enter the policy in the policy creation tool\.

1. Choose **Create endpoint**\.

After the endpoint is created, choose the link in the AWS Management Console to view the endpoint details\.

![\[Link to the Amazon VPC endpoint details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/data-api-vpc-endpoint-link-to-details.png)

The endpoint **Details** tab shows the DNS hostnames that were generated while creating the Amazon VPC endpoint\.

![\[Link to the Amazon VPC endpoint details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/data-api-vpc-endpoint-dns-names.png)

You can use the standard endpoint \(`rds-data.region.amazonaws.com`\) or one of the VPC\-specific endpoints to call the Data API within the Amazon VPC\. The standard Data API endpoint automatically routes to the Amazon VPC endpoint\. This routing occurs because the Private DNS hostname was enabled when the Amazon VPC endpoint was created\.

When you use an Amazon VPC endpoint in a Data API call, all traffic between your application and the Data API remains in the Amazon VPCs that contain them\. You can use an Amazon VPC endpoint for any type of Data API call\. For information about calling the Data API, see [Calling the Data API](#data-api.calling)\.

## Calling the Data API<a name="data-api.calling"></a>

After you enable the Data API for an Aurora Serverless DB cluster, you can call the Data API or the AWS CLI to run SQL statements on the DB cluster\. The Data API supports the programming languages supported by the AWS SDK\. For more information, see [ Tools to Build on AWS](https://aws.amazon.com/tools/)\.

**Note**  
Currently, the Data API doesn't support arrays of Universal Unique Identifiers \(UUIDs\)\.

The Data API provides the following operations to perform SQL statements\.


****  

|  Data API Operation  |  AWS CLI Command  |  Description  | 
| --- | --- | --- | 
|  [https://docs.aws.amazon.com/rdsdataservice/latest/APIReference/API_ExecuteStatement.html](https://docs.aws.amazon.com/rdsdataservice/latest/APIReference/API_ExecuteStatement.html)  |  [https://docs.aws.amazon.com/cli/latest/reference/rds-data/execute-statement.html](https://docs.aws.amazon.com/cli/latest/reference/rds-data/execute-statement.html)  |  Runs a SQL statement against a database\.  | 
|  [https://docs.aws.amazon.com/rdsdataservice/latest/APIReference/API_BatchExecuteStatement.html](https://docs.aws.amazon.com/rdsdataservice/latest/APIReference/API_BatchExecuteStatement.html)  |  [https://docs.aws.amazon.com/cli/latest/reference/rds-data/batch-execute-statement.html](https://docs.aws.amazon.com/cli/latest/reference/rds-data/batch-execute-statement.html)  |  Runs a batch SQL statement over an array of data for bulk update and insert operations\. You can run a DML statement with array of parameter sets\. A batch SQL statement can provide a significant performance improvement over individual insert and update statements\.  | 

You can run both operations for performing a SQL statement independently, or you can run them in a transaction\. The Data API provides the following operations to support transactions\.


****  

|  Data API Operation  |  AWS CLI Command  |  Description  | 
| --- | --- | --- | 
|  [https://docs.aws.amazon.com/rdsdataservice/latest/APIReference/API_BeginTransaction.html](https://docs.aws.amazon.com/rdsdataservice/latest/APIReference/API_BeginTransaction.html)  |  [https://docs.aws.amazon.com/cli/latest/reference/rds-data/begin-transaction.html](https://docs.aws.amazon.com/cli/latest/reference/rds-data/begin-transaction.html)  |  Starts a SQL transaction\.  | 
|  [https://docs.aws.amazon.com/rdsdataservice/latest/APIReference/API_CommitTransaction.html](https://docs.aws.amazon.com/rdsdataservice/latest/APIReference/API_CommitTransaction.html)  |  [https://docs.aws.amazon.com/cli/latest/reference/rds-data/commit-transaction.html](https://docs.aws.amazon.com/cli/latest/reference/rds-data/commit-transaction.html)  |  Ends a SQL transaction and commits the changes\.  | 
|  [https://docs.aws.amazon.com/rdsdataservice/latest/APIReference/API_RollbackTransaction.html](https://docs.aws.amazon.com/rdsdataservice/latest/APIReference/API_RollbackTransaction.html)  |  [https://docs.aws.amazon.com/cli/latest/reference/rds-data/rollback-transaction.html](https://docs.aws.amazon.com/cli/latest/reference/rds-data/rollback-transaction.html)  |  Performs a rollback of a transaction\.  | 

The operations for performing SQL statements and supporting transactions have the following common Data API parameters and AWS CLI options\. Some operations support other parameters or options\.


****  

|  Data API Operation Parameter  |  AWS CLI Command Option  |  Required  |  Description  | 
| --- | --- | --- | --- | 
|  `resourceArn`  |  `--resource-arn`  |  Yes  |  The Amazon Resource Name \(ARN\) of the Aurora Serverless DB cluster\.  | 
|  `secretArn`  |  `--secret-arn`  |  Yes  |  The name or ARN of the secret that enables access to the DB cluster\.  | 

You can use parameters in Data API calls to `ExecuteStatement` and `BatchExecuteStatement`, and when you run the AWS CLI commands `execute-statement` and `batch-execute-statement`\. To use a parameter, you specify a name\-value pair in the `SqlParameter` data type\. You specify the value with the `Field` data type\. The following table maps Java Database Connectivity \(JDBC\) data types to the data types you specify in Data API calls\.


****  

|  JDBC Data Type  |  Data API Data Type  | 
| --- | --- | 
|  `INTEGER, TINYINT, SMALLINT, BIGINT`  |  `LONG`  | 
|  `FLOAT, REAL, DOUBLE`  |  `DOUBLE`  | 
|  `DECIMAL`  |  `STRING`  | 
|  `BOOLEAN, BIT`  |  `BOOLEAN`  | 
|  `BLOB, BINARY, LONGVARBINARY, VARBINARY`  |  `BLOB`  | 
|  `CLOB`  |  `STRING`  | 
|  Other types \(including types related to date and time\)  |  `STRING`  | 

For some specific types, such as `DECIMAL` or `TIME`, a hint might be required to instruct the Data API that the `String` value should be passed to the database as a different type\. You can do this by including values in `typeHint` in the `SqlParameter` data type\. The possible values for `typeHint` are the following:
+ `DECIMAL` – The corresponding `String` parameter value is sent as an object of `DECIMAL` type to the database\.
+ `TIMESTAMP` – The corresponding `String` parameter value is sent as an object of `TIMESTAMP` type to the database\. The accepted format is `YYYY-MM-DD HH:MM:SS[.FFF]`\.
+ `TIME` – The corresponding `String` parameter value is sent as an object of `TIME` type to the database\. The accepted format is `HH:MM:SS[.FFF]`\.
+ `DATE` – The corresponding `String` parameter value is sent as an object of `DATE` type to the database\. The accepted format is `YYYY-MM-DD`\.

**Note**  
 For Amazon Aurora PostgreSQL, the Data API always returns the Aurora PostgreSQL datatype `TIMESTAMPTZ` in UTC timezone\.

**Topics**
+ [Calling the Data API with the AWS CLI](#data-api.calling.cli)
+ [Calling the Data API from a Python Application](#data-api.calling.python)
+ [Calling the Data API from a Java Application](#data-api.calling.java)

**Note**  
These examples are not exhaustive\.

### Calling the Data API with the AWS CLI<a name="data-api.calling.cli"></a>

You can call the Data API using the AWS Command Line Interface \(AWS CLI\)\.

The following examples use the AWS CLI for the Data API\. For more information, see [AWS Command Line Interface Reference for the Data API](https://docs.aws.amazon.com/cli/latest/reference/rds-data/index.html)\.

In each example, replace the DB cluster ARN with the ARN for your Aurora Serverless DB cluster\. Also, replace the secret ARN with the ARN of the secret in AWS Secrets Manager that allows access to the DB cluster\.

**Note**  
The AWS CLI can format responses in JSON\.

**Topics**
+ [Starting a SQL Transaction](#data-api.calling.cli.begin-transaction)
+ [Running a SQL Statement](#data-api.calling.cli.execute-statment)
+ [Running a Batch SQL Statement Over an Array of Data](#data-api.calling.cli.batch-execute-statement)
+ [Committing a SQL Transaction](#data-api.calling.cli.commit-transaction)
+ [Rolling Back a SQL Transaction](#data-api.calling.cli.rollback-transaction)

#### Starting a SQL Transaction<a name="data-api.calling.cli.begin-transaction"></a>

You can start a SQL transaction using the `aws rds-data begin-transaction` CLI command\. The call returns a transaction identifier\.

**Important**  
A transaction times out if there are no calls that use its transaction ID in three minutes\. If a transaction times out before it's committed, it's rolled back automatically\.  
DDL statements inside a transaction cause an implicit commit\. We recommend that you run each DDL statement in a separate `execute-statement` command with the `--continue-after-timeout` option\.

In addition to the common options, specify the following option:
+ `--database` \(optional\) – The name of the database\.

For example, the following CLI command starts a SQL transaction\.

For Linux, macOS, or Unix:

```
aws rds-data begin-transaction --resource-arn "arn:aws:rds:us-east-1:123456789012:cluster:mydbcluster" \
--database "mydb" --secret-arn "arn:aws:secretsmanager:us-east-1:123456789012:secret:mysecret"
```

For Windows:

```
aws rds-data begin-transaction --resource-arn "arn:aws:rds:us-east-1:123456789012:cluster:mydbcluster" ^
--database "mydb" --secret-arn "arn:aws:secretsmanager:us-east-1:123456789012:secret:mysecret"
```

The following is an example of the response\.

```
{
    "transactionId": "ABC1234567890xyz"
}
```

#### Running a SQL Statement<a name="data-api.calling.cli.execute-statment"></a>

You can run a SQL statement using the `aws rds-data execute-statement` CLI command\.

You can run the SQL statement in a transaction by specifying the transaction identifier with the `--transaction-id` option\. You can start a transaction using the `aws rds-data begin-transaction` CLI command\. You can end and commit a transaction using the `aws rds-data commit-transaction` CLI command\.

**Important**  
If you don't specify the `--transaction-id` option, changes that result from the call are committed automatically\.

In addition to the common options, specify the following options:
+ `--sql` \(required\) – A SQL statement to run on the DB cluster\.
+ `--transaction-id` \(optional\) – The identifier of a transaction that was started using the `begin-transaction` CLI command\. Specify the transaction ID of the transaction that you want to include the SQL statement in\.
+ `--parameters` \(optional\) – The parameters for the SQL statement\.
+ `--include-result-metadata | --no-include-result-metadata` \(optional\) – A value that indicates whether to include metadata in the results\. The default is `--no-include-result-metadata`\.
+ `--database` \(optional\) – The name of the database\.
+ `--continue-after-timeout | --no-continue-after-timeout` \(optional\) – A value that indicates whether to continue running the statement after the call times out\. The default is `--no-continue-after-timeout`\.

  For data definition language \(DDL\) statements, we recommend continuing to run the statement after the call times out to avoid errors and the possibility of corrupted data structures\.

The DB cluster returns a response for the call\.

**Note**  
The response size limit is 1 MB\. If the call returns more than 1 MB of response data, the call is terminated\.  
The maximum number of requests per second is 1,000\.

For example, the following CLI command runs a single SQL statement and omits the metadata in the results \(the default\)\.

For Linux, macOS, or Unix:

```
aws rds-data execute-statement --resource-arn "arn:aws:rds:us-east-1:123456789012:cluster:mydbcluster" \
--database "mydb" --secret-arn "arn:aws:secretsmanager:us-east-1:123456789012:secret:mysecret" \
--sql "select * from mytable"
```

For Windows:

```
aws rds-data execute-statement --resource-arn "arn:aws:rds:us-east-1:123456789012:cluster:mydbcluster" ^
--database "mydb" --secret-arn "arn:aws:secretsmanager:us-east-1:123456789012:secret:mysecret" ^
--sql "select * from mytable"
```

The following is an example of the response\.

```
{
    "numberOfRecordsUpdated": 0,
    "records": [
        [
            {
                "longValue": 1
            },
            {
                "stringValue": "ValueOne"
            }
        ],
        [
            {
                "longValue": 2
            },
            {
                "stringValue": "ValueTwo"
            }
        ],
        [
            {
                "longValue": 3
            },
            {
                "stringValue": "ValueThree"
            }
        ]
    ]
}
```

The following CLI command runs a single SQL statement in a transaction by specifying the `--transaction-id` option\.

For Linux, macOS, or Unix:

```
aws rds-data execute-statement --resource-arn "arn:aws:rds:us-east-1:123456789012:cluster:mydbcluster" \
--database "mydb" --secret-arn "arn:aws:secretsmanager:us-east-1:123456789012:secret:mysecret" \
--sql "update mytable set quantity=5 where id=201" --transaction-id "ABC1234567890xyz"
```

For Windows:

```
aws rds-data execute-statement --resource-arn "arn:aws:rds:us-east-1:123456789012:cluster:mydbcluster" ^
--database "mydb" --secret-arn "arn:aws:secretsmanager:us-east-1:123456789012:secret:mysecret" ^
--sql "update mytable set quantity=5 where id=201" --transaction-id "ABC1234567890xyz"
```

The following is an example of the response\.

```
{
    "numberOfRecordsUpdated": 1
}
```

The following CLI command runs a single SQL statement with parameters\.

For Linux, macOS, or Unix:

```
aws rds-data execute-statement --resource-arn "arn:aws:rds:us-east-1:123456789012:cluster:mydbcluster" \
--database "mydb" --secret-arn "arn:aws:secretsmanager:us-east-1:123456789012:secret:mysecret" \
--sql "insert into mytable values (:id, :val)" --parameters "[{\"name\": \"id\", \"value\": {\"longValue\": 1}},{\"name\": \"val\", \"value\": {\"stringValue\": \"value1\"}}]"
```

For Windows:

```
aws rds-data execute-statement --resource-arn "arn:aws:rds:us-east-1:123456789012:cluster:mydbcluster" ^
--database "mydb" --secret-arn "arn:aws:secretsmanager:us-east-1:123456789012:secret:mysecret" ^
--sql "insert into mytable values (:id, :val)" --parameters "[{\"name\": \"id\", \"value\": {\"longValue\": 1}},{\"name\": \"val\", \"value\": {\"stringValue\": \"value1\"}}]"
```

The following is an example of the response\.

```
{
    "numberOfRecordsUpdated": 1
}
```

The following CLI command runs a data definition language \(DDL\) SQL statement\. The DDL statement renames column `job` to column `role`\.

**Important**  
For DDL statements, we recommend continuing to run the statement after the call times out\. When a DDL statement terminates before it is finished running, it can result in errors and possibly corrupted data structures\. To continue running a statement after a call times out, specify the `--continue-after-timeout` option\.

For Linux, macOS, or Unix:

```
aws rds-data execute-statement --resource-arn "arn:aws:rds:us-east-1:123456789012:cluster:mydbcluster" \
--database "mydb" --secret-arn "arn:aws:secretsmanager:us-east-1:123456789012:secret:mysecret" \
--sql "alter table mytable change column job role varchar(100)" --continue-after-timeout
```

For Windows:

```
aws rds-data execute-statement --resource-arn "arn:aws:rds:us-east-1:123456789012:cluster:mydbcluster" ^
--database "mydb" --secret-arn "arn:aws:secretsmanager:us-east-1:123456789012:secret:mysecret" ^
--sql "alter table mytable change column job role varchar(100)" --continue-after-timeout
```

The following is an example of the response\.

```
{
    "generatedFields": [],
    "numberOfRecordsUpdated": 0
}
```

**Note**  
The `generatedFields` data isn't supported by Aurora PostgreSQL\. To get the values of generated fields, use the `RETURNING` clause\. For more information, see [ Returning Data From Modified Rows](https://www.postgresql.org/docs/10/dml-returning.html) in the PostgreSQL documentation\.

#### Running a Batch SQL Statement Over an Array of Data<a name="data-api.calling.cli.batch-execute-statement"></a>

You can run a batch SQL statement over an array of data by using the `aws rds-data batch-execute-statement` CLI command\. You can use this command to perform a bulk import or update operation\.

You can run the SQL statement in a transaction by specifying the transaction identifier with the `--transaction-id` option\. You can start a transaction by using the `aws rds-data begin-transaction` CLI command\. You can end and commit a transaction by using the `aws rds-data commit-transaction` CLI command\.

**Important**  
If you don't specify the `--transaction-id` option, changes that result from the call are committed automatically\.

In addition to the common options, specify the following options:
+ `--sql` \(required\) – A SQL statement to run on the DB cluster\.
+ `--transaction-id` \(optional\) – The identifier of a transaction that was started using the `begin-transaction` CLI command\. Specify the transaction ID of the transaction that you want to include the SQL statement in\.
+ `--parameter-set` \(optional\) – The parameter sets for the batch operation\.
+ `--database` \(optional\) – The name of the database\.

The DB cluster returns a response to the call\.

**Note**  
The response size limit is 1 MB\. If the call returns more than 1 MB of response data, the call is terminated\.  
The maximum number of requests per second is 1,000\.

For example, the following CLI command runs a batch SQL statement over an array of data with a parameter set\.

For Linux, macOS, or Unix:

```
aws rds-data batch-execute-statement --resource-arn "arn:aws:rds:us-east-1:123456789012:cluster:mydbcluster" \
--database "mydb" --secret-arn "arn:aws:secretsmanager:us-east-1:123456789012:secret:mysecret" \
--sql "insert into mytable values (:id, :val)" \
--parameter-sets "[[{\"name\": \"id\", \"value\": {\"longValue\": 1}},{\"name\": \"val\", \"value\": {\"stringValue\": \"ValueOne\"}}],
[{\"name\": \"id\", \"value\": {\"longValue\": 2}},{\"name\": \"val\", \"value\": {\"stringValue\": \"ValueTwo\"}}],
[{\"name\": \"id\", \"value\": {\"longValue\": 3}},{\"name\": \"val\", \"value\": {\"stringValue\": \"ValueThree\"}}]]"
```

For Windows:

```
aws rds-data batch-execute-statement --resource-arn "arn:aws:rds:us-east-1:123456789012:cluster:mydbcluster" ^
--database "mydb" --secret-arn "arn:aws:secretsmanager:us-east-1:123456789012:secret:mysecret" ^
--sql "insert into mytable values (:id, :val)" ^
--parameter-sets "[[{\"name\": \"id\", \"value\": {\"longValue\": 1}},{\"name\": \"val\", \"value\": {\"stringValue\": \"ValueOne\"}}],
[{\"name\": \"id\", \"value\": {\"longValue\": 2}},{\"name\": \"val\", \"value\": {\"stringValue\": \"ValueTwo\"}}],
[{\"name\": \"id\", \"value\": {\"longValue\": 3}},{\"name\": \"val\", \"value\": {\"stringValue\": \"ValueThree\"}}]]"
```

**Note**  
Don't include line breaks in the `--parameter-sets` option\.

#### Committing a SQL Transaction<a name="data-api.calling.cli.commit-transaction"></a>

Using the `aws rds-data commit-transaction` CLI command, you can end a SQL transaction that you started with `aws rds-data begin-transaction` and commit the changes\.

In addition to the common options, specify the following option:
+ `--transaction-id` \(required\) – The identifier of a transaction that was started using the `begin-transaction` CLI command\. Specify the transaction ID of the transaction that you want to end and commit\.

For example, the following CLI command ends a SQL transaction and commits the changes\.

For Linux, macOS, or Unix:

```
aws rds-data commit-transaction --resource-arn "arn:aws:rds:us-east-1:123456789012:cluster:mydbcluster" \
--secret-arn "arn:aws:secretsmanager:us-east-1:123456789012:secret:mysecret" \
--transaction-id "ABC1234567890xyz"
```

For Windows:

```
aws rds-data commit-transaction --resource-arn "arn:aws:rds:us-east-1:123456789012:cluster:mydbcluster" ^
--secret-arn "arn:aws:secretsmanager:us-east-1:123456789012:secret:mysecret" ^
--transaction-id "ABC1234567890xyz"
```

The following is an example of the response\.

```
{
    "transactionStatus": "Transaction Committed"
}
```

#### Rolling Back a SQL Transaction<a name="data-api.calling.cli.rollback-transaction"></a>

Using the `aws rds-data rollback-transaction` CLI command, you can roll back a SQL transaction that you started with `aws rds-data begin-transaction`\. Rolling back a transaction cancels its changes\.

**Important**  
If the transaction ID has expired, the transaction was rolled back automatically\. In this case, an `aws rds-data rollback-transaction` command that specifies the expired transaction ID returns an error\.

In addition to the common options, specify the following option:
+ `--transaction-id` \(required\) – The identifier of a transaction that was started using the `begin-transaction` CLI command\. Specify the transaction ID of the transaction that you want to roll back\.

For example, the following AWS CLI command rolls back a SQL transaction\.

For Linux, macOS, or Unix:

```
aws rds-data rollback-transaction --resource-arn "arn:aws:rds:us-east-1:123456789012:cluster:mydbcluster" \
--secret-arn "arn:aws:secretsmanager:us-east-1:123456789012:secret:mysecret" \
--transaction-id "ABC1234567890xyz"
```

For Windows:

```
aws rds-data rollback-transaction --resource-arn "arn:aws:rds:us-east-1:123456789012:cluster:mydbcluster" ^
--secret-arn "arn:aws:secretsmanager:us-east-1:123456789012:secret:mysecret" ^
--transaction-id "ABC1234567890xyz"
```

The following is an example of the response\.

```
{
    "transactionStatus": "Rollback Complete"
}
```

### Calling the Data API from a Python Application<a name="data-api.calling.python"></a>

You can call the Data API from a Python application\.

The following examples use the AWS SDK for Python \(Boto\)\. For more information about Boto, see the [AWS SDK for Python \(Boto 3\) Documentation](http://boto3.amazonaws.com/v1/documentation/api/latest/index.html)\.

In each example, replace the DB cluster's Amazon Resource Name \(ARN\) with the ARN for your Aurora Serverless DB cluster\. Also, replace the secret ARN with the ARN of the secret in AWS Secrets Manager that allows access to the DB cluster\.

**Topics**
+ [Running a SQL Query](#data-api.calling.python.run-query)
+ [Running a DML SQL Statement](#data-api.calling.python.run-inert)
+ [Running a SQL Transaction](#data-api.calling.python.run-transaction)

#### Running a SQL Query<a name="data-api.calling.python.run-query"></a>

You can run a `SELECT` statement and fetch the results with a Python application\.

The following example runs a SQL query\.

```
import boto3 

rdsData = boto3.client('rds-data')

cluster_arn = 'arn:aws:rds:us-east-1:123456789012:cluster:mydbcluster' 
secret_arn = 'arn:aws:secretsmanager:us-east-1:123456789012:secret:mysecret' 
 
response1 = rdsData.execute_statement(
            resourceArn = cluster_arn, 
            secretArn = secret_arn, 
            database = 'mydb', 
            sql = 'select * from employees limit 3')

print (response1['records'])
[
    [
        {
            'longValue': 1
        }, 
        {
            'stringValue': 'ROSALEZ'
        },
        {
            'stringValue': 'ALEJANDRO'
        },
        {
            'stringValue': '2016-02-15 04:34:33.0'
        }
    ], 
    [
        {
            'longValue': 1
        }, 
        {
            'stringValue': 'DOE'
        },
        {
            'stringValue': 'JANE'
        },
        {
            'stringValue': '2014-05-09 04:34:33.0'
        }
    ], 
    [
        {
            'longValue': 1
        }, 
        {
            'stringValue': 'STILES'
        },
        {
            'stringValue': 'JOHN'
        },
        {
            'stringValue': '2017-09-20 04:34:33.0'
        }
    ]
]
```

#### Running a DML SQL Statement<a name="data-api.calling.python.run-inert"></a>

You can run a data manipulation language \(DML\) statement to insert, update, or delete data in your database\. You can also use parameters in DML statements\.

**Important**  
If a call isn't part of a transaction because it doesn't include the `transactionID` parameter, changes that result from the call are committed automatically\.

The following example runs an insert SQL statement and uses parameters\.

```
import boto3

cluster_arn = 'arn:aws:rds:us-east-1:123456789012:cluster:mydbcluster' 
secret_arn = 'arn:aws:secretsmanager:us-east-1:123456789012:secret:mysecret' 

rdsData = boto3.client('rds-data')


param1 = {'name':'firstname', 'value':{'stringValue': 'JACKSON'}}
param2 = {'name':'lastname', 'value':{'stringValue': 'MATEO'}}
paramSet = [param1, param2]

response2 = rdsData.execute_statement(resourceArn=cluster_arn, 
                                      secretArn=secret_arn,
                                      database='mydb',
                                      sql='insert into employees(first_name, last_name) VALUES(:firstname, :lastname)',
                                      parameters = paramSet)

print (response2["numberOfRecordsUpdated"])
```

#### Running a SQL Transaction<a name="data-api.calling.python.run-transaction"></a>

You can start a SQL transaction, run one or more SQL statements, and then commit the changes with a Python application\.

**Important**  
A transaction times out if there are no calls that use its transaction ID in three minutes\. If a transaction times out before it's committed, it's rolled back automatically\.  
If you don't specify a transaction ID, changes that result from the call are committed automatically\.

The following example runs a SQL transaction that inserts a row in a table\.

```
import boto3 

rdsData = boto3.client('rds-data')

cluster_arn = 'arn:aws:rds:us-east-1:123456789012:cluster:mydbcluster' 
secret_arn = 'arn:aws:secretsmanager:us-east-1:123456789012:secret:mysecret' 

tr = rdsData.begin_transaction(
     resourceArn = cluster_arn, 
     secretArn = secret_arn, 
     database = 'mydb') 
 
response3 = rdsData.execute_statement(
     resourceArn = cluster_arn, 
     secretArn = secret_arn, 
     database = 'mydb', 
     sql = 'insert into employees(first_name, last_name) values('XIULAN', 'WANG')', 
     transactionId = tr['transactionId']) 
 
cr = rdsData.commit_transaction(
     resourceArn = cluster_arn, 
     secretArn = secret_arn, 
     transactionId = tr['transactionId']) 
 
cr['transactionStatus'] 
'Transaction Committed' 
 
response3['numberOfRecordsUpdated'] 
1
```

**Note**  
If you run a data definition language \(DDL\) statement, we recommend continuing to run the statement after the call times out\. When a DDL statement terminates before it is finished running, it can result in errors and possibly corrupted data structures\. To continue running a statement after a call times out, set the `continueAfterTimeout` parameter to `true`\.

### Calling the Data API from a Java Application<a name="data-api.calling.java"></a>

You can call the Data API from a Java application\.

The following examples use the AWS SDK for Java\. For more information, see the [AWS SDK for Java Developer Guide](https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/welcome.html)\.

In each example, replace the DB cluster's Amazon Resource Name \(ARN\) with the ARN for your Aurora Serverless DB cluster\. Also, replace the secret ARN with the ARN of the secret in AWS Secrets Manager that allows access to the DB cluster\.

**Topics**
+ [Running a SQL Query](#data-api.calling.java.run-query)
+ [Running a SQL Transaction](#data-api.calling.java.run-transaction)
+ [Running a Batch SQL Operation](#data-api.calling.java.run-batch)

#### Running a SQL Query<a name="data-api.calling.java.run-query"></a>

You can run a `SELECT` statement and fetch the results with a Java application\.

The following example runs a SQL query\.

```
package com.amazonaws.rdsdata.examples;

import com.amazonaws.services.rdsdata.AWSRDSData;
import com.amazonaws.services.rdsdata.AWSRDSDataClient;
import com.amazonaws.services.rdsdata.model.ExecuteStatementRequest;
import com.amazonaws.services.rdsdata.model.ExecuteStatementResult;
import com.amazonaws.services.rdsdata.model.Field;

import java.util.List;

public class FetchResultsExample {
  public static final String RESOURCE_ARN = "arn:aws:rds:us-east-1:123456789012:cluster:mydbcluster";
  public static final String SECRET_ARN = "arn:aws:secretsmanager:us-east-1:123456789012:secret:mysecret";

  public static void main(String[] args) {
    AWSRDSData rdsData = AWSRDSDataClient.builder().build();

    ExecuteStatementRequest request = new ExecuteStatementRequest()
            .withResourceArn(RESOURCE_ARN)
            .withSecretArn(SECRET_ARN)
            .withDatabase("mydb")
            .withSql("select * from mytable");

    ExecuteStatementResult result = rdsData.executeStatement(request);

    for (List<Field> fields: result.getRecords()) {
      String stringValue = fields.get(0).getStringValue();
      long numberValue = fields.get(1).getLongValue();

      System.out.println(String.format("Fetched row: string = %s, number = %d", stringValue, numberValue));
    }
  }
}
```

#### Running a SQL Transaction<a name="data-api.calling.java.run-transaction"></a>

You can start a SQL transaction, run one or more SQL statements, and then commit the changes with a Java application\.

**Important**  
A transaction times out if there are no calls that use its transaction ID in three minutes\. If a transaction times out before it's committed, it's rolled back automatically\.  
If you don't specify a transaction ID, changes that result from the call are committed automatically\.

The following example runs a SQL transaction\.

```
package com.amazonaws.rdsdata.examples;

import com.amazonaws.services.rdsdata.AWSRDSData;
import com.amazonaws.services.rdsdata.AWSRDSDataClient;
import com.amazonaws.services.rdsdata.model.BeginTransactionRequest;
import com.amazonaws.services.rdsdata.model.BeginTransactionResult;
import com.amazonaws.services.rdsdata.model.CommitTransactionRequest;
import com.amazonaws.services.rdsdata.model.ExecuteStatementRequest;

public class TransactionExample {
  public static final String RESOURCE_ARN = "arn:aws:rds:us-east-1:123456789012:cluster:mydbcluster";
  public static final String SECRET_ARN = "arn:aws:secretsmanager:us-east-1:123456789012:secret:mysecret";

  public static void main(String[] args) {
    AWSRDSData rdsData = AWSRDSDataClient.builder().build();

    BeginTransactionRequest beginTransactionRequest = new BeginTransactionRequest()
            .withResourceArn(RESOURCE_ARN)
            .withSecretArn(SECRET_ARN)
            .withDatabase("mydb");
    BeginTransactionResult beginTransactionResult = rdsData.beginTransaction(beginTransactionRequest);
    String transactionId = beginTransactionResult.getTransactionId();

    ExecuteStatementRequest executeStatementRequest = new ExecuteStatementRequest()
            .withTransactionId(transactionId)
            .withResourceArn(RESOURCE_ARN)
            .withSecretArn(SECRET_ARN)
            .withSql("INSERT INTO test_table VALUES ('hello world!')");
    rdsData.executeStatement(executeStatementRequest);

    CommitTransactionRequest commitTransactionRequest = new CommitTransactionRequest()
            .withTransactionId(transactionId)
            .withResourceArn(RESOURCE_ARN)
            .withSecretArn(SECRET_ARN);
    rdsData.commitTransaction(commitTransactionRequest);
  }
}
```

**Note**  
If you run a data definition language \(DDL\) statement, we recommend continuing to run the statement after the call times out\. When a DDL statement terminates before it is finished running, it can result in errors and possibly corrupted data structures\. To continue running a statement after a call times out, set the `continueAfterTimeout` parameter to `true`\.

#### Running a Batch SQL Operation<a name="data-api.calling.java.run-batch"></a>

You can run bulk insert and update operations over an array of data with a Java application\. You can run a DML statement with array of parameter sets\.

**Important**  
If you don't specify a transaction ID, changes that result from the call are committed automatically\.

The following example runs a batch insert operation\.

```
package com.amazonaws.rdsdata.examples;

import com.amazonaws.services.rdsdata.AWSRDSData;
import com.amazonaws.services.rdsdata.AWSRDSDataClient;
import com.amazonaws.services.rdsdata.model.BatchExecuteStatementRequest;
import com.amazonaws.services.rdsdata.model.Field;
import com.amazonaws.services.rdsdata.model.SqlParameter;

import java.util.Arrays;

public class BatchExecuteExample {
  public static final String RESOURCE_ARN = "arn:aws:rds:us-east-1:123456789012:cluster:mydbcluster";
  public static final String SECRET_ARN = "arn:aws:secretsmanager:us-east-1:123456789012:secret:mysecret";

  public static void main(String[] args) {
      AWSRDSData rdsData = AWSRDSDataClient.builder().build();

    BatchExecuteStatementRequest request = new BatchExecuteStatementRequest()
            .withDatabase("test")
            .withResourceArn(RESOURCE_ARN)
            .withSecretArn(SECRET_ARN)
            .withSql("INSERT INTO test_table2 VALUES (:string, :number)")
            .withParameterSets(Arrays.asList(
                    Arrays.asList(
                            new SqlParameter().withName("string").withValue(new Field().withStringValue("Hello")),
                            new SqlParameter().withName("number").withValue(new Field().withLongValue(1L))
                    ),
                    Arrays.asList(
                            new SqlParameter().withName("string").withValue(new Field().withStringValue("World")),
                            new SqlParameter().withName("number").withValue(new Field().withLongValue(2L))
                    )
            ));

    rdsData.batchExecuteStatement(request);
  }
}
```

## Using the Java Client Library for Data API<a name="data-api.java-client-library"></a>

You can download and use a Java client library for the Data API\. The Java client library provides an alternative way to use the Data API\. Using this library, you can map your client\-side classes to requests and responses of the Data API\. This mapping support can ease integration with some specific Java types, such as `Date`, `Time`, and `BigDecimal`\.

### Downloading the Java Client Library for Data API<a name="data-api.java-client-library.downloading"></a>

The Data API Java client library is open source in GitHub at the following location:

[ https://github\.com/awslabs/rds\-data\-api\-client\-library\-java](https://github.com/awslabs/rds-data-api-client-library-java)

You can build the library manually from the source files, but the best practice is to consume the library using Apache Maven dependency management\. Add the following dependency to your Maven POM file:

```
<dependency>
    <groupId>software.amazon.rdsdata</groupId>
    <artifactId>rds-data-api-client-library-java</artifactId>
    <version>1.0.4</version>
</dependency>
```

### Java Client Library Examples<a name="data-api.java-client-library.examples"></a>

Following, you can find some common examples of using the Data API Java client library\. These examples assume that you have a table `accounts` with two columns: `accountId` and `name`\. You also have the following data transfer object \(DTO\)\.

```
public class Account {
    int accountId;
    String name;
    // getters and setters omitted
}
```

The client library enables you to pass DTOs as input parameters\. The following example shows how customer DTOs are mapped to input parameters sets\.

```
var account1 = new Account(1, "John");
var account2 = new Account(2, "Mary");
client.forSql("INSERT INTO accounts(accountId, name) VALUES(:accountId, :name)")
         .withParams(account1, account2)
         .execute();
```

In some cases, it's easier to work with simple values as input parameters\. You can do so with the following syntax\.

```
client.forSql("INSERT INTO accounts(accountId, name) VALUES(:accountId, :name)")
         .withParam("accountId", 3)
         .withParam("name", "Zhang")
         .execute();
```

The following is another example that works with simple values as input parameters\.

```
client.forSql("INSERT INTO accounts(accountId, name) VALUES(?, ?)", 4, "Carlos")
         .execute();
```

The client library provides automatic mapping to DTOs when a result is returned\. The following examples show how the result is mapped to your DTOs\.

```
List<Account> result = client.forSql("SELECT * FROM accounts")
          .execute()
          .mapToList(Account.class);
          
Account result = client.forSql("SELECT * FROM accounts WHERE account_id = 1")
          .execute()
          .mapToSingle(Account.class);
```

## Troubleshooting Data API Issues<a name="data-api.troubleshooting"></a>

Use the following sections, titled with common error messages, to help troubleshoot problems that you have with the Data API\. 

**Topics**
+ [Transaction <transaction\_ID> Is Not Found](#data-api.troubleshooting.tran-id-not-found)
+ [Packet for Query Is Too Large](#data-api.troubleshooting.packet-too-large)
+ [Database Response Exceeded Size Limit](#data-api.troubleshooting.response-size-too-large)
+ [HttpEndpoint Is Not Enabled for Cluster <cluster\_ID>](#data-api.troubleshooting.http-endpoint-not-enabled)

### Transaction <transaction\_ID> Is Not Found<a name="data-api.troubleshooting.tran-id-not-found"></a>

In this case, the transaction ID specified in a Data API call wasn't found\. The cause for this issue is almost always one of the following:
+ The specified transaction ID wasn't created by a [https://docs.aws.amazon.com/rdsdataservice/latest/APIReference/API_BeginTransaction.html](https://docs.aws.amazon.com/rdsdataservice/latest/APIReference/API_BeginTransaction.html) call\.
+ The specified transaction ID has expired\.

  A transaction expires if no call uses the transaction ID within three minutes\.

To solve the issue, make sure that your call has a valid transaction ID\. Also make sure that each transaction call runs within three minutes of the last one\.

For information about running transactions, see [Calling the Data API](#data-api.calling)\.

### Packet for Query Is Too Large<a name="data-api.troubleshooting.packet-too-large"></a>

In this case, the result set returned for a row was too large\. The Data API size limit is 64 KB per row in the result set returned by the database\.

To solve this issue, make sure that each row in a result set is 64 KB or less\.

### Database Response Exceeded Size Limit<a name="data-api.troubleshooting.response-size-too-large"></a>

In this case, the size of the result set returned by the database was too large\. The Data API limit is 1 MB in the result set returned by the database\.

To solve this issue, make sure that calls to the Data API return 1 MB of data or less\. If you need to return more than 1 MB, you can use multiple [https://docs.aws.amazon.com/rdsdataservice/latest/APIReference/API_ExecuteStatement.html](https://docs.aws.amazon.com/rdsdataservice/latest/APIReference/API_ExecuteStatement.html) calls with the `LIMIT` clause in your query\.

For more information about the `LIMIT` clause, see [SELECT Syntax](https://dev.mysql.com/doc/refman/5.7/en/select.html) in the MySQL documentation\.

### HttpEndpoint Is Not Enabled for Cluster <cluster\_ID><a name="data-api.troubleshooting.http-endpoint-not-enabled"></a>

The cause for this issue is almost always one of the following:
+ The Data API isn't enabled for the Aurora Serverless DB cluster\. To use the Data API with an Aurora Serverless DB cluster, the Data API must be enabled for the DB cluster\.
+ The DB cluster was renamed after the Data API was enabled for it\.

If the Data API has not been enabled for the DB cluster, enable it\.

If the DB cluster was renamed after the Data API was enabled for the DB cluster, disable the Data API and then enable it again\.

For information about enabling the Data API, see [Enabling the Data API](#data-api.enabling)\.