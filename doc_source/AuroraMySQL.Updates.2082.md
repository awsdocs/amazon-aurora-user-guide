# Aurora MySQL Database Engine Updates 2020\-08\-28 \(Version 2\.08\.2\)<a name="AuroraMySQL.Updates.2082"></a>

**Version:** 2\.08\.2

Aurora MySQL 2\.08\.2 is generally available\. Aurora MySQL 2\.x versions are compatible with MySQL 5\.7 and Aurora MySQL 1\.x versions are compatible with MySQL 5\.6\.

 Currently supported Aurora MySQL releases are 1\.14\.\*, 1\.15\.\*, 1\.16\.\*, 1\.17\.\*, 1\.18\.\*, 1\.19\.\*, 1\.20\.\*, 1\.21\.\*, 1\.22\.\*, 2\.01\.\*, 2\.02\.\*, 2\.03\.\*, 2\.04\.\*, 2\.05\.\*, 2\.06\.\*, 2\.07\.\*, and 2\.08\.\*\. 

 You can restore a snapshot from a currently supported Aurora MySQL release into Aurora MySQL 2\.08\.2\. You also have the option to upgrade existing Aurora MySQL 2\.\* database clusters to Aurora MySQL 2\.08\.2\. You can't upgrade an existing Aurora MySQL 1\.\* cluster directly to 2\.08\.2; however, you can restore its snapshot to Aurora MySQL 2\.08\.2\. See [Restoring from a DB Cluster Snapshot](USER_RestoreFromSnapshot.md) for more information about restoring snapshots\. 

 To create a cluster with an older version of Aurora MySQL, specify the engine version through the AWS Management Console, the AWS CLI, or the RDS API\. 

If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB Cluster](USER_UpgradeDBInstance.Maintenance.md)\.

**Note**  
For information on how to upgrade your Aurora MySQL database cluster, see [Database Upgrades and Patches for Amazon Aurora MySQL](AuroraMySQL.Updates.md#AuroraMySQL.Updates.Patching)\.

## Improvements<a name="AuroraMySQL.Updates.2082.Improvements"></a>

 **Critical fixes:** 
+  Fixed an issue that might cause an unplanned outage and affect database availability\. 

 **Availability fixes:** 
+ Fixed an issue where the Aurora MySQL database could restart if it is a binlog replica and replicates a DDL event over the mysql `time_zone` table\. 