# Migrating data to Amazon Aurora with PostgreSQL compatibility<a name="AuroraPostgreSQL.Migrating"></a>

You have several options for migrating data from your existing database to an Amazon Aurora PostgreSQL\-Compatible Edition DB cluster\. Your migration options also depend on the database that you are migrating from and the size of the data that you are migrating\. Following are your options:

**[ Migrating an RDS for PostgreSQL DB instance using a snapshot](#AuroraPostgreSQL.Migrating.RDSPostgreSQL.Import.Console)**  
You can migrate data directly from an RDS for PostgreSQL DB snapshot to an Aurora PostgreSQL DB cluster\.

**[ Migrating an RDS for PostgreSQL DB instance using an Aurora read replica](#AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica)**  
You can also migrate from an RDS for PostgreSQL DB instance by creating an Aurora PostgreSQL read replica of an RDS for PostgreSQL DB instance\. When the replica lag between the RDS for PostgreSQL DB instance and the Aurora PostgreSQL read replica is zero, you can stop replication\. At this point, you can make the Aurora read replica a standalone Aurora PostgreSQL DB cluster for reading and writing\.

**[Importing S3 data into Aurora PostgreSQL ](USER_PostgreSQL.S3Import.md) **  
You can migrate data by importing it from Amazon S3 into a table belonging to an Aurora PostgreSQL DB cluster\. 

**Migrating from a database that is not PostgreSQL\-compatible**  
You can use AWS Database Migration Service \(AWS DMS\) to migrate data from a database that is not PostgreSQL\-compatible\. For more information on AWS DMS, see [What is AWS Database Migration Service?](https://docs.aws.amazon.com/dms/latest/userguide/Welcome.html) in the *AWS Database Migration Service User Guide*\. 

For a list of AWS Regions where Aurora is available, see [Amazon Aurora](https://docs.aws.amazon.com/general/latest/gr/rande.html#aurora) in the *AWS General Reference*\.

**Important**  
If you plan to migrate an RDS for PostgreSQL DB instance to an Aurora PostgreSQL DB cluster in the near future, we strongly recommend that you turn off auto minor version upgrades for the DB instance early in the migration planning phase\. Migration to Aurora PostgreSQL might be delayed if the RDS for PostgreSQL version isn't yet supported by Aurora PostgreSQL\.   
For information about Aurora PostgreSQL versions, see [ Engine versions for Amazon Aurora PostgreSQL](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraPostgreSQL.Updates.20180305.html)\.

## Migrating a snapshot of an RDS for PostgreSQL DB instance to an Aurora PostgreSQL DB cluster<a name="AuroraPostgreSQL.Migrating.RDSPostgreSQL.Import.Console"></a>

To create an Aurora PostgreSQL DB cluster, you can migrate a DB snapshot of an RDS for PostgreSQL DB instance\. The new Aurora PostgreSQL DB cluster is populated with the data from the original RDS for PostgreSQL DB instance\. For information about creating a DB snapshot, see [Creating a DB snapshot](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CreateSnapshot.html)\.

In some cases, the DB snapshot might not be in the AWS Region where you want to locate your data\. If so, use the Amazon RDS console to copy the DB snapshot to that AWS Region\. For information about copying a DB snapshot, see [Copying a DB snapshot](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CopySnapshot.html)\.

You can migrate RDS for PostgreSQL snapshots that are compatible with the Aurora PostgreSQL versions available in the given AWS Region\. For example, you can migrate a snapshot from an RDS for PostgreSQL 11\.1 DB instance to Aurora PostgreSQL version 11\.4, 11\.7, 11\.8, or 11\.9 in the US West \(N\. California\) Region\. You can migrate RDS for PostgreSQL 10\.11 snapshot to Aurora PostgreSQL 10\.11, 10\.12, 10\.13, and 10\.14\. In other words, the RDS for PostgreSQL snapshot must use the same or a lower minor version as the Aurora PostgreSQL\. 

You can also choose for your new Aurora PostgreSQL DB cluster to be encrypted at rest by using an AWS KMS key\. This option is available only for unencrypted DB snapshots\.

To migrate an RDS for PostgreSQL DB snapshot to an Aurora PostgreSQL DB cluster, you can use the AWS Management Console, the AWS CLI, or the RDS API\. When you use the AWS Management Console, the console takes the actions necessary to create both the DB cluster and the primary instance\.

### Console<a name="AuroraPostgreSQL.Migrating.RDSPostgreSQL.Import.Console.console"></a>

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
   + **DB instance class**: Choose a DB instance class that has the required storage and capacity for your database, for example `db.r6g.large`\. Aurora cluster volumes automatically grow as the amount of data in your database increases\. So you only need to choose a DB instance class that meets your current storage requirements\. For more information, see [Overview of Aurora storage](Aurora.Overview.StorageReliability.md#Aurora.Overview.Storage)\.
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
   + **Enable Encryption**: Choose **Enable Encryption** for your new Aurora PostgreSQL DB cluster to be encrypted at rest\. Also choose a KMS key as the **AWS KMS key** value\.
   + **Auto minor version upgrade**: Choose **Enable auto minor version upgrade** to enable your Aurora PostgreSQL DB cluster to receive minor PostgreSQL DB engine version upgrades automatically when they become available\.

     The **Auto minor version upgrade** option only applies to upgrades to PostgreSQL minor engine versions for your Aurora PostgreSQL DB cluster\. It doesn't apply to regular patches applied to maintain system stability\.

1. Choose **Migrate** to migrate your DB snapshot\. 

1. Choose **Databases** to see the new DB cluster\. Choose the new DB cluster to monitor the progress of the migration\. On the **Connectivity & security** tab, you can find the cluster endpoint to use for connecting to the primary writer instance of the DB cluster\. For more information on connecting to an Aurora PostgreSQL DB cluster, see [Connecting to an Amazon Aurora DB cluster](Aurora.Connecting.md)\. 

### AWS CLI<a name="AuroraPostgreSQL.Migrating.RDSPostgreSQL.Import.Console.CLI"></a>

Using the AWS CLI to migrate an RDS for PostgreSQL DB snapshot to an Aurora PostgreSQL involves two separate AWS CLI commands\. First, you use the `restore-db-cluster-from-snapshot` AWS CLI command create a new Aurora PostgreSQL DB cluster\. You then use the `create-db-instance` command to create the primary DB instance in the new cluster to complete the migration\. The following procedure creates an Aurora PostgreSQL DB cluster with primary DB instance that has the same configuration as the DB instance used to create the snapshot\. 

**To migrate an RDS for PostgreSQL DB snapshot to an Aurora PostgreSQL DB cluster**

1. Use the [describe\-db\-snapshots](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-snapshots.html) command to obtain information about the DB snapshot you want to migrate\. You can specify either the `--db-instance-identifier` parameter or the `--db-snapshot-identifier` in the command\. If you don't specify one of these parameters, you get all snapshots\. 

   ```
   aws rds describe-db-snapshots --db-instance-identifier <your-db-instance-name>
   ```

1. The command returns all configuration details for any snapshots created from the DB instance specified\. In the output, find the snapshot that you want to migrate and locate its Amazon Resource Name \(ARN\)\. To learn more about Amazon RDS ARNs, see [Amazon Relational Database Service \(Amazon RDS\)](https://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html#arn-syntax-rds)\. An ARN looks similar to the output following\.

   ```
   “DBSnapshotArn": "arn:aws:rds:aws-region:111122223333:snapshot:<snapshot_name>"
   ```

   Also in the output you can find configuration details for the RDS for PostgreSQL DB instance, such as the engine version, allocated storage, whether or not the DB instance is encrypted, and so on\. 

1. Use the [restore\-db\-cluster\-from\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-cluster-from-snapshot.html) command to start the migration\. Specify the following parameters:
   + `--db-cluster-identifier` – The name that you want to give to the Aurora PostgreSQL DB cluster\. This Aurora DB cluster is the target for your DB snapshot migration\. 
   + `--snapshot-identifier` – The Amazon Resource Name \(ARN\) of the DB snapshot to migrate\. 
   + `--engine` – Specify `aurora-postgresql` for the Aurora DB cluster engine\.
   + `--kms-key-id` – This optional parameter lets you create an encrypted Aurora PostgreSQL DB cluster from an unencrypted DB snapshot\. It also lets you choose a different encryption key for the DB cluster than the key used for the DB snapshot\.
**Note**  
You can't create an unencrypted Aurora PostgreSQL DB cluster from an encrypted DB snapshot\. 

   Without the `--kms-key-id` parameter specified as shown following, the [restore\-db\-cluster\-from\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-cluster-from-snapshot.html) AWS CLI command creates an empty Aurora PostgreSQL DB cluster that's either encrypted using the same key as the DB snapshot or is unencrypted if the source DB snapshot isn't encrypted\. 

   For Linux, macOS, or Unix:

   ```
   aws rds restore-db-cluster-from-snapshot \
       --db-cluster-identifier cluster-name \
       --snapshot-identifier arn:aws:rds:aws-region:111122223333:snapshot:your-snapshot-name \
       --engine aurora-postgresql
   ```

   For Windows:

   ```
   aws rds restore-db-cluster-from-snapshot ^
       --db-cluster-identifier new_cluster ^
       --snapshot-identifier arn:aws:rds:aws-region:111122223333:snapshot:your-snapshot-name ^
       --engine aurora-postgresql
   ```

1. The command returns details about the Aurora PostgreSQL DB cluster that's being created for the migration\. You can check the status of the Aurora PostgreSQL DB cluster by using the [describe\-db\-clusters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clussters.html) AWS CLI command\.

   ```
   aws rds describe-db-clusters --db-cluster-idenfifier cluster-name
   ```

1. When the DB cluster becomes "available", you use [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) command to populate the Aurora PostgreSQL DB cluster with the DB instance based on your Amazon RDS DB snapshot\. Specify the following parameters:
   + `--db-cluster-identifier` – The name of the new Aurora PostgreSQL DB cluster that you created in the previous step\. 
   + `--db-instance-identifier` – The name you want to give to the DB instance\. This instance becomes the primary node in your Aurora PostgreSQL DB cluster\.
   + `----db-instance-class ` – Specify the DB instance class to use\. Choose from among the DB instance classes supported by the Aurora PostgreSQL version to which you're migrating\. For more information, see [DB instance class types](Concepts.DBInstanceClass.md#Concepts.DBInstanceClass.Types) and [Supported DB engines for DB instance classes](Concepts.DBInstanceClass.md#Concepts.DBInstanceClass.SupportAurora)\.
   + `--engine` – Specify `aurora-postgresql` for the DB instance\.

   You can also create the DB instance with a different configuration than the source DB snapshot, by passing in the appropriate options in the `create-db-instance` AWS CLI command\. For more information, see the [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) command\. 

   For Linux, macOS, or Unix:

   ```
   aws rds create-db-instance \
       --db-cluster-identifier cluster-name \
       --db-instance-identifier --db-instance-class db.instance.class \
       --engine aurora-postgresql
   ```

   For Windows:

   ```
   aws rds create-db-instance ^
       --db-cluster-identifier cluster-name ^
       --db-instance-identifier --db-instance-class db.instance.class ^
       --engine aurora-postgresql
   ```

When the migration process completes, the Aurora PostgreSQL cluster has a populated primary DB instance\. 

## Migrating data from an RDS for PostgreSQL DB instance to an Aurora PostgreSQL DB cluster using an Aurora read replica<a name="AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica"></a>

You can use an RDS for PostgreSQL DB instance as the basis for a new Aurora PostgreSQL DB cluster by using an Aurora read replica for the migration process\. The Aurora read replica option is available only for migrating within the same AWS Region and account, and it's available only if the Region offers a compatible version of Aurora PostgreSQL for your RDS for PostgreSQL DB instance\. By *compatible*, we mean that the Aurora PostgreSQL version is the same as the RDS for PostgreSQL version, or that it is a higher minor version in the same major version family\.

For example, to use this technique to migrate an RDS for PostgreSQL 11\.14 DB instance, the Region must offer Aurora PostgreSQL version 11\.14 or a higher minor version in the PostgreSQL version 11 family\. 

**Topics**
+ [Overview of migrating data by using an Aurora read replica](#AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica.Intro)
+ [Preparing to migrate data by using an Aurora read replica](#AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica.Prepare)
+ [Creating an Aurora read replica](#AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica.Create)
+ [Promoting an Aurora read replica](#AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica.Promote)

### Overview of migrating data by using an Aurora read replica<a name="AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica.Intro"></a>

Migrating from an RDS for PostgreSQL DB instance to an Aurora PostgreSQL DB cluster is a multistep procedure\. First, you create an Aurora read replica of your source RDS for PostgreSQL DB instance\. That starts a replication process from your RDS for PostgreSQL DB instance to a special\-purpose DB cluster known as a **Replica* cluster*\. The Replica cluster consists solely of an Aurora read replica \(a reader instance\)\. 

Once the Replica cluster exists, you monitor the lag between it and the source RDS for PostgreSQL DB instance\. When the replica lag is zero \(0\), you can promote the Replica cluster\. Replication stops, the Replica cluster is promoted to a standalone Aurora DB cluster, and the reader is promoted to writer instance for the cluster\. You can then add instances to the Aurora PostgreSQL DB cluster to size your Aurora PostgreSQL DB cluster for your use case\. You can also delete the RDS for PostgreSQL DB instance if you have no further need of it\. 

**Note**  
It can take several hours per terabyte of data for the migration to complete\. 

You can't create an Aurora read replica if your RDS for PostgreSQL DB instance already has an Aurora read replica or if it has a cross\-Region read replica\. 

### Preparing to migrate data by using an Aurora read replica<a name="AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica.Prepare"></a>

During the migration process using Aurora read replica, updates made to the source RDS for PostgreSQL DB instance are asynchronously replicated to the Aurora read replica of the Replica cluster\. The process uses PostgreSQL's native streaming replication functionality which stores write\-ahead logs \(WAL\) segments on the source instance\. Before starting this migration process, make sure that your instance has sufficient storage capacity by checking values for the metrics listed in the table\. 


| Metric | Description | 
| --- | --- | 
|  `FreeStorageSpace`  |  The available storage space\. Units: Bytes  | 
|  `OldestReplicationSlotLag`  |  The size of the lag for WAL data in the replica that is lagging the most\. Units: Megabytes  | 
|  `RDSToAuroraPostgreSQLReplicaLag`  |  The amount of time in seconds that an Aurora PostgreSQL DB cluster lags behind the source RDS DB instance\.  | 
|  `TransactionLogsDiskUsage`  |  The disk space used by the transaction logs\. Units: Megabytes  | 

For more information about monitoring your RDS instance, see [Monitoring](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Monitoring.html) in the *Amazon RDS User Guide*\.

### Creating an Aurora read replica<a name="AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica.Create"></a>

You can create an Aurora read replica for an RDS for PostgreSQL DB instance by using the AWS Management Console or the AWS CLI\. The option to create an Aurora read replica using the AWS Management Console is available only if the AWS Region offers a compatible Aurora PostgreSQL version\. That is, it's available only if there's an Aurora PostgreSQL version that is the same as the RDS for PostgreSQL version or a higher minor version in the same major version family\.

#### Console<a name="AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica.Create.Console"></a>

**To create an Aurora read replica from a source PostgreSQL DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\. 

1. Choose the RDS for PostgreSQL DB instance that you want to use as the source for your Aurora read replica\. For **Actions**, choose **Create Aurora read replica**\. If this choice doesn't display, it means that a compatible Aurora PostgreSQL version isn't available in the Region\.   
![\[Create Aurora read replica\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/Aurorapgres-migrate.png)

1. On the Create Aurora read replica settings page, you configure the properties for the Aurora PostgreSQL DB cluster as shown in the following table\. The Replica DB cluster is created from a snapshot of the source DB instance using the same 'master' user name and password as the source, so you can't change these at this time\.     
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraPostgreSQL.Migrating.html)

1. Choose **Create read replica**\.

#### AWS CLI<a name="AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica.Create.CLI"></a>

To create an Aurora read replica from a source RDS for PostgreSQL DB instance by using the AWS CLI, you first use the [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) CLI command to create an empty Aurora DB cluster\. Once the DB cluster exists, you then use the [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) command to create the primary instance for your DB cluster\. The primary instance is the first instance that's created in an Aurora DB cluster\. In this case, it's created initially as an Aurora read replica of your RDS for PostgreSQL DB instance\. When the process concludes, your RDS for PostgreSQL DB instance has effectively been migrated to an Aurora PostgreSQL DB cluster\.

You don't need to specify the main user account \(typically, `postgres`\), its password, or the database name\. The Aurora read replica obtains these automatically from the source RDS for PostgreSQL DB instance that you identify when you invoke the AWS CLI commands\. 

You do need to specify the engine version to use for the Aurora PostgreSQL DB cluster and the DB instance\. The version you specify should match the source RDS for PostgreSQL DB instance\. If the source RDS for PostgreSQL DB instance is encrypted, you need to also specify encryption for the Aurora PostgreSQL DB cluster primary instance\. Migrating an encrypted instance to an unencrypted Aurora DB cluster isn't supported\. 

The following examples create an Aurora PostgreSQL DB cluster named `my-new-aurora-cluster` that's going to use an unencrypted RDS DB source instance\. You first create the Aurora PostgreSQL DB cluster by calling the [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) CLI command\. The example shows how to use the optional `--storage-encrypted `parameter to specify that the DB cluster should be encrypted\. Because the source DB isn't encrypted, the `--kms-key-id` is used to specify the key to use\. For more information about required and optional parameters, see the list following the example\.

For Linux, macOS, or Unix:

```
aws rds create-db-cluster  \
    --db-cluster-identifier my-new-aurora-cluster \
    --db-subnet-group-name my-db-subnet
    --vpc-security-group-ids sg-11111111 
    --engine aurora-postgresql \
    --engine-version same-as-your-rds-instance-version \
    --replication-source-identifier arn:aws:rds:aws-region:111122223333:db/rpg-source-db \
    --storage-encrypted \
    --kms-key-id arn:aws:kms:aws-region:111122223333:key/11111111-2222-3333-444444444444
```

For Windows:

```
aws rds create-db-cluster  ^
    --db-cluster-identifier my-new-aurora-cluster ^
    --db-subnet-group-name my-db-subnet ^
    --vpc-security-group-ids sg-11111111 ^
    --engine aurora-postgresql ^
    --engine-version same-as-your-rds-instance-version ^
    --replication-source-identifier arn:aws:rds:aws-region:111122223333:db/rpg-source-db ^
    --storage-encrypted ^
    --kms-key-id arn:aws:kms:aws-region:111122223333:key/11111111-2222-3333-444444444444
```

In the following list you can find more information about some of the options shown in the example\. Unless otherwise specified, these parameters are required\.
+ `--db-cluster-identfier` – You need to give your new Aurora PostgreSQL DB cluster a name\.
+ `--db-subnet-group-name` – Create your Aurora PostgreSQL DB cluster in the same DB subnet as the source DB instance\.
+ `--vpc-security-group-ids` – Specify the security group for your Aurora PostgreSQL DB cluster\. 
+ `--engine-version` – Specify the version to use for the Aurora PostgreSQL DB cluster\. This should be the same as the version used by your source RDS for PostgreSQL DB instance\. 
+ `--replication-source-identifier` – Identify your RDS for PostgreSQL DB instance using its Amazon Resource Name \(ARN\)\. For more information about Amazon RDS ARNs, see [Amazon Relational Database Service \(Amazon RDS\)](https://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html#arn-syntax-rds) in the *AWS General Reference*\. of your DB cluster\.
+ `--storage-encrypted` – Optional\. Use only when needed to specify encryption as follows:
  + Use this parameter when the source DB instance has encrypted storage\. The call to [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) fails if you don't use this parameter with a source DB instance that has encrypted storage\. If you want to use a different key for the Aurora PostgreSQL DB cluster than the key used by the source DB instance, you need to also specify the `--kms-key-id`\. 
  + Use if the source DB instance's storage is unencrypted but you want the Aurora PostgreSQL DB cluster to use encryption\. If so, you also need to identify the encryption key to use with the `--kms-key-id` parameter\.
+ `--kms-key-id` – Optional\. When used, you can specify the key to use for storage encryption \(`--storage-encrypted`\) by using the key's ARN, ID, alias ARN, or its alias name\. This parameter is needed only for the following situations:
  + To choose a different key for the Aurora PostgreSQL DB cluster than that used by the source DB instance\. 
  + To create an encrypted cluster from an unencrypted source\. In this case, you need to specify the key that Aurora PostgreSQL should use for encryption\.

After creating the Aurora PostgreSQL DB cluster, you then create the primary instance by using the [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) CLI command, as shown in the following: 

For Linux, macOS, or Unix:

```
aws rds create-db-instance \
    --db-cluster-identifier my-new-aurora-cluster \
    --db-instance-class db.x2g.16xlarge \
    --db-instance-identifier rpg-for-migration \
    --engine aurora-postgresql
```

For Windows:

```
aws rds create-db-instance ^
    --db-cluster-identifier my-new-aurora-cluster ^
    --db-instance-class db.x2g.16xlarge ^
    --db-instance-identifier rpg-for-migration ^
    --engine aurora-postgresql
```

In the following list, you can find more information about some of the options shown in the example\.
+ `--db-cluster-identifier` – Specify the name of the Aurora PostgreSQL DB cluster that you created with the [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) command in the previous steps\.
+ `--db-instance-class` – The name of the DB instance class to use for your primary instance, such as `db.r4.xlarge`, `db.t4g.medium`, `db.x2g.16xlarge`, and so on\. For a list of available DB instance classes, see [DB instance class types](Concepts.DBInstanceClass.md#Concepts.DBInstanceClass.Types)\. 
+ `--db-instance-identifier` – Specify the name to give your primary instance\.
+ `--engine aurora-postgresql` – Specify `aurora-postgresql` for the engine\.

#### RDS API<a name="Aurora.Migration.RDSPostgreSQL.Create.API"></a>

To create an Aurora read replica from a source RDS for PostgreSQL DB instance, first use the RDS API operation [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) to create a new Aurora DB cluster for the Aurora read replica that gets created from your source RDS for PostgreSQL DB instance\. When the Aurora PostgreSQL DB cluster is available, you then use the [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) to create the primary instance for the Aurora DB cluster\.

You don't need to specify the main user account \(typically, `postgres`\), its password, or the database name\. The Aurora read replica obtains these automatically from the source RDS for PostgreSQL DB instance specified with `ReplicationSourceIdentifier`\. 

You do need to specify the engine version to use for the Aurora PostgreSQL DB cluster and the DB instance\. The version you specify should match the source RDS for PostgreSQL DB instance\. If the source RDS for PostgreSQL DB instance is encrypted, you need to also specify encryption for the Aurora PostgreSQL DB cluster primary instance\. Migrating an encrypted instance to an unencrypted Aurora DB cluster isn't supported\. 

To create the Aurora DB cluster for the Aurora read replica, use the RDS API operation [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) with the following parameters:
+ `DBClusterIdentifier` – The name of the DB cluster to create\.
+ `DBSubnetGroupName` – The name of the DB subnet group to associate with this DB cluster\.
+ `Engine=aurora-postgresql` – The name of the engine to use\.
+ `ReplicationSourceIdentifier` – The Amazon Resource Name \(ARN\) for the source PostgreSQL DB instance\. For more information about Amazon RDS ARNs, see [Amazon Relational Database Service \(Amazon RDS\)](https://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html#arn-syntax-rds) in the *Amazon Web Services General Reference\.* If `ReplicationSourceIdentifier` identifies an encrypted source, Amazon RDS uses your default KMS key unless you specify a different key using the `KmsKeyId` option\. 
+ `VpcSecurityGroupIds` – The list of Amazon EC2 VPC security groups to associate with this DB cluster\.
+ `StorageEncrypted` – Indicates that the DB cluster is encrypted\. When you use this parameter without also specifying the `ReplicationSourceIdentifier`, Amazon RDS uses your default KMS key\.
+ `KmsKeyId` – The key for an encrypted cluster\. When used, you can specify the key to use for storage encryption by using the key's ARN, ID, alias ARN, or its alias name\.

For more information, see [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) in the *Amazon RDS API Reference*\.

Once the Aurora DB cluster is available, you can then create a primary instance for it by using the RDS API operation [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) with the following parameters:
+ `DBClusterIdentifier` – The name of your DB cluster\.
+ `DBInstanceClass` – The name of the DB instance class to use for your primary instance\.
+ `DBInstanceIdentifier` – The name of your primary instance\.
+ `Engine=aurora-postgresql` – The name of the engine to use\.

For more information, see [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) in the *Amazon RDS API Reference*\.

### Promoting an Aurora read replica<a name="AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica.Promote"></a>

The migration to Aurora PostgreSQL isn't complete until you promote the Replica cluster, so don't delete the RDS for PostgreSQL source DB instance just yet\. 

Before promoting the Replica cluster, make sure that the RDS for PostgreSQL DB instance doesn't have any in\-process transactions or other activity writing to the database\. When the replica lag on the Aurora read replica reaches zero \(0\), you can promote the Replica cluster\. For more information about monitoring replica lag, see [Monitoring Aurora PostgreSQL replication](AuroraPostgreSQL.Replication.md#AuroraPostgreSQL.Replication.Monitoring) and [Instance\-level metrics for Amazon Aurora](Aurora.AuroraMySQL.Monitoring.Metrics.md#Aurora.AuroraMySQL.Monitoring.Metrics.instances)\.

#### Console<a name="AuroraPostgreSQL.Migrating.RDSPostgreSQL.Replica.Promote.Console"></a>

**To promote an Aurora read replica to an Aurora DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\. 

1. Choose the Replica cluster\.   
![\[Promote a Replica cluster to full Aurora PostgreSQL DB cluster status\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/Aurorapgres-migrate-2.png)

1. For **Actions**, choose **Promote**\. This may take a few minutes\. 

When the process completes, the Aurora Replica cluster is a Regional Aurora PostgreSQL DB cluster, with a Writer instance containing the data from the RDS for PostgreSQL DB instance\.

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

After you promote the Replica cluster, you can confirm that the promotion has completed by checking the event log, as follows\.

**To confirm that the Aurora Replica cluster was promoted**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Events**\.

1. On the **Events** page, find the name of your cluster in the **Source** list\. Each event has a source, type, time, and message\. You can see all events that have occurred in your AWS Region for your account\. A successful promotion generates the following message\.  

   ```
   Promoted Read Replica cluster to a stand-alone database cluster.
   ```

After promotion is complete, the source RDS for PostgreSQL DB instance and the Aurora PostgreSQL DB cluster are unlinked\. You can direct your client applications to the endpoint for the Aurora read replica\. For more information on the Aurora endpoints, see [Amazon Aurora connection management](Aurora.Overview.Endpoints.md)\. At this point, you can safely delete the DB instance\.