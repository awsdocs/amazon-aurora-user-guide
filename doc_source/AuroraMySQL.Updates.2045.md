# Aurora MySQL Database Engine Updates 2019\-07\-08 \(Version 2\.04\.5\)<a name="AuroraMySQL.Updates.2045"></a>

**Version:** 2\.04\.5

Aurora MySQL 2\.04\.5 is generally available\. Aurora MySQL 2\.x versions are compatible with MySQL 5\.7 and Aurora MySQL 1\.x versions are compatible with MySQL 5\.6\.

 You have the option to upgrade existing Aurora MySQL 2\.\* database clusters to Aurora MySQL 2\.04\.5\. We do not allow in\-place upgrade of Aurora MySQL 1\.\* clusters\. This restriction will be lifted in a later Aurora MySQL 2\.\* release\. You can restore snapshots of Aurora MySQL 1\.14\.\*, 1\.15\.\*, 1\.16\.\*, 1\.17\.\*, 1\.18\.\*, 1\.19\.\*, 2\.01\.\*, 2\.02\.\*, 2\.03\.\* and 2\.04\.\* into Aurora MySQL 2\.04\.5\. 

If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB Cluster](USER_UpgradeDBInstance.Maintenance.md)\.

**Note**  
For information on how to upgrade your Aurora MySQL database cluster, see [Database Upgrades and Patches for Amazon Aurora MySQL](AuroraMySQL.Updates.md#AuroraMySQL.Updates.Patching)\.

## Improvements<a name="AuroraMySQL.Updates.2045.Improvements"></a>
+  Fixed a race condition during storage volume growth that caused the database to restart\. 
+  Fixed an internal communication failure during volume open that caused the database to restart\. 
+  Added DDL recovery support for `ALTER TABLE ALGORITHM=INPLACE` on partitioned tables\. 
+  Fixed an issue with DDL recovery of `ALTER TABLE ALGORITHM=COPY` that caused the database to restart\. 
+  Improved Aurora Replica stability under heavy delete workload on the writer\. 
+  Fixed a database restart caused by a deadlatch between the thread performing full\-text search index sync and the thread performing eviction of full\-text search table from dictionary cache\. 
+  Fixed a stability issue on the binlog worker during DDL replication while the connection to the binlog master is unstable\. 
+  Fixed an out\-of\-memory issue in the full\-text search code that caused the database to restart\. 
+  Fixed an issue on the Aurora Writer that caused it to restart when the entire 64 TiB volume is used\. 
+  Fixed a race condition in the Performance Schema feature that caused the database to restart\. 
+  Fixed an issue that caused aborted connections when handling an error in network protocol management\. 

## Comparison with Aurora MySQL Version 1<a name="AuroraMySQL.Updates.2045.Compare56"></a>

The following Amazon Aurora MySQL features are supported in Aurora MySQL Version 1 \(compatible with MySQL 5\.6\), but these features are currently not supported in Aurora MySQL Version 2 \(compatible with MySQL 5\.7\)\.
+ Asynchronous key prefetch \(AKP\)\. For more information, see [Working with Asynchronous Key Prefetch in Amazon Aurora](AuroraMySQL.BestPractices.md#Aurora.BestPractices.AKP)\.
+ Hash joins\. For more information, see [Working with Hash Joins in Aurora MySQL](AuroraMySQL.BestPractices.md#Aurora.BestPractices.HashJoin)\.
+ Native functions for synchronously invoking AWS Lambda functions\. For more information, see [Invoking a Lambda Function with an Aurora MySQL Native Function](AuroraMySQL.Integrating.Lambda.md#AuroraMySQL.Integrating.NativeLambda)\.
+ Scan batching\. For more information, see [Aurora MySQL Database Engine Updates 2017\-12\-11](AuroraMySQL.Updates.20171211.md)\.
+ Migrating data from MySQL using an Amazon S3 bucket\. For more information, see [Migrating Data from MySQL by Using an Amazon S3 Bucket](AuroraMySQL.Migrating.ExtMySQL.md#AuroraMySQL.Migrating.ExtMySQL.S3)\.

## MySQL 5\.7 compatibility<a name="AuroraMySQL.Updates.2045.Compatibility"></a>

Aurora MySQL 2\.04\.5 is wire\-compatible with MySQL 5\.7 and includes features such as JSON support, spatial indexes, and generated columns\. Aurora MySQL uses a native implementation of spatial indexing using z\-order curves to deliver >20x better write performance and >10x better read performance than MySQL 5\.7 for spatial datasets\.

Aurora MySQL 2\.04\.5 does not currently support the following MySQL 5\.7 features:
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