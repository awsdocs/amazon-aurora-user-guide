# Aurora MySQL Database Engine Updates 2020\-09\-02 \(Version 1\.23\.0\)<a name="AuroraMySQL.Updates.1230"></a>

**Version:** 1\.23\.0

Aurora MySQL 1\.23\.0 is generally available\. Aurora MySQL 1\.\* versions are compatible with MySQL 5\.6 and Aurora MySQL 2\.\* versions are compatible with MySQL 5\.7\.

 Currently supported Aurora MySQL releases are 1\.14\.\*, 1\.15\.\*, 1\.16\.\*, 1\.17\.\*, 1\.18\.\*, 1\.19\.\*, 1\.20\.\*, 1\.21\.\*, 1\.22\.\*, 1\.23\.\*, 2\.01\.\*, 2\.02\.\*, 2\.03\.\*, 2\.04\.\*, 2\.05\.\*, 2\.06\.\*, 2\.07\.\*, 2\.08\.\*, and 2\.09\.\*\. You can restore the snapshot of an Aurora MySQL 1\.\* database into Aurora MySQL 1\.23\.0\. 

 To create a cluster with an older version of Aurora MySQL, please specify the engine version through the RDS Console, the AWS CLI, or the Amazon RDS API\. 

**Note**  
 This version is currently not available in the following regions: AWS GovCloud \(US\-East\) \[us\-gov\-east\-1\], AWS GovCloud \(US\-West\) \[us\-gov\-west\-1\]\. There will be a separate announcement once it is made available\. 

 If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB Cluster](USER_UpgradeDBInstance.Maintenance.md)\. 

## Improvements<a name="AuroraMySQL.Updates.1230.Improvements"></a>

 **New features:** 
+  You can now turn parallel query on or off for an existing cluster by changing the value of the DB cluster parameter `aurora_parallel_query`\. You don't need to use the `parallelquery` setting for the `--engine-mode` parameter when creating the cluster\. 

   Parallel query is now expanded to be available in all regions where Aurora MySQL is available\. 

   There are a number of other functionality enhancements and changes to the procedures for upgrading and enabling parallel query in an Aurora cluster\. For more information, see [Working with Parallel Query for Amazon Aurora MySQL](aurora-mysql-parallel-query.md)\. 
+  With this release, you can create Amazon Aurora MySQL database instances with up to 128 TiB of storage\. The new storage limit is an increase from the prior 64 TiB\. The 128 TiB storage size supports larger databases\. This capability is not supported on small instances sizes \(db\.t2 or db\.t3\)\. A single tablespace cannot grow beyond 64 TiB due to [InnoDB limitations with 16 KB page size](https://dev.mysql.com/doc/refman/5.7/en/innodb-limits.html)\. 

   Aurora alerts you when the cluster volume size is near 128 TiB, so that you can take action prior to hitting the size limit\. The alerts appear in the mysql log and RDS Events in the AWS Management Console\. 
+  Improved binary log \(binlog\) processing to reduce crash recovery time and commit time latency when very large transactions are involved\. 

 **High priority fixes:** 
+ [CVE\-2019\-2911](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-2911)
+ [CVE\-2019\-2537](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-2537)
+ [CVE\-2018\-2787](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-2787)
+ [CVE\-2018\-2784](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-2784)
+ [CVE\-2018\-2645](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-2645)
+ [CVE\-2018\-2640](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-2640)

 **Availability improvements:** 
+  Fixed an issue in the lock manager where a race condition can cause a lock to be shared by two transactions, causing the database to restart\. 
+  Fixed an issue related to transaction lock memory management with long\-running write transactions resulting in a database restart\. 
+  Fixed a race condition in the lock manager that resulted in a database restart or failover during transaction rollback\. 
+  Fixed an issue during upgrade from 5\.6 to 5\.7 when the `innodb_file_format` changed on a table that had Fast DDL enabled\. 
+  Fixed multiple issues where the the engine might restart during zero\-downtime patching while checking for a quiesced point in database activity for patching\. 
+  Fixed an issue related to DDL recovery that impacts restart of the DB instance while recovering an interrupted `DROP TRIGGER` operation\. 
+  Fixed a bug that might cause unavailability of the database if a crash occurs during the execution of certain partitioning operations\. Specifically, an interrupted `ALTER TABLE` operation that modifies the type of partitioning or the number of partitions in a table\. 
+  Fix default value of `table_open_cache` on 16XL and 24XL instances which could cause repeated failovers and high CPU utilization on large instances classes \(R4/R5\-16XL, R5\-12XL, R5\-24XL\)\. This impacted 1\.21\.x and 1\.22\.x\. 

 **Global databases:** 
+  Populate missing data in the MySQL `INFORMATION_SCHEMA.REPLICA_HOST_STATUS` view on primary and secondary AWS Regions in an Aurora global database\. 
+  Fixed unexpected query failures that could occur in a Global DB secondary Region due to garbage collection of UNDO records in the primary Region, after temporary network connectivity issues between the primary and secondary Regions\. 

 **Parallel query:** 
+  Fixed an issue where parallel query might cause a long\-running query to return an empty result\. 
+  Fixed an issue where a query on a small table on the Aurora read replica might take more than one second\. 
+  Fixed an issue that might cause a restart when a parallel query and a DML statement are executing concurrently under a heavy workload\. 

 **General improvements:** 
+  Fixed an issue where queries using spatial index might return partial results if spatial index was created on tables with already existing large spatial values\. 
+  Increased maximum allowable length for audit system variables `server_audit_incl_users` and `server_audit_excl_users` from 1024 bytes to 2000 bytes\. 
+  Fixed an issue where a binlog replica connected to an Aurora MySQL binlog primary might show incomplete data when the Aurora MySQL binlog primary loads data from S3 under `statement` `binlog_format`\. 
+  Comply with community behavior to map `mixed` binlog\_format to `row` instead of `statement` for loading data\. 
+  Fixed an issue causing binlog replication to stop working when the user closes the connection and the session is using temporary tables\. 
+  Improved response time of a query involving MyISAM temporary tables\. 
+  Fix permission issue when binlog worker runs a native lambda function\. 
+  Fixed an issue on Aurora read replicas when trying to query or rotate the slow log or general log\. 
+  Fixed an issue that broke logical replication when the `binlog_checksum` parameter is set to different values on the master and the replica\. 
+  Fixed an issue where the read replica might transiently see partial results of a recently committed transaction on the writer\. 
+  Include transaction info of the rolled\-back transaction in `show engine innodb status` when a deadlock is resolved\. 

## Integration of MySQL community edition bug fixes<a name="AuroraMySQL.Updates.1230.Patches"></a>
+  Binlog events with `ALTER TABLE ADD COLUMN ALGORITHM=QUICK` will be rewritten as `ALGORITHM=DEFAULT` to be compatible with the community edition\. 
+  BUG \#22350047: IF CLIENT KILLED AFTER ROLLBACK TO SAVEPOINT PREVIOUS STMTS COMMITTED 
+  Bug \#29915479: RUNNING COM\_REGISTER\_SLAVE WITHOUT COM\_BINLOG\_DUMP CAN RESULTS IN SERVER EXIT 
+  Bug \#30441969: BUG \#29723340: MYSQL SERVER CRASH AFTER SQL QUERY WITH DATA ?AST 
+  Bug \#30628268: OUT OF MEMORY CRASH 
+  Bug \#27081349: UNEXPECTED BEHAVIOUR WHEN DELETE WITH SPATIAL FUNCTION 
+  Bug \#27230859: UNEXPECTED BEHAVIOUR WHILE HANDLING INVALID POLYGON" 
+  Bug \#27081349: UNEXPECTED BEHAVIOUR WHEN DELETE WITH SPATIAL" 
+  Bug \#26935001: ALTER TABLE AUTO\_INCREMENT TRIES TO READ INDEX FROM DISCARDED TABLESPACE 
+  Bug \#29770705: SERVER CRASHED WHILE EXECUTING SELECT WITH SPECIFIC WHERE CLAUSE 
+  Bug \#27659490: SELECT USING DYNAMIC RANGE AND INDEX MERGE USE TOO MUCH MEMORY\(OOM\) 
+  Bug \#24786290: REPLICATION BREAKS AFTER BUG \#74145 HAPPENS IN MASTER 
+  Bug \#27703912: EXCESSIVE MEMORY USAGE WITH MANY PREPARE 
+  Bug \#20527363: TRUNCATE TEMPORARY TABLE CRASH: \!DICT\_TF2\_FLAG\_IS\_SET\(TABLE, DICT\_TF2\_TEMPORARY\) 
+  Bug\#23103937 PS\_TRUNCATE\_ALL\_TABLES\(\) DOES NOT WORK IN SUPER\_READ\_ONLY MODE 
+  Bug \#25053286: USE VIEW WITH CONDITION IN PROCEDURE CAUSES INCORRECT BEHAVIOR \(fixed in 5\.6\.36\) 
+  Bug \#25586773: INCORRECT BEHAVIOR FOR CREATE TABLE SELECT IN A LOOP IN SP \(fixed in 5\.6\.39\) 
+  Bug \#27407480: AUTOMATIC\_SP\_PRIVILEGES REQUIRES NEED THE INSERT PRIVILEGES FOR MYSQL\.USER TABLE 
+  Bug \#26997096: `relay_log_space` value is not updated in a synchronized manner so that its value is sometimes much higher than the actual disk space used by relay logs\. 
+  Bug\#15831300 SLAVE\_TYPE\_CONVERSIONS=ALL\_NON\_LOSSY NOT WORKING AS EXPECTED 
+  SSL Bug backport Bug \#17087862, Bug \#20551271 
+  Bug \#16894092: PERFORMANCE REGRESSION IN 5\.6\.6\+ FOR INSERT INTO \.\.\. SELECT \.\.\. FROM \(fixed in 5\.6\.15\)\. 
+  Port a bug fix related to `SLAVE_TYPE_CONVERSIONS`\. 