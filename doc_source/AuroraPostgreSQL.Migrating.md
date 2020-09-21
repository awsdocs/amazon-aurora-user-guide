# Migrating Data to Amazon Aurora with PostgreSQL Compatibility<a name="AuroraPostgreSQL.Migrating"></a>

You have several options for migrating data from your existing database to an Amazon Aurora with PostgreSQL compatibility DB cluster\. Your migration options also depend on the database that you are migrating from and the size of the data that you are migrating\. Following are your options:

**Migrating from an RDS PostgreSQL DB instance**  
You can migrate data directly from an Amazon RDS PostgreSQL DB snapshot to an Aurora PostgreSQL DB cluster\. For more information, see [Migrating an RDS PostgreSQL DB Snapshot to an Aurora PostgreSQL DB Cluster](#AuroraPostgreSQL.Migrating.RDSPostgreSQL.Import.Console)\.  
You can also migrate from an RDS PostgreSQL DB instance by creating an Aurora PostgreSQL Read Replica of a PostgreSQL DB instance\. When the replica lag between the PostgreSQL DB instance and the Aurora PostgreSQL Read Replica is zero, you can stop replication\. At this point, you can make the Aurora Read Replica a standalone Aurora PostgreSQL DB cluster for reading and writing\. For more information, see [Migrating Data from an RDS PostgreSQL DB Instance to an Aurora PostgreSQL DB Cluster by Using an Aurora Read Replica](#AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica)\.

**Migrating from a database that is not PostgreSQL\-compatible**  
You can use AWS Database Migration Service \(AWS DMS\) to migrate data from a database that is not PostgreSQL\-compatible\. For more information on AWS DMS, see [What Is AWS Database Migration Service?](https://docs.aws.amazon.com/dms/latest/userguide/Welcome.html)

**Importing Amazon S3 data**  
You can migrate by importing data from Amazon S3 into a table belonging to an Aurora PostgreSQL DB cluster for an RDS PostgreSQL DB instance\. For more information, see [Importing Amazon S3 Data into an Aurora PostgreSQL DB Cluster](#USER_PostgreSQL.S3Import)\.

For a list of AWS Regions where Aurora is available, see [Amazon Aurora](https://docs.aws.amazon.com/general/latest/gr/rande.html#aurora) in the *AWS General Reference*\.

## Migrating an RDS PostgreSQL DB Snapshot to an Aurora PostgreSQL DB Cluster<a name="AuroraPostgreSQL.Migrating.RDSPostgreSQL.Import.Console"></a>

To create an Aurora PostgreSQL DB cluster, you can migrate a DB snapshot of an RDS PostgreSQL DB instance\. The new Aurora PostgreSQL DB cluster is populated with the data from the original RDS PostgreSQL DB instance\. The DB snapshot must be from an RDS DB instance running PostgreSQL 9\.6\.1 or 9\.6\.3\. For information about creating a DB snapshot, see [Creating a DB Snapshot](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CreateSnapshot.html)\.

In some cases, the DB snapshot might not be in the AWS Region where you want to locate your data\. If so, use the Amazon RDS console to copy the DB snapshot to that AWS Region\. For information about copying a DB snapshot, see [Copying a DB Snapshot](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CopySnapshot.html)\.

When you migrate the DB snapshot by using the console, the console takes the actions necessary to create both the DB cluster and the primary instance\.

You can also choose for your new Aurora PostgreSQL DB cluster to be encrypted at rest by using an AWS Key Management Service \(AWS KMS\) customer master key \(CMK\)\. This option is available only for unencrypted DB snapshots\.

**To migrate a PostgreSQL DB snapshot by using the RDS console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose **Snapshots**\.

1. On the **Snapshots** page, choose the snapshot that you want to migrate into an Aurora PostgreSQL DB cluster\.

1. Choose **Migrate Database**\.

1. Set the following values on the **Migrate Database** page:
   + **DB Instance Class**: Choose a DB instance class that has the required storage and capacity for your database, for example `db.r3.large`\. Aurora cluster volumes automatically grow as the amount of data in your database increases, up to a maximum size of 128 tebibytes \(TiB\) for Aurora MySQL and 64 TiB for Aurora PostgreSQL\. So you only need to choose a DB instance class that meets your current storage requirements\. For more information, see [Overview of Aurora Storage](Aurora.Overview.StorageReliability.md#Aurora.Overview.Storage)\.
   + **DB Instance Identifier**: Enter a name for the DB cluster that is unique for your account in the AWS Region that you chose\. This identifier is used in the endpoint addresses for the instances in your DB cluster\. You might choose to add some intelligence to the name, such as including the AWS Region and DB engine that you chose, for example **aurora\-cluster1**\.

     The DB instance identifier has the following constraints:
     + It must contain 1–63 alphanumeric characters or hyphens\.
     + Its first character must be a letter\.
     + It can't end with a hyphen or contain two consecutive hyphens\.
     + It must be unique for all DB instances per AWS account, per AWS Region\.
   + **VPC**: If you have an existing VPC, then you can use that VPC with your Aurora PostgreSQL DB cluster by choosing your VPC identifier, for example `vpc-a464d1c1`\. For information on using an existing VPC, see [How to Create a VPC for Use with Amazon Aurora](Aurora.CreateVPC.md)\.

     Otherwise, you can choose to have Amazon RDS create a VPC for you by choosing **Create a new VPC**\. 
   + **Subnet Group**: If you have an existing subnet group, then you can use that subnet group with your Aurora PostgreSQL DB cluster by choosing your subnet group identifier, for example `gs-subnet-group1`\.

     Otherwise, you can choose to have Amazon RDS create a subnet group for you by choosing **Create a new subnet group**\. 
   + **Publicly Accessible**: Choose **No** to specify that instances in your DB cluster can only be accessed by resources inside of your VPC\. Choose **Yes** to specify that instances in your DB cluster can be accessed by resources on the public network\. The default is **Yes**\.
**Note**  
Your production DB cluster might not need to be in a public subnet, because only your application servers require access to your DB cluster\. If your DB cluster doesn't need to be in a public subnet, set **Publicly Accessible** to **No**\.
   + **Availability Zone**: Choose the Availability Zone to host the primary instance for your Aurora PostgreSQL DB cluster\. To have Amazon RDS choose an Availability Zone for you, choose **No Preference**\.
   + **Database Port**: Enter the default port to be used when connecting to instances in the Aurora PostgreSQL DB cluster\. The default is `5432`\.
**Note**  
You might be behind a corporate firewall that doesn't allow access to default ports such as the PostgreSQL default port, 5432\. In this case, provide a port value that your corporate firewall allows\. Remember that port value later when you connect to the Aurora PostgreSQL DB cluster\.
   + **Enable Encryption**: Choose **Yes** for your new Aurora PostgreSQL DB cluster to be encrypted at rest\. If you choose **Yes**, also choose an AWS KMS customer master key \(CMK\) as the **Master Key** value\.
   + **Auto minor version upgrade**: Choose **Enable auto minor version upgrade** to enable your Aurora PostgreSQL DB cluster to receive minor PostgreSQL DB engine version upgrades automatically when they become available\.

     The **Auto Minor Version Upgrade** option only applies to upgrades to PostgreSQL minor engine versions for your Aurora PostgreSQL DB cluster\. It doesn't apply to regular patches applied to maintain system stability\.

1. Choose **Migrate** to migrate your DB snapshot\. 

1. Choose **Instances**, and then choose the arrow icon to show the DB cluster details and monitor the progress of the migration\. On the details page, you can find the cluster endpoint used to connect to the primary instance of the DB cluster\. For more information on connecting to an Aurora PostgreSQL DB cluster, see [Connecting to an Amazon Aurora DB Cluster](Aurora.Connecting.md)\. 

## Migrating Data from an RDS PostgreSQL DB Instance to an Aurora PostgreSQL DB Cluster by Using an Aurora Read Replica<a name="AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica"></a>

You can migrate from a PostgreSQL DB instance to an Aurora PostgreSQL DB cluster by using an Aurora Read Replica\. When you need to migrate from an RDS PostgreSQL DB instance to an Aurora PostgreSQL DB cluster, we recommend using this approach\. 

In this case, Amazon RDS uses the PostgreSQL DB engine's streaming replication functionality to create a special type of DB cluster for the source PostgreSQL DB instance\. This type of DB cluster is called an Aurora Read Replica\. Updates made to the source PostgreSQL DB instance are asynchronously replicated to the Aurora Read Replica\. 

**Topics**
+ [Overview of Migrating Data by Using an Aurora Read Replica](#AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica.Intro)
+ [Preparing to Migrate Data by Using an Aurora Read Replica](#AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica.Prepare)
+ [Creating an Aurora Read Replica](#AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica.Create)
+ [Promoting an Aurora Read Replica](#AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica.Promote)

### Overview of Migrating Data by Using an Aurora Read Replica<a name="AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica.Intro"></a>

To migrate from an RDS PostgreSQL DB instance to an Aurora PostgreSQL DB cluster, we recommend creating an Aurora Read Replica of your source PostgreSQL DB instance\. When the replica lag between the PostgreSQL DB instance and the Aurora PostgreSQL Read Replica is zero, you can stop replication\. At this point, you can promote the Aurora Read Replica to be a standalone Aurora PostgreSQL DB cluster\. This standalone DB cluster can then accept write loads\. 

Be prepared for migration to take a while, roughly several hours per tebibyte \(TiB\) of data\. While the migration is in progress, your Amazon RDS PostgreSQL instance accumulates write ahead log \(WAL\) segments\. Make sure that your Amazon RDS instance has sufficient storage capacity for these segments\. 

When you create an Aurora Read Replica of a PostgreSQL DB instance, Amazon RDS creates a DB snapshot of your source PostgreSQL DB instance\. This snapshot is private to Amazon RDS and incurs no charges\. Amazon RDS then migrates the data from the DB snapshot to the Aurora Read Replica\. After the DB snapshot data is migrated to the new Aurora PostgreSQL DB cluster, RDS starts replication between your PostgreSQL DB instance and the Aurora PostgreSQL DB cluster\. 

You can only have one Aurora Read Replica for a PostgreSQL DB instance\. If you try to create an Aurora Read Replica for your Amazon RDS PostgreSQL instance and you already have a read replica, the request is rejected\. 

**Note**  
Replication issues can arise due to feature differences between Aurora PostgreSQL and the PostgreSQL engine version of your RDS PostgreSQL DB instance that is the replication source\. You can replicate only from an Amazon RDS PostgreSQL instance that is compatible with the Aurora PostgreSQL version in question\. For example, if the supported Aurora PostgreSQL version is 9\.6\.3, the Amazon RDS PostgreSQL DB instance must be running version 9\.6\.1 or greater\. If you encounter an error, you can find help in the [Amazon RDS Community Forum](https://forums.aws.amazon.com/forum.jspa?forumID=60) or by contacting AWS Support\.

For more information on PostgreSQL read replicas, see [Working with Read Replicas](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ReadRepl.html) in the *Amazon RDS User Guide*\.

### Preparing to Migrate Data by Using an Aurora Read Replica<a name="AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica.Prepare"></a>

Before you migrate data from your RDS PostgreSQL instance to an Aurora PostgreSQL cluster, make sure that your instance has sufficient storage capacity\. This storage capacity is for the write ahead log \(WAL\) segments that accumulate during the migration\. There are several metrics to check for this, described following\. 


| Metric | Description | 
| --- | --- | 
|  `FreeStorageSpace`  |  The available storage space\. Units: Bytes  | 
|  `OldestReplicationSlotLag`  |  The size of the lag for WAL data in the replica that is lagging the most\. Units: Megabytes  | 
|  `RDSToAuroraPostgreSQLReplicaLag`  |  The amount of time in seconds that an Aurora PostgreSQL DB cluster lags behind the source RDS DB instance\.  | 
|  `TransactionLogsDiskUsage`  |  The disk space used by the transaction logs\. Units: Megabytes  | 

For more information about monitoring your RDS instance, see [Monitoring](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Monitoring.html) in the *Amazon RDS User Guide*\.

### Creating an Aurora Read Replica<a name="AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica.Create"></a>

You can create an Aurora Read Replica for a PostgreSQL DB instance by using the console or the AWS CLI\.

#### Console<a name="AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica.Create.Console"></a>

**To create an Aurora Read Replica from a source PostgreSQL DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\. 

1. Choose the PostgreSQL DB instance that you want to use as the source for your Aurora Read Replica, and choose **Create Aurora read replica** for **Actions**\.  
![\[Create Aurora Read Replica\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/Aurorapgres-migrate.png)

1. Choose the DB cluster specifications that you want to use for the Aurora Read Replica, as described in the following table\.     
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraPostgreSQL.Migrating.html)

1. Choose **Create read replica**\.

#### AWS CLI<a name="AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica.Create.CLI"></a>

To create an Aurora Read Replica from a source PostgreSQL DB instance, use the [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) and [ create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) AWS CLI commands to create a new Aurora PostgreSQL DB cluster\. When you call the create\-db\-cluster command, include the `--replication-source-identifier` parameter to identify the Amazon Resource Name \(ARN\) for the source PostgreSQL DB instance\. For more information about Amazon RDS ARNs, see [Amazon Relational Database Service \(Amazon RDS\)](https://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html#arn-syntax-rds) in the *AWS General Reference*\. 

Don't specify the master user name, master password, or database name\. The Aurora Read Replica uses the same master user name, master password, and database name as the source PostgreSQL DB instance\. 

For Linux, macOS, or Unix:

```
aws rds create-db-cluster --db-cluster-identifier sample-replica-cluster --engine aurora-postgresql \
    --db-subnet-group-name mysubnetgroup --vpc-security-group-ids sg-c7e5b0d2 \
    --replication-source-identifier arn:aws:rds:us-west-2:123456789012:db:master-postgresql-instance
```

For Windows:

```
aws rds create-db-cluster --db-cluster-identifier sample-replica-cluster --engine aurora-postgresql ^
    --db-subnet-group-name mysubnetgroup --vpc-security-group-ids sg-c7e5b0d2 ^
    --replication-source-identifier arn:aws:rds:us-west-2:123456789012:db:master-postgresql-instance
```

If you use the console to create an Aurora Read Replica, then RDS automatically creates the primary instance for your DB cluster Aurora Read Replica\. If you use the CLI to create an Aurora Read Replica, you must explicitly create the primary instance for your DB cluster\. The primary instance is the first instance that is created in a DB cluster\.

You can create a primary instance for your DB cluster by using the [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) CLI command with the following parameters:
+ `--db-cluster-identifier`

  The name of your DB cluster\.
+ `--db-instance-class`

  The name of the DB instance class to use for your primary instance\.
+ `--db-instance-identifier`

  The name of your primary instance\.
+ `--engine aurora-postgresql`

  The database engine to use\.

In the following example, you create a primary instance named *`myreadreplicainstance`* for the DB cluster named *`myreadreplicacluster`*\. You do this using the DB instance class specified in *`myinstanceclass`*\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds create-db-instance \
    --db-cluster-identifier myreadreplicacluster \
    --db-instance-class myinstanceclass
    --db-instance-identifier myreadreplicainstance \
    --engine aurora-postgresql
```
For Windows:  

```
aws rds create-db-instance \
    --db-cluster-identifier myreadreplicacluster \
    --db-instance-class myinstanceclass
    --db-instance-identifier myreadreplicainstance \
    --engine aurora-postgresql
```

#### RDS API<a name="Aurora.Migration.RDSPostgreSQL.Create.API"></a>

To create an Aurora Read Replica from a source PostgreSQL DB instance, use the RDS API operations [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) and [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) to create a new Aurora DB cluster and primary instance\. Don't specify the master user name, master password, or database name\. The Aurora Read Replica uses the same master user name, master password, and database name as the source PostgreSQL DB instance\. 

You can create a new Aurora DB cluster for an Aurora Read Replica from a source PostgreSQL DB instance\. To do so, use the RDS API operation [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) with the following parameters:
+ `DBClusterIdentifier`

  The name of the DB cluster to create\.
+ `DBSubnetGroupName`

  The name of the DB subnet group to associate with this DB cluster\.
+ `Engine=aurora-postgresql`

  The name of the engine to use\.
+ `ReplicationSourceIdentifier`

  The Amazon Resource Name \(ARN\) for the source PostgreSQL DB instance\. For more information about Amazon RDS ARNs, see [Amazon Relational Database Service \(Amazon RDS\)](https://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html#arn-syntax-rds) in the *Amazon Web Services General Reference\.*
+ `VpcSecurityGroupIds`

  The list of Amazon EC2 VPC security groups to associate with this DB cluster\.

In the following example, you create a DB cluster named *`myreadreplicacluster`* from a source PostgreSQL DB instance\. This cluster has an ARN set to *`mysqlARN`*\. The cluster is associated with a DB subnet group named *`mysubnetgroup`* and a VPC security group named *`mysecuritygroup`*\.

**Example**  

```
https://rds.us-east-1.amazonaws.com/
    ?Action=CreateDBCluster
    &DBClusterIdentifier=myreadreplicacluster
    &DBSubnetGroupName=mysubnetgroup
    &Engine=aurora-postgresql
    &ReplicationSourceIdentifier=mysqlARN
    &SignatureMethod=HmacSHA256
    &SignatureVersion=4
    &Version=2014-10-31
    &VpcSecurityGroupIds=mysecuritygroup
    &X-Amz-Algorithm=AWS4-HMAC-SHA256
    &X-Amz-Credential=AKIADQKE4SARGYLE/20150927/us-east-1/rds/aws4_request
    &X-Amz-Date=20150927T164851Z
    &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
    &X-Amz-Signature=6a8f4bd6a98f649c75ea04a6b3929ecc75ac09739588391cd7250f5280e716db
```

If you use the console to create an Aurora Read Replica, then Amazon RDS automatically creates the primary instance for your DB cluster Aurora Read Replica\. If you use the CLI to create an Aurora Read Replica, you must explicitly create the primary instance for your DB cluster\. The primary instance is the first instance that is created in a DB cluster\.

You can create a primary instance for your DB cluster by using the RDS API operation [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) with the following parameters:
+ `DBClusterIdentifier`

  The name of your DB cluster\.
+ `DBInstanceClass`

  The name of the DB instance class to use for your primary instance\.
+ `DBInstanceIdentifier`

  The name of your primary instance\.
+ `Engine=aurora-postgresql`

  The name of the engine to use\.

In this example, you create a primary instance named *`myreadreplicainstance`* for the DB cluster named *`myreadreplicacluster`*\. You do this using the DB instance class specified in *`myinstanceclass`*\.

**Example**  

```
https://rds.us-east-1.amazonaws.com/
    ?Action=CreateDBInstance
    &DBClusterIdentifier=myreadreplicacluster
    &DBInstanceClass=myinstanceclass
    &DBInstanceIdentifier=myreadreplicainstance
    &Engine=aurora-postgresql
    &SignatureMethod=HmacSHA256
    &SignatureVersion=4
    &Version=2014-09-01
    &X-Amz-Algorithm=AWS4-HMAC-SHA256
    &X-Amz-Credential=AKIADQKE4SARGYLE/20140424/us-east-1/rds/aws4_request
    &X-Amz-Date=20140424T194844Z
    &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
    &X-Amz-Signature=bee4aabc750bf7dad0cd9e22b952bd6089d91e2a16592c2293e532eeaab8bc77
```

### Promoting an Aurora Read Replica<a name="AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica.Promote"></a>

After migration completes, you can promote the Aurora Read Replica to a standalone DB cluster\. You then direct your client applications to the endpoint for the Aurora Read Replica\. For more information on the Aurora endpoints, see [Amazon Aurora Connection Management](Aurora.Overview.Endpoints.md)\. Promotion should complete fairly quickly\. You can't delete the primary PostgreSQL DB instance or unlink the DB instance and the Aurora Read Replica until the promotion is complete\.

Before you promote your Aurora Read Replica, stop any transactions from being written to the source PostgreSQL DB instance\. Then wait for the replica lag on the Aurora Read Replica to reach zero\. 

After you promote your read replica, confirm that the promotion has completed\. To do this, choose **Instances** in the navigation pane and confirm that there is a **Promoted Read Replica cluster to stand\-alone database cluster** event for your read replica\. After promotion is complete, the primary PostgreSQL DB Instance and the Aurora Read Replica are unlinked\. At this point, you can safely delete the DB instance if you want to\.

#### Console<a name="AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica.Promote.Console"></a>

**To promote an Aurora Read Replica to an Aurora DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Instances**\. 

1. Choose the DB instance for the Aurora Read Replica and choose **Promote** for **Actions**\. 

1. Choose **Promote Read Replica**\.

#### AWS CLI<a name="AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica.Promote.CLI"></a>

To promote an Aurora Read Replica to a stand\-alone DB cluster, use the [https://docs.aws.amazon.com/cli/latest/reference/rds/promote-read-replica-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/promote-read-replica-db-cluster.html) AWS CLI command\. 

**Example**  
For Linux, macOS, or Unix:  

```
aws rds promote-read-replica-db-cluster \
    --db-cluster-identifier myreadreplicacluster
```
For Windows:  

```
aws rds promote-read-replica-db-cluster ^
    --db-cluster-identifier myreadreplicacluster
```

## Importing Amazon S3 Data into an Aurora PostgreSQL DB Cluster<a name="USER_PostgreSQL.S3Import"></a>

You can import data from Amazon S3 into a table belonging to an Aurora PostgreSQL DB cluster\. To do this, you use the `aws_s3` PostgreSQL extension that Aurora PostgreSQL provides\. 

**Note**  
To import from Amazon S3 into Aurora PostgreSQL, your database must be running PostgreSQL version 10\.7 or later\. 

For more information on storing data with Amazon S3, see [Create a Bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html) in the *Amazon Simple Storage Service Getting Started Guide*\. For instructions on how to upload a file to an Amazon S3 bucket, see [Add an Object to a Bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg/PuttingAnObjectInABucket.html) in the *Amazon Simple Storage Service Getting Started Guide*\.

**Topics**
+ [Overview of Importing Amazon S3 Data](#USER_PostgreSQL.S3Import.Overview)
+ [Setting Up Access to an Amazon S3 Bucket](#USER_PostgreSQL.S3Import.AccessPermission)
+ [Using the aws\_s3\.table\_import\_from\_s3 Function to Import Amazon S3 Data](#USER_PostgreSQL.S3Import.FileFormats)
+ [Function Reference](#USER_PostgreSQL.S3Import.Reference)

### Overview of Importing Amazon S3 Data<a name="USER_PostgreSQL.S3Import.Overview"></a>

To import data stored in an Amazon S3 bucket to a PostgreSQL database table, follow these steps\. 

**To import S3 data into Aurora PostgreSQL**

1. Install the required PostgreSQL extensions\. These include the `aws_s3` and `aws_commons` extensions\. To do so, start psql and use the following command\. 

   ```
   psql=> CREATE EXTENSION aws_s3 CASCADE;
   NOTICE: installing required extension "aws_commons"
   ```

   The `aws_s3` extension provides the [aws\_s3\.table\_import\_from\_s3](#aws_s3.table_import_from_s3) function that you use to import Amazon S3 data\. The `aws_commons` extension provides additional helper functions\.

1. Identify the database table and Amazon S3 file to use\.

   The [aws\_s3\.table\_import\_from\_s3](#aws_s3.table_import_from_s3) function requires the name of the PostgreSQL database table that you want to import data into\. The function also requires that you identify the Amazon S3 file to import\. To provide this information, take the following steps\.

   1. Identify the PostgreSQL database table to put the data in\. For example, the following is a sample `t1` database table used in the examples for this topic\. 

      ```
      psql=> CREATE TABLE t1 (col1 varchar(80), col2 varchar(80), col3 varchar(80));
      ```

   1. Get the following information to identify the Amazon S3 file that you want to import:
      + Bucket name – A *bucket* is a container for Amazon S3 objects or files\.
      + File path – The file path locates the file in the Amazon S3 bucket\.
      + AWS Region – The AWS Region is the location of the Amazon S3 bucket\. For example, if the S3 bucket is in the US East \(N\. Virginia\) Region, use `us-east-1`\. For a listing of AWS Region names and associated values, see [ Regions and Availability Zones ](Concepts.RegionsAndAvailabilityZones.md)\.

      To find how to get this information, see [View an Object](https://docs.aws.amazon.com/AmazonS3/latest/gsg/OpeningAnObject.html) in the *Amazon Simple Storage Service Getting Started Guide*\. You can confirm the information by using the AWS CLI command `aws s3 cp`\. If the information is correct, this command downloads a copy of the Amazon S3 file\. 

      ```
      aws s3 cp s3://sample_s3_bucket/sample_file_path ./ 
      ```

   1. Use the [aws\_commons\.create\_s3\_uri](#USER_PostgreSQL.S3Import.create_s3_uri) function to create an `aws_commons._s3_uri_1` structure to hold the Amazon S3 file information\. You provide this `aws_commons._s3_uri_1` structure as a parameter in the call to the [aws\_s3\.table\_import\_from\_s3](#aws_s3.table_import_from_s3) function\.

      For a psql example, see the following\.

      ```
      psql=> SELECT aws_commons.create_s3_uri(
         'sample_s3_bucket',
         'sample.csv',
         'us-east-1'
      ) AS s3_uri \gset
      ```

1. Provide permission to access the Amazon S3 file\.

   To import data from an Amazon S3 file, give the Aurora PostgreSQL DB cluster permission to access the Amazon S3 bucket the file is in\. To do this, you use either an AWS Identity and Access Management \(IAM\) role or security credentials\. For more information, see [Setting Up Access to an Amazon S3 Bucket](#USER_PostgreSQL.S3Import.AccessPermission)\.

1. Import the Amazon S3 data by calling the `aws_s3.table_import_from_s3` function\.

   After you complete the previous preparation tasks, use the [aws\_s3\.table\_import\_from\_s3](#aws_s3.table_import_from_s3) function to import the Amazon S3 data\. For more information, see [Using the aws\_s3\.table\_import\_from\_s3 Function to Import Amazon S3 Data](#USER_PostgreSQL.S3Import.FileFormats)\.

### Setting Up Access to an Amazon S3 Bucket<a name="USER_PostgreSQL.S3Import.AccessPermission"></a>

To import data from an Amazon S3 file, give the Aurora PostgreSQL DB cluster permission to access the Amazon S3 bucket the file is in\. You provide access to an Amazon S3 bucket in one of two ways, as described in the following topics\.

**Topics**
+ [Using an IAM Role to Access an Amazon S3 Bucket](#USER_PostgreSQL.S3Import.ARNRole)
+ [Using Security Credentials to Access an Amazon S3 Bucket](#USER_PostgreSQL.S3Import.Credentials)
+ [Troubleshooting Access to Amazon S3](#USER_PostgreSQL.S3Import.troubleshooting)

#### Using an IAM Role to Access an Amazon S3 Bucket<a name="USER_PostgreSQL.S3Import.ARNRole"></a>

Before you load data from an Amazon S3 file, give your Aurora PostgreSQL DB cluster permission to access the Amazon S3 bucket the file is in\. This way, you don't have to manage additional credential information or provide it in the [aws\_s3\.table\_import\_from\_s3](#aws_s3.table_import_from_s3) function call\.

To do this, create an IAM policy that provides access to the Amazon S3 bucket\. Create an IAM role and attach the policy to the role\. Then assign the IAM role to your DB cluster\. 

**To give an Aurora PostgreSQL DB cluster access to Amazon S3 through an IAM role**

1. Create an IAM policy\. This policy provides the bucket and object permissions that allow your Aurora PostgreSQL DB cluster to access Amazon S3\. 

   Include in the policy the following required actions to allow the transfer of files from an Amazon S3 bucket to Aurora PostgreSQL: 
   + `s3:GetObject` 
   + `s3:ListBucket` 

   Include in the policy the following resources to identify the Amazon S3 bucket and objects in the bucket\. This shows the Amazon Resource Name \(ARN\) format for accessing Amazon S3\.
   + arn:aws:s3:::*your\-s3\-bucket*
   + arn:aws:s3:::*your\-s3\-bucket*/\*

   For more information on creating an IAM policy for Aurora PostgreSQL, see [Creating and Using an IAM Policy for IAM Database Access](UsingWithRDS.IAMDBAuth.IAMPolicy.md)\. See also [Tutorial: Create and Attach Your First Customer Managed Policy](https://docs.aws.amazon.com/IAM/latest/UserGuide/tutorial_managed-policies.html) in the *IAM User Guide*\.

   The following AWS CLI command creates an IAM policy named `rds-s3-import-policy` with these options\. It grants access to a bucket named `your-s3-bucket`\. 
**Note**  
After you create the policy, note the Amazon Resource Name \(ARN\) of the policy\. You need the ARN for a subsequent step when you attach the policy to an IAM role\.   
**Example**  

   For Linux, macOS, or Unix:

   ```
   aws iam create-policy \
      --policy-name rds-s3-import-policy \
      --policy-document '{
        "Version": "2012-10-17",
        "Statement": [
          {
            "Sid": "s3import",
            "Action": [
              "s3:GetObject",
              "s3:ListBucket"
            ],
            "Effect": "Allow",
            "Resource": [
              "arn:aws:s3:::your-s3-bucket", 
              "arn:aws:s3:::your-s3-bucket/*"
            ] 
          }
        ] 
      }'
   ```

   For Windows:

   ```
   aws iam create-policy ^
      --policy-name rds-s3-import-policy ^
      --policy-document '{
        "Version": "2012-10-17",
        "Statement": [
          {
            "Sid": "s3import",
            "Action": [
              "s3:GetObject",
              "s3:ListBucket"
            ], 
            "Effect": "Allow",
            "Resource": [
              "arn:aws:s3:::your-s3-bucket", 
              "arn:aws:s3:::your-s3-bucket/*"
            ] 
          }
        ] 
      }'
   ```

1. Create an IAM role\. You do this so Aurora PostgreSQL can assume this IAM role on your behalf to access your Amazon S3 buckets\. For more information, see [Creating a Role to Delegate Permissions to an IAM User](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-user.html) in the *IAM User Guide*\.

   The following example shows using the AWS CLI command to create a role named `rds-s3-import-role`\.   
**Example**  

   For Linux, macOS, or Unix:

   ```
   aws iam create-role \
      --role-name rds-s3-import-role \
      --assume-role-policy-document '{
        "Version": "2012-10-17",
        "Statement": [
          {
            "Effect": "Allow",
            "Principal": {
               "Service": "rds.amazonaws.com"
             },
            "Action": "sts:AssumeRole"
          }
        ] 
      }'
   ```

   For Windows:

   ```
   aws iam create-role ^
      --role-name rds-s3-import-role ^
      --assume-role-policy-document '{
        "Version": "2012-10-17",
        "Statement": [
          {
            "Effect": "Allow",
            "Principal": {
               "Service": "rds.amazonaws.com"
             },
            "Action": "sts:AssumeRole"
          }
        ] 
      }'
   ```

1. Attach the IAM policy that you created to the IAM role that you created\.

   The following AWS CLI command attaches the policy created earlier to the role named `rds-s3-import-role` Replace `your-policy-arn` with the policy ARN that you noted in an earlier step\.   
**Example**  

   For Linux, macOS, or Unix:

   ```
   aws iam attach-role-policy \
      --policy-arn your-policy-arn \
      --role-name rds-s3-import-role
   ```

   For Windows:

   ```
   aws iam attach-role-policy ^
      --policy-arn your-policy-arn ^
      --role-name rds-s3-import-role
   ```

1. Add the IAM role to the DB cluster\. You do so by using the AWS Management Console or AWS CLI, as described following\.

##### Console<a name="collapsible-section-1"></a>

**To add an IAM role for a PostgreSQL DB cluster using the console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console.aws.amazon.com/rds/](https://console.aws.amazon.com/rds/)\. 

1. Choose the PostgreSQL DB cluster name to display its details\.

1. On the **Connectivity & security** tab, in the **Manage IAM roles **section, choose the role to add under **Add IAM roles to this instance**\. 

1. Under **Feature**, choose **s3Import**\.

1. Choose **Add role**\.

##### AWS CLI<a name="collapsible-section-2"></a>

**To add an IAM role for a PostgreSQL DB cluster using the CLI**
+ Use the following command to add the role to the PostgreSQL DB cluster named `my-db-cluster`\. Replace *`your-role-arn`* with the role ARN that you noted in a previous step\. Use `s3Import` for the value of the `--feature-name` option\.   
**Example**  

  For Linux, macOS, or Unix:

  ```
  aws rds add-role-to-db-cluster \
     --db-cluster-identifier my-db-cluster \
     --feature-name s3Import \
     --role-arn your-role-arn   \
     --region your-region
  ```

  For Windows:

  ```
  aws rds add-role-to-db-cluster ^
     --db-cluster-identifier my-db-cluster ^
     --feature-name s3Import ^
     --role-arn your-role-arn ^
     --region your-region
  ```

#### Using Security Credentials to Access an Amazon S3 Bucket<a name="USER_PostgreSQL.S3Import.Credentials"></a>

If you prefer, you can use security credentials to provide access to an Amazon S3 bucket instead of providing access with an IAM role\. To do this, use the `credentials` parameter in the [aws\_s3\.table\_import\_from\_s3](#aws_s3.table_import_from_s3) function call\. 

The `credentials` parameter is a structure of type `aws_commons._aws_credentials_1`, which contains AWS credentials\. Use the [aws\_commons\.create\_aws\_credentials](#USER_PostgreSQL.S3Import.create_aws_credentials) function to set the access key and secret key in an `aws_commons._aws_credentials_1` structure, as shown following\. 

```
psql=> SELECT aws_commons.create_aws_credentials(
   'sample_access_key', 'sample_secret_key', '')
AS creds \gset
```

After creating the `aws_commons._aws_credentials_1 `structure, use the [aws\_s3\.table\_import\_from\_s3](#aws_s3.table_import_from_s3) function with the `credentials` parameter to import the data, as shown following\.

```
psql=> SELECT aws_s3.table_import_from_s3(
   't', '', '(format csv)',
   :'s3_uri', 
   :'creds'
);
```

Or you can include the [aws\_commons\.create\_aws\_credentials](#USER_PostgreSQL.S3Import.create_aws_credentials) function call inline within the `aws_s3.table_import_from_s3` function call\.

```
psql=> SELECT aws_s3.table_import_from_s3(
   't', '', '(format csv)',
   :'s3_uri', 
   aws_commons.create_aws_credentials('sample_access_key', 'sample_secret_key', '')
);
```

#### Troubleshooting Access to Amazon S3<a name="USER_PostgreSQL.S3Import.troubleshooting"></a>

If you encounter connection problems when attempting to import Amazon S3 file data, see the following for recommendations:
+ [Troubleshooting Amazon Aurora Identity and Access](security_iam_troubleshoot.md) 
+ [Troubleshooting Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/dev/troubleshooting.html)
+ [Troubleshooting Amazon S3 and IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/troubleshoot_iam-s3.html)

### Using the aws\_s3\.table\_import\_from\_s3 Function to Import Amazon S3 Data<a name="USER_PostgreSQL.S3Import.FileFormats"></a>

Import your Amazon S3 data by calling the [aws\_s3\.table\_import\_from\_s3](#aws_s3.table_import_from_s3) function\. 

**Note**  
The following examples use the IAM role method for providing access to the Amazon S3 bucket\. Thus, there are no credential parameters in the `aws_s3.table_import_from_s3` function calls\.

The following shows a typical PostgreSQL example using psql\.

```
psql=> SELECT aws_s3.table_import_from_s3(
   't1',
   '', 
   '(format csv)',
   :'s3_uri'
);
```

The parameters are the following:
+ `t1` – The name for the table in the PostgreSQL DB cluster to copy the data into\. 
+ `''` – An optional list of columns in the database table\. You can use this parameter to indicate which columns of the S3 data go in which table columns\. If no columns are specified, all the columns are copied to the table\. For an example of using a column list, see [Importing an Amazon S3 File That Uses a Custom Delimiter](#USER_PostgreSQL.S3Import.FileFormats.CustomDelimiter)\.
+ `(format csv)` – PostgreSQL COPY arguments\. The copy process uses the arguments and format of the [PostgreSQL COPY](https://www.postgresql.org/docs/current/sql-copy.html) command\. In the preceding example, the `COPY` command uses the comma\-separated value \(CSV\) file format to copy the data\. 
+  `s3_uri` – A structure that contains the information identifying the Amazon S3 file\. For an example of using the [aws\_commons\.create\_s3\_uri](#USER_PostgreSQL.S3Import.create_s3_uri) function to create an `s3_uri` structure, see [Overview of Importing Amazon S3 Data](#USER_PostgreSQL.S3Import.Overview)\.

For the full reference of this function, see [aws\_s3\.table\_import\_from\_s3](#aws_s3.table_import_from_s3)\.

The following examples show how to specify different kinds of files when importing Amazon S3 data\.

**Topics**
+ [Importing an Amazon S3 File That Uses a Custom Delimiter](#USER_PostgreSQL.S3Import.FileFormats.CustomDelimiter)
+ [Importing an Amazon S3 Compressed \(gzip\) File](#USER_PostgreSQL.S3Import.FileFormats.gzip)
+ [Importing an Encoded Amazon S3 File](#USER_PostgreSQL.S3Import.FileFormats.Encoded)

#### Importing an Amazon S3 File That Uses a Custom Delimiter<a name="USER_PostgreSQL.S3Import.FileFormats.CustomDelimiter"></a>

The following example shows how to import a file that uses a custom delimiter\. It also shows how to control where to put the data in the database table using the `column_list` parameter of the [aws\_s3\.table\_import\_from\_s3](#aws_s3.table_import_from_s3) function\. 

For this example, assume that the following information is organized into pipe\-delimited columns in the Amazon S3 file\.

```
1|foo1|bar1|elephant1
2|foo2|bar2|elephant2
3|foo3|bar3|elephant3
4|foo4|bar4|elephant4
...
```

**To import a file that uses a custom delimiter**

1. Create a table in the database for the imported data\.

   ```
   psql=> CREATE TABLE test (a text, b text, c text, d text, e text);
   CREATE TABLE
   ```

1. Use the following form of the [aws\_s3\.table\_import\_from\_s3](#aws_s3.table_import_from_s3) function to import data from the Amazon S3 file\. 

   You can include the [aws\_commons\.create\_s3\_uri](#USER_PostgreSQL.S3Import.create_s3_uri) function call inline within the `aws_s3.table_import_from_s3` function call to specify the file\. 

   ```
   psql=> SELECT aws_s3.table_import_from_s3(
      'test',
      'a,b,d,e',
      'DELIMITER ''|''', 
      aws_commons.create_s3_uri('sampleBucket', 'pipeDelimitedSampleFile', 'us-east-2')
   );
   ```

The data is now in the table in the following columns\.

```
psql=> SELECT * FROM test;
a | b | c | d | e 
---+------+---+---+------+-----------
1 | foo1 | | bar1 | elephant1
2 | foo2 | | bar2 | elephant2
3 | foo3 | | bar3 | elephant3
4 | foo4 | | bar4 | elephant4
```

#### Importing an Amazon S3 Compressed \(gzip\) File<a name="USER_PostgreSQL.S3Import.FileFormats.gzip"></a>

The following example shows how to import a file from Amazon S3 that is compressed with gzip\. 

Ensure that the file contains the following Amazon S3 metadata:
+ Key: `Content-Encoding`
+ Value: `gzip`

For more about adding these values to Amazon S3 metadata, see [How Do I Add Metadata to an S3 Object?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/add-object-metadata.html) in the *Amazon Simple Storage Service Console User Guide*\.

Import the gzip file into your Aurora PostgreSQL DB cluster as shown following\.

```
psql=> CREATE TABLE test_gzip(id int, a text, b text, c text, d text);
CREATE TABLE
psql=> SELECT aws_s3.table_import_from_s3(
 'test_gzip', '', '(format csv)',
 'myS3Bucket', 'test-data.gz', 'us-east-2'
);
```

#### Importing an Encoded Amazon S3 File<a name="USER_PostgreSQL.S3Import.FileFormats.Encoded"></a>

The following example shows how to import a file from Amazon S3 that has Windows\-1252 encoding\.

```
psql=> SELECT aws_s3.table_import_from_s3(
 'test_table', '', 'encoding ''WIN1252''',
 aws_commons.create_s3_uri('sampleBucket', 'SampleFile', 'us-east-2')
);
```

### Function Reference<a name="USER_PostgreSQL.S3Import.Reference"></a>

**Topics**
+ [aws\_s3\.table\_import\_from\_s3](#aws_s3.table_import_from_s3)
+ [aws\_commons\.create\_s3\_uri](#USER_PostgreSQL.S3Import.create_s3_uri)
+ [aws\_commons\.create\_aws\_credentials](#USER_PostgreSQL.S3Import.create_aws_credentials)

#### aws\_s3\.table\_import\_from\_s3<a name="aws_s3.table_import_from_s3"></a>

Imports Amazon S3 data into an Aurora PostgreSQL table\. The `aws_s3` extension provides the `aws_s3.table_import_from_s3` function\. 

##### Syntax<a name="aws_s3.table_import_from_s3-syntax"></a>

The three required parameters are `table_name`, `column_list` and `options`\. These identify the database table and specify how the data is copied into the table\. 

You can also use these parameters: 
+ The `s3_info` parameter specifies the Amazon S3 file to import\. When you use this parameter, access to Amazon S3 is provided by an IAM role for the PostgreSQL DB cluster\.

  ```
  aws_s3.table_import_from_s3 (
     table_name text, 
     column_list text, 
     options text, 
     s3_info aws_commons._s3_uri_1
  )
  ```
+ The `credentials` parameter specifies the credentials to access Amazon S3\. When you use this parameter, you don't use an IAM role\.

  ```
  aws_s3.table_import_from_s3 (
     table_name text, 
     column_list text, 
     options text, 
     s3_info aws_commons._s3_uri_1,
     credentials aws_commons._aws_credentials_1
  )
  ```

##### Parameters<a name="aws_s3.table_import_from_s3-parameters"></a>

 *table\_name*   
A required text string containing the name of the PostgreSQL database table to import the data into\. 

 *column\_list*   
A required text string containing an optional list of the PostgreSQL database table columns in which to copy the data\. If the string is empty, all columns of the table are used\. For an example, see [Importing an Amazon S3 File That Uses a Custom Delimiter](#USER_PostgreSQL.S3Import.FileFormats.CustomDelimiter)\.

 *options*   
A required text string containing arguments for the PostgreSQL `COPY` command\. These arguments specify how the data is to be copied into the PostgreSQL table\. For more details, see the [PostgreSQL COPY documentation](https://www.postgresql.org/docs/current/sql-copy.html)\.

 *s3\_info*   
An `aws_commons._s3_uri_1` composite type containing the following information about the S3 object:  
+ `bucket` – The name of the Amazon S3 bucket containing the file\.
+ `file_path` – The Amazon S3 path of the file\.
+ `region` – The AWS Region that the file is in\. For a listing of AWS Region names and associated values, see [ Regions and Availability Zones ](Concepts.RegionsAndAvailabilityZones.md)\.

 *credentials*   
An `aws_commons._aws_credentials_1` composite type containing the following credentials to use for the import operation:  
+ Access key
+ Secret key
+ Session token
For information about creating an `aws_commons._aws_credentials_1` composite structure, see [aws\_commons\.create\_aws\_credentials](#USER_PostgreSQL.S3Import.create_aws_credentials)\.

##### Alternate Syntax<a name="aws_s3.table_import_from_s3-alternative-syntax"></a>

To help with testing, you can use an expanded set of parameters instead of the `s3_info` and `credentials` parameters\. Following are additional syntax variations for the `aws_s3.table_import_from_s3` function: 
+ Instead of using the `s3_info` parameter to identify an Amazon S3 file, use the combination of the `bucket`, `file_path`, and `region` parameters\. With this form of the function, access to Amazon S3 is provided by an IAM role on the PostgreSQL DB instance\.

  ```
  aws_s3.table_import_from_s3 (
     table_name text, 
     column_list text, 
     options text, 
     bucket text, 
     file_path text, 
     region text 
  )
  ```
+ Instead of using the `credentials` parameter to specify Amazon S3 access, use the combination of the `access_key`, `session_key`, and `session_token` parameters\.

  ```
  aws_s3.table_import_from_s3 (
     table_name text, 
     column_list text, 
     options text, 
     bucket text, 
     file_path text, 
     region text, 
     access_key text, 
     secret_key text, 
     session_token text 
  )
  ```

##### Alternate Parameters<a name="aws_s3.table_import_from_s3-alternative-parameters"></a>

*bucket*  
A text string containing the name of the Amazon S3 bucket that contains the file\. 

*file\_path*  
A text string containing the Amazon S3 path of the file\. 

*region*  
A text string containing the AWS Region that the file is in\. For a listing of AWS Region names and associated values, see [ Regions and Availability Zones ](Concepts.RegionsAndAvailabilityZones.md)\.

*access\_key*  
A text string containing the access key to use for the import operation\. The default is NULL\.

*secret\_key*  
A text string containing the secret key to use for the import operation\. The default is NULL\.

*session\_token*  
\(Optional\) A text string containing the session key to use for the import operation\. The default is NULL\.

#### aws\_commons\.create\_s3\_uri<a name="USER_PostgreSQL.S3Import.create_s3_uri"></a>

Creates an `aws_commons._s3_uri_1` structure to hold Amazon S3 file information\. Use the results of the `aws_commons.create_s3_uri` function in the `s3_info` parameter of the [aws\_s3\.table\_import\_from\_s3](#aws_s3.table_import_from_s3) function\. 

##### Syntax<a name="USER_PostgreSQL.S3Import.create_s3_uri-syntax"></a>

```
aws_commons.create_s3_uri(
   bucket text,
   file_path text,
   region text
)
```

##### Parameters<a name="USER_PostgreSQL.S3Import.create_s3_uri-parameters"></a>

*bucket*  
A required text string containing the Amazon S3 bucket name for the file\.

*file\_path*  
A required text string containing the Amazon S3 path of the file\.

*region*  
A required text string containing the AWS Region that the file is in\. For a listing of AWS Region names and associated values, see [ Regions and Availability Zones ](Concepts.RegionsAndAvailabilityZones.md)\.

#### aws\_commons\.create\_aws\_credentials<a name="USER_PostgreSQL.S3Import.create_aws_credentials"></a>

Sets an access key and secret key in an `aws_commons._aws_credentials_1` structure\. Use the results of the `aws_commons.create_aws_credentials` function in the `credentials` parameter of the [aws\_s3\.table\_import\_from\_s3](#aws_s3.table_import_from_s3) function\. 

##### Syntax<a name="USER_PostgreSQL.S3Import.create_aws_credentials-syntax"></a>

```
aws_commons.create_aws_credentials(
   access_key text,
   secret_key text,
   session_token text
)
```

##### Parameters<a name="USER_PostgreSQL.S3Import.create_aws_credentials-parameters"></a>

*access\_key*  
A required text string containing the access key to use for importing an Amazon S3 file\. The default is NULL\.

*secret\_key*  
A required text string containing the secret key to use for importing an Amazon S3 file\. The default is NULL\.

*session\_token*  
An optional text string containing the session token to use for importing an Amazon S3 file\. The default is NULL\. If you provide an optional `session_token`, you can use temporary credentials\.