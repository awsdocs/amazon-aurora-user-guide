# Managing an Aurora Global Database<a name="aurora-global-database-managing"></a>

 You can perform most management operations on the individual clusters that make up an Aurora global database\. When you choose **Group related resources** on the **Databases** page in the console, you see the primary cluster and secondary clusters grouped under the associated global database object\. 

![\[Screenshot showing how Aurora global databases are represented on the Databases page in the AWS Management Console.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-databases-cluster-tree-01.png)

 To see the properties that apply to an entire Aurora global database, choose that global database\. 

![\[Screenshot showing a selected Aurora global database and associated settings in the AWS Management Console.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-databases-global-cluster-maintenance.png)

**Topics**
+ [Configuring an Aurora Global Database](#aurora-global-database-modifying)
+ [Monitoring an Aurora Global Database](#aurora-global-database-monitoring)
+ [Cross\-Region Disaster Recovery for Aurora Global Databases](#aurora-global-database-disaster-recovery)
+ [Performance Insights for Aurora Global Databases](#aurora-global-database-pi)
+ [Removing a Cluster from an Aurora Global Database](#aurora-global-database-detaching)
+ [Deleting an Aurora Global Database](#aurora-global-database-deleting)

## Configuring an Aurora Global Database<a name="aurora-global-database-modifying"></a>

 The **Databases** page in the AWS Management Console lists all your Aurora global databases, showing the primary cluster and secondary clusters for each one\. The Aurora global database is an object that has its own configuration settings, in particular the AWS Regions associated with the primary and secondary clusters\. The following shows an Aurora MySQL example\.

![\[Screenshot showing a selected Aurora global database and associated configuration settings in the AWS Management Console.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-databases-global-cluster-configuration.png)

 You can select an Aurora global database and modify its settings, as shown in the following screenshot\.

![\[Screenshot showing the page to modify settings for an Aurora global database.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-databases-modify-global-01.png)

You can configure the parameter groups independently for each Aurora cluster within the Aurora global database\. Most parameters work the same as for other kinds of Aurora clusters\. We recommend keeping the settings consistent among all the clusters in a global database, to avoid unexpected behavior changes if you promote a secondary cluster to be the primary\. For example, use the same settings for time zones and character sets to avoid inconsistent behavior if a different cluster takes over as the primary cluster\. 

The `aurora_enable_repl_bin_log_filtering` and `aurora_enable_replica_log_compression` configuration settings have no effect\. 

## Monitoring an Aurora Global Database<a name="aurora-global-database-monitoring"></a>

To view the status of a global database, use the `aurora_global_db_status` and `aurora_global_db_instance_status` functions\. 

**Note**  
Only Aurora PostgreSQL supports the `aurora_global_db_status` and `aurora_global_db_instance_status` functions\.

**To monitor a global database**

1. Connect to the global database primary cluster endpoint using a PostgreSQL utility such as psql\. For more information about how to connect, see [Connecting to an Aurora Global Database](aurora-global-database-connecting.md)\.

1. Use the `aurora_global_db_status` function in a psql command to list the primary and secondary volumes\. This shows the lag times of the global database secondary DB clusters\.

   ```
   postgres=> select * from aurora_global_db_status();
   ```

   ```
   aws_region | highest_lsn_written | durability_lag_in_msec | rpo_lag_in_msec | last_lag_calculation_time  | feedback_epoch | feedback_xmin
   ------------+---------------------+------------------------+-----------------+----------------------------+----------------+---------------
   us-east-1  |         93763984222 |                     -1 |              -1 | 1970-01-01 00:00:00+00     |              0 |             0
   us-west-2  |         93763984222 |                    900 |            1090 | 2020-05-12 22:49:14.328+00 |              2 |    3315479243
   (2 rows)
   ```

   The output includes a row for each DB cluster of the global database containing the following columns:
   + **aws\_region** – The AWS Region that this DB cluster is in\. For tables listing AWS Regions by engine, see [ Regions and Availability Zones](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Concepts.RegionsAndAvailabilityZones.html#Aurora.Overview.Availability)\.
   + **highest\_lsn\_written** – The highest log sequence number \(LSN\) currently written on this DB cluster\. 

     A *log sequence number \(LSN\)* is a unique sequential number that identifies a record in the database transaction log\. LSNs are ordered such that a larger LSN represents a later transaction\.
   + **durability\_lag\_in\_msec** – The timestamp difference between the highest log sequence number written on a secondary DB cluster \(`highest_lsn_written`\) and the `highest_lsn_written` on the primary DB cluster\.
   + **rpo\_lag\_in\_msec** – The recovery point objective \(RPO\) lag\. This lag is the time difference between the most recent user transaction commit stored on a secondary DB cluster and the most recent user transaction commit stored on the primary DB cluster\.
   + **last\_lag\_calculation\_time** – The timestamp when values were last calculated for `replication_lag_in_msec` and `rpo_lag_in_msec`\.
   + **feedback\_epoch** – The epoch the secondary DB cluster uses when it generates hot standby information\.

     *Hot standby *is when a DB cluster can connect and query while the server is in recovery or standby mode\. Hot standby feedback is information about the DB cluster when it's in hot standby\. For more information, see [Hot Standby](https://www.postgresql.org/docs/current/hot-standby.html) in the PostgreSQL documentation\.
   + **feedback\_xmin** – The minimum \(oldest\) active transaction ID used by the secondary DB cluster\.

1. Use the `aurora_global_db_instance_status` function to list all secondary DB instances for both the primary DB cluster and secondary DB clusters\.

   ```
   postgres=> select * from aurora_global_db_instance_status();
   ```

   ```
   server_id                                   |              session_id              | aws_region | durable_lsn | highest_lsn_rcvd | feedback_epoch | feedback_xmin | oldest_read_view_lsn | visibility_lag_in_msec
   --------------------------------------------+--------------------------------------+------------+-------------+------------------+----------------+---------------+----------------------+------------------------
   apg-global-db-rpo-mammothrw-elephantro-1-n1 | MASTER_SESSION_ID                    | us-east-1  | 93763985102 |                  |                |               |                      |
   apg-global-db-rpo-mammothrw-elephantro-1-n2 | f38430cf-6576-479a-b296-dc06b1b1964a | us-east-1  | 93763985099 |      93763985102 |              2 |    3315479243 |          93763985095 |                     10
   apg-global-db-rpo-elephantro-mammothrw-n1   | 0d9f1d98-04ad-4aa4-8fdd-e08674cbbbfe | us-west-2  | 93763985095 |      93763985099 |              2 |    3315479243 |          93763985089 |                   1017
   (3 rows)
   ```

   The output includes a row for each DB instance of the global database containing the following columns:
   + **server\_id** – The server identifier for the DB instance\.
   + **session\_id** – A unique identifier for the current session\.
   + **aws\_region** – The AWS Region that this DB instance is in\. For tables listing AWS Regions by engine, see [ Regions and Availability Zones](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Concepts.RegionsAndAvailabilityZones.html#Aurora.Overview.Availability)\.
   + **durable\_lsn** – The LSN made durable in storage\.
   + **highest\_lsn\_rcvd** – The highest LSN received by the DB Instance from the writer DB Instance\.
   + **feedback\_epoch** – The epoch the DB instance uses when it generates hot standby information\.

     Hot standby is when a DB instance can connect and query while the server is in recovery or standby mode\. Hot standby feedback is information about the DB instance when it's in hot standby\. For more information, see the PostgreSQL documentation on [Hot Standby](https://www.postgresql.org/docs/current/hot-standby.html)\.
   + **feedback\_xmin** – The minimum \(oldest\) active transaction ID used by the DB instance\.
   + **oldest\_read\_view\_lsn** – The oldest LSN used by the DB instance to read from storage\.
   + **visibility\_lag\_in\_msec** – How far this DB instance is lagging behind the writer DB instance\.

To see how these values change over time, consider the following transaction block where a table insert takes an hour\.

```
psql> BEGIN;
psql> INSERT INTO table1 SELECT Large_Data_That_Takes_1_Hr_To_Insert;
psql> COMMIT;
```

In some cases, there might be a network disconnect between the primary DB cluster and the secondary DB cluster after the `BEGIN` statement\. If so, the secondary DB cluster's `replication_lag_in_msec` value starts increasing\. At the end of the `INSERT` statement, the `replication_lag_in_msec` value is 1 hour\. However, the `rpo_lag_in_msec` value is 0 because all the user data committed between the primary DB cluster and secondary DB cluster are still the same\. As soon as the `COMMIT` statement completes, the `rpo_lag_in_msec` value increases\. 

## Cross\-Region Disaster Recovery for Aurora Global Databases<a name="aurora-global-database-disaster-recovery"></a>

In some cases, your Aurora global database might include more than one secondary AWS Region\. If so, you can choose which AWS Region to fail over to if an outage affects the primary AWS Region\. To help determine which secondary AWS Region to choose, you can monitor the replication lag for all your secondary Regions\. In some cases, one secondary Region might have substantially less replication lag than the others\. If so, that's a good indication that the associated Aurora cluster has enough database and network capacity to take over the full read/write workload for your application\. 

When your global database has multiple secondary Regions, make sure that you detach all the secondary Regions if the primary AWS Region experiences an outage\. Then, you promote one of those secondary Regions to be the new primary AWS Region\. Finally, you create new clusters in each of the other secondary Regions and attach those clusters to your global database\. 

When you promote a secondary cluster to be the primary cluster, you also need to update the endpoints that your applications use to connect to the global database\. To get a new writer endpoint from a newly promoted cluster, you can convert a former reader endpoint by removing `-ro` from the endpoint string\. For example, if a former reader endpoint is `global-16rr-test-cluster-1.cluster-ro-12345678901.us-west-2.rds.amazonaws.com`, then the new promoted writer endpoint is `global-16rr-test-cluster-1.cluster-cps2igpwyrwa.us-west-2.rds.amazonaws.com`\. 

**Topics**
+ [Failover for Aurora Global Databases](#aurora-global-database-failover)
+ [Managing Recovery for Aurora Global Databases](#aurora-global-database-manage-recovery)

### Failover for Aurora Global Databases<a name="aurora-global-database-failover"></a>

Aurora global databases introduce a higher level of failover capability than a default Aurora cluster\. If an entire cluster in one AWS Region becomes unavailable, you can promote another cluster in the global database to have read/write capability\. 

You can manually activate the failover mechanism if a cluster in a different AWS Region is a better choice to be the primary cluster\. For example, you might increase the capacity of one of the secondary clusters and then promote it to be the primary cluster\. Or the balance of activity among the AWS Regions might change, so that switching the primary cluster to a different AWS Region might give lower latency for write operations\. 

The following procedure outlines what to do to promote one of the secondary clusters in an Aurora global database\. 

**To promote a secondary cluster**

Before you begin the procedure, the primary cluster fails or otherwise becomes unavailable\. 

1. Remove the secondary cluster from the Aurora global database\. Doing so promotes the cluster to full read/write capability\. To learn how to remove an Aurora cluster from a global database, see [Removing a Cluster from an Aurora Global Database](#aurora-global-database-detaching)\. 

1. Reconfigure your application to divert write traffic to the newly promoted cluster\. 
**Important**  
 At this point, stop issuing any DML statements or other write operations to the cluster that became unavailable\. To avoid data inconsistencies between the clusters, known as a *"split\-brain" scenario*, avoid writing to the former primary cluster, even if it comes back online\. 

1.  Create a new Aurora global database with the newly promoted cluster as the primary cluster\. To learn how to create an Aurora global database, see [Creating an Aurora Global Database](aurora-global-database-getting-started.md#aurora-global-database-creating)\. 

1. Add to the Aurora global database the AWS Region of the cluster that encountered the issue\. Now that AWS Region contains a new secondary cluster\. To learn how to add an AWS Region to an Aurora global database, see [Adding an AWS Region to an Aurora Global Database](aurora-global-database-getting-started.md#aurora-global-database-attaching)\. 

1.  When the outage is resolved and you're ready to assign your original AWS Region as the primary cluster again, perform the same steps in reverse: 

   1. Remove one of the secondary clusters from the Aurora global database\. 

   1. Make that cluster the primary cluster of a new Aurora global database in the original AWS Region\. 

   1. Redirect all the write traffic to the primary cluster in the original AWS Region\. 

   1.  Add an AWS Region to set up one or more secondary clusters in the same AWS Regions as before\. 

### Managing Recovery for Aurora Global Databases<a name="aurora-global-database-manage-recovery"></a>

You can control the point at which a global database resumes, in case of disasters such as network or hardware failures that affect the primary DB cluster\. You manage recovery by setting a recovery point objective \(RPO\) for the global database\. 

**Note**  
Managing the RPO is currently supported for Amazon Aurora with PostgreSQL compatibility\. For a list of supported Aurora PostgreSQL versions and AWS Regions, see [Limitations of Aurora Global Databases](aurora-global-database.md#aurora-global-database.limitations)\.

The *recovery point objective* is the oldest age of data that you want recoverable from a secondary DB cluster\. The RPO is used when your database resumes operations in a new AWS Region after a failover\. It represents the amount of data loss measured in time that you're willing to accept on a database failover\.

By using a managed RPO, you can control the maximum amount of data loss as the secondary DB clusters lag behind the primary DB cluster\. This data loss is measured in time and is called the *RPO lag time\.* When you set the RPO, Aurora PostgreSQL enforces it on your global database as follows:

1. Aurora PostgreSQL allows transactions to commit on the primary DB cluster if the RPO lag time of at least one secondary DB cluster is less than the RPO time\. 

1. Aurora PostgreSQL blocks transaction commits if there is no secondary DB cluster that has an RPO lag time less than the RPO time\. If Aurora PostgreSQL starts blocking commits, it inserts an event into the PostgreSQL log file\. It then emits wait events that show the sessions that are blocked\. 

1. Aurora PostgreSQL allows transactions to commit again on the primary DB cluster when the RPO lag time of at least one secondary DB cluster becomes less than the RPO time\. 

This approach ensures that Aurora PostgreSQL doesn't allow transaction commits to complete that would result in a violation of your chosen RPO time\. 

**Topics**
+ [Viewing the Recovery Point Objective](#aurora-global-database-view-rpo)
+ [Setting the Recovery Point Objective](#aurora-global-database-set-rpo)
+ [Disabling the Recovery Point Objective](#aurora-global-database-disable-rpo)

#### Viewing the Recovery Point Objective<a name="aurora-global-database-view-rpo"></a>

The recovery point objective \(RPO\) of a global database is stored in the `rds.global_db_rpo` parameter\. To view the current RPO setting of the `rds.global_db_rpo` parameter and other parameters of the cluster parameter group, see [Viewing Parameter Values for a DB Cluster Parameter Group](USER_WorkingWithParamGroups.md#USER_WorkingWithParamGroups.ViewingCluster)\.

#### Setting the Recovery Point Objective<a name="aurora-global-database-set-rpo"></a>

You can set the global database's RPO using the AWS Management Console, the AWS CLI, or the RDS API\. For more information, see [Modifying Parameters in a DB Cluster Parameter Group](USER_WorkingWithParamGroups.md#USER_WorkingWithParamGroups.ModifyingCluster)\.

##### Console<a name="aurora-global-database-set-rpo.Console"></a>

**To set the RPO**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Identify the primary DB cluster's parameter group of the global database\. For more information about a cluster's configuration settings, see [Configuring an Aurora Global Database](#aurora-global-database-modifying)\.

1. Open the primary DB cluster parameter group and set the **rds\.global\_db\_rpo** parameter\. For more information, see [Modifying Parameters in a DB Cluster Parameter Group](USER_WorkingWithParamGroups.md#USER_WorkingWithParamGroups.ModifyingCluster)\.

1. Set the **rds\.global\_db\_rpo** parameter value to the number of seconds you want for the recover point objective\. Valid values are from 20 seconds up to the maximum integer value of 2,147,483,647 \(68 years\)\.

##### AWS CLI<a name="aurora-global-database-set-rpo.CLI"></a>

To set the `rds.global_db_rpo` parameter, use the AWS CLI, use the [modify\-db\-cluster\-parameter\-group](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster-parameter-group.html) command\. 

The following example sets the RPO to 5,000 seconds for the primary DB cluster parameter group named `global_db_cluster_parameter_group`\. Valid values are from 20 seconds up to the maximum integer value of 2,147,483,647 \(68 years\)\.

For Linux, macOS, or Unix:

```
aws rds modify-db-cluster-parameter-group \
    --db-cluster-parameter-group-name global_db_cluster_parameter_group \
    --parameters "ParameterName=rds.global_db_rpo,ParameterValue=5000,ApplyMethod=immediate"
```

For Windows:

```
aws rds modify-db-cluster-parameter-group ^
    --db-cluster-parameter-group-name global_db_cluster_parameter_group ^
    --parameters "ParameterName=rds.global_db_rpo,ParameterValue=5000,ApplyMethod=immediate"
```

##### RDS API<a name="aurora-global-database-set-rpo.API"></a>

To modify the `rds.global_db_rpo` parameter, use the Amazon RDS [ ModifyDBClusterParameterGroup](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBClusterParameterGroup.html) API operation\.

#### Disabling the Recovery Point Objective<a name="aurora-global-database-disable-rpo"></a>

To disable the RPO, reset the `rds.global_db_rpo` parameter\. You can reset parameters using the AWS Management Console, the AWS CLI, or the RDS API\.

##### Console<a name="aurora-global-database-set-rpo.Console"></a>

**To disable the RPO**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

1. In the list, choose your primary DB cluster parameter group\.

1. Choose **Edit parameters**\.

1. Choose the box next to the **rds\.global\_db\_rpo** parameter\.

1. Choose **Reset**\. 

1. When the screen shows **Reset parameters in DB parameter group**, choose **Reset parameters**\.

For more information on how to reset a parameter with the console, see [Modifying Parameters in a DB Cluster Parameter Group](USER_WorkingWithParamGroups.md#USER_WorkingWithParamGroups.ModifyingCluster)\.

##### AWS CLI<a name="aurora-global-database-set-rpo.CLI"></a>

To reset the `rds.global_db_rpo` parameter, use the [reset\-db\-cluster\-parameter\-group](https://docs.aws.amazon.com/cli/latest/reference/rds/reset-db-cluster-parameter-group.html) command\.

For Linux, macOS, or Unix:

```
aws rds reset-db-cluster-parameter-group \
    --db-cluster-parameter-group-name global_db_cluster_parameter_group \
    --parameters "ParameterName=rds.global_db_rpo,ApplyMethod=immediate"
```

For Windows:

```
aws rds reset-db-cluster-parameter-group ^
    --db-cluster-parameter-group-name global_db_cluster_parameter_group ^
    --parameters "ParameterName=rds.global_db_rpo,ApplyMethod=immediate"
```

##### RDS API<a name="aurora-global-database-set-rpo.API"></a>

To reset the `rds.global_db_rpo` parameter, use the Amazon RDS API [ ResetDBClusterParameterGroup](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ResetDBClusterParameterGroup.html) operation\.

## Performance Insights for Aurora Global Databases<a name="aurora-global-database-pi"></a>

 You can use Amazon RDS Performance Insights and Aurora global databases together\. When you do so, the Performance Insights reports apply to each cluster in the global database individually\. You can enable or turn off Performance Insights for each cluster that's part of the global database\. When you add a new secondary AWS Region to a global database that's already using Performance Insights, make sure that you enable Performance Insights in the newly added cluster\. It doesn't inherit the Performance Insights setting from the existing global database\. 

 You can switch AWS Regions while viewing the Performance Insights page for a DB instance that's attached to a global database\. However, you might not see performance information immediately after switching AWS Regions\. Although the DB instances might have identical names in each AWS Region, the associated Performance Insights URL is different for each DB instance\. After switching AWS Regions, choose the name of the DB instance again in the Performance Insights navigation pane\. 

 For DB instances associated with a global database, the factors affecting performance might be different in each AWS Region\. For example, the DB instances in each AWS Region might have different capacity\. 

 For information about using Performance Insights, see [Using Amazon RDS Performance Insights](USER_PerfInsights.md)\. 

## Removing a Cluster from an Aurora Global Database<a name="aurora-global-database-detaching"></a>

 Removing an Aurora cluster from an Aurora global database turns it back into a regional cluster, with full read/write capability and no longer synchronized with the primary cluster\. 

 You might remove an Aurora cluster from a global database in case the primary cluster becomes degraded or isolated\. Performing a failover for an Aurora global database involves removing one of the secondary clusters from the original global database\. You then use the cluster as the primary cluster in a new Aurora global database\. For more information, see [Failover for Aurora Global Databases](#aurora-global-database-failover)\. 

 If you no longer need the global database, make sure that you remove all secondary clusters and then the primary cluster before deleting the global database itself\. You can then delete some or all of the clusters you removed, or continue to use some or all as single\-Region Aurora clusters\. For more information, see [Deleting an Aurora Global Database](#aurora-global-database-deleting)\.

### Console<a name="aurora-global-database-detach.console"></a>

**To remove an Aurora cluster from an Aurora global database**

1. Choose the cluster on the **Databases** page\.

1.  For **Actions**, choose **Remove from Global**\. The removed cluster becomes a regular Aurora cluster with full read/write capability\. It is no longer kept synchronized with the primary cluster\.   
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

 Before you can delete an Aurora global database, make sure that you first remove or delete the global database's clusters\. Because a global database typically holds business\-critical data, you can't delete the global database and the associated clusters in a single step\. For instructions about how to remove a cluster from a global database, which makes it a standalone Aurora cluster again, see [Removing a Cluster from an Aurora Global Database](#aurora-global-database-detaching)\. For a procedure that demonstrates how to delete clusters after they are removed, see [Deleting a DB Instance in an Aurora DB Cluster](USER_DeleteInstance.md)\.

### Console<a name="aurora-global-database-delete.console"></a>

To delete an Aurora global database with the AWS Management Console, you first remove or delete all secondary clusters associated with the global database\. You then remove the primary cluster, and finally delete the global database itself\. 

Deleting the writer instance from an Aurora cluster deletes the cluster itself\. Because a global database typically holds business\-critical data, you can't delete the global database and the associated clusters in a single step\. 

For instructions on removing clusters from a global database, see [Removing a Cluster from an Aurora Global Database](#aurora-global-database-detaching)\. For a procedure showing how to delete clusters after they are removed, see [Deleting a DB Instance in an Aurora DB Cluster](USER_DeleteInstance.md)\. Deleting the primary instance from an Aurora cluster deletes the cluster itself\. 

**To delete an Aurora global database using the AWS Management Console**

1.  Confirm that all other clusters are removed from the Aurora global database\. 

    If the global database includes any clusters nested underneath it, as in the following screenshot, you can't delete the global database yet\. Follow the procedure in [Removing a Cluster from an Aurora Global Database](#aurora-global-database-detaching) for each cluster associated with the global database\.   
![\[The global database shown preceding can't be deleted because it still has associated clusters.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-databases-cluster-tree-08-not-empty.png)

   After the global database has no associated clusters, you can delete it\. The following screenshot shows how the `global-db-demo` cluster is no longer associated with the global database after being removed\.   
![\[All clusters are removed from the global database, so now it can be deleted.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-databases-cluster-tree-09-empty.png)

1.  On the **Databases** page, or the detail page for the global database, choose **Delete** for **Actions**\. 

### AWS CLI<a name="aurora-global-database-delete.cli"></a>

 To delete a cluster that is part of an Aurora global database with the AWS CLI, run the [delete\-global\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/delete-global-cluster.html) command\. 

 The following command deletes the Aurora global database with the specified global cluster ID\. 

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

 To delete a cluster that is part of an Aurora global database with the RDS API, run the [DeleteGlobalCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DeleteGlobalCluster.html) operation\.  