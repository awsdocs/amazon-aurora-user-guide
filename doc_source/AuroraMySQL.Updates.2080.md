# Aurora MySQL Database Engine Updates 2020\-06\-02 \(Version 2\.08\.0\)<a name="AuroraMySQL.Updates.2080"></a>

**Version:** 2\.08\.0

Aurora MySQL 2\.08\.0 is generally available\. Aurora MySQL 2\.x versions are compatible with MySQL 5\.7 and Aurora MySQL 1\.x versions are compatible with MySQL 5\.6\.

 Currently supported Aurora MySQL releases are 1\.14\.\*, 1\.15\.\*, 1\.16\.\*, 1\.17\.\*, 1\.18\.\*, 1\.19\.\*, 1\.20\.\*, 1\.21\.\*, 1\.22\.\*, 2\.01\.\*, 2\.02\.\*, 2\.03\.\*, 2\.04\.\*, 2\.05\.\*, 2\.06\.\*, 2\.07\.\*, and 2\.08\.\*\. 

 You can restore a snapshot from a currently supported Aurora MySQL release into Aurora MySQL 2\.08\.0\. You also have the option to upgrade existing Aurora MySQL 2\.\* database clusters to Aurora MySQL 2\.08\.0\. You can't upgrade an existing Aurora MySQL 1\.\* cluster directly to 2\.08\.0; however, you can restore its snapshot to Aurora MySQL 2\.08\.0\. 

 To create a cluster with an older version of Aurora MySQL, specify the engine version through the AWS Management Console, the AWS CLI, or the RDS API\. 

If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB Cluster](USER_UpgradeDBInstance.Maintenance.md)\.

**Note**  
For information on how to upgrade your Aurora MySQL database cluster, see [Database Upgrades and Patches for Amazon Aurora MySQL](AuroraMySQL.Updates.md#AuroraMySQL.Updates.Patching)\.

## Improvements<a name="AuroraMySQL.Updates.2080.Improvements"></a>

 **New features:** 
+  Improved binary log \(binlog\) processing to reduce crash recovery time and commit time latency when very large transactions are involved\.  
+  Launching Database Activity Streams \(DAS\) feature for Aurora MySQL\. This feature provides a near real\-time data stream of the database activity in your relational database to help you monitor activity\. For more information, see [Using Database Activity Streams with Amazon Aurora](DBActivityStreams.md)\. 
+  Updated timezone files to support the latest Brazil timezone change\. 
+  Introduced new keywords in SQL to exercise the hash join functionality for a specific table and/or inner table: `HASH_JOIN`, `HASH_JOIN_PROBING`, and `HASH_JOIN_BUILDING`\. For additional details, see [Aurora MySQL Hints](AuroraMySQL.Reference.md#AuroraMySQL.Reference.Hints)\. 
+  Introduced join order hint support in Aurora MySQL 5\.7 by backporting [a MySQL 8\.0 feature](https://github.com/mysql/mysql-server/commit/d2d91c3286b9ac3b95ef0e5036c5319aa4ffeda2#diff-4d30ba39ad1958c6f8148f67c94a896b)\. The new hints are `JOIN_FIXED_ORDER`, `JOIN_ORDER`, `JOIN_PREFIX`, and `JOIN_SUFFIX`\. For detailed documentation of join order hint support, see [WL\#9158: Join Order Hints](https://dev.mysql.com/worklog/task/?id=9158)\. 
+  Aurora Machine Learning now supports user\-defined functions with `MEDIUMINT` as the return type\. 
+  The `lambda_async()` stored procedure now supports all MySQL `utf8` characters\. 

 **High priority fixes:** 
+  Fixed an issue that could cause a reader DB instance to return incomplete results for an FTS query after the `INFORMATION_SCHEMA.INNODB_SYS_TABLES` table is queried on the writer DB instance\. 
+ [CVE\-2019\-5443](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-5443)
+ [CVE\-2019\-3822](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-3822)

 **Availability improvements:** 
+  Fixed an issue that resulted in a database restart after a multi\-query statement that accesses multiple tables or databases is executed with the query cache enabled\. 
+  Fixed a race condition in the lock manager that resulted in a database restart or failover during transaction rollback\. 
+  Fixed an issue that triggered database restart or failover when multiple connections are trying to update the same table with a Full\-Text Search index\. 
+  Fixed an issue that could trigger a database restart or failover during a `kill session` command\. 
+  Fixed an issue that caused reader DB instance to restart during a multi\-statement transaction with multiple `SELECT` statements and a heavy write workload on the writer DB instance with `AUTOCOMMIT` enabled\. 
+  Fixed an issue that caused reader DB instance to restart after executing long\-running queries while the writer DB instance is under a heavy OLTP write workload\. 

 **General improvements:** 
+  Improved database recovery time and commit latency for long running transactions when binlog is enabled\. 
+  Improved the algorithm to generate better statistics for estimating distinct value counts on indexed columns, including columns with skewed data distributions\. 
+  Reduced the response time and CPU utilization of join queries that access MyISAM temporary tables and the results spill to local storage\. 
+  Fixed an issue that prevented Aurora MySQL 5\.6 snapshots with database or table names containing spaces from being restored to a new Aurora MySQL 5\.7 cluster\. 
+  Included victim transaction info when deadlock is resolved in `show engine innodb status`\. 
+  Fixed an issue that caused connections to get stuck when clients of multiple different versions are connected to the same database and are accessing the query cache\. 
+  Fixed a memory leak resulting from multiple invocations of the Zero\-Downtime Patch \(ZDP\) or Zero\-Downtime Restart \(ZDR\) workflow throughout the lifetime of a database instance\. 
+  Fixed an error message in Zero\-Downtime Patch \(ZDP\) or Zero\-Downtime Restart \(ZDR\) operations wrongly stating that the last transaction was aborted if the auto\-commit flag is turned off\. 
+  Fixed an issue in Zero\-Downtime Patch \(ZDP\) operations that could lead to a server failure error message when restoring user session variables in the new database process\. 
+  Fixed an issue in Zero Downtime Patch \(ZDP\) operations that might cause intermittent database failures when there are long running queries during patching\. 
+  Fixed an issue where queries including an Aurora Machine Learning function returned empty error messages due to an incorrectly handled error response from Machine Learning services such as Amazon Sagemaker and Amazon Comprehend\. 
+  Fixed an issue in the out\-of\-memory monitoring functionality that did not honor a custom value of the `table_definition_cache` parameter\. 
+  The error message "Query execution was interrupted" is returned if an Aurora Machine Learning query is interrupted\. Previously, the generic message "Internal error in processing ML request" was returned instead\. 
+  Fixed an issue that could cause a binlog worker to experience a connection timeout when the `slave_net_timeout` parameter is less than the `aurora_binlog_replication_max_yield_seconds` parameter and there is low workload on the binlog master cluster\. 
+  Improved monitoring of the binlog recovery progress by outputting informational messages in the error log at a frequency of one message per minute\. 
+  Fixed an issue that could cause active transactions not to be reported by the `SHOW ENGINE INNODB STATUS` query\. 

## Integration of MySQL community edition bug fixes<a name="AuroraMySQL.Updates.2080.Patches"></a>
+  [Bug \#25289359](https://github.com/mysql/mysql-server/commit/64161c9abd50de7ba0b542bd4895881f6ead6531): A full\-text cache lock taken when data is synchronized was not released if the full\-text cache size exceeded the full\-text cache size limit\.  
+  [Bug \#29138644](https://github.com/mysql/mysql-server/commit/fbfd9fcd32afc11ba77d52fa0690aa26dcd64f72): Manually changing the system time while the MySQL server was running caused page cleaner thread delays\.  
+  [Bug \#25222337](https://github.com/mysql/mysql-server/commit/273d5c9d7072c63b6c47dbef6963d7dc491d5131): A NULL virtual column field name in a virtual index caused a server exit during a field name comparison that occurs while populating virtual columns affected by a foreign key constraint\.  
+  [Bug \#25053286](https://github.com/mysql/mysql-server/commit/d7b37d4d141a95f577916448650c429f0d6e193d): Executing a stored procedure containing a query that accessed a view could allocate memory that was not freed until the session ended\.  
+  [Bug \#25586773](https://github.com/mysql/mysql-server/commit/88301e5adab65f6750f66af284be410c4369d0c1): Executing a stored procedure containing a statement that created a table from the contents of certain [SELECT](https://dev.mysql.com/doc/refman/5.7/en/select.html)  statements could result in a memory leak\.  
+  [Bug \#28834208](https://github.com/mysql/mysql-server/commit/ca722bbb409209d683534846a90093c118bf8c5b): During log application, after an [OPTIMIZE TABLE](https://dev.mysql.com/doc/refman/5.7/en/optimize-table.html)  operation, InnoDB did not populate virtual columns before checking for virtual column index updates\.  
+  [Bug \#26666274](https://github.com/mysql/mysql-server/commit/bd87573bc159c849f34aa8293ec43ac053cbfda0): Infinite loop in performance schema buffer container due to 32\-bit unsigned integer overflow\. 