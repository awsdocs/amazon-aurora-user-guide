# Using logical replication to perform a major version upgrade for Aurora PostgreSQL<a name="AuroraPostgreSQL.MajorVersionUpgrade"></a>

When you deploy a blue\-green major version upgrade, you continue to use the current version of Aurora PostgreSQL database while gradually migrating the changing data to the new major version database\. The current version of the database is referred as the "blue" environment and the new database version is referred as the "green" environment\. The blue\-green upgrade process uses Aurora fast cloning and logical replication in PostgreSQL for upgrading\.

Aurora fast cloning fully loads the existing data by taking a snapshot of the source database\. Fast cloning uses a copy\-on\-write protocol built on top of the Aurora storage layer, which allows you to create a clone of database in a short time\. This method is very effective when upgrading to a large database\.

Logical replication in PostgreSQL tracks and transfers your data changes from initial instance to a new instance running in parallel until you move to the newer version of PostgreSQL\. Logical replication uses a publish and subscribe model\. For more information about Aurora PostgreSQL logical replication, see [Replication with Amazon Aurora PostgreSQL](AuroraPostgreSQL.Replication.md)

**Topics**
+ [Requirements](#AuroraPostgreSQL.MajorVersionUpgrade.Requirements)
+ [Limitations](#AuroraPostgreSQL.MajorVersionUpgrade.Limitations)
+ [Setting and checking parameter values](#AuroraPostgreSQL.MajorVersionUpgrade.Parameters)
+ [Upgrading Aurora PostgreSQL to a new major version](#AuroraPostgreSQL.MajorVersionUpgrade.StartLogicalReplication)
+ [Performing post\-upgrade tasks](#AuroraPostgreSQL.MajorVersionUpgrade.PostUpgrade)

## Requirements<a name="AuroraPostgreSQL.MajorVersionUpgrade.Requirements"></a>

You must meet the following requirements to perform a blue\-green upgrade:
+ You must have rds\_superuser permissions\.
+ Your Aurora PostgreSQL DB cluster must be running the latest minor version available\. Make sure to apply any minor version updates and patches to your Aurora PostgreSQL DB cluster before configuring replication\. The `aurora_volume_logical_start_lsn` function that's used in this technique is built into the highest minor version of each major Aurora PostgreSQL release\.
+ All of your tables must have a primary key or include a [PostgreSQL identity column](https://www.postgresql.org/docs/current/sql-createtable.html)\.
+ Configure the security group for your VPC to allow inbound and outbound access between the two Aurora PostgreSQL DB clusters, both old and new\. You can grant access to a specific classless inter\-domain routing \(CIDR\) range or to another security group in your VPC or in a peer VPC\. \(Peer VPC requires a VPC peering connection\.\)

**Note**  
For detailed information about the permissions required to configure and manage a running logical replication scenario, see the [PostgreSQL core documentation](https://www.postgresql.org/docs/13/logical-replication-security.html)\. 

## Limitations<a name="AuroraPostgreSQL.MajorVersionUpgrade.Limitations"></a>

 When you upgrade your Aurora PostgreSQL DB cluster to a new major version by deploying the blue\-green upgrade process, you are using the native PostgreSQL logical replication feature\. It has the same capabilities and limitations\. For more information about the limitations of PostgreSQL logical replication, see [PostgreSQL logical replication restrictions](https://www.postgresql.org/docs/13/logical-replication-restrictions.html)\. 
+ Data definition language \(DDL\) commands are not replicated\. 
+ Replication doesn't support schema changes in a live database\. The schema is recreated in its original form during the cloning process\. If you change the schema after cloning, but before completing the upgrade, it isn't reflected in the upgraded instance\.
+ Large objects are not replicated, but you can store data in normal tables\.
+ Replication is only supported by tables, including partitioned tables\. Replication to other types of relations, such as views, materialized views, or foreign tables, is not supported\.
+ Sequence data is not replicated and requires a manual update post\-failover\.

**Note**  
This upgrade doesn't support auto\-scripting\. You should perform all the steps manually\.

## Setting and checking parameter values<a name="AuroraPostgreSQL.MajorVersionUpgrade.Parameters"></a>

 Before upgrading, configure the writer instance of your Aurora PostgreSQL DB cluster to act as a publication server\. The instance should use a custom DB cluster parameter group with the following settings: 
+ `rds.logical_replication` – Set this parameter to 1\. The `rds.logical_replication` parameter serves the same purpose as a standalone PostgreSQL server's `wal_level` parameter and other parameters that control the write\-ahead log file management\.
+ `max_replication_slots` – Set this parameter to the total number of subscriptions that you plan to create\. If you are using AWS DMS, set this parameter to the number of AWS DMS tasks that you plan to use for changed data capture from this DB cluster\.
+ `max_wal_senders` – Set to the number of concurrent connections, plus a few extra, to make available for management tasks and new sessions\. If you are using AWS DMS, the number of max\_wal\_senders should be equal to the number of concurrent sessions plus the number of AWS DMS tasks that may be working at any given time\. 
+ `max_logical_replication_workers` – Set to the number of logical replication workers and table synchronization workers that you expect\. It's generally safe to set the number of replication workers to the same value used for max\_wal\_senders\. The workers are taken from the pool of background processes \(max\_worker\_processes\) allocated for the server\.
+  `max_worker_processes` – Set to the number of background processes for the server\. This number should be large enough to allocate workers for replication, auto\-vacuum processes, and other maintenance processes that may take place concurrently\.

When you upgrade to a newer version of Aurora PostgreSQL, you need to duplicate any parameters that you modified in the earlier version of the parameter group\. These parameters are applied to the upgraded version\. You can query the `pg_settings` table to get a list of parameter settings so that you can re\-create them on your new Aurora PostgreSQL DB cluster\.

For example, to get the settings for replication parameters, run the following query:

```
SELECT name, setting FROM pg_settings WHERE name in ('rds.logical_replication', 'max_replication_slots', 'max_wal_senders', 'max_logical_replication_workers', 'max_worker_processes');
```

## Upgrading Aurora PostgreSQL to a new major version<a name="AuroraPostgreSQL.MajorVersionUpgrade.StartLogicalReplication"></a>

**To prepare the publisher \(blue\)**

1. In the example that follows, the source writer instance \(blue\) is an Aurora PostgreSQL DB cluster running PostgreSQL version 11\.15\. This is the publication node in our replication scenario\. For this demonstration, our source writer instance hosts a sample table that holds a series of values:

   ```
   CREATE TABLE my_table (a int PRIMARY KEY);
   INSERT INTO my_table VALUES (generate_series(1,100));
   ```

1. To create a publication on the source instance, connect to the writer node of the instance with psql \(the CLI for PostgreSQL\) or with the client of your choice\)\. Enter the following command in each database:

   ```
   CREATE PUBLICATION publication_name FOR ALL TABLES;
   ```

   The publication\_name specifies the name of the publication\. 

1. You also need to create a replication slot on the instance\. The following command creates a replication slot and loads the `pgoutput` [logical decoding plug\-in](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html)\. The plug\-in changes content read from write\-ahead logging \(WAL\) to the logical replication protocol, and filters the data according to the publication specification\.

   ```
   SELECT pg_create_logical_replication_slot('replication_slot_name', 'pgoutput');
   ```

**To clone the publisher**

1. Use the Amazon RDS Console to create a clone of the source instance\. Highlight the instance name in the Amazon RDS Console, and then choose **Create clone** in the **Actions** menu\.  
![\[In-place upgrade of an Aurora MySQL DB cluster from version 2 to version 3\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/apg-logicalreplication-mvu-create-clone.png)

1. Provide a unique name for the instance\. Most of the settings are defaults from the source instance\. When you’ve made changes required for the new instance, choose **Create clone**\.   
![\[In-place upgrade of an Aurora MySQL DB cluster from version 2 to version 3\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/apg-logicalreplication-mvu-create-clone-note.png)

1. While the target instance is initiating, the **Status** column of the writer node displays Creating in the **Status** column\. When the instance is ready, the status changes to Available\.

**To prepare the clone for an upgrade**

1. The clone is the ‘green’ instance in the deployment model\. It is the host of the replication subscription node\. When the node becomes available, connect with psql and query the new writer node to obtain the log sequence number \(LSN\)\. The LSN identifies the beginning of a record in the WAL stream\.

   ```
   SELECT aurora_volume_logical_start_lsn();
   ```

1. In the response from the query, you find the LSN number\. You need this number later in the process, so make a note of it\.

   ```
   postgres=> SELECT aurora_volume_logical_start_lsn();
   aurora_volume_logical_start_lsn 
   ---------------
   0/402E2F0
   (1 row)
   ```

1. Before upgrading the clone, drop the clone's replication slot\.

   ```
   SELECT pg_drop_replication_slot('replication_slot_name');
   ```

**To upgrade the cluster to a new major version**
+ After cloning the provider node, use the Amazon RDS Console to initiate a major version upgrade on the subscription node\. Highlight the instance name in the RDS console, and select the **Modify** button\. Select the updated version and your updated parameter groups, and apply the settings immediately to upgrade the target instance\.  
![\[In-place upgrade of an Aurora MySQL DB cluster from version 2 to version 3\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/apg-logicalreplication-mvu-modify-DB-cluster.png)
+ You can also use the CLI to perform an upgrade:

  ```
  aws rds modify-db-cluster —db-cluster-identifier $TARGET_Aurora_ID —engine-version 13.6 —allow-major-version-upgrade —apply-immediately
  ```

**To prepare the subscriber \(green\)**

1. When the clone becomes available, connect with psql and define the subscription\. To do so, you need to specify the following options in the `CREATE SUBSCRIPTION` command: 
   + `subscription_name` – The name of the subscription\.
   + `admin_user_name` – The name of an administrative user with rds\_superuser permissions\.
   + `admin_user_password` – The password associated with the administrative user\.
   + `source_instance_URL` – The URL of the publication server instance\.
   + `database` – The database that the subscription server will connect with\.
   + `publication_name` – The name of the publication server\.
   + `replication_slot_name` – The name of the replication slot\.

   ```
   CREATE SUBSCRIPTION subscription_name CONNECTION 'postgres://admin_user_name:admin_user_password@source_instance_URL/database' PUBLICATION publication_name 
   WITH (copy_data = false, create_slot = false, enabled = false, connect = true, slot_name = 'replication_slot_name');
   ```

1.  After creating the subscription, query the [pg\_replication\_origin](https://www.postgresql.org/docs/14/catalog-pg-replication-origin.html) view to retrieve the roname value, which is the identifier of the replication origin\. Each instance has one `roname`: 

   ```
   SELECT * FROM pg_replication_origin;
   ```

   For example:

   ```
   postgres=> 
   SELECT * FROM pg_replication_origin;
    
   roident | roname 
   ---------+----------
   1 | pg_24586
   ```

1. Provide the LSN that you saved from the earlier query of the publication node and the `roname` returned from the subscription node \[INSTANCE\] in the command\. This command uses the `[pg\_replication\_origin\_advance](https://www.postgresql.org/docs/14/functions-admin.html)` function to specify the starting point in the log sequence for replication\.

   ```
   SELECT pg_replication_origin_advance('roname', 'log_sequence_number');
   ```

   `roname` is the identifier returned by the pg\_replication\_origin view\.

   `log_sequence_number` is the value returned by the earlier query of the `aurora_volume_logical_start_lsn` function\. 

1. Then, use the `ALTER SUBSCRIPTION... ENABLE` clause to turn on logical replication\.

   ```
   ALTER SUBSCRIPTION subscription_name ENABLE;
   ```

1. At this point, you can confirm that replication is working\. Add a value to the publication instance, then confirm that the value is replicated to the subscription node\.

   Then, use the following command to monitor replication lag on the publication node:

   ```
   SELECT now() AS CURRENT_TIME, slot_name, active, active_pid, pg_size_pretty(pg_wal_lsn_diff(pg_current_wal_lsn(),
   confirmed_flush_lsn)) AS diff_size, pg_wal_lsn_diff(pg_current_wal_lsn(),
   confirmed_flush_lsn) AS diff_bytes FROM pg_replication_slots WHERE slot_type = 'logical';
   ```

   For example:

   ```
   postgres=> SELECT now() AS CURRENT_TIME, slot_name, active, active_pid, pg_size_pretty(pg_wal_lsn_diff(pg_current_wal_lsn(), 
   confirmed_flush_lsn)) AS diff_size, pg_wal_lsn_diff(pg_current_wal_lsn(), confirmed_flush_lsn) AS diff_bytes FROM pg_replication_slots WHERE slot_type = 'logical';
    
   current_time                   | slot_name             | active | active_pid | diff_size | diff_bytes 
   -------------------------------+-----------------------+--------+------------+-----------+------------
   2022-04-13 15:11:00.243401+00  | replication_slot_name | t      | 21854      | 136 bytes | 136
   (1 row)
   ```

   You can monitor the replication lag using `diff_size` and `diff_bytes` values\. When these values reach 0, the replica has caught up to the source DB instance\. 

## Performing post\-upgrade tasks<a name="AuroraPostgreSQL.MajorVersionUpgrade.PostUpgrade"></a>

When the upgrade is complete, the instance status displays as **Available** in the **Status** column of the console dashboard\. On the new instance, we recommend you do the following:
+ Redirect your applications to point to the writer node\.
+ Add reader nodes to manage the caseload and provide high\-availability in the event of an issue with the writer node\.
+ Aurora PostgreSQL DB clusters occasionally require operating system updates\. These updates might include a newer version of glibc library\. During such updates, we recommend you to follow the guidelines as described in [Collations supported in Aurora PostgreSQL](PostgreSQL-Collations.md)\. 
+ Update user permissions on the new instance to ensure access\.

After testing your application and data on the new instance, we recommend that you make a final backup of your initial instance before removing it\. For more information about using logical replication on an Aurora host, see [Setting up logical replication for your Aurora PostgreSQL DB cluster](AuroraPostgreSQL.Replication.Logical.md#AuroraPostgreSQL.Replication.Logical.Configure)\. 