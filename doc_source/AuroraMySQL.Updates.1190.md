# Aurora MySQL Database Engine Updates 2019\-02\-07 \(Version 1\.19\.0\)<a name="AuroraMySQL.Updates.1190"></a>

**Version:** 1\.19\.0

 Aurora MySQL 1\.19\.0 is generally available\. All new Aurora MySQL database clusters with MySQL 5\.6 compatibility, including those restored from snapshots, can be created with 1\.17\.8 or 1\.19\.0\. You have the option, but are not required, to upgrade existing database clusters to Aurora MySQL 1\.19\.0\. To use an older version, you can create new database clusters in Aurora MySQL 1\.14\.4, Aurora MySQL 1\.15\.1, Aurora MySQL 1\.16, Aurora MySQL 1\.17\.8, or Aurora MySQL 1\.18\.0\. You can do so using the AWS CLI or the Amazon RDS API and specifying the engine version\. 

If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB Cluster](USER_UpgradeDBInstance.Maintenance.md)\.

**Note**  
 This version is currently not available in the AWS GovCloud \(US\-West\) \[us\-gov\-west\-1\] and China \(Beijing\) \[cn\-north\-1\] regions\. There will be a separate announcement once it is made available\. 

**Note**  
The procedure to upgrade your DB cluster has changed\. For more information, see [Database Upgrades and Patches for Amazon Aurora MySQL](AuroraMySQL.Updates.md#AuroraMySQL.Updates.Patching)\.

## Features<a name="AuroraMySQL.Updates.1190.Features"></a>
+  **Aurora Version Selector** \- Starting with Aurora MySQL 1\.19\.0, you can choose from among multiple versions of MySQL 5\.6 compatible Aurora on the Amazon RDS console\. For more information, see [Aurora MySQL Engine Versions](AuroraMySQL.Updates.md#AuroraMySQL.Updates.EngineVersions)\. 

## Improvements<a name="AuroraMySQL.Updates.1190.Improvements"></a>
+  Fixed a stability issue related to the `CHECK TABLE` query on an Aurora Replica\. 
+  Introduced a new global user variable `aurora_disable_hash_join` to disable Hash Join\. 
+  Fixed a stability issue when generating the output row during multiple table hash join\. 
+  Fixed an issue that returned a wrong result because of a plan change during Hash Join applicability check\. 
+  Zero Downtime Patching is supported with long running transactions\. This enhancement will come into effect when upgrading from version 1\.19 to a higher one\. 
+  Zero Downtime Patching is now supported when binlog is enabled\. This enhancement will come into effect when upgrading from version 1\.19 to a higher one\. 
+  Fixed an issue that caused a spike in CPU utilization on the Aurora Replica unrelated to the workload\. 
+  Fixed a race condition in the lock manager that resulted in a database restart\. 
+  Fixed a race condition in the lock manager component to improve stability of Aurora instances\. 
+  Improved stability of the deadlock detector inside the lock manager component\. 
+  `INSERT` operation on a table is prohibited if InnoDB detects that the index is corrupted\. 
+  Fixed a stability issue in Fast DDL\. 
+  Improved Aurora stability by reducing the memory consumption in scan batching for single\-row subquery\. 
+  Fixed a stability issue that occurred after a foreign key was dropped while the system variable `foreign_key_checks` is set to 0\. 
+  Fixed an issue in the Out Of Memory Avoidance feature that erroneously overrode changes to the `table_definition_cache` value made by the user\. 
+  Fixed stability issues in the Out Of Memory Avoidance feature\. 
+  Fixed an issue that set `query_time` and `lock_time` in `slow_query_log` to garbage values\. 
+  Fixed a parallel query stability issue triggered by improper handling of string collation internally\. 
+  Fixed a parallel query stability issue triggered by a secondary index search\. 
+  Fixed a parallel query stability issue triggered by a multi\-table update\. 

## Integration of MySQL community edition bug fixes<a name="AuroraMySQL.Updates.1190.Patches"></a>
+  BUG \#32917: DETECT ORPHAN TEMP\-POOL FILES, AND HANDLE GRACEFULLY 
+  BUG \#63144 CREATE TABLE IF NOT EXISTS METADATA LOCK IS TOO RESTRICTIVE 