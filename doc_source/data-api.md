# Using the Data API for Aurora Serverless v1<a name="data-api"></a><a name="data_api"></a>

By using the Data API for Aurora Serverless v1, you can work with a web\-services interface to your Aurora Serverless v1 DB cluster\. The Data API doesn't require a persistent connection to the DB cluster\. Instead, it provides a secure HTTP endpoint and integration with AWS SDKs\. You can use the endpoint to run SQL statements without managing connections\.

All calls to the Data API are synchronous\. By default, a call times out if it's not finished processing within 45 seconds\. However, you can continue running a SQL statement if the call times out by using the `continueAfterTimeout` parameter\. For an example, see [Running a SQL transaction](#data-api.calling.java.run-transaction)\. 

Users don't need to pass credentials with calls to the Data API, because the Data API uses database credentials stored in AWS Secrets Manager\. To store credentials in Secrets Manager, users must be granted the appropriate permissions to use Secrets Manager, and also the Data API\. For more information about authorizing users, see [Authorizing access to the Data API](#data-api.access)\. 

You can also use Data API to integrate Aurora Serverless v1 with other AWS applications such as AWS Lambda, AWS AppSync, and AWS Cloud9\.  The API provides a more secure way to use AWS Lambda\. It enables you to access your DB cluster without your needing to configure a Lambda function to access resources in a virtual private cloud \(VPC\)\. For more information, see [AWS Lambda](https://aws.amazon.com/lambda/), [AWS AppSync](https://aws.amazon.com/appsync/), and [AWS Cloud9](https://aws.amazon.com/cloud9/)\. 

You can enable the Data API when you create the Aurora Serverless v1 cluster\. You can also modify the configuration later\. For more information, see [Enabling the Data API](#data-api.enabling)\. 

After you enable the Data API, you can also use the query editor for Aurora Serverless v1\. For more information, see [Using the query editor for Aurora Serverless v1](query-editor.md)\.

**Note**  
The Data API and query editor aren't supported for Aurora Serverless v2\.

**Topics**
+ [Data API availability](#data-api.regions)
+ [Authorizing access to the Data API](#data-api.access)
+ [Enabling the Data API](#data-api.enabling)
+ [Creating an Amazon VPC endpoint for the Data API \(AWS PrivateLink\)](#data-api.vpc-endpoint)
+ [Calling the Data API](#data-api.calling)
+ [Using the Java client library for Data API](#data-api.java-client-library)
+ [Processing query results in JSON format](#data-api-json)
+ [Troubleshooting Data API issues](#data-api.troubleshooting)
+ [Logging Data API calls with AWS CloudTrail](logging-using-cloudtrail-data-api.md)

## Data API availability<a name="data-api.regions"></a>

The Data API can be enabled for Aurora Serverless v1 DB clusters using specific Aurora MySQL and Aurora PostgreSQL versions only\. For more information, see [Data API for Aurora Serverless v1](Concepts.AuroraFeaturesRegionsDBEngines.grids.md#Concepts.Aurora_Fea_Regions_DB-eng.Feature.Data_API)\. 

The following table shows the AWS Regions where the Data API is currently available for Aurora Serverless v1\. To access the Data API in these Regions, use the HTTPS protocol\.


| Region | Link | 
| --- | --- | 
| US East \(Ohio\) | rds\-data\.us\-east\-2\.amazonaws\.com | 
| US East \(N\. Virginia\) | rds\-data\.us\-east\-1\.amazonaws\.com | 
| US West \(N\. California\) | rds\-data\.us\-west\-1\.amazonaws\.com | 
| US West \(Oregon\) | rds\-data\.us\-west\-2\.amazonaws\.com | 
| Asia Pacific \(Mumbai\) | rds\-data\.ap\-south\-1\.amazonaws\.com | 
| Asia Pacific \(Seoul\) | rds\-data\.ap\-northeast\-2\.amazonaws\.com | 
| Asia Pacific \(Singapore\) | rds\-data\.ap\-southeast\-1\.amazonaws\.com | 
| Asia Pacific \(Sydney\) | rds\-data\.ap\-southeast\-2\.amazonaws\.com | 
| Asia Pacific \(Tokyo\) | rds\-data\.ap\-northeast\-1\.amazonaws\.com | 
| Canada \(Central\) | rds\-data\.ca\-central\-1\.amazonaws\.com | 
| Europe \(Frankfurt\) | rds\-data\.eu\-central\-1\.amazonaws\.com | 
| Europe \(Ireland\) | rds\-data\.eu\-west\-1\.amazonaws\.com | 
| Europe \(London\) | rds\-data\.eu\-west\-2\.amazonaws\.com | 
| Europe \(Paris\) | rds\-data\.eu\-west\-3\.amazonaws\.com | 

If you require cryptographic modules validated by FIPS 140\-2 when accessing the Data API through a command line interface or an API, use a FIPS endpoint\. For more information about the available FIPS endpoints, see [Federal Information Processing Standard \(FIPS\) 140\-2](http://aws.amazon.com/compliance/fips/)\.

## Authorizing access to the Data API<a name="data-api.access"></a>

Users can invoke Data API operations only if they are authorized to do so\. You can give a user permission to use the Data API by attaching an AWS Identity and Access Management \(IAM\) policy that defines their privileges\. You can also attach the policy to a role if you're using IAM roles\. An AWS managed policy, `AmazonRDSDataFullAccess`, includes permissions for the RDS Data API\. 

 The `AmazonRDSDataFullAccess` policy also includes permissions for the user to get the value of a secret from AWS Secrets Manager\. Users need to use Secrets Manager to store secrets that they can use in their calls to the Data API\. Using secrets means that users don't need to include database credentials for the resources that they target in their calls to the Data API\. The Data API transparently calls Secrets Manager, which allows \(or denies\) the user's request for the secret\. For information about setting up secrets to use with the Data API, see [Storing database credentials in AWS Secrets Manager](#data-api.secrets)\. 

 The `AmazonRDSDataFullAccess` policy provides complete access \(through the Data API\) to resources\. You can narrow the scope by defining your own policies that specify the Amazon Resource Name \(ARN\) of a resource\. 

For example, the following policy shows an example of the minimum required permissions for a user to access the Data API for the DB cluster identified by its ARN\. The policy includes the needed permissions to access Secrets Manager and get authorization to the DB instance for the user\. 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "SecretsManagerDbCredentialsAccess",
            "Effect": "Allow",
            "Action": [
                "secretsmanager:GetSecretValue"
            ],
            "Resource": "arn:aws:secretsmanager:*:*:secret:rds-db-credentials/*"
        },
        {
            "Sid": "RDSDataServiceAccess",
            "Effect": "Allow",
            "Action": [
                "rds-data:BatchExecuteStatement",
                "rds-data:BeginTransaction",
                "rds-data:CommitTransaction",
                "rds-data:ExecuteStatement",
                "rds-data:RollbackTransaction"
            ],
            "Resource": "arn:aws:rds:us-east-2:111122223333:cluster:prod"
        }
    ]
}
```

We recommend that you use a specific ARN for the "Resources" element in your policy statements \(as shown in the example\) rather than a wildcard \(\*\)\.

### Working with tag\-based authorization<a name="data-api.access.tag-based-access"></a>

The Data API and Secrets Manager both support tag\-based authorization\. *Tags* are key\-value pairs that label a resource, such as an RDS cluster, with an additional string value, for example: 
+ `environment:production`
+ `environment:development`

You can apply tags to your resources for cost allocation, operations support, access control, and many other reasons\. \(If you don't already have tags on your resources and you want to apply them, you can learn more at [Tagging Amazon RDS resources](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/USER_Tagging.html)\.\) You can use the tags in your policy statements to limit access to the RDS clusters that are labeled with these tags\. As an example, an Aurora DB cluster might have tags that identify its environment as either production or development\. 



The following example shows how you can use tags in your policy statements\. This statement requires that both the cluster and the secret passed in the Data API request have an `environment:production` tag\. 

Here's how the policy gets applied: When a user makes a call using the Data API, the request is sent to the service\. The Data API first verifies that the cluster ARN passed in the request is tagged with `environment:production`\. It then calls Secrets Manager to retrieve the value of the user's secret in the request\. Secrets Manager also verifies that the user's secret is tagged with `environment:production`\. If so, Data API then uses the retrieved value for the user's DB password\. Finally, if that's also correct, the Data API request is invoked successfully for the user\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "SecretsManagerDbCredentialsAccess",
            "Effect": "Allow",
            "Action": [
                 "secretsmanager:GetSecretValue"
               ],
            "Resource": "arn:aws:secretsmanager:*:*:secret:rds-db-credentials/*",
            "Condition": {
                    "StringEquals": {
                        "aws:ResourceTag/environment": [
                                         "production"
                                        ]
                     }
             }
        },
        {
            "Sid": "RDSDataServiceAccess",
            "Effect": "Allow",
            "Action": [
                  "rds-data:*"
               ],
            "Resource": "arn:aws:rds:us-east-2:111122223333:cluster:*",
            "Condition": {
                    "StringEquals": {
                        "aws:ResourceTag/environment": [
                                         "production"
                                        ]
                     }
             }
         }
     ]
}
```

The example shows separate actions for `rds-data` and `secretsmanager` for the Data API and Secrets Manager\. However, you can combine actions and define tag conditions in many different ways to support your specific use cases\. For more information, see [Using identity\-based policies \(IAM policies\) for Secrets Manager](https://docs.aws.amazon.com/secretsmanager/latest/userguide/auth-and-access_identity-based-policies.html#permissions_grant-limited-condition)\. 

 In the "Condition" element of the policy, you can choose tag keys from among the following: 
+  `aws:TagKeys` 
+  `aws:ResourceTag/${TagKey}` 

To learn more about resource tags and how to use `aws:TagKeys`, see [Controlling access to AWS resources using resource tags](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_tags.html#access_tags_control-tag-keys)\.

**Note**  
 Both the Data API and AWS Secrets Manager authorize users\. If you don't have permissions for all actions defined in a policy, you get an `AccessDeniedException` error\.

### Storing database credentials in AWS Secrets Manager<a name="data-api.secrets"></a>

When you call the Data API, you can pass credentials for the Aurora Serverless v1 DB cluster by using a secret in Secrets Manager\. To pass credentials in this way, you specify the name of the secret or the Amazon Resource Name \(ARN\) of the secret\.

**To store DB cluster credentials in a secret**

1. Use Secrets Manager to create a secret that contains credentials for the Aurora DB cluster\.

   For instructions, see [Create a database secret](https://docs.aws.amazon.com/secretsmanager/latest/userguide/create_database_secret.html) in the *AWS Secrets Manager User Guide*\.

1. Use the Secrets Manager console to view the details for the secret you created, or run the `aws secretsmanager describe-secret` AWS CLI command\.

   Note the name and ARN of the secret\. You can use them in calls to the Data API\.

For more information about using Secrets Manager, see the [AWS Secrets Manager User Guide](https://docs.aws.amazon.com/secretsmanager/latest/userguide/intro.html)\.

To understand how Amazon Aurora manages identity and access management, see [How Amazon Aurora works with IAM](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/security_iam_service-with-iam.html)\. 

For more information about creating an IAM policy, see [Creating IAM Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html) in the *IAM User Guide*\. For information about adding an IAM policy to a user, see [Adding and Removing IAM Identity Permissions](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_manage-attach-detach.html) in the *IAM User Guide*\.

## Enabling the Data API<a name="data-api.enabling"></a>

To use the Data API, enable it for your Aurora Serverless v1 DB cluster\. You can enable the Data API when you create or modify the DB cluster\.

**Note**  
 Currently, you can't use the Data API with Aurora Serverless v2 DB instances\. 

### Console<a name="data-api.enabling.console"></a>

You can enable the Data API by using the RDS console when you create or modify an Aurora Serverless v1 DB cluster\. When you create an Aurora Serverless v1 DB cluster, you do so by enabling the Data API in the RDS console's **Connectivity** section\. When you modify an Aurora Serverless v1 DB cluster, you do so by enabling the Data API in the RDS console's **Network & Security** section\.

The following screenshot shows the enabled **Data API** when modifying an Aurora Serverless v1 DB cluster\.

![\[Enabling the Data API for an Aurora Serverless v1 DB cluster with console\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/web-server-api-endpoint.png)

For instructions, see [Creating an Aurora Serverless v1 DB cluster](aurora-serverless.create.md) and [Modifying an Aurora Serverless v1 DB cluster](aurora-serverless.modifying.md)\.

### AWS CLI<a name="data-api.enabling.cli"></a>

When you create or modify an Aurora Serverless v1 DB cluster using AWS CLI commands, the Data API is enabled when you specify `--enable-http-endpoint`\.

You can specify the `--enable-http-endpoint` using the following AWS CLI commands:
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

When you create or modify an Aurora Serverless v1 DB cluster using Amazon RDS API operations, you enable the Data API by setting the `EnableHttpEndpoint` value to `true`\.

You can set the `EnableHttpEndpoint` value using the following API operations:
+  [CreateDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) 
+  [ModifyDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) 

## Creating an Amazon VPC endpoint for the Data API \(AWS PrivateLink\)<a name="data-api.vpc-endpoint"></a>

Amazon VPC enables you to launch AWS resources, such as Aurora DB clusters and applications, into a virtual private cloud \(VPC\)\. AWS PrivateLink provides private connectivity between VPCs and AWS services with high security on the Amazon network\. Using AWS PrivateLink, you can create Amazon VPC endpoints, which enable you to connect to services across different accounts and VPCs based on Amazon VPC\. For more information about AWS PrivateLink, see [VPC Endpoint Services \(AWS PrivateLink\)](https://docs.aws.amazon.com/vpc/latest/userguide/endpoint-service.html) in the *Amazon Virtual Private Cloud User Guide*\.

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

With the Data API enabled on your Aurora Serverless v1 DB cluster, you can run SQL statements on the Aurora DB cluster by using the Data API or the AWS CLI\. The Data API supports the programming languages supported by the AWS SDKs\. For more information, see [Tools to build on AWS](http://aws.amazon.com/tools/)\.

**Note**  
Currently, the Data API doesn't support arrays of Universal Unique Identifiers \(UUIDs\)\.

The Data API provides the following operations to perform SQL statements\.


****  

|  Data API operation  |  AWS CLI command  |  Description  | 
| --- | --- | --- | 
|  [https://docs.aws.amazon.com/rdsdataservice/latest/APIReference/API_ExecuteStatement.html](https://docs.aws.amazon.com/rdsdataservice/latest/APIReference/API_ExecuteStatement.html)  |  [https://docs.aws.amazon.com/cli/latest/reference/rds-data/execute-statement.html](https://docs.aws.amazon.com/cli/latest/reference/rds-data/execute-statement.html)  |  Runs a SQL statement on a database\.  | 
|  [https://docs.aws.amazon.com/rdsdataservice/latest/APIReference/API_BatchExecuteStatement.html](https://docs.aws.amazon.com/rdsdataservice/latest/APIReference/API_BatchExecuteStatement.html)  |  [https://docs.aws.amazon.com/cli/latest/reference/rds-data/batch-execute-statement.html](https://docs.aws.amazon.com/cli/latest/reference/rds-data/batch-execute-statement.html)  |  Runs a batch SQL statement over an array of data for bulk update and insert operations\. You can run a data manipulation language \(DML\) statement with an array of parameter sets\. A batch SQL statement can provide a significant performance improvement over individual insert and update statements\.  | 

You can use either operation to run individual SQL statements or to run transactions\. For transactions, the Data API provides the following operations\.


****  

|  Data API operation  |  AWS CLI command  |  Description  | 
| --- | --- | --- | 
|  [https://docs.aws.amazon.com/rdsdataservice/latest/APIReference/API_BeginTransaction.html](https://docs.aws.amazon.com/rdsdataservice/latest/APIReference/API_BeginTransaction.html)  |  [https://docs.aws.amazon.com/cli/latest/reference/rds-data/begin-transaction.html](https://docs.aws.amazon.com/cli/latest/reference/rds-data/begin-transaction.html)  |  Starts a SQL transaction\.  | 
|  [https://docs.aws.amazon.com/rdsdataservice/latest/APIReference/API_CommitTransaction.html](https://docs.aws.amazon.com/rdsdataservice/latest/APIReference/API_CommitTransaction.html)  |  [https://docs.aws.amazon.com/cli/latest/reference/rds-data/commit-transaction.html](https://docs.aws.amazon.com/cli/latest/reference/rds-data/commit-transaction.html)  |  Ends a SQL transaction and commits the changes\.  | 
|  [https://docs.aws.amazon.com/rdsdataservice/latest/APIReference/API_RollbackTransaction.html](https://docs.aws.amazon.com/rdsdataservice/latest/APIReference/API_RollbackTransaction.html)  |  [https://docs.aws.amazon.com/cli/latest/reference/rds-data/rollback-transaction.html](https://docs.aws.amazon.com/cli/latest/reference/rds-data/rollback-transaction.html)  |  Performs a rollback of a transaction\.  | 

The operations for performing SQL statements and supporting transactions have the following common Data API parameters and AWS CLI options\. Some operations support other parameters or options\.


****  

|  Data API operation parameter  |  AWS CLI command option  |  Required  |  Description  | 
| --- | --- | --- | --- | 
|  `resourceArn`  |  `--resource-arn`  |  Yes  |  The Amazon Resource Name \(ARN\) of the Aurora Serverless v1 DB cluster\.  | 
|  `secretArn`  |  `--secret-arn`  |  Yes  |  The name or ARN of the secret that enables access to the DB cluster\.  | 

You can use parameters in Data API calls to `ExecuteStatement` and `BatchExecuteStatement`, and when you run the AWS CLI commands `execute-statement` and `batch-execute-statement`\. To use a parameter, you specify a name\-value pair in the `SqlParameter` data type\. You specify the value with the `Field` data type\. The following table maps Java Database Connectivity \(JDBC\) data types to the data types that you specify in Data API calls\.


****  

|  JDBC data type  |  Data API data type  | 
| --- | --- | 
|  `INTEGER, TINYINT, SMALLINT, BIGINT`  |  `LONG` \(or `STRING`\)  | 
|  `FLOAT, REAL, DOUBLE`  |  `DOUBLE`  | 
|  `DECIMAL`  |  `STRING`  | 
|  `BOOLEAN, BIT`  |  `BOOLEAN`  | 
|  `BLOB, BINARY, LONGVARBINARY, VARBINARY`  |  `BLOB`  | 
|  `CLOB`  |  `STRING`  | 
|  Other types \(including types related to date and time\)  |  `STRING`  | 

**Note**  
 You can specify the `LONG` or `STRING` data type in your Data API call for `LONG` values returned by the database\. We recommend that you do so to avoid losing precision for extremely large numbers, which can happen when you work with JavaScript\. 

Certain types, such as `DECIMAL` and `TIME`, require a hint so that the Data API passes `String` values to the database as the correct type\. To use a hint, include values for `typeHint` in the `SqlParameter` data type\. The possible values for `typeHint` are the following:
+ `DATE` – The corresponding `String` parameter value is sent as an object of `DATE` type to the database\. The accepted format is `YYYY-MM-DD`\.
+ `DECIMAL` – The corresponding `String` parameter value is sent as an object of `DECIMAL` type to the database\.
+ `JSON` – The corresponding `String` parameter value is sent as an object of `JSON` type to the database\.
+ `TIME` – The corresponding `String` parameter value is sent as an object of `TIME` type to the database\. The accepted format is `HH:MM:SS[.FFF]`\.
+ `TIMESTAMP` – The corresponding `String` parameter value is sent as an object of `TIMESTAMP` type to the database\. The accepted format is `YYYY-MM-DD HH:MM:SS[.FFF]`\.
+ `UUID` – The corresponding `String` parameter value is sent as an object of `UUID` type to the database\.

**Note**  
 For Amazon Aurora PostgreSQL, the Data API always returns the Aurora PostgreSQL data type `TIMESTAMPTZ` in UTC time zone\.

### Calling the Data API with the AWS CLI<a name="data-api.calling.cli"></a>

You can call the Data API using the AWS CLI\.

The following examples use the AWS CLI for the Data API\. For more information, see [AWS CLI reference for the Data API](https://docs.aws.amazon.com/cli/latest/reference/rds-data/index.html)\.

In each example, replace the Amazon Resource Name \(ARN\) for the DB cluster with the ARN for your Aurora Serverless v1 DB cluster\. Also, replace the secret ARN with the ARN of the secret in Secrets Manager that allows access to the DB cluster\.

**Note**  
The AWS CLI can format responses in JSON\.

**Topics**
+ [Starting a SQL transaction](#data-api.calling.cli.begin-transaction)
+ [Running a SQL statement](#data-api.calling.cli.execute-statement)
+ [Running a batch SQL statement over an array of data](#data-api.calling.cli.batch-execute-statement)
+ [Committing a SQL transaction](#data-api.calling.cli.commit-transaction)
+ [Rolling back a SQL transaction](#data-api.calling.cli.rollback-transaction)

#### Starting a SQL transaction<a name="data-api.calling.cli.begin-transaction"></a>

You can start a SQL transaction using the `aws rds-data begin-transaction` CLI command\. The call returns a transaction identifier\.

**Important**  
A transaction times out if there are no calls that use its transaction ID in three minutes\. If a transaction times out before it's committed, it's rolled back automatically\.  
Data definition language \(DDL\) statements inside a transaction cause an implicit commit\. We recommend that you run each DDL statement in a separate `execute-statement` command with the `--continue-after-timeout` option\.

In addition to the common options, specify the `--database` option, which provides the name of the database\.

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

#### Running a SQL statement<a name="data-api.calling.cli.execute-statement"></a>

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
+  `--format-records-as "JSON"|"NONE"` – An optional value that specifies whether to format the result set as a JSON string\. The default is `"NONE"`\. For usage information about processing JSON result sets, see [Processing query results in JSON format](#data-api-json)\. 

The DB cluster returns a response for the call\.

**Note**  
The response size limit is 1 MiB\. If the call returns more than 1 MiB of response data, the call is terminated\.  
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
The `generatedFields` data isn't supported by Aurora PostgreSQL\. To get the values of generated fields, use the `RETURNING` clause\. For more information, see [ Returning data from modified rows](https://www.postgresql.org/docs/10/dml-returning.html) in the PostgreSQL documentation\.

#### Running a batch SQL statement over an array of data<a name="data-api.calling.cli.batch-execute-statement"></a>

You can run a batch SQL statement over an array of data by using the `aws rds-data batch-execute-statement` CLI command\. You can use this command to perform a bulk import or update operation\.

You can run the SQL statement in a transaction by specifying the transaction identifier with the `--transaction-id` option\. You can start a transaction by using the `aws rds-data begin-transaction` CLI command\. You can end and commit a transaction by using the `aws rds-data commit-transaction` CLI command\.

**Important**  
If you don't specify the `--transaction-id` option, changes that result from the call are committed automatically\.

In addition to the common options, specify the following options:
+ `--sql` \(required\) – A SQL statement to run on the DB cluster\.
**Tip**  
 For MySQL\-compatible statements, don't include a semicolon at the end of the `--sql` parameter\. A trailing semicolon might cause a syntax error\. 
+ `--transaction-id` \(optional\) – The identifier of a transaction that was started using the `begin-transaction` CLI command\. Specify the transaction ID of the transaction that you want to include the SQL statement in\.
+ `--parameter-set` \(optional\) – The parameter sets for the batch operation\.
+ `--database` \(optional\) – The name of the database\.

The DB cluster returns a response to the call\.

**Note**  
There isn't a fixed upper limit on the number of parameter sets\. However, the maximum size of the HTTP request submitted through the Data API is 4 MiB\. If the request exceeds this limit, the Data API returns an error and doesn't process the request\. This 4 MiB limit includes the size of the HTTP headers and the JSON notation in the request\. Thus, the number of parameter sets that you can include depends on a combination of factors, such as the size of the SQL statement and the size of each parameter set\.  
The response size limit is 1 MiB\. If the call returns more than 1 MiB of response data, the call is terminated\.  
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

#### Committing a SQL transaction<a name="data-api.calling.cli.commit-transaction"></a>

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

#### Rolling back a SQL transaction<a name="data-api.calling.cli.rollback-transaction"></a>

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

### Calling the Data API from a Python application<a name="data-api.calling.python"></a>

You can call the Data API from a Python application\.

The following examples use the AWS SDK for Python \(Boto\)\. For more information about Boto, see the [AWS SDK for Python \(Boto 3\) documentation](https://boto3.amazonaws.com/v1/documentation/api/latest/index.html)\.

In each example, replace the DB cluster's Amazon Resource Name \(ARN\) with the ARN for your Aurora Serverless v1 DB cluster\. Also, replace the secret ARN with the ARN of the secret in Secrets Manager that allows access to the DB cluster\.

**Topics**
+ [Running a SQL query](#data-api.calling.python.run-query)
+ [Running a DML SQL statement](#data-api.calling.python.run-inert)
+ [Running a SQL transaction](#data-api.calling.python.run-transaction)

#### Running a SQL query<a name="data-api.calling.python.run-query"></a>

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

#### Running a DML SQL statement<a name="data-api.calling.python.run-inert"></a>

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

#### Running a SQL transaction<a name="data-api.calling.python.run-transaction"></a>

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

### Calling the Data API from a Java application<a name="data-api.calling.java"></a>

You can call the Data API from a Java application\.

The following examples use the AWS SDK for Java\. For more information, see the [AWS SDK for Java Developer Guide](https://docs.aws.amazon.com/sdk-for-java/latest/developer-guide/welcome.html)\.

In each example, replace the DB cluster's Amazon Resource Name \(ARN\) with the ARN for your Aurora Serverless v1 DB cluster\. Also, replace the secret ARN with the ARN of the secret in Secrets Manager that allows access to the DB cluster\.

**Topics**
+ [Running a SQL query](#data-api.calling.java.run-query)
+ [Running a SQL transaction](#data-api.calling.java.run-transaction)
+ [Running a batch SQL operation](#data-api.calling.java.run-batch)

#### Running a SQL query<a name="data-api.calling.java.run-query"></a>

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

#### Running a SQL transaction<a name="data-api.calling.java.run-transaction"></a>

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

#### Running a batch SQL operation<a name="data-api.calling.java.run-batch"></a>

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

## Using the Java client library for Data API<a name="data-api.java-client-library"></a>

You can download and use a Java client library for the Data API\. This Java client library provides an alternative way to use the Data API\. Using this library, you can map your client\-side classes to requests and responses of the Data API\. This mapping support can ease integration with some specific Java types, such as `Date`, `Time`, and `BigDecimal`\.

### Downloading the Java client library for Data API<a name="data-api.java-client-library.downloading"></a>

The Data API Java client library is open source in GitHub at the following location:

[ https://github\.com/awslabs/rds\-data\-api\-client\-library\-java](https://github.com/awslabs/rds-data-api-client-library-java)

You can build the library manually from the source files, but the best practice is to consume the library using Apache Maven dependency management\. Add the following dependency to your Maven POM file\.

 For version 2\.x, which is compatible with AWS SDK 2\.x, use the following: 

```
<dependency>
   <groupId>software.amazon.rdsdata</groupId>
   <artifactId>rds-data-api-client-library-java</artifactId>
   <version>2.0.0</version>
</dependency>
```

 For version 1\.x, which is compatible with AWS SDK 1\.x, use the following: 

```
<dependency>
    <groupId>software.amazon.rdsdata</groupId>
    <artifactId>rds-data-api-client-library-java</artifactId>
    <version>1.0.8</version>
</dependency>
```

### Java client library examples<a name="data-api.java-client-library.examples"></a>

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
         .withParamSets(account1, account2)
         .execute();
```

In some cases, it's easier to work with simple values as input parameters\. You can do so with the following syntax\.

```
client.forSql("INSERT INTO accounts(accountId, name) VALUES(:accountId, :name)")
         .withParameter("accountId", 3)
         .withParameter("name", "Zhang")
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

In many cases, the database result set contains only a single value\. In order to simplify retrieving such results, the client library offers the following API:

```
int numberOfAccounts = client.forSql("SELECT COUNT(*) FROM accounts")
          .execute()
          .singleValue(Integer.class);
```

**Note**  
The `mapToList` function converts a SQL result set into a user\-defined object list\. We don't support using the `.withFormatRecordsAs(RecordsFormatType.JSON)` statement in an `ExecuteStatement` call for the Java client library, because it serves the same purpose\. For more information, see [Processing query results in JSON format](#data-api-json)\.

## Processing query results in JSON format<a name="data-api-json"></a>

 When you call the `ExecuteStatement` operation, you can choose to have the query results returned as a string in JSON format\. That way, you can use your programming language's JSON parsing capabilities to interpret and reformat the result set\. Doing so can help to avoid writing extra code to loop through the result set and interpret each column value\. 

 To request the result set in JSON format, you pass the optional `formatRecordsAs` parameter with a value of `JSON`\. The JSON\-formatted result set is returned in the `formattedRecords` field of the `ExecuteStatementResponse` structure\. 

 The `BatchExecuteStatement` action doesn't return a result set\. Thus, the JSON option doesn't apply to that action\. 

 To customize the keys in the JSON hash structure, define column aliases in the result set\. You can do so by using the `AS` clause in the column list of your SQL query\. 

 You might use the JSON capability to make the result set easier to read and map its contents to language\-specific frameworks\. Because the volume of the ASCII\-encoded result set is larger than the default representation, you might choose the default representation for queries that return large numbers of rows or large column values that consume more memory than is available to your application\. 

**Topics**
+ [Retrieving query results in JSON format](#data-api-json-querying)
+ [Data Type Mapping](#data-api-json-datatypes)
+ [Troubleshooting](#data-api-json-troubleshooting)
+ [Examples](#data-api-json-examples)

### Retrieving query results in JSON format<a name="data-api-json-querying"></a>

 To receive the the result set as a JSON string, include `.withFormatRecordsAs(RecordsFormatType.JSON)` in the `ExecuteStatement` call\. The return value comes back as a JSON string in the `formattedRecords` field\. In this case, the `columnMetadata` is `null`\. The column labels are the keys of the object that represents each row\. These column names are repeated for each row in the result set\. The column values are quoted strings, numeric values, or special values representing `true`, `false`, or `null`\. Column metadata such as length constraints and the precise type for numbers and strings isn't preserved in the JSON response\. 

 If you omit the `.withFormatRecordsAs()` call or specify a parameter of `NONE`, the result set is returned in binary format using the `Records` and `columnMetadata` fields\. 

### Data Type Mapping<a name="data-api-json-datatypes"></a>

 The SQL values in the result set are mapped to a smaller set of JSON types\. The values are represented in JSON as strings, numbers, and some special constants such as `true`, `false`, and `null`\. You can convert these values into variables in your application, using strong or weak typing as appropriate for your programming language\. 


****  

|   JDBC data type   |   JSON data type   | 
| --- | --- | 
|   `INTEGER`, `TINYINT`, `SMALLINT`, `BIGINT`   |   Number by default\. String if the `LongReturnType` option is set to `STRING`\.   | 
|   `FLOAT`, `REAL`, `DOUBLE`   |   Number   | 
|   `DECIMAL`   |   String by default\. Number if the `DecimalReturnType` option is set to `DOUBLE_OR_LONG`\.   | 
|   `STRING`   |   String   | 
|   `BOOLEAN`, `BIT`   |   Boolean   | 
|   `BLOB`, `BINARY`, `VARBINARY`, `LONGVARBINARY`   |   String in base64 encoding\.   | 
|   `CLOB`   |   String   | 
|   `ARRAY`   |   Array   | 
|   `NULL`   |   `null`   | 
|   Other types \(including types related to date and time\)   |   String   | 

### Troubleshooting<a name="data-api-json-troubleshooting"></a>

 The JSON response is limited to 10 megabytes\. If the response is larger than this limit, your program receives a `BadRequestException` error\. In this case, you can resolve the error using one of the following techniques: 
+  Reduce the number of rows in the result set\. To do so, add a `LIMIT` clause\. You might split a large result set into multiple smaller ones by submitting several queries with `LIMIT` and `OFFSET` clauses\. 

   If the result set includes rows that are filtered out by application logic, you can remove those rows from the result set by adding more conditions in the `WHERE` clause\. 
+  Reduce the number of columns in the result set\. To do so, remove items from the select list of the query\. 
+  Shorten the column labels by using column aliases in the query\. Each column name is repeated in the JSON string for each row in the result set\. Thus, a query result with long column names and many rows could exceed the size limit\. In particular, use column aliases for complicated expressions to avoid having the entire expression repeated in the JSON string\. 
+  Although with SQL you can use column aliases to produce a result set having more than one column with the same name, duplicate key names aren't allowed in JSON\. The RDS Data API returns an error if you request the result set in JSON format and more than one column has the same name\. Thus, make sure that all the column labels have unique names\. 

### Examples<a name="data-api-json-examples"></a>

 The following Java examples show how to call `ExecuteStatement` with the response as a JSON\-formatted string, then interpret the result set\. Substitute the appropriate values for the *databaseName*, *secretStoreArn*, and *clusterArn* parameters\. 

 The following Java example demonstrates a query that returns a decimal numeric value in the result set\. The `assertThat` calls test that the fields of the response have the expected properties based on the rules for JSON result sets\. 

 This example works with the following schema and sample data: 

```
create table test_simplified_json (a float);
insert into test_simplified_json values(10.0);
```

```
public void JSON_result_set_demo() {
    var sql = "select * from test_simplified_json";
    var request = new ExecuteStatementRequest()
      .withDatabase(databaseName)
      .withSecretArn(secretStoreArn)
      .withResourceArn(clusterArn)
      .withSql(sql)
      .withFormatRecordsAs(RecordsFormatType.JSON);
    var result = rdsdataClient.executeStatement(request);
}
```

 The value of the `formattedRecords` field from the preceding program is: 

```
[{"a":10.0}]
```

 The `Records` and `ColumnMetadata` fields in the response are both null, due to the presence of the JSON result set\. 

 The following Java example demonstrates a query that returns an integer numeric value in the result set\. The example calls `getFormattedRecords` to return only the JSON\-formatted string and ignore the other response fields that are blank or null\. The example deserializes the result into a structure representing a list of records\. Each record has fields whose names correspond to the column aliases from the result set\. This technique simplifies the code that parses the result set\. Your application doesn't have to loop through the rows and columns of the result set and convert each value to the appropriate type\. 

 This example works with the following schema and sample data: 

```
create table test_simplified_json (a int);
insert into test_simplified_json values(17);
```

```
public void JSON_deserialization_demo() {
    var sql = "select * from test_simplified_json";
    var request = new ExecuteStatementRequest()
      .withDatabase(databaseName)
      .withSecretArn(secretStoreArn)
      .withResourceArn(clusterArn)
      .withSql(sql)
      .withFormatRecordsAs(RecordsFormatType.JSON);
    var result = rdsdataClient.executeStatement(request)
      .getFormattedRecords();

/* Turn the result set into a Java object, a list of records.
   Each record has a field 'a' corresponding to the column
   labelled 'a' in the result set. */
    private static class Record { public int a; }
    var recordsList = new ObjectMapper().readValue(
        response, new TypeReference<List<Record>>() {
        });
}
```

 The value of the `formattedRecords` field from the preceding program is: 

```
[{"a":17}]
```

 To retrieve the `a` column of result row 0, the application would refer to `recordsList.get(0).a`\. 

 In contrast, the following Java example shows the kind of code that's required to construct a data structure holding the result set when you don't use the JSON format\. In this case, each row of the result set contains fields with information about a single user\. Building a data structure to represent the result set requires looping through the rows\. For each row, the code retrieves the value of each field, performs an appropriate type conversion, and assigns the result to the corresponding field in the object representing the row\. Then the code adds the object representing each user to the data structure representing the entire result set\. If the query was changed to reorder, add, or remove fields in the result set, the application code would have to change also\. 

```
/* Verbose result-parsing code that doesn't use the JSON result set format */
for (var row: response.getRecords()) {
    var user = User.builder()
      .userId(row.get(0).getLongValue())
      .firstName(row.get(1).getStringValue())
      .lastName(row.get(2).getStringValue())
      .dob(Instant.parse(row.get(3).getStringValue()))
      .build();
    result.add(user);
  }
```

 The following sample values show the values of the `formattedRecords` field for result sets with different numbers of columns, column aliases, and column data types\. 

 If the result set includes multiple rows, each row is represented as an object that is an array element\. Each column in the result set becomes a key in the object\. The keys are repeated for each row in the result set\. Thus, for result sets consisting of many rows and columns, you might need to define short column aliases to avoid exceeding the length limit for the entire response\. 

 This example works with the following schema and sample data: 

```
create table sample_names (id int, name varchar(128));
insert into sample_names values (0, "Jane"), (1, "Mohan"), (2, "Maria"), (3, "Bruce"), (4, "Jasmine");
```

```
[{"id":0,"name":"Jane"},{"id":1,"name":"Mohan"},
{"id":2,"name":"Maria"},{"id":3,"name":"Bruce"},{"id":4,"name":"Jasmine"}]
```

 If a column in the result set is defined as an expression, the text of the expression becomes the JSON key\. Thus, it's typically convenient to define a descriptive column alias for each expression in the select list of the query\. For example, the following query includes expressions such as function calls and arithmetic operations in its select list\. 

```
select count(*), max(id), 4+7 from sample_names;
```

 Those expressions are passed through to the JSON result set as keys\. 

```
[{"count(*)":5,"max(id)":4,"4+7":11}]
```

 Adding `AS` columns with descriptive labels makes the keys simpler to interpret in the JSON result set\. 

```
select count(*) as rows, max(id) as largest_id, 4+7 as addition_result from sample_names;
```

 With the revised SQL query, the column labels defined by the `AS` clauses are used as the key names\. 

```
[{"rows":5,"largest_id":4,"addition_result":11}]
```

 The value for each key\-value pair in the JSON string can be a quoted string\. The string might contain unicode characters\. If the string contains escape sequences or the `"` or `\` characters, those characters are preceded by backslash escape characters\. The following examples of JSON strings demonstrate these possibilities\. For example, the `string_with_escape_sequences` result contains the special characters backspace, newline, carriage return, tab, form feed, and `\`\. 

```
[{"quoted_string":"hello"}]
[{"unicode_string":"邓不利多"}]
[{"string_with_escape_sequences":"\b \n \r \t \f \\ '"}]
```

 The value for each key\-value pair in the JSON string can also represent a number\. The number might be an integer, a floating\-point value, a negative value, or a value represented as exponential notation\. The following examples of JSON strings demonstrate these possibilities\. 

```
[{"integer_value":17}]
[{"float_value":10.0}]
[{"negative_value":-9223372036854775808,"positive_value":9223372036854775807}]
[{"very_small_floating_point_value":4.9E-324,"very_large_floating_point_value":1.7976931348623157E308}]
```

 Boolean and null values are represented with the unquoted special keywords `true`, `false`, and `null`\. The following examples of JSON strings demonstrate these possibilities\. 

```
[{"boolean_value_1":true,"boolean_value_2":false}]
[{"unknown_value":null}]
```

 If you select a value of a BLOB type, the result is represented in the JSON string as a base64\-encoded value\. To convert the value back to its original representation, you can use the appropriate decoding function in your application's language\. For example, in Java you call the function `Base64.getDecoder().decode()`\. The following sample output shows the result of selecting a BLOB value of `hello world` and returning the result set as a JSON string\. 

```
[{"blob_column":"aGVsbG8gd29ybGQ="}]
```

 The following Python example shows how to access the values from the result of a call to the Python `execute_statement` function\. The result set is a string value in the field `response['formattedRecords']`\. The code turns the JSON string into a data structure by calling the `json.loads` function\. Then each row of the result set is a list element within the data structure, and within each row you can refer to each field of the result set by name\. 

```
import json

result = json.loads(response['formattedRecords'])
print (result[0]["id"])
```

 The following Javascript example shows how to access the values from the result of a call to the Javascript `executeStatement` function\. The result set is a string value in the field `response.formattedRecords`\. The code turns the JSON string into a data structure by calling the `JSON.parse` function\. Then each row of the result set is an array element within the data structure, and within each row you can refer to each field of the result set by name\. 

```
<script>
    const result = JSON.parse(response.formattedRecords);
    document.getElementById("display").innerHTML = result[0].id;
</script>
```

## Troubleshooting Data API issues<a name="data-api.troubleshooting"></a>

Use the following sections, titled with common error messages, to help troubleshoot problems that you have with the Data API\. 

**Topics**
+ [Transaction <transaction\_ID> is not found](#data-api.troubleshooting.tran-id-not-found)
+ [Packet for query is too large](#data-api.troubleshooting.packet-too-large)
+ [Database response exceeded size limit](#data-api.troubleshooting.response-size-too-large)
+ [HttpEndpoint is not enabled for cluster <cluster\_ID>](#data-api.troubleshooting.http-endpoint-not-enabled)

### Transaction <transaction\_ID> is not found<a name="data-api.troubleshooting.tran-id-not-found"></a>

In this case, the transaction ID specified in a Data API call wasn't found\. The cause for this issue is appended to the error message, and is one of the following:
+ Transaction may be expired\.

  Make sure that each transactional call runs within three minutes of the previous one\.

  It's also possible that the specified transaction ID wasn't created by a [BeginTransaction](https://docs.aws.amazon.com/rdsdataservice/latest/APIReference/API_BeginTransaction.html) call\. Make sure that your call has a valid transaction ID\.
+ One previous call resulted in a termination of your transaction\.

  The transaction was already ended by your `CommitTransaction` or `RollbackTransaction` call\.
+ Transaction has been aborted due to an error from a previous call\.

  Check whether your previous calls have thrown any exceptions\.

For information about running transactions, see [Calling the Data API](#data-api.calling)\.

### Packet for query is too large<a name="data-api.troubleshooting.packet-too-large"></a>

In this case, the result set returned for a row was too large\. The Data API size limit is 64 KB per row in the result set returned by the database\.

To solve this issue, make sure that each row in a result set is 64 KB or less\.

### Database response exceeded size limit<a name="data-api.troubleshooting.response-size-too-large"></a>

In this case, the size of the result set returned by the database was too large\. The Data API limit is 1 MiB in the result set returned by the database\.

To solve this issue, make sure that calls to the Data API return 1 MiB of data or less\. If you need to return more than 1 MiB, you can use multiple [https://docs.aws.amazon.com/rdsdataservice/latest/APIReference/API_ExecuteStatement.html](https://docs.aws.amazon.com/rdsdataservice/latest/APIReference/API_ExecuteStatement.html) calls with the `LIMIT` clause in your query\.

For more information about the `LIMIT` clause, see [SELECT syntax](https://dev.mysql.com/doc/refman/5.7/en/select.html) in the MySQL documentation\.

### HttpEndpoint is not enabled for cluster <cluster\_ID><a name="data-api.troubleshooting.http-endpoint-not-enabled"></a>

Check the following potential causes for this issue:
+ The Data API isn't enabled for the Aurora Serverless v1 DB cluster\. To use the Data API with an Aurora Serverless v1 DB cluster, the Data API must be enabled for the DB cluster\. For information about enabling the Data API, see [Enabling the Data API](#data-api.enabling)\.
+ The DB cluster was renamed after the Data API was enabled for it\. In that case, turn off the Data API for that cluster and then enable it again\.
+ The ARN you specified doesn't precisely match the ARN of the cluster\. Check that the ARN returned from another source or constructed by program logic matches the ARN of the cluster exactly\. For example, make sure that the ARN you use has the correct letter case for all alphabetic characters\. 

If the Data API has not been enabled for the DB cluster, enable it\. Make sure that it's an Aurora Serverless v1 cluster\. Currently, you can't use the Data API with Aurora Serverless v2\.

If the DB cluster was renamed after the Data API was enabled for the DB cluster, disable the Data API and then enable it again\.

For information about enabling the Data API, see [Enabling the Data API](#data-api.enabling)\.