# Migrating data to Amazon Aurora with PostgreSQL compatibility<a name="AuroraPostgreSQL.Migrating"></a>

You have several options for migrating data from your existing database to an Amazon Aurora PostgreSQL\-Compatible Edition DB cluster\. Your migration options also depend on the database that you are migrating from and the size of the data that you are migrating\. Following are your options:

**[ Migrating an RDS for PostgreSQL DB instance using a snapshot](#AuroraPostgreSQL.Migrating.RDSPostgreSQL.Import.Console)**  
You can migrate data directly from an RDS for PostgreSQL DB snapshot to an Aurora PostgreSQL DB cluster\.

**[ Migrating an RDS for PostgreSQL DB instance using an Aurora read replica](#AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica)**  
You can also migrate from an RDS for PostgreSQL DB instance by creating an Aurora PostgreSQL read replica of an RDS for PostgreSQL DB instance\. When the replica lag between the RDS for PostgreSQL DB instance and the Aurora PostgreSQL read replica is zero, you can stop replication\. At this point, you can make the Aurora read replica a standalone Aurora PostgreSQL DB cluster for reading and writing\.

**[Importing S3 data into Aurora PostgreSQL ](#USER_PostgreSQL.S3Import) **  
You can migrate data by importing it from Amazon S3 into a table belonging to an Aurora PostgreSQL DB cluster\. 

**Migrating from a database that is not PostgreSQL\-compatible**  
You can use AWS Database Migration Service \(AWS DMS\) to migrate data from a database that is not PostgreSQL\-compatible\. For more information on AWS DMS, see [What is AWS Database Migration Service?](https://docs.aws.amazon.com/dms/latest/userguide/Welcome.html) in the *AWS Database Migration Service User Guide*\. 

For a list of AWS Regions where Aurora is available, see [Amazon Aurora](https://docs.aws.amazon.com/general/latest/gr/rande.html#aurora) in the *AWS General Reference*\.

**Important**  
If you plan to migrate an RDS for PostgreSQL DB instance to an Aurora PostgreSQL DB cluster in the near future, we strongly recommend that you disable auto minor version upgrades for the DB instance early in the migration planning phase\. Migration to Aurora PostgreSQL might be delayed if the RDS for PostgreSQL version isn't yet supported by Aurora PostgreSQL\. For information about Aurora PostgreSQL versions, see [ Engine versions for Amazon Aurora PostgreSQL](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraPostgreSQL.Updates.20180305.html)\.

## Migrating a snapshot of an RDS for PostgreSQL DB instance to an Aurora PostgreSQL DB cluster<a name="AuroraPostgreSQL.Migrating.RDSPostgreSQL.Import.Console"></a>

To create an Aurora PostgreSQL DB cluster, you can migrate a DB snapshot of an RDS for PostgreSQL DB instance\. The new Aurora PostgreSQL DB cluster is populated with the data from the original RDS for PostgreSQL DB instance\. For information about creating a DB snapshot, see [Creating a DB snapshot](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CreateSnapshot.html)\.

In some cases, the DB snapshot might not be in the AWS Region where you want to locate your data\. If so, use the Amazon RDS console to copy the DB snapshot to that AWS Region\. For information about copying a DB snapshot, see [Copying a DB snapshot](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CopySnapshot.html)\.

You can migrate RDS for PostgreSQL snapshots that are compatible with the Aurora PostgreSQL versions available in the given AWS Region\. For example, a snapshot from an RDS for PostgreSQL 11\.1 DB instance can be migrated to Aurora PostgreSQL version 11\.4, 11\.7, 11\.8, or 11\.9 in the US West \(N\. California\) Region\. An RDS PostgreSQL 10\.11 snapshot can be migrated to Aurora PostgreSQL 10\.11, 10\.12, 10\.13, and 10\.14\. In other words, the RDS for PostgreSQL snapshot must use the same or a lower minor version as the Aurora PostgreSQL\. 

When you migrate the DB snapshot by using the console, the console takes the actions necessary to create both the DB cluster and the primary instance\.

You can also choose for your new Aurora PostgreSQL DB cluster to be encrypted at rest by using an AWS Key Management Service \(AWS KMS\) customer master key \(CMK\)\. This option is available only for unencrypted DB snapshots\.

**To migrate a PostgreSQL DB snapshot by using the RDS console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose **Snapshots**\.

1. On the **Snapshots** page, choose the RDS for PostgreSQL snapshot that you want to migrate into an Aurora PostgreSQL DB cluster\.

1. Choose **Actions** then choose **Migrate snapshot**\.

1. Set the following values on the **Migrate database** page:
   + **DB engine version**: Choose a DB engine version you want to use for the new migrated instance\.
   + **DB instance identifier**: Enter a name for the DB cluster that is unique for your account in the AWS Region that you chose\. This identifier is used in the endpoint addresses for the instances in your DB cluster\. You might choose to add some intelligence to the name, such as including the AWS Region and DB engine that you chose, for example **aurora\-cluster1**\.

     The DB instance identifier has the following constraints:
     + It must contain 1–63 alphanumeric characters or hyphens\.
     + Its first character must be a letter\.
     + It can't end with a hyphen or contain two consecutive hyphens\.
     + It must be unique for all DB instances per AWS account, per AWS Region\.
   + **DB instance class**: Choose a DB instance class that has the required storage and capacity for your database, for example `db.r3.large`\. Aurora cluster volumes automatically grow as the amount of data in your database increases\. 128 tebibytes \(TiB\) So you only need to choose a DB instance class that meets your current storage requirements\. For more information, see [Overview of Aurora storage](Aurora.Overview.StorageReliability.md#Aurora.Overview.Storage)\.
   + **Virtual private cloud \(VPC\)**: If you have an existing VPC, then you can use that VPC with your Aurora PostgreSQL DB cluster by choosing your VPC identifier, for example `vpc-a464d1c1`\. For information on using an existing VPC, see [How to create a VPC for use with Amazon Aurora](Aurora.CreateVPC.md)\.

     Otherwise, you can choose to have Amazon RDS create a VPC for you by choosing **Create new VPC**\. 
   + **Subnet group**: If you have an existing subnet group, then you can use that subnet group with your Aurora PostgreSQL DB cluster by choosing your subnet group identifier, for example `gs-subnet-group1`\.
   + **Public access**: Choose **No** to specify that instances in your DB cluster can only be accessed by resources inside of your VPC\. Choose **Yes** to specify that instances in your DB cluster can be accessed by resources on the public network\.
**Note**  
Your production DB cluster might not need to be in a public subnet, because only your application servers require access to your DB cluster\. If your DB cluster doesn't need to be in a public subnet, set **Public access** to **No**\.
   + **VPC security group**: Choose a VPC security group to allow access to your database\.
   + **Availability Zone**: Choose the Availability Zone to host the primary instance for your Aurora PostgreSQL DB cluster\. To have Amazon RDS choose an Availability Zone for you, choose **No preference**\.
   + **Database port**: Enter the default port to be used when connecting to instances in the Aurora PostgreSQL DB cluster\. The default is `5432`\.
**Note**  
You might be behind a corporate firewall that doesn't allow access to default ports such as the PostgreSQL default port, 5432\. In this case, provide a port value that your corporate firewall allows\. Remember that port value later when you connect to the Aurora PostgreSQL DB cluster\.
   + **Enable Encryption**: Choose **Enable Encryption** for your new Aurora PostgreSQL DB cluster to be encrypted at rest\. Also choose an AWS KMS customer master key \(CMK\) as the **Master key** value\.
   + **Auto minor version upgrade**: Choose **Enable auto minor version upgrade** to enable your Aurora PostgreSQL DB cluster to receive minor PostgreSQL DB engine version upgrades automatically when they become available\.

     The **Auto minor version upgrade** option only applies to upgrades to PostgreSQL minor engine versions for your Aurora PostgreSQL DB cluster\. It doesn't apply to regular patches applied to maintain system stability\.

1. Choose **Migrate** to migrate your DB snapshot\. 

1. Choose **Databases** to see the new DB cluster\. Choose the new DB cluster to monitor the progress of the migration\. On the **Connectivity & security** tab, you can find the cluster endpoint to use for connecting to the primary writer instance of the DB cluster\. For more information on connecting to an Aurora PostgreSQL DB cluster, see [Connecting to an Amazon Aurora DB cluster](Aurora.Connecting.md)\. 

## Migrating data from an RDS for PostgreSQL DB instance to an Aurora PostgreSQL DB cluster using an Aurora read replica<a name="AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica"></a>

You can migrate from an RDS for PostgreSQL DB instance to an Aurora PostgreSQL DB cluster by using an Aurora read replica\. When you need to migrate from an RDS for PostgreSQL DB instance to an Aurora PostgreSQL DB cluster, we recommend using this approach\. 

In this case, Amazon RDS uses the PostgreSQL DB engine's streaming replication functionality to create a special type of DB cluster for the source PostgreSQL DB instance\. This type of DB cluster is called an Aurora read replica\. Updates made to the source RDS for PostgreSQL DB instance are asynchronously replicated to the Aurora read replica\. 

**Topics**
+ [Overview of migrating data by using an Aurora read replica](#AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica.Intro)
+ [Preparing to migrate data by using an Aurora read replica](#AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica.Prepare)
+ [Creating an Aurora read replica](#AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica.Create)
+ [Promoting an Aurora read replica](#AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica.Promote)

### Overview of migrating data by using an Aurora read replica<a name="AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica.Intro"></a>

To migrate from an RDS for PostgreSQL DB instance to an Aurora PostgreSQL DB cluster, we recommend creating an Aurora read replica of your source RDS for PostgreSQL DB instance\. When the replica lag between the RDS for PostgreSQL DB instance and the Aurora PostgreSQL Read Replica is zero, you can stop replication\. At this point, you can promote the Aurora read replica to be a standalone Aurora PostgreSQL DB cluster\. This standalone DB cluster can then accept write loads\. 

Be prepared for migration to take a while, roughly several hours per tebibyte \(TiB\) of data\. While the migration is in progress, your RDS for PostgreSQL instance accumulates write ahead log \(WAL\) segments\. Make sure that your Amazon RDS instance has sufficient storage capacity for these segments\. 

When you create an Aurora read replica of an RDS for PostgreSQL DB instance, Amazon RDS creates a DB snapshot of your source RDS for PostgreSQL DB instance\. This snapshot is private to Amazon RDS and incurs no charges\. Amazon RDS then migrates the data from the DB snapshot to the Aurora read replica\. After the DB snapshot data is migrated to the new Aurora PostgreSQL DB cluster, RDS starts replication between your RDS for PostgreSQL DB instance and the Aurora PostgreSQL DB cluster\. 

You can only have one Aurora read replica for an RDS for PostgreSQL DB instance\. If you try to create an Aurora read replica for your RDS for PostgreSQL instance and you already have an Aurora read replica or a cross\-region read replica, the request is rejected\. 

**Note**  
Replication issues can arise due to feature differences between Aurora PostgreSQL and the PostgreSQL engine version of your RDS for PostgreSQL DB instance that is the replication source\. You can replicate only from an RDS for PostgreSQL instance that is compatible with the Aurora PostgreSQL version in question\. For example, if the supported Aurora PostgreSQL version is 12\.4, the RDS for PostgreSQL DB instance must be running version 12\.2 or greater\. If you encounter an error, you can find help in the [Amazon RDS community forum](https://forums.aws.amazon.com/forum.jspa?forumID=60) or by contacting AWS Support\.

For more information on PostgreSQL read replicas, see [Working with read replicas](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ReadRepl.html) in the *Amazon RDS User Guide*\.

### Preparing to migrate data by using an Aurora read replica<a name="AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica.Prepare"></a>

Before you migrate data from your RDS for PostgreSQL instance to an Aurora PostgreSQL cluster, make sure that your instance has sufficient storage capacity\. This storage capacity is for the write ahead log \(WAL\) segments that accumulate during the migration\. There are several metrics to check for this, described following\. 


| Metric | Description | 
| --- | --- | 
|  `FreeStorageSpace`  |  The available storage space\. Units: Bytes  | 
|  `OldestReplicationSlotLag`  |  The size of the lag for WAL data in the replica that is lagging the most\. Units: Megabytes  | 
|  `RDSToAuroraPostgreSQLReplicaLag`  |  The amount of time in seconds that an Aurora PostgreSQL DB cluster lags behind the source RDS DB instance\.  | 
|  `TransactionLogsDiskUsage`  |  The disk space used by the transaction logs\. Units: Megabytes  | 

For more information about monitoring your RDS instance, see [Monitoring](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Monitoring.html) in the *Amazon RDS User Guide*\.

### Creating an Aurora read replica<a name="AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica.Create"></a>

You can create an Aurora read replica for an RDS for PostgreSQL DB instance by using the console or the AWS CLI\.

#### Console<a name="AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica.Create.Console"></a>

**To create an Aurora read replica from a source PostgreSQL DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\. 

1. Choose the RDS for PostgreSQL DB instance that you want to use as the source for your Aurora read replica, and choose **Create Aurora read replica** for **Actions**\.  
![\[Create Aurora read replica\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/Aurorapgres-migrate.png)

1. Choose the DB cluster specifications that you want to use for the Aurora read replica, as described in the following table\.     
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraPostgreSQL.Migrating.html)

1. Choose **Create read replica**\.

#### AWS CLI<a name="AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica.Create.CLI"></a>

To create an Aurora read replica from a source RDS for PostgreSQL DB instance, use the [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) and [ create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) AWS CLI commands to create a new Aurora PostgreSQL DB cluster\. When you call the create\-db\-cluster command, include the `--replication-source-identifier` parameter to identify the Amazon Resource Name \(ARN\) for the source RDS for PostgreSQL DB instance\. For more information about Amazon RDS ARNs, see [Amazon Relational Database Service \(Amazon RDS\)](https://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html#arn-syntax-rds) in the *AWS General Reference*\. 

Don't specify the master user name, master password, or database name\. The Aurora read replica uses the same master user name, master password, and database name as the source RDS for PostgreSQL DB instance\. 

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

If you use the console to create an Aurora read replica, then RDS automatically creates the primary instance for your DB cluster Aurora Read Replica\. If you use the CLI to create an Aurora read replica, you must explicitly create the primary instance for your DB cluster\. The primary instance is the first instance that is created in a DB cluster\.

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
    --db-instance-class myinstanceclass \
    --db-instance-identifier myreadreplicainstance \
    --engine aurora-postgresql
```
For Windows:  

```
aws rds create-db-instance ^
    --db-cluster-identifier myreadreplicacluster ^
    --db-instance-class myinstanceclass ^
    --db-instance-identifier myreadreplicainstance ^
    --engine aurora-postgresql
```

#### RDS API<a name="Aurora.Migration.RDSPostgreSQL.Create.API"></a>

To create an Aurora read replica from a source RDS for PostgreSQL DB instance, use the RDS API operations [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) and [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) to create a new Aurora DB cluster and primary instance\. Don't specify the master user name, master password, or database name\. The Aurora read replica uses the same master user name, master password, and database name as the source RDS for PostgreSQL DB instance\. 

You can create a new Aurora DB cluster for an Aurora read replica from a source RDS for PostgreSQL DB instance\. To do so, use the RDS API operation [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) with the following parameters:
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

See an example with the RDS API operation [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html)\.

If you use the console to create an Aurora read replica, then Amazon RDS automatically creates the primary instance for your DB cluster Aurora Read Replica\. If you use the CLI to create an Aurora read replica, you must explicitly create the primary instance for your DB cluster\. The primary instance is the first instance that is created in a DB cluster\.

You can create a primary instance for your DB cluster by using the RDS API operation [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) with the following parameters:
+ `DBClusterIdentifier`

  The name of your DB cluster\.
+ `DBInstanceClass`

  The name of the DB instance class to use for your primary instance\.
+ `DBInstanceIdentifier`

  The name of your primary instance\.
+ `Engine=aurora-postgresql`

  The name of the engine to use\.

See an example with the RDS API operation [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html)\.

### Promoting an Aurora read replica<a name="AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica.Promote"></a>

After migration completes, you can promote the Aurora read replica to a standalone DB cluster\. You then direct your client applications to the endpoint for the Aurora read replica\. For more information on the Aurora endpoints, see [Amazon Aurora connection management](Aurora.Overview.Endpoints.md)\. Promotion should complete fairly quickly\. You can't delete the primary PostgreSQL DB instance or unlink the DB instance and the Aurora read replica until the promotion is complete\.

Before you promote your Aurora read replica, stop any transactions from being written to the source RDS for PostgreSQL DB instance\. Then wait for the replica lag on the Aurora read replica to reach zero\. For more information, see [Monitoring Aurora PostgreSQL replication](AuroraPostgreSQL.Replication.md#AuroraPostgreSQL.Replication.Monitoring) and [Monitoring Amazon Aurora metrics with Amazon CloudWatch](Aurora.Monitoring.md)\.

#### Console<a name="AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica.Promote.Console"></a>

**To promote an Aurora read replica to an Aurora DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\. 

1. Choose the DB instance for the Aurora read replica\. 

1. For **Actions**, choose **Promote**\. 

1. Choose **Promote read replica**\.

#### AWS CLI<a name="AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica.Promote.CLI"></a>

To promote an Aurora read replica to a stand\-alone DB cluster, use the [https://docs.aws.amazon.com/cli/latest/reference/rds/promote-read-replica-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/promote-read-replica-db-cluster.html) AWS CLI command\. 

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

#### RDS API<a name="AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica.Promote.API"></a>

To promote an Aurora read replica to a stand\-alone DB cluster, use the RDS API operation [PromoteReadReplicaDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_PromoteReadReplicaDBCluster.html)\. 

After you promote your read replica, confirm that the promotion has completed by using the following procedure\.

**To confirm that the Aurora read replica was promoted**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Events**\.

1. On the **Events** page, verify that there is an event, `Promoted read replica cluster to a stand-alone database cluster` for the cluster that you promoted\.

After promotion is complete, the primary RDS for PostgreSQL DB instance and the Aurora Read Replica are unlinked\. At this point, you can safely delete the DB instance if you want to\.

## Importing Amazon S3 data into an Aurora PostgreSQL DB cluster<a name="USER_PostgreSQL.S3Import"></a>

You can import data from Amazon S3 into a table belonging to an Aurora PostgreSQL DB cluster\. To do this, you use the `aws_s3` PostgreSQL extension that Aurora PostgreSQL provides\. 

**Note**  
To import from Amazon S3 into Aurora PostgreSQL, your database must be running PostgreSQL version 10\.7 or later\. 

For more information on storing data with Amazon S3, see [Create a bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html) in the *Amazon Simple Storage Service Getting Started Guide*\. For instructions on how to upload a file to an Amazon S3 bucket, see [Add an object to a bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg/PuttingAnObjectInABucket.html) in the *Amazon Simple Storage Service Getting Started Guide*\.

**Topics**
+ [Overview of importing Amazon S3 data](#USER_PostgreSQL.S3Import.Overview)
+ [Setting up access to an Amazon S3 bucket](#USER_PostgreSQL.S3Import.AccessPermission)
+ [Using the aws\_s3\.table\_import\_from\_s3 function to import Amazon S3 data](#USER_PostgreSQL.S3Import.FileFormats)
+ [Function reference](#USER_PostgreSQL.S3Import.Reference)

### Overview of importing Amazon S3 data<a name="USER_PostgreSQL.S3Import.Overview"></a>

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

      To find how to get this information, see [View an object](https://docs.aws.amazon.com/AmazonS3/latest/gsg/OpeningAnObject.html) in the *Amazon Simple Storage Service Getting Started Guide*\. You can confirm the information by using the AWS CLI command `aws s3 cp`\. If the information is correct, this command downloads a copy of the Amazon S3 file\. 

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

   To import data from an Amazon S3 file, give the Aurora PostgreSQL DB cluster permission to access the Amazon S3 bucket the file is in\. To do this, you use either an AWS Identity and Access Management \(IAM\) role or security credentials\. For more information, see [Setting up access to an Amazon S3 bucket](#USER_PostgreSQL.S3Import.AccessPermission)\.

1. Import the Amazon S3 data by calling the `aws_s3.table_import_from_s3` function\.

   After you complete the previous preparation tasks, use the [aws\_s3\.table\_import\_from\_s3](#aws_s3.table_import_from_s3) function to import the Amazon S3 data\. For more information, see [Using the aws\_s3\.table\_import\_from\_s3 function to import Amazon S3 data](#USER_PostgreSQL.S3Import.FileFormats)\.

### Setting up access to an Amazon S3 bucket<a name="USER_PostgreSQL.S3Import.AccessPermission"></a>

To import data from an Amazon S3 file, give the Aurora PostgreSQL DB cluster permission to access the Amazon S3 bucket the file is in\. You provide access to an Amazon S3 bucket in one of two ways, as described in the following topics\.

**Topics**
+ [Using an IAM role to access an Amazon S3 bucket](#USER_PostgreSQL.S3Import.ARNRole)
+ [Using security credentials to access an Amazon S3 bucket](#USER_PostgreSQL.S3Import.Credentials)
+ [Troubleshooting access to Amazon S3](#USER_PostgreSQL.S3Import.troubleshooting)

#### Using an IAM role to access an Amazon S3 bucket<a name="USER_PostgreSQL.S3Import.ARNRole"></a>

Before you load data from an Amazon S3 file, give your Aurora PostgreSQL DB cluster permission to access the Amazon S3 bucket the file is in\. This way, you don't have to manage additional credential information or provide it in the [aws\_s3\.table\_import\_from\_s3](#aws_s3.table_import_from_s3) function call\.

To do this, create an IAM policy that provides access to the Amazon S3 bucket\. Create an IAM role and attach the policy to the role\. Then assign the IAM role to your DB cluster\. 

**To give an Aurora PostgreSQL DB cluster access to Amazon S3 through an IAM role**

1. Create an IAM policy\. 

   This policy provides the bucket and object permissions that allow your Aurora PostgreSQL DB cluster to access Amazon S3\. 

   Include in the policy the following required actions to allow the transfer of files from an Amazon S3 bucket to Aurora PostgreSQL: 
   + `s3:GetObject` 
   + `s3:ListBucket` 

   Include in the policy the following resources to identify the Amazon S3 bucket and objects in the bucket\. This shows the Amazon Resource Name \(ARN\) format for accessing Amazon S3\.
   + arn:aws:s3:::*your\-s3\-bucket*
   + arn:aws:s3:::*your\-s3\-bucket*/\*

   For more information on creating an IAM policy for Aurora PostgreSQL, see [Creating and using an IAM policy for IAM database access](UsingWithRDS.IAMDBAuth.IAMPolicy.md)\. See also [Tutorial: Create and attach your first customer managed policy](https://docs.aws.amazon.com/IAM/latest/UserGuide/tutorial_managed-policies.html) in the *IAM User Guide*\.

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

1. Create an IAM role\. 

   You do this so Aurora PostgreSQL can assume this IAM role on your behalf to access your Amazon S3 buckets\. For more information, see [Creating a role to delegate permissions to an IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-user.html) in the *IAM User Guide*\.

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

1. Add the IAM role to the DB cluster\. 

   You do so by using the AWS Management Console or AWS CLI, as described following\. 
**Note**  
You can't associate an IAM role with an Aurora Serverless DB cluster\. For more information, see [Using Amazon Aurora Serverless v1](aurora-serverless.md)\.  
Also, be sure the database you use doesn't have any restrictions noted in [Importing Amazon S3 data into an Aurora PostgreSQL DB cluster](#USER_PostgreSQL.S3Import)\. 

##### Console<a name="collapsible-section-1"></a>

**To add an IAM role for a PostgreSQL DB cluster using the console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose the PostgreSQL DB cluster name to display its details\.

1. On the **Connectivity & security** tab, in the **Manage IAM roles **section, choose the role to add under **Add IAM roles to this cluster **\. 

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

##### RDS API<a name="collapsible-section-3"></a>

To add an IAM role for a PostgreSQL DB cluster using the Amazon RDS API, call the [AddRoleToDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_AddRoleToDBCluster.html) operation\. 

#### Using security credentials to access an Amazon S3 bucket<a name="USER_PostgreSQL.S3Import.Credentials"></a>

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

#### Troubleshooting access to Amazon S3<a name="USER_PostgreSQL.S3Import.troubleshooting"></a>

If you encounter connection problems when attempting to import Amazon S3 file data, see the following for recommendations:
+ [Troubleshooting Amazon Aurora identity and access](security_iam_troubleshoot.md)
+ [Troubleshooting Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/dev/troubleshooting.html) in the *Amazon Simple Storage Service Developer Guide*
+ [Troubleshooting Amazon S3 and IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/troubleshoot_iam-s3.html) in the *IAM User Guide*

### Using the aws\_s3\.table\_import\_from\_s3 function to import Amazon S3 data<a name="USER_PostgreSQL.S3Import.FileFormats"></a>

Import your Amazon S3 data by calling the [aws\_s3\.table\_import\_from\_s3](#aws_s3.table_import_from_s3) function\. 

**Note**  
The following examples use the IAM role method for providing access to the Amazon S3 bucket\. Thus, the `aws_s3.table_import_from_s3` function calls don't include credential parameters\.

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
+ `''` – An optional list of columns in the database table\. You can use this parameter to indicate which columns of the S3 data go in which table columns\. If no columns are specified, all the columns are copied to the table\. For an example of using a column list, see [Importing an Amazon S3 file that uses a custom delimiter](#USER_PostgreSQL.S3Import.FileFormats.CustomDelimiter)\.
+ `(format csv)` – PostgreSQL COPY arguments\. The copy process uses the arguments and format of the [PostgreSQL COPY](https://www.postgresql.org/docs/current/sql-copy.html) command\. In the preceding example, the `COPY` command uses the comma\-separated value \(CSV\) file format to copy the data\. 
+  `s3_uri` – A structure that contains the information identifying the Amazon S3 file\. For an example of using the [aws\_commons\.create\_s3\_uri](#USER_PostgreSQL.S3Import.create_s3_uri) function to create an `s3_uri` structure, see [Overview of importing Amazon S3 data](#USER_PostgreSQL.S3Import.Overview)\.

The return value is text\. For the full reference of this function, see [aws\_s3\.table\_import\_from\_s3](#aws_s3.table_import_from_s3)\.

The following examples show how to specify different kinds of files when importing Amazon S3 data\.

**Topics**
+ [Importing an Amazon S3 file that uses a custom delimiter](#USER_PostgreSQL.S3Import.FileFormats.CustomDelimiter)
+ [Importing an Amazon S3 compressed \(gzip\) file](#USER_PostgreSQL.S3Import.FileFormats.gzip)
+ [Importing an encoded Amazon S3 file](#USER_PostgreSQL.S3Import.FileFormats.Encoded)

#### Importing an Amazon S3 file that uses a custom delimiter<a name="USER_PostgreSQL.S3Import.FileFormats.CustomDelimiter"></a>

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

#### Importing an Amazon S3 compressed \(gzip\) file<a name="USER_PostgreSQL.S3Import.FileFormats.gzip"></a>

The following example shows how to import a file from Amazon S3 that is compressed with gzip\. 

Ensure that the file contains the following Amazon S3 metadata:
+ Key: `Content-Encoding`
+ Value: `gzip`

For more about adding these values to Amazon S3 metadata, see [How do I add metadata to an S3 object?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/add-object-metadata.html) in the *Amazon Simple Storage Service Console User Guide*\.

Import the gzip file into your Aurora PostgreSQL DB cluster as shown following\.

```
psql=> CREATE TABLE test_gzip(id int, a text, b text, c text, d text);
CREATE TABLE
psql=> SELECT aws_s3.table_import_from_s3(
 'test_gzip', '', '(format csv)',
 'myS3Bucket', 'test-data.gz', 'us-east-2'
);
```

#### Importing an encoded Amazon S3 file<a name="USER_PostgreSQL.S3Import.FileFormats.Encoded"></a>

The following example shows how to import a file from Amazon S3 that has Windows\-1252 encoding\.

```
psql=> SELECT aws_s3.table_import_from_s3(
 'test_table', '', 'encoding ''WIN1252''',
 aws_commons.create_s3_uri('sampleBucket', 'SampleFile', 'us-east-2')
);
```

### Function reference<a name="USER_PostgreSQL.S3Import.Reference"></a>

**Topics**
+ [aws\_s3\.table\_import\_from\_s3](#aws_s3.table_import_from_s3)
+ [aws\_commons\.create\_s3\_uri](#USER_PostgreSQL.S3Import.create_s3_uri)
+ [aws\_commons\.create\_aws\_credentials](#USER_PostgreSQL.S3Import.create_aws_credentials)

#### aws\_s3\.table\_import\_from\_s3<a name="aws_s3.table_import_from_s3"></a>

Imports Amazon S3 data into an Aurora PostgreSQL table\. The `aws_s3` extension provides the `aws_s3.table_import_from_s3` function\. The return value is text\.

##### Syntax<a name="aws_s3.table_import_from_s3-syntax"></a>

The required parameters are `table_name`, `column_list` and `options`\. These identify the database table and specify how the data is copied into the table\. 

You can also use the following parameters: 
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
A required text string containing an optional list of the PostgreSQL database table columns in which to copy the data\. If the string is empty, all columns of the table are used\. For an example, see [Importing an Amazon S3 file that uses a custom delimiter](#USER_PostgreSQL.S3Import.FileFormats.CustomDelimiter)\.

 *options*   
A required text string containing arguments for the PostgreSQL `COPY` command\. These arguments specify how the data is to be copied into the PostgreSQL table\. For more details, see the [PostgreSQL COPY documentation](https://www.postgresql.org/docs/current/sql-copy.html)\.

 *s3\_info*   
An `aws_commons._s3_uri_1` composite type containing the following information about the S3 object:  
+ `bucket` – The name of the Amazon S3 bucket containing the file\.
+ `file_path` – The Amazon S3 file name including the path of the file\.
+ `region` – The AWS Region that the file is in\. For a listing of AWS Region names and associated values, see [ Regions and Availability Zones ](Concepts.RegionsAndAvailabilityZones.md)\.

 *credentials*   
An `aws_commons._aws_credentials_1` composite type containing the following credentials to use for the import operation:  
+ Access key
+ Secret key
+ Session token
For information about creating an `aws_commons._aws_credentials_1` composite structure, see [aws\_commons\.create\_aws\_credentials](#USER_PostgreSQL.S3Import.create_aws_credentials)\.

##### Alternate syntax<a name="aws_s3.table_import_from_s3-alternative-syntax"></a>

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

##### Alternate parameters<a name="aws_s3.table_import_from_s3-alternative-parameters"></a>

*bucket*  
A text string containing the name of the Amazon S3 bucket that contains the file\. 

*file\_path*  
A text string containing the Amazon S3 file name including the path of the file\. 

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
A required text string containing the Amazon S3 file name including the path of the file\.

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