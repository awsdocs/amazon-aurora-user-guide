# Overview of Database Activity Streams<a name="DBActivityStreams.Overview"></a>

As an Amazon Aurora database administrator, you need to safeguard your database and meet compliance and regulatory requirements\. One strategy is to integrate database activity streams with your monitoring tools\. In this way, you monitor and set alarms for auditing activity in your Amazon Aurora cluster\.

Security threats are both external and internal\. To protect against internal threats, you can control administrator access to data streams by configuring the Database Activity Streams feature\. DBAs don't have access to the collection, transmission, storage, and processing of the streams\.

**Topics**
+ [How database activity streams work](#DBActivityStreams.Overview.how-they-work)
+ [Asynchronous and synchronous mode for database activity streams](#DBActivityStreams.Overview.sync-mode)
+ [Requirements for database activity streams](#DBActivityStreams.Overview.requirements)
+ [Region and version availability](#DBActivityStreams.Overview.Availability)
+ [Supported DB instance classes for database activity streams](#DBActivityStreams.Overview.requirements.classes)

## How database activity streams work<a name="DBActivityStreams.Overview.how-they-work"></a>

In Amazon Aurora, you start a database activity stream at the cluster level\. All DB instances within your cluster have database activity streams enabled\.

Your Aurora DB cluster pushes activities to an Amazon Kinesis data stream in near real time\. The Kinesis stream is created automatically\. From Kinesis, you can configure AWS services such as Amazon Kinesis Data Firehose and AWS Lambda to consume the stream and store the data\.

**Important**  
Use of the Database Activity Streams feature in Amazon Aurora is free, but Amazon Kinesis charges for a data stream\. For more information, see [Amazon Kinesis Data Streams pricing](https://aws.amazon.com/kinesis/data-streams/pricing/)\.

If you use an Aurora global database, start a database activity stream on each DB cluster separately\. Each cluster delivers audit data to its own Kinesis stream within its own AWS Region\. The activity streams don't operate differently during a failover\. They continue to audit your global database as usual\.

You can configure applications for compliance management to consume database activity streams\. For Aurora PostgreSQL, compliance applications include IBM's Security Guardium and Imperva's SecureSphere Database Audit and Protection\. These applications can use the stream to generate alerts and audit activity on your Aurora DB cluster\.

The following graphic shows an Aurora DB cluster configured with Amazon Kinesis Data Firehose\.

![\[Database Activity Streams\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-das.png)

## Asynchronous and synchronous mode for database activity streams<a name="DBActivityStreams.Overview.sync-mode"></a>

You can choose to have the database session handle database activity events in either of the following modes:
+ **Asynchronous mode** – When a database session generates an activity stream event, the session returns to normal activities immediately\. In the background, the activity stream event is made a durable record\. If an error occurs in the background task, an RDS event is sent\. This event indicates the beginning and end of any time windows where activity stream event records might have been lost\.

  Asynchronous mode favors database performance over the accuracy of the activity stream\. 
**Note**  
 Asynchronous mode is available for both Aurora PostgreSQL and Aurora MySQL\. 
+ **Synchronous mode** – When a database session generates an activity stream event, the session blocks other activities until the event is made durable\. If the event can't be made durable for some reason, the database session returns to normal activities\. However, an RDS event is sent indicating that activity stream records might be lost for some time\. A second RDS event is sent after the system is back to a healthy state\.

  The synchronous mode favors the accuracy of the activity stream over database performance\.
**Note**  
 Synchronous mode is available for Aurora PostgreSQL\. You can't use synchronous mode with Aurora MySQL\. 

## Requirements for database activity streams<a name="DBActivityStreams.Overview.requirements"></a>

In Aurora, database activity streams have the following requirements and limitations:
+ Amazon Kinesis is required for database activity streams\.
+ AWS Key Management Service \(AWS KMS\) is required for database activity streams because they are always encrypted\.
+ Applying additional encryption to your Amazon Kinesis data stream is incompatible with database activity streams, which are already encrypted with your AWS KMS key\.
+ Start your database activity stream at the DB cluster level\. If you add a DB instance to your cluster, you don't need to start an activity stream on the instance: it is audited automatically\.
+ In an Aurora global database, make sure to start an activity stream on each DB cluster separately\. Each cluster delivers audit data to its own Kinesis stream within its own AWS Region\.
+ In Aurora PostgreSQL, make sure to stop database activity stream before an upgrade\. You can start the database activity stream after the upgrade completes\.

## Region and version availability<a name="DBActivityStreams.Overview.Availability"></a>

Feature availability and support varies across specific versions of each Aurora database engine, and across AWS Regions\. For more information on version and Region availability with Aurora and database activity streams, see [Database activity streams in Aurora](Concepts.Aurora_Fea_Regions_DB-eng.Feature.DBActivityStreams.md)\. 

## Supported DB instance classes for database activity streams<a name="DBActivityStreams.Overview.requirements.classes"></a>

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