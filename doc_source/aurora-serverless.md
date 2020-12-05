# Using Amazon Aurora Serverless<a name="aurora-serverless"></a>

 Amazon Aurora Serverless is an on\-demand, autoscaling configuration for Amazon Aurora\. Unlike Aurora *provisioned DB clusters* for which you manually manage capacity, an *Aurora Serverless DB cluster* is a DB cluster that scales compute capacity up and down based on your application's needs\. Aurora Serverless provides a relatively simple, cost\-effective option for infrequent, intermittent, or unpredictable workloads\. It is cost\-effective because it automatically starts up, scales compute capacity to match your application's usage, and shuts down when it's not in use\. To learn more about pricing, see [Serverless Pricing](https://aws.amazon.com/rds/aurora/pricing/) under **MySQL\-Compatible Edition** or **PostgreSQL\-Compatible Edition** on the Amazon Aurora Pricing page\. 

 Aurora Serverless clusters have the same kind of high\-capacity, distributed, and highly available storage volume that is used by provisioned DB clusters\. The cluster volume for an Aurora Serverless cluster is always encrypted\. You can choose the encryption key, but you can't disable encryption\. That means that you can perform the same operations on an Aurora Serverless that are allowed for encrypted snapshots, but you can't perform operations that aren't allowed on such snapshots\. For more information, see [Aurora Serverless and snapshots](aurora-serverless.how-it-works.md#aurora-serverless.snapshots)\. 

**Topics**
+ [Advantages of Aurora Serverless](#aurora-serverless.advantages)
+ [Use cases for Aurora Serverless](#aurora-serverless.use-cases)
+ [Limitations of Aurora Serverless](#aurora-serverless.limitations)
+ [Requirements of Aurora Serverless](#aurora-serverless.requirements)
+ [Using TLS/SSL with Aurora Serverless](#aurora-serverless.tls)
+ [How Aurora Serverless works](aurora-serverless.how-it-works.md)
+ [Creating an Aurora Serverless DB cluster](aurora-serverless.create.md)
+ [Restoring an Aurora Serverless DB cluster](aurora-serverless.restorefromsnapshot.md)
+ [Modifying an Aurora Serverless DB cluster](aurora-serverless.modifying.md)
+ [Scaling Aurora Serverless DB cluster capacity manually](aurora-serverless.setting-capacity.md)
+ [Viewing Aurora Serverless DB clusters](aurora-serverless.viewing.md)
+ [Aurora Serverless versions](aurora-serverless.relnotes.md)
+ [Using the Data API for Aurora Serverless](data-api.md)
+ [Logging Data API calls with AWS CloudTrail](logging-using-cloudtrail-data-api.md)
+ [Using the query editor for Aurora Serverless](query-editor.md)

## Advantages of Aurora Serverless<a name="aurora-serverless.advantages"></a>

Aurora Serverless provides the following advantages:
+ Simpler than provisioned – Aurora Serverless removes much of the complexity of managing DB instances and capacity\.
+ Scalable – Aurora Serverless seamlessly scales compute and memory capacity as needed, with no disruption to client connections\.
+ Cost\-effective – When you use Aurora Serverless, you pay only for the database resources that you consume, on a per\-second basis\.
+ Highly available storage – Aurora Serverless uses the same fault\-tolerant, distributed storage system with six\-way replication as Aurora to protect against data loss\.

## Use cases for Aurora Serverless<a name="aurora-serverless.use-cases"></a>

Aurora Serverless is designed for the following use cases:
+ Infrequently used applications – You have an application that is only used for a few minutes several times per day or week, such as a low\-volume blog site\. With Aurora Serverless, you pay for only the database resources that you consume on a per\-second basis\.
+ New applications – You're deploying a new application and you're unsure about the instance size you need\. By using Aurora Serverless, you can create a database endpoint and have the database autoscale to the capacity requirements of your application\.
+ Variable workloads – You're running a lightly used application, with peaks of 30 minutes to several hours a few times each day, or several times per year\. Examples are applications for human resources, budgeting, and operational reporting applications\. With Aurora Serverless, you no longer need to provision for peak or average capacity\.
+ Unpredictable workloads – You're running daily workloads that have sudden and unpredictable increases in activity\. An example is a traffic site that sees a surge of activity when it starts raining\. With Aurora Serverless, your database autoscales capacity to meet the needs of the application's peak load and scales back down when the surge of activity is over\. 
+ Development and test databases – Your developers use databases during work hours but don't need them on nights or weekends\. With Aurora Serverless, your database automatically shuts down when it's not in use\.
+ Multi\-tenant applications – With Aurora Serverless, you don't have to individually manage database capacity for each application in your fleet\. Aurora Serverless manages individual database capacity for you\. 

## Limitations of Aurora Serverless<a name="aurora-serverless.limitations"></a>

The following limitations apply to Aurora Serverless:
+ Aurora Serverless is available in certain AWS Regions and for specific Aurora MySQL and Aurora PostgreSQL versions only\. For more information, see [Aurora Serverless](Concepts.AuroraFeaturesRegionsDBEngines.grids.md#Concepts.Aurora_Fea_Regions_DB-eng.Feature.Serverless)\. 
+ Aurora Serverless doesn't support the following features:
  + Aurora cloning
  + Aurora global database
  + Aurora multi\-master clusters
  + Aurora Replicas
  + AWS Identity and Access Management \(IAM\) database authentication
  + Backtracking in Aurora 
  + Database Activity Streams \(DAS\)
  + Performance Insights
+ Connections to an Aurora Serverless DB cluster are closed automatically if held open for longer than one day\.
+ Aurora MySQL\-compatible and Aurora PostgreSQL\-compatible Serverless DB clusters have the following limitations:
  + Exporting Aurora Serverless snapshots to Amazon S3 buckets\.
  + Saving data to text files in Amazon S3\.
  + Loading text file data to Aurora MySQL Serverless from Amazon S3\. However, you can load data to Aurora PostgreSQL Serverless from Amazon S3 by using the `aws_s3` extension with the `aws_s3.table_import_from_s3` function and the `credentials` parameter\. For more information, see [Importing Amazon S3 data into an Aurora PostgreSQL DB cluster](AuroraPostgreSQL.Migrating.md#USER_PostgreSQL.S3Import)\.
+ Aurora MySQL\-compatible Aurora Serverless DB clusters don't support the following:
  + Invoking AWS Lambda functions from within your Aurora MySQL DB cluster\. However, AWS Lambda functions can make calls to your Aurora MySQL Serverless DB cluster\.
  + Restoring a snapshot from a non\-Aurora MySQL or non\-Amazon RDS MySQL DB instance\.
  + Replicating data using binlog\-based replication, regardless of whether the Aurora MySQL\-based Aurora Serverless DB cluster is the source or the target of the replication\. To replicate data into an Aurora Serverless DB cluster from a MySQL DB instance outside Amazon Aurora such as one running on Amazon EC2, we recommend that you consider using AWS Database Migration Service\. For more information, see [AWS Database Migration Service User Guide](https://docs.aws.amazon.com/dms/latest/userguide/)\. 
+ Aurora PostgreSQL\-compatible Aurora Serverless DB clusters have the following limitations:
  + Outbound communications such as those enabled by Amazon RDS PostgreSQL extensions are not supported\. For example, you can't access external data with the `postgres_fdw/dblink` extension\. For more information about Amazon RDS PostgreSQL extensions, see [PostgreSQL on Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html#PostgreSQL.Concepts.General.FeatureSupport.Extensions.101x)\. 
  + Currently, certain SQL queries and commands, such as cursors with hold \(`DECLARE name ... CURSOR WITH HOLD FOR query`\); session\-level advisory locks; temporary relations; and asynchronous notifications \(`LISTEN`, `NOTIFY` commands\) prevent scaling and are not recommended\. For more information, see [Autoscaling for Aurora Serverless](aurora-serverless.how-it-works.md#aurora-serverless.how-it-works.auto-scaling)\. 

## Requirements of Aurora Serverless<a name="aurora-serverless.requirements"></a>

When you create an Aurora Serverless DB cluster, be sure you pay attention to the following requirements:
+ You must use specific port numbers for each DB engine:
  + Aurora MySQL – `3306`
  + Aurora PostgreSQL – `5432` 
+ You must create your Aurora Serverless DB cluster in an Amazon Virtual Private Cloud \(Amazon VPC\)\. Aurora Serverless DB clusters are accessible only from an Amazon VPC and can't use a public IP address\. 
+ You can't give an Aurora Serverless DB cluster a public IP address\. You can access an Aurora Serverless DB cluster only from within a virtual private cloud \(VPC\) based on the Amazon VPC service\.
+ You must create two AWS PrivateLink endpoints for each Aurora Serverless DB cluster\. If you reach the limit for AWS PrivateLink endpoints within your VPC, you can't create any more Aurora Serverless clusters in that VPC\. For information about checking and changing the limits on endpoints within a VPC, see [Amazon VPC limits](https://docs.aws.amazon.com/vpc/latest/userguide/amazon-vpc-limits.html)\. 
+ You must create subnets in different Availability Zones for the DB subnet group that you use for your Aurora Serverless DB cluster\. In other words, you can't have more than one subnet in the same Availability Zone\.
+ Changes to a subnet group used by an Aurora Serverless DB cluster are not applied to the cluster\.
+ You can access an Aurora Serverless DB cluster from AWS Lambda\. To do so, you must configure your Lambda function to run in the same VPC as your Aurora Serverless DB cluster\. For more information about working with AWS Lambda, see [Configuring a Lambda function to access resources in an Amazon VPC](https://docs.aws.amazon.com/lambda/latest/dg/vpc.html) in the *AWS Lambda Developer Guide*\. 

## Using TLS/SSL with Aurora Serverless<a name="aurora-serverless.tls"></a>

 You can connect to Aurora Serverless clusters using the Transport Layer Security/Secure Sockets Layer \(TLS/SSL\) protocol\. To do so, you use the same general procedure as described in [Connecting to an Amazon Aurora DB cluster](Aurora.Connecting.md)\. 

**Note**  
With Aurora Serverless clusters, you don't need to download and use Amazon RDS SSL/TLS certificates\. Instead, use certificates from the AWS Certificate Manager \(ACM\)\. For more information, see the *[AWS Certificate Manager User Guide](https://docs.aws.amazon.com/acm/latest/userguide/)*\.

 You can ensure that your session uses TLS between your client and the Aurora Serverless VPC endpoint\. To do so, specify the requirement on the client side with the `--ssl-mode` parameter\. SSL session variables are not set for SSL connections to an Aurora Serverless DB cluster\. 

 Aurora Serverless supports TLS protocol version 1\.0, 1\.1, and 1\.2\. However, you don't need to configure an Aurora Serverless database for TLS\. In particular, don't use the `REQUIRE` clause on your database user privileges for SSL\. Doing so prevents that user from connecting\. 

 By default, client programs establish an encrypted connection with Aurora Serverless, with further control available through the `--ssl-mode` option\. From the client side, Aurora Serverless supports all SSL modes\. 

**Note**  
 TLS support for Aurora Serverless clusters currently isn't available in the China \(Beijing\) AWS Region\. 

 For the `mysql` and `psql` client, the SSL modes are the following: 

**PREFERRED**  
 SSL is the first choice, but it isn't required\. 

**DISABLED**  
 No SSL is allowed\. 

**REQUIRED**  
 Enforce SSL\. 

**VERIFY\_CA**  
 Enforce SSL and verify the certificate authority \(CA\)\. 

**VERIFY\_IDENTITY**  
 Enforce SSL and verify the CA and CA hostname\. 

When using a `mysql` or `psql` client with `--ssl-mode` `VERIFY_CA` or `VERIFY_IDENTITY`, specify the `--ssl-ca` option pointing to a CA in \.pem format\. For a \.pem file that you can use, download the [Amazon root CA 1 trust store](https://www.amazontrust.com/repository/AmazonRootCA1.pem) from Amazon Trust Services\. 

 Aurora Serverless uses wildcard certificates\. If you use the `mysql` client to connect with SSL mode `VERIFY_IDENTITY`, currently you must use the MySQL 8\.0\-compatible `mysql` command\. 