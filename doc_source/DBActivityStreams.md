# Using Database Activity Streams with Amazon Aurora<a name="DBActivityStreams"></a>

Monitoring your database activity can help you provide safeguards for your database and help to meet compliance and regulatory requirements\. One way of monitoring database activity with Amazon Aurora is to use the database activity streams feature\. Database activity streams provide a near real\-time data stream of the database activity in your relational database\. When you integrate database activity streams with monitoring tools, you can monitor and audit database activity\. 

Beyond external security threats, managed databases need to provide protection against insider risks from database administrators \(DBAs\)\. Database activity streams help protect your databases from internal threats by controlling DBA access to the database activity streams\. Thus, the collection, transmission, storage, and subsequent processing of the stream of database activity is beyond the access of the DBAs that manage the database\. 

A stream of database activity is pushed from Aurora to an Amazon Kinesis data stream that is created on behalf of your Aurora DB cluster\. From Kinesis, the activity stream can then be consumed by AWS services such as Amazon Kinesis Data Firehose and AWS Lambda, or by applications for compliance management\. For database activity streams with Aurora PostgreSQL, these compliance applications include IBM's Security Guardium, McAfee's Data Center Security Suite, and Imperva's SecureSphere Database Audit and Protection\. Such applications can use the activity stream information to generate alerts and provide auditing of all activity on your Aurora DB clusters\.

**Tip**  
 Check the website for the particular application that you want to use with database activity streams\. Confirm support for the specific Aurora DB engine and engine version that you intend to use\. 

Database activity streams have the following limits and requirements:
+ For Aurora PostgreSQL, database activity streams are supported for version 2\.3 or higher and versions 3\.0 or higher\. For PostgreSQL version compatibility, see [Engine Versions for Amazon Aurora PostgreSQL ](AuroraPostgreSQL.Updates.20180305.md)\. 
+ For Aurora MySQL, database activity streams are supported for version 2\.08 or higher, which is compatible with MySQL version 5\.7\. 
+ Database activity streams support the DB instance classes listed for Aurora in [Supported DB Engines for DB Instance Classes](Concepts.DBInstanceClass.md#Concepts.DBInstanceClass.SupportAurora), with some exceptions:
  +  For Aurora PostgreSQL, you can't use streams with the db\.t3\.medium instance class\. 
  +  For Aurora MySQL, you can't use streams with any of the db\.t2 or db\.t3 instance classes\.
+ Database activity streams aren't supported in the following AWS Regions:
  + China \(Beijing\) Region, `cn-north-1`
  + China \(Ningxia\) Region, `cn-northwest-1`
  + AWS GovCloud \(US\-East\), `us-gov-east-1`
  + AWS GovCloud \(US\-West\), `us-gov-west-1`
  + Asia Pacific \(Osaka\-Local\) Region, `ap-northeast-3`
  + Europe \(Milan\) Region, `eu-south-1`
  + Africa \(Cape Town\) Region, `af-south-1`
  + Middle East \(Bahrain\) Region, `me-south-1`
+ Database activity streams require use of AWS Key Management Service \(AWS KMS\)\. AWS KMS is required because the activity streams are always encrypted\.

**Topics**
+ [Network Prerequisites for Aurora MySQL Database Activity Streams](#DBActivityStreams.Prereqs)
+ [Starting a Database Activity Stream](#DBActivityStreams.Enabling)
+ [Getting the Status of an Activity Stream](#DBActivityStreams.Status)
+ [Stopping an Activity Stream](#DBActivityStreams.Disabling)
+ [Monitoring Database Activity Streams](#DBActivityStreams.Monitoring)
+ [Managing Access to Database Activity Streams](#DBActivityStreams.ManagingAccess)

## Network Prerequisites for Aurora MySQL Database Activity Streams<a name="DBActivityStreams.Prereqs"></a>

 To use database activity streams with Aurora MySQL, all the DB instances that use database activity streams within a VPC must be able to access AWS KMS endpoints\. Make sure this requirement is satisfied before enabling database activity streams for your Aurora MySQL cluster\. 

**Important**  
 If the Aurora MySQL DB cluster can't access the AWS KMS endpoint, the activity stream stops functioning\. In that case, Aurora notifies you about this issue using RDS Events\. 

 If the Aurora cluster is publicly available, this requirement is satisfied automatically\.  

If you receive a notification that your Aurora MySQL cluster can't use database activity streams because it can't access a AWS KMS endpoint, check if your Aurora DB cluster is public or private\. If your Aurora DB cluster is private, you must configure it to enable connections\.

For an Aurora DB cluster to be public, it must be marked as publicly accessible\. If you look at the details for the DB cluster in the AWS Management Console, **Publicly Accessible** is **Yes** if this is the case\. The DB cluster must also be in an Amazon VPC public subnet\. For more information about publicly accessible DB instances, see [Working with a DB Instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md)\. For more information about public Amazon VPC subnets, see [Your VPC and Subnets](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html)\.

If your Aurora DB cluster isn't publicly accessible and in a VPC public subnet, it is private\. You might keep your Aurora MySQL cluster private and use it with database activity streams\. If so, configure the cluster so that it can connect to Internet addresses through Network Address Translation \(NAT\)\. For more information about configuring NAT in your VPC, see [NAT Gateways](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html)\.

For more information about configuring VPC endpoints, see [VPC Endpoints](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints.html)\.

## Starting a Database Activity Stream<a name="DBActivityStreams.Enabling"></a>

You start an activity stream at the DB cluster level to monitor database activity for all DB instances of the cluster\. Any DB instances added to the cluster are also automatically monitored\. 

When you start an activity stream, each database activity event, such as a change or access, generates an activity stream event\. Access events are generated from SQL commands such as `CONNECT` and `SELECT`\. Change events are generated from SQL commands such as `CREATE` and `INSERT`\. To make each activity stream event durable, Aurora encrypts and stores it\.

You can choose to have the database session handle database activity events either asynchronously or synchronously:
+ **Asynchronous mode** – In *asynchronous mode, *when a database session generates an activity stream event, the session returns to normal activities immediately\. In the background, the activity stream event is made a durable record\. If an error occurs in the background task, an RDS event is sent\. This event indicates the beginning and end of any time windows where activity stream event records might have been lost\.

  Asynchronous mode favors database performance over the accuracy of the activity stream\. 
**Note**  
 Asynchronous mode is available for both Aurora PostgreSQL and Aurora MySQL\. 
+ **Synchronous mode** – In *synchronous mode, *when a database session generates an activity stream event, the session blocks other activities until the event is made durable\. If the event can't be made durable for some reason, the database session returns to normal activities\. However, an RDS event is sent indicating that activity stream records might be lost for some time\. A second RDS event is sent after the system is back to a healthy state\.

  The synchronous mode favors the accuracy of the activity stream over database performance\.
**Note**  
 Synchronous mode is available for Aurora PostgreSQL\. You can't use synchronous mode with Aurora MySQL\. 

### Console<a name="DBActivityStreams.Enabling-collapsible-section-E1"></a>

**To start an activity stream**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the DB cluster that you want to use\.

1. For **Actions**, choose **Start activity stream**\. The **Database Activity Stream** window appears\. 

1. Enter the following settings in the **Database Activity Stream** window:
   + For **Master key**, choose a key from the list of AWS KMS keys\. 
**Note**  
 If your Aurora MySQL cluster can't access AWS KMS keys, follow the instructions in [Network Prerequisites for Aurora MySQL Database Activity Streams](#DBActivityStreams.Prereqs) to enable such access first\. 

     The master key is used to encrypt the key that in turn encrypts the database activity logged\. You must choose a master key other than the default key\. For more information about encryption keys and AWS KMS, see [What is AWS Key Management Service?](https://docs.aws.amazon.com/kms/latest/developerguide/overview.html) in the *AWS Key Management Service Developer Guide\.*
   + For **Database activity stream mode**, choose **Asynchronous** or **Synchronous**\.
**Note**  
 Currently, this choice only applies to Aurora PostgreSQL\. For Aurora MySQL, you can use only asynchronous mode\. 
   + Choose **Apply immediately**\.

     If you choose **Schedule for the next maintenance window**, the database doesn't restart right away\. Instead, it remains in the `PENDING REBOOT` state\. In this case, the database activity stream doesn't start until the next maintenance window\.

   When you're done entering settings, choose **Continue**\.

   The cluster's DB instance status shows that the database activity stream is being configured\. 

### AWS CLI<a name="DBActivityStreams.Enabling-collapsible-section-E2"></a>

To start database activity streams for a DB cluster, configure the DB cluster using the [start\-activity\-stream](https://docs.aws.amazon.com/cli/latest/reference/rds/start-activity-stream.html) AWS CLI command\. Identify the AWS Region for the DB cluster with the `--region` parameter\. The `--apply-immediately` parameter is optional\.

For Linux, macOS, or Unix:

```
aws rds --region MY_REGION \
    start-activity-stream \
    --mode [sync | async] \
    --kms-key-id MY_KMS_KEY_ARN \
    --resource-arn MY_CLUSTER_ARN \
    --apply-immediately
```

For Windows:

```
aws rds --region MY_REGION ^
    start-activity-stream ^
    --mode [sync | async] ^
    --kms-key-id MY_KMS_KEY_ARN ^
    --resource-arn MY_CLUSTER_ARN ^
    --apply-immediately
```

**Note**  
 The `--mode` parameter is required\. For Aurora PostgreSQL, you can choose either value\. For Aurora MySQL, you must always specify `async`\. 

## Getting the Status of an Activity Stream<a name="DBActivityStreams.Status"></a>

You can get the status of an activity stream using the console or AWS CLI\.

### Console<a name="DBActivityStreams.Status-collapsible-section-S1"></a>

**To get the status of a DB cluster's activity stream**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the DB cluster\.

1. Choose the **Configuration** tab, and check **Database activity stream** for status\.

### AWS CLI<a name="DBActivityStreams.Status-collapsible-section-S2"></a>

You can get a DB cluster's activity stream configuration as the response to a [describe\-db\-clusters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html) CLI request\. In the following example, see the values for `ActivityStreamKinesisStreamName`, `ActivityStreamStatus`, `ActivityStreamKmsKeyId`, and `ActivityStreamMode`\.

The request is as follows\.

```
aws rds --region MY_REGION describe-db-clusters --db-cluster-identifier my-cluster
```

The response includes the following items for a database activity stream\.

 The following example shows a JSON response\. These fields are the same for Aurora PostgreSQL and Aurora MySQL, except that `ActivityStreamMode` is always `async` for Aurora MySQL, while for Aurora PostgreSQL it might be `sync` or `async`\.

```
{
"DBClusters": [
  {
    "DBClusterIdentifier": "my-cluster",
    ...
    "ActivityStreamKinesisStreamName": "aws-rds-das-cluster-A6TSYXITZCZXJHIRVFUBZ5LTWY",
    "ActivityStreamStatus": "starting",
    "ActivityStreamKmsKeyId": "12345678-abcd-efgh-ijkl-bd041f170262",
    "ActivityStreamMode": "async",
    "DbClusterResourceId": "cluster-ABCD123456"
    ...
  }
]
}
```

## Stopping an Activity Stream<a name="DBActivityStreams.Disabling"></a>

You can stop an activity stream using the console or AWS CLI\. 

If you delete a DB cluster, the activity stream is stopped and the underlying Amazon Kinesis stream is deleted automatically\.

### Console<a name="DBActivityStreams.Disabling-collapsible-section-D1"></a>

**To turn off an activity stream**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the DB cluster that you want to stop the database activity stream for\.

1. For **Actions**, choose **Stop activity stream**\. The **Database Activity Stream** window appears\.

   1. Choose **Apply immediately**\.

      If you choose **Schedule for the next maintenance window**, the database doesn't restart right away\. Instead, it remains in the `PENDING REBOOT` state\. In this case, the data activity stream isn't disabled until the next maintenance window\.

   1. Choose **Continue**\.

### AWS CLI<a name="DBActivityStreams.Disabling-collapsible-section-D2"></a>

To stop database activity streams for a DB cluster, configure the DB cluster using the AWS CLI command [stop\-activity\-stream](https://docs.aws.amazon.com/cli/latest/reference/rds/stop-activity-stream.html)\. Identify the AWS Region for the DB cluster using the `--region` parameter\. The `--apply-immediately` parameter is optional\.

For Linux, macOS, or Unix:

```
aws rds --region MY_REGION \
        stop-activity-stream \
        --resource-arn MY_CLUSTER_ARN \
        --apply-immediately
```

For Windows:

```
aws rds --region MY_REGION ^
        stop-activity-stream ^
        --resource-arn MY_CLUSTER_ARN ^
        --apply-immediately
```

## Monitoring Database Activity Streams<a name="DBActivityStreams.Monitoring"></a>

Database activity streams monitor and report activities on the database as described following\.

The stream of activity is collected and transmitted to Amazon Kinesis\. From Kinesis, you can monitor the activity stream, or other services and applications can consume the activity stream for further analysis\. You can find the underlying Kinesis stream name by using the AWS CLI command `describe-db-clusters` or the RDS API `DescribeDBClusters` operation\.

Aurora manages the Kinesis stream for you\.
+  You don't use an existing Kinesis stream\. Aurora creates the Kinesis stream automatically with a 24\-hour retention period\. 
+  Aurora scales the Kinesis stream if necessary\. 
+  Aurora recreates the Kinesis stream if you delete it\. 
+  If you stop the database activity stream or delete the DB cluster, Aurora deletes the Kinesis stream\. 

The following categories of activity are monitored and put in the activity stream audit log:
+ **SQL commands** – All SQL commands are audited, and also prepared statements, built\-in functions, and functions in Procedural Language for SQL \(PL/SQL\)\. Calls to stored procedures are audited\. Any SQL statements issued inside stored procedures or functions are also audited\.
+ **Other database information** – Activity monitored includes the full SQL statement, the row count of affected rows from DML commands, accessed objects, and the unique database name\. For Aurora PostgreSQL, database activity streams also monitor the bind variables and stored procedure parameters\. 
**Important**  
The full SQL text of each statement is visible in the activity stream audit log, including any sensitive data\. However, database user passwords are redacted if Aurora can determine them from the context, such as in the following SQL statement\.   

  ```
  ALTER ROLE role-name WITH password
  ```
+ **Connection information** – Activity monitored includes session and network information, the server process ID, and exit codes\.

If an activity stream has a failure while monitoring a DB instance, you are notified by using RDS events\. If a failure occurs, you can shut down the DB instance or let it continue\.

**Topics**
+ [Accessing an Activity Stream from Kinesis](#DBActivityStreams.KinesisAccess)
+ [Audit Log Contents and Examples](#DBActivityStreams.AuditLog)
+ [Processing an Activity Stream using the AWS SDK](#DBActivityStreams.CodeExample)

### Accessing an Activity Stream from Kinesis<a name="DBActivityStreams.KinesisAccess"></a>

When you enable an activity stream for a DB cluster, a Kinesis stream is created for you\. From Kinesis, you can monitor your database activity in real time\. To further analyze database activity, you can connect your Kinesis stream to consumer applications\. You can also connect it to compliance management applications\.  

**To access an activity stream from Kinesis**

1. Open the Kinesis console at [https://console\.aws\.amazon\.com/kinesis](https://console.aws.amazon.com/kinesis)\.

1. Choose your activity stream from the list of Kinesis streams\.

   An activity stream's name includes the prefix `aws-rds-das-` followed by the DB cluster's resource ID\. The following is an example\. 

   ```
   aws-rds-das-cluster-NHVOV4PCLWHGF52NP
   ```

   To use the Amazon RDS console to find your DB cluster's resource ID, choose your DB cluster from the list of databases, and then choose the **Configuration** tab\.

   To use the AWS CLI to find the full Kinesis stream name for an activity stream, use a [describe\-db\-clusters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html) CLI request and note the value of `ActivityStreamKinesisStreamName` in the response\.

1. Choose **Monitoring** to begin observing the database activity\.

For more information about using Amazon Kinesis, see [What Is Amazon Kinesis Data Streams?](https://docs.aws.amazon.com/streams/latest/dev/introduction.html)\.

### Audit Log Contents and Examples<a name="DBActivityStreams.AuditLog"></a>

The database activity events that are monitored are represented in the Kinesis activity stream as JSON strings\. The structure consists of a JSON object containing a `DatabaseActivityMonitoringRecord`, which in turn contains a `databaseActivityEventList` array of activity events\. 

**Topics**
+ [Examples of Database Activity Streams Audit Log](#DBActivityStreams.AuditLog.Examples)
+ [Database Activity Monitoring Records JSON Object](#DBActivityStreams.AuditLog.DatabaseActivityMonitoringRecords)
+ [databaseActivityEvents JSON Object](#DBActivityStreams.AuditLog.databaseActivityEvents)
+ [databaseActivityEventList JSON Array](#DBActivityStreams.AuditLog.databaseActivityEventList)

#### Examples of Database Activity Streams Audit Log<a name="DBActivityStreams.AuditLog.Examples"></a>

Following are sample decrypted JSON audit logs of activity event records\.

**Example Activity Event Record of an Aurora PostgreSQL CONNECT SQL Statement**  
Following is an activity event record of a logon with the use of a `CONNECT` SQL statement \(`command`\) by a psql client \(`clientApplication`\)\.   

```
{
  "type":"DatabaseActivityMonitoringRecords",
  "version":"1.1",
  "databaseActivityEvents": 
    {
      "type":"DatabaseActivityMonitoringRecord",
      "clusterId":"cluster-4HNY5V4RRNPKKYB7ICFKE5JBQQ",
      "instanceId":"db-FZJTMYKCXQBUUZ6VLU7NW3ITCM",
      "databaseActivityEventList":[
        {
          "startTime": "2019-10-30 00:39:49.940668+00",
          "logTime": "2019-10-30 00:39:49.990579+00",
          "statementId": 1,
          "substatementId": 1,
          "objectType": null,
          "command": "CONNECT",
          "objectName": null,
          "databaseName": "postgres",
          "dbUserName": "rdsadmin",
          "remoteHost": "172.31.3.195",
          "remotePort": "49804",
          "sessionId": "5ce5f7f0.474b",
          "rowCount": null,
          "commandText": null,
          "paramList": [],
          "pid": 18251,
          "clientApplication": "psql",
          "exitCode": null,
          "class": "MISC",
          "serverVersion": "2.3.1",
          "serverType": "PostgreSQL",
          "serviceName": "Amazon Aurora PostgreSQL-Compatible edition",
          "serverHost": "172.31.3.192",
          "netProtocol": "TCP",
          "dbProtocol": "Postgres 3.0",
          "type": "record",
          "errorMessage": null
        }
      ]
    },
   "key":"decryption-key"
}
```

**Example Activity Event Record of an Aurora MySQL CONNECT SQL Statement**  
Following is an activity event record of a logon with the use of a `CONNECT` SQL statement \(`command`\) by a mysql client \(`clientApplication`\)\.   

```
{
  "type":"DatabaseActivityMonitoringRecord",
  "clusterId":"cluster-some_id",
  "instanceId":"db-some_id",
  "databaseActivityEventList":[
    {
      "logTime":"2020-05-22 18:07:13.267214+00",
      "type":"record",
      "clientApplication":null,
      "pid":2830,
      "dbUserName":"rdsadmin",
      "databaseName":"",
      "remoteHost":"localhost",
      "remotePort":"11053",
      "command":"CONNECT",
      "commandText":"",
      "paramList":null,
      "objectType":"TABLE",
      "objectName":"",
      "statementId":0,
      "substatementId":1,
      "exitCode":"0",
      "sessionId":"725121",
      "rowCount":0,
      "serverHost":"master",
      "serverType":"MySQL",
      "serviceName":"Amazon Aurora MySQL",
      "serverVersion":"MySQL 5.7.12",
      "startTime":"2020-05-22 18:07:13.267207+00",
      "endTime":"2020-05-22 18:07:13.267213+00",
      "transactionId":"0",
      "dbProtocol":"MySQL",
      "netProtocol":"TCP",
      "errorMessage":"",
      "class":"MAIN"
    }
  ]
}
```

**Example Activity Event Record of an Aurora PostgreSQL CREATE TABLE Statement**  
Following is an example of a `CREATE TABLE` event for Aurora PostgreSQL\.  

```
{
  "type":"DatabaseActivityMonitoringRecords",
  "version":"1.1",
  "databaseActivityEvents": 
    {
      "type":"DatabaseActivityMonitoringRecord",
      "clusterId":"cluster-4HNY5V4RRNPKKYB7ICFKE5JBQQ",
      "instanceId":"db-FZJTMYKCXQBUUZ6VLU7NW3ITCM",
      "databaseActivityEventList":[
        {
          "startTime": "2019-05-24 00:36:54.403455+00",
          "logTime": "2019-05-24 00:36:54.494235+00",
          "statementId": 2,
          "substatementId": 1,
          "objectType": null,
          "command": "CREATE TABLE",
          "objectName": null,
          "databaseName": "postgres",
          "dbUserName": "rdsadmin",
          "remoteHost": "172.31.3.195",
          "remotePort": "34534",
          "sessionId": "5ce73c6f.7e64",
          "rowCount": null,
          "commandText": "create table my_table (id serial primary key, name varchar(32));",
          "paramList": [],
          "pid": 32356,
          "clientApplication": "psql",
          "exitCode": null,
          "class": "DDL",
          "serverVersion": "2.3.1",
          "serverType": "PostgreSQL",
          "serviceName": "Amazon Aurora PostgreSQL-Compatible edition",
          "serverHost": "172.31.3.192",
          "netProtocol": "TCP",
          "dbProtocol": "Postgres 3.0",
          "type": "record",
          "errorMessage": null
        }
      ]
    },
   "key":"decryption-key"
}
```

**Example Activity Event Record of an Aurora MySQL CREATE TABLE Statement**  
Following is an example of a `CREATE TABLE` statement for Aurora MySQL\. The operation is represented as two separate event records\. One event has `"class":"MAIN"`\. The other event has `"class":"AUX"`\. The messages might arrive in any order\. The `logTime` field of the `MAIN` event is always earlier than the `logTime` fields of any corresponding `AUX` events\.   
 The following example shows the event with a `class` value of `MAIN`\.   

```
{
  "type":"DatabaseActivityMonitoringRecord",
  "clusterId":"cluster-some_id",
  "instanceId":"db-some_id",
  "databaseActivityEventList":[
    {
      "logTime":"2020-05-22 18:07:12.250221+00",
      "type":"record",
      "clientApplication":null,
      "pid":2830,
      "dbUserName":"master",
      "databaseName":"test",
      "remoteHost":"localhost",
      "remotePort":"11054",
      "command":"QUERY",
      "commandText":"CREATE TABLE test1 (id INT)",
      "paramList":null,
      "objectType":"TABLE",
      "objectName":"test1",
      "statementId":65459278,
      "substatementId":1,
      "exitCode":"0",
      "sessionId":"725118",
      "rowCount":0,
      "serverHost":"master",
      "serverType":"MySQL",
      "serviceName":"Amazon Aurora MySQL",
      "serverVersion":"MySQL 5.7.12",
      "startTime":"2020-05-22 18:07:12.226384+00",
      "endTime":"2020-05-22 18:07:12.250222+00",
      "transactionId":"0",
      "dbProtocol":"MySQL",
      "netProtocol":"TCP",
      "errorMessage":"",
      "class":"MAIN"
    }
  ]
}
```
 The following example shows the corresponding event with a `class` value of `AUX`\.   

```
{
  "type":"DatabaseActivityMonitoringRecord",
  "clusterId":"cluster-some_id",
  "instanceId":"db-some_id",
  "databaseActivityEventList":[
    {
      "logTime":"2020-05-22 18:07:12.247182+00",
      "type":"record",
      "clientApplication":null,
      "pid":2830,
      "dbUserName":"master",
      "databaseName":"test",
      "remoteHost":"localhost",
      "remotePort":"11054",
      "command":"CREATE",
      "commandText":"test1",
      "paramList":null,
      "objectType":"TABLE",
      "objectName":"test1",
      "statementId":65459278,
      "substatementId":2,
      "exitCode":"",
      "sessionId":"725118",
      "rowCount":0,
      "serverHost":"master",
      "serverType":"MySQL",
      "serviceName":"Amazon Aurora MySQL",
      "serverVersion":"MySQL 5.7.12",
      "startTime":"2020-05-22 18:07:12.226384+00",
      "endTime":"2020-05-22 18:07:12.247182+00",
      "transactionId":"0",
      "dbProtocol":"MySQL",
      "netProtocol":"TCP",
      "errorMessage":"",
      "class":"AUX"
    }
  ]
}
```

**Example Activity Event Record of an Aurora PostgreSQL SELECT Statement**  
Following is an example of a `SELECT` event\.  

```
{
  "type":"DatabaseActivityMonitoringRecords",
  "version":"1.1",
  "databaseActivityEvents": 
    {
      "type":"DatabaseActivityMonitoringRecord",
      "clusterId":"cluster-4HNY5V4RRNPKKYB7ICFKE5JBQQ",
      "instanceId":"db-FZJTMYKCXQBUUZ6VLU7NW3ITCM",
      "databaseActivityEventList":[
        {
          "startTime": "2019-05-24 00:39:49.920564+00",
          "logTime": "2019-05-24 00:39:49.940668+00",
          "statementId": 6,
          "substatementId": 1,
          "objectType": "TABLE",
          "command": "SELECT",
          "objectName": "public.my_table",
          "databaseName": "postgres",
          "dbUserName": "rdsadmin",
          "remoteHost": "172.31.3.195",
          "remotePort": "34534",
          "sessionId": "5ce73c6f.7e64",
          "rowCount": 10,
          "commandText": "select * from my_table;",
          "paramList": [],
          "pid": 32356,
          "clientApplication": "psql",
          "exitCode": null,
          "class": "READ",
          "serverVersion": "2.3.1",
          "serverType": "PostgreSQL",
          "serviceName": "Amazon Aurora PostgreSQL-Compatible edition",
          "serverHost": "172.31.3.192",
          "netProtocol": "TCP",
          "dbProtocol": "Postgres 3.0",
          "type": "record",
          "errorMessage": null
        }
      ]
    },
   "key":"decryption-key"
}
```

**Example Activity Event Record of an Aurora MySQL SELECT Statement**  
Following is an example of a `SELECT` event\.  
 The following example shows the event with a `class` value of `MAIN`\.   

```
{
  "type":"DatabaseActivityMonitoringRecord",
  "clusterId":"cluster-some_id",
  "instanceId":"db-some_id",
  "databaseActivityEventList":[
    {
      "logTime":"2020-05-22 18:29:57.986467+00",
      "type":"record",
      "clientApplication":null,
      "pid":2830,
      "dbUserName":"master",
      "databaseName":"test",
      "remoteHost":"localhost",
      "remotePort":"11054",
      "command":"QUERY",
      "commandText":"SELECT * FROM test1 WHERE id < 28",
      "paramList":null,
      "objectType":"TABLE",
      "objectName":"test1",
      "statementId":65469218,
      "substatementId":1,
      "exitCode":"0",
      "sessionId":"726571",
      "rowCount":2,
      "serverHost":"master",
      "serverType":"MySQL",
      "serviceName":"Amazon Aurora MySQL",
      "serverVersion":"MySQL 5.7.12",
      "startTime":"2020-05-22 18:29:57.986364+00",
      "endTime":"2020-05-22 18:29:57.986467+00",
      "transactionId":"0",
      "dbProtocol":"MySQL",
      "netProtocol":"TCP",
      "errorMessage":"",
      "class":"MAIN"
    }
  ]
}
```
 The following example shows the corresponding event with a `class` value of `AUX`\.   

```
{
  "type":"DatabaseActivityMonitoringRecord",
  "clusterId":"cluster-some_id",
  "instanceId":"db-some_id",
  "databaseActivityEventList":[
    {
      "logTime":"2020-05-22 18:29:57.986399+00",
      "type":"record",
      "clientApplication":null,
      "pid":2830,
      "dbUserName":"master",
      "databaseName":"test",
      "remoteHost":"localhost",
      "remotePort":"11054",
      "command":"READ",
      "commandText":"test1",
      "paramList":null,
      "objectType":"TABLE",
      "objectName":"test1",
      "statementId":65469218,
      "substatementId":2,
      "exitCode":"",
      "sessionId":"726571",
      "rowCount":0,
      "serverHost":"master",
      "serverType":"MySQL",
      "serviceName":"Amazon Aurora MySQL",
      "serverVersion":"MySQL 5.7.12",
      "startTime":"2020-05-22 18:29:57.986364+00",
      "endTime":"2020-05-22 18:29:57.986399+00",
      "transactionId":"0",
      "dbProtocol":"MySQL",
      "netProtocol":"TCP",
      "errorMessage":"",
      "class":"AUX"
    }
  ]
}
```

#### Database Activity Monitoring Records JSON Object<a name="DBActivityStreams.AuditLog.DatabaseActivityMonitoringRecords"></a>

The database activity event records are in a JSON object that contains the following information\.


****  

| JSON Field | Data Type | Description | 
| --- | --- | --- | 
|  `type`  | string |  The type of JSON record\. The value is `DatabaseActivityMonitoringRecords`\.  | 
| version | string | The version of the database activity monitoring records\. The version of the generated database activity records depends on the engine version of the DB cluster\.[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/DBActivityStreams.html)All of the following fields are in both version 1\.0 and version 1\.1 except where specifically noted\. | 
|  [databaseActivityEvents](#DBActivityStreams.AuditLog.databaseActivityEvents)  | string |  A JSON object containing the activity events\.  | 
| key | string | An encryption key you use to decrypt the [databaseActivityEventList](#DBActivityStreams.AuditLog.databaseActivityEventList) databaseActivityEventList JSON array\. | 

#### databaseActivityEvents JSON Object<a name="DBActivityStreams.AuditLog.databaseActivityEvents"></a>

The `databaseActivityEvents` JSON object contains the following information\.

##### Top\-Level Fields in JSON Record<a name="DBActivityStreams.AuditLog.topLevel"></a>

 Each event in the audit log is wrapped inside a record in JSON format\. This record contains the following fields\. 

**type**  
 This field always has the value `DatabaseActivityMonitoringRecords`\. 

**version**  
 This field represents the version of the database activity stream data protocol or contract\. It defines what fields are available\. Version 1\.0 represents the original data activity streams support for Aurora PostgreSQL versions 10\.7 and 11\.4\. Version 1\.1 represents the data activity streams support for Aurora PostgreSQL versions 10\.10 and higher and Aurora PostgreSQL 11\.5 and higher\. Version 1\.1 includes the additional fields `errorMessage` and `startTime`\. Version 1\.2 represents the data activity streams support for Aurora MySQL 2\.08 and higher\. Version 1\.2 includes the additional fields `endTime` and `transactionId`\. 

**databaseActivityEvents**  
 An encrypted string representing one or more activity events\. It's represented as a base64 byte array\. When you decrypt the string, the result is a record in JSON format with fields as shown in the the examples in this section\. 

**key**  
 The encrypted data key used to encrypt the `databaseActivityEvents` string\. This is the same AWS KMS key that you provided when you started the database activity stream\. 

 The following example shows the format of this record\. 

```
{
  "type":"DatabaseActivityMonitoringRecords",
  "version":"1.1",
  "databaseActivityEvents":"encrypted audit records",
  "key":"encrypted key"
}
```

Take the following steps to decrypt the contents of the `databaseActivityEvents` field:

1.  Decrypt the value in the `key` JSON field using the AWS KMS key you provided when starting database activity stream\. Doing so returns the data encryption key in clear text\. 

1.  Base64\-decode the value in the `databaseActivityEvents` JSON field to obtain the ciphertext, in binary format, of the audit payload\. 

1.  Decrypt the binary ciphertext with the data encryption key that you decoded in the first step\. 

1.  Decompress the decrypted payload\. 
   +  The encrypted payload is in the `databaseActivityEvents` field\. 
   +  The `databaseActivityEventList` field contains an array of audit records\. The `type` fields in the array can be `record` or `heartbeat`\. 

The audit log activity event record is a JSON object that contains the following information\.


****  

| JSON Field | Data Type | Description | 
| --- | --- | --- | 
|  `type`  | string |  The type of JSON record\. The value is `DatabaseActivityMonitoringRecord`\.  | 
| clusterId | string | The DB cluster resource identifier\. It corresponds to the DB cluster attribute DbClusterResourceId\. | 
| instanceId | string | The DB instance resource identifier\. It corresponds to the DB instance attribute DbiResourceId\. | 
|  [databaseActivityEventList](#DBActivityStreams.AuditLog.databaseActivityEventList)   | string |  An array of activity audit records or heartbeat messages\.  | 

#### databaseActivityEventList JSON Array<a name="DBActivityStreams.AuditLog.databaseActivityEventList"></a>

The audit log payload is an encrypted `databaseActivityEventList` JSON array\. The following table lists alphabetically the fields for each activity event in the decrypted `DatabaseActivityEventList` array of an audit log\. The fields are different depending on whether you use Aurora PostgreSQL or Aurora MySQL\. Consult the table that applies to your database engine\.

**Important**  
 The event structure is subject to change\. Aurora might add new fields to activity events in the future\. In applications that parse the JSON data, make sure that your code can ignore or take appropriate actions for unknown field names\. 


**databaseActivityEventList fields for Aurora PostgreSQL**  

| Field | Data Type | Description | 
| --- | --- | --- | 
| class | string |  The class of activity event\. Valid values for Aurora PostgreSQL are the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/DBActivityStreams.html)  | 
| clientApplication | string | The application the client used to connect as reported by the client\. The client doesn't have to provide this information, so the value can be null\. | 
| command | string | The name of the SQL command without any command details\. | 
| commandText | string |  The actual SQL statement passed in by the user\. For Aurora PostgreSQL, the value is identical to the original SQL statement\. This field is used for all types of records except for connect or disconnect records, in which case the value is null\.  The full SQL text of each statement is visible in the activity stream audit log, including any sensitive data\. However, database user passwords are redacted if Aurora can determine them from the context, such as in the following SQL statement\.  <pre>ALTER ROLE role-name WITH password</pre>   | 
| databaseName | string | The database to which the user connected\. | 
| dbProtocol | string | The database protocol, for example Postgres 3\.0\. | 
| dbUserName | string | The database user with which the client authenticated\. | 
| errorMessage\(version 1\.1 database activity records only\) | string |  If there was any error, this field is populated with the error message that would've been generated by the DB server\. The `errorMessage` value is null for normal statements that didn't result in an error\.  An error is defined as any activity that would produce a client\-visible PostgreSQL error log event at a severity level of `ERROR` or greater\. For more information, see [PostgreSQL Message Severity Levels](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-SEVERITY-LEVELS)\. For example, syntax errors and query cancellations generate an error message\.  Internal PostgreSQL server errors such as background checkpointer process errors do not generate an error message\. However, records for such events are still emitted regardless of the setting of the log severity level\. This prevents attackers from turning off logging to attempt avoiding detection\. See also the `exitCode` field\.  | 
| exitCode | int | A value used for a session exit record\. On a clean exit, this contains the exit code\. An exit code can't always be obtained in some failure scenarios\. Examples are if PostgreSQL does an exit\(\) or if an operator performs a command such as kill \-9\.If there was any error, the `exitCode` field shows the SQL error code, `SQLSTATE`, as listed in [ PostgreSQL Error Codes](https://www.postgresql.org/docs/current/errcodes-appendix.html)\. See also the `errorMessage` field\. | 
| logTime | string | A timestamp as recorded in the auditing code path\. This represents the SQL statement execution end time\. See also the startTime field\. | 
| netProtocol | string | The network communication protocol\. | 
| objectName | string | The name of the database object if the SQL statement is operating on one\. This field is used only where the SQL statement operates on a database object\. If the SQL statement is not operating on an object, this value is null\. | 
| objectType | string | The database object type such as table, index, view, and so on\. This field is used only where the SQL statement operates on a database object\. If the SQL statement is not operating on an object, this value is null\. Valid values include the following:[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/DBActivityStreams.html) | 
| paramList | string | An array of comma\-separated parameters passed to the SQL statement\. If the SQL statement has no parameters, this value is an empty array\. | 
| pid | int | The process ID of the backend process that is allocated for serving the client connection\. | 
| remoteHost | string | Either the client IP address or hostname\. For Aurora PostgreSQL, which one is used depends on the database's log\_hostname parameter setting\. | 
| remotePort | string | The client port number\. | 
| rowCount | int | The number of table rows affected or retrieved by the SQL statement\. This field is used only for SQL statements that are data manipulation language \(DML\) statements\. If the SQL statement is not a DML statement, this value is null\. | 
| serverHost | string | The database server host IP address\. | 
| serverType | string | The database server type, for example PostgreSQL\. | 
| serverVersion | string | The database server version, for example 2\.3\.1 for Aurora PostgreSQL\. | 
| serviceName | string | The name of the service, for example Amazon Aurora PostgreSQL\-Compatible edition\.  | 
| sessionId | int | A pseudo\-unique session identifier\. | 
| startTime\(version 1\.1 database activity records only\) | string |  The time when execution began for the SQL statement\.  To calculate the approximate execution time of the SQL statement, use `logTime – startTime`\. See also the `logTime` field\.  | 
| statementId | int | An identifier for the client's SQL statement\. The counter is at the session level and increments with each SQL statement entered by the client\.  | 
| substatementId | int | An identifier for a SQL substatement\. This value counts the contained substatements for each SQL statement identified by the statementId field\. | 
| type | string | The event type\. Valid values are record or heartbeat\. | 


**databaseActivityEventList fields for Aurora MySQL**  

| Field | Data Type | Description | 
| --- | --- | --- | 
| class | string |  The class of activity event\. Valid values for Aurora MySQL are the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/DBActivityStreams.html)  | 
| clientApplication | string | The application the client used to connect as reported by the client\. The client doesn't have to provide this information, so the value can be null\. | 
| command | string | The general category of the SQL statement\. The values for this field depend on the value of `class`\. The values when `class` is `MAIN` include the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/DBActivityStreams.html) The values when `class` is `AUX` include the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/DBActivityStreams.html)  | 
| commandText | string |  For events with a `class` value of `MAIN`, this field represents the actual SQL statement passed in by the user\. This field is used for all types of records except for connect or disconnect records, in which case the value is null\.  For events with a `class` value of `AUX`, this field contains supplemental information about the objects involved in the event\.  For Aurora MySQL, characters such as quotation marks are preceded by a backslash, representing an escape character\.  The full SQL text of each statement is visible in the audit log, including any sensitive data\. However, database user passwords are redacted if Aurora can determine them from the context, such as in the following SQL statement\.  <pre>mysql> SET PASSWORD = 'my-password';</pre>   | 
| databaseName | string | The database to which the user connected\. | 
| dbProtocol | string | The database protocol\. Currently, this value is always MySQL for Aurora MySQL\. | 
| dbUserName | string | The database user with which the client authenticated\. | 
| endTime\(version 1\.2 database activity records only\) | string |  The time when execution ended for the SQL statement\. It is represented in Coordinated Universal Time \(UTC\) format\. To calculate the execution time of the SQL statement, use `endTime – startTime`\. See also the `startTime` field\.  | 
| errorMessage\(version 1\.1 database activity records only\) | string |  If there was any error, this field is populated with the error message that would've been generated by the DB server\. The `errorMessage` value is null for normal statements that didn't result in an error\.  An error is defined as any activity that would produce a client\-visible MySQL error log event at a severity level of `ERROR` or greater\. For more information, see [The Error Log](https://dev.mysql.com/doc/refman/5.7/en/error-log.html) in the *MySQL Reference Manual*\. For example, syntax errors and query cancellations generate an error message\.  Internal MySQL server errors such as background checkpointer process errors do not generate an error message\. However, records for such events are still emitted regardless of the setting of the log severity level\. This prevents attackers from turning off logging to attempt avoiding detection\. See also the `exitCode` field\.  | 
| exitCode | int | A value used for a session exit record\. On a clean exit, this contains the exit code\. An exit code can't always be obtained in some failure scenarios\. In such cases, this value might be zero or might be blank\. | 
| logTime | string | A timestamp as recorded in the auditing code path\. It is represented in Coordinated Universal Time \(UTC\) format\. For the most accurate way to calculate statement duration, see the startTime and endTime fields\. | 
| netProtocol | string | The network communication protocol\. Currently, this value is always TCP for Aurora MySQL\. | 
| objectName | string | The name of the database object if the SQL statement is operating on one\. This field is used only where the SQL statement operates on a database object\. If the SQL statement isn't operating on an object, this value is blank\. To construct the fully qualified name of the object, combine databaseName and objectName\. If the query involves multiple objects, this field can be a comma\-separated list of names\. | 
| objectType | string | The database object type such as table, index, and so on\. This field is used only where the SQL statement operates on a database object\. If the SQL statement is not operating on an object, this value is null\. Valid values for Aurora MySQL include the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/DBActivityStreams.html)  | 
| paramList | string | This field isn't used for Aurora MySQL and is always null\. | 
| pid | int | The process ID of the backend process that is allocated for serving the client connection\. When the database server is restarted, the pid changes and the counter for the statementId field starts over\. | 
| remoteHost | string | Either the IP address or hostname of the client that issued the SQL statement\. For Aurora MySQL, which one is used depends on the database's skip\_name\_resolve parameter setting\. The value localhost indicates activity from the rdsadmin special user\.  | 
| remotePort | string | The client port number\. | 
| rowCount | int | The number of table rows affected or retrieved by the SQL statement\. This field is used only for SQL statements that are data manipulation language \(DML\) statements\. If the SQL statement is not a DML statement, this value is null\. | 
| serverHost | string | The database server instance identifier\. This value is represented differently for Aurora MySQL than for Aurora PostgreSQL\. Aurora PostgreSQL uses an IP address instead of an identifier\. | 
| serverType | string | The database server type, for example MySQL\. | 
| serverVersion | string | The database server version\. Currently, this value is always MySQL 5\.7\.12 for Aurora MySQL\. | 
| serviceName | string | The name of the service\. Currently, this value is always Amazon Aurora MySQL for Aurora MySQL\. | 
| sessionId | int | A pseudo\-unique session identifier\. | 
| startTime\(version 1\.1 database activity records only\) | string |  The time when execution began for the SQL statement\. It is represented in Coordinated Universal Time \(UTC\) format\. To calculate the execution time of the SQL statement, use `endTime – startTime`\. See also the `endTime` field\.  | 
| statementId | int | An identifier for the client's SQL statement\. The counter increments with each SQL statement entered by the client\. The counter is reset when the DB instance is restarted\. | 
| substatementId | int | An identifier for a SQL substatement\. This value is 1 for events with class MAIN and 2 for events with class AUX\. Use the statementId field to identify all the events generated by the same statement\. | 
| transactionId\(version 1\.2 database activity records only\) | int | An identifier for a transaction\. | 
| type | string | The event type\. Valid values are record or heartbeat\. | 

### Processing an Activity Stream using the AWS SDK<a name="DBActivityStreams.CodeExample"></a>

You can programmatically process an activity stream by using the AWS SDK\. The following are fully functioning Java and Python examples of how you might process the Kinesis data stream\.

------
#### [ Java ]

```
import java.io.ByteArrayInputStream;
import java.io.ByteArrayOutputStream;
import java.io.IOException;
import java.net.InetAddress;
import java.nio.ByteBuffer;
import java.nio.charset.StandardCharsets;
import java.security.NoSuchAlgorithmException;
import java.security.NoSuchProviderException;
import java.security.Security;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.UUID;
import java.util.zip.GZIPInputStream;

import javax.crypto.Cipher;
import javax.crypto.NoSuchPaddingException;
import javax.crypto.spec.SecretKeySpec;

import com.amazonaws.auth.AWSStaticCredentialsProvider;
import com.amazonaws.auth.BasicAWSCredentials;
import com.amazonaws.encryptionsdk.AwsCrypto;
import com.amazonaws.encryptionsdk.CryptoInputStream;
import com.amazonaws.encryptionsdk.jce.JceMasterKey;
import com.amazonaws.services.kinesis.clientlibrary.exceptions.InvalidStateException;
import com.amazonaws.services.kinesis.clientlibrary.exceptions.ShutdownException;
import com.amazonaws.services.kinesis.clientlibrary.exceptions.ThrottlingException;
import com.amazonaws.services.kinesis.clientlibrary.interfaces.IRecordProcessor;
import com.amazonaws.services.kinesis.clientlibrary.interfaces.IRecordProcessorCheckpointer;
import com.amazonaws.services.kinesis.clientlibrary.interfaces.IRecordProcessorFactory;
import com.amazonaws.services.kinesis.clientlibrary.lib.worker.InitialPositionInStream;
import com.amazonaws.services.kinesis.clientlibrary.lib.worker.KinesisClientLibConfiguration;
import com.amazonaws.services.kinesis.clientlibrary.lib.worker.ShutdownReason;
import com.amazonaws.services.kinesis.clientlibrary.lib.worker.Worker;
import com.amazonaws.services.kinesis.clientlibrary.lib.worker.Worker.Builder;
import com.amazonaws.services.kinesis.model.Record;
import com.amazonaws.services.kms.AWSKMS;
import com.amazonaws.services.kms.AWSKMSClientBuilder;
import com.amazonaws.services.kms.model.DecryptRequest;
import com.amazonaws.services.kms.model.DecryptResult;
import com.amazonaws.util.Base64;
import com.amazonaws.util.IOUtils;
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.annotations.SerializedName;
import org.bouncycastle.jce.provider.BouncyCastleProvider;

public class DemoConsumer {

    private static final String STREAM_NAME = "aws-rds-das-[cluster-external-resource-id]";
    private static final String APPLICATION_NAME = "AnyApplication"; //unique application name for dynamo table generation that holds kinesis shard tracking
    private static final String AWS_ACCESS_KEY = "[AWS_ACCESS_KEY_TO_ACCESS_KINESIS]";
    private static final String AWS_SECRET_KEY = "[AWS_SECRET_KEY_TO_ACCESS_KINESIS]";
    private static final String DBC_RESOURCE_ID = "[cluster-external-resource-id]";
    private static final String REGION_NAME = "[region-name]"; //us-east-1, us-east-2...
    private static final BasicAWSCredentials CREDENTIALS = new BasicAWSCredentials(AWS_ACCESS_KEY, AWS_SECRET_KEY);
    private static final AWSStaticCredentialsProvider CREDENTIALS_PROVIDER = new AWSStaticCredentialsProvider(CREDENTIALS);

    private static final AwsCrypto CRYPTO = new AwsCrypto();
    private static final AWSKMS KMS = AWSKMSClientBuilder.standard()
            .withRegion(REGION_NAME)
            .withCredentials(CREDENTIALS_PROVIDER).build();

    class Activity {
        String type;
        String version;
        String databaseActivityEvents;
        String key;
    }

    class ActivityEvent {
        @SerializedName("class") String _class;
        String clientApplication;
        String command;
        String commandText;
        String databaseName;
        String dbProtocol;
        String dbUserName;
        String endTime;
        String errorMessage;
        String exitCode;
        String logTime;
        String netProtocol;
        String objectName;
        String objectType;
        List<String> paramList;
        String pid;
        String remoteHost;
        String remotePort;
        String rowCount;
        String serverHost;
        String serverType;
        String serverVersion;
        String serviceName;
        String sessionId;
        String startTime;
        String statementId;
        String substatementId;
        String transactionId;
        String type;
    }

    class ActivityRecords {
        String type;
        String clusterId;
        String instanceId;
        List<ActivityEvent> databaseActivityEventList;
    }

    static class RecordProcessorFactory implements IRecordProcessorFactory {
        @Override
        public IRecordProcessor createProcessor() {
            return new RecordProcessor();
        }
    }

    static class RecordProcessor implements IRecordProcessor {

        private static final long BACKOFF_TIME_IN_MILLIS = 3000L;
        private static final int PROCESSING_RETRIES_MAX = 10;
        private static final long CHECKPOINT_INTERVAL_MILLIS = 60000L;
        private static final Gson GSON = new GsonBuilder().serializeNulls().create();

        private static final Cipher CIPHER;
        static {
            Security.insertProviderAt(new BouncyCastleProvider(), 1);
            try {
                CIPHER = Cipher.getInstance("AES/GCM/NoPadding", "BC");
            } catch (NoSuchAlgorithmException | NoSuchPaddingException | NoSuchProviderException e) {
                throw new ExceptionInInitializerError(e);
            }
        }

        private long nextCheckpointTimeInMillis;

        @Override
        public void initialize(String shardId) {
        }

        @Override
        public void processRecords(final List<Record> records, final IRecordProcessorCheckpointer checkpointer) {
            for (final Record record : records) {
                processSingleBlob(record.getData());
            }

            if (System.currentTimeMillis() > nextCheckpointTimeInMillis) {
                checkpoint(checkpointer);
                nextCheckpointTimeInMillis = System.currentTimeMillis() + CHECKPOINT_INTERVAL_MILLIS;
            }
        }

        @Override
        public void shutdown(IRecordProcessorCheckpointer checkpointer, ShutdownReason reason) {
            if (reason == ShutdownReason.TERMINATE) {
                checkpoint(checkpointer);
            }
        }

        private void processSingleBlob(final ByteBuffer bytes) {
            try {
                // JSON $Activity
                final Activity activity = GSON.fromJson(new String(bytes.array(), StandardCharsets.UTF_8), Activity.class);

                // Base64.Decode
                final byte[] decoded = Base64.decode(activity.databaseActivityEvents);
                final byte[] decodedDataKey = Base64.decode(activity.key);

                Map<String, String> context = new HashMap<>();
                context.put("aws:rds:dbc-id", DBC_RESOURCE_ID);

                // Decrypt
                final DecryptRequest decryptRequest = new DecryptRequest()
                        .withCiphertextBlob(ByteBuffer.wrap(decodedDataKey)).withEncryptionContext(context);
                final DecryptResult decryptResult = KMS.decrypt(decryptRequest);
                final byte[] decrypted = decrypt(decoded, getByteArray(decryptResult.getPlaintext()));

                // GZip Decompress
                final byte[] decompressed = decompress(decrypted);
                // JSON $ActivityRecords
                final ActivityRecords activityRecords = GSON.fromJson(new String(decompressed, StandardCharsets.UTF_8), ActivityRecords.class);

                // Iterate throught $ActivityEvents
                for (final ActivityEvent event : activityRecords.databaseActivityEventList) {
                    System.out.println(GSON.toJson(event));
                }
            } catch (Exception e) {
                // Handle error.
                e.printStackTrace();
            }
        }

        private static byte[] decompress(final byte[] src) throws IOException {
            ByteArrayInputStream byteArrayInputStream = new ByteArrayInputStream(src);
            GZIPInputStream gzipInputStream = new GZIPInputStream(byteArrayInputStream);
            return IOUtils.toByteArray(gzipInputStream);
        }

        private void checkpoint(IRecordProcessorCheckpointer checkpointer) {
            for (int i = 0; i < PROCESSING_RETRIES_MAX; i++) {
                try {
                    checkpointer.checkpoint();
                    break;
                } catch (ShutdownException se) {
                    // Ignore checkpoint if the processor instance has been shutdown (fail over).
                    System.out.println("Caught shutdown exception, skipping checkpoint." + se);
                    break;
                } catch (ThrottlingException e) {
                    // Backoff and re-attempt checkpoint upon transient failures
                    if (i >= (PROCESSING_RETRIES_MAX - 1)) {
                        System.out.println("Checkpoint failed after " + (i + 1) + "attempts." + e);
                        break;
                    } else {
                        System.out.println("Transient issue when checkpointing - attempt " + (i + 1) + " of " + PROCESSING_RETRIES_MAX + e);
                    }
                } catch (InvalidStateException e) {
                    // This indicates an issue with the DynamoDB table (check for table, provisioned IOPS).
                    System.out.println("Cannot save checkpoint to the DynamoDB table used by the Amazon Kinesis Client Library." + e);
                    break;
                }
                try {
                    Thread.sleep(BACKOFF_TIME_IN_MILLIS);
                } catch (InterruptedException e) {
                    System.out.println("Interrupted sleep" + e);
                }
            }
        }
    }

    private static byte[] decrypt(final byte[] decoded, final byte[] decodedDataKey) throws IOException {
        // Create a JCE master key provider using the random key and an AES-GCM encryption algorithm
        final JceMasterKey masterKey = JceMasterKey.getInstance(new SecretKeySpec(decodedDataKey, "AES"),
                "BC", "DataKey", "AES/GCM/NoPadding");
        try (final CryptoInputStream<JceMasterKey> decryptingStream = CRYPTO.createDecryptingStream(masterKey, new ByteArrayInputStream(decoded));
             final ByteArrayOutputStream out = new ByteArrayOutputStream()) {
            IOUtils.copy(decryptingStream, out);
            return out.toByteArray();
        }
    }

    public static void main(String[] args) throws Exception {
        final String workerId = InetAddress.getLocalHost().getCanonicalHostName() + ":" + UUID.randomUUID();
        final KinesisClientLibConfiguration kinesisClientLibConfiguration =
                new KinesisClientLibConfiguration(APPLICATION_NAME, STREAM_NAME, CREDENTIALS_PROVIDER, workerId);
        kinesisClientLibConfiguration.withInitialPositionInStream(InitialPositionInStream.LATEST);
        kinesisClientLibConfiguration.withRegionName(REGION_NAME);
        final Worker worker = new Builder()
                .recordProcessorFactory(new RecordProcessorFactory())
                .config(kinesisClientLibConfiguration)
                .build();

        System.out.printf("Running %s to process stream %s as worker %s...\n", APPLICATION_NAME, STREAM_NAME, workerId);

        try {
            worker.run();
        } catch (Throwable t) {
            System.err.println("Caught throwable while processing data.");
            t.printStackTrace();
            System.exit(1);
        }
        System.exit(0);
    }

    private static byte[] getByteArray(final ByteBuffer b) {
        byte[] byteArray = new byte[b.remaining()];
        b.get(byteArray);
        return byteArray;
    }
}
```

------
#### [ Python ]

```
import base64
import json
import zlib
import aws_encryption_sdk
from aws_encryption_sdk.internal.crypto import WrappingKey
from aws_encryption_sdk.key_providers.raw import RawMasterKeyProvider
from aws_encryption_sdk.identifiers import WrappingAlgorithm, EncryptionKeyType
import boto3

REGION_NAME = '<region>'                    # us-east-1
RESOURCE_ID = '<external-resource-id>'      # cluster-ABCD123456
STREAM_NAME = 'aws-rds-das-' + RESOURCE_ID  # aws-rds-das-cluster-ABCD123456


class MyRawMasterKeyProvider(RawMasterKeyProvider):
    provider_id = "BC"

    def __new__(cls, *args, **kwargs):
        obj = super(RawMasterKeyProvider, cls).__new__(cls)
        return obj

    def __init__(self, plain_key):
        RawMasterKeyProvider.__init__(self)
        self.wrapping_key = WrappingKey(wrapping_algorithm=WrappingAlgorithm.AES_256_GCM_IV12_TAG16_NO_PADDING,
                                        wrapping_key=plain_key, wrapping_key_type=EncryptionKeyType.SYMMETRIC)

    def _get_raw_key(self, key_id):
        return self.wrapping_key


def decrypt_payload(payload, data_key):
    my_key_provider = MyRawMasterKeyProvider(data_key)
    my_key_provider.add_master_key("DataKey")
    decrypted_plaintext, header = aws_encryption_sdk.decrypt(
        source=payload,
        materials_manager=aws_encryption_sdk.DefaultCryptoMaterialsManager(master_key_provider=my_key_provider))
    return decrypted_plaintext


def decrypt_decompress(payload, key):
    decrypted = decrypt_payload(payload, key)
    return zlib.decompress(decrypted, zlib.MAX_WBITS + 16)


def main():
    session = boto3.session.Session()
    kms = session.client('kms', region_name=REGION_NAME)
    kinesis = session.client('kinesis', region_name=REGION_NAME)

    response = kinesis.describe_stream(StreamName=STREAM_NAME)
    shard_iters = []
    for shard in response['StreamDescription']['Shards']:
        shard_iter_response = kinesis.get_shard_iterator(StreamName=STREAM_NAME, ShardId=shard['ShardId'],
                                                         ShardIteratorType='LATEST')
        shard_iters.append(shard_iter_response['ShardIterator'])

    while len(shard_iters) > 0:
        next_shard_iters = []
        for shard_iter in shard_iters:
            response = kinesis.get_records(ShardIterator=shard_iter, Limit=10000)
            for record in response['Records']:
                record_data = record['Data']
                record_data = json.loads(record_data)
                payload_decoded = base64.b64decode(record_data['databaseActivityEvents'])
                data_key_decoded = base64.b64decode(record_data['key'])
                data_key_decrypt_result = kms.decrypt(CiphertextBlob=data_key_decoded,
                                                      EncryptionContext={'aws:rds:dbc-id': RESOURCE_ID})
                print decrypt_decompress(payload_decoded, data_key_decrypt_result['Plaintext'])
            if 'NextShardIterator' in response:
                next_shard_iters.append(response['NextShardIterator'])
        shard_iters = next_shard_iters


if __name__ == '__main__':
    main()
```

------

## Managing Access to Database Activity Streams<a name="DBActivityStreams.ManagingAccess"></a>

Any user with appropriate AWS Identity and Access Management \(IAM\) role privileges for database activity streams can create, start, stop, and modify the activity stream settings for a DB cluster\. These actions are included in the audit log of the stream\. For best compliance practices, we recommend that you don't provide these privileges to DBAs\.

You set access to database activity streams using IAM policies\. For more information about Aurora authentication, see [Identity and Access Management in Amazon Aurora](UsingWithRDS.IAM.md)\. For more information about creating IAM policies, see [Creating and Using an IAM Policy for IAM Database Access](UsingWithRDS.IAMDBAuth.IAMPolicy.md)\. 

**Example Policy to Allow Configuring Database Activity Streams**  
To give users fine\-grained access to modify activity streams, use the service\-specific operation context keys `rds:StartActivityStream` and `rds:StopActivityStream`  in an IAM policy\. The following IAM policy example allows a user or role to configure activity streams\.  

```
{
   "Version":"2012-10-17",
   "Statement":[
        {
            "Sid":"ConfigureActivityStreams",
            "Effect":"Allow",
            "Action": [
                "rds:StartActivityStream",
                "rds:StopActivityStream"
            ],
            "Resource":"*",
          }
     ]
}
```

**Example Policy to Allow Starting Database Activity Streams**  
The following IAM policy example allows a user or role to start activity streams\.  

```
{
    "Version":"2012-10-17",
    "Statement":[
        {
            "Sid":"AllowStartActivityStreams",
            "Effect":"Allow",
            "Action":"rds:StartActivityStream",
            "Resource":"*"
        }
    ]
}
```

**Example Policy to Allow Stopping Database Activity Streams**  
The following IAM policy example allows a user or role to stop activity streams\.  

```
{
    "Version":"2012-10-17",
    "Statement":[
        {
            "Sid":"AllowStopActivityStreams",
            "Effect":"Allow",
            "Action":"rds:StopActivityStream",
            "Resource":"*"
        }
     ]
}
```

**Example Policy to Deny Starting Database Activity Streams**  
The following IAM policy example prevents a user or role from starting activity streams\.  

```
{
    "Version":"2012-10-17",
    "Statement":[
        {
            "Sid":"DenyStartActivityStreams",
            "Effect":"Deny",
            "Action":"rds:StartActivityStream",
            "Resource":"*"
        }
     ]
}
```

**Example Policy to Deny Stopping Database Activity Streams**  
The following IAM policy example prevents a user or role from stopping activity streams\.  

```
{
    "Version":"2012-10-17",
    "Statement":[
        {
            "Sid":"DenyStopActivityStreams",
            "Effect":"Deny",
            "Action":"rds:StopActivityStream",
            "Resource":"*"
        }
    ]
}
```