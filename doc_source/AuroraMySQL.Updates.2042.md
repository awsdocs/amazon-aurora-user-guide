# Aurora MySQL Database Engine Updates 2019\-05\-02 \(Version 2\.04\.2\)<a name="AuroraMySQL.Updates.2042"></a>

**Version:** 2\.04\.2

Aurora MySQL 2\.04\.2 is generally available\. Aurora MySQL 2\.x versions are compatible with MySQL 5\.7 and Aurora MySQL 1\.x versions are compatible with MySQL 5\.6\.

When creating a new Aurora MySQL DB cluster \(including restoring a snapshot\), you have the option of choosing compatibility with either MySQL 5\.7 or MySQL 5\.6\. We do not allow in\-place upgrade of Aurora MySQL 1\.\* clusters or restore of Aurora MySQL 1\.\* clusters from an Amazon S3 backup into Aurora MySQL 2\.04\.2\. We plan to remove these restrictions in a later Aurora MySQL 2\.\* release\.

You can restore snapshots of Aurora MySQL 1\.14\.\*, 1\.15\.\*, 1\.16\.\*, 1\.17\.\*, 1\.18\.\*, 1\.19\.\*, 2\.01\.\*, 2\.02\.\*, 2\.03\.\*, 2\.04\.0, and 2\.04\.1 into Aurora MySQL 2\.04\.2\.

If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB Cluster](USER_UpgradeDBInstance.Maintenance.md)\.

**Note**  
 This version is currently not available in the AWS GovCloud \(US\-West\) \[us\-gov\-west\-1\] and China \(Ningxia\) \[cn\-northwest\-1\] AWS Regions\. There will be a separate announcement once it is made available\. 

**Note**  
For information on how to upgrade your Aurora MySQL database cluster, see [Database Upgrades and Patches for Amazon Aurora MySQL](AuroraMySQL.Updates.md#AuroraMySQL.Updates.Patching)\.

## Improvements<a name="AuroraMySQL.Updates.2042.Improvements"></a>
+  Added support for SSL binlog replication using custom certificates\. For information on using SSL binlog replication in Aurora MySQL, see [mysql\_rds\_import\_binlog\_ssl\_material](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/mysql_rds_import_binlog_ssl_material.html)\. 
+  Fixed a deadlatch on the Aurora primary instance that occurs when a table with a Full Text Search index is being optimized\. 
+  Fixed an issue on the Aurora Replicas where performance of certain queries using `SELECT(*)` could be impacted on tables that have secondary indexes\. 
+  Fixed a condition that resulted in Error 1032 being posted\. 
+  Improved the stability of Aurora Replicas by fixing multiple deadlatches\. 

## Integration of MySQL Bug Fixes<a name="AuroraMySQL.Updates.2042.BugFixes"></a>
+  Bug \#24829050 \- INDEX\_MERGE\_INTERSECTION OPTIMIZATION CAUSES WRONG QUERY RESULTS 

## Comparison with Aurora MySQL Version 1<a name="AuroraMySQL.Updates.2042.Compare56"></a>

The following Amazon Aurora MySQL features are supported in Aurora MySQL Version 1 \(compatible with MySQL 5\.6\), but these features are currently not supported in Aurora MySQL Version 2 \(compatible with MySQL 5\.7\)\.
+ Asynchronous key prefetch \(AKP\)\. For more information, see [Working with Asynchronous Key Prefetch in Amazon Aurora](AuroraMySQL.BestPractices.md#Aurora.BestPractices.AKP)\.
+ Hash joins\. For more information, see [Working with Hash Joins in Aurora MySQL](AuroraMySQL.BestPractices.md#Aurora.BestPractices.HashJoin)\.
+ Native functions for synchronously invoking AWS Lambda functions\. For more information, see [Invoking a Lambda Function with an Aurora MySQL Native Function](AuroraMySQL.Integrating.Lambda.md#AuroraMySQL.Integrating.NativeLambda)\.
+ Scan batching\. For more information, see [Aurora MySQL Database Engine Updates 2017\-12\-11](AuroraMySQL.Updates.20171211.md)\.
+ Migrating data from MySQL using an Amazon S3 bucket\. For more information, see [Migrating Data from MySQL by Using an Amazon S3 Bucket](AuroraMySQL.Migrating.ExtMySQL.md#AuroraMySQL.Migrating.ExtMySQL.S3)\.

## MySQL 5\.7 compatibility<a name="AuroraMySQL.Updates.2042.Compatibility"></a>

Aurora MySQL 2\.04\.2 is wire\-compatible with MySQL 5\.7 and includes features such as JSON support, spatial indexes, and generated columns\. Aurora MySQL uses a native implementation of spatial indexing using z\-order curves to deliver >20x better write performance and >10x better read performance than MySQL 5\.7 for spatial datasets\.

Aurora MySQL 2\.04\.2 does not currently support the following MySQL 5\.7 features:
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