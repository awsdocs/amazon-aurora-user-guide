# Aurora MySQL Database Engine Updates 2019\-03\-25 \(Version 2\.04\)<a name="AuroraMySQL.Updates.204"></a>

**Version:** 2\.04

Aurora MySQL 2\.04 is generally available\. Aurora MySQL 2\.x versions are compatible with MySQL 5\.7 and Aurora MySQL 1\.x versions are compatible with MySQL 5\.6\.

When creating a new Aurora MySQL DB cluster \(including restoring a snapshot\), you have the option of choosing compatibility with either MySQL 5\.7 or MySQL 5\.6\. We do not allow in\-place upgrade of Aurora MySQL 1\.\* clusters or restore of Aurora MySQL 1\.\* clusters from an Amazon S3 backup into Aurora MySQL 2\.04\.0\. We plan to remove these restrictions in a later Aurora MySQL 2\.\* release\.

You can restore snapshots of Aurora MySQL 1\.19\.\*, 2\.01\.\*, 2\.02\.\*, and 2\.03\.\* into Aurora MySQL 2\.04\.0\. You cannot restore snapshots of Aurora MySQL 1\.14\.\* or lower, 1\.15\.\*, 1\.16\.\*, 1\.17\.\*, 1\.18\.\* into Aurora MySQL 2\.04\.0\. This restriction is removed in Aurora MySQL 2\.04\.1\.

If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB Cluster](USER_UpgradeDBInstance.Maintenance.md)\.

**Note**  
 This version is currently not available in the AWS GovCloud \(US\-West\) \[us\-gov\-west\-1\] region\. There will be a separate announcement once it is made available\. 

**Note**  
The procedure to upgrade your DB cluster has changed\. For more information, see [Database Upgrades and Patches for Amazon Aurora MySQL](AuroraMySQL.Updates.md#AuroraMySQL.Updates.Patching)\.

## Improvements<a name="AuroraMySQL.Updates.204.Improvements"></a>
+  Supports GTID\-based replication\. For information about using GTID\-based replication with Aurora MySQL, see [Using GTID\-Based Replication for Aurora MySQL](mysql-replication-gtid.md)\. 
+  Fixed an issue where an Aurora Replica incorrectly throws a`Running in read-only mode` error when a statement deleting or updating rows in a temporary table contains an InnoDB subquery\. 

## Integration of MySQL Bug Fixes<a name="AuroraMySQL.Updates.204.BugFixes"></a>
+  Bug \#26225783: MYSQL CRASH ON CREATE TABLE \(REPRODUCEABLE\) \-> INNODB: ALONG SEMAPHORE WAIT\. 