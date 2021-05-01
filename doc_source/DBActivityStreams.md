# Using Database Activity Streams with Amazon Aurora<a name="DBActivityStreams"></a>

Monitoring your database activity can help you provide safeguards for your database and help to meet compliance and regulatory requirements\. To monitor database activity with Amazon Aurora, you can use the Database Activity Streams feature\. Database activity streams provide a near real\-time stream of the activity in your relational database\. When you integrate database activity streams with monitoring tools, you can monitor and audit database activity\. 

Beyond external security threats, managed databases need to provide protection against insider risks from database administrators \(DBAs\)\. Database activity streams help protect your databases from internal threats by controlling DBA access to the database activity streams\. Thus, the collection, transmission, storage, and subsequent processing of the stream of database activity is beyond the access of the DBAs that manage the database\. 

A stream of database activity is pushed from Aurora to an Amazon Kinesis data stream that is created on behalf of your Aurora DB cluster\. From Kinesis, AWS services such as Amazon Kinesis Data Firehose and AWS Lambda can consume the activity stream\. Database Activity Streams is a free feature, but Amazon Kinesis charges for its streams\. For more information, see [Amazon Kinesis Data Streams pricing](http://aws.amazon.com/kinesis/data-streams/pricing/)\.

An activity stream can also be consumed by applications for compliance management\. For database activity streams with Aurora PostgreSQL, compliance applications include IBM's Security Guardium, McAfee's Data Center Security Suite, and Imperva's SecureSphere Database Audit and Protection\. Such applications can use the stream to generate alerts and audit your Aurora DB clusters\.

Check the website for the particular application that you want to use with database activity streams\. Confirm support for the specific Aurora DB engine and engine version that you intend to use\. 

Database activity streams have the following limits and requirements:
+ For Aurora PostgreSQL, database activity streams are supported for version 2\.3 or higher and versions 3\.0 or higher\. For PostgreSQL version compatibility, see [Amazon Aurora PostgreSQL releases and engine versions](AuroraPostgreSQL.Updates.20180305.md)\. 
+ For Aurora MySQL, database activity streams are supported for version 2\.08 or higher, which is compatible with MySQL version 5\.7\.
+ You can start a database activity stream on the primary or secondary cluster of an Aurora global database\. For DB engine version requirements, see [Limitations of Amazon Aurora global databases](aurora-global-database.md#aurora-global-database.limitations)\.
+ Database activity streams support the DB instance classes listed for Aurora in [Supported DB engines for DB instance classes](Concepts.DBInstanceClass.md#Concepts.DBInstanceClass.SupportAurora), with some exceptions:
  +  For Aurora PostgreSQL, you only use streams with the db\.r4 or db\.r5 instance classes\. 
  +  For Aurora MySQL, you can't use streams with any of the db\.t2 or db\.t3 instance classes\.
+ Database activity streams aren't supported in the following AWS Regions:
  + China \(Beijing\) Region, `cn-north-1`
  + China \(Ningxia\) Region, `cn-northwest-1`
  + AWS GovCloud \(US\-East\), `us-gov-east-1`
  + AWS GovCloud \(US\-West\), `us-gov-west-1`
+ Database activity streams require use of AWS Key Management Service \(AWS KMS\)\. AWS KMS is required because the activity streams are always encrypted\.
+ Database activity streams aren't supported in Aurora Serverless\.

**Topics**
+ [Network prerequisites for Aurora MySQL Database Activity Streams](DBActivityStreams.Prereqs.md)
+ [Starting a database activity stream](DBActivityStreams.Enabling.md)
+ [Getting the status of an activity stream](DBActivityStreams.Status.md)
+ [Stopping an activity stream](DBActivityStreams.Disabling.md)
+ [Monitoring database activity streams](DBActivityStreams.Monitoring.md)
+ [Managing access to database activity streams](DBActivityStreams.ManagingAccess.md)