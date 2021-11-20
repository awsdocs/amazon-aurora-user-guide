# Aurora MySQL database engine updates 2021\-05\-25 \(version 2\.10\.0\)<a name="AuroraMySQL.Updates.2100"></a><a name="2100"></a><a name="2.10.0"></a>

 **Version:** 2\.10\.0 

 Aurora MySQL 2\.10\.0 is generally available\. Aurora MySQL 2\.x versions are compatible with MySQL 5\.7, and Aurora MySQL 1\.x versions are compatible with MySQL 5\.6\. 

 Currently supported Aurora MySQL releases are 1\.14\.\*, 1\.15\.\*, 1\.16\.\*, 1\.17\.\*, 1\.18\.\*, 1\.19\.\*, 1\.20\.\*, 1\.21\.\*, 1\.22\.\*, 1\.23\.\*, 2\.01\.\*, 2\.02\.\*, 2\.03\.\*, 2\.04\.\*, 2\.05\.\*, 2\.06\.\*, 2\.07\.\*, 2\.08\.\*, 2\.09\.\*, and 2\.10\.\*\. 

 You can upgrade an existing Aurora MySQL 2\.\* database cluster to Aurora MySQL 2\.10\.0\. For clusters running Aurora MySQL version 1, you can upgrade an existing Aurora MySQL 1\.23 or higher cluster directly to 2\.10\.0\. You can also restore a snapshot from any currently supported Aurora MySQL release into Aurora MySQL 2\.10\.0\. 

 To create a cluster with an older version of Aurora MySQL, specify the engine version through the AWS Management Console, the AWS CLI, or the Amazon RDS API\. 

 If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB cluster](USER_UpgradeDBInstance.Maintenance.md)\. 

**Note**  
 For information on how to upgrade your Aurora MySQL database cluster, see [Upgrading the minor version or patch level of an Aurora MySQL DB cluster](AuroraMySQL.Updates.Patching.md)\. 

## Improvements<a name="AuroraMySQL.Updates.2100.Improvements"></a>

 **Security fixes:** 
+ [CVE\-2021\-23841](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-23841)
+ [CVE\-2021\-3449](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-3449)
+ [CVE\-2020\-28196](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-28196)
+  [CVE\-2020\-14790](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14790) 
+  [CVE\-2020\-14776](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14776) 
+  [CVE\-2020\-14567](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14567) 
+  [CVE\-2020\-14559](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14559) 
+  [CVE\-2020\-14553](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14553) 
+  [CVE\-2020\-14547](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14547) 
+  [CVE\-2020\-14540](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14540) 
+  [CVE\-2020\-14539](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14539) 
+  [CVE\-2018\-3251](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-3251) 
+  [CVE\-2018\-3156](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-3156) 
+  [CVE\-2018\-3143](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-3143) 

 **New features:** 
+  The `db.t3.large` instance class is now supported for Aurora MySQL\. 
+  *Binary log replication:* 
  +  Introduced the binlog I/O cache to improve binlog performance by reducing contention between writer threads and dump threads\. For more information, see [Optimizing binary log replication](AuroraMySQL.Replication.md#binlog-optimization)\. 
  +  In [Aurora MySQL version 2\.08](AuroraMySQL.Updates.2080.md), we introduced improved binary log \(binlog\) processing to reduce crash recovery time and commit time latency when very large transactions are involved\. These improvements are now supported for clusters that have GTID enabled\. 
+  *Improved reader instance availability:* 
  +  Previously, when a writer instance restarted, all reader instances in an Aurora MySQL cluster restarted as well\. With today's launch, in\-Region reader instances continue to serve read requests during a writer instance restart, improving read availability in the cluster\. For more information, see [Rebooting an Aurora MySQL cluster \(version 2\.10 and higher\)](USER_RebootCluster.md#aurora-mysql-survivable-replicas)\. 
**Important**  
 After you upgrade to Aurora MySQL 2\.10, rebooting the writer instance doesn't perform a reboot of the entire cluster\. If you want to reboot the entire cluster, now you reboot any reader instances in the cluster after rebooting the writer instance\. 
+  Improved the performance of the read ahead page reads requested by logical read ahead \(LRA\)  technique\. This was done by batching the multiple page reads in a single request sent to Aurora storage\. As a result, the queries that use the LRA  optimization execute up to 3x faster\. 
+  *Zero\-downtime restarts and patching:* 
  +  Improved zero\-downtime restart \(ZDR\) and zero–downtime patching \(ZDP\) to enable ZDR and ZDP in a wider range of scenarios, including the added support for cases when binary logging is enabled\. Also, improved visibility into ZDR and ZDP events\. See documentation for details: [Zero\-downtime restart \(ZDR\) for Amazon Aurora MySQL](AuroraMySQL.Replication.md#AuroraMySQL.Replication.Availability) and [Using zero\-downtime patching](AuroraMySQL.Updates.Patching.md#AuroraMySQL.Updates.ZDP)\. 

 **Availability improvements:** 
+  Improvements for faster startup when the database has a large number of temporary indexes and tables created during a prior interrupted DDL activity\. 
+  Fixed multiple issues related to repeated restarts during the crash recovery of interrupted DDL operations, such as `DROP TRIGGER`, `ALTER TABLE`, and specifically `ALTER TABLE` that modifies the type of partitioning or number of partitions in a table\. 
+  Fixed an issue that could cause a server restart during Database Activity Streams \(DAS\) log processing\. 
+  Fixed an issue printing an error message while processing an `ALTER` query on system tables\. 

 **General improvements:** 
+  Fixed an issue where the query cache could return stale results on a reader instance\. 
+  Fixed an issue where some Aurora commit metrics were not being updated when the system variable `innodb_flush_log_at_trx_commit` was set to 0 or 2\. 
+  Fixed an issue where a query result stored in the query cache was not refreshed by multistatement transactions\. 
+  Fixed an issue that could cause the last\-modified timestamp of binary log files to not be updated correctly\. This could lead to binary log files being purged prematurely, before reaching the customer\-configured retention period\. 
+  Fixed incorrect reported binlog filename and position from InnoDB after crash recovery\. 
+  Fixed an issue that could cause large transactions to generate incorrect binlog events if the `binlog_checksum` parameter was set to `NONE`\. 
+  Fixed an issue that caused a binlog replica to stop with an error if the replicated transaction contained a DDL statement and a large number of row changes\. 
+  Fixed an issue leading to a restart in a reader instance when dropping a table\. 
+  Fixed an issue that caused open source connectors to fail when attempting to consume a binlog file with a large transaction\. 
+  Fixed an issue that could lead to incorrect query results on the large geometry column after creating a spatial index on the table with the large geometry values\. 
+  The database now recreates the temporary tablespace during restart, which allows the associated storage space to be freed and reclaimed\. 
+  Fixed an issue that prevented `performance_schema` tables from being truncated on Aurora reader instances\. 
+  Fixed an issue that caused a binlog replica to stop with an `HA_ERR_KEY_NOT_FOUND` error\. 
+  Fixed an issue that caused the database to restart when running `FLUSH TABLES WITH READ LOCK` statement\. 
+  Fixed an issue that prevented the use of user\-level lock functions on Aurora reader instances\. 

## Integration of MySQL community edition bug fixes<a name="AuroraMySQL.Updates.2100.Patches"></a>
+  Interleaved transactions could sometimes deadlock the replica applier when the transaction isolation level was set to [REPEATABLE READ](https://dev.mysql.com/doc/refman/5.7/en/innodb-transaction-isolation-levels.html#isolevel_repeatable-read)\. \(Bug \#25040331\) 
+  When a stored procedure contained a statement referring to a view which in turn referred to another view, the procedure could not be invoked successfully more than once\. \(Bug \#87858, Bug \#26864199\) 
+  For queries with many `OR` conditions, the optimizer now is more memory\-efficient and less likely to exceed the memory limit imposed by the [range\_optimizer\_max\_mem\_size](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_range_optimizer_max_mem_size) system variable\. In addition, the default value for that variable has been raised from 1,536,000 to 8,388,608\. \(Bug \#79450, Bug \#22283790\) 
+  *Replication:* In the `next_event()` function, which is called by a replica's SQL thread to read the next event from the relay log, the SQL thread did not release the `relaylog.log_lock` it acquired when it ran into an error \(for example, due to a closed relay log\), causing all other threads waiting to acquire a lock on the relay log to hang\. With this fix, the lock is released before the SQL thread leaves the function under the situation\. \(Bug \#21697821\) 
+  Fixing a memory corruption for `ALTER TABLE` with virtual column\. \(Bug \#24961167; Bug \#24960450\) 
+  *Replication:* Multithreaded replicas could not be configured with small queue sizes using [slave\_pending\_jobs\_size\_max](https://dev.mysql.com/doc/refman/5.7/en/replication-options-replica.html#sysvar_slave_pending_jobs_size_max) if they ever needed to process transactions larger than that size\. Any packet larger than [slave\_pending\_jobs\_size\_max](https://dev.mysql.com/doc/refman/5.7/en/replication-options-replica.html#sysvar_slave_pending_jobs_size_max) was rejected with the error `ER_MTS_EVENT_BIGGER_PENDING_JOBS_SIZE_MAX`, even if the packet was smaller than the limit set by [slave\_max\_allowed\_packet](https://dev.mysql.com/doc/refman/5.7/en/replication-options-replica.html#sysvar_slave_max_allowed_packet)\. With this fix, [slave\_pending\_jobs\_size\_max](https://dev.mysql.com/doc/refman/5.7/en/replication-options-replica.html#sysvar_slave_pending_jobs_size_max) becomes a soft limit rather than a hard limit\. If the size of a packet exceeds [slave\_pending\_jobs\_size\_max](https://dev.mysql.com/doc/refman/5.7/en/replication-options-replica.html#sysvar_slave_pending_jobs_size_max) but is less than [slave\_max\_allowed\_packet](https://dev.mysql.com/doc/refman/5.7/en/replication-options-replica.html#sysvar_slave_max_allowed_packet), the transaction is held until all the replica workers have empty queues, and then processed\. All subsequent transactions are held until the large transaction has been completed\. The queue size for replica workers can therefore be limited while still allowing occasional larger transactions\. \(Bug \#21280753, Bug \#77406\) 
+  *Replication:* When using a multithreaded replica, applier errors displayed worker ID data that was inconsistent with data externalized in Performance Schema replication tables\. \(Bug \#25231367\) 
+  *Replication:* On a GTID\-based replication replica running with [–gtid\-mode=ON](https://dev.mysql.com/doc/refman/5.7/en/replication-options-gtids.html#sysvar_gtid_mode), [–log\-bin=OFF](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin), and using [–slave\-skip\-errors](https://dev.mysql.com/doc/refman/5.7/en/replication-options-replica.html#sysvar_slave_skip_errors), when an error was encountered that should be ignored `Exec_Master_Log_Pos` was not being correctly updated, causing `Exec_Master_Log_Pos` to loose synchrony with `Read_master_log_pos`\. If a `GTID_NEXT` was not specified, the replica would never update its GTID state when rolling back from a single statement transaction\. The `Exec_Master_Log_Pos` would not be updated because even though the transaction was finished, its GTID state would show otherwise\. The fix removes the restraint of updating the GTID state when a transaction is rolled back only if `GTID_NEXT` is specified\. \(Bug \#22268777\) 
+  *Replication:* A partially failed statement was not correctly consuming an auto\-generated or specified GTID when binary logging was disabled\. The fix ensures that a partially failed [DROP TABLE](https://dev.mysql.com/doc/refman/5.7/en/drop-table.html), a partially failed [DROP USER](https://dev.mysql.com/doc/refman/5.7/en/drop-user.html), or a partially failed [DROP VIEW](http://simonlightstone.contently.com/) consume respectively the relevant GTID and save it into `@@GLOBAL.GTID_EXECUTED` and `mysql.gtid_executed` table when binary logging is disabled\. \(Bug \#21686749\) 
+  *Replication:* Replicas running MySQL 5\.7 could not connect to a MySQL 5\.5 source due to an error retrieving the [server\_uuid](https://dev.mysql.com/doc/refman/5.7/en/replication-options.html#sysvar_server_uuid), which is not part of MySQL 5\.5\. This was caused by changes in the method of retrieving the `server_uuid`\. \(Bug \#22748612\) 
+  *Replication:* The GTID transaction skipping mechanism that silently skips a GTID transaction that was previously executed did not work properly for XA transactions\. \(Bug \#25041920\) 
+  [">XA ROLLBACK](https://dev.mysql.com/doc/refman/5.7/en/xa.html) statements that failed because an incorrect transaction ID was given, could be recorded in the binary log with the correct transaction ID, and could therefore be actioned by replication replicas\. A check is now made for the error situation before binary logging takes place, and failed XA `ROLLBACK` statements are not logged\. \(Bug \#26618925\) 
+  *Replication:* If a replica was set up using a [CHANGE MASTER TO](https://dev.mysql.com/doc/refman/5.7/en/change-master-to.html) statement that did not specify the source log file name and source log position, then shut down before [START SLAVE](https://dev.mysql.com/doc/refman/5.7/en/start-slave.html) was issued, then restarted with the option [–relay\-log\-recovery](https://dev.mysql.com/doc/refman/5.7/en/replication-options-replica.html#sysvar_relay_log_recovery)) set, replication did not start\. This happened because the receiver thread had not been started before relay log recovery was attempted, so no log rotation event was available in the relay log to provide the source log file name and source log position\. In this situation, the replica now skips relay log recovery and logs a warning, then proceeds to start replication\. \(Bug \#28996606, Bug \#93397\) 
+  *Replication:* In row\-based replication, a message that incorrectly displayed field lengths was returned when replicating from a table with a `utf8mb3` column to a table of the same definition where the column was defined with a `utf8mb4` character set\. \(Bug \#25135304, Bug \#83918\) 
+  *Replication:* When a [RESET SLAVE](https://dev.mysql.com/doc/refman/5.7/en/reset-slave.html) statement was issued on a replication replica with GTIDs in use, the existing relay log files were purged, but the replacement new relay log file was generated before the set of received GTIDs for the channel had been cleared\. The former GTID set was therefore written to the new relay log file as the `PREVIOUS_GTIDS` event, causing a fatal error in replication stating that the replica had more GTIDs than the source, even though the gtid\_executed set for both servers was empty\. Now, when `RESET SLAVE` is issued, the set of received GTIDs is cleared before the new relay log file is generated, so that this situation does not occur\. \(Bug \#27411175\) 
+  *Replication:* With GTIDs in use for replication, transactions including statements that caused a parsing error \([ER\_PARSE\_ERROR](https://dev.mysql.com/doc/mysql-errors/8.0/en/server-error-reference.html#error_er_parse_error)\) could not be skipped manually by the recommended method of injecting an empty or replacement transaction with the same GTID\. This action should result in the replica identifying the GTID as already used, and therefore skipping the unwanted transaction that shared its GTID\. However, in the case of a parsing error, because the statement was parsed before the GTID was checked to see if it needed to be skipped, the replication applier thread stopped due to the parsing error, even though the intention was for the transaction to be skipped anyway\. With this fix, the replication applier thread now ignores parsing errors if the transaction concerned needs to be skipped because the GTID was already used\. Note that this behavior change does not apply in the case of workloads consisting of binary log output produced by `mysqlbinlog`\. In that situation, there would be a risk that a transaction with a parsing error that immediately follows a skipped transaction would also be silently skipped, when it ought to raise an error\. \(Bug \#27638268\) 
+  *Replication:* Enable the SQL thread to GTID skip a partial transaction\. \(Bug \#25800025\) 
+  *Replication:* When a negative or fractional timeout parameter was supplied to `WAIT_UNTIL_SQL_THREAD_AFTER_GTIDS()`, the server behaved in unexpected ways\. With this fix: 
  +  A fractional timeout value is read as\-is, with no round\-off\. 
  +  A negative timeout value is rejected with an error if the server is on a strict SQL mode; if the server is not on a strict SQL mode, the value makes the function return `NULL` immediately without any waiting and then issue a warning\. \(Bug \#24976304, Bug \#83537\) 
+  *Replication:* If the `WAIT_FOR_EXECUTED_GTID_SET()` function was used with a timeout value including a fractional part \(for example, 1\.5\), an error in the casting logic meant that the timeout was rounded down to the nearest whole second, and to zero for values less than 1 second \(for example, 0\.1\)\. The casting logic has now been corrected so that the timeout value is applied as originally specified with no rounding\. Thanks to Dirkjan Bussink for the contribution\. \(Bug \#29324564, Bug \#94247\) 
+  With GTIDs enabled, [XA COMMIT](https://dev.mysql.com/doc/refman/5.7/en/xa.html) on a disconnected XA transaction within a multiple\-statement transaction raised an assertion\. \(Bug \#22173903\) 
+  *Replication:* An assertion was raised in debug builds if an [XA ROLLBACK](https://dev.mysql.com/doc/refman/5.7/en/xa.html) statement was issued for an unknown transaction identifier when the [gtid\_next](https://dev.mysql.com/doc/refman/5.7/en/replication-options-gtids.html#sysvar_gtid_next) value had been set manually\. The server now does not attempt to update the GTID state if an XA `ROLLBACK` statement fails with an error\. \(Bug \#27928837, Bug \#90640\) 
+  Fix wrong sorting order issue when multiple `CASE` functions are used in `ORDER BY` clause \(Bug\#22810883\)\. 

## Comparison with Aurora MySQL version 1<a name="AuroraMySQL.Updates.2100.Compare56"></a>

The following Amazon Aurora MySQL features are supported in Aurora MySQL version 1 \(compatible with MySQL 5\.6\), but these features are currently not supported in Aurora MySQL version 2 \(compatible with MySQL 5\.7\)\.
+ Asynchronous key prefetch \(AKP\)\. For more information, see [Optimizing Amazon Aurora indexed join queries with asynchronous key prefetch](AuroraMySQL.BestPractices.md#Aurora.BestPractices.AKP)\.
+ Hash joins\. For more information, see [Optimizing large Aurora MySQL join queries with hash joins](AuroraMySQL.BestPractices.md#Aurora.BestPractices.HashJoin)\.
+ Native functions for synchronously invoking AWS Lambda functions\. For more information, see [Invoking a Lambda function with an Aurora MySQL native function](AuroraMySQL.Integrating.Lambda.md#AuroraMySQL.Integrating.NativeLambda)\.
+ Scan batching\. For more information, see [Aurora MySQL database engine updates 2017\-12\-11](AuroraMySQL.Updates.20171211.md)\.
+ Migrating data from MySQL using an Amazon S3 bucket\. For more information, see [Migrating data from MySQL by using an Amazon S3 bucket](AuroraMySQL.Migrating.ExtMySQL.md#AuroraMySQL.Migrating.ExtMySQL.S3)\.

## MySQL 5\.7 compatibility<a name="AuroraMySQL.Updates.2100.Compatibility"></a>

This Aurora MySQL version is wire\-compatible with MySQL 5\.7 and includes features such as JSON support, spatial indexes, and generated columns\. Aurora MySQL uses a native implementation of spatial indexing using z\-order curves to deliver >20x better write performance and >10x better read performance than MySQL 5\.7 for spatial datasets\.

This Aurora MySQL version does not currently support the following MySQL 5\.7 features:
+ Group replication plugin
+ Increased page size
+ InnoDB buffer pool loading at startup
+ InnoDB full\-text parser plugin
+ Multisource replication
+ Online buffer pool resizing
+ Password validation plugin
+ Query rewrite plugins
+ Replication filtering
+ The `CREATE TABLESPACE` SQL statement