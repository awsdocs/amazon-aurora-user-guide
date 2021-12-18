# Overview of Database Activity Streams<a name="DBActivityStreams.Overview"></a>

As an Amazon Aurora database administrator, you need to safeguard your database and meet compliance and regulatory requirements\. One strategy is to integrate database activity streams with your monitoring tools\. In this way, you monitor and set alarms for audit activity in your Amazon Aurora cluster\.

Security threats are both external and internal\. To protect against internal threats, you can control administrator access to data streams by using the Database Activity Streams feature\. DBAs don't have access to the collection, transmission, storage, and processing of the streams\.

**Topics**
+ [How database activity streams work](#DBActivityStreams.Overview.how-they-work)
+ [Asynchronous and synchronous mode](#DBActivityStreams.Overview.sync-mode)
+ [Requirements for database activity streams](#DBActivityStreams.Overview.requirements)

## How database activity streams work<a name="DBActivityStreams.Overview.how-they-work"></a>

Database activity streams provide a near\-real\-time stream of the activity in your DB cluster\. Amazon Aurora pushes activities to an Amazon Kinesis data stream\. The Kinesis stream is created automatically\. From Kinesis, you can configure AWS services such as Amazon Kinesis Data Firehose and AWS Lambda to consume the stream and store the data\.

**Important**  
Database Activity Streams is a free feature, but Amazon Kinesis charges for a data stream\. For more information, see [Amazon Kinesis Data Streams pricing](https://aws.amazon.com/kinesis/data-streams/pricing/)\.

Applications for compliance management can also consume activity streams\. For Aurora PostgreSQL, compliance applications include IBM's Security Guardium and Imperva's SecureSphere Database Audit and Protection\. These applications can use the stream to generate alerts and audit activity on your Aurora DB cluster\.

The following graphic shows an Aurora cluster configured with Amazon Kinesis Data Firehose\.

![\[Database Activity Streams\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-das.png)

## Asynchronous and synchronous mode<a name="DBActivityStreams.Overview.sync-mode"></a>

You can choose to have the database session handle activity events in either of the following modes:
+ **Asynchronous mode** – When a database session generates an activity stream event, the session returns to normal activities immediately\. In the background, the activity stream event is made a durable record\. If an error occurs in the background task, an RDS event is sent\. This event indicates the beginning and end of any time windows where activity stream event records might have been lost\.

  Asynchronous mode favors database performance over the accuracy of the activity stream\. 
**Note**  
 Asynchronous mode is available for both Aurora PostgreSQL and Aurora MySQL\. 
+ **Synchronous mode** – When a database session generates an activity stream event, the session blocks other activities until the event is made durable\. If the event can't be made durable for some reason, the database session returns to normal activities\. However, an RDS event is sent indicating that activity stream records might be lost for some time\. A second RDS event is sent after the system is back to a healthy state\.

  The synchronous mode favors the accuracy of the activity stream over database performance\.
**Note**  
 Synchronous mode is available for Aurora PostgreSQL\. You can't use synchronous mode with Aurora MySQL\. 

## Requirements for database activity streams<a name="DBActivityStreams.Overview.requirements"></a>

In Aurora, database activity streams have the following requirements and limitations\.

**Topics**
+ [Supported Aurora engine versions](#DBActivityStreams.Overview.requirements.version)
+ [Supported DB instance classes](#DBActivityStreams.Overview.requirements.classes)
+ [AWS Region support](#DBActivityStreams.Overview.requirements.Regions)
+ [Miscellaneous requirements](#DBActivityStreams.Overview.requirements.misc)

### Supported Aurora engine versions<a name="DBActivityStreams.Overview.requirements.version"></a>

For Aurora PostgreSQL, database activity streams are supported for the following versions:
+ All 13 versions
+ All 12 versions
+ Version 11\.6 and higher 11 versions
+ Version 10\.11 and higher 10 versions

For more information about Aurora PostgreSQL versions, see [Amazon Aurora PostgreSQL releases and engine versions](AuroraPostgreSQL.Updates.20180305.md)\.

For Aurora MySQL, database activity streams are supported for version 2\.08 or higher, which is compatible with MySQL version 5\.7\.

**Note**  
Database activity streams aren't supported in Aurora Serverless\.

### Supported DB instance classes<a name="DBActivityStreams.Overview.requirements.classes"></a>

For Aurora MySQL, you can use database activity streams with the following DB instance classes:
+ db\.r6g
+ db\.r5
+ db\.r4
+ db\.r3
+ db\.x2g

For Aurora PostgreSQL, you can use database activity streams with the following DB instance classes:
+ db\.r6g
+ db\.r5
+ db\.r4
+ db\.x2g

### AWS Region support<a name="DBActivityStreams.Overview.requirements.Regions"></a>

Database activity streams are supported in all AWS Regions except the following:
+ Asia Pacific \(Jakarta\) Region, `ap-southeast-3`
+ China \(Beijing\) Region, `cn-north-1`
+ China \(Ningxia\) Region, `cn-northwest-1`
+ AWS GovCloud \(US\-East\), `us-gov-east-1`
+ AWS GovCloud \(US\-West\), `us-gov-west-1`

### Miscellaneous requirements<a name="DBActivityStreams.Overview.requirements.misc"></a>
+ Database activity streams require use of AWS Key Management Service \(AWS KMS\)\. AWS KMS is required because the activity streams are always encrypted\.
+ Database activity streams require use of Amazon Kinesis\.