# Aurora MySQL Database Engine Updates 2020\-09\-17 \(Version 2\.09\.0\)<a name="AuroraMySQL.Updates.2090"></a>

**Version:** 2\.09\.0

Aurora MySQL 2\.09\.0 is generally available\. Aurora MySQL 2\.x versions are compatible with MySQL 5\.7 and Aurora MySQL 1\.x versions are compatible with MySQL 5\.6\.

 Currently supported Aurora MySQL releases are 1\.14\.\*, 1\.15\.\*, 1\.16\.\*, 1\.17\.\*, 1\.18\.\*, 1\.19\.\*, 1\.20\.\*, 1\.21\.\*, 1\.22\.\*, 1\.23\.\*, 2\.01\.\*, 2\.02\.\*, 2\.03\.\*, 2\.04\.\*, 2\.05\.\*, 2\.06\.\*, 2\.07\.\*, 2\.08\.\*, and 2\.09\.\*\. 

 You can restore a snapshot from a currently supported Aurora MySQL release into Aurora MySQL 2\.09\.0\. You also have the option to upgrade existing Aurora MySQL 2\.\* database clusters to Aurora MySQL 2\.09\.0\. You can't upgrade an existing Aurora MySQL 1\.\* cluster directly to 2\.09\.0; however, you can restore its snapshot to Aurora MySQL 2\.09\.0\. 

 To create a cluster with an older version of Aurora MySQL, specify the engine version through the AWS Management Console, the AWS CLI, or the RDS API\. 

If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB Cluster](USER_UpgradeDBInstance.Maintenance.md)\.

**Note**  
For information on how to upgrade your Aurora MySQL database cluster, see [Database Upgrades and Patches for Amazon Aurora MySQL](AuroraMySQL.Updates.md#AuroraMySQL.Updates.Patching)\.

## Improvements<a name="AuroraMySQL.Updates.2090.Improvements"></a>

 **New features:** 
+  With this release, you can create Amazon Aurora MySQL database instances with up to 128 TiB of storage\. The new storage limit is an increase from the prior 64 TiB\. The 128 TiB storage size supports larger databases\. This capability is not supported on small instances sizes \(db\.t2 or db\.t3\)\. A single tablespace cannot grow beyond 64 TiB due to [InnoDB limitations with 16 KB page size](https://dev.mysql.com/doc/refman/5.7/en/innodb-limits.html)\. 

   Aurora alerts you when the cluster volume size is near 128 TiB, so that you can take action prior to hitting the size limit\. The alerts appear in the mysql log and RDS Events in the AWS Management Console\. 
+  You can now turn parallel query on or off for an existing cluster by changing the value of the DB cluster parameter `aurora_parallel_query`\. You don't need to use the `parallelquery` setting for the `--engine-mode` parameter when creating the cluster\. 

   Parallel query is now expanded to be available in all regions where Aurora MySQL is available\. 

   There are a number of other functionality enhancements and changes to the procedures for upgrading and enabling parallel query in an Aurora cluster\. For more information, see [Working with Parallel Query for Amazon Aurora MySQL](aurora-mysql-parallel-query.md)\. 

 **High priority fixes:** 
+  Backport of Community Bug \#27659490: SELECT USING DYNAMIC RANGE AND INDEX MERGE USE TOO MUCH MEMORY \(OOM\) 
+  Bug \#26881508: MYSQL \#1: DISABLE\_ABORT\_ON\_ERROR IN AUTH\_COMMON\.H 
+  Backport of Community Bug \#24437124: POSSIBLE BUFFER OVERFLOW ON CREATE TABLE 
+  Backport of Bug \#27158030: INNODB ONLINE ALTER CRASHES WITH CONCURRENT DML 
+  Bug \#29770705: SERVER CRASHED WHILE EXECUTING SELECT WITH SPECIFIC WHERE CLAUSE 
+  Backport of BUG \#26502135: MYSQLD SEGFAULTS IN MDL\_CONTEXT::TRY\_ACQUIRE\_LOCK\_IMPL 
+  Backport of Bug \#26935001: ALTER TABLE AUTO\_INCREMENT TRIES TO READ INDEX FROM DISCARDED TABLESPACE 
+  Bug \#28491099: \[FATAL\] MEMORY BLOCK IS INVALID \| INNODB: ASSERTION FAILURE: UT0UT\.CC:670 
+  Bug \#30499288: GCC 9\.2\.1 REPORTS A NEW WARNING FOR OS\_FILE\_GET\_PARENT\_DIR 
+  Bug \#29952565 where MYSQLD GOT SIGNAL 11 WHILE EXECUTING A QUERY\(UNION \+ ORDER BY \+ SUB\-QUERY\) 
+  Bug \#30628268: OUT OF MEMORY CRASH 
+  Bug \#30441969: BUG \#29723340: MYSQL SERVER CRASH AFTER SQL QUERY WITH DATA ?AST 
+  Bug \#30569003: 5\.7 REPLICATION BREAKAGE WITH SYNTAX ERROR WITH GRANT MANAGEMENT 
+  Bug \#29915479: RUNNING COM\_REGISTER\_SLAVE WITHOUT COM\_BINLOG\_DUMP CAN RESULTS IN SERVER EXIT 
+  Bug \#30569003: 5\.7 REPLICATION BREAKAGE WITH SYNTAX ERROR WITH GRANT MANAGEMENT 
+  Bug \#29915479: RUNNING COM\_REGISTER\_SLAVE WITHOUT COM\_BINLOG\_DUMP CAN RESULTS IN SERVER EXIT 
+  Bug \#20712046: SHOW PROCESSLIST AND PERFORMANCE\_SCHEMA TABLES DO NOT MASK PASSWORD FROM QUERY 
+  Backport bug \#18898433: EXTREMELY SLOW PERFORMANCE WITH OUTER JOINS AND JOIN BUFFER \(fixed in 5\.7\.21\)\. Queries with many left joins were slow if join buffering was used \(for example, using the block nested loop algorithm\)\. \(Bug \#18898433, Bug \#72854\)" 
+  Backport bug \#26402045: MYSQLD CRASHES ON QUERY \(fixed in MySQL 5\.7\.23\)\. Certain cases of subquery materialization could cause a server exit\. These queries now produce an error suggesting that materialization be disabled\. \(Bug \#26402045\) 
+  \[Backport from MySQL\] users other than rdsadmin is disallowed to update pfs table in the reader replica\. 
+  Fix the issue where the customer can not update the perfschema in the reader replica 
+  Bug \#26666274: INFINITE LOOP IN PERFORMANCE SCHEMA BUFFER CONTAINER 
+  [Bug \#26997096](https://github.com/mysql/mysql-server/commit/78f25d2809ad457e81f90342239c9bc32a36cdfa): relay\_log\_space value is not updated in a synchronized manner so that its value sometimes much higher than the actual disk space used by relay logs\. 
+  BUG \#25082593: FOREIGN KEY VALIDATION DOESN'T NEED TO ACQUIRE GAP LOCK IN READ COMMITTED 
+ [CVE\-2019\-2731](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-2731)
+ [CVE\-2018\-2645](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-2645)
+ [CVE\-2019\-2581](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-2581)
+ [CVE\-2018\-2787](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-2787)
+ [CVE\-2019\-2482](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-2482)
+ [CVE\-2018\-2640](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-2640)
+ [CVE\-2018\-2784](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-2784)
+ [CVE\-2019\-2628](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-2628)
+ [CVE\-2019\-2911](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-2911)
+ [CVE\-2019\-2628](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-2628)
+ [CVE\-2018\-3284](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-3284)
+ [CVE\-2018\-3065](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-3065)
+ [CVE\-2019\-2537](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-2537)
+ [CVE\-2019\-2948](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-2948)
+ [CVE\-2019\-2434](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-2434)
+ [CVE\-2019\-2420](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-2420)

 **Availability improvements:** 
+  Enable lock manager ABA fix by default\. 
+  Fixed an issue in the lock manager where a race condition can cause a lock to be shared by two transactions, causing the database to restart\. 
+  Fixed an issue when creating a temporary table with compressed row format might result in a restart\. 
+  Fix default value of `table_open_cache` on 16XL and 24XL instances which could cause repeated failovers and high CPU utilization on large instances classes \(R4/R5\-16XL, R5\-12XL, R5\-24XL\)\. This impacted 2\.07\.x\. 
+  Fixed an issue where restoring a cluster from Amazon S3 to Aurora MySQL version 2\.08\.0 takes longer than expected when the S3 backup didn't include the `mysql.host` table\. 
+  Fixed an issue which might cause repeated failovers due to updates of virtual columns with secondary indexes\. 
+  Fixed an issue related to transaction lock memory management with long\-running write transactions resulting in a database restart\. 
+  Fixed multiple issues where the the engine might crash during zero\-downtime patching while checking for safe point for patching\. 
+  Fixed an issue to skip redo logging for temporary tables, which was previously causing a crash\. 
+  Fixed a race condition in the lock manager between killing connection/query and the session killed\. 
+  Fixed an issue where the database could crash if it is a binlog replica and receives a DDL event over the MySQL `time_zone` table\. 

 **Global databases:** 
+  MySQL `INFORMATION_SCHEMA.REPLICA_HOST_STATUS` view in a secondary Region now shows the entries for the replicas belonging to that Region\. 
+  Fixed unexpected query failures that could occur in a Global DB secondary Region after temporary network connectivity issues between the primary and secondary Regions\. 

 **Parallel query:** 
+  Fixed the `EXPLAIN` plan for a Parallel Query query, which is incorrect for a simple single\-table query\. 
+  Fixed self\-deadlatch that may occur when Parallel Query is enabled\. 

 **General improvements:** 
+  Export to S3 now supports the `ENCRYPTION` keyword\. 
+  The `aurora_binlog_replication_max_yield_seconds` parameter now has a max value of 36,000\. The previous maximum accepted value was 45\. This parameter works only when the parameter `aurora_binlog_use_large_read_buffer` is set to 1\. 
+  Changed the behavior to map `MIXED` `binlog_format` to `ROW` instead of `STATEMENT` when executing `LOAD DATA FROM INFILE | S3`\. 
+  Fixed an issue where a binlog replica connected to an Aurora MySQL binlog primary might show incomplete data when the primary executed `LOAD DATA FROM S3` and `binlog_format` is set to `STATEMENT`\. 
+  Increased maximum allowable length for audit system variables `server_audit_incl_users` and `server_audit_excl_users` from 1024 bytes to 2000 bytes\. 
+  Fixed an issue where users may lose access to the database when lowering the `max_connections` parameter in the parameter group when the current connections is greater than the value being set\. 
+  Fixed an issue in Data Activity Streams where a single quote and backslash were not escaped properly\. 

## Integration of MySQL community edition bug fixes<a name="AuroraMySQL.Updates.2090.Patches"></a>
+  Bug \#27659490: SELECT USING DYNAMIC RANGE AND INDEX MERGE USE TOO MUCH MEMORY\(OOM\) 
+  Bug \#26881508: MYSQL \#1: DISABLE\_ABORT\_ON\_ERROR IN AUTH\_COMMON\.H 
+  Bug \#24437124: POSSIBLE BUFFER OVERFLOW ON CREATE TABLE 
+  Bug \#27158030: INNODB ONLINE ALTER CRASHES WITH CONCURRENT DML 
+  Bug \#29770705: SERVER CRASHED WHILE EXECUTING SELECT WITH SPECIFIC WHERE CLAUSE 
+  Bug \#26502135: MYSQLD SEGFAULTS IN MDL\_CONTEXT::TRY\_ACQUIRE\_LOCK\_IMPL 
+  Bug \#26935001: ALTER TABLE AUTO\_INCREMENT TRIES TO READ INDEX FROM DISCARDED TABLESPACE 
+  Bug \#28491099: \[FATAL\] MEMORY BLOCK IS INVALID \| INNODB: ASSERTION FAILURE: UT0UT\.CC:670 
+  Bug \#30499288: GCC 9\.2\.1 REPORTS A NEW WARNING FOR OS\_FILE\_GET\_PARENT\_DIR 
+  Bug \#29952565: where MYSQLD GOT SIGNAL 11 WHILE EXECUTING A QUERY\(UNION \+ ORDER BY \+ SUB\-QUERY\) 
+  Bug \#30628268: OUT OF MEMORY CRASH 
+  Bug \#30441969: BUG \#29723340: MYSQL SERVER CRASH AFTER SQL QUERY WITH DATA ?AST 
+  Bug \#30569003: 5\.7 REPLICATION BREAKAGE WITH SYNTAX ERROR WITH GRANT MANAGEMENT 
+  Bug \#29915479: RUNNING COM\_REGISTER\_SLAVE WITHOUT COM\_BINLOG\_DUMP CAN RESULTS IN SERVER EXIT 
+  Bug \#30569003: 5\.7 REPLICATION BREAKAGE WITH SYNTAX ERROR WITH GRANT MANAGEMENT 
+  Bug \#29915479: RUNNING COM\_REGISTER\_SLAVE WITHOUT COM\_BINLOG\_DUMP CAN RESULTS IN SERVER EXIT 
+  Bug \#20712046: SHOW PROCESSLIST AND PERFORMANCE\_SCHEMA TABLES DO NOT MASK PASSWORD FROM QUERY 
+  Bug \#18898433: EXTREMELY SLOW PERFORMANCE WITH OUTER JOINS AND JOIN BUFFER \(fixed in 5\.7\.21\) 
+  Bug \#26402045: MYSQLD CRASHES ON QUERY \(fixed in MySQL 5\.7\.23\) 
+  Bug \#23103937: PS\_TRUNCATE\_ALL\_TABLES\(\) DOES NOT WORK IN SUPER\_READ\_ONLY MODE 
+  Bug \#26666274: INFINITE LOOP IN PERFORMANCE SCHEMA BUFFER CONTAINER 
+  Bug \#26997096: relay\_log\_space value is not updated in a synchronized manner so that its value sometimes much higher than the actual disk space used by relay logs\. \([https://github.com/mysql/mysql-server/commit/78f25d2809ad457e81f90342239c9bc32a36cdfa](https://github.com/mysql/mysql-server/commit/78f25d2809ad457e81f90342239c9bc32a36cdfa)\) 
+  Bug \#25082593: FOREIGN KEY VALIDATION DOESN’T NEED TO ACQUIRE GAP LOCK IN READ COMMITTED 
+  Bug \#24764800: REPLICATION FAILING ON SLAVE WITH XAER\_RMFAIL ERROR\. 
+  Bug \#81441: WARNING ABOUT LOCALHOST WHEN USING SKIP\-NAME\-RESOLVE\. 