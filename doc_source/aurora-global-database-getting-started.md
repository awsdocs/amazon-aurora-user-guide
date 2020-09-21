# Getting Started with Aurora Global Databases<a name="aurora-global-database-getting-started"></a>

 To begin using Aurora global database, you create an Aurora cluster and add one or more secondary AWS Regions\. You can load the data into the cluster either before or after adding the secondary AWS Regions\. 

**Topics**
+ [Creating an Aurora Global Database](#aurora-global-database-creating)
+ [Adding an AWS Region to an Aurora Global Database](#aurora-global-database-attaching)
+ [Importing Data into an Aurora Global Database](#aurora-global-database-etl)

## Creating an Aurora Global Database<a name="aurora-global-database-creating"></a>

 An Aurora global database spans multiple AWS Regions\. First you create the global database itself along with the read/write primary cluster\. Then you add one or more read\-only secondary clusters in other AWS Regions\. 

 When specifying names for the primary and secondary clusters, choose names that are different than your existing Aurora clusters, even if those clusters are in other AWS Regions\. 

**Important**  
 Before you create an Aurora global database, complete the setup tasks for your account, network, and security settings in [Setting Up for Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_SettingUp.html) in the Amazon RDS User Guide\. 

### Console<a name="aurora-global-database-create.console"></a>

To use the console to create a global database for Aurora MySQL version 5\.6\.10a, see the second process\.

**To create an Aurora global database**

1. Start with an existing Aurora cluster or create a new cluster\. Use Aurora MySQL version 1\.22\.0 or later, Aurora MySQL version 2\.07 or later, or Aurora PostgreSQL version 10\.11\. For more about creating a cluster, see [Creating a DB Cluster and Connecting to a Database on an Aurora MySQL DB Cluster](CHAP_GettingStartedAurora.CreatingConnecting.Aurora.md) or [Creating a DB Cluster and Connecting to a Database on an Aurora PostgreSQL DB Cluster](CHAP_GettingStartedAurora.CreatingConnecting.AuroraPostgreSQL.md)\. 

1. After your Aurora cluster is created and available, create one or more secondary clusters\. To do so, follow the steps in [Adding an AWS Region to an Aurora Global Database](#aurora-global-database-attaching)\. Each secondary cluster is in a different AWS Region than the original cluster\. 

 Aurora MySQL version 5\.6\.10a requires you to choose to make the cluster compatible with Aurora global database at cluster creation time\. You see the **Global** choice in the AWS Management Console only when creating a cluster with this Aurora MySQL version\. For Aurora MySQL version 1\.22 and later, you can use any cluster with an Aurora global database without making any special choice when creating the cluster\. 

 For Aurora MySQL versions between 5\.6\.10a and 1\.22, you don't make any special choice when creating the cluster\. However, to use such a cluster in an Aurora global database, make sure to upgrade it to Aurora MySQL version 1\.22 or later first\. 

**To create an Aurora MySQL global database \(version 5\.6\.10a only\)**

1.  Sign in to the AWS Management Console and open the Amazon RDS console for an AWS Region where Aurora global databases are available\. For more information, see [Limitations of Aurora Global Databases](aurora-global-database.md#aurora-global-database.limitations)\. 

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
 Choosing **Global** sets up the global database and the primary Aurora cluster\. After the global database is created and available, you can add a secondary AWS Region\. 

1.  Fill in the remaining settings using the same decision process as for other Aurora clusters\. For more information about creating an Aurora DB cluster, see [Creating an Amazon Aurora DB Cluster](Aurora.CreateInstance.md)\.   
![\[Screenshot showing supplemental settings when creating an Aurora global database.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-databases-create-global-02.png)

1.  Choose **Create**\. 

 When the primary DB cluster is created and available, create one or more secondary clusters by following the steps in [Adding an AWS Region to an Aurora Global Database](#aurora-global-database-attaching)\. 

### AWS CLI<a name="aurora-global-database-create.cli"></a>

To create an Aurora global database using the CLI, use either of the following procedures:
+ Create an Aurora global database and then add a primary AWS Region and a DB instance in that AWS Region\. 
+ Create the primary cluster first, then create the global database afterward, specifying which cluster to use as the primary cluster\.

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
        --master-username userid \
        --master-user-password password \
        --engine { aurora | aurora-mysql | aurora-postgresql } \
        --engine-version version \
        --global-cluster-identifier global_database_id
      ```

      For Windows:

      ```
      aws rds create-db-cluster ^
        --region primary_region ^
        --db-cluster-identifier db_cluster_id ^
        --master-username userid ^
        --master-user-password password ^
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

As an alternative procedure, you can create the primary DB cluster first and then create the global database afterward\.

**To create an Aurora global database by creating the primary DB cluster and then creating the global database**

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
       --master-username userid \
       --master-user-password password \
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
       --master-username userid ^
       --master-user-password password ^
       --engine { aurora | aurora-mysql | aurora-postgresql } ^
       --engine-version version
   
   aws rds --region primary_region ^
     create-db-instance ^
       --db-instance-class instance_class ^
       --db-cluster-identifier primary_cluster_id ^
       --db-instance-identifier db_instance_id ^
       --engine { aurora | aurora-mysql | aurora-postgresql }
   ```

1.  Create a DB instance within the Aurora DB cluster using the following values:
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

1. After the primary DB cluster and DB instance are created and available, create the Aurora global database\. Do this by calling the AWS CLI `[create\-global\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/create-global-cluster.html)` command in the AWS Region where you want to create the Aurora global database\. Use the `--source-db-cluster-identifier` parameter to specify the primary DB cluster created earlier\.

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
The maximum number of secondary Aurora clusters you can attach to an Aurora global database is five\. Thus, the most clusters you can associate with a global database is six: one primary cluster with a read/write instance and optional reader instances, and five read\-only secondary clusters\.   
 For each secondary cluster in a global database, the maximum number of reader DB instances in the primary cluster is reduced by one\. For example, if your global database has three secondary clusters, the maximum number of reader DB instances in the primary cluster is 12 instead of 15\. In some cases, the number of reader instances in the primary cluster plus the number of secondary clusters might total 15\. If it does, you can't add any more secondary clusters to the global database\. 

### Console<a name="aurora-global-database-attach.console"></a>

**To add an AWS Region to an Aurora global database**

1.  In the navigation pane of the AWS Management Console, choose **Databases**\. 

1.  Choose the check box for the Aurora global database for which you want to create a secondary cluster\. If the primary cluster or DB instances inside it are still in the `Creating` state, wait until they are all `Available`\. 

1.  For **Actions**, choose **Add a region**\.   
![\[Screenshot showing "Add region" choice for an Aurora global database.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-databases-cluster-tree-03-global-cluster-actions.png)

1.  On the **Add a region** page, choose the secondary AWS Region\.   
![\[Screenshot showing the "Add a region" dialog for an Aurora global database.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-databases-add-region-01.png)

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

## Importing Data into an Aurora Global Database<a name="aurora-global-database-etl"></a>

 To import data into an Aurora global database, use one of the following techniques: 
+  Create a snapshot of an Aurora cluster or Amazon RDS DB instance and restore it to the primary cluster of an Aurora global database\. Currently, make sure that any import snapshot is from a source that is compatible with Aurora MySQL version 1, Aurora MySQL version 2, or Aurora PostgreSQL 10\.11\.

  The following shows an example of restoring an Aurora MySQL 5\.6 instance\.  
![\[Screenshot showing the restore snapshot page for an Aurora global database.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-databases-restore-snapshot-01.png)
+  Use point\-in\-time restore on the primary cluster to restore the cluster data to a previous state\. For more information, see [Restoring a DB Cluster to a Specified Time](USER_PIT.md)\.

 The crucial distinction for an Aurora global database is that you always import the data to the cluster that you designate as the primary cluster\. You can do the initial data import before or after adding that cluster to the Aurora global database\. All the data from the primary cluster is automatically replicated to any secondary clusters\. 