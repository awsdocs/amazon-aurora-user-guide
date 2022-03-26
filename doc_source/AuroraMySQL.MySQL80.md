# Aurora MySQL version 3 compatible with MySQL 8\.0<a name="AuroraMySQL.MySQL80"></a>

 You can use Aurora MySQL version 3 to get the latest MySQL\-compatible features, performance enhancements, and bug fixes\. Following, you can learn about Aurora MySQL version 3, with MySQL 8\.0 compatibility\. You can learn how to upgrade your clusters and applications to Aurora MySQL version 3\. 

**Topics**
+ [Features from community MySQL 8\.0](#AuroraMySQL.8.0-features-community)
+ [New parallel query optimizations](#AuroraMySQL.8.0-features-pq)
+ [Release notes for Aurora MySQL version 3](#AuroraMySQL.mysql80-bugs-fixed)
+ [Comparison of Aurora MySQL version 2 and Aurora MySQL version 3](#Aurora.AuroraMySQL.Compare-v2-v3)
+ [Comparison of Aurora MySQL version 3 and community MySQL 8\.0](#Aurora.AuroraMySQL.Compare-80-v3)
+ [Upgrading to Aurora MySQL version 3](#AuroraMySQL.mysql80-upgrade-procedure)

## Features from community MySQL 8\.0<a name="AuroraMySQL.8.0-features-community"></a>

 The initial release of Aurora MySQL version 3 is compatible with community MySQL 8\.0\.23\. MySQL 8\.0 introduces several new features, including the following: 
+  JSON functions\. For usage information, see [JSON Functions](https://dev.mysql.com/doc/refman/8.0/en/json-functions.html) in the *MySQL Reference Manual*\. 
+  Window functions\. For usage information, see [Window Functions](https://dev.mysql.com/doc/refman/8.0/en/window-functions.html) in the *MySQL Reference Manual*\. 
+  Common table expressions \(CTEs\), using the `WITH` clause\. For usage information, see [WITH \(Common Table Expressions\)](https://dev.mysql.com/doc/refman/8.0/en/with.html) in the *MySQL Reference Manual*\. 
+  Optimized `ADD COLUMN` and `RENAME COLUMN` clauses for the `ALTER TABLE` statement\. These optimizations are called "instant DDL\." Aurora MySQL version 3 is compatible with the community MySQL instant DDL feature\. The former Aurora fast DDL feature isn't used\. For usage information for instant DDL, see [Instant DDL \(Aurora MySQL version 3\)](AuroraMySQL.Managing.FastDDL.md#AuroraMySQL.mysql80-instant-ddl)\. 
+  Descending, functional, and invisible indexes\. For usage information, see [Invisible Indexes](https://dev.mysql.com/doc/refman/8.0/en/invisible-indexes.html), [Descending Indexes](https://dev.mysql.com/doc/refman/8.0/en/descending-indexes.html), and [CREATE INDEX Statement](https://dev.mysql.com/doc/refman/8.0/en/create-index.html#create-index-functional-key-parts) in the *MySQL Reference Manual*\. 
+  Role\-based privileges controlled through SQL statements\. For more information on changes to the privilege model, see [Role\-based privilege model](#AuroraMySQL.privilege-model)\. 
+  `NOWAIT` and `SKIP LOCKED` clauses with the `SELECT ... FOR SHARE` statement\. These clauses avoid waiting for other transactions to release row locks\. For usage information, see [Locking Reads](https://dev.mysql.com/doc/refman/8.0/en/innodb-locking-reads.html) in the *MySQL Reference Manual*\. 
+  Improvements to binary log \(binlog\) replication\. For the Aurora MySQL details, see [Binary log replication](#AuroraMySQL.mysql80-binlog)\. In particular, you can perform filtered replication\. For usage information about filtered replication, see [How Servers Evaluate Replication Filtering Rules](https://dev.mysql.com/doc/refman/8.0/en/replication-rules.html) in the *MySQL Reference Manual*\. 
+  Hints\. Some of the MySQL 8\.0–compatible hints were already backported to Aurora MySQL version 2\. For information about using hints with Aurora MySQL, see [Aurora MySQL hints](AuroraMySQL.Reference.md#AuroraMySQL.Reference.Hints)\. For the full list of hints in community MySQL 8\.0, see [Optimizer Hints](https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html) in the *MySQL Reference Manual*\. 

 For the full list of features added to MySQL 8\.0 community edition, see the blog post [The complete list of new features in MySQL 8\.0](https://dev.mysql.com/blog-archive/the-complete-list-of-new-features-in-mysql-8-0/)\. 

 Aurora MySQL version 3 also includes changes to keywords for inclusive language, backported from community MySQL 8\.0\.26\. For details about those changes, see [Inclusive language changes for Aurora MySQL version 3](#AuroraMySQL.8.0-inclusive-language)\. 

## New parallel query optimizations<a name="AuroraMySQL.8.0-features-pq"></a>

 The Aurora parallel query optimization now applies to more SQL operations: 
+  Parallel query now applies to tables containing the data types `TEXT`, `BLOB`, `JSON`, `GEOMETRY`, and `VARCHAR` and `CHAR` longer than 768 bytes\. 
+  Parallel query can optimize queries involving partitioned tables\. 
+  Parallel query can optimize queries involving aggregate function calls in the select list and the `HAVING` clause\. 

 For more information about these enhancements, see [Upgrading parallel query clusters to Aurora MySQL version 3](aurora-mysql-parallel-query.md#aurora-mysql-parallel-query-upgrade-pqv2)\. For general information about Aurora parallel query, see [Working with parallel query for Amazon Aurora MySQL](aurora-mysql-parallel-query.md)\. 

## Release notes for Aurora MySQL version 3<a name="AuroraMySQL.mysql80-bugs-fixed"></a>

 For the release notes for all Aurora MySQL version 3 releases, see [ Database engine updates for Amazon Aurora MySQL version 3](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraMySQLReleaseNotes/AuroraMySQL.Updates.30Updates.html) in the *Release Notes for Aurora MySQL*\. 

## Comparison of Aurora MySQL version 2 and Aurora MySQL version 3<a name="Aurora.AuroraMySQL.Compare-v2-v3"></a>

 Use the following to learn about changes to be aware of when you upgrade your Aurora MySQL version 2 cluster to version 3\. 

**Topics**
+ [Feature differences between Aurora MySQL version 2 and 3](#AuroraMySQL.Compare-v2-v3-features)
+ [Instance class support](#AuroraMySQL.mysql80-instance-classes)
+ [Parameter changes for Aurora MySQL version 3](#AuroraMySQL.mysql80-parameter-changes)
+ [Status variables](#AuroraMySQL.mysql80-status-vars)
+ [Inclusive language changes for Aurora MySQL version 3](#AuroraMySQL.8.0-inclusive-language)
+ [AUTO\_INCREMENT values](#AuroraMySQL.mysql80-autoincrement)
+ [Temporary tables on reader DB instances](#AuroraMySQL.mysql80-temp-tables-readers)
+ [Storage engine for internal temporary tables](#AuroraMySQL.mysql80-internal-temp-tables-engine)
+ [Binary log replication](#AuroraMySQL.mysql80-binlog)

### Feature differences between Aurora MySQL version 2 and 3<a name="AuroraMySQL.Compare-v2-v3-features"></a>

 The following Amazon Aurora MySQL features are supported in Aurora MySQL for MySQL 5\.7, but these features aren't supported in Aurora MySQL for MySQL 8\.0: 
+  Backtrack currently isn't available for Aurora MySQL version 3 clusters\. We intend to make this feature available in a subsequent minor version\. 

   If you have an Aurora MySQL version 2 cluster that uses backtrack, currently you can't use the snapshot restore method to upgrade to Aurora MySQL version 3\. This limitation applies to all clusters that use backtrack clusters, regardless of whether the backtrack setting is turned on\. For details about upgrade procedures, see [Upgrading to Aurora MySQL version 3](#AuroraMySQL.mysql80-upgrade-procedure)\. 
+  You can't use Aurora MySQL version 3 for Aurora Serverless v1 clusters\. Aurora MySQL version 3 works with Aurora Serverless v2, which is currently in preview\. 
+  Lab mode doesn't apply to Aurora MySQL version 3\. There aren't any lab mode features in Aurora MySQL version 3\. Instant DDL supersedes the fast online DDL feature that was formerly available in lab mode\. For an example, see [Instant DDL \(Aurora MySQL version 3\)](AuroraMySQL.Managing.FastDDL.md#AuroraMySQL.mysql80-instant-ddl)\. 
+  The query cache is removed from community MySQL 8\.0 and also from Aurora MySQL version 3\. 
+  Aurora MySQL version 3 is compatible with the community MySQL hash join feature\. The Aurora\-specific implementation of hash joins in Aurora MySQL version 2 isn't used\. For information about using hash joins with Aurora parallel query, see [Turning on hash join for parallel query clusters](aurora-mysql-parallel-query.md#aurora-mysql-parallel-query-enabling-hash-join) and [Aurora MySQL hints](AuroraMySQL.Reference.md#AuroraMySQL.Reference.Hints)\. For general usage information about hash joins, see [Hash Join Optimization](https://dev.mysql.com/doc/refman/8.0/en/hash-joins.html) in the *MySQL Reference Manual*\. 
+  Currently, you can't restore a physical backup from the Percona XtraBackup tool to an Aurora MySQL version 3 cluster\. We intend to support this feature in a subsequent minor version\. 
+  The `mysql.lambda_async` stored procedure that was deprecated in Aurora MySQL version 2 is removed in version 3\. For version 3, use the asynchronous function `lambda_async` instead\. 
+  The default character set in Aurora MySQL version 3 is `utf8mb4`\. In Aurora MySQL version 2, the default character set was `latin1`\. For information about this character set, see [The utf8mb4 Character Set \(4\-Byte UTF\-8 Unicode Encoding\)](https://dev.mysql.com/doc/refman/8.0/en/charset-unicode-utf8mb4.html) in the *MySQL Reference Manual*\. 
+  The `innodb_flush_log_at_trx_commit` configuration parameter can't be modified\. The default value of 1 always applies\. 

 Some Aurora MySQL features are available for certain combinations of AWS Region and DB engine version\. For details, see [Supported features in Amazon Aurora by AWS Region and Aurora DB engine](Concepts.AuroraFeaturesRegionsDBEngines.grids.md)\. 

### Instance class support<a name="AuroraMySQL.mysql80-instance-classes"></a>

 Aurora MySQL version 3 supports a different set of instance classes than Aurora MySQL version 2 does: 
+  For larger instances, you can use the modern instance classes such as `db.r5`, `db.r6g`, and `db.x2g`\. 
+  For smaller instances, you can use the modern instance classes such as `db.t3` and `db.t4g`\. 

 The following instance classes from Aurora MySQL version 2 aren't available for Aurora MySQL version 3: 
+  `db.r4` 
+  `db.r3` 
+  `db.t3.small` 
+  `db.t2` 

 Check your administration scripts for any CLI statements that create Aurora MySQL DB instances and hardcode instance class names that aren't available for Aurora MySQL version 3\. If necessary, modify the instance class names to ones that Aurora MySQL version 3 supports\. 

**Tip**  
 To check the instance classes that you can use for a specific combination of Aurora MySQL version and AWS Region, use the `describe-orderable-db-instance-options` AWS CLI command\. 

 For full details about Aurora instance classes, see [Aurora DB instance classes](Concepts.DBInstanceClass.md)\. 

### Parameter changes for Aurora MySQL version 3<a name="AuroraMySQL.mysql80-parameter-changes"></a>

 Aurora MySQL version 3 includes new cluster\-level and instance\-level configuration parameters\. Aurora MySQL version 3 also removes some parameters that were present in Aurora MySQL version 2\. Some parameter names are changed as a result of the initiative for inclusive language\. For backward compatibility, you can still retrieve the parameter values using either the old names or the new names\. However, you must use the new names to specify parameter values in a custom parameter group\. 

 In Aurora MySQL version 3, the value of the `lower_case_table_names` parameter is set permanently at the time the cluster is created\. If you use a nondefault value for this option, set up your Aurora MySQL version 3 custom parameter group before upgrading\. Then specify the parameter group during the create cluster or snapshot restore operation\. 

 For the full list of Aurora MySQL cluster parameters, see [Cluster\-level parameters](AuroraMySQL.Reference.md#AuroraMySQL.Reference.Parameters.Cluster)\. The table covers all the parameters from Aurora MySQL version 1, 2, and 3\. The table includes notes showing which parameters are new in Aurora MySQL version 3 or were removed from Aurora MySQL version 3\. 

 For the full list of Aurora MySQL instance parameters, see [Instance\-level parameters](AuroraMySQL.Reference.md#AuroraMySQL.Reference.Parameters.Instance)\. The table covers all the parameters from Aurora MySQL version 1, 2, and 3\. The table includes notes showing which parameters are new in Aurora MySQL version 3 and which parameters were removed from Aurora MySQL version 3\. It also includes notes showing which parameters were modifiable in earlier versions but not Aurora MySQL version 3\. 

 For information about parameter names that changed, see [Inclusive language changes for Aurora MySQL version 3](#AuroraMySQL.8.0-inclusive-language)\. 

### Status variables<a name="AuroraMySQL.mysql80-status-vars"></a>

 For information about status variables that aren't applicable to Aurora MySQL, see [MySQL status variables that don't apply to Aurora MySQL](AuroraMySQL.Reference.md#AuroraMySQL.Reference.StatusVars.Inapplicable)\. 

### Inclusive language changes for Aurora MySQL version 3<a name="AuroraMySQL.8.0-inclusive-language"></a>

 Aurora MySQL version 3 is compatible with version 8\.0\.23 from the MySQL community edition\. Aurora MySQL version 3 also includes changes from MySQL 8\.0\.26 related to keywords and system schemas for inclusive language\. For example, the `SHOW REPLICA STATUS` command is now preferred instead of `SHOW SLAVE STATUS`\. 

 The following Amazon CloudWatch metrics have new names in Aurora MySQL version 3\. 

 In Aurora MySQL version 3, only the new metric names are available\. Make sure to update any alarms or other automation that relies on metric names when you upgrade to Aurora MySQL version 3\. 


|  Old name  |  New name  | 
| --- | --- | 
|  ForwardingMasterDMLLatency  |  ForwardingWriterDMLLatency  | 
|  ForwardingMasterOpenSessions  |  ForwardingWriterOpenSessions  | 
|  AuroraDMLRejectedMasterFull  |  AuroraDMLRejectedWriterFull  | 
|  ForwardingMasterDMLThroughput  |  ForwardingWriterDMLThroughput  | 

 The following status variables have new names in Aurora MySQL version 3\. 

 For compatibility, you can use either name in the initial Aurora MySQL version 3 release\. The old status variable names are to be removed in a future release\. 


|  Name to be removed  |  New or preferred name  | 
| --- | --- | 
|  Aurora\_fwd\_master\_dml\_stmt\_duration  |  Aurora\_fwd\_writer\_dml\_stmt\_duration  | 
|  Aurora\_fwd\_master\_dml\_stmt\_count  |  Aurora\_fwd\_writer\_dml\_stmt\_count  | 
|  Aurora\_fwd\_master\_select\_stmt\_duration  |  Aurora\_fwd\_writer\_select\_stmt\_duration  | 
|  Aurora\_fwd\_master\_select\_stmt\_count  |  Aurora\_fwd\_writer\_select\_stmt\_count  | 
|  Aurora\_fwd\_master\_errors\_session\_timeout  |  Aurora\_fwd\_writer\_errors\_session\_timeout  | 
|  Aurora\_fwd\_master\_open\_sessions  |  Aurora\_fwd\_writer\_open\_sessions  | 
|  Aurora\_fwd\_master\_errors\_session\_limit  |  Aurora\_fwd\_writer\_errors\_session\_limit  | 
|  Aurora\_fwd\_master\_errors\_rpc\_timeout  |  Aurora\_fwd\_writer\_errors\_rpc\_timeout  | 

 The following configuration parameters have new names in Aurora MySQL version 3\. 

 For compatibility, you can check the parameter values in the `mysql` client by using either name in the initial Aurora MySQL version 3 release\. You can only modify the values in a custom parameter group by using the new names\. The old parameter names are to be removed in a future release\. 


|  Name to be removed  |  New or preferred name  | 
| --- | --- | 
|  aurora\_fwd\_master\_idle\_timeout  |  aurora\_fwd\_writer\_idle\_timeout  | 
|  aurora\_fwd\_master\_max\_connections\_pct  |  aurora\_fwd\_writer\_max\_connections\_pct  | 
|  master\_verify\_checksum  |  source\_verify\_checksum  | 
|  sync\_master\_info  |  sync\_source\_info  | 
|  init\_slave  |  init\_replica  | 
|  rpl\_stop\_slave\_timeout  |  rpl\_stop\_replica\_timeout  | 
|  log\_slow\_slave\_statements  |  log\_slow\_replica\_statements  | 
|  slave\_max\_allowed\_packet  |  replica\_max\_allowed\_packet  | 
|  slave\_compressed\_protocol  |  replica\_compressed\_protocol  | 
|  slave\_exec\_mode  |  replica\_exec\_mode  | 
|  slave\_type\_conversions  |  replica\_type\_conversions  | 
|  slave\_sql\_verify\_checksum  |  replica\_sql\_verify\_checksum  | 
|  slave\_parallel\_type  |  replica\_parallel\_type  | 
|  slave\_preserve\_commit\_order  |  replica\_preserve\_commit\_order  | 
|  log\_slave\_updates  |  log\_replica\_updates  | 
|  slave\_allow\_batching  |  replica\_allow\_batching  | 
|  slave\_load\_tmpdir  |  replica\_load\_tmpdir  | 
|  slave\_net\_timeout  |  replica\_net\_timeout  | 
|  sql\_slave\_skip\_counter  |  sql\_replica\_skip\_counter  | 
|  slave\_skip\_errors  |  replica\_skip\_errors  | 
|  slave\_checkpoint\_period  |  replica\_checkpoint\_period  | 
|  slave\_checkpoint\_group  |  replica\_checkpoint\_group  | 
|  slave\_transaction\_retries  |  replica\_transaction\_retries  | 
|  slave\_parallel\_workers  |  replica\_parallel\_workers  | 
|  slave\_pending\_jobs\_size\_max  |  replica\_pending\_jobs\_size\_max  | 
|  pseudo\_slave\_mode  |  pseudo\_replica\_mode  | 

 The following stored procedures have new names in Aurora MySQL version 3\. 

 For compatibility, you can use either name in the initial Aurora MySQL version 3 release\. The old procedure names are to be removed in a future release\. 


|  Name to be removed  |  New or preferred name  | 
| --- | --- | 
|  mysql\.rds\_set\_master\_auto\_position  |  mysql\.rds\_set\_source\_auto\_position  | 
|  mysql\.rds\_set\_external\_master  |  mysql\.rds\_set\_external\_source  | 
|  mysql\.rds\_set\_external\_master\_with\_auto\_position  |  mysql\.rds\_set\_external\_source\_with\_auto\_position  | 
|  mysql\.rds\_reset\_external\_master  |  mysql\.rds\_reset\_external\_source  | 
|  mysql\.rds\_next\_master\_log  |  mysql\.rds\_next\_source\_log  | 

### AUTO\_INCREMENT values<a name="AuroraMySQL.mysql80-autoincrement"></a>

 In Aurora MySQL version 3, Aurora preserves the `AUTO_INCREMENT` value for each table when it restarts each DB instance\. In Aurora MySQL version 2, the `AUTO_INCREMENT` value wasn't preserved after a restart\. 

 The `AUTO_INCREMENT` value isn't preserved when you set up a new cluster by restoring from a snapshot, performing a point\-in\-time recovery, and cloning a cluster\. In these cases, the `AUTO_INCREMENT` value is initialized to the value based on the largest column value in the table at the time the snapshot was created\. This behavior is different than in RDS for MySQL 8\.0, where the `AUTO_INCREMENT` value is preserved during these operations\. 

### Temporary tables on reader DB instances<a name="AuroraMySQL.mysql80-temp-tables-readers"></a>

 You can't create temporary tables using the InnoDB storage engine on Aurora MySQL reader instances\. On reader instances, the InnoDB storage engine is configured as read\-only\. Make sure that any `CREATE TEMPORARY TABLE` statements on reader instances run with the `NO_ENGINE_SUBSTITUTION` SQL mode turned off\. 

 The error that you receive is different depending on whether you use a plain `CREATE TEMPORARY TABLE` statement or the variation `CREATE TEMPORARY TABLE AS SELECT`\. The following examples show the different kinds of errors\. 

 This temporary table behavior only applies to read\-only instances\. This first example confirms that's the kind of instance the session is connected to\. 

```
mysql> select @@innodb_read_only;
+--------------------+
| @@innodb_read_only |
+--------------------+
|                  1 |
+--------------------+
```

 For plain `CREATE TEMPORARY TABLE` statements, the statement fails when the `NO_ENGINE_SUBSTITUTION` SQL mode is turned on\. It succeeds when that SQL mode is turned off\. 

```
mysql> set sql_mode = 'NO_ENGINE_SUBSTITUTION';

mysql>  CREATE TEMPORARY TABLE tt2 (id int) ENGINE=InnoDB;
ERROR 3161 (HY000): Storage engine InnoDB is disabled (Table creation is disallowed).
mysql> SET sql_mode = '';

mysql> CREATE TEMPORARY TABLE tt4 (id int) ENGINE=InnoDB;

mysql> SHOW CREATE TABLE tt4\G
*************************** 1. row ***************************
       Table: tt4
Create Table: CREATE TEMPORARY TABLE `tt4` (
  `id` int DEFAULT NULL
) ENGINE=MyISAM DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
```

 For `CREATE TEMPORARY TABLE AS SELECT` statements, the statement fails whether or not the `NO_ENGINE_SUBSTITUTION` SQL mode is turned on\. MySQL community edition doesn't support storage engine substitution with `CREATE TABLE AS SELECT` or `CREATE TEMPORARY TABLE AS SELECT` statements\. For those statements, remove the `ENGINE=InnoDB` clause from your SQL code\. 

```
mysql> set sql_mode = 'NO_ENGINE_SUBSTITUTION';

mysql> CREATE TEMPORARY TABLE tt1 ENGINE=InnoDB AS SELECT * FROM t1;
ERROR 3161 (HY000): Storage engine InnoDB is disabled (Table creation is disallowed).

mysql> SET sql_mode = '';

mysql> CREATE TEMPORARY TABLE tt3 ENGINE=InnoDB AS SELECT * FROM t1;
ERROR 1874 (HY000): InnoDB is in read only mode.
```

### Storage engine for internal temporary tables<a name="AuroraMySQL.mysql80-internal-temp-tables-engine"></a>

 In Aurora MySQL version 3, the way internal temporary tables work is different from earlier Aurora MySQL versions\. Instead of choosing between the InnoDB and MyISAM storage engines for such temporary tables, now you choose between the `TempTable` and InnoDB storage engines\. 

 With the `TempTable` storage engine, you can make an additional choice for how to handle certain data\. The data affected overflows the memory pool that holds all the internal temporary tables for the DB instance\. 

 Those choices can influence the performance for queries that generate high volumes of temporary data, for example while performing aggregations such as `GROUP BY` on large tables\. 

**Tip**  
 If your workload includes queries that generate internal temporary tables, confirm how your application performs with this change by running benchmarks and monitoring performance\-related metrics\.   
 In some cases, the amount of temporary data fits within the `TempTable` memory pool or only overflows the memory pool by a small amount\. In these cases, we recommend using the `TempTable` setting for internal temporary tables and memory\-mapped files to hold any overflow data\. That setting is the default\.   
 If a substantial amount of temporary data overflows the `TempTable` memory pool, we recommend using the `MEMORY` storage engine instead for internal temporary tables\. Or you can choose `TempTable` for internal temporary tables and InnoDB tables to hold any overflow data\. 

 You make the initial choice between the `TempTable` storage engine and the `MEMORY` storage engine for internal temporary tables\. You do so by setting the parameter `internal_tmp_mem_storage_engine`\. This parameter replaces the `internal_tmp_disk_storage_engine` parameter used in Aurora MySQL version 1 and 2\. 

 The `TempTable` storage engine is the default\. `TempTable` uses a common memory pool for all temporary tables that use this engine, instead of a maximum memory limit per table\. The size of this memory pool is specified by the `temptable_max_ram` parameter\. It defaults to 1 GB on DB instances with 16 or more GB of memory, and 16 MB on DB instances with less than 16 GB of memory\. The size of the memory pool influences session\-level memory consumption\. 

 If you use the `TempTable` storage engine and the temporary data exceeds the size of the memory pool, Aurora MySQL stores the overflow data using a secondary mechanism\. 

 You can set the parameters `temptable_use_mmap` and `temptable_max_mmap` to specify if the data overflows to memory\-mapped temporary files or to InnoDB internal temporary tables on disk\. The different data formats and overflow criteria of these overflow mechanisms can affect query performance\. They do so by influencing the amount of data written to disk and the demand on disk storage throughput\. 

 Aurora MySQL stores the overflow data differently depending on your choice of data overflow destination and whether the query runs on a writer or reader DB instance: 
+  On the writer instance, data that overflows to InnoDB internal temporary tables is stored in the Aurora cluster volume\. 
+  On the writer instance, data that overflows to memory\-mapped temporary files resides on local storage on the Aurora MySQL version 3 instance\. 
+  On reader instances, overflow data always resides on memory\-mapped temporary files on local storage\. That's because read\-only instances can't store any data on the Aurora cluster volume\. 

**Note**  
 The configuration parameters related to internal temporary tables apply differently to the writer and reader instances in your cluster\. For reader instances, Aurora MySQL always uses the `TempTable` storage engine and a value of 1 for `temptable_use_mmap`\. The size for `temptable_max_mmap` defaults to 1 GB, for both writer and reader instances, regardless of the DB instance memory size\. You can adjust this value the same way as on the writer instance, except that you can't specify a value of zero for `temptable_max_mmap` on reader instances\. 

### Binary log replication<a name="AuroraMySQL.mysql80-binlog"></a>

 In MySQL 8\.0 community edition, binary log replication is turned on by default\. In Aurora MySQL version 3, binary log replication is turned off by default\. 

**Tip**  
 If your high availability requirements are fulfilled by the Aurora built\-in replication features, you can leave binary log replication turned off\. That way, you can avoid the performance overhead of binary log replication\. You can also avoid the associated monitoring and troubleshooting that are needed to manage binary log replication\. 

 Aurora supports binary log replication from a MySQL 5\.7–compatible source to Aurora MySQL version 3\. The source system can be an Aurora MySQL DB cluster, an RDS for MySQL DB instance, or an on\-premises MySQL instance\. 

 As does community MySQL, Aurora MySQL supports replication from a source running a specific version to a target running the same major version or one major version higher\. For example, replication from a MySQL 5\.6–compatible system to Aurora MySQL version 3 isn't supported\. Replicating from Aurora MySQL version 3 to a MySQL 5\.7–compatible or MySQL 5\.6–compatible system isn't supported\. For details about using binary log replication, see [Replication between Aurora and MySQL or between Aurora and another Aurora DB cluster \(binary log replication\)](AuroraMySQL.Replication.md#AuroraMySQL.Replication.MySQL)\. 

 Aurora MySQL version 3 includes improvements to binary log replication in community MySQL 8\.0, such as filtered replication\. For details about the community MySQL 8\.0 improvements, see [How Servers Evaluate Replication Filtering Rules](https://dev.mysql.com/doc/refman/8.0/en/replication-rules.html) in the *MySQL Reference Manual*\. 

#### Multithreaded replication<a name="AuroraMySQL.multithreaded-replication"></a>

 With Aurora MySQL version 3, Aurora MySQL supports multithreaded replication\. For usage information, see [Multithreaded binary log replication \(Aurora MySQL version 3 and higher\)](AuroraMySQL.Replication.md#binlog-optimization-multithreading)\. 

**Note**  
 We still recommend not using multithreaded replication with Aurora MySQL version 1 and version 2\. 

#### Transaction compression for binary log replication<a name="AuroraMySQL.binlog-transaction-compression"></a>

 For usage information about binary log compression, see [Binary Log Transaction Compression](https://dev.mysql.com/doc/refman/8.0/en/binary-log-transaction-compression.html) in the MySQL Reference Manual\. 

 The following limitations apply to binary log compression in Aurora MySQL version 3: 
+  Transactions whose binary log data is larger than the maximum allowed packet size aren't compressed, regardless of whether the Aurora MySQL binary log compression setting is turned on\. Such transactions are replicated without being compressed\. 
+  If you use a connector for change data capture \(CDC\) that doesn't support MySQL 8\.0 yet, you can't use this feature\. We recommend that you test any third\-party connectors thoroughly with binary log compression before turning on binlog compression on systems that use binlog replication for CDC\. 

## Comparison of Aurora MySQL version 3 and community MySQL 8\.0<a name="Aurora.AuroraMySQL.Compare-80-v3"></a>

 You can use the following information to learn about the changes to be aware of when you convert from a different MySQL 8\.0–compatible system to Aurora MySQL version 3\. 

 In general, Aurora MySQL version 3 supports the feature set of community MySQL 8\.0\.23\. Some new features from MySQL 8\.0 community edition don't apply to Aurora MySQL\. Some of those features aren't compatible with some aspect of Aurora, such as the Aurora storage architecture\. Other features aren't needed because the Amazon RDS management service provides equivalent functionality\. The following features in community MySQL 8\.0 aren't supported or work differently in Aurora MySQL version 3\. 

 For release notes for all Aurora MySQL version 3 releases, see [ Database engine updates for Amazon Aurora MySQL version 3](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraMySQLReleaseNotes/AuroraMySQL.Updates.30Updates.html) in the *Release Notes for Aurora MySQL*\. 

**Topics**
+ [MySQL 8\.0 features not available in Aurora MySQL version 3](#Aurora.AuroraMySQL.Compare-80-v3-features)
+ [Role\-based privilege model](#AuroraMySQL.privilege-model)
+ [Authentication](#AuroraMySQL.mysql80-authentication)

### MySQL 8\.0 features not available in Aurora MySQL version 3<a name="Aurora.AuroraMySQL.Compare-80-v3-features"></a>

 The following features from community MySQL 8\.0 aren't available or work differently in Aurora MySQL version 3\. 
+  Resource groups and associated SQL statements aren't supported in Aurora MySQL\. 
+  The Aurora storage architecture means that you don't have to manually manage files and the underlying storage for your database\. In particular, Aurora handles the undo tablespace differently than community MySQL does\. This difference from community MySQL has the following consequences: 
  +  Aurora MySQL doesn't support named tablespaces\. 
  +  The `innodb_undo_log_truncate` configuration setting is turned off and can't be turned on\. Aurora has its own mechanism for reclaiming storage space\. 
  +  Aurora MySQL doesn't have the `CREATE UNDO TABLESPACE`, `ALTER UNDO TABLESPACE ... SET INACTIVE`, and `DROP UNDO TABLESPACE` statements\. 
  +  Aurora sets the number of undo tablespaces automatically and manages those tablespaces for you\. 
+  TLS 1\.3 isn't supported in Aurora MySQL version 3\. 
+  The `aurora_hot_page_contention` status variable isn't available\. The hot page contention feature isn't supported\. For the full list of status variables not available in Aurora MySQL version 3, see [Status variables](#AuroraMySQL.mysql80-status-vars)\. 
+  You can't modify the settings of any MySQL plugins\. 
+  The X plugin isn't supported\. 

### Role\-based privilege model<a name="AuroraMySQL.privilege-model"></a>

 With Aurora MySQL version 3, you can't modify the tables in the `mysql` database directly\. In particular, you can't set up users by inserting into the `mysql.user` table\. Instead, you use SQL statements to grant role\-based privileges\. You also can't create other kinds of objects such as stored procedures in the `mysql` database\. You can still query the `mysql` tables\. If you use binary log replication, changes made directly to the `mysql` tables on the source cluster aren't replicated to the target cluster\. 

 In some cases, your application might use shortcuts to create users or other objects by inserting into the `mysql` tables\. If so, change your application code to use the corresponding statements such as `CREATE USER`\. If your application creates stored procedures or other objects in the `mysql` database, use a different database instead\. 

 To export metadata for database users during the migration from an external MySQL database, you can use `mysqlpump` command instead of `mysqldump`\. Use the following syntax\. 

```
mysqlpump --exclude-databases=mysql --users
```

 This statement dumps all databases except for the tables in the `mysql` system database\. It also includes `CREATE USER` and `GRANT` statements to reproduce all MySQL users in the migrated database\. You can also use the [pt\-show\-grants tool](https://www.percona.com/doc/percona-toolkit/LATEST/pt-show-grants.html) on the source system to list `CREATE USER` and `GRANT` statements to reproduce all the database users\. 

 To simplify managing permissions for many users or applications, you can use the `CREATE ROLE` statement to create a role that has a set of permissions\. Then you can use the `GRANT` and `SET ROLE` statements and the `current_role` function to assign roles to users or applications, switch the current role, and check which roles are in effect\. For more information on the role\-based permission system in MySQL 8\.0, see [Using Roles](https://dev.mysql.com/doc/refman/8.0/en/roles.html) in the MySQL Reference Manual\. 

 Aurora MySQL version 3 includes a special role that has all of the following privileges\. This role is named `rds_superuser_role`\. The primary administrative user for each cluster already has this role granted\. The `rds_superuser_role` role includes the following privileges for all database objects: 
+  `ALTER` 
+  `APPLICATION_PASSWORD_ADMIN` 
+  `ALTER ROUTINE` 
+  `CONNECTION_ADMIN` 
+  `CREATE` 
+  `CREATE ROLE` 
+  `CREATE ROUTINE` 
+  `CREATE TABLESPACE` 
+  `CREATE TEMPORARY TABLES` 
+  `CREATE USER` 
+  `CREATE VIEW` 
+  `DELETE` 
+  `DROP` 
+  `DROP ROLE` 
+  `EVENT` 
+  `EXECUTE` 
+  `INDEX` 
+  `INSERT` 
+  `LOCK TABLES` 
+  `PROCESS` 
+  `REFERENCES` 
+  `RELOAD` 
+  `REPLICATION CLIENT` 
+  `REPLICATION SLAVE` 
+  `ROLE_ADMIN` 
+  `SET_USER_ID` 
+  `SELECT` 
+  `SHOW DATABASES` 
+  `SHOW VIEW` 
+  `TRIGGER` 
+  `UPDATE` 
+  `XA_RECOVER_ADMIN` 

 The role definition also includes `WITH GRANT OPTION` so that an administrative user can grant that role to other users\. In particular, the administrator must grant any privileges needed to perform binary log replication with the Aurora MySQL cluster as the target\. 

**Tip**  
 To see the full details of the permissions, enter the following statements\.   

```
SHOW GRANTS FOR rds_superuser_role@'%';
SHOW GRANTS FOR name_of_administrative_user_for_your_cluster@'%';
```

 Aurora MySQL version 3 also includes roles that you can use to access other AWS services\. You can set these roles as an alternative to `GRANT` statements\. For example, you specify `GRANT AWS_LAMBDA_ACCESS TO user` instead of `GRANT INVOKE LAMBDA ON *.* TO user`\. For the procedures to access other AWS services, see [Integrating Amazon Aurora MySQL with other AWS services](AuroraMySQL.Integrating.md)\. Aurora MySQL version 3 includes the following roles related to accessing other AWS services: 
+  `AWS_LAMBDA_ACCESS` role, as an alternative to the `INVOKE LAMBDA` privilege\. For usage information, [Invoking a Lambda function from an Amazon Aurora MySQL DB cluster](AuroraMySQL.Integrating.Lambda.md)\. 
+  `AWS_LOAD_S3_ACCESS` role, as an alternative to the `LOAD FROM S3` privilege\. For usage information, see [Loading data into an Amazon Aurora MySQL DB cluster from text files in an Amazon S3 bucket](AuroraMySQL.Integrating.LoadFromS3.md)\. 
+  `AWS_SELECT_S3_ACCESS` role, as an alternative to the `SELECT INTO S3` privilege\. For usage information, see [Saving data from an Amazon Aurora MySQL DB cluster into text files in an Amazon S3 bucket](AuroraMySQL.Integrating.SaveIntoS3.md)\. 
+  `AWS_SAGEMAKER_ACCESS` role, as an alternative to the `INVOKE SAGEMAKER` privilege\. For usage information, see [Using machine learning \(ML\) with Aurora MySQL](mysql-ml.md)\. 
+  `AWS_COMPREHEND_ACCESS` role, as an alternative to the `INVOKE COMPREHEND` privilege\. For usage information, see [Using machine learning \(ML\) with Aurora MySQL](mysql-ml.md)\. 

 When you grant access by using roles in Aurora MySQL version 3, you also activate the role by using the `SET ROLE role_name` or `SET ROLE ALL` statement\. The following example shows how\. Substitute the appropriate role name for `AWS_SELECT_S3_ACCESS`\. 

```
# Grant role to user
mysql> GRANT AWS_SELECT_S3_ACCESS TO 'user'@'domain-or-ip-address'

# Check the current roles for your user. In this case, the AWS_SELECT_S3_ACCESS role has not been activated.
# Only the rds_superuser_role is currently in effect.
mysql> SELECT CURRENT_ROLE();
+--------------------------+
| CURRENT_ROLE()           |
+--------------------------+
| `rds_superuser_role`@`%` |
+--------------------------+
1 row in set (0.00 sec)

# Activate all roles associated with this user using SET ROLE.
# You can activate specific roles or all roles.
# In this case, the user only has 2 roles, so we specify ALL.
mysql> SET ROLE ALL;
Query OK, 0 rows affected (0.00 sec)

# Verify role is now active
mysql> SELECT CURRENT_ROLE();
+--------------------------------------------------+
| CURRENT_ROLE()                                   |
+--------------------------------------------------+
| `AWS_LAMBDA_ACCESS`@`%`,`rds_superuser_role`@`%` |
+--------------------------------------------------+
```

### Authentication<a name="AuroraMySQL.mysql80-authentication"></a>

 In community MySQL 8\.0, the default authentication plugin is `caching_sha2_password`\. Aurora MySQL version 3 still uses the `mysql_native_password` plugin\. You can't change the `default_authentication_plugin` setting\. 

## Upgrading to Aurora MySQL version 3<a name="AuroraMySQL.mysql80-upgrade-procedure"></a>

 For specific upgrade paths to upgrade your database from Aurora MySQL version 1 or 2 to version 3, you can use one of the following approaches: 
+  To upgrade an Aurora MySQL version 2 cluster to version 3, create a snapshot of the version 2 cluster and restore the snapshot to create a new version 3 cluster\. Follow the procedure in [Restoring from a DB cluster snapshot](USER_RestoreFromSnapshot.md)\. Currently, in\-place upgrade isn't available from Aurora MySQL version 2 to Aurora MySQL version 3\. 
+  To upgrade from Aurora MySQL version 1, first do an intermediate upgrade to Aurora MySQL version 2\. To do the upgrade to Aurora MySQL version 2, use any of the upgrade methods in [Upgrading Amazon Aurora MySQL DB clusters](AuroraMySQL.Updates.Upgrading.md)\. Then use the snapshot restore technique to upgrade from Aurora MySQL version 2 to Aurora MySQL version 3\. Snapshot restore isn't available from Aurora MySQL version 1 clusters \(MySQL 5\.6–compatible\) to Aurora MySQL version 3\. 
+  Currently, you can't clone a MySQL 5\.7–compatible Aurora cluster to a MySQL 8\.0–compatible one\. Use the snapshot restore technique instead\. 
+  If you have an Aurora MySQL version 2 cluster that uses backtrack, currently you can't use the snapshot restore method to upgrade to Aurora MySQL version 3\. This limitation applies to all clusters that use backtrack, regardless of whether the backtrack setting is turned on\. In this case, perform a logical dump and restore by using a tool such as the `mysqldump` command\. For more information about using `mysqldump` for Aurora MySQL, see [Migrating from MySQL to Amazon Aurora by using mysqldump](AuroraMySQL.Migrating.ExtMySQL.md#AuroraMySQL.Migrating.ExtMySQL.mysqldump)\. 

**Tip**  
 In some cases, you might specify the option to upload database logs to CloudWatch when you restore the snapshot\. If so, examine the logs in CloudWatch to diagnose any issues that occur during the restore and associated upgrade operation\. The CLI examples in this section demonstrate how to do so using the `--enable-cloudwatch-logs-exports` option\. 

**Topics**
+ [Upgrade planning for Aurora MySQL version 3](#AuroraMySQL.mysql80-planning)
+ [Example of upgrading from Aurora MySQL version 2 to version 3](#AuroraMySQL.mysql80-upgrade-example-v2-v3)
+ [Example of upgrading from Aurora MySQL version 1 to version 3](#AuroraMySQL.mysql80-upgrade-example-v1-v3)
+ [Troubleshooting upgrade issues with Aurora MySQL version 3](#AuroraMySQL.mysql80-upgrade-troubleshooting)
+ [Post\-upgrade cleanup for Aurora MySQL version 3](#AuroraMySQL.mysql80-post-upgrade)

### Upgrade planning for Aurora MySQL version 3<a name="AuroraMySQL.mysql80-planning"></a>

 To help you decide the right time and approach to upgrade, you can learn the differences between Aurora MySQL version 3 and your current Aurora and MySQL environment: 
+  If you are converting from RDS for MySQL 8\.0 or community MySQL 8\.0, see [Comparison of Aurora MySQL version 3 and community MySQL 8\.0](#Aurora.AuroraMySQL.Compare-80-v3)\. 
+  If you are upgrading from Aurora MySQL version 2, RDS for MySQL 5\.7, or community MySQL 5\.7, see [Comparison of Aurora MySQL version 2 and Aurora MySQL version 3](#Aurora.AuroraMySQL.Compare-v2-v3)\. 
+  Create new MySQL 8\.0\-compatible versions of any custom parameter groups\. Apply any necessary custom parameter values to the new parameter groups\. Consult [Parameter changes for Aurora MySQL version 3](#AuroraMySQL.mysql80-parameter-changes) to learn about parameter changes\. 
**Note**  
 For most parameter settings, you can choose the custom parameter group either when you create the cluster or associate the parameter group with the cluster later\. However, if you use a nondefault setting for the `lower_case_table_names` parameter, you must set up the custom parameter group with this setting in advance\. Then specify the parameter group when you perform the snapshot restore to create the cluster\. Any change to the `lower_case_table_names` parameter has no effect after the cluster is created\. 

 You can also find more MySQL\-specific upgrade considerations and tips in [Changes in MySQL 8\.0](https://dev.mysql.com/doc/refman/8.0/en/upgrading-from-previous-series.html) in the *MySQL Reference Manual*\. For example, you can use the command `mysqlcheck --check-upgrade` to analyze your existing Aurora MySQL databases and identify potential upgrade issues\. 

 Currently, the primary upgrade path from earlier Aurora MySQL versions to Aurora MySQL version 3 is by restoring a snapshot to create a new cluster\. You can restore a snapshot of a cluster running any minor version of Aurora MySQL version 2 \(MySQL 5\.7–compatible\) to Aurora MySQL version 3\. To upgrade from Aurora MySQL version 1, you use a two\-step process\. First restore a snapshot to an Aurora MySQL version 2 cluster, then make a snapshot of that cluster and restore it to an Aurora MySQL version 3 cluster\. For the upgrade procedure from Aurora MySQL version 1 or 2, see [Upgrading to Aurora MySQL version 3](#AuroraMySQL.mysql80-upgrade-procedure)\. For general information about upgrading by restoring a snapshot, see [Upgrading Amazon Aurora MySQL DB clusters](AuroraMySQL.Updates.Upgrading.md)\. 

 After you finish the upgrade itself, you can follow the post\-upgrade procedures in [Post\-upgrade cleanup for Aurora MySQL version 3](#AuroraMySQL.mysql80-post-upgrade)\. Finally, test your application's functionality and performance\. 

 If you are converting from RDS from MySQL or community MySQL, follow the migration procedure explained in [Migrating data to an Amazon Aurora MySQL DB cluster](AuroraMySQL.Migrating.md)\. In some cases, you might use binary log replication to synchronize your data with an Aurora MySQL version 3 cluster as part of the migration\. If so, the source system must run a version that's compatible with MySQL 5\.7, or a MySQL 8\.0–compatible version that is 8\.0\.23 or lower\. 

### Example of upgrading from Aurora MySQL version 2 to version 3<a name="AuroraMySQL.mysql80-upgrade-example-v2-v3"></a>

 The following AWS CLI example demonstrates the steps to upgrade an Aurora MySQL version 2 cluster to Aurora MySQL version 3\. 

 The first step is to determine the version of the cluster that you want to upgrade\. The following AWS CLI command shows how\. The output confirms that the original cluster is a MySQL 5\.7–compatible one that's running Aurora MySQL version 2\.09\.2\. 

 This cluster has at least one DB instance\. For the upgrade process to work properly, this original cluster requires a writer instance\. 

```
$ aws rds describe-db-clusters --db-cluster-id cluster-57-upgrade-ok \
  --query '*[].EngineVersion' --output text
5.7.mysql_aurora.2.09.2
```

 The following command shows how to check which upgrade paths are available from a specific version\. In this case, the original cluster is running version `5.7.mysql_aurora.2.09.2`\. The output shows that this version can be upgraded to Aurora MySQL version 3\. 

 If the original cluster uses a version number that is too low to upgrade to Aurora MySQL version 3, the upgrade requires an additional step\. First, restore the snapshot to create a new cluster that could be upgraded to Aurora MySQL version 3\. Then, take a snapshot of that intermediate cluster\. Finally, restore the snapshot of the intermediate cluster to create a new Aurora MySQL version 3 cluster\. 

```
$ aws rds describe-db-engine-versions --engine aurora-mysql \
  --engine-version 5.7.mysql_aurora.2.09.2 \
  --query 'DBEngineVersions[].ValidUpgradeTarget[].EngineVersion'
[
    "5.7.mysql_aurora.2.10.0",
    "5.7.mysql_aurora.2.10.1",
    "8.0.mysql_aurora.3.01.0"
]
```

 The following command creates a snapshot of the cluster to upgrade to Aurora MySQL version 3\. The original cluster remains intact\. You later create a new Aurora MySQL version 3 cluster from the snapshot\. 

```
aws rds create-db-cluster-snapshot --db-cluster-id cluster-57-upgrade-ok \
  --db-cluster-snapshot-id cluster-57-upgrade-ok-snapshot
{
  "DBClusterSnapshotIdentifier": "cluster-57-upgrade-ok-snapshot",
  "DBClusterIdentifier": "cluster-57-upgrade-ok",
  "SnapshotCreateTime": "2021-10-06T23:20:18.087000+00:00"
}
```

 The following command restores the snapshot to a new cluster that's running Aurora MySQL version 3\. 

```
$ aws rds restore-db-cluster-from-snapshot \
  --snapshot-id cluster-57-upgrade-ok-snapshot \
  --db-cluster-id cluster-80-restored --engine aurora-mysql \
  --engine-version 8.0.mysql_aurora.3.01.0 \
  --enable-cloudwatch-logs-exports '["error","general","slowquery","audit"]'
{
  "DBClusterIdentifier": "cluster-80-restored",
  "Engine": "aurora-mysql",
  "EngineVersion": "8.0.mysql_aurora.3.01.0",
  "Status": "creating"
}
```

 Restoring the snapshot sets up the storage for the cluster and establishes the database version that the cluster can use\. Because the compute capacity of the cluster is separate from the storage, you set up any DB instances for the cluster once the cluster itself is created\. The following example creates a writer DB instance using one of the db\.r5 instance classes\. 

**Tip**  
 You might have administration scripts that create DB instances using older instance classes such as db\.r3, db\.r4, db\.t2, or db\.t3\. If so, modify your scripts to use one of the instance classes that are supported with Aurora MySQL version 3\. For information about the instance classes that you can use with Aurora MySQL version 3, see [Instance class support](#AuroraMySQL.mysql80-instance-classes)\. 

```
$ aws rds create-db-instance --db-instance-identifier instance-running-version-3 \
  --db-cluster-identifier cluster-80-restored \
  --db-instance-class db.r5.xlarge --engine aurora-mysql
{
  "DBInstanceIdentifier": "instance-running-version-3",
  "DBClusterIdentifier": "cluster-80-restored",
  "DBInstanceClass": "db.r5.xlarge",
  "EngineVersion": "8.0.mysql_aurora.3.01.0",
  "DBInstanceStatus": "creating"
}
```

 After the upgrade is finished, you can verify the Aurora\-specific version number of the cluster by using the AWS CLI\. 

```
$ aws rds describe-db-clusters --db-cluster-id cluster-80-restored \
  --query '*[].EngineVersion' --output text
8.0.mysql_aurora.3.01.0
```

 You can also verify the MySQL\-specific version of the database engine by calling the `version` function\. 

```
mysql> select version();
+-----------+
| version() |
+-----------+
| 8.0.23    |
+-----------+
```

### Example of upgrading from Aurora MySQL version 1 to version 3<a name="AuroraMySQL.mysql80-upgrade-example-v1-v3"></a>

 The following example shows the two\-stage upgrade process if the original snapshot is from a version that can't be directly restored to Aurora MySQL version 3\. Instead, that snapshot is restored to a cluster running an intermediate version that you can upgrade to Aurora MySQL version 3\. This intermediate cluster doesn't need any associated DB instances\. Then, another snapshot is created from the intermediate cluster\. Finally, the second snapshot is restored to create a new Aurora MySQL version 3 cluster and a writer DB instance\. 

 The Aurora MySQL version 1 cluster that we start with is named `aurora-mysql-v1-to-v2`\. It's running Aurora MySQL version 1\.23\.4\. It has at least one DB instance in the cluster\. 

 This example checks which Aurora MySQL version 2 versions can be upgraded to the `8.0.mysql_aurora.3.01.0` to use on the upgraded cluster\. For this example, we choose version 2\.10\.0 as the intermediate version\. 

```
$ aws rds describe-db-engine-versions --engine aurora-mysql \
  --query '*[].{EngineVersion:EngineVersion,TargetVersions:ValidUpgradeTarget[*].EngineVersion} |
    [?contains(TargetVersions, `'8.0.mysql_aurora.3.01.0'`) == `true`]|[].EngineVersion' \
  --output text
...
5.7.mysql_aurora.2.08.3
5.7.mysql_aurora.2.09.1
5.7.mysql_aurora.2.09.2
5.7.mysql_aurora.2.10.0
...
```

 The following example verifies that Aurora MySQL version 1\.23\.4 to 2\.10\.0 is an available upgrade path\. Thus, the Aurora MySQL version that we're running can be upgraded to 2\.10\.0\. Then that cluster can be upgraded to 3\.01\.0\. 

```
aws rds describe-db-engine-versions --engine aurora \
  --query '*[].{EngineVersion:EngineVersion,TargetVersions:ValidUpgradeTarget[*].EngineVersion} |
    [?contains(TargetVersions, `'5.7.mysql_aurora.2.10.0'`) == `true`]|[].[EngineVersion]' \
  --output text
...
5.6.mysql_aurora.1.22.5
5.6.mysql_aurora.1.23.0
5.6.mysql_aurora.1.23.1
5.6.mysql_aurora.1.23.2
5.6.mysql_aurora.1.23.3
5.6.mysql_aurora.1.23.4
...
```

 The following example creates a snapshot named `aurora-mysql-v1-to-v2-snapshot` that's based on the original Aurora MySQL version 1 cluster\. 

```
$ aws rds create-db-cluster-snapshot \
  --db-cluster-id aurora-mysql-v1-to-v2 \
  --db-cluster-snapshot-id aurora-mysql-v1-to-v2-snapshot
{
  "DBClusterSnapshotIdentifier": "aurora-mysql-v1-to-v2-snapshot",
  "DBClusterIdentifier": "aurora-mysql-v1-to-v2"
}
```

 The following example creates the intermediate Aurora MySQL version 2 cluster from the version 1 snapshot\. This intermediate cluster is named `aurora-mysql-v2-to-v3`\. It's running Aurora MySQL version 2\.10\.0\. 

 The example also creates a writer instance for the cluster\. For the upgrade process to work properly, this intermediate cluster requires a writer instance\. 

```
$ aws rds restore-db-cluster-from-snapshot \
  --snapshot-id aurora-mysql-v1-to-v2-snapshot \
  --db-cluster-id aurora-mysql-v2-to-v3 \
  --engine aurora-mysql --engine-version 5.7.mysql_aurora.2.10.0 \
  --enable-cloudwatch-logs-exports '["error","general","slowquery","audit"]'
{
    "DBCluster": {
        "AllocatedStorage": 1,
        "AvailabilityZones": [
            "us-east-1a",
            "us-east-1d",
            "us-east-1f"
        ],
...

$ aws rds create-db-instance --db-instance-identifier upgrade-demo-instance \
  --db-cluster-identifier aurora-mysql-v2-to-v3 \
  --db-instance-class db.r5.xlarge \
  --engine aurora-mysql
{
  "DBInstanceIdentifier": "upgrade-demo-instance",
  "DBInstanceClass": "db.r5.xlarge",
  "DBInstanceStatus": "creating"
}
```

 The following example creates a snapshot from the intermediate Aurora MySQL version 2 cluster\. This snapshot is named `aurora-mysql-v2-to-v3-snapshot`\. This is the snapshot to be restored to create the Aurora MySQL version 3 cluster\. 

```
$ aws rds create-db-cluster-snapshot \
  --db-cluster-id aurora-mysql-v2-to-v3 \
  --db-cluster-snapshot-id aurora-mysql-v2-to-v3-snapshot
{
  "DBClusterSnapshotIdentifier": "aurora-mysql-v2-to-v3-snapshot",
  "DBClusterIdentifier": "aurora-mysql-v2-to-v3"
}
```

 The following command creates the Aurora MySQL version 3 cluster\. This cluster is named `aurora-mysql-v3-fully-upgraded`\. 

```
$ aws rds restore-db-cluster-from-snapshot \
  --snapshot-id aurora-mysql-v2-to-v3-snapshot \
  --db-cluster-id aurora-mysql-v3-fully-upgraded \
  --engine aurora-mysql --engine-version 8.0.mysql_aurora.3.01.0 \
  --enable-cloudwatch-logs-exports '["error","general","slowquery","audit"]'
{
    "DBCluster": {
        "AllocatedStorage": 1,
        "AvailabilityZones": [
            "us-east-1b",
            "us-east-1c",
            "us-east-1d"
        ],
...
```

 Now that the Aurora MySQL version 3 cluster is created, the following example creates a writer DB instance for it\. When the cluster and the writer instance become available, you can connect to the cluster and begin using it\. All of the data from the original cluster is preserved through each of the snapshot stages\. 

```
$ aws rds create-db-instance \
  --db-instance-identifier instance-also-running-v3 \
  --db-cluster-identifier aurora-mysql-v3-fully-upgraded \
  --db-instance-class db.r5.xlarge --engine aurora-mysql
{
  "DBInstanceIdentifier": "instance-also-running-v3",
  "DBClusterIdentifier": "aurora-mysql-v3-fully-upgraded",
  "DBInstanceClass": "db.r5.xlarge",
  "EngineVersion": "8.0.mysql_aurora.3.01.0",
  "DBInstanceStatus": "creating"
}
```

### Troubleshooting upgrade issues with Aurora MySQL version 3<a name="AuroraMySQL.mysql80-upgrade-troubleshooting"></a>

 If your upgrade to Aurora MySQL version 3 doesn't complete successfully, you can diagnose the cause of the problem\. Then you can make any required changes to the original database schema or table data and run the upgrade process again\. 

 If the upgrade process to Aurora MySQL version 3 fails, the problem is detected while creating and then upgrading the writer instance for the restored snapshot\. Aurora leaves behind the original 5\.7\-compatible writer instance\. That way, you can examine the log from the preliminary checks that Aurora runs before performing the upgrade\. The following examples start with a 5\.7\-compatible database that requires some changes before it can be upgraded to Aurora MySQL version 3\. The examples demonstrate how the first attempted upgrade doesn't succeed, how to examine the log file, and how to fix the problems and run a successful upgrade\. 

 First, we create a new MySQL 5\.7\-compatible cluster named `problematic-57-80-upgrade`\. As the name suggests, this cluster contains at least one schema object that causes a problem during an upgrade to a MySQL 8\.0\-compatible version\. 

```
$ aws rds create-db-cluster --engine aurora-mysql \
  --engine-version 5.7.mysql_aurora.2.10.0 \
  --db-cluster-identifier problematic-57-80-upgrade \
  --master-username my_username \
  --master-user-password my_password
{
  "DBClusterIdentifier": "problematic-57-80-upgrade",
  "Status": "creating"
}

$ aws rds create-db-instance \
  --db-instance-identifier instance-preupgrade \
  --db-cluster-identifier problematic-57-80-upgrade \
  --db-instance-class db.t2.small --engine aurora-mysql
{
  "DBInstanceIdentifier": "instance-preupgrade",
  "DBClusterIdentifier": "problematic-57-80-upgrade",
  "DBInstanceClass": "db.t2.small",
  "DBInstanceStatus": "creating"
}

$ aws rds wait db-instance-available \
  --db-instance-identifier instance-preupgrade
```

 In the cluster that we intend to upgrade, we introduce a problematic table\. Creating a `FULLTEXT` index and then dropping the index leaves behind some metadata that causes a problem during the upgrade\. 

```
$ mysql -u my_username -p \
  -h problematic-57-80-upgrade.cluster-example123.us-east-1.rds.amazonaws.com

mysql> create database problematic_upgrade;
Query OK, 1 row affected (0.02 sec)

mysql> use problematic_upgrade;
Database changed
mysql> CREATE TABLE dangling_fulltext_index
    ->   (id INT AUTO_INCREMENT PRIMARY KEY, txtcol TEXT NOT NULL)
    ->   ENGINE=InnoDB;
Query OK, 0 rows affected (0.05 sec)

mysql> ALTER TABLE dangling_fulltext_index ADD FULLTEXT(txtcol);
Query OK, 0 rows affected, 1 warning (0.14 sec)

mysql> ALTER TABLE dangling_fulltext_index DROP INDEX txtcol;
Query OK, 0 rows affected (0.06 sec)
```

 This example attempts to perform the upgrade procedure\. We take a snapshot of the original cluster and wait for snapshot creation to complete\. Then we restore the snapshot, specifying the MySQL 8\.0\-compatible version number\. We also create the writer instance for the cluster\. That is the point where the upgrade processing actually happens\. Then we wait for the writer instance to become available\. That's the point where the upgrade process is finished, whether it succeeded or failed\. 

**Tip**  
 If you restore the snapshot using the AWS Management Console, Aurora creates the writer instance automatically and upgrades it to the requested engine version\. 

```
$ aws rds create-db-cluster-snapshot --db-cluster-id problematic-57-80-upgrade \
  --db-cluster-snapshot-id problematic-57-80-upgrade-snapshot
{
  "DBClusterSnapshotIdentifier": "problematic-57-80-upgrade-snapshot",
  "DBClusterIdentifier": "problematic-57-80-upgrade",
  "Engine": "aurora-mysql",
  "EngineVersion": "5.7.mysql_aurora.2.10.0"
}

$ aws rds wait db-cluster-snapshot-available \
  --db-cluster-snapshot-id problematic-57-80-upgrade-snapshot

$ aws rds restore-db-cluster-from-snapshot \
  --snapshot-id problematic-57-80-upgrade-snapshot \
  --db-cluster-id cluster-80-attempt-1 --engine aurora-mysql \
  --engine-version 8.0.mysql_aurora.3.01.0 \
  --enable-cloudwatch-logs-exports '["error","general","slowquery","audit"]'
{
  "DBClusterIdentifier": "cluster-80-attempt-1",
  "Engine": "aurora-mysql",
  "EngineVersion": "8.0.mysql_aurora.3.01.0",
  "Status": "creating"
}

$ aws rds create-db-instance --db-instance-identifier instance-attempt-1 \
  --db-cluster-identifier cluster-80-attempt-1 \
  --db-instance-class db.r5.xlarge --engine aurora-mysql
{
  "DBInstanceIdentifier": "instance-attempt-1",
  "DBClusterIdentifier": "cluster-80-attempt-1",
  "DBInstanceClass": "db.r5.xlarge",
  "EngineVersion": "8.0.mysql_aurora.3.01.0",
  "DBInstanceStatus": "creating"
}

$ aws rds wait db-instance-available \
  --db-instance-identifier instance-attempt-1
```

 Now we examine the newly created cluster and associated instance to verify if the upgrade succeeded\. The cluster and instance are still running a MySQL 5\.7\-compatible version\. That means that the upgrade failed\. When an upgrade fails, Aurora only leaves the writer instance behind so that you can examine any log files\. You can't restart the upgrade process with that newly created cluster\. After you correct the problem by making changes in your original cluster, you must run the upgrade steps again: make a new snapshot of the original cluster and restore it to another MySQL 8\.0\-compatible cluster\. 

```
$ aws rds describe-db-clusters \
  --db-cluster-identifier cluster-80-attempt-1 \
  --query '*[].[Status]' --output text
available
$ aws rds describe-db-clusters \
  --db-cluster-identifier cluster-80-attempt-1 \
  --query '*[].[EngineVersion]' --output text
5.7.mysql_aurora.2.10.0

$ aws rds describe-db-instances \
  --db-instance-identifier instance-attempt-1 \
  --query '*[].{DBInstanceStatus:DBInstanceStatus}' --output text
available
$ aws rds describe-db-instances \
  --db-instance-identifier instance-attempt-1 \
  --query '*[].[EngineVersion]' --output text
5.7.mysql_aurora.2.10.0
```

 To get a summary of what happened during the upgrade process, we get a listing of events for the newly created writer instance\. In this example, we list the events over the last 600 minutes to cover the whole time interval of the upgrade process\. The events in the listing aren't necessarily in chronological order\. The highlighted event shows the problem that confirms the cluster couldn't be upgraded\. 

```
$ aws rds describe-events \
  --source-identifier instance-attempt-1 --source-type db-instance \
  --duration 600
{
    "Events": [
        {
            "SourceIdentifier": "instance-attempt-1",
            "SourceType": "db-instance",
            "Message": "Binlog position from crash recovery is mysql-bin-changelog.000001 154",
            "EventCategories": [],
            "Date": "2021-12-03T20:26:17.862000+00:00",
            "SourceArn": "arn:aws:rds:us-east-1:123456789012:db:instance-attempt-1"
        },
        {
            "SourceIdentifier": "instance-attempt-1",
            "SourceType": "db-instance",
            "Message": "Database cluster is in a state that cannot be upgraded:
PreUpgrade checks failed: Oscar PreChecker Found 1 errors",
            "EventCategories": [
                "maintenance"
            ],
            "Date": "2021-12-03T20:26:50.436000+00:00",
            "SourceArn": "arn:aws:rds:us-east-1:123456789012:db:instance-attempt-1"
        },
        {
            "SourceIdentifier": "instance-attempt-1",
            "SourceType": "db-instance",
            "Message": "DB instance created",
            "EventCategories": [
                "creation"
            ],
            "Date": "2021-12-03T20:26:58.830000+00:00",
            "SourceArn": "arn:aws:rds:us-east-1:123456789012:db:instance-attempt-1"
        },
...
```

 To diagnose the exact cause of the problem, examine the database logs for the newly created writer instance\. When an upgrade to an 8\.0\-compatible version fails, the instance contains a log file with the file name `upgrade-prechecks.log`\. This example shows how to detect the presence of that log and then download it to a local file for examination\. 

```
$ aws rds describe-db-log-files --db-instance-identifier instance-attempt-1 \
  --query '*[].[LogFileName]' --output text
error/mysql-error-running.log
error/mysql-error-running.log.2021-12-03.20
error/mysql-error-running.log.2021-12-03.21
error/mysql-error.log
external/mysql-external.log
upgrade-prechecks.log

$ aws rds download-db-log-file-portion --db-instance-identifier instance-attempt-1 \
  --log-file-name upgrade-prechecks.log --starting-token 0 \
  --output text >upgrade_prechecks.log
```

 The `upgrade-prechecks.log` file is in JSON format\. We download it using the `--output text` option to avoid encoding JSON output within another JSON wrapper\. For Aurora MySQL version 3 upgrades, this log always includes certain informational and warning messages\. It only includes error messages if the upgrade fails\. If the upgrade succeeds, the log file isn't produced at all\. The following excerpts show the kinds of entries you can expect to find\. 

```
$ cat upgrade-prechecks.log
{
    "serverAddress": "/tmp%2Fmysql.sock",
    "serverVersion": "5.7.12",
    "targetVersion": "8.0.23",
    "auroraServerVersion": "2.10.0",
    "auroraTargetVersion": "3.01.0",
    "outfilePath": "/rdsdbdata/tmp/PreChecker.log",
    "checksPerformed": [
```

 If `"detectedProblems"` is empty, the upgrade didn't encounter any occurrences of that type of problem\. You can ignore those entries\. 

```
{
    "id": "oldTemporalCheck",
    "title": "Usage of old temporal type",
    "status": "OK",
   "detectedProblems": []
},
```

 Checks for removed variables or changed default values aren't performed automatically\. Aurora uses the parameter group mechanism instead of a physical configuration file\. You always receive some messages with this `CONFIGURATION_ERROR` status, whether or not the variable changes have any effect on your database\. You can consult the MySQL documentation for details about these changes\. 

```
{
    "id": "removedSysLogVars",
    "title": "Removed system variables for error logging to the system log configuration",
    "status": "CONFIGURATION_ERROR",
    "description": "To run this check requires full path to MySQL server
configuration file to be specified at 'configPath' key of options dictionary",
    "documentationLink": "https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-13.html#mysqld-8-0-13-logging"
},
```

 This warning about obsolete date and time data types occurs if the `SQL_MODE` setting in your parameter group is left at the default value\. Your database might or might not contain columns with the affected types\. 

```
{
    "id": "zeroDatesCheck",
    "title": "Zero Date, Datetime, and Timestamp values",
    "status": "OK",
    "description": "Warning: By default zero date/datetime/timestamp
values are no longer allowed in MySQL, as of 5.7.8 NO_ZERO_IN_DATE and
NO_ZERO_DATE are included in SQL_MODE by default. These modes should be used
with strict mode as they will be merged with strict mode in a future release.
If you do not include these modes in your SQL_MODE setting, you are able to
insert date/datetime/timestamp values that contain zeros. It is strongly
advised to replace zero values with valid ones, as they may not work
correctly in the future.",
    "documentationLink": "https://lefred.be/content/mysql-8-0-and-wrong-dates/",
    "detectedProblems": [
        {
            "level": "Warning",
            "dbObject": "global.sql_mode",
            "description": "does not contain either NO_ZERO_DATE or
NO_ZERO_IN_DATE which allows insertion of zero dates"
        }
    ]
},
```

 When the `detectedProblems` field contains entries with a `level` value of `Error`, that means that the upgrade can't succeed until you correct those issues\. 

```
{
    "id": "getDanglingFulltextIndex",
    "title": "Tables with dangling FULLTEXT index reference",
    "status": "OK",
    "description": "Error: The following tables contain dangling
FULLTEXT index which is not supported. It is recommended to rebuild the
table before upgrade.",
    "detectedProblems": [
        {
           "level": "Error",
           "dbObject": "problematic_upgrade.dangling_fulltext_index",
           "description": "Table `problematic_upgrade.dangling_fulltext_index` contains dangling FULLTEXT index. Kindly recreate the table before upgrade."
        }
    ]
},
```

**Tip**  
 To summarize all of those errors and display the associated object and description fields, you can run the command `grep -A 2 '"level": "Error"'` on the contents of the `upgrade-prechecks.log` file\. Doing so displays each error line and the two lines after it, which contain the name of the corresponding database object and guidance about how to correct the problem\.   

```
$ cat upgrade-prechecks.log | grep -A 2 '"level": "Error"'
"level": "Error",
"dbObject": "problematic_upgrade.dangling_fulltext_index",
"description": "Table `problematic_upgrade.dangling_fulltext_index` contains
dangling FULLTEXT index. Kindly recreate the table before upgrade."
```

 This `defaultAuthenticationPlugin` check always displays this warning message for Aurora MySQL version 3 upgrades\. That's because Aurora MySQL version 3 uses the `mysql_native_password` plugin instead of `caching_sha2_password`\. You don't need to take any action for this warning\. 

```
{
    "id": "defaultAuthenticationPlugin",
    "title": "New default authentication plugin considerations",
    "description": "Warning: The new default authentication plugin
'caching_sha2_password' offers more secure password hashing than previously
used 'mysql_native_password' (and consequent improved client connection
...
    "documentationLink": "https://dev.mysql.com/doc/refman/8.0/en/upgrading-from-previous-series.html#upgrade-caching-sha2-password-compatibility-issues\nhttps://dev.mysql.com/doc/refman/8.0/en/upgrading-from-previous-series.html#upgrade-caching-sha2-password-replication"
}
```

 The end of the `upgrade-prechecks.log` file summarizes how many checks encountered each type of minor or severe problem\. A nonzero `errorCount` indicates that the upgrade failed\. 

```
    ],
    "errorCount": 1,
    "warningCount": 2,
    "noticeCount": 0,
    "Summary": "1 errors were found. Please correct these issues before
upgrading to avoid compatibility issues."
}
```

 The next sequence of examples demonstrates how to fix this particular issue and run the upgrade process again\. This time, the upgrade succeeds\. 

 First, we go back to the original cluster and create a new table with the same structure and contents as the one with faulty metadata\. In practice, you would probably rename this table back to the original table name after the upgrade\. 

```
$ mysql -u my_username -p \
  -h problematic-57-80-upgrade.cluster-example123.us-east-1.rds.amazonaws.com

mysql> show databases;
+---------------------+
| Database            |
+---------------------+
| information_schema  |
| mysql               |
| performance_schema  |
| problematic_upgrade |
| sys                 |
+---------------------+
5 rows in set (0.00 sec)

mysql> use problematic_upgrade;
mysql> show tables;
+-------------------------------+
| Tables_in_problematic_upgrade |
+-------------------------------+
| dangling_fulltext_index       |
+-------------------------------+
1 row in set (0.00 sec)

mysql> desc dangling_fulltext_index;
+--------+---------+------+-----+---------+----------------+
| Field  | Type    | Null | Key | Default | Extra          |
+--------+---------+------+-----+---------+----------------+
| id     | int(11) | NO   | PRI | NULL    | auto_increment |
| txtcol | text    | NO   |     | NULL    |                |
+--------+---------+------+-----+---------+----------------+
2 rows in set (0.00 sec)

mysql> CREATE TABLE recreated_table LIKE dangling_fulltext_index;
Query OK, 0 rows affected (0.03 sec)

mysql> INSERT INTO recreated_table SELECT * FROM dangling_fulltext_index;
Query OK, 0 rows affected (0.00 sec)

mysql> drop table dangling_fulltext_index;
Query OK, 0 rows affected (0.05 sec)
```

 Now we go through the same process as before: creating a snapshot from the original cluster, restoring the snapshot to a new MySQL 8\.0\-compatible cluster, and creating a writer instance to complete the upgrade process\. 

```
$ aws rds create-db-cluster-snapshot --db-cluster-id problematic-57-80-upgrade \
  --db-cluster-snapshot-id problematic-57-80-upgrade-snapshot-2
{
  "DBClusterSnapshotIdentifier": "problematic-57-80-upgrade-snapshot-2",
  "DBClusterIdentifier": "problematic-57-80-upgrade",
  "Engine": "aurora-mysql",
  "EngineVersion": "5.7.mysql_aurora.2.10.0"
}

$ aws rds wait db-cluster-snapshot-available \
  --db-cluster-snapshot-id problematic-57-80-upgrade-snapshot-2

$ aws rds restore-db-cluster-from-snapshot \
  --snapshot-id problematic-57-80-upgrade-snapshot-2 \
  --db-cluster-id cluster-80-attempt-2 --engine aurora-mysql \
  --engine-version 8.0.mysql_aurora.3.01.0 \
  --enable-cloudwatch-logs-exports '["error","general","slowquery","audit"]'
{
  "DBClusterIdentifier": "cluster-80-attempt-2",
  "Engine": "aurora-mysql",
  "EngineVersion": "8.0.mysql_aurora.3.01.0",
  "Status": "creating"
}

$ aws rds create-db-instance --db-instance-identifier instance-attempt-2 \
  --db-cluster-identifier cluster-80-attempt-2 \
  --db-instance-class db.r5.xlarge --engine aurora-mysql
{
  "DBInstanceIdentifier": "instance-attempt-2",
  "DBClusterIdentifier": "cluster-80-attempt-2",
  "DBInstanceClass": "db.r5.xlarge",
  "EngineVersion": "8.0.mysql_aurora.3.01.0",
  "DBInstanceStatus": "creating"
}

$ aws rds wait db-instance-available \
  --db-instance-identifier instance-attempt-2
```

 This time, checking the version after the upgrade completes confirms that the version number changed to reflect Aurora MySQL version 3\. We can connect to the database and confirm the MySQL engine version is an 8\.0\-compatible one\. We confirm that the upgraded cluster contains the fixed version of the table that caused the original upgrade problem\. 

```
$ aws rds describe-db-clusters \
  --db-cluster-identifier cluster-80-attempt-2 \
  --query '*[].[EngineVersion]' --output text
8.0.mysql_aurora.3.01.0
$ aws rds describe-db-instances \
  --db-instance-identifier instance-attempt-2 \
  --query '*[].[EngineVersion]' --output text
8.0.mysql_aurora.3.01.0

$ mysql -h cluster-80-attempt-2.cluster-example123.us-east-1.rds.amazonaws.com \
  -u my_username -p

mysql> select version();
+-----------+
| version() |
+-----------+
| 8.0.23    |
+-----------+
1 row in set (0.00 sec)

mysql> show databases;
+---------------------+
| Database            |
+---------------------+
| information_schema  |
| mysql               |
| performance_schema  |
| problematic_upgrade |
| sys                 |
+---------------------+
5 rows in set (0.00 sec)

mysql> use problematic_upgrade;
Database changed
mysql> show tables;
+-------------------------------+
| Tables_in_problematic_upgrade |
+-------------------------------+
| recreated_table               |
+-------------------------------+
1 row in set (0.00 sec)
```

### Post\-upgrade cleanup for Aurora MySQL version 3<a name="AuroraMySQL.mysql80-post-upgrade"></a>

 After you finish upgrading any Aurora MySQL version 1 or 2 clusters to Aurora MySQL version 3, you can perform these other cleanup actions: 
+  Create new MySQL 8\.0–compatible versions of any custom parameter groups\. Apply any necessary custom parameter values to the new parameter groups\. 
+  Update any CloudWatch alarms, setup scripts, and so on to use the new names for any metrics whose names were affected by inclusive language changes\. For a list of such metrics, see [Inclusive language changes for Aurora MySQL version 3](#AuroraMySQL.8.0-inclusive-language)\. 
+  Update any AWS CloudFormation templates to use the new names for any configuration parameters whose names were affected by inclusive language changes\. For a list of such parameters, see [Inclusive language changes for Aurora MySQL version 3](#AuroraMySQL.8.0-inclusive-language)\. 

#### Spatial indexes<a name="AuroraMySQL.mysql80-spatial"></a>

 After upgrading to Aurora MySQL version 3, check if you need to drop or recreate objects and indexes related to spatial indexes\. Before MySQL 8\.0, Aurora could optimize spatial queries using indexes that didn't contain a spatial resource identifier \(SRID\)\. Aurora MySQL version 3 only uses spatial indexes containing SRIDs\. During an upgrade, Aurora automatically drops any spatial indexes without SRIDs and prints warning messages in the database log\. If you observe such warning messages, create new spatial indexes with SRIDs after the upgrade\. For more information about changes to spatial functions and data types in MySQL 8\.0, see [Changes in MySQL 8\.0](https://dev.mysql.com/doc/refman/8.0/en/upgrading-from-previous-series.html) in the *MySQL Reference Manual*\. 