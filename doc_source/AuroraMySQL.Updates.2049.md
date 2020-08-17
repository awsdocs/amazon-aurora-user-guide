# Aurora MySQL Database Engine Updates 2020\-08\-14 \(Version 2\.04\.9\)<a name="AuroraMySQL.Updates.2049"></a>

**Version:** 2\.04\.9

Aurora MySQL 2\.04\.9 is generally available\. Aurora MySQL 2\.x versions are compatible with MySQL 5\.7 and Aurora MySQL 1\.x versions are compatible with MySQL 5\.6\.

 Currently supported Aurora MySQL releases are 1\.14\.\*, 1\.15\.\*, 1\.16\.\*, 1\.17\.\*, 1\.18\.\*, 1\.19\.\*, 2\.01\.\*, 2\.02\.\*, 2\.03\.\*, 2\.04\.\*, and 2\.05\.\*\. You can restore a snapshot of any 2\.\* Aurora MySQL release into Aurora MySQL 2\.04\.9\. You also have the option to upgrade existing Aurora MySQL 2\.\* database clusters to Aurora MySQL 2\.04\.9\. 

 To create a cluster with an older version of Aurora MySQL, please specify the engine version through the AWS Management Console, the AWS CLI, or the RDS API\. 

If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB Cluster](USER_UpgradeDBInstance.Maintenance.md)\.

**Note**  
 This version is currently not available in the following AWS Regions: AWS GovCloud \(US\-East\) \[us\-gov\-east\-1\], AWS GovCloud \(US\-West\) \[us\-gov\-west\-1\], Asia Pacific \(Hong Kong\) \[ap\-east\-1\], and Middle East \(Bahrain\) \[me\-south\-1\]\. There will be a separate announcement once it is made available\. 

**Note**  
For information on how to upgrade your Aurora MySQL database cluster, see [Database Upgrades and Patches for Amazon Aurora MySQL](AuroraMySQL.Updates.md#AuroraMySQL.Updates.Patching)\.

## Improvements<a name="AuroraMySQL.Updates.2049.Improvements"></a>

 **High\-priority fixes:** 

 **CVE fixes** 
+ [CVE\-2019\-2805](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-2805)
+ [CVE\-2019\-2730](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-2730)
+ [CVE\-2019\-2739](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-2739)
+ [CVE\-2019\-2778](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-2778)
+ [CVE\-2019\-2758](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-2758)
+ [CVE\-2018\-3064](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-3064)
+ [CVE\-2018\-3058](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-3058)
+ [CVE\-2018\-2786](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-2786)
+ [CVE\-2017\-3653](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-3653)
+ [CVE\-2017\-3455](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-3455)
+ [CVE\-2017\-3464](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-3464)
+ [CVE\-2017\-3465](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-3465)
+ [CVE\-2017\-3244](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-3244)
+ [CVE\-2016\-5612](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2016-5612)
+ [CVE\-2019\-2628](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-2628)
+ [CVE\-2019\-2740](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-2740)
+ [CVE\-2019\-2922](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-2922)
+ [CVE\-2019\-2923](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-2923)
+ [CVE\-2019\-2924](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-2924)
+ [CVE\-2019\-2910](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-2910)
+ [CVE\-2019\-5443](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-5443)
+ [CVE\-2019\-3822](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-3822)
+ [CVE\-2020\-2760](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-2760)
+ [CVE\-2019\-2911](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-2911)
+ [CVE\-2018\-2813](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-2813)

 **Availability improvements:** 
+  Fixed an issue that could cause a database restart or failover due to execution of a kill session command\. 
+  Fixed an issue that causes a database restart during execution of a complex query involving multi\-table joins and aggregation that use intermediate tables internally\. 
+  Fixed an issue that causes database restarts due to an interrupted `DROP TABLE` on multiple tables\. 
+  Fixed an issue that causes a database failover during database recovery\. 
+  Fixed a database restart caused by incorrect reporting of threads\_running when audit and slow query logs are enabled\. 
+  Fixed an issue where a `kill query` command might get stuck during execution\. 
+  Fixed a race condition in the lock manager that resulted in a database restart or failover during transaction rollback\. 
+  Fixed an issue that triggered database restart or failover when multiple connections are trying to update the same table with a Full\-Text Search index\. 
+  Fixed an issue that can cause a deadlatch when purging an index resulting in a failover or restart\. 

 **General improvements:** 
+  Fixed issues that could cause queries on read replicas to use data from an uncommitted transaction\. This issue is limited to the transactions that are started immediately after a database restart\. 
+  Fixed an issue encountered during `INPLACE ALTER TABLE` for a table with triggers defined and when the DDL did not contain a `RENAME` clause\. 
+  Fixed an issue that caused cloning to take longer on some database clusters with high writeload\. 
+  Fixed an issue encountered during an upgrade when a partitioned table has embedded spaces in the name\. 
+  Fixed an issue where the read replica might transiently see partial results of a recently committed transaction on the writer\. 
+  Fixed an issue where queries on a read replica against an FTS table may produce stale results\. This will only occur when the FTS query on the read replica closely follows a query on `INFORMATION_SCHEMA.INNODB_SYS_TABLES` for the same FTS table on the writer\. 
+  Fixed an issue that resulted in a slow restore of Aurora 1\.x database cluster containing FTS \(Full\-Text Search\) indexes to an Aurora 2\.x database cluster\. 
+  Extended maximum allowable length to 2000 for `server_audit_incl_users` and `server_audit_excl_users` global parameters\. 
+  Fixed an issue where Aurora 1\.x to Aurora 2\.x restore might take an extended time to complete\. 
+  Fixed an issue where a `lambda_async` invocation through stored procedure doesn't work with Unicode\. 
+  Fixed an issue encountered when a spatial index does not properly handle an off\-record geometry column\. 
+  Fixed an issue that might cause a query to fail on a reader DB instance with `InternalFailureException` error with message"Operation terminated \(internal error\)"\. 

## Integration of MySQL Bug Fixes<a name="AuroraMySQL.Updates.2049.BugFixes"></a>
+  Bug \#23070734, Bug \#80060: Concurrent TRUNCATE TABLEs cause stalls 
+  Bug \#23103937: PS\_TRUNCATE\_ALL\_TABLES\(\) DOES NOT WORK IN SUPER\_READ\_ONLY MODE 
+  Bug\#22551677: When taking the server offline, a race condition within the Performance Schema could lead to a server exit\. 
+  Bug \#27082268: Invalid FTS sync synchronization\. 
+  BUG \#12589870: Fixed an issues which causes a restart with multi\-query statement when query cache is enabled\. 
+  Bug \#26402045: Certain cases of subquery materialization could cause a server exit\. These queries now produce an error suggesting that materialization be disabled\. 
+  Bug \#18898433: Queries with many left joins were slow if join buffering was used \(for example, using the block nested loop algorithm\)\. 
+  Bug \#25222337: A NULL virtual column field name in a virtual index caused a server exit during a field name comparison that occurs while populating virtual columns affected by a foreign key constraint\. \([https://github.com/mysql/mysql-server/commit/273d5c9d7072c63b6c47dbef6963d7dc491d5131](https://github.com/mysql/mysql-server/commit/273d5c9d7072c63b6c47dbef6963d7dc491d5131)\) 
+  Bug \#25053286: Executing a stored procedure containing a query that accessed a view could allocate memory that was not freed until the session ended\. \([https://github.com/mysql/mysql-server/commit/d7b37d4d141a95f577916448650c429f0d6e193d](https://github.com/mysql/mysql-server/commit/d7b37d4d141a95f577916448650c429f0d6e193d)\) 
+  Bug \#25586773: Executing a stored procedure containing a statement that created a table from the contents of certain SELECT \(https://dev\.mysql\.com/doc/refman/5\.7/en/select\.html\) statements could result in a memory leak\. \([https://github.com/mysql/mysql-server/commit/88301e5adab65f6750f66af284be410c4369d0c1](https://github.com/mysql/mysql-server/commit/88301e5adab65f6750f66af284be410c4369d0c1)\) 
+  Bug \#26666274: INFINITE LOOP IN PERFORMANCE SCHEMA BUFFER CONTAINER\. 
+  Bug \#23550835, Bug \#23298025, Bug \#81464: A SELECT Performance Schema tables when an internal buffer was full could cause a server exit\. 

## Comparison with Aurora MySQL Version 1<a name="AuroraMySQL.Updates.2049.Compare56"></a>

The following Amazon Aurora MySQL features are supported in Aurora MySQL Version 1 \(compatible with MySQL 5\.6\), but these features are currently not supported in Aurora MySQL Version 2 \(compatible with MySQL 5\.7\)\.
+ Asynchronous key prefetch \(AKP\)\. For more information, see [Working with Asynchronous Key Prefetch in Amazon Aurora](AuroraMySQL.BestPractices.md#Aurora.BestPractices.AKP)\.
+ Hash joins\. For more information, see [Working with Hash Joins in Aurora MySQL](AuroraMySQL.BestPractices.md#Aurora.BestPractices.HashJoin)\.
+ Native functions for synchronously invoking AWS Lambda functions\. For more information, see [Invoking a Lambda Function with an Aurora MySQL Native Function](AuroraMySQL.Integrating.Lambda.md#AuroraMySQL.Integrating.NativeLambda)\.
+ Scan batching\. For more information, see [Aurora MySQL Database Engine Updates 2017\-12\-11](AuroraMySQL.Updates.20171211.md)\.
+ Migrating data from MySQL using an Amazon S3 bucket\. For more information, see [Migrating Data from MySQL by Using an Amazon S3 Bucket](AuroraMySQL.Migrating.ExtMySQL.md#AuroraMySQL.Migrating.ExtMySQL.S3)\.

## MySQL 5\.7 compatibility<a name="AuroraMySQL.Updates.2049.Compatibility"></a>

Aurora MySQL 2\.04\.9 is wire\-compatible with MySQL 5\.7 and includes features such as JSON support, spatial indexes, and generated columns\. Aurora MySQL uses a native implementation of spatial indexing using z\-order curves to deliver >20x better write performance and >10x better read performance than MySQL 5\.7 for spatial datasets\.

Aurora MySQL 2\.04\.9 does not currently support the following MySQL 5\.7 features:
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