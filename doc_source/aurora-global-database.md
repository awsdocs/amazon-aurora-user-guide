# Working with Amazon Aurora Global Databases<a name="aurora-global-database"></a>

 Following, you can find a description of Amazon Aurora global databases\. An Aurora global database spans multiple AWS Regions, enabling low latency global reads and disaster recovery from region\-wide outages\. 

**Topics**
+ [Overview of Aurora Global Databases](#aurora-global-database-overview)
+ [Creating an Aurora Global Database](#aurora-global-database-creating)
+ [Adding an AWS Region to an Aurora Global Database](#aurora-global-database-attaching)
+ [Removing a Cluster from an Aurora Global Database](#aurora-global-database-detaching)
+ [Deleting an Aurora Global Database](#aurora-global-database-deleting)
+ [Importing Data into an Aurora Global Database](#aurora-global-database-etl)
+ [Managing an Aurora Global Database](#aurora-global-database-managing)
+ [Configuring an Aurora Global Database](#aurora-global-database-modifying)
+ [Connecting to Aurora Global Databases](#aurora-global-database-connecting)
+ [Failover for Aurora Global Database](#aurora-global-database-failover)
+ [Using Aurora Global Databases with Other AWS Services](#aurora-global-database-interop)

## Overview of Aurora Global Databases<a name="aurora-global-database-overview"></a>

 An Aurora global database consists of one primary AWS Region where your data is mastered, and one read\-only, secondary AWS Region\. Aurora replicates data to the secondary AWS Region with typical latency of under a second\. You issue write operations directly to the primary DB instance in the primary AWS Region\. Aurora global databases use dedicated infrastructure to replicate your data, leaving database resources available entirely to serve application workloads\. Applications with a worldwide footprint can use reader instances in the secondary Region for low latency reads\. In the unlikely event your database becomes degraded or isolated in an AWS region, you can promote the secondary AWS Region to take full read\-write workloads in under a minute\. 

 The Aurora cluster in the primary AWS Region where your data is mastered performs both read and write operations\. The cluster in the secondary region enables low\-latency reads\. You can scale up the secondary cluster independently by adding one of more DB instances \(Aurora Replicas\) to serve read\-only workloads\. For disaster recovery, you can remove and promote the secondary cluster to allow full read and write operations\. 

 Only the primary cluster performs write operations\. Clients that perform write operations connect to the DB cluster endpoint of the primary cluster\. 

**Topics**
+ [Advantages of Aurora Global Databases](#aurora-global-database.advantages)
+ [Requirements for Aurora Global Databases](#aurora-global-database-prereqs)
+ [Current Limitations of Aurora Global Databases](#aurora-global-database.limitations)

### Advantages of Aurora Global Databases<a name="aurora-global-database.advantages"></a>

 Aurora global databases use dedicated infrastructure to replicate changes between the primary DB cluster and the secondary cluster\. Aurora global databases provide the following advantages: 
+  The replication performed by an Aurora global database has limited to no performance impact on the primary DB cluster\. The resources of the DB instances are fully devoted to serve read and write workloads\. 
+  Changes are replicated between AWS Regions with minimal lag, typically less than 1 second\. 
+  The secondary cluster enables fast failover for disaster recovery\. You can typically promote a secondary cluster and make it available for writes in under a minute\. 
+  Applications in remote AWS Regions experience lower query latency when they read from a secondary cluster\. 
+  You can add up to 16 Aurora Replicas to the secondary cluster, allowing you to scale reads beyond the capacity of a single Aurora cluster\. 

### Requirements for Aurora Global Databases<a name="aurora-global-database-prereqs"></a>

 Aurora global databases are available in these AWS Regions: 
+  US East \(N\. Virginia\) 
+  US East \(Ohio\) 
+  US West \(Oregon\) 
+  EU \(Ireland\) 

 If you create an Aurora cluster through the AWS CLI or RDS API and intend to add it later to an Aurora global database, you must use the `global` value for the engine mode parameter\. 

### Current Limitations of Aurora Global Databases<a name="aurora-global-database.limitations"></a>

 The following limitations currently apply to Aurora global databases: 
+  Aurora global databases are only available for Aurora with MySQL 5\.6 compatibility\. 
+  You can't use `db.t2` instance classes for an Aurora global database\. You have a choice of `db.r3` or `db.r4` instance classes\. 
+  The secondary cluster must be in a different AWS Region than the primary cluster\. 
+  You can't create a cross\-region Read Replica from the primary cluster in same region as the secondary\. See [Replicating Amazon Aurora MySQL DB Clusters Across AWS Regions](AuroraMySQL.Replication.CrossRegion.md) for information about cross\-region Read Replicas\. 
+  The following features aren't supported for Aurora global databases: 
  +  Cloning\. See [Cloning Databases in an Aurora DB Cluster](Aurora.Managing.Clone.md) for information about cloning\. 
  +  Backtrack\. See [Backtracking an Aurora DB Cluster](AuroraMySQL.Managing.Backtrack.md) for information about backtracking\. 
  +  Parallel query\. See [Working with Parallel Query for Amazon Aurora MySQL](aurora-mysql-parallel-query.md) for information about parallel query\. 
  +  Aurora Serverless\. See [Using Amazon Aurora Serverless](aurora-serverless.md) for information about Aurora Serverless\. 

## Creating an Aurora Global Database<a name="aurora-global-database-creating"></a>

 An Aurora global database spans multiple AWS Regions\. You create the global database itself along with the the read\-write primary cluster\. Then you create a read\-only secondary cluster in another AWS Region\. 
+  To create a new global database, you create the global database and the primary cluster that it contains, and then add a secondary cluster\. 
+  If you have an existing Aurora cluster, you can take a snapshot and restore it to a new Aurora global database\. To do so, follow the procedure in [Importing Data into an Aurora Global Database](#aurora-global-database-etl)\. 

 When specifying names for the primary and secondary cluster, choose names that are different than your existing Aurora clusters, even if those clusters are in other AWS Regions\. 

**Important**  
 Before you create an Aurora global database, complete the setup tasks for your account, network, and security settings in [Setting Up for Amazon RDS](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_SettingUp.html) in the *Amazon Relational Database Service User Guide*\. 

### Console<a name="aurora-global-database-create.console"></a>

**To create an Aurora global database using the AWS Management Console**

1.  Sign in to the AWS Management Console and open the Amazon RDS console for a region where Aurora global databases are available\. 

1.  Choose **Create Database**\. 

1.  On the **Select engine** page, choose the MySQL 5\.6\-compatible Aurora engine and **Global** for **Database location**\. For an example, see the following images\. 
**Note**  
 Make sure that **Quick create** is not selected\. Turning off **Quick create** makes visible the choices that you need for Aurora global databases\. 

   1.  Choose Aurora as the engine:   
![\[Screenshot of the engine options choices when creating a database.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-databases-create-global-01a.png)

   1.  Choose MySQL 5\.6 compatibility:   
![\[Screenshot of the edition and engine version choices when creating an Aurora cluster.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-databases-create-global-01b.png)

   1.  Choose **Global** as the location:   
![\[Screenshot of the database location choices when creating an Aurora cluster. Choose "global" to create an Aurora global databases.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-databases-create-global-01c.png)
**Note**  
 Choosing **Global** sets up the global database and the primary Aurora cluster\. Once the global database is created and available, you can add the secondary AWS Region\. 

1.  Fill in the remaining settings using the same decision process as for other Aurora clusters\. For more information about creating an Aurora DB cluster, see [Creating an Amazon Aurora DB Cluster](Aurora.CreateInstance.md)\.   
![\[Screenshot showing supplemental settings when creating an Aurora global database.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-databases-create-global-02.png)

1.  Choose **Create**\. 

 When the primary DB cluster is created and available, create a secondary cluster by following the steps in [Adding an AWS Region to an Aurora Global Database](#aurora-global-database-attaching)\. 

### AWS CLI<a name="aurora-global-database-create.cli"></a>

**To create an Aurora global database using the CLI**

1.  Create an Aurora global database and then add a primary AWS Region and a DB instance in that AWS Region\. 

   1.  Create the Aurora global database itself\. Initially, no clusters are associated with it\. 

      For Linux, OS X, or Unix:

      ```
      aws rds create-global-cluster \
          --global-cluster-identifier global_database_id \
          --engine aurora \
          --engine-version 5.6.10a
      ```

      For Windows:

      ```
      aws rds create-global-cluster ^
          --global-cluster-identifier global_database_id ^
          --engine aurora ^
          --engine-version 5.6.10a
      ```

       To learn more about additional options, see `[create\-global\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/create-global-cluster.html)`\. 

   1.  Check that the Aurora global database has become available to use by using the AWS CLI `[describe\-global\-clusters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-global-clusters.html)` command, as shown in the following example\. 

      ```
      aws rds --region primary_region \
        describe-global-clusters \
          --global-cluster-identifier global_database_id    # optional
      ```

   1.  When the `describe-global-clusters` results show a status of `available`, create the primary cluster for the Aurora global database\. To do so, use the AWS CLI `[create\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html)` command\. Specify `aurora` for the `--engine` parameter, `5.6.10a` for the `--engine-version` parameter, and `global` for the `--engine-mode` parameter\. Specify the same `--global-cluster-identifier` value as when you created the global database\. The following example shows how\. 

      For Linux, OS X, or Unix:

      ```
      aws rds create-db-cluster \
        --region primary_region \
        --db-cluster-identifier db_cluster_id \
        --master-username master_userid \
        --master-user-password master_password \
        --engine aurora \
        --engine-version 5.6.10a \
        --engine-mode global \
        --global-cluster-identifier global_database_id
      ```

      For Windows:

      ```
      aws rds create-db-cluster ^
        --region primary_region ^
        --db-cluster-identifier db_cluster_id ^
        --master-username master_userid ^
        --master-user-password master_password ^
        --engine aurora ^
        --engine-version 5.6.10a ^
        --engine-mode global ^
        --global-cluster-identifier global_database_id
      ```

       global database\. 

       To learn more about additional options, see `[create\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html)`\. 

   1.  Check that the Aurora cluster has become available to use by using the AWS CLI `[describe\-db\-clusters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html)` command, as shown in the following example\. 

      ```
      aws rds --region primary_region \
        describe-db-clusters \
          --db-cluster-identifier db_cluster_id # optional
      ```

   1.  When the `describe-db-clusters` results show a status of `available`, create the primary DB instance for the primary cluster\. To do so, use the AWS CLI `[create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html)` command\. Specify `aurora` for the `--engine` parameter, `5.6.10a` for the `--engine-version` parameter, and `global` for the `--engine-mode` parameter\. The following example shows how\. 

      For Linux, OS X, or Unix:

      ```
      aws rds create-db-instance \
        --db-cluster-identifier db_cluster_id \
        --db-instance-class instance_class \
        --db-instance-identifier db_instance_id \
        --engine aurora \
        --engine-version 5.6.10a
      ```

      For Windows:

      ```
      aws rds create-db-instance ^
        --db-cluster-identifier db_cluster_id ^
        --db-instance-class instance_class ^
        --db-instance-identifier db_instance_id ^
        --engine aurora ^
        --engine-version 5.6.10a
      ```

       You don't need to include the `--master-username` and `--master-user-password` parameters, because those values are taken from the primary DB cluster\. 

       To learn more about additional options, see `[create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html)`\. 

1.  Check that the primary cluster of the Aurora global database has become available to use by using the AWS CLI `[describe\-db\-clusters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html)` command, as shown in the following example\. 

   ```
   aws rds describe-db-clusters --db-cluster-identifier sample-global-cluster
   ```

    When the `describe-db-clusters` results show a status of `available`, create the primary instance for the primary cluster cluster so that replication can begin\. To do so, use the AWS CLI `[create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html)` command as shown in the following example\. 

   For Linux, OS X, or Unix:

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

1.  Alternatively, you can create the primary cluster first, then create the global database afterwards, specifying which cluster to use as the primary cluster: 

   1.  Create an empty Aurora cluster with the `global` engine mode, and a DB instance within that cluster\. Engine mode `global` means that you can add this cluster to an Aurora global database\. 

      For Linux, OS X, or Unix:

      ```
      aws rds --region primary_region \
        create-db-cluster \
          --db-cluster-identifier primary_cluster_id \
          --master-username master_userid \
          --master-user-password master_password \
          --engine aurora \
          --engine-version 5.6.10a \
          --engine-mode global
      
      aws rds --region primary_region \
        create-db-instance \
          --db-instance-class instance_class \
          --db-cluster-identifier primary_cluster_id \
          --db-instance-identifier db_instance_id \
          --engine aurora
      ```

      For Windows:

      ```
      aws rds --region primary_region ^
        create-db-cluster ^
          --db-cluster-identifier primary_cluster_id ^
          --master-username master_userid ^
          --master-user-password master_password ^
          --engine aurora ^
          --engine-version 5.6.10a ^
          --engine-mode global
      
      aws rds --region primary_region ^
        create-db-instance ^
          --db-instance-class instance_class ^
          --db-cluster-identifier primary_cluster_id ^
          --db-instance-identifier db_instance_id ^
          --engine aurora
      ```

   1.  When the primary DB cluster and DB instance are created and available, create the Aurora global database by calling the AWS CLI `[create\-global\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/create-global-cluster.html)` command in the AWS Region where you want to create the Aurora global database\. 

      For Linux, OS X, or Unix:

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

 To create an Aurora global database with the RDS API, run the [CreateGlobalCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateGlobalCluster.html) action\. 

## Adding an AWS Region to an Aurora Global Database<a name="aurora-global-database-attaching"></a>

 After you create an Aurora global database and its primary cluster, you increase its global footprint by adding a new AWS Region\. This process involves attaching a secondary Aurora cluster\. The secondary cluster must be in a different AWS Region than the primary cluster\. 

**Note**  
 Currently, the maximum number of secondary Aurora clusters you can attach to an Aurora global database is one\. 

### Console<a name="aurora-global-database-attach.console"></a>

**To add an AWS Region to a Aurora global database using the AWS Management Console**

1.  In the top\-right corner of the AWS Management Console, choose any AWS Region where Aurora global databases are available\. 

1.  In the navigation pane, choose **Databases**\. 

1.  Choose the check box for the Aurora global database for which you want to create a secondary cluster\. If the primary cluster or DB instances inside it are still in `Creating` state, wait until they are all `Available`\. 

1.  Choose **Actions**, and then choose **Add region**\.   
![\[Screenshot showing "Add region" choice for an Aurora global database.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-databases-cluster-tree-03-global-cluster-actions.png)

1.  On the **Add a region** page, choose the secondary AWS Region\.   
![\[Screenshot showing the "Add region" dialog for an Aurora global database.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-databases-add-region-01.png)

1.  Complete the remaining fields for the Aurora cluster in the new AWS Region, and then choose **Create**\. 

### AWS CLI<a name="aurora-global-database-attach.cli"></a>

 To add an AWS Region to an Aurora global database with the AWS CLI, run the [create\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) command\. 

 The following commands create a new Aurora cluster, attach it to a global database as a read\-only secondary cluster, and then add a DB instance to the new cluster\. The `--global-cluster-identifier` option for the `create-db-cluster` command specifies the global database to which to attach the new cluster\. 

 For an encrypted cluster, specify the `--source-region` option with a value that matches the primary AWS Region\. 

For Linux, OS X, or Unix:

```
aws rds --region secondary_region \
  create-db-cluster \
    --db-cluster-identifier secondary_cluster_id \
    --global-cluster-identifier global_database_id \
    --engine aurora

aws rds --region secondary_region \
  create-db-instance \
    --db-instance-class instance_class \
    --db-cluster-identifier secondary_cluster_id \
    --db-instance-identifier db_instance_id \
    --engine aurora
```

For Windows:

```
aws rds --region secondary_region ^
  create-db-cluster ^
    --db-cluster-identifier secondary_cluster_id ^
    --global-cluster-identifier global_database_id_id ^
    --engine aurora

aws rds --region secondary_region ^
  create-db-instance ^
    --db-instance-class instance_class ^
    --db-cluster-identifier secondary_cluster_id ^
    --db-instance-identifier db_instance_id ^
    --engine aurora
```

### RDS API<a name="aurora-global-database-attach.api"></a>

 To add a new new AWS Region to an Aurora global database with the RDS API, run the [CreateGlobalCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateGlobalCluster.html) action\. 

## Removing a Cluster from an Aurora Global Database<a name="aurora-global-database-detaching"></a>

 Removing an Aurora cluster from an Aurora global database turns it back into a regional cluster, with full read\-write capability and no longer synchronized with the primary cluster\. 

 You might remove an Aurora cluster from a global database in case the primary cluster becomes degraded or isolated\. Performing a failover for an Aurora global database involves removing the secondary cluster from the original global database and then using it as the primary cluster in a new Aurora global database\. 

 If you no longer need the global database, you must remove the secondary cluster and then the primary cluster before deleting the global database itself\. You can then delete one or both of the clusters you removed, or continue to use one or both as single\-region Aurora clusters\. 

### Console<a name="aurora-global-database-detach.console"></a>

 To remove an Aurora cluster from an Aurora global database with the AWS Management Console, choose the cluster on the **Databases**, choose **Actions**, and then choose **Remove from Global**\. The removed cluster becomes a regular Aurora cluster with full read\-write capability\. It is no longer kept synchronized with the primary cluster\. 

![\[Screenshot showing confirmation prompt to remove a secondary cluster from an Aurora global database.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-databases-detach-secondary-convert-standalone.png)

 If a secondary cluster is still associated with the global database, you can't remove the primary cluster\. 

 After you remove or delete the secondary cluster, then you can remove the primary cluster the same way\. 

 After clusters are removed from an Aurora global database, you still see them in the **Databases** page, but without the **Global**, **Primary**, and **Secondary** labels\. 

![\[Screenshot showing clusters after being removed from an Aurora global database.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-databases-cluster-tree-06-after-detaching.png)

### AWS CLI<a name="aurora-global-database-detach.cli"></a>

 To remove an Aurora cluster from an Aurora global database with the AWS CLI, run the [remove\-from\-global\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/remove-from-global-cluster.html) command\. 

 The following commands remove a secondary cluster and then the primary cluster from an Aurora global database\. The clusters to remove are identified by the `--db-cluster-identifier` parameter in each case\. The Aurora global database is identified by the `--global-cluster-identifier` parameter\. 

For Linux, OS X, or Unix:

```
aws rds --region secondary_region \
  remove-from-global-cluster \
    --db-cluster-identifier secondary_cluster_ARN \
    --global-cluster-identifier global_database_id

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

aws rds --region primary_region ^
  remove-from-global-cluster ^
    --db-cluster-identifier primary_cluster_ARN ^
    --global-cluster-identifier global_database_id
```

### RDS API<a name="aurora-global-database-detach.api"></a>

 To remove an Aurora cluster from an Aurora global database with the RDS API, run the [RemoveFromGlobalCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RemoveFromGlobalCluster.html) action\. 

## Deleting an Aurora Global Database<a name="aurora-global-database-deleting"></a>

 Before you can delete an Aurora global database, you must delete or remove the secondary and primary cluster associated with the global database\. See [Removing a Cluster from an Aurora Global Database](#aurora-global-database-detaching) for the procedure to remove a cluster from the global database and make it a standalone Aurora cluster again\. 

### Console<a name="aurora-global-database-delete.console"></a>

**To delete an Aurora global database using the AWS Management Console**

 To delete a cluster that is part of an Aurora global database with the AWS Management Console, you remove or delete any secondary cluster associated with the global database, remove the primary cluster, and then delete the global database itself\. Because a global database typically holds business\-critical data, you can't delete the global database and the associated clusters in a single step\. See [Removing a Cluster from an Aurora Global Database](#aurora-global-database-detaching) for instructions on removing clusters from a global database\. See [Deleting a DB Instance in a DB Cluster](USER_DeleteInstance.md) for the procedure to delete clusters once they are removed\. Deleting the primary instance from an Aurora cluster deletes the cluster itself\. 

1.  Confirm that all other clusters are removed from the Aurora global database\. 

    If the global database includes any clusters nested underneath it, as in the following screenshot, you can't delete the global database yet\. Follow the procedure in [Removing a Cluster from an Aurora Global Database](#aurora-global-database-detaching) for each cluster associated with the global database\.   
![\[This global database can't be deleted because it still has associated clusters.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-databases-cluster-tree-08-not-empty.png)

    After the global database has no associated clusters, you can proceed to delete it\. The following screenshot shows how the `global-db-demo` cluster is no longer associated with the global database after being removed\.   
![\[All clusters are removed from the global database, so now it can be deleted.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-databases-cluster-tree-09-empty.png)

1.  From the **Databases** page, or the detail page for the global database, choose **Actions** and then choose **Delete**\. 

### AWS CLI<a name="aurora-global-database-delete.cli"></a>

 To delete a cluster that is part of an Aurora global database with the AWS CLI, run the [delete\-global\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/delete-global-cluster.html) command\. 

 The following command deletes the Aurora global database with the specified global cluster ID: 

For Linux, OS X, or Unix:

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

 To delete a cluster that is part of an Aurora global database with the RDS API, run the [DeleteGlobalCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DeleteGlobalCluster.html) operation\. 

## Importing Data into an Aurora Global Database<a name="aurora-global-database-etl"></a>

 To import data into an Aurora global database, do the same as for other kinds of Aurora clusters: 
+  Create a snapshot of an Aurora MySQL cluster or Amazon RDS MySQL DB instance and restore it to the primary cluster of an Aurora global database\. Currently, any snapshot must be from a source that is compatible with MySQL 5\.6\.   
![\[Screenshot showing the restore snapshot page for an Aurora global database.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-databases-restore-snapshot-01.png)
+  Use point\-in\-time restore on the primary cluster to restore the cluster data to a previous state\. 
+  Use a physical import technique, such as the Percona xtrabackup tool\. 
+  Use a logical import technique, such as preparing data with the `mysqldump` command and loading it with the `mysql` command\. 

 The crucial distinction for an Aurora global database is that you always import the data to the cluster that you designate as the primary cluster\. You can do the initial data import before or after adding that cluster to the Aurora Global Database\. All the data from the primary cluster is automatically replicated to the secondary cluster\. 

## Managing an Aurora Global Database<a name="aurora-global-database-managing"></a>

 You can perform most management operations on the individual clusters that make up an Aurora global database\. When you select **Group related resources** on the **Databases** page in the console, you see the primary cluster and secondary cluster grouped under the associated global database object\. 

![\[Screenshot showing how Aurora global databases are represented on the Databases page in the AWS Management Console.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-databases-cluster-tree-01.png)

 To see the properties that apply to an entire Aurora global database, choose that global database\. 

![\[Screenshot showing a selected Aurora global database and associated settings in the AWS Management Console.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-databases-global-cluster-maintenance.png)

## Configuring an Aurora Global Database<a name="aurora-global-database-modifying"></a>

 The **Databases** page in the AWS Management Console lists all your Aurora global databases, showing the primary cluster and secondary cluster for each one\. The Aurora global database is an object that has its own configuration settings, in particular the AWS Regions associated with the primary and secondary cluster\. 

![\[Screenshot showing a selected Aurora global database and associated configuration settings in the AWS Management Console.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-databases-global-cluster-configuration.png)

 You can select an Aurora global database and modify its settings, as shown in the following screenshot: 

![\[Screenshot showing the page to modify settings for an Aurora global database.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-databases-modify-global-01.png)

 You can configure the parameter groups independently for each Aurora cluster within the Aurora global database\. Most parameters work the same as for other kinds of Aurora clusters\. The `aurora_enable_repl_bin_log_filtering` and `aurora_enable_replica_log_compression` configuration settings have no effect\. We recommend keeping the settings consistent between all the clusters in a global database, to avoid unexpected behavior changes if you promote a secondary cluster to be the primary\. For example, use the same settings for time zones and character sets to avoid inconsistent behavior if a different cluster takes over as the primary cluster\. 

## Connecting to Aurora Global Databases<a name="aurora-global-database-connecting"></a>

 For read\-only query traffic, you connect to the reader endpoint for the Aurora cluster in your AWS Region\. 

 To run data manipulation language \(DML\) or data definition language \(DDL\) statements, you connect to the cluster endpoint for the primary cluster\. This endpoint might be in a different AWS Region than your application\. 

 When you view the Aurora global database in the AWS Management Console, you can see all the general\-purpose endpoints associated with all of its clusters, as shown in the following screenshot\. There is a single cluster endpoint, associated with the primary cluster, that you use for write operations\. The primary cluster and each secondary cluster has a reader endpoint, which you use for read\-only queries\. Choose whichever reader endpoint is in your AWS Region or the AWS Region closest to you, to minimize latency\. 

![\[Screenshot showing a selected Aurora global database and associated connectivity settings (endpoints) in the AWS Management Console.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-databases-primary-cluster-connectivity.png)

## Failover for Aurora Global Database<a name="aurora-global-database-failover"></a>

 Aurora global databases introduce a higher level of failover capability than a default Aurora cluster\. If an entire cluster in one AWS Region becomes unavailable, you can promote another cluster in the global database to have read\-write capability\. 

 You can manually activate the failover mechanism if a cluster in a different AWS Region is a better choice to be the primary cluster\. For example, you might increase the capacity of the secondary cluster and then promote it to be the primary cluster\. Or the balance of activity among the AWS Regions might change, so that switching the primary cluster to a different AWS Region might give lower latency for write operations\. 

 The following steps outline the sequence of events when you promote a secondary cluster in an Aurora global database: 

1.  The primary cluster becomes unavailable\. 

1.  You remove the secondary cluster from the Aurora global database\. Doing so promotes it to full read\-write capability\. To learn how to remove an Aurora cluster from a global database, see [Removing a Cluster from an Aurora Global Database](#aurora-global-database-detaching)\. 

1.  You reconfigure your application to divert write traffic to the newly promoted cluster\. 
**Important**  
 At this point, you stop issuing any DML statements or other write operations to the cluster that became unavailable\. To avoid data inconsistencies between the clusters, known as a "split\-brain" scenario, avoid writing to the former primary cluster, even if it comes back online\. 

1.  You create a new Aurora global database with the newly promoted cluster as the primary cluster\. To learn how to create an Aurora global database, see [Creating an Aurora Global Database](#aurora-global-database-creating)\. 

1.  You add to the Aurora global database the AWS Region of the cluster that encountered the issue\. Now that AWS Region contains a new secondary cluster\. To learn how to add an AWS Region to an Aurora global database, see [Adding an AWS Region to an Aurora Global Database](#aurora-global-database-attaching)\. 

1.  When the outage is resolved and you are ready to assign your original AWS Region as the primary cluster again, you perform the same steps in reverse: 

   1.  You remove the secondary cluster from the Aurora global database\. 

   1.  You make that cluster the primary cluster of a new Aurora global database in the original AWS Region\. 

   1.  You redirect all the write traffic to the primary cluster in the original AWS Region\. 

   1.  You add an AWS Region to set up a secondary cluster in the same AWS Region as before\. 

## Using Aurora Global Databases with Other AWS Services<a name="aurora-global-database-interop"></a>

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