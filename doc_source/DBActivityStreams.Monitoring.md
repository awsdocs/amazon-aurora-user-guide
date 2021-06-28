# Monitoring database activity streams<a name="DBActivityStreams.Monitoring"></a>

Database activity streams monitor and report activities\. The stream of activity is collected and transmitted to Amazon Kinesis\. From Kinesis, you can monitor the activity stream, or other services and applications can consume the activity stream for further analysis\. You can find the underlying Kinesis stream name by using the AWS CLI command `describe-db-clusters` or the RDS API `DescribeDBClusters` operation\.

Aurora manages the Kinesis stream for you as follows:
+ Aurora creates the Kinesis stream automatically with a 24\-hour retention period\. 
+  Aurora scales the Kinesis stream if necessary\. 
+  If you stop the database activity stream or delete the DB cluster, Aurora deletes the Kinesis stream\. 

The following categories of activity are monitored and put in the activity stream audit log:
+ **SQL commands** – All SQL commands are audited, and also prepared statements, built\-in functions, and functions in PL/SQL\. Calls to stored procedures are audited\. Any SQL statements issued inside stored procedures or functions are also audited\.
+ **Other database information** – Activity monitored includes the full SQL statement, the row count of affected rows from DML commands, accessed objects, and the unique database name\. For Aurora PostgreSQL, database activity streams also monitor the bind variables and stored procedure parameters\. 
**Important**  
The full SQL text of each statement is visible in the activity stream audit log, including any sensitive data\. However, database user passwords are redacted if Aurora can determine them from the context, such as in the following SQL statement\.   

  ```
  ALTER ROLE role-name WITH password
  ```
+ **Connection information** – Activity monitored includes session and network information, the server process ID, and exit codes\.

If an activity stream has a failure while monitoring your DB instance, you are notified through RDS events\.

**Topics**
+ [Accessing an activity stream from Kinesis](#DBActivityStreams.KinesisAccess)
+ [Audit log contents and examples](#DBActivityStreams.AuditLog)
+ [Processing a database activity stream using the AWS SDK](#DBActivityStreams.CodeExample)

## Accessing an activity stream from Kinesis<a name="DBActivityStreams.KinesisAccess"></a>

When you enable an activity stream for a DB cluster, a Kinesis stream is created for you\. From Kinesis, you can monitor your database activity in real time\. To further analyze database activity, you can connect your Kinesis stream to consumer applications\. You can also connect the stream to compliance management applications such as IBM's Security Guardium, McAfee's Data Center Security Suite, or Imperva's SecureSphere Database Audit and Protection\.

**To access an activity stream from Kinesis**

1. Open the Kinesis console at [https://console\.aws\.amazon\.com/kinesis](https://console.aws.amazon.com/kinesis)\.

1. Choose your activity stream from the list of Kinesis streams\.

   An activity stream's name includes the prefix `aws-rds-das-cluster-` followed by the resource ID of the DB cluster\. The following is an example\. 

   ```
   aws-rds-das-cluster-NHVOV4PCLWHGF52NP
   ```

   To use the Amazon RDS console to find the resource ID for the DB cluster, choose your DB cluster from the list of databases, and then choose the **Configuration** tab\.

   To use the AWS CLI to find the full Kinesis stream name for an activity stream, use a [describe\-db\-clusters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html) CLI request and note the value of `ActivityStreamKinesisStreamName` in the response\.

1. Choose **Monitoring** to begin observing the database activity\.

For more information about using Amazon Kinesis, see [What Is Amazon Kinesis Data Streams?](https://docs.aws.amazon.com/streams/latest/dev/introduction.html)\.

## Audit log contents and examples<a name="DBActivityStreams.AuditLog"></a>

Monitored events are represented in the database activity stream as JSON strings\. The structure consists of a JSON object containing a `DatabaseActivityMonitoringRecord`, which in turn contains a `databaseActivityEventList` array of activity events\. 

**Topics**
+ [Examples of an audit log for an activity stream](#DBActivityStreams.AuditLog.Examples)
+ [DatabaseActivityMonitoringRecords JSON object](#DBActivityStreams.AuditLog.DatabaseActivityMonitoringRecords)
+ [databaseActivityEvents JSON Object](#DBActivityStreams.AuditLog.databaseActivityEvents)
+ [databaseActivityEventList JSON array](#DBActivityStreams.AuditLog.databaseActivityEventList)

### Examples of an audit log for an activity stream<a name="DBActivityStreams.AuditLog.Examples"></a>

Following are sample decrypted JSON audit logs of activity event records\.

**Example Activity event record of an Aurora PostgreSQL CONNECT SQL statement**  
Following is an activity event record of a login with the use of a `CONNECT` SQL statement \(`command`\) by a psql client \(`clientApplication`\)\.  

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

**Example Activity event record of an Aurora MySQL CONNECT SQL statement**  
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

**Example Activity event record of an Aurora PostgreSQL CREATE TABLE statement**  
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

**Example Activity event record of an Aurora MySQL CREATE TABLE statement**  
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

**Example Activity event record of an Aurora PostgreSQL SELECT statement**  
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

**Example Activity event record of an Aurora MySQL SELECT statement**  
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

### DatabaseActivityMonitoringRecords JSON object<a name="DBActivityStreams.AuditLog.DatabaseActivityMonitoringRecords"></a>

The database activity event records are in a JSON object that contains the following information\.


****  

| JSON Field | Data Type | Description | 
| --- | --- | --- | 
|  `type`  | string |  The type of JSON record\. The value is `DatabaseActivityMonitoringRecords`\.  | 
| version | string | The version of the database activity monitoring records\. The version of the generated database activity records depends on the engine version of the DB cluster: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/DBActivityStreams.Monitoring.html)All of the following fields are in both version 1\.0 and version 1\.1 except where specifically noted\. | 
|  [databaseActivityEvents](#DBActivityStreams.AuditLog.databaseActivityEvents)  | string |  A JSON object containing the activity events\.  | 
| key | string | An encryption key you use to decrypt the [databaseActivityEventList](#DBActivityStreams.AuditLog.databaseActivityEventList) databaseActivityEventList JSON array\. | 

### databaseActivityEvents JSON Object<a name="DBActivityStreams.AuditLog.databaseActivityEvents"></a>

The `databaseActivityEvents` JSON object contains the following information\.

#### Top\-level fields in JSON record<a name="DBActivityStreams.AuditLog.topLevel"></a>

 Each event in the audit log is wrapped inside a record in JSON format\. This record contains the following fields\. 

**type**  
 This field always has the value `DatabaseActivityMonitoringRecords`\. 

**version**  
 This field represents the version of the database activity stream data protocol or contract\. It defines which fields are available\.  
Version 1\.0 represents the original data activity streams support for Aurora PostgreSQL versions 10\.7 and 11\.4\. Version 1\.1 represents the data activity streams support for Aurora PostgreSQL versions 10\.10 and higher and Aurora PostgreSQL 11\.5 and higher\. Version 1\.1 includes the additional fields `errorMessage` and `startTime`\. Version 1\.2 represents the data activity streams support for Aurora MySQL 2\.08 and higher\. Version 1\.2 includes the additional fields `endTime` and `transactionId`\.

**databaseActivityEvents**  
 An encrypted string representing one or more activity events\. It's represented as a base64 byte array\. When you decrypt the string, the result is a record in JSON format with fields as shown in the examples in this section\.

**key**  
 The encrypted data key used to encrypt the `databaseActivityEvents` string\. This is the same AWS KMS customer master key \(CMK\) that you provided when you started the database activity stream\.

 The following example shows the format of this record\.s

```
{
  "type":"DatabaseActivityMonitoringRecords",
  "version":"1.1",
  "databaseActivityEvents":"encrypted audit records",
  "key":"encrypted key"
}
```

Take the following steps to decrypt the contents of the `databaseActivityEvents` field:

1.  Decrypt the value in the `key` JSON field using the AWS KMS CMK you provided when starting database activity stream\. Doing so returns the data encryption key in clear text\. 

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

### databaseActivityEventList JSON array<a name="DBActivityStreams.AuditLog.databaseActivityEventList"></a>

The audit log payload is an encrypted `databaseActivityEventList` JSON array\. The following tables lists alphabetically the fields for each activity event in the decrypted `DatabaseActivityEventList` array of an audit log\. The fields differ depending on whether you use Aurora PostgreSQL or Aurora MySQL\. Consult the table that applies to your database engine\.

**Important**  
The event structure is subject to change\. Aurora might add new fields to activity events in the future\. In applications that parse the JSON data, make sure that your code can ignore or take appropriate actions for unknown field names\. 


**databaseActivityEventList fields for Aurora PostgreSQL**  

| Field | Data Type | Description | 
| --- | --- | --- | 
| class | string |  The class of activity event\. Valid values for Aurora PostgreSQL are the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/DBActivityStreams.Monitoring.html)  | 
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
| objectType | string | The database object type such as table, index, view, and so on\. This field is used only where the SQL statement operates on a database object\. If the SQL statement is not operating on an object, this value is null\. Valid values include the following:[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/DBActivityStreams.Monitoring.html) | 
| paramList | string | An array of comma\-separated parameters passed to the SQL statement\. If the SQL statement has no parameters, this value is an empty array\. | 
| pid | int | The process ID of the backend process that is allocated for serving the client connection\. | 
| remoteHost | string | Either the client IP address or hostname\. For Aurora PostgreSQL, which one is used depends on the database's log\_hostname parameter setting\. | 
| remotePort | string | The client port number\. | 
| rowCount | int | The number of rows returned by the SQL statement\. For example, if a SELECT statement returns 10 rows, rowCount is 10\. For INSERT or UPDATE statements, rowCount is 0\. | 
| serverHost | string | The database server host IP address\. | 
| serverType | string | The database server type, for example PostgreSQL\. | 
| serverVersion | string | The database server version, for example 2\.3\.1 for Aurora PostgreSQL\. | 
| serviceName | string | The name of the service, for example Amazon Aurora PostgreSQL\-Compatible edition\.  | 
| sessionId | int | A pseudo\-unique session identifier\. | 
| sessionId | int | A pseudo\-unique session identifier\. | 
| startTime\(version 1\.1 database activity records only\) | string |  The time when execution began for the SQL statement\.  To calculate the approximate execution time of the SQL statement, use `logTime - startTime`\. See also the `logTime` field\.  | 
| statementId | int | An identifier for the client's SQL statement\. The counter is at the session level and increments with each SQL statement entered by the client\.  | 
| substatementId | int | An identifier for a SQL substatement\. This value counts the contained substatements for each SQL statement identified by the statementId field\. | 
| type | string | The event type\. Valid values are record or heartbeat\. | 


**databaseActivityEventList fields for Aurora MySQL**  

| Field | Data Type | Description | 
| --- | --- | --- | 
| class | string |  The class of activity event\. Valid values for Aurora MySQL are the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/DBActivityStreams.Monitoring.html)  | 
| clientApplication | string | The application the client used to connect as reported by the client\. The client doesn't have to provide this information, so the value can be null\. | 
| command | string |  The general category of the SQL statement\. The values for this field depend on the value of `class`\. The values when `class` is `MAIN` include the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/DBActivityStreams.Monitoring.html) The values when `class` is `AUX` include the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/DBActivityStreams.Monitoring.html)  | 
| commandText | string |  For events with a `class` value of `MAIN`, this field represents the actual SQL statement passed in by the user\. This field is used for all types of records except for connect or disconnect records, in which case the value is null\.  For events with a `class` value of `AUX`, this field contains supplemental information about the objects involved in the event\.  For Aurora MySQL, characters such as quotation marks are preceded by a backslash, representing an escape character\.  The full SQL text of each statement is visible in the audit log, including any sensitive data\. However, database user passwords are redacted if Aurora can determine them from the context, such as in the following SQL statement\.  <pre>mysql> SET PASSWORD = 'my-password';</pre>   | 
| databaseName | string | The database to which the user connected\. | 
| dbProtocol | string | The database protocol\. Currently, this value is always MySQL for Aurora MySQL\. | 
| dbUserName | string | The database user with which the client authenticated\. | 
| endTime\(version 1\.2 database activity records only\) | string |  The time when execution ended for the SQL statement\. It is represented in Coordinated Universal Time \(UTC\) format\. To calculate the execution time of the SQL statement, use `endTime - startTime`\. See also the `startTime` field\.  | 
| errorMessage\(version 1\.1 database activity records only\) | string |  If there was any error, this field is populated with the error message that would've been generated by the DB server\. The `errorMessage` value is null for normal statements that didn't result in an error\.  An error is defined as any activity that would produce a client\-visible MySQL error log event at a severity level of `ERROR` or greater\. For more information, see [The Error Log](https://dev.mysql.com/doc/refman/5.7/en/error-log.html) in the *MySQL Reference Manual*\. For example, syntax errors and query cancellations generate an error message\.  Internal MySQL server errors such as background checkpointer process errors do not generate an error message\. However, records for such events are still emitted regardless of the setting of the log severity level\. This prevents attackers from turning off logging to attempt avoiding detection\. See also the `exitCode` field\.  | 
| exitCode | int | A value used for a session exit record\. On a clean exit, this contains the exit code\. An exit code can't always be obtained in some failure scenarios\. In such cases, this value might be zero or might be blank\. | 
| logTime | string | A timestamp as recorded in the auditing code path\. It is represented in Coordinated Universal Time \(UTC\) format\. For the most accurate way to calculate statement duration, see the startTime and endTime fields\. | 
| netProtocol | string | The network communication protocol\. Currently, this value is always TCP for Aurora MySQL\. | 
| objectName | string | The name of the database object if the SQL statement is operating on one\. This field is used only where the SQL statement operates on a database object\. If the SQL statement isn't operating on an object, this value is blank\. To construct the fully qualified name of the object, combine databaseName and objectName\. If the query involves multiple objects, this field can be a comma\-separated list of names\. | 
| objectType | string |  The database object type such as table, index, and so on\. This field is used only where the SQL statement operates on a database object\. If the SQL statement is not operating on an object, this value is null\. Valid values for Aurora MySQL include the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/DBActivityStreams.Monitoring.html)  | 
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
| startTime\(version 1\.1 database activity records only\) | string |  The time when execution began for the SQL statement\. It is represented in Coordinated Universal Time \(UTC\) format\. To calculate the execution time of the SQL statement, use `endTime - startTime`\. See also the `endTime` field\.  | 
| statementId | int | An identifier for the client's SQL statement\. The counter increments with each SQL statement entered by the client\. The counter is reset when the DB instance is restarted\. | 
| substatementId | int | An identifier for a SQL substatement\. This value is 1 for events with class MAIN and 2 for events with class AUX\. Use the statementId field to identify all the events generated by the same statement\. | 
| transactionId\(version 1\.2 database activity records only\) | int | An identifier for a transaction\. | 
| type | string | The event type\. Valid values are record or heartbeat\. | 

## Processing a database activity stream using the AWS SDK<a name="DBActivityStreams.CodeExample"></a>

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
from aws_encryption_sdk import CommitmentPolicy
from aws_encryption_sdk.internal.crypto import WrappingKey
from aws_encryption_sdk.key_providers.raw import RawMasterKeyProvider
from aws_encryption_sdk.identifiers import WrappingAlgorithm, EncryptionKeyType
import boto3

REGION_NAME = '<region>'                    # us-east-1
RESOURCE_ID = '<external-resource-id>'      # cluster-ABCD123456
STREAM_NAME = 'aws-rds-das-' + RESOURCE_ID  # aws-rds-das-cluster-ABCD123456

enc_client = aws_encryption_sdk.EncryptionSDKClient(commitment_policy=CommitmentPolicy.REQUIRE_ENCRYPT_ALLOW_DECRYPT)

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
    decrypted_plaintext, header = enc_client.decrypt(
        source=payload,
        materials_manager=aws_encryption_sdk.materials_managers.default.DefaultCryptoMaterialsManager(master_key_provider=my_key_provider))
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
                print decrypt_decompress((payload_decoded, data_key_decrypt_result['Plaintext']))
            if 'NextShardIterator' in response:
                next_shard_iters.append(response['NextShardIterator'])
        shard_iters = next_shard_iters


if __name__ == '__main__':
    main()
```

------