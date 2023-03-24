# Best practices with Amazon Aurora MySQL<a name="AuroraMySQL.BestPractices"></a><a name="best_practices"></a>

This topic includes information on best practices and options for using or migrating data to an Amazon Aurora MySQL DB cluster\. The information in this topic summarizes and reiterates some of the guidelines and procedures that you can find in [Managing an Amazon Aurora DB cluster](CHAP_Aurora.md)\.

**Contents**
+ [Determining which DB instance you are connected to](#AuroraMySQL.BestPractices.DeterminePrimaryInstanceConnection)
+ [Best practices for Aurora MySQL performance and scaling](#AuroraMySQL.BestPractices.Performance)
  + [Using T instance classes for development and testing](#AuroraMySQL.BestPractices.T2Medium)
  + [Optimizing large Aurora MySQL join queries with hash joins](#Aurora.BestPractices.HashJoin)
    + [Enabling hash joins](#Aurora.BestPractices.HashJoin.Enabling)
    + [Optimizing queries for hash joins](#Aurora.BestPractices.HashJoin.Optimizing)
  + [Using Amazon Aurora to scale reads for your MySQL database](#AuroraMySQL.BestPractices.ReadScaling)
+ [Best practices for Aurora MySQL high availability](#AuroraMySQL.BestPractices.HA)
  + [Using Amazon Aurora for Disaster Recovery with your MySQL databases](#AuroraMySQL.BestPractices.DisasterRecovery)
  + [Migrating from MySQL to Amazon Aurora MySQL with reduced downtime](#AuroraMySQL.BestPractices.Migrating)
  + [Avoiding slow performance, automatic restart, and failover for Aurora MySQL DB instances](#AuroraMySQL.BestPractices.Avoiding)
+ [Recommendations for Aurora MySQL](#AuroraMySQL.BestPractices.FeatureRecommendations)
  + [Using multithreaded replication in Aurora MySQL version 3](#AuroraMySQL.BestPractices.MTReplica)
  + [Invoking AWS Lambda functions using native MySQL functions](#AuroraMySQL.BestPractices.Lambda)
  + [Avoiding XA transactions with Amazon Aurora MySQL](#AuroraMySQL.BestPractices.XA)
  + [Keeping foreign keys turned on during DML statements](#Aurora.BestPractices.ForeignKeys)
  + [Configuring how frequently the log buffer is flushed](#AuroraMySQL.BestPractices.Flush)
  + [Minimizing and troubleshooting Aurora MySQL deadlocks](#AuroraMySQL.BestPractices.deadlocks)
    + [Minimizing InnoDB deadlocks](#AuroraMySQL.BestPractices.deadlocks-minimize)
    + [Monitoring InnoDB deadlocks](#AuroraMySQL.BestPractices.deadlocks-monitor)

## Determining which DB instance you are connected to<a name="AuroraMySQL.BestPractices.DeterminePrimaryInstanceConnection"></a>

To determine which DB instance in an Aurora MySQL DB cluster a connection is connected to, check the `innodb_read_only` global variable, as shown in the following example\.

```
SHOW GLOBAL VARIABLES LIKE 'innodb_read_only'; 
```

The `innodb_read_only` variable is set to `ON` if you are connected to a reader DB instance\. This setting is `OFF` if you are connected to a writer DB instance, such as primary instance in a provisioned cluster\.

This approach can be helpful if you want to add logic to your application code to balance the workload or to ensure that a write operation is using the correct connection\.

## Best practices for Aurora MySQL performance and scaling<a name="AuroraMySQL.BestPractices.Performance"></a>

You can apply the following best practices to improve the performance and scalability of your Aurora MySQL clusters\.

**Topics**
+ [Using T instance classes for development and testing](#AuroraMySQL.BestPractices.T2Medium)
+ [Optimizing large Aurora MySQL join queries with hash joins](#Aurora.BestPractices.HashJoin)
+ [Using Amazon Aurora to scale reads for your MySQL database](#AuroraMySQL.BestPractices.ReadScaling)

### Using T instance classes for development and testing<a name="AuroraMySQL.BestPractices.T2Medium"></a>

Amazon Aurora MySQL instances that use the `db.t2`, `db.t3`, or `db.t4g` DB instance classes are best suited for applications that do not support a high workload for an extended amount of time\. The T instances are designed to provide moderate baseline performance and the capability to burst to significantly higher performance as required by your workload\. They are intended for workloads that don't use the full CPU often or consistently, but occasionally need to burst\. We recommend using the T DB instance classes only for development and test servers, or other non\-production servers\. For more details on the T instance classes, see [Burstable performance instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/burstable-performance-instances.html)\.

If your Aurora cluster is larger than 40 TB, don't use the T instance classes\. When your database has a large volume of data, the memory overhead for managing schema objects can exceed the capacity of a T instance\.

Don't enable the MySQL Performance Schema on Amazon Aurora MySQL T instances\. If the Performance Schema is enabled, the instance might run out of memory\.

**Tip**  
 If your database is sometimes idle but at other times has a substantial workload, you can use Aurora Serverless v2 as an alternative to T instances\. With Aurora Serverless v2, you define a capacity range and Aurora automatically scales your database up or down depending on the current workload\. For usage details, see [Using Aurora Serverless v2](aurora-serverless-v2.md)\. For the database engine versions that you can use with Aurora Serverless v2, see [Requirements for Aurora Serverless v2](aurora-serverless-v2.requirements.md)\. 

When you use a T instance as a DB instance in an Aurora MySQL DB cluster, we recommend the following:
+ Use the same DB instance class for all instances in your DB cluster\. For example, if you use `db.t2.medium` for your writer instance, then we recommend that you use `db.t2.medium` for your reader instances also\.
+ Don't adjust any memory\-related configuration settings, such as `innodb_buffer_pool_size`\. Aurora uses a highly tuned set of default values for memory buffers on T instances\. These special defaults are needed for Aurora to run on memory\-constrained instances\. If you change any memory\-related settings on a T instance, you are much more likely to encounter out\-of\-memory conditions, even if your change is intended to increase buffer sizes\.
+ Monitor your CPU Credit Balance \(`CPUCreditBalance`\) to ensure that it is at a sustainable level\. That is, CPU credits are being accumulated at the same rate as they are being used\.

  When you have exhausted the CPU credits for an instance, you see an immediate drop in the available CPU and an increase in the read and write latency for the instance\. This situation results in a severe decrease in the overall performance of the instance\.

  If your CPU credit balance is not at a sustainable level, then we recommend that you modify your DB instance to use a one of the supported R DB instance classes \(scale compute\)\.

  For more information on monitoring metrics, see [Viewing metrics in the Amazon RDS console](USER_Monitoring.md)\.
+ Monitor the replica lag \(`AuroraReplicaLag`\) between the writer instance and the reader instances\.

  If a reader instance runs out of CPU credits before the writer instance does, the resulting lag can cause the reader instance to restart frequently\. This result is common when an application has a heavy load of read operations distributed among reader instances, at the same time that the writer instance has a minimal load of write operations\.

  If you see a sustained increase in replica lag, make sure that your CPU credit balance for the reader instances in your DB cluster is not being exhausted\.

  If your CPU credit balance is not at a sustainable level, then we recommend that you modify your DB instance to use one of the supported R DB instance classes \(scale compute\)\.
+ Keep the number of inserts per transaction below 1 million for DB clusters that have binary logging enabled\.

  If the DB cluster parameter group for your DB cluster has the `binlog_format` parameter set to a value other than `OFF`, then your DB cluster might experience out\-of\-memory conditions if the DB cluster receives transactions that contain over 1 million rows to insert\. You can monitor the freeable memory \(`FreeableMemory`\) metric to determine if your DB cluster is running out of available memory\. You then check the write operations \(`VolumeWriteIOPS`\) metric to see if a writer instance is receiving a heavy load of write operations\. If this is the case, then we recommend that you update your application to limit the number of inserts in a transaction to less than 1 million\. Alternatively, you can modify your instance to use one of the supported R DB instance classes \(scale compute\)\.

### Optimizing large Aurora MySQL join queries with hash joins<a name="Aurora.BestPractices.HashJoin"></a>

When you need to join a large amount of data by using an equijoin, a hash join can improve query performance\. You can enable hash joins for Aurora MySQL\.

A hash join column can be any complex expression\. In a hash join column, you can compare across data types in the following ways:
+ You can compare anything across the category of precise numeric data types, such as `int`, `bigint`, `numeric`, and `bit`\.
+ You can compare anything across the category of approximate numeric data types, such as `float` and `double`\.
+ You can compare items across string types if the string types have the same character set and collation\.
+ You can compare items with date and timestamp data types if the types are the same\.

**Note**  
You can't compare data types in different categories\.

The following restrictions apply to hash joins for Aurora MySQL:
+ Left\-right outer joins aren't supported for Aurora MySQL version 2, but are supported for version 3\.
+ Semijoins such as subqueries aren't supported, unless the subqueries are materialized first\.
+ Multiple\-table updates or deletes aren't supported\.
**Note**  
Single\-table updates or deletes are supported\.
+ BLOB and spatial data type columns can't be join columns in a hash join\.

#### Enabling hash joins<a name="Aurora.BestPractices.HashJoin.Enabling"></a>

To enable hash joins:
+ Aurora MySQL version 2 – Set the DB parameter or DB cluster parameter `aurora_disable_hash_join` to `0`\. Turning off `aurora_disable_hash_join` sets the value of `optimizer_switch` to `hash_join=on`\.
+ Aurora MySQL version 3 – Set the MySQL server parameter `optimizer_switch` to `block_nested_loop=on`\.

Hash joins are turned on by default in Aurora MySQL version 3 and turned off by default in Aurora MySQL version 2\. The following example illustrates how to enable hash joins for Aurora MySQL version 3\. You can issue the statement `select @@optimizer_switch` first to see what other settings are present in the `SET` parameter string\. Updating one setting in the `optimizer_switch` parameter doesn't erase or modify the other settings\.

```
mysql> SET optimizer_switch='block_nested_loop=on';
```

**Note**  
For Aurora MySQL version 3, hash join support is available in all minor versions and is turned on by default\.  
For Aurora MySQL version 2, hash join support is available in all minor versions\. In Aurora MySQL version 2, the hash join feature is always controlled by the `aurora_disable_hash_join` value\.

With this setting, the optimizer chooses to use a hash join based on cost, query characteristics, and resource availability\. If the cost estimation is incorrect, you can force the optimizer to choose a hash join\. You do so by setting `hash_join_cost_based`, a MySQL server variable, to `off`\. The following example illustrates how to force the optimizer to choose a hash join\.

```
mysql> SET optimizer_switch='hash_join_cost_based=off';
```

**Note**  
This setting overrides the decisions of the cost\-based optimizer\. While the setting can be useful for testing and development, we recommend that you not use it in production\.

#### Optimizing queries for hash joins<a name="Aurora.BestPractices.HashJoin.Optimizing"></a>

To find out whether a query can take advantage of a hash join, use the `EXPLAIN` statement to profile the query first\. The `EXPLAIN` statement provides information about the execution plan to use for a specified query\.

In the output for the `EXPLAIN` statement, the `Extra` column describes additional information included with the execution plan\. If a hash join applies to the tables used in the query, this column includes values similar to the following:
+ `Using where; Using join buffer (Hash Join Outer table table1_name)`
+ `Using where; Using join buffer (Hash Join Inner table table2_name)`

The following example shows the use of EXPLAIN to view the execution plan for a hash join query\.

```
mysql> explain SELECT sql_no_cache * FROM hj_small, hj_big, hj_big2
    ->     WHERE hj_small.col1 = hj_big.col1 and hj_big.col1=hj_big2.col1 ORDER BY 1;
+----+-------------+----------+------+---------------+------+---------+------+------+----------------------------------------------------------------+
| id | select_type | table    | type | possible_keys | key  | key_len | ref  | rows | Extra                                                          |
+----+-------------+----------+------+---------------+------+---------+------+------+----------------------------------------------------------------+
|  1 | SIMPLE      | hj_small | ALL  | NULL          | NULL | NULL    | NULL |    6 | Using temporary; Using filesort                                |
|  1 | SIMPLE      | hj_big   | ALL  | NULL          | NULL | NULL    | NULL |   10 | Using where; Using join buffer (Hash Join Outer table hj_big)  |
|  1 | SIMPLE      | hj_big2  | ALL  | NULL          | NULL | NULL    | NULL |   15 | Using where; Using join buffer (Hash Join Inner table hj_big2) |
+----+-------------+----------+------+---------------+------+---------+------+------+----------------------------------------------------------------+
3 rows in set (0.04 sec)
```

In the output, the `Hash Join Inner table` is the table used to build hash table, and the `Hash Join Outer table` is the table that is used to probe the hash table\.

For more information about the extended `EXPLAIN` output format, see [Extended EXPLAIN Output Format](https://dev.mysql.com/doc/refman/8.0/en/explain-extended.html) in the MySQL product documentation\.

 In Aurora MySQL 2\.08 and higher, you can use SQL hints to influence whether a query uses hash join or not, and which tables to use for the build and probe sides of the join\. For details, see [Aurora MySQL hints](AuroraMySQL.Reference.Hints.md)\. 

### Using Amazon Aurora to scale reads for your MySQL database<a name="AuroraMySQL.BestPractices.ReadScaling"></a>

You can use Amazon Aurora with your MySQL DB instance to take advantage of the read scaling capabilities of Amazon Aurora and expand the read workload for your MySQL DB instance\. To use Aurora to read scale your MySQL DB instance, create an Aurora MySQL DB cluster and make it a read replica of your MySQL DB instance\. Then connect to the Aurora MySQL cluster to process the read queries\. The source database can be an RDS for MySQL DB instance, or a MySQL database running external to Amazon RDS\. For more information, see [Using Amazon Aurora to scale reads for your MySQL database](AuroraMySQL.Replication.MySQL.md#AuroraMySQL.Replication.ReadScaling)\.

## Best practices for Aurora MySQL high availability<a name="AuroraMySQL.BestPractices.HA"></a>

You can apply the following best practices to improve the availability of your Aurora MySQL clusters\.

**Topics**
+ [Using Amazon Aurora for Disaster Recovery with your MySQL databases](#AuroraMySQL.BestPractices.DisasterRecovery)
+ [Migrating from MySQL to Amazon Aurora MySQL with reduced downtime](#AuroraMySQL.BestPractices.Migrating)
+ [Avoiding slow performance, automatic restart, and failover for Aurora MySQL DB instances](#AuroraMySQL.BestPractices.Avoiding)

### Using Amazon Aurora for Disaster Recovery with your MySQL databases<a name="AuroraMySQL.BestPractices.DisasterRecovery"></a>

You can use Amazon Aurora with your MySQL DB instance to create an offsite backup for disaster recovery\. To use Aurora for disaster recovery of your MySQL DB instance, create an Amazon Aurora DB cluster and make it a read replica of your MySQL DB instance\. This applies to an RDS for MySQL DB instance, or a MySQL database running external to Amazon RDS\.

**Important**  
When you set up replication between a MySQL DB instance and an Amazon Aurora MySQL DB cluster, you should monitor the replication to ensure that it remains healthy and repair it if necessary\.

For instructions on how to create an Amazon Aurora MySQL DB cluster and make it a read replica of your MySQL DB instance, follow the procedure in [Using Amazon Aurora to scale reads for your MySQL database](#AuroraMySQL.BestPractices.ReadScaling)\.

For more information on disaster recovery models, see [How to choose the best disaster recovery option for your Amazon Aurora MySQL cluster](http://aws.amazon.com/blogs/database/how-to-choose-the-best-disaster-recovery-option-for-your-amazon-aurora-mysql-cluster/)\.

### Migrating from MySQL to Amazon Aurora MySQL with reduced downtime<a name="AuroraMySQL.BestPractices.Migrating"></a>

When importing data from a MySQL database that supports a live application to an Amazon Aurora MySQL DB cluster, you might want to reduce the time that service is interrupted while you migrate\. To do so, you can use the procedure documented in [Importing data to a MySQL or MariaDB DB instance with reduced downtime](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/MySQL.Procedural.Importing.NonRDSRepl.html) in the *Amazon Relational Database Service User Guide*\. This procedure can especially help if you are working with a very large database\. You can use the procedure to reduce the cost of the import by minimizing the amount of data that is passed across the network to AWS\.

The procedure lists steps to transfer a copy of your database data to an Amazon EC2 instance and import the data into a new RDS for MySQL DB instance\. Because Amazon Aurora is compatible with MySQL, you can instead use an Amazon Aurora DB cluster for the target Amazon RDS MySQL DB instance\.

### Avoiding slow performance, automatic restart, and failover for Aurora MySQL DB instances<a name="AuroraMySQL.BestPractices.Avoiding"></a>

If you're running a heavy workload or workloads that spike beyond the allocated resources of your DB instance, you can exhaust the resources on which you're running your application and Aurora database\. To get metrics on your database instance such as CPU utilization, memory usage, and number of database connections used, you can refer to the metrics provided by Amazon CloudWatch, Performance Insights, and Enhanced Monitoring\. For more information on monitoring your DB instance, see [Monitoring metrics in an Amazon Aurora cluster](MonitoringAurora.md)\.

If your workload exhausts the resources you're using, your DB instance might slow down, restart, or even fail over to another DB instance\. To avoid this, monitor your resource utilization, examine the workload running on your DB instance, and make optimizations where necessary\. If optimizations don't improve the instance metrics and mitigate the resource exhaustion, consider scaling up your DB instance before you reach its limits\. For more information on available DB instance classes and their specifications, see [Aurora DB instance classes](Concepts.DBInstanceClass.md)\.

## Recommendations for Aurora MySQL<a name="AuroraMySQL.BestPractices.FeatureRecommendations"></a>

The following features are available in Aurora MySQL for MySQL compatibility\. However, they have performance, scalability, stability, or compatibility issues in the Aurora environment\. Thus, we recommend that you follow certain guidelines in your use of these features\. For example, we recommend that you don't use certain features for production Aurora deployments\.

**Topics**
+ [Using multithreaded replication in Aurora MySQL version 3](#AuroraMySQL.BestPractices.MTReplica)
+ [Invoking AWS Lambda functions using native MySQL functions](#AuroraMySQL.BestPractices.Lambda)
+ [Avoiding XA transactions with Amazon Aurora MySQL](#AuroraMySQL.BestPractices.XA)
+ [Keeping foreign keys turned on during DML statements](#Aurora.BestPractices.ForeignKeys)
+ [Configuring how frequently the log buffer is flushed](#AuroraMySQL.BestPractices.Flush)
+ [Minimizing and troubleshooting Aurora MySQL deadlocks](#AuroraMySQL.BestPractices.deadlocks)

### Using multithreaded replication in Aurora MySQL version 3<a name="AuroraMySQL.BestPractices.MTReplica"></a>

By default, Aurora uses single\-threaded replication when an Aurora MySQL DB cluster is used as a read replica for binary log replication\.

Although Aurora MySQL doesn't prohibit multithreaded replication, this feature is only supported in Aurora MySQL version 3\.

Aurora MySQL version 2 inherited several issues regarding multithreaded replication from MySQL\. For that version, we recommend that you not use multithreaded replication in production\.

If you do use multithreaded replication, we recommend that you test any use thoroughly\.

For more information about using replication in Amazon Aurora, see [Replication with Amazon Aurora](Aurora.Replication.md)\. For information about multithreaded replication in Aurora MySQL version 3, see [Multithreaded binary log replication \(Aurora MySQL version 3 and higher\)](AuroraMySQL.Replication.MySQL.md#binlog-optimization-multithreading)\. 

### Invoking AWS Lambda functions using native MySQL functions<a name="AuroraMySQL.BestPractices.Lambda"></a>

We recommend using the native MySQL functions `lambda_sync` and `lambda_async` to invoke Lambda functions\.

If you are using the deprecated `mysql.lambda_async` procedure, we recommend that you wrap calls to the `mysql.lambda_async` procedure in a stored procedure\. You can call this stored procedure from different sources, such as triggers or client code\. This approach can help to avoid impedance mismatch issues and make it easier for your database programmers to invoke Lambda functions\.

For more information on invoking Lambda functions from Amazon Aurora, see [Invoking a Lambda function from an Amazon Aurora MySQL DB cluster](AuroraMySQL.Integrating.Lambda.md)\.

### Avoiding XA transactions with Amazon Aurora MySQL<a name="AuroraMySQL.BestPractices.XA"></a>

We recommend that you don't use eXtended Architecture \(XA\) transactions with Aurora MySQL, because they can cause long recovery times if the XA was in the `PREPARED` state\. If you must use XA transactions with Aurora MySQL, follow these best practices:
+ Don't leave an XA transaction open in the `PREPARED` state\.
+ Keep XA transactions as small as possible\.

For more information about using XA transactions with MySQL, see [XA transactions](https://dev.mysql.com/doc/refman/8.0/en/xa.html) in the MySQL documentation\.

### Keeping foreign keys turned on during DML statements<a name="Aurora.BestPractices.ForeignKeys"></a>

We strongly recommend that you don't run any data definition language \(DDL\) statements when the `foreign_key_checks` variable is set to `0` \(off\)\.

If you need to insert or update rows that require a transient violation of foreign keys, follow these steps:

1. Set `foreign_key_checks` to `0`\.

1. Make your data manipulation language \(DML\) changes\.

1. Make sure that your completed changes don't violate any foreign key constraints\.

1. Set `foreign_key_checks` to `1` \(on\)\.

In addition, follow these other best practices for foreign key constraints:
+ Make sure that your client applications don't set the `foreign_key_checks` variable to `0` as a part of the `init_connect` variable\.
+ If a restore from a logical backup such as `mysqldump` fails or is incomplete, make sure that `foreign_key_checks` is set to `1` before starting any other operations in the same session\. A logical backup sets `foreign_key_checks` to `0` when it starts\.

### Configuring how frequently the log buffer is flushed<a name="AuroraMySQL.BestPractices.Flush"></a>

In MySQL Community Edition, to make transactions durable, the InnoDB log buffer must be flushed to durable storage\. You use the`innodb_flush_log_at_trx_commit` parameter to configure how frequently the log buffer is flushed to disk\.

When you set the `innodb_flush_log_at_trx_commit` parameter to the default value of 1, the log buffer is flushed at each transaction commit\. This setting helps to keep the database [ACID](https://dev.mysql.com/doc/refman/5.7/en/glossary.html#glos_acid) compliant\. We recommend that you keep the default setting of 1\.

Changing `innodb_flush_log_at_trx_commit` to a nondefault value, 0 or 2, can help reduce data manipulation language \(DML\) latency, but sacrifices the durability of the log records\. This lack of durability makes the database ACID noncompliant\. We recommend that your databases be ACID compliant to avoid the risk of data loss in the event of a server restart\. For more information on this parameter, see [innodb\_flush\_log\_at\_trx\_commit](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_flush_log_at_trx_commit) in the MySQL documentation\.

In Aurora MySQL, redo log processing is offloaded to the storage layer, so no flushing to log files occurs on the DB instance\. When a write is issued, redo logs are sent from the writer DB instance directly to the Aurora cluster volume\. The only writes that cross the network are redo log records\. No pages are ever written from the database tier\.

By default, in Aurora MySQL, the `innodb_flush_log_at_trx_commit` parameter is set to 1\. Each thread committing a transaction waits for confirmation from the Aurora cluster volume\. This confirmation indicates that this record and all previous redo log records are written and have achieved [quorum](https://aws.amazon.com/blogs/database/amazon-aurora-under-the-hood-quorum-and-correlated-failure/)\. Persisting the log records and achieving quorum make the transaction durable, whether through autocommit or explicit commit\. For more information on the Aurora storage architecture, see [Amazon Aurora storage demystified](https://d1.awsstatic.com/events/reinvent/2020/Amazon_Aurora_storage_demystified_DAT401.pdf)\.

Aurora MySQL doesn't flush logs to data files as MySQL Community Edition does\. However, you can use the `innodb_flush_log_at_trx_commit` parameter to relax durability constraints when writing redo log records to the Aurora cluster volume\. When you set `innodb_flush_log_at_trx_commit` to a nondefault value \(0 or 2\), the database doesn't wait for confirmation that the redo log records are written to the Aurora cluster volume\. While these settings can lower DML latency to the client, they can also result in data loss in the event of a failover or restart\. Therefore, we recommend that you keep the `innodb_flush_log_at_trx_commit` parameter set to the default value of 1\.

While data loss can occur in both MySQL Community Edition and Aurora MySQL, behavior differs in each database because of their different architectures\. These architectural differences can lead to varying degrees of data loss\. To make sure that your database is ACID compliant, always set `innodb_flush_log_at_trx_commit` to 1\.

**Note**  
You can't configure the `innodb_flush_log_at_trx_commit` parameter in Aurora MySQL version 3\. Aurora MySQL version 3 always uses the default setting of 1, which is ACID compliant\.

### Minimizing and troubleshooting Aurora MySQL deadlocks<a name="AuroraMySQL.BestPractices.deadlocks"></a>

Users running workloads that regularly experience constraint violations on unique secondary indexes or foreign keys, when modifying records on the same data page concurrently, might experience increased deadlocks and lock wait timeouts\. These deadlocks and timeouts are because of a MySQL Community Edition [bug fix](https://bugs.mysql.com/bug.php?id=98324)\.

This fix is included in MySQL Community Edition versions 5\.7\.26 and higher, and was backported into Aurora MySQL versions 2\.10\.3 and higher\. The fix is necessary for enforcing *serializability*, by implementing additional locking for these types of data manipulation language \(DML\) operations, on changes made to records in an InnoDB table\. This issue was uncovered as part of an investigation into deadlock issues introduced by a previous MySQL Community Edition [bug fix](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-26.html)\.

The fix changed the internal handling for the *partial rollback* of a tuple \(row\) update in the InnoDB storage engine\. Operations that generate constraint violations on foreign keys or unique secondary indexes cause partial rollback\. This includes, but isn't limited to, concurrent `INSERT...ON DUPLICATE KEY UPDATE`, `REPLACE INTO,` and `INSERT IGNORE` statements \(*upserts*\)\.

In this context, partial rollback doesn't refer to the rollback of application\-level transactions, but rather an internal InnoDB rollback of changes to a clustered index, when a constraint violation is encountered\. For example, a duplicate key value is found during an upsert operation\.

In a normal insert operation, InnoDB atomically creates [clustered](https://dev.mysql.com/doc/refman/5.7/en/innodb-index-types.html) and secondary index entries for each index\. If InnoDB detects a duplicate value on a unique secondary index during an upsert operation, the inserted entry in the clustered index has to be reverted \(partial rollback\), and the update then has to be applied to the existing duplicate row\. During this internal partial rollback step, InnoDB must lock each record seen as part of the operation\. The fix ensures transaction serializability by introducing additional locking after the partial rollback\.

#### Minimizing InnoDB deadlocks<a name="AuroraMySQL.BestPractices.deadlocks-minimize"></a>

You can take the following approaches to reduce the frequency of deadlocks in your database instance\. More examples can be found in the [MySQL documentation](https://bugs.mysql.com/bug.php?id=98324)\.

1. To reduce the chances of deadlocks, commit transactions immediately after making a related set of changes\. You can do this by breaking up large transactions \(multiple row updates between commits\) into smaller ones\. If you're batch inserting rows, then try to reduce batch insert sizes, especially when using the upsert operations mentioned previously\.

   To reduce the number of possible partial rollbacks, you can try some of the following approaches:

   1. Replace batch insert operations with inserting one row at a time\. This can reduce the amount of time where locks are held by transactions that might have conflicts\.

   1. Instead of using `REPLACE INTO`, rewrite the SQL statement as a multistatement transaction such as the following:

      ```
      BEGIN;
      DELETE conflicting rows;
      INSERT new rows;
      COMMIT;
      ```

   1. Instead of using `INSERT...ON DUPLICATE KEY UPDATE`, rewrite the SQL statement as a multistatement transaction such as the following:

      ```
      BEGIN;
      SELECT rows that conflict on secondary indexes;
      UPDATE conflicting rows;
      INSERT new rows;
      COMMIT;
      ```

1. Avoid long\-running transactions, active or idle, that might hold onto locks\. This includes interactive MySQL client sessions that might be open for an extended period with an uncommitted transaction\. When optimizing transaction sizes or batch sizes, the impact can vary depending on a number of factors such as concurrency, number of duplicates, and table structure\. Any changes should be implemented and tested based on your workload\.

1. In some situations, deadlocks can occur when two transactions attempt to access the same datasets, either in one or multiple tables, in different orders\. To prevent this, you can modify the transactions to access the data in the same order, thereby serializing the access\. For example, create a queue of transactions to be completed\. This approach can help to avoid deadlocks when multiple transactions occur concurrently\.

1. Adding carefully chosen indexes to your tables can improve selectivity and reduce the need to access rows, which leads to less locking\.

1. If you encounter [gap locking](https://dev.mysql.com/doc/refman/5.7/en/innodb-locking.html#innodb-gap-locks), you can modify the transaction isolation level to `READ COMMITTED` for the session or transaction to prevent it\. For more information on InnoDB isolation levels and their behaviors, see [Transaction isolation levels](https://dev.mysql.com/doc/refman/5.7/en/innodb-transaction-isolation-levels.html) in the MySQL documentation\.

**Note**  
While you can take precautions to reduce the possibility of deadlocks occurring, deadlocks are an expected database behavior and can still occur\. Applications should have the necessary logic to handle deadlocks when they are encountered\. For example, implement retry and backing\-off logic in the application\. It’s best to address the root cause of the issue but if a deadlock does occur, the application has the option to wait and retry\.

#### Monitoring InnoDB deadlocks<a name="AuroraMySQL.BestPractices.deadlocks-monitor"></a>

[Deadlocks](https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos_deadlock) can occur in MySQL when application transactions try to take table\-level and row\-level locks in a way that results in circular waiting\. An occasional InnoDB deadlock isn't necessarily an issue, because the InnoDB storage engine detects the condition immediately and rolls back one of the transactions automatically\. If you encounter deadlocks frequently, we recommend reviewing and modifying your application to alleviate performance issues and avoid deadlocks\. When [deadlock detection](https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos_deadlock_detection) is turned on \(the default\), InnoDB automatically detects transaction deadlocks and rolls back a transaction or transactions to break the deadlock\. InnoDB tries to pick small transactions to roll back, where the size of a transaction is determined by the number of rows inserted, updated, or deleted\.
+ `SHOW ENGINE` statement – The `SHOW ENGINE INNODB STATUS \G` statement contains [details](https://dev.mysql.com/doc/refman/5.7/en/show-engine.html) of the most recent deadlock encountered on the database since the last restart\.
+ MySQL error log – If you encounter frequent deadlocks where the output of the `SHOW ENGINE` statement is inadequate, you can turn on the [innodb\_print\_all\_deadlocks](https://dev.mysql.com/doc/refman/8.0/en/innodb-parameters.html#sysvar_innodb_print_all_deadlocks) DB cluster parameter\.

  When this parameter is turned on, information about all deadlocks in InnoDB user transactions is recorded in the Aurora MySQL [error log](https://dev.mysql.com/doc/refman/8.0/en/error-log.html)\.
+ Amazon CloudWatch metrics – We also recommend that you proactively monitor deadlocks using the CloudWatch metric `Deadlocks`\. For more information, see [Instance\-level metrics for Amazon Aurora](Aurora.AuroraMonitoring.Metrics.md#Aurora.AuroraMySQL.Monitoring.Metrics.instances)\.
+ Amazon CloudWatch Logs – With CloudWatch Logs, you can view metrics, analyze log data, and create real\-time alarms\. For more information, see [Monitor errors in Amazon Aurora MySQL and Amazon RDS for MySQL using Amazon CloudWatch and send notifications using Amazon SNS](https://aws.amazon.com/blogs/database/monitor-errors-in-amazon-aurora-mysql-and-amazon-rds-for-mysql-using-amazon-cloudwatch-and-send-notifications-using-amazon-sns/)\.

  Using CloudWatch Logs with `innodb_print_all_deadlocks` turned on, you can configure alarms to notify you when the number of deadlocks exceeds a given threshold\. To define a threshold, we recommend that you observe your trends and use a value based on your normal workload\.
+ Performance Insights – When you use Performance Insights, you can monitor the `innodb_deadlocks` and `innodb_lock_wait_timeout` metrics\. For more information on these metrics, see [Non\-native counters for Aurora MySQL](USER_PerfInsights_Counters.md#USER_PerfInsights_Counters.Aurora_MySQL.NonNative)\.