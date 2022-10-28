# Comparison of Aurora MySQL version 2 and Aurora MySQL version 3<a name="Aurora.AuroraMySQL.Compare-v2-v3"></a>

Use the following to learn about changes to be aware of when you upgrade your Aurora MySQL version 2 cluster to version 3\.

**Topics**
+ [Feature differences between Aurora MySQL version 2 and 3](#AuroraMySQL.Compare-v2-v3-features)
+ [Instance class support](#AuroraMySQL.mysql80-instance-classes)
+ [Parameter changes for Aurora MySQL version 3](#AuroraMySQL.mysql80-parameter-changes)
+ [Status variables](#AuroraMySQL.mysql80-status-vars)
+ [Inclusive language changes for Aurora MySQL version 3](#AuroraMySQL.8.0-inclusive-language)
+ [AUTO\_INCREMENT values](#AuroraMySQL.mysql80-autoincrement)
+ [Binary log replication](#AuroraMySQL.mysql80-binlog)

## Feature differences between Aurora MySQL version 2 and 3<a name="AuroraMySQL.Compare-v2-v3-features"></a>

 The following Amazon Aurora MySQL features are supported in Aurora MySQL for MySQL 5\.7, but these features aren't supported in Aurora MySQL for MySQL 8\.0: 
+  Backtrack currently isn't available for Aurora MySQL version 3 clusters\. We intend to make this feature available in a subsequent minor version\. 

   If you have an Aurora MySQL version 2 cluster that uses backtrack, currently you can't use the snapshot restore method to upgrade to Aurora MySQL version 3\. This limitation applies to all clusters that use backtrack clusters, regardless of whether the backtrack setting is turned on\. For details about upgrade procedures, see [Upgrading to Aurora MySQL version 3](AuroraMySQL.mysql80-upgrade-procedure.md)\. 
+  You can't use Aurora MySQL version 3 for Aurora Serverless v1 clusters\. Aurora MySQL version 3 works with Aurora Serverless v2\. 
+  Lab mode doesn't apply to Aurora MySQL version 3\. There aren't any lab mode features in Aurora MySQL version 3\. Instant DDL supersedes the fast online DDL feature that was formerly available in lab mode\. For an example, see [Instant DDL \(Aurora MySQL version 3\)](AuroraMySQL.Managing.FastDDL.md#AuroraMySQL.mysql80-instant-ddl)\. 
+  The query cache is removed from community MySQL 8\.0 and also from Aurora MySQL version 3\. 
+  Aurora MySQL version 3 is compatible with the community MySQL hash join feature\. The Aurora\-specific implementation of hash joins in Aurora MySQL version 2 isn't used\. For information about using hash joins with Aurora parallel query, see [Turning on hash join for parallel query clusters](aurora-mysql-parallel-query.md#aurora-mysql-parallel-query-enabling-hash-join) and [Aurora MySQL hints](AuroraMySQL.Reference.md#AuroraMySQL.Reference.Hints)\. For general usage information about hash joins, see [Hash Join Optimization](https://dev.mysql.com/doc/refman/8.0/en/hash-joins.html) in the *MySQL Reference Manual*\. 
+  Currently, you can't restore a physical backup from the Percona XtraBackup tool to an Aurora MySQL version 3 cluster\. We intend to support this feature in a subsequent minor version\. 
+  The `mysql.lambda_async` stored procedure that was deprecated in Aurora MySQL version 2 is removed in version 3\. For version 3, use the asynchronous function `lambda_async` instead\. 
+  The default character set in Aurora MySQL version 3 is `utf8mb4`\. In Aurora MySQL version 2, the default character set was `latin1`\. For information about this character set, see [The utf8mb4 Character Set \(4\-Byte UTF\-8 Unicode Encoding\)](https://dev.mysql.com/doc/refman/8.0/en/charset-unicode-utf8mb4.html) in the *MySQL Reference Manual*\. 
+  The `innodb_flush_log_at_trx_commit` configuration parameter can't be modified\. The default value of 1 always applies\. 

 Some Aurora MySQL features are available for certain combinations of AWS Region and DB engine version\. For details, see [Supported features in Amazon Aurora by AWS Region and Aurora DB engine](Concepts.AuroraFeaturesRegionsDBEngines.grids.md)\. 

## Instance class support<a name="AuroraMySQL.mysql80-instance-classes"></a>

 Aurora MySQL version 3 supports a different set of instance classes than Aurora MySQL version 2 does: 
+  For larger instances, you can use the modern instance classes such as `db.r5`, `db.r6g`, and `db.x2g`\. 
+  For smaller instances, you can use the modern instance classes such as `db.t3` and `db.t4g`\. 

 The following instance classes from Aurora MySQL version 2 aren't available for Aurora MySQL version 3: 
+  `db.r4` 
+  `db.r3` 
+  `db.t3.small` 
+  `db.t2` 

 Check your administration scripts for any CLI statements that create Aurora MySQL DB instances\. Hardcode instance class names that aren't available for Aurora MySQL version 3\. If necessary, modify the instance class names to ones that Aurora MySQL version 3 supports\. 

**Tip**  
 To check the instance classes that you can use for a specific combination of Aurora MySQL version and AWS Region, use the `describe-orderable-db-instance-options` AWS CLI command\. 

 For full details about Aurora instance classes, see [Aurora DB instance classes](Concepts.DBInstanceClass.md)\. 

## Parameter changes for Aurora MySQL version 3<a name="AuroraMySQL.mysql80-parameter-changes"></a>

 Aurora MySQL version 3 includes new cluster\-level and instance\-level configuration parameters\. Aurora MySQL version 3 also removes some parameters that were present in Aurora MySQL version 2\. Some parameter names are changed as a result of the initiative for inclusive language\. For backward compatibility, you can still retrieve the parameter values using either the old names or the new names\. However, you must use the new names to specify parameter values in a custom parameter group\. 

 In Aurora MySQL version 3, the value of the `lower_case_table_names` parameter is set permanently at the time the cluster is created\. If you use a nondefault value for this option, set up your Aurora MySQL version 3 custom parameter group before upgrading\. Then specify the parameter group during the create cluster or snapshot restore operation\. 

 For the full list of Aurora MySQL cluster parameters, see [Cluster\-level parameters](AuroraMySQL.Reference.md#AuroraMySQL.Reference.Parameters.Cluster)\. The table covers all the parameters from Aurora MySQL version 1, 2, and 3\. The table includes notes showing which parameters are new in Aurora MySQL version 3 or were removed from Aurora MySQL version 3\. 

 For the full list of Aurora MySQL instance parameters, see [Instance\-level parameters](AuroraMySQL.Reference.md#AuroraMySQL.Reference.Parameters.Instance)\. The table covers all the parameters from Aurora MySQL version 1, 2, and 3\. The table includes notes showing which parameters are new in Aurora MySQL version 3 and which parameters were removed from Aurora MySQL version 3\. It also includes notes showing which parameters were modifiable in earlier versions but not Aurora MySQL version 3\. 

 For information about parameter names that changed, see [Inclusive language changes for Aurora MySQL version 3](#AuroraMySQL.8.0-inclusive-language)\. 

## Status variables<a name="AuroraMySQL.mysql80-status-vars"></a>

 For information about status variables that aren't applicable to Aurora MySQL, see [MySQL status variables that don't apply to Aurora MySQL](AuroraMySQL.Reference.md#AuroraMySQL.Reference.StatusVars.Inapplicable)\. 

## Inclusive language changes for Aurora MySQL version 3<a name="AuroraMySQL.8.0-inclusive-language"></a>

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

## AUTO\_INCREMENT values<a name="AuroraMySQL.mysql80-autoincrement"></a>

 In Aurora MySQL version 3, Aurora preserves the `AUTO_INCREMENT` value for each table when it restarts each DB instance\. In Aurora MySQL version 2, the `AUTO_INCREMENT` value wasn't preserved after a restart\. 

 The `AUTO_INCREMENT` value isn't preserved when you set up a new cluster by restoring from a snapshot, performing a point\-in\-time recovery, and cloning a cluster\. In these cases, the `AUTO_INCREMENT` value is initialized to the value based on the largest column value in the table at the time the snapshot was created\. This behavior is different than in RDS for MySQL 8\.0, where the `AUTO_INCREMENT` value is preserved during these operations\. 

## Binary log replication<a name="AuroraMySQL.mysql80-binlog"></a>

 In MySQL 8\.0 community edition, binary log replication is turned on by default\. In Aurora MySQL version 3, binary log replication is turned off by default\. 

**Tip**  
 If your high availability requirements are fulfilled by the Aurora built\-in replication features, you can leave binary log replication turned off\. That way, you can avoid the performance overhead of binary log replication\. You can also avoid the associated monitoring and troubleshooting that are needed to manage binary log replication\. 

 Aurora supports binary log replication from a MySQL 5\.7–compatible source to Aurora MySQL version 3\. The source system can be an Aurora MySQL DB cluster, an RDS for MySQL DB instance, or an on\-premises MySQL instance\. 

 As does community MySQL, Aurora MySQL supports replication from a source running a specific version to a target running the same major version or one major version higher\. For example, replication from a MySQL 5\.6–compatible system to Aurora MySQL version 3 isn't supported\. Replicating from Aurora MySQL version 3 to a MySQL 5\.7–compatible or MySQL 5\.6–compatible system isn't supported\. For details about using binary log replication, see [Replication between Aurora and MySQL or between Aurora and another Aurora DB cluster \(binary log replication\)](AuroraMySQL.Replication.MySQL.md)\. 

 Aurora MySQL version 3 includes improvements to binary log replication in community MySQL 8\.0, such as filtered replication\. For details about the community MySQL 8\.0 improvements, see [How Servers Evaluate Replication Filtering Rules](https://dev.mysql.com/doc/refman/8.0/en/replication-rules.html) in the *MySQL Reference Manual*\. 

### Multithreaded replication<a name="AuroraMySQL.multithreaded-replication"></a>

 With Aurora MySQL version 3, Aurora MySQL supports multithreaded replication\. For usage information, see [Multithreaded binary log replication \(Aurora MySQL version 3 and higher\)](AuroraMySQL.Replication.MySQL.md#binlog-optimization-multithreading)\. 

**Note**  
 We still recommend not using multithreaded replication with Aurora MySQL version 1 and version 2\. 

### Transaction compression for binary log replication<a name="AuroraMySQL.binlog-transaction-compression"></a>

 For usage information about binary log compression, see [Binary Log Transaction Compression](https://dev.mysql.com/doc/refman/8.0/en/binary-log-transaction-compression.html) in the MySQL Reference Manual\. 

 The following limitations apply to binary log compression in Aurora MySQL version 3: 
+  Transactions whose binary log data is larger than the maximum allowed packet size aren't compressed\. This is true regardless of whether the Aurora MySQL binary log compression setting is turned on\. Such transactions are replicated without being compressed\. 
+  If you use a connector for change data capture \(CDC\) that doesn't support MySQL 8\.0 yet, you can't use this feature\. We recommend that you test any third\-party connectors thoroughly with binary log compression\. Also, we recommend that you do so before turning on binlog compression on systems that use binlog replication for CDC\. 