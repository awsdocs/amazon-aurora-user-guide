# Aurora MySQL Database Engine Updates 2019\-11\-22 \(Version 2\.06\.0\)<a name="AuroraMySQL.Updates.2060"></a>

**Version:** 2\.06\.0

Aurora MySQL 2\.06\.0 is generally available\. Aurora MySQL 2\.x versions are compatible with MySQL 5\.7 and Aurora MySQL 1\.x versions are compatible with MySQL 5\.6\.

 Currently supported Aurora MySQL releases are 1\.14\.\*, 1\.15\.\*, 1\.16\.\*, 1\.17\.\*, 1\.18\.\*, 1\.19\.\*, 2\.01\.\*, 2\.02\.\*, 2\.03\.\*, 2\.04\.\*, 2\.05\.\*, and 2\.06\.\*\. 

 You can restore a snapshot from a currently supported Aurora MySQL release into Aurora MySQL 2\.06\.0\. You also have the option to upgrade existing Aurora MySQL 2\.\* database clusters to Aurora MySQL 2\.06\.0\. You cannot upgrade an existing Aurora MySQL 1\.\* cluster directly to 2\.06\.0; however, you can restore its snapshot to Aurora MySQL 2\.06\.0\. 

 To create a cluster with an older version of Aurora MySQL, please specify the engine version through the AWS Management Console, the AWS CLI, or the RDS API\. 

**Note**  
 This version is currently not available in the following AWS Regions: AWS GovCloud \(US\-East\) \[us\-gov\-east\-1\], AWS GovCloud \(US\-West\) \[us\-gov\-west\-1\], China \(Ningxia\) \[cn\-northwest\-1\], Asia Pacific \(Hong Kong\) \[ap\-east\-1\], and Middle East \(Bahrain\) \[me\-south\-1\]\. There will be a separate announcement once it is made available\. 

If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB Cluster](USER_UpgradeDBInstance.Maintenance.md)\.

**Note**  
For information on how to upgrade your Aurora MySQL database cluster, see [Database Upgrades and Patches for Amazon Aurora MySQL](AuroraMySQL.Updates.md#AuroraMySQL.Updates.Patching)\.

## Improvements<a name="AuroraMySQL.Updates.2060.Improvements"></a>

 **New features:** 
+  Aurora MySQL clusters now support the instance types db\.r5\.8xlarge, db\.r5\.16xlarge, and db\.r5\.24xlarge\. For more information about instance types for Aurora MySQL clusters, see [DB Instance Classes](Concepts.DBInstanceClass.md)\. 
+  The hash join feature is now generally available and does not require the Aurora lab mode setting to be ON\. This feature can improve query performance when you need to join a large amount of data by using an equi\-join\. For more information about using this feature, see [Working with Hash Joins in Aurora MySQL](AuroraMySQL.BestPractices.md#Aurora.BestPractices.HashJoin)\. 
+  The hot row contention feature is now generally available and does not require the Aurora lab mode setting to be ON\. This feature substantially improves throughput for workloads with many transactions contending for rows on the same page\. 
+  Aurora MySQL 2\.06 and higher support "rewinding" a DB cluster to a specific time, without restoring data from a backup\. This feature, known as Backtrack, provides a quick way to recover from user errors, such as dropping the wrong table or deleting the wrong row\. Backtrack completes within seconds, even for large databases\. Read [the AWS blog](https://aws.amazon.com/blogs/aws/amazon-aurora-backtrack-turn-back-time/) for an overview, and refer to [Backtracking an Aurora DB Cluster](AuroraMySQL.Managing.Backtrack.md) for more details\. 
+  Aurora 2\.06 and higher support synchronous AWS Lambda invocations through the native function `lambda_sync()`\. Also available is native function `lambda_async()`, which can be used as an alternative to the existing stored procedure for asynchronous Lambda invocation\. For information about calling Lambda functions, see [Invoking a Lambda Function from an Amazon Aurora MySQL DB Cluster](AuroraMySQL.Integrating.Lambda.md)\. 

 **Critical fixes:** 

 None\. 

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
+ [CVE\-2017\-3465](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-3465)
+ [CVE\-2017\-3244](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-3244)
+ [CVE\-2016\-5612](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2016-5612)

 **Connection handling** 
+  Database availability has been improved to better service a surge in client connections while executing one or more DDLs\. It is handled by temporarily creating additional threads when needed\. You are advised to upgrade if the database becomes unresponsive following a surge in connections while processing DDL\. 

 **Engine restart** 
+  Fixed an issue of prolonged unavailability while restarting the engine\. This addresses an issue in the buffer pool initialization\. This issue occurs rarely but can potentially impact any supported release\. 
+  Fixed an issue that causes a database configured as a binlog master to restart while a heavy write workload is running\. 

 **General stability fixes:** 
+  Made improvements where queries accessing uncached data could be slower than usual\. Customers experiencing unexplained elevated read latency while accessing uncached data are encouraged to upgrade as they may be experiencing this issue\. 
+  Fixed an issue that failed to restore partitioned tables from a database snapshot\. Customers who encounter errors when accessing partitioned tables in a database that has been restored from the snapshot of an Aurora MySQL 1\.\* database are advised to use this version\. 
+  Improved stability of the Aurora Replicas by fixing lock contention between threads serving read queries and the one applying schema changes while a DDL query is in progress on the writer DB instance\. 
+  Fixed a stability issue related to `mysql.innodb_table_stats` table update triggered by DDL operations\. 
+  Fixed an issue that incorrectly reported `ERROR 1836` when a nested query is executed against a temporary table on the Aurora Replica\. 

 **Performance enhancements:** 
+  Improved performance of binlog replication by preventing unnecessary API calls to the cache if the query cache has been disabled on the binlog worker\. 

## Comparison with Aurora MySQL Version 1<a name="AuroraMySQL.Updates.2060.Compare56"></a>

The following Amazon Aurora MySQL features are supported in Aurora MySQL Version 1 \(compatible with MySQL 5\.6\), but these features are currently not supported in Aurora MySQL Version 2 \(compatible with MySQL 5\.7\)\.
+ Asynchronous key prefetch \(AKP\)\. For more information, see [Working with Asynchronous Key Prefetch in Amazon Aurora](AuroraMySQL.BestPractices.md#Aurora.BestPractices.AKP)\.
+ Scan batching\. For more information, see [Aurora MySQL Database Engine Updates 2017\-12\-11](AuroraMySQL.Updates.20171211.md)\.
+ Migrating data from MySQL using an Amazon S3 bucket\. For more information, see [Migrating Data from MySQL by Using an Amazon S3 Bucket](AuroraMySQL.Migrating.ExtMySQL.md#AuroraMySQL.Migrating.ExtMySQL.S3)\.

## MySQL 5\.7 compatibility<a name="AuroraMySQL.Updates.2060.Compatibility"></a>

Aurora MySQL 2\.06\.0 is wire\-compatible with MySQL 5\.7 and includes features such as JSON support, spatial indexes, and generated columns\. Aurora MySQL uses a native implementation of spatial indexing using z\-order curves to deliver >20x better write performance and >10x better read performance than MySQL 5\.7 for spatial datasets\.

Aurora MySQL 2\.06\.0 does not currently support the following MySQL 5\.7 features:
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