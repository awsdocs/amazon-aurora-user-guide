# Aurora MySQL database engine updates 2018\-10\-11 \(version 2\.03\) \(deprecated\)<a name="AuroraMySQL.Updates.203"></a>

**Version:** 2\.03

Aurora MySQL 2\.03 is generally available\. Aurora MySQL 2\.x versions are compatible with MySQL 5\.7 and Aurora MySQL 1\.x versions are compatible with MySQL 5\.6\.

When creating a new Aurora MySQL DB cluster, you can choose compatibility with either MySQL 5\.7 or MySQL 5\.6\. When restoring a MySQL 5\.6\-compatible snapshot, you can choose compatibility with either MySQL 5\.7 or MySQL 5\.6\.

You can restore snapshots of Aurora MySQL 1\.14\.\*, 1\.15\.\*, 1\.16\.\*, 1\.17\.\*, 1\.18\.\*, 2\.01\.\*, and 2\.02\.\* into Aurora MySQL 2\.03\.

We don't allow in\-place upgrade of Aurora MySQL 1\.\* clusters into Aurora MySQL 2\.03 or restore to Aurora MySQL 2\.03 from an Amazon S3 backup\. We plan to remove these restrictions in a later Aurora MySQL 2\.\* release\.

**Note**  
 This version is currently not available in the AWS GovCloud \(US\-West\) \[us\-gov\-west\-1\] and China \(Beijing\) \[cn\-north\-1\] regions\. There will be a separate announcement once it is made available\. 

If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB cluster](USER_UpgradeDBInstance.Maintenance.md)\.

## Improvements<a name="AuroraMySQL.Updates.203.Improvements"></a>
+  Performance schema is available\. 
+  Fixed an issue where zombie sessions with killed state might consume more CPU\. 
+  Fixed a dead latch issue when a read\-only transaction is acquiring a lock on a record on the Aurora Writer\. 
+  Fixed an issue where the Aurora Replica without customer workload might have high CPU utilization\. 
+  Multiple fixes on issues that might cause the Aurora Replica or the Aurora writer to restart\. 
+  Added capability to skip diagnostic logging when the disk throughput limit is reached\. 
+  Fixed a memory leak issue when binlog is enabled on the Aurora Writer\. 

## Integration of MySQL community edition bug fixes<a name="AuroraMySQL.Updates.203.Patches"></a>
+  REVERSE SCAN ON A PARTITIONED TABLE DOES ICP \- ORDER BY DESC \(Bug \#24929748\)\. 
+  JSON\_OBJECT CREATES INVALID JSON CODE \(Bug\#26867509\)\. 
+  INSERTING LARGE JSON DATA TAKES AN INORDINATE AMOUNT OF TIME \(Bug \#22843444\)\. 
+  PARTITIONED TABLES USE MORE MEMORY IN 5\.7 THAN 5\.6 \(Bug \#25080442\)\. 

## Comparison with Aurora MySQL version 1<a name="AuroraMySQL.Updates.203.Compare56"></a>

The following Amazon Aurora MySQL features are supported in Aurora MySQL version 1 \(compatible with MySQL 5\.6\), but these features are currently not supported in Aurora MySQL version 2 \(compatible with MySQL 5\.7\)\.
+ Asynchronous key prefetch \(AKP\)\. For more information, see [Optimizing Amazon Aurora indexed join queries with asynchronous key prefetch](AuroraMySQL.BestPractices.md#Aurora.BestPractices.AKP)\.
+ Hash joins\. For more information, see [Optimizing large Aurora MySQL join queries with hash joins](AuroraMySQL.BestPractices.md#Aurora.BestPractices.HashJoin)\.
+ Native functions for synchronously invoking AWS Lambda functions\. These functions are available for MySQL 5\.7\-compatible clusters in Aurora MySQL 2\.06 and higher\. For more information, see [Invoking a Lambda function with an Aurora MySQL native function](AuroraMySQL.Integrating.Lambda.md#AuroraMySQL.Integrating.NativeLambda)\.
+ Scan batching\. For more information, see [Aurora MySQL database engine updates 2017\-12\-11 \(version 1\.16\) \(deprecated\)](AuroraMySQL.Updates.20171211.md)\.
+ Migrating data from MySQL using an Amazon S3 bucket\. For more information, see [Migrating data from MySQL by using an Amazon S3 bucket](AuroraMySQL.Migrating.ExtMySQL.md#AuroraMySQL.Migrating.ExtMySQL.S3)\.

## MySQL 5\.7 compatibility<a name="AuroraMySQL.Updates.203.Compatibility"></a>

Aurora MySQL 2\.03 is wire\-compatible with MySQL 5\.7 and includes features such as JSON support, spatial indexes, and generated columns\. Aurora MySQL uses a native implementation of spatial indexing using z\-order curves to deliver >20x better write performance and >10x better read performance than MySQL 5\.7 for spatial datasets\.

Aurora MySQL 2\.03 does not currently support the following MySQL 5\.7 features:
+ Global transaction identifiers \(GTIDs\)\. Aurora MySQL supports GTIDs in version 2\.04 and higher\.
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
+ X Protocol