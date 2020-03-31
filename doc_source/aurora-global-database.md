# Working with Amazon Aurora Global Database<a name="aurora-global-database"></a>

Following, you can find a description of Amazon Aurora Global Database\. Each Aurora global database spans multiple AWS Regions, enabling low latency global reads and disaster recovery from region\-wide outages\. 

**Topics**
+ [Overview of Aurora Global Database](#aurora-global-database-overview)
+ [Creating an Aurora Global Database](#aurora-global-database-creating)
+ [Adding an AWS Region to an Aurora Global Database](#aurora-global-database-attaching)
+ [Removing a Cluster from an Aurora Global Database](#aurora-global-database-detaching)
+ [Deleting an Aurora Global Database](#aurora-global-database-deleting)
+ [Importing Data into an Aurora Global Database](#aurora-global-database-etl)
+ [Connecting to an Aurora Global Database](#aurora-global-database-connecting)
+ [Failover for Aurora Global Database](#aurora-global-database-failover)
+ [Performance Insights for Aurora Global Database](#aurora-global-database-pi)
+ [Using Multiple Secondary Regions with Aurora Global Database](#aurora-global-database-multiple-secondary-regions)
+ [Using Aurora Global Database with Other AWS Services](#aurora-global-database-interop)

## Overview of Aurora Global Database<a name="aurora-global-database-overview"></a>

 An Aurora global database consists of one primary AWS Region where your data is mastered, and up to five read\-only, secondary AWS Regions\. Aurora replicates data to the secondary AWS Regions with typical latency of under a second\. You issue write operations directly to the primary DB instance in the primary AWS Region\. An Aurora global database uses dedicated infrastructure to replicate your data, leaving database resources available entirely to serve application workloads\. Applications with a worldwide footprint can use reader instances in the secondary AWS Regions for low latency reads\. In the unlikely event your database becomes degraded or isolated in an AWS region, you can promote one of the secondary AWS Regions to take full read\-write workloads in under a minute\. 

 The Aurora cluster in the primary AWS Region where your data is mastered performs both read and write operations\. The clusters in the secondary regions enable low\-latency reads\. You can scale up the secondary clusters independently by adding one of more DB instances \(Aurora Replicas\) to serve read\-only workloads\. For disaster recovery, you can remove a secondary cluster from the Aurora global database and promote it to allow full read and write operations\. 

 Only the primary cluster performs write operations\. Clients that perform write operations connect to the DB cluster endpoint of the primary cluster\. 

**Topics**
+ [Advantages of Aurora Global Database](#aurora-global-database.advantages)
+ [Limitations of Aurora Global Database](#aurora-global-database.limitations)

### Advantages of Aurora Global Database<a name="aurora-global-database.advantages"></a>

 Aurora Global Database provides the following advantages: 
+ Secondary clusters provide scaling of reads far beyond the capacity of a single Aurora cluster\. 

  Each secondary cluster can have up to 16 read\-only Aurora Replica instances instead of the limit of 15 for a single Aurora cluster\.
+  The replication performed by an Aurora global database has little performance impact on the primary DB cluster\. The resources of the DB instances are fully devoted to serve application read and write workloads\. 
+  Changes are replicated between AWS Regions with minimal lag time, typically less than 1 second\. 
+  The secondary clusters enable fast failover for disaster recovery\. You can typically promote a secondary cluster and make it available for writes in under a minute\. 
+  Applications in remote AWS Regions experience lower query latency when they read from a secondary cluster\. 

### Limitations of Aurora Global Database<a name="aurora-global-database.limitations"></a>

 The following limitations currently apply to Aurora Global Database:
+  Aurora Global Database is available for the following versions:
  + Aurora MySQL\. For Aurora MySQL with MySQL 5\.6 compatibility, you can use version 5\.6\.10a or version 1\.22 or higher\. For Aurora MySQL with MySQL 5\.7 compatibility, you can use version 2\.07 or higher\.
  + Aurora PostgreSQL with PostgreSQL 10\.11 compatibility\.
+  You have a choice of using `db.r4` or `db.r5` instance classes for an Aurora global database\. You can't use `db.t2` or `db.t3` instance classes\.
+ Aurora Global Database is available in the following regions:
  + US East \(N\. Virginia\)
  + US East \(Ohio\)
  + US West \(N\. California\)
  + US West \(Oregon\)
  + Europe \(Ireland\)
  + Europe \(London\)
  + Europe \(Paris\)
  + Europe \(Frankfurt\)
  + Asia Pacific \(Mumbai\)
  + Asia Pacific \(Singapore\)
  + Asia Pacific \(Sydney\)
  + Asia Pacific \(Tokyo\)
  + Asia Pacific \(Seoul\)
  + Canada \(Central\)
+  A secondary cluster must be in a different AWS Region than the primary cluster\. 
+  You can't create an Aurora MySQL cross\-region Read Replica from the primary cluster in the same region as a secondary cluster\. See [Replicating Amazon Aurora MySQL DB Clusters Across AWS Regions](AuroraMySQL.Replication.CrossRegion.md) for information about cross\-region Read Replicas\. 
+ To upgrade your global database clusters, you must upgrade the secondary clusters before the primary cluster\. For more information about upgrading, see [Database Upgrades and Patches for Amazon Aurora MySQL](AuroraMySQL.Updates.md#AuroraMySQL.Updates.Patching) or [Upgrading the PostgreSQL DB Engine for Aurora PostgreSQL](USER_UpgradeDBInstance.PostgreSQL.md)\. 

 The following features aren't supported for Aurora Global Database: 
+  Cloning\. For information about cloning, see [Cloning Databases in an Aurora DB Cluster](Aurora.Managing.Clone.md)\. 
+  Backtrack\. For information about backtracking, see [Backtracking an Aurora DB Cluster](AuroraMySQL.Managing.Backtrack.md)\. 
+  Parallel query\. For information about parallel query, see [Working with Parallel Query for Amazon Aurora MySQL](aurora-mysql-parallel-query.md)\. 
+  Aurora Serverless\. For information about Aurora Serverless, see [Using Amazon Aurora Serverless](aurora-serverless.md)\. 
+ Database Activity Streams\. For information about Database Activity Streams, see [Using Database Activity Streams with Aurora PostgreSQL](DBActivityStreams.md)\.
+  Stopping and starting the DB clusters within the global database\. See [Stopping and Starting an Amazon Aurora DB Cluster](aurora-cluster-stop-start.md) for information about stopping and starting Aurora clusters\. 

## Creating an Aurora Global Database<a name="aurora-global-database-creating"></a>

 An Aurora global database spans multiple AWS Regions\. First you create the global database itself along with the read\-write primary cluster\. Then you add one or more read\-only secondary clusters in other AWS Regions\. 

 When specifying names for the primary and secondary clusters, choose names that are different than your existing Aurora clusters, even if those clusters are in other AWS Regions\. 

**Important**  
 Before you create an Aurora global database, complete the setup tasks for your account, network, and security settings in [Setting Up for Amazon RDS](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_SettingUp.html) in the *Amazon Relational Database Service User Guide*\. 

### Console<a name="aurora-global-database-create.console"></a>

To use the console to create a global database for Aurora MySQL version 5\.6\.10a, see the second process\.

**To create an Aurora global database using the AWS Management Console**

1. Start with an existing Aurora cluster or create a new cluster\. Use Aurora MySQL version 1\.22\.0 or higher, Aurora MySQL version 2\.07 or higher, or Aurora PostgreSQL version 10\.11\. For more about creating a cluster, see [Creating a DB Cluster and Connecting to a Database on an Aurora MySQL DB Cluster](CHAP_GettingStartedAurora.CreatingConnecting.Aurora.md) or [Creating a DB Cluster and Connecting to a Database on an Aurora PostgreSQL DB Cluster](CHAP_GettingStartedAurora.CreatingConnecting.AuroraPostgreSQL.md)\. 

1. After your Aurora cluster is created and available, create one or more secondary clusters\. To do so, follow the steps in [Adding an AWS Region to an Aurora Global Database](#aurora-global-database-attaching)\. Each secondary cluster is in a different AWS Region than the original cluster\. 

**To create an Aurora MySQL global database using the AWS Management Console \(version 5\.6\.10a only\)**

 Aurora MySQL version 5\.6\.10a requires choosing to make the cluster compatible with Aurora global database at cluster creation time\. You see the **Global** choice in the AWS Management Console only when creating a cluster with this Aurora MySQL version\. For Aurora MySQL version 1\.22 and higher, you can use any cluster with an Aurora global database without making any special choice when creating the cluster\. 

 For Aurora MySQL versions between 5\.6\.10a and 1\.22, you don't make any special choice when creating the cluster\. However, to use such a cluster in an Aurora global database, you must upgrade it to Aurora MySQL version 1\.22 or higher first\. 

1.  Sign in to the AWS Management Console and open the Amazon RDS console for a region where Aurora global databases are available\. \(See also [Limitations of Aurora Global Database](#aurora-global-database.limitations)\.\) 

1.  Choose **Create Database**\. 

1.  On the **Select engine** page, choose the Aurora engine and **Global** for **Database location**\. For an example, see the following images\. 
**Note**  
 Make sure that **Standard Create** is chosen\. **Standard create** makes visible the choices that you need for Aurora global databases\. Don't choose **Easy Create**\. 

   1.  Choose **Amazon Aurora** as the engine:   
![\[Screenshot of the engine options choices when creating a database.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-databases-create-global-01a.png)

   1.  Choose the **Amazon Aurora with MySQL compatibility** edition\. 

   1. Choose the **Version** 5\.6\.10a\.

   1.  Choose **Global** as the database location:   
![\[Screenshot of the database location choices when creating an Aurora cluster. Choose "global" to create an Aurora global databases.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-databases-create-global-01c.png)
**Note**  
 Choosing **Global** sets up the global database and the primary Aurora cluster\. Once the global database is created and available, you can add a secondary AWS Region\. 

1.  Fill in the remaining settings using the same decision process as for other Aurora clusters\. For more information about creating an Aurora DB cluster, see [Creating an Amazon Aurora DB Cluster](Aurora.CreateInstance.md)\.   
![\[Screenshot showing supplemental settings when creating an Aurora global database.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-databases-create-global-02.png)

1.  Choose **Create**\. 

 When the primary DB cluster is created and available, create one or more secondary clusters by following the steps in [Adding an AWS Region to an Aurora Global Database](#aurora-global-database-attaching)\. 

### AWS CLI<a name="aurora-global-database-create.cli"></a>

To create an Aurora global database using the CLI, use either of the following procedures:
+ Create an Aurora global database and then add a primary AWS Region and a DB instance in that AWS Region\. 
+ Alternatively, you can create the primary cluster first, then create the global database afterwards, specifying which cluster to use as the primary cluster\.

**To create an Aurora global database and then add a primary AWS Region and DB instance**

1. Create an Aurora global database and then add a primary AWS Region cluster\. 

   1.  Create the Aurora global database itself\. Initially, no clusters are associated with it\. Use the following values:
      + For Aurora MySQL, specify `aurora` for the `--engine` parameter\.
      + For Aurora PostgreSQL, specify `aurora-postgresql` for the `--engine` parameter and `10.11` for the `--engine-version` parameter\.

      For Linux, macOS, or Unix:

      ```
      aws rds create-global-cluster --region primary_region  \
          --global-cluster-identifier global_database_id \
          --engine engine_type \
          --engine-version version # optional
      ```

      For Windows:

      ```
      aws rds create-global-cluster ^
          --global-cluster-identifier global_database_id ^
          --engine engine_type ^
          --engine-version version # optional
      ```

       To learn more about additional options, see `[create\-global\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/create-global-cluster.html)`\. 

   1.  Check that the Aurora global database has become available to use by using the AWS CLI `[describe\-global\-clusters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-global-clusters.html)` command, as shown in the following example\. 

      ```
      aws rds describe-global-clusters --region primary_region --global-cluster-identifier global_database_id    # ID is optional
      ```

      When the results show a status of `available`, go on to the next step\.

   1. Create the primary cluster for the Aurora global database\. To do so, use the AWS CLI `[create\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html)` command\. Specify the same `--global-cluster-identifier` value as when you created the global database\.
      + For Aurora MySQL, specify `aurora` for the `--engine` parameter\.
      + For Aurora PostgreSQL, specify `aurora-postgresql` for the `--engine` parameter and `10.11` for the `--engine-version` parameter\.

      For Linux, macOS, or Unix:

      ```
      aws rds create-db-cluster \
        --region primary_region \
        --db-cluster-identifier db_cluster_id \
        --master-username master_userid \
        --master-user-password master_password \
        --engine { aurora | aurora-mysql | aurora-postgresql } \
        --engine-version version \
        --global-cluster-identifier global_database_id
      ```

      For Windows:

      ```
      aws rds create-db-cluster ^
        --region primary_region ^
        --db-cluster-identifier db_cluster_id ^
        --master-username master_userid ^
        --master-user-password master_password ^
        --engine { aurora | aurora-mysql | aurora-postgresql } ^
        --engine-version version ^
        --global-cluster-identifier global_database_id
      ```
**Note**  
For Aurora MySQL version 5\.6\.10a, you need to also specify `global` for the `--engine-mode` parameter\. 

      To learn more about additional options, see `[create\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html)`\. 

   1.  Check that the Aurora cluster has become available to use by using the AWS CLI `[describe\-db\-clusters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html)` command, as shown in the following example\. 

      ```
      aws rds describe-db-clusters --region primary_region --db-cluster-identifier db_cluster_id # ID is optional
      ```

      When the results show a status of `available`, go on to the next step\.

1. Create the primary DB instance for the primary cluster\. To do so, use the AWS CLI `[create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html)` command\. 
   +  Specify the `--engine` parameter as follows:
     + For Aurora MySQL version 1 or 5\.6\.10a, use `--engine aurora`\.
     + For Aurora MySQL version 2, use `--engine aurora-mysql`\.
     + For Aurora PostgreSQL, use `--engine aurora-postgresql`\. 
   +  Optionally, specify the `--engine-version` parameter to choose a specific version to use\. 
   + For Aurora MySQL version 5\.6\.10a, specify `--engine-mode global`\. 

    The following example shows how\. 

   For Linux, macOS, or Unix:

   ```
   aws rds create-db-instance \
     --db-cluster-identifier db_cluster_id \
     --db-instance-class instance_class \
     --db-instance-identifier db_instance_id \
     --engine { aurora | aurora-mysql | aurora-postgresql} \
     --engine-version version \ 
     --region primary_region
   ```

   For Windows:

   ```
   aws rds create-db-instance ^
     --db-cluster-identifier db_cluster_id ^
     --db-instance-class instance_class ^
     --db-instance-identifier db_instance_id ^
     --engine { aurora | aurora-mysql | aurora-postgresql } ^
     --engine-version version ^
     --region primary_region
   ```

    You don't need to include the `--master-username` and `--master-user-password` parameters, because those values are taken from the primary DB cluster\. 

    To learn more about additional options, see `[create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html)`\. 

1.  Check that the primary cluster of the Aurora global database has become available to use by using the AWS CLI `[describe\-db\-clusters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html)` command, as shown in the following example\. 

   ```
   aws rds describe-db-clusters --db-cluster-identifier sample-global-cluster
   ```

1.  When the `describe-db-clusters` results show a status of `available`, create the primary instance for the primary cluster so that replication can begin\. To do so, use the AWS CLI `[create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html)` command as shown in the following example\. 

   For Linux, macOS, or Unix:

   ```
   aws rds create-db-instance \
     --db-cluster-identifier sample-global-cluster \
     --db-instance-class instance_class \
     --db-instance-identifier sample-replica-instance \
     --engine aurora
   ```

   For Windows:

   ```
   aws rds create-db-instance ^
     --db-cluster-identifier sample-global-cluster ^
     --db-instance-class instance_class ^
     --db-instance-identifier sample-replica-instance ^
     --engine aurora
   ```

    When the DB instance is created and available, replication begins\. You can determine if the DB instance is available by calling the AWS CLI `[describe\-db\-instances](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html)` command\. 

**As an alternative procedure, you can create the primary DB cluster first and then create the global database afterwards**

1.  Create an empty Aurora DB cluster\. 
   + For Aurora MySQL version 5\.6\.10a, specify the following parameters and values:
     + `--engine aurora` 
     + `--engine-version 5.6.10a` 
     + `--engine-mode global` 

       Use this setting only in combination with the `aurora` `--engine` setting\.
   + For Aurora PostgreSQL, specify the following parameters and values:
     + `--engine aurora-postgresql` 
     + `--engine-version 10.11` 

   For Linux, macOS, or Unix:

   ```
   aws rds --region primary_region \
     create-db-cluster \
       --db-cluster-identifier primary_cluster_id \
       --master-username master_userid \
       --master-user-password master_password \
       --engine { aurora | aurora-mysql | aurora-postgresql } \
       --engine-version version
   
   aws rds --region primary_region \
     create-db-instance \
       --db-instance-class instance_class \
       --db-cluster-identifier primary_cluster_id \
       --db-instance-identifier db_instance_id \
       --engine { aurora | aurora-mysql | aurora-postgresql }
   ```

   For Windows:

   ```
   aws rds --region primary_region ^
     create-db-cluster ^
       --db-cluster-identifier primary_cluster_id ^
       --master-username master_userid ^
       --master-user-password master_password ^
       --engine { aurora | aurora-mysql | aurora-postgresql } ^
       --engine-version version
   
   aws rds --region primary_region ^
     create-db-instance ^
       --db-instance-class instance_class ^
       --db-cluster-identifier primary_cluster_id ^
       --db-instance-identifier db_instance_id ^
       --engine { aurora | aurora-mysql | aurora-postgresql }
   ```

1.  Create a DB instance within the Aurora DB cluster using the value \.
   + For Aurora MySQL version 5\.6\.10a, specify `--engine aurora` and `--engine-mode global`\.
   + For Aurora PostgreSQL, specify `--engine aurora-postgresql`\.

   The following is an Aurora PostgreSQL example\.

   For Linux, macOS, or Unix:

   ```
   aws rds --region primary_region \
     create-db-instance \
       --db-instance-class instance_class \
       --db-cluster-identifier primary_cluster_id \
       --db-instance-identifier db_instance_id \
       --engine aurora-postgresql
   ```

   For Windows:

   ```
   aws rds --region primary_region ^
     create-db-instance ^
       --db-instance-class instance_class ^
       --db-cluster-identifier primary_cluster_id ^
       --db-instance-identifier db_instance_id ^
       --engine aurora-postgresql
   ```

1. After the primary DB cluster and DB instance are created and available, create the Aurora global database by calling the AWS CLI `[create\-global\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/create-global-cluster.html)` command in the AWS Region where you want to create the Aurora global database\. Use the `--source-db-cluster-identifier` parameter to specify the primary DB cluster created earlier\.

   For Linux, macOS, or Unix:

   ```
   aws rds create-global-cluster \
       --global-cluster-identifier global_database_id \
       --source-db-cluster-identifier primary_cluster_ARN
   ```

   For Windows:

   ```
   aws rds create-global-cluster ^
       --global-cluster-identifier global_database_id ^
       --source-db-cluster-identifier primary_cluster_ARN
   ```

### RDS API<a name="aurora-global-database-create.api"></a>

 To create an Aurora global database with the RDS API, run the [CreateGlobalCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateGlobalCluster.html) operation\. 

## Adding an AWS Region to an Aurora Global Database<a name="aurora-global-database-attaching"></a>

 After you create an Aurora global database and its primary cluster, you increase its global footprint by adding one or more new AWS Regions\. This process involves attaching one or more secondary Aurora clusters\. Each secondary cluster must be in a different AWS Region than the primary cluster and any other secondary clusters\. 

**Note**  
The maximum number of secondary Aurora clusters you can attach to an Aurora global database is five\. Thus, the most clusters you can associate with a global database is six: one primary cluster with a read\-write instance and optional reader instances, and five read\-only secondary clusters\.   
 For each secondary cluster in a global database, the maximum number of reader DB instances in the primary cluster is reduced by one\. For example, if your global database has three secondary clusters, the maximum number of reader DB instances in the primary cluster is 12 instead of 15\. If the number of reader instances in the primary cluster plus the number of secondary clusters totals 15, you can't add any more secondary clusters to the global database\. 

### Console<a name="aurora-global-database-attach.console"></a>

**To add an AWS Region to an Aurora global database using the AWS Management Console**

1.  In the navigation pane of the AWS Management Console, choose **Databases**\. 

1.  Choose the check box for the Aurora global database for which you want to create a secondary cluster\. If the primary cluster or DB instances inside it are still in the `Creating` state, wait until they are all `Available`\. 

1.  For **Actions**, choose **Add region**\.   
![\[Screenshot showing "Add region" choice for an Aurora global database.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-databases-cluster-tree-03-global-cluster-actions.png)

1.  On the **Add a region** page, choose the secondary AWS Region\.   
![\[Screenshot showing the "Add region" dialog for an Aurora global database.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-databases-add-region-01.png)

1.  Complete the remaining fields for the secondary Aurora cluster in the new AWS Region, and then choose **Add region**\. 

### AWS CLI<a name="aurora-global-database-attach.cli"></a>

 To add an AWS Region to an Aurora global database with the AWS CLI, run the [create\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) command\. 

 The following commands create a new Aurora cluster, attach it to a global database as a read\-only secondary cluster, and then add a DB instance to the new cluster\. The `--global-cluster-identifier` option for the `create-db-cluster` command specifies the global database to which to attach the new cluster\. Specify the `--engine` parameter as follows:
+ For Aurora MySQL version 1 or 5\.6\.10, use `--engine aurora`
+ For Aurora MySQL version 2, use `--engine aurora-mysql`
+ For Aurora PostgreSQL, use `--engine aurora-postgresql` 

 For an encrypted cluster, specify the `--source-region` option with a value that matches the primary AWS Region\. 

The following is an Aurora PostgreSQL example\.

For Linux, macOS, or Unix:

```
aws rds --region secondary_region \
  create-db-cluster \
    --db-cluster-identifier secondary_cluster_id \
    --global-cluster-identifier global_database_id \
    --engine { aurora | aurora-mysql | aurora-postgresql }
    --engine-version version 

aws rds --region secondary_region \
  create-db-instance \
    --db-instance-class instance_class \
    --db-cluster-identifier secondary_cluster_id \
    --db-instance-identifier db_instance_id \
    --engine { aurora | aurora-mysql | aurora-postgresql }
```

For Windows:

```
aws rds --region secondary_region ^
  create-db-cluster ^
    --db-cluster-identifier secondary_cluster_id ^
    --global-cluster-identifier global_database_id_id ^
    --engine { aurora | aurora-mysql | aurora-postgresql } ^
    --engine-version version

aws rds --region secondary_region ^
  create-db-instance ^
    --db-instance-class instance_class ^
    --db-cluster-identifier secondary_cluster_id ^
    --db-instance-identifier db_instance_id ^
    --engine { aurora | aurora-mysql | aurora-postgresql }
```

### RDS API<a name="aurora-global-database-attach.api"></a>

 To add a new AWS Region to an Aurora global database with the RDS API, run the [CreateDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) operation\. Specify the identifier of the existing global database using the `GlobalClusterIdentifier` parameter\. 

## Removing a Cluster from an Aurora Global Database<a name="aurora-global-database-detaching"></a>

 Removing an Aurora cluster from an Aurora global database turns it back into a regional cluster, with full read\-write capability and no longer synchronized with the primary cluster\. 

 You might remove an Aurora cluster from a global database in case the primary cluster becomes degraded or isolated\. Performing a failover for an Aurora global database involves removing one of the secondary clusters from the original global database, and then using it as the primary cluster in a new Aurora global database\. For more information, see [Failover for Aurora Global Database](#aurora-global-database-failover)\. 

 If you no longer need the global database, you must remove all secondary clusters and then the primary cluster before deleting the global database itself\. You can then delete some or all of the clusters you removed, or continue to use some or all as single\-region Aurora clusters\. For more information, see [Deleting an Aurora Global Database](#aurora-global-database-deleting)\.

### Console<a name="aurora-global-database-detach.console"></a>

**To remove an Aurora cluster from an Aurora global database with the AWS Management Console**

1. Choose the cluster on the **Databases** page\.

1.  For **Actions**, choose **Remove from Global**\. The removed cluster becomes a regular Aurora cluster with full read\-write capability\. It is no longer kept synchronized with the primary cluster\.   
![\[Screenshot showing confirmation prompt to remove a secondary cluster from an Aurora global database.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-databases-detach-secondary-convert-standalone.png)

    If any secondary clusters are still associated with the global database, you can't remove the primary cluster\. 

    After you remove or delete all secondary clusters, then you can remove the primary cluster the same way\. 

 After clusters are removed from an Aurora global database, you still see them in the **Databases** page, but without the **Global**, **Primary**, and **Secondary** labels\. 

![\[Screenshot showing clusters after being removed from an Aurora global database.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-databases-cluster-tree-06-after-detaching.png)

### AWS CLI<a name="aurora-global-database-detach.cli"></a>

 To remove an Aurora cluster from an Aurora global database with the AWS CLI, run the [remove\-from\-global\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/remove-from-global-cluster.html) command\. 

The following commands remove a secondary cluster and then the primary cluster from an Aurora global database\. The clusters to remove are identified by the `--db-cluster-identifier` parameter in each case\. The Aurora global database is identified by the `--global-cluster-identifier` parameter\. 

For Linux, macOS, or Unix:

```
aws rds --region secondary_region \
  remove-from-global-cluster \
    --db-cluster-identifier secondary_cluster_ARN \
    --global-cluster-identifier global_database_id
... repeat above command with a different --region for all secondary clusters ...

aws rds --region primary_region \
  remove-from-global-cluster \
    --db-cluster-identifier primary_cluster_ARN \
    --global-cluster-identifier global_database_id
```

For Windows:

```
aws rds --region secondary_region ^
  remove-from-global-cluster ^
    --db-cluster-identifier secondary_cluster_ARN ^
    --global-cluster-identifier global_database_id
... repeat above command with a different --region for all secondary clusters ...

aws rds --region primary_region ^
  remove-from-global-cluster ^
    --db-cluster-identifier primary_cluster_ARN ^
    --global-cluster-identifier global_database_id
```

### RDS API<a name="aurora-global-database-detach.api"></a>

 To remove an Aurora cluster from an Aurora global database with the RDS API, run the [RemoveFromGlobalCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RemoveFromGlobalCluster.html) action\. 

## Deleting an Aurora Global Database<a name="aurora-global-database-deleting"></a>

 Before you can delete an Aurora global database, you must first remove or delete the global database's clusters\. Because a global database typically holds business\-critical data, you can't delete the global database and the associated clusters in a single step\. See [Removing a Cluster from an Aurora Global Database](#aurora-global-database-detaching) for instructions on removing a cluster from a global database, which makes it a standalone Aurora cluster again\. See [Deleting a DB Instance in an Aurora DB Cluster](USER_DeleteInstance.md) for the procedure to delete clusters after they are removed\.

### Console<a name="aurora-global-database-delete.console"></a>

**To delete an Aurora global database using the AWS Management Console**

 To delete an Aurora global database with the AWS Management Console, you first remove or delete all secondary clusters associated with the global database, then remove the primary cluster, and finally delete the global database itself\. Deleting the writer instance from an Aurora cluster deletes the cluster itself\. Because a global database typically holds business\-critical data, you can't delete the global database and the associated clusters in a single step\. See [Removing a Cluster from an Aurora Global Database](#aurora-global-database-detaching) for instructions on removing clusters from a global database\. See [Deleting a DB Instance in an Aurora DB Cluster](USER_DeleteInstance.md) for the procedure to delete clusters once they are removed\. Deleting the primary instance from an Aurora cluster deletes the cluster itself\. 

1.  Confirm that all other clusters are removed from the Aurora global database\. 

    If the global database includes any clusters nested underneath it, as in the following screenshot, you can't delete the global database yet\. Follow the procedure in [Removing a Cluster from an Aurora Global Database](#aurora-global-database-detaching) for each cluster associated with the global database\.   
![\[This global database can't be deleted because it still has associated clusters.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-databases-cluster-tree-08-not-empty.png)

    After the global database has no associated clusters, you can proceed to delete it\. The following screenshot shows how the `global-db-demo` cluster is no longer associated with the global database after being removed\.   
![\[All clusters are removed from the global database, so now it can be deleted.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-databases-cluster-tree-09-empty.png)

1.  From the **Databases** page, or the detail page for the global database, choose **Actions** and then choose **Delete**\. 

### AWS CLI<a name="aurora-global-database-delete.cli"></a>

 To delete a cluster that is part of an Aurora global database with the AWS CLI, run the [delete\-global\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/delete-global-cluster.html) command\. 

 The following command deletes the Aurora global database with the specified global cluster ID: 

For Linux, macOS, or Unix:

```
aws rds --region primary_region \
  delete-global-cluster \
    --global-cluster-identifier global_database_id
```

For Windows:

```
aws rds --region primary_region ^
  delete-global-cluster ^
    --global-cluster-identifier global_database_id
```

### RDS API<a name="aurora-global-database-delete.api"></a>

 To delete a cluster that is part of an Aurora global database with the RDS API, run the [DeleteGlobalCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DeleteGlobalCluster.html) action\. 

## Importing Data into an Aurora Global Database<a name="aurora-global-database-etl"></a>

 To import data into an Aurora global database, use one of the following techniques: 
+  Create a snapshot of an Aurora cluster or Amazon RDS DB instance and restore it to the primary cluster of an Aurora global database\. Currently, any snapshot must be from a source that is compatible with Aurora MySQL version 1, Aurora MySQL version 2, or Aurora PostgreSQL 10\.11\.

  The following shows an example of restoring an Aurora MySQL 5\.6 instance\.  
![\[Screenshot showing the restore snapshot page for an Aurora global database.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-databases-restore-snapshot-01.png)
+  Use point\-in\-time restore on the primary cluster to restore the cluster data to a previous state\. For more information, see [Restoring a DB Cluster to a Specified Time](USER_PIT.md)\.

 The crucial distinction for an Aurora global database is that you always import the data to the cluster that you designate as the primary cluster\. You can do the initial data import before or after adding that cluster to the Aurora Global Database\. All the data from the primary cluster is automatically replicated to any secondary clusters\. 

## Connecting to an Aurora Global Database<a name="aurora-global-database-connecting"></a>

 For read\-only query traffic, you connect to the reader endpoint for the Aurora cluster in your AWS Region\. 

 To run data manipulation language \(DML\) or data definition language \(DDL\) statements, you connect to the cluster endpoint for the primary cluster\. This endpoint might be in a different AWS Region than your application\. 

 When you view the Aurora global database in the AWS Management Console, you can see all the general\-purpose endpoints associated with all of its clusters, as shown in the following screenshot\. There is a single cluster endpoint, associated with the primary cluster, that you use for write operations\. The primary cluster and each secondary cluster has a reader endpoint, which you use for read\-only queries\. Choose whichever reader endpoint is in your AWS Region or the AWS Region closest to you, to minimize latency\. The following shows an Aurora MySQL example\.

![\[Screenshot showing an Aurora global database and associated connectivity settings (endpoints) in the AWS Management Console.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-databases-primary-cluster-connectivity.png)

## Failover for Aurora Global Database<a name="aurora-global-database-failover"></a>

 Aurora global databases introduce a higher level of failover capability than a default Aurora cluster\. If an entire cluster in one AWS Region becomes unavailable, you can promote another cluster in the global database to have read\-write capability\. 

 You can manually activate the failover mechanism if a cluster in a different AWS Region is a better choice to be the primary cluster\. For example, you might increase the capacity of one of the secondary clusters and then promote it to be the primary cluster\. Or the balance of activity among the AWS Regions might change, so that switching the primary cluster to a different AWS Region might give lower latency for write operations\. 

 The following steps outline the sequence of events when you promote one of the secondary clusters in an Aurora global database: 

1.  The primary cluster becomes unavailable\. 

1.  You remove the secondary cluster from the Aurora global database\. Doing so promotes it to full read\-write capability\. To learn how to remove an Aurora cluster from a global database, see [Removing a Cluster from an Aurora Global Database](#aurora-global-database-detaching)\. 

1.  You reconfigure your application to divert write traffic to the newly promoted cluster\. 
**Important**  
 At this point, you stop issuing any DML statements or other write operations to the cluster that became unavailable\. To avoid data inconsistencies between the clusters, known as a "split\-brain" scenario, avoid writing to the former primary cluster, even if it comes back online\. 

1.  You create a new Aurora global database with the newly promoted cluster as the primary cluster\. To learn how to create an Aurora global database, see [Creating an Aurora Global Database](#aurora-global-database-creating)\. 

1.  You add to the Aurora global database the AWS Region of the cluster that encountered the issue\. Now that AWS Region contains a new secondary cluster\. To learn how to add an AWS Region to an Aurora global database, see [Adding an AWS Region to an Aurora Global Database](#aurora-global-database-attaching)\. 

1.  When the outage is resolved and you are ready to assign your original AWS Region as the primary cluster again, you perform the same steps in reverse: 

   1.  You remove one of the secondary clusters from the Aurora global database\. 

   1.  You make that cluster the primary cluster of a new Aurora global database in the original AWS Region\. 

   1.  You redirect all the write traffic to the primary cluster in the original AWS Region\. 

   1.  You add an AWS Region to set up one or more secondary clusters in the same AWS Regions as before\. 

## Performance Insights for Aurora Global Database<a name="aurora-global-database-pi"></a>

 You can use Amazon RDS Performance Insights and Aurora Global Database together\. When you do so, the Performance Insights reports apply to each cluster in the global database individually\. You can enable or turn off Performance Insights for each cluster that's part of the global database\. When you add a new secondary region to a global database, that's already using Performance Insights, you must enable Performance Insights in the newly added cluster\. It doesn't inherit the Performance Insights setting from the existing global database\. 

 You can switch AWS Regions while viewing the Performance Insights page for a DB instance that's attached to a global database\. However, you might not see performance information immediately after switching AWS Regions\. Although the DB instances might have identical names in each AWS Region, the associated Performance Insights URL is different for each DB instance\. After switching AWS Regions, choose the name of the DB instance again in the Performance Insights navigation pane\. 

 For DB instances associated with a global database, the factors affecting performance might be different in each AWS Region\. For example, the DB instances in each region might have different capacity\. 

 For information about using Performance Insights, see [Using Amazon RDS Performance Insights](USER_PerfInsights.md)\. 

## Using Multiple Secondary Regions with Aurora Global Database<a name="aurora-global-database-multiple-secondary-regions"></a>

 When your Aurora global database includes more than one secondary region, you can choose which AWS Region to fail over to in case of an outage affecting the primary AWS Region\. To help determine which secondary region to choose, you can monitor the replication lag for all the secondary regions\. If one secondary region has substantially less replication lag than the others, that's a good indication that the associated Aurora cluster has enough database and network capacity to take over the full read\-write workload for your application\. 

 When your global database has multiple secondary regions, you must detach all the secondary regions if the primary region experiences an outage\. Then, you promote one of those secondary regions to be the new primary region\. Finally, you create new clusters in each of the other secondary regions and attach those clusters to the global database\. 

## Using Aurora Global Database with Other AWS Services<a name="aurora-global-database-interop"></a>

 In some cases, you access other AWS services in combination with an Aurora global database\. In these cases, you need the same privileges, external functions, and so on, in the corresponding AWS Regions for all the associated clusters\. Even though an Aurora cluster in a global database might start out as a read\-only replication target, you might later promote it to the primary cluster\. To prepare for that possibility, set up any necessary write privileges for other services ahead of time, for all Aurora clusters in the global database\. 

 The following list summarizes the actions to take for each AWS service\. 

**Invoking Lambda functions**  
 For all the Aurora clusters that make up the Aurora global database, perform the procedures in [Invoking a Lambda Function from an Amazon Aurora MySQL DB Cluster](AuroraMySQL.Integrating.Lambda.md)\.   
 For each cluster in the Aurora global database, set the `aws_default_lambda_role` cluster parameter to the Amazon Resource Name \(ARN\) of the new IAM role\.   
 To permit database users in an Aurora global database to invoke Lambda functions, associate the role that you created in [Creating an IAM Role to Allow Amazon Aurora to Access AWS Services](AuroraMySQL.Integrating.Authorizing.IAM.CreateRole.md) with each cluster in the Aurora global database\.   
 Configure each cluster in the Aurora global database to allow outbound connections to Lambda\. For instructions, see [Enabling Network Communication from Amazon Aurora MySQL to Other AWS Services](AuroraMySQL.Integrating.Authorizing.Network.md)\. 

**Loading data from S3**  
 For all the Aurora clusters that make up the Aurora global database, perform the procedures in [Loading Data into an Amazon Aurora MySQL DB Cluster from Text Files in an Amazon S3 Bucket](AuroraMySQL.Integrating.LoadFromS3.md)\.   
 For each Aurora cluster in the global database, set either the `aurora_load_from_s3_role` or `aws_default_s3_role` DB cluster parameter to the Amazon Resource Name \(ARN\) of the new IAM role\. If an IAM role isn't specified for `aurora_load_from_s3_role`, Aurora uses the IAM role specified in `aws_default_s3_role`\.   
 To permit database users in an Aurora global database to access Amazon S3, associate the role that you created in [Creating an IAM Role to Allow Amazon Aurora to Access AWS Services](AuroraMySQL.Integrating.Authorizing.IAM.CreateRole.md) with each Aurora cluster in the global database\.   
 Configure each Aurora cluster in the global database to allow outbound connections to Amazon S3\. For instructions, see [Enabling Network Communication from Amazon Aurora MySQL to Other AWS Services](AuroraMySQL.Integrating.Authorizing.Network.md)\. 

**Saving queried data to S3**  
 For all the Aurora clusters that make up the Aurora global database, perform the procedures in [Saving Data from an Amazon Aurora MySQL DB Cluster into Text Files in an Amazon S3 Bucket](AuroraMySQL.Integrating.SaveIntoS3.md)\.   
 For each Aurora cluster in the global database, set either the `aurora_select_into_s3_role` or `aws_default_s3_role` DB cluster parameter to the Amazon Resource Name \(ARN\) of the new IAM role\. If an IAM role isn't specified for `aurora_select_into_s3_role`, Aurora uses the IAM role specified in `aws_default_s3_role`\.   
 To permit database users in an Aurora global database to access Amazon S3, associate the role that you created in [Creating an IAM Role to Allow Amazon Aurora to Access AWS Services](AuroraMySQL.Integrating.Authorizing.IAM.CreateRole.md) with each Aurora cluster in the global database\.   
 Configure each Aurora cluster in the global database to allow outbound connections to Amazon S3\. For instructions, see [Enabling Network Communication from Amazon Aurora MySQL to Other AWS Services](AuroraMySQL.Integrating.Authorizing.Network.md)\. 