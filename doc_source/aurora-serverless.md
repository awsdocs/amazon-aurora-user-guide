# Using Amazon Aurora Serverless<a name="aurora-serverless"></a>

 Amazon Aurora Serverless is an on\-demand, autoscaling configuration for Amazon Aurora\. An *Aurora Serverless DB cluster* is a DB cluster that automatically starts up, shuts down, and scales up or down its compute capacity based on your application's needs\. Aurora Serverless provides a relatively simple, cost\-effective option for infrequent, intermittent, or unpredictable workloads\. It can provide this because it automatically starts up, scales compute capacity to match your application's usage, and shuts down when it's not in use\. 

**Note**  
 A non\-Serverless DB cluster for Aurora is called a *provisioned DB cluster*\. Aurora Serverless clusters and provisioned clusters both have the same kind of high\-capacity, distributed, and highly available storage volume\. 

For information on Aurora Serverless pricing, see [Serverless Pricing](https://aws.amazon.com/rds/aurora/pricing/) under either **MySQL\-Compatible Edition** or **PostgreSQL\-Compatible Edition**\.

**Topics**
+ [Advantages of Aurora Serverless](#aurora-serverless.advantages)
+ [Use Cases for Aurora Serverless](#aurora-serverless.use-cases)
+ [Limitations of Aurora Serverless](#aurora-serverless.limitations)
+ [Using TLS/SSL with Aurora Serverless](#aurora-serverless.tls)
+ [How Aurora Serverless Works](aurora-serverless.how-it-works.md)
+ [Creating an Aurora Serverless DB Cluster](aurora-serverless.create.md)
+ [Restoring an Aurora Serverless DB Cluster](aurora-serverless.restorefromsnapshot.md)
+ [Modifying an Aurora Serverless DB Cluster](aurora-serverless.modifying.md)
+ [Setting the Capacity of an Aurora Serverless DB Cluster](aurora-serverless.setting-capacity.md)
+ [Viewing Aurora Serverless DB Clusters](aurora-serverless.viewing.md)
+ [Aurora Serverless Versions](aurora-serverless.relnotes.md)
+ [Using the Data API for Aurora Serverless](data-api.md)
+ [Logging Data API Calls with AWS CloudTrail](logging-using-cloudtrail-data-api.md)
+ [Using the Query Editor for Aurora Serverless](query-editor.md)

## Advantages of Aurora Serverless<a name="aurora-serverless.advantages"></a>

Aurora Serverless provides the following advantages:

**Simpler**  
Aurora Serverless removes much of the complexity of managing DB instances and capacity\.

**Scalable**  
Aurora Serverless seamlessly scales compute and memory capacity as needed, with no disruption to client connections\.

**Cost\-effective**  
When you use Aurora Serverless, you pay for only the database resources that you consume, on a per\-second basis\. 

**Highly available storage**  
Aurora Serverless uses the same fault\-tolerant, distributed storage system with six\-way replication as Aurora to protect against data loss\.

## Use Cases for Aurora Serverless<a name="aurora-serverless.use-cases"></a>

Aurora Serverless is designed for the following use cases:

**Infrequently used applications**  
You have an application that is only used for a few minutes several times per day or week, such as a low\-volume blog site\. With Aurora Serverless, you pay for only the database resources that you consume on a per\-second basis\.

**New applications**  
You are deploying a new application and are unsure about which instance size you need\. With Aurora Serverless, you can create a database endpoint and have the database autoscale to the capacity requirements of your application\.

**Variable workloads**  
You're running a lightly used application, with peaks of 30 minutes to several hours a few times each day, or several times per year\. Examples are applications for human resources, budgeting, and operational reporting applications\. With Aurora Serverless, you no longer need to provision to either peak or average capacity\.

**Unpredictable workloads**  
You're running workloads where there is database usage throughout the day, but also peaks of activity that are hard to predict\. An example is a traffic site that sees a surge of activity when it starts raining\. With Aurora Serverless, your database autoscales capacity to meet the needs of the application's peak load and scales back down when the surge of activity is over\.

**Development and test databases**  
Your developers use databases during work hours but don't need them on nights or weekends\. With Aurora Serverless, your database automatically shuts down when it's not in use\.

**Multi\-tenant applications**  
With Aurora Serverless, you don't have to individually manage database capacity for each application in your fleet\. Aurora Serverless manages individual database capacity for you\.

## Limitations of Aurora Serverless<a name="aurora-serverless.limitations"></a>

The following limitations apply to Aurora Serverless:
+ Aurora Serverless is available for the following: 
  + Aurora MySQL version 1, compatible with MySQL version 5\.6\.
  + Aurora MySQL version 2, compatible with MySQL version 5\.7\. Select Aurora MySQL version 2\.07\.1 to be able to use Aurora Serverless with MySQL 5\.7 compatibility\.
  + Aurora with PostgreSQL version 10\.7 compatibility\.
+ The port number for connections must be:
  + `3306` for Aurora MySQL
  + `5432` for Aurora PostgreSQL
+ You can't give an Aurora Serverless DB cluster a public IP address\. You can access an Aurora Serverless DB cluster only from within a virtual private cloud \(VPC\) based on the Amazon VPC service\.
+ Each Aurora Serverless DB cluster requires two AWS PrivateLink endpoints\. If you reach the limit for AWS PrivateLink endpoints within your VPC, you can't create any more Aurora Serverless clusters in that VPC\. For information about checking and changing the limits on endpoints within a VPC, see [Amazon VPC Limits](https://docs.aws.amazon.com/vpc/latest/userguide/amazon-vpc-limits.html)\. 
+ A DB subnet group used by Aurora Serverless can't have more than one subnet in the same Availability Zone\.
+ Changes to a subnet group used by an Aurora Serverless DB cluster are not applied to the cluster\.
+ A connection to an Aurora Serverless DB cluster is closed automatically if it stays open for longer than one day\.
+ Binlog\-based replication isn't supported for Aurora Serverless DB clusters\. 
+ Aurora Serverless doesn't support the following features:
  + [Loading data from an Amazon S3 bucket](AuroraMySQL.Integrating.LoadFromS3.md)
  + [Saving data to an Amazon S3 bucket](AuroraMySQL.Integrating.SaveIntoS3.md)
  + [Invoking an AWS Lambda function with an Aurora MySQL native function](AuroraMySQL.Integrating.Lambda.md#AuroraMySQL.Integrating.NativeLambda)
  + [Aurora Replicas](AuroraMySQL.Replication.md)
  + [Backtracking](AuroraMySQL.Managing.Backtrack.md)
  + [Multi\-master clusters](aurora-multi-master.md)
  + [Database cloning](Aurora.Managing.Clone.md)
  + [IAM database authentication](UsingWithRDS.IAMDBAuth.md)
  + [Restoring a snapshot from a MySQL DB instance](AuroraMySQL.Migrating.RDSMySQL.md)
  + [Amazon RDS Performance Insights](USER_PerfInsights.md)

**Note**  
You can access an Aurora Serverless DB cluster from AWS Lambda\. You must configure your Lambda function to run in the same VPC as your Aurora Serverless DB cluster\. For more information about working with AWS Lambda, see [Configuring a Lambda Function to Access Resources in an Amazon VPC](https://docs.aws.amazon.com/lambda/latest/dg/vpc.html) in the *AWS Lambda Developer Guide*\.

## Using TLS/SSL with Aurora Serverless<a name="aurora-serverless.tls"></a>

 You can connect to Aurora Serverless clusters using the Transport Layer Security/Secure Sockets Layer \(TLS/SSL\) protocol\. To do so, you use the same general procedure as described in [Connecting to an Amazon Aurora DB Cluster](Aurora.Connecting.md)\. 

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

When using a `mysql` or `psql` client with `--ssl-mode` `VERIFY_CA` or `VERIFY_IDENTITY`, specify the `--ssl-ca` option pointing to a CA in \.pem format\. For a \.pem file that you can use, download the [Amazon Root CA 1 trust store](https://www.amazontrust.com/repository/AmazonRootCA1.pem) from Amazon Trust Services\. 

 Aurora Serverless uses wildcard certificates\. If you use the `mysql` client to connect with SSL mode `VERIFY_IDENTITY`, currently you must use the MySQL 8\.0\-compatible `mysql` command\. 