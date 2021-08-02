# Using Amazon Aurora Serverless v1<a name="aurora-serverless"></a>

Amazon Aurora Serverless v1 \(Amazon Aurora Serverless version 1\) is an on\-demand autoscaling configuration for Amazon Aurora\. An *Aurora Serverless DB cluster* is a DB cluster that scales compute capacity up and down based on your application's needs\. This contrasts with Aurora *provisioned DB clusters*, for which you manually manage capacity\. Aurora Serverless v1 provides a relatively simple, cost\-effective option for infrequent, intermittent, or unpredictable workloads\. It is cost\-effective because it automatically starts up, scales compute capacity to match your application's usage, and shuts down when it's not in use\. 

To learn more about pricing, see [Serverless Pricing](https://aws.amazon.com/rds/aurora/pricing/) under **MySQL\-Compatible Edition** or **PostgreSQL\-Compatible Edition** on the Amazon Aurora pricing page\. 

Aurora Serverless v1 clusters have the same kind of high\-capacity, distributed, and highly available storage volume that is used by provisioned DB clusters\. The cluster volume for an Aurora Serverless v1 cluster is always encrypted\. You can choose the encryption key, but you can't disable encryption\. That means that you can perform the same operations on an Aurora Serverless v1 that you can on encrypted snapshots\. For more information, see [Aurora Serverless v1 and snapshots](aurora-serverless.how-it-works.md#aurora-serverless.snapshots)\. 

**Topics**
+ [Advantages of Aurora Serverless v1](#aurora-serverless.advantages)
+ [Use cases for Aurora Serverless v1](#aurora-serverless.use-cases)
+ [Limitations of Aurora Serverless v1](#aurora-serverless.limitations)
+ [Configuration requirements for Aurora Serverless v1](#aurora-serverless.requirements)
+ [Using TLS/SSL with Aurora Serverless v1](#aurora-serverless.tls)
+ [How Aurora Serverless v1 works](aurora-serverless.how-it-works.md)
+ [Creating an Aurora Serverless v1 DB cluster](aurora-serverless.create.md)
+ [Restoring an Aurora Serverless v1 DB cluster](aurora-serverless.restorefromsnapshot.md)
+ [Modifying an Aurora Serverless v1 DB cluster](aurora-serverless.modifying.md)
+ [Scaling Aurora Serverless v1 DB cluster capacity manually](aurora-serverless.setting-capacity.md)
+ [Viewing Aurora Serverless v1 DB clusters](aurora-serverless.viewing.md)
+ [Deleting an Aurora Serverless v1 DB cluster](aurora-serverless.delete.md)
+ [Aurora Serverless v1 and Aurora database engine versions](aurora-serverless.relnotes.md)
+ [Using the Data API for Aurora Serverless](data-api.md)
+ [Logging Data API calls with AWS CloudTrail](logging-using-cloudtrail-data-api.md)
+ [Using the query editor for Aurora Serverless](query-editor.md)

## Advantages of Aurora Serverless v1<a name="aurora-serverless.advantages"></a>

Aurora Serverless v1 provides the following advantages:
+ **Simpler than provisioned** – Aurora Serverless v1 removes much of the complexity of managing DB instances and capacity\.
+ **Scalable** – Aurora Serverless v1 seamlessly scales compute and memory capacity as needed, with no disruption to client connections\.
+ **Cost\-effective** – When you use Aurora Serverless v1, you pay only for the database resources that you consume, on a per\-second basis\.
+ **Highly available storage** – Aurora Serverless v1 uses the same fault\-tolerant, distributed storage system with six\-way replication as Aurora to protect against data loss\.

## Use cases for Aurora Serverless v1<a name="aurora-serverless.use-cases"></a>

Aurora Serverless v1 is designed for the following use cases:
+ **Infrequently used applications** – You have an application that is only used for a few minutes several times per day or week, such as a low\-volume blog site\. With Aurora Serverless v1, you pay for only the database resources that you consume on a per\-second basis\.
+ **New applications** – You're deploying a new application and you're unsure about the instance size you need\. By using Aurora Serverless v1, you can create a database endpoint and have the database autoscale to the capacity requirements of your application\.
+ **Variable workloads** – You're running a lightly used application, with peaks of 30 minutes to several hours a few times each day, or several times per year\. Examples are applications for human resources, budgeting, and operational reporting applications\. With Aurora Serverless v1, you no longer need to provision for peak or average capacity\.
+ **Unpredictable workloads** – You're running daily workloads that have sudden and unpredictable increases in activity\. An example is a traffic site that sees a surge of activity when it starts raining\. With Aurora Serverless v1, your database autoscales capacity to meet the needs of the application's peak load and scales back down when the surge of activity is over\. 
+ **Development and test databases** – Your developers use databases during work hours but don't need them on nights or weekends\. With Aurora Serverless v1, your database automatically shuts down when it's not in use\.
+ **Multi\-tenant applications** – With Aurora Serverless v1, you don't have to individually manage database capacity for each application in your fleet\. Aurora Serverless v1 manages individual database capacity for you\. 

## Limitations of Aurora Serverless v1<a name="aurora-serverless.limitations"></a>

The following limitations apply to Aurora Serverless v1:
+ Aurora Serverless v1 is available in certain AWS Regions and for specific Aurora MySQL and Aurora PostgreSQL versions only\. For more information, see [Aurora Serverless v1](Concepts.AuroraFeaturesRegionsDBEngines.grids.md#Concepts.Aurora_Fea_Regions_DB-eng.Feature.Serverless)\. 
+ Aurora Serverless v1 doesn't support the following features:
  + Aurora global databases
  + Aurora multi\-master clusters
  + Aurora Replicas
  + AWS Identity and Access Management \(IAM\) database authentication
  + Backtracking in Aurora 
  + Database activity streams
  + Performance Insights
+ Connections to an Aurora Serverless v1 DB cluster are closed automatically if held open for longer than one day\.
+ All Aurora Serverless v1 DB clusters have the following limitations: 
  + You can't export Aurora Serverless v1 snapshots to Amazon S3 buckets\.
  + You can't save data to text files in Amazon S3\.
  + You can't use AWS Database Migration Service and Change Data Capture \(CDC\) with Aurora Serverless DB clusters\. Only provisioned Aurora DB clusters support CDC with AWS DMS as a source\.
  + You can't load text file data to Aurora MySQL Serverless from Amazon S3\. However, you can load data to Aurora PostgreSQL Serverless from Amazon S3 by using the `aws_s3` extension with the `aws_s3.table_import_from_s3` function and the `credentials` parameter\. For more information, see [Importing Amazon S3 data into an Aurora PostgreSQL DB cluster](AuroraPostgreSQL.Migrating.md#USER_PostgreSQL.S3Import)\.
+ Aurora MySQL–based DB clusters running Aurora Serverless v1 don't support the following:
  + Invoking AWS Lambda functions from within your Aurora MySQL DB cluster\. However, AWS Lambda functions can make calls to your Aurora MySQL Serverless DB cluster\.
  + Restoring a snapshot from a DB instance that isn't Aurora MySQL or RDS for MySQL\.
  + Replicating data using replication based on binary logs \(binlogs\)\. This limitation is true regardless of whether your Aurora MySQL\-based DB cluster Aurora Serverless v1 is the source or the target of the replication\. To replicate data into an Aurora Serverless v1 DB cluster from a MySQL DB instance outside Aurora, such as one running on Amazon EC2, consider using AWS Database Migration Service\. For more information, see the [AWS Database Migration Service User Guide](https://docs.aws.amazon.com/dms/latest/userguide/)\. 
+ Aurora PostgreSQL–based DB clusters running Aurora Serverless v1 have the following limitations:
  + Aurora PostgreSQL query plan management \(`apg_plan_management` extension\) isn't supported\.
  + The logical replication feature available in Amazon RDS PostgreSQL and Aurora PostgreSQL isn't supported\. 
  + Outbound communications such as those enabled by Amazon RDS for PostgreSQL extensions aren't supported\. For example, you can't access external data with the `postgres_fdw/dblink` extension\. For more information about RDS PostgreSQL extensions, see [PostgreSQL on Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html#PostgreSQL.Concepts.General.FeatureSupport.Extensions.101x) in the *RDS User Guide*\.
  + Currently, certain SQL queries and commands aren't recommended\. These include session\-level advisory locks, temporary relations, asynchronous notifications \(`LISTEN`\), and cursors with hold \(`DECLARE name ... CURSOR WITH HOLD FOR query`\)\. Also, `NOTIFY` commands prevent scaling and aren't recommended\. 

    For more information, see [Autoscaling for Aurora Serverless v1](aurora-serverless.how-it-works.md#aurora-serverless.how-it-works.auto-scaling)\.
+ You can't set the preferred backup window for an Aurora Serverless v1 DB cluster\.

## Configuration requirements for Aurora Serverless v1<a name="aurora-serverless.requirements"></a>

When you create an Aurora Serverless v1 DB cluster, pay attention to the following requirements:
+ Use these specific port numbers for each DB engine:
  + Aurora MySQL – `3306`
  + Aurora PostgreSQL – `5432` 
+ Create your Aurora Serverless v1 DB cluster in a virtual private cloud \(VPC\) based on the Amazon VPC service\. When you create an Aurora Serverless v1 DB cluster in your VPC, you consume two \(2\) of the fifty \(50\) Interface and Gateway Load Balancer endpoints allotted to your VPC\. These endpoints are created automatically for you\. To increase your quota, you can contact AWS Support\. For more information, see [Amazon VPC quotas](https://docs.aws.amazon.com/vpc/latest/userguide/amazon-vpc-limits.html#vpc-limits-endpoints)\. 
+ You can't give an Aurora Serverless v1 DB cluster a public IP address\. You can access an Aurora Serverless v1 DB cluster only from within a VPC\. 
+ Create subnets in different Availability Zones for the DB subnet group that you use for your Aurora Serverless v1 DB cluster\. In other words, you can't have more than one subnet in the same Availability Zone\.
+ Changes to a subnet group used by an Aurora Serverless v1 DB cluster aren't applied to the cluster\.
+ You can access an Aurora Serverless v1 DB cluster from AWS Lambda\. To do so, you must configure your Lambda function to run in the same VPC as your Aurora Serverless v1 DB cluster\. For more information about working with AWS Lambda, see [Configuring a Lambda function to access resources in an Amazon VPC](https://docs.aws.amazon.com/lambda/latest/dg/vpc.html) in the *AWS Lambda Developer Guide*\. 

## Using TLS/SSL with Aurora Serverless v1<a name="aurora-serverless.tls"></a>

By default, Aurora Serverless v1 uses the Transport Layer Security/Secure Sockets Layer \(TLS/SSL\) protocol to encrypt communications between clients and your Aurora Serverless v1 DB cluster\. It supports TLS/SSL versions 1\.0, 1\.1, and 1\.2\. You don't need to configure your Aurora Serverless v1 DB cluster to use TLS/SSL\. 

However, the following limitations apply: 
+ TLS/SSL support for Aurora Serverless v1 DB clusters isn't currently available in the China \(Beijing\) AWS Region\.
+ When you create database users for an Aurora MySQL–based Aurora Serverless v1 DB cluster, don't use the `REQUIRE` clause for SSL permissions\. Doing so prevents users from connecting to the Aurora DB instance\.
+ For both MySQL Client and PostgreSQL Client utilities, session variables that you might use in other environments have no effect when using TLS/SSL between client and Aurora Serverless v1\. 
+ For the MySQL Client, when connecting with TLS/SSL's `VERIFY_IDENTITY` mode, currently you need to use the MySQL 8\.0\-compatible `mysql` command\. For more information, see [Connecting to a DB instance running the MySQL database engine](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ConnectToInstance.html)\.

Depending on the client that you use to connect to Aurora Serverless v1 DB cluster, you might not need to specify TLS/SSL to get an encrypted connection\. For example, to use the PostgreSQL Client to connect to an Aurora Serverless v1 DB cluster running Aurora PostgreSQL\-Compatible Edition, connect as you normally do\.

```
psql -h endpoint -U user
```

After you enter your password, the PostgreSQL Client shows you see the connection details, including the TLS/SSL version and cipher\.

```
psql (12.5 (Ubuntu 12.5-0ubuntu0.20.04.1), server 10.12)
SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
Type "help" for help.
```

**Important**  
 Aurora Serverless v1 uses the Transport Layer Security/Secure Sockets Layer \(TLS/SSL\) protocol to encrypt connections by default unless SSL/TLS is disabled by the client application\. The TLS/SSL connection terminates at the router fleet\. Communication between the router fleet and your Aurora Serverless v1 DB cluster occurs within the service's internal network boundary\.   
 You can check the status of the client connection to examine whether the connection to Aurora Serverless v1 is TLS/SSL encrypted\. The PostgreSQL `pg_stat_ssl` and `pg_stat_activity` tables and its `ssl_is_used` function don't show the TLS/SSL state for the communication between the client application and Aurora Serverless v1\. Similarly, the TLS/SSL state can't be derived from the MySQL `status` statement\.   
 The Aurora cluster parameters `force_ssl` for PostgreSQL and `require_secure_transport` for MySQL aren't supported for Aurora Serverless v1\. For a complete list of parameters supported by Aurora Serverless v1, call the [DescribeEngineDefaultClusterParameters](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeEngineDefaultClusterParameters.html) API\. For more information on parameter groups and Aurora Serverless v1, see [Parameter groups and Aurora Serverless v1](aurora-serverless.how-it-works.md#aurora-serverless.parameter-groups)\. 

To use the MySQL Client to connect to an Aurora Serverless v1 DB cluster running Aurora MySQL\-Compatible Edition, you specify TLS/SSL in your request\. The following example includes the [Amazon root CA 1 trust store](https://www.amazontrust.com/repository/AmazonRootCA1.pem) downloaded from Amazon Trust Services, which is necessary for this connection to succeed\. 

```
mysql -h endpoint -P 3306 -u user -p --ssl-ca=amazon-root-CA-1.pem --ssl-mode=REQUIRED
```

When prompted, enter your password\. Soon, the MySQL monitor opens\. You can confirm that the session is encrypted by using the `status` command\. 

```
mysql> status
--------------
mysql  Ver 14.14 Distrib 5.5.62, for Linux (x86_64) using readline 5.1
Connection id:          19
Current database:
Current user:           ***@*******
SSL:                    Cipher in use is ECDHE-RSA-AES256-SHA
...
```

To learn more about connecting to Aurora MySQL database with the MySQL Client, see [Connecting to a DB instance running the MySQL database engine](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ConnectToInstance.html)\. 

Aurora Serverless v1 supports all TLS/SSL modes available to the MySQL Client \(`mysql`\) and PostgreSQL Client \(`psql`\), including those listed in the following table\.


| Description of TLS/SSL mode | mysql | psql | 
| --- | --- | --- | 
| Connect without using TLS/SSL\. | DISABLED | disable | 
| Try the connection using TLS/SSL first, but fall back to non\-SSL if necessary\.  | PREFERRED | prefer \(default\) | 
| Enforce using TLS/SSL\.  | REQUIRED | require | 
| Enforce TLS/SSL and verify the CA\. | VERIFY\_CA | verify\-ca  | 
| Enforce TLS/SSL, verify the CA, and verify the CA hostname\. | VERIFY\_IDENTITY | verify\-full | 

Aurora Serverless v1 uses wildcard certificates\. If you specify the "verify CA" or the "verify CA and CA hostname" option when using TLS/SSL, first download the [Amazon root CA 1 trust store](https://www.amazontrust.com/repository/AmazonRootCA1.pem) from Amazon Trust Services\. After doing so, you can identify this PEM\-formatted file in your client command\. To do so using the PostgreSQL Client:

For Linux, macOS, or Unix:

```
psql 'host=endpoint user=user sslmode=require sslrootcert=amazon-root-CA-1.pem dbname=db-name'
```

To learn more about working with the Aurora PostgreSQL database using the Postgres Client, see [Connecting to a DB instance running the PostgreSQL database engine](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ConnectToPostgreSQLInstance.html)\.

For more information about connecting to Aurora DB clusters in general, see [Connecting to an Amazon Aurora DB cluster](Aurora.Connecting.md)\.