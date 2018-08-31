# Amazon Aurora MySQL Database Engine Updates 2018\-08\-23<a name="AuroraMySQL.Updates.2023"></a>

**Version:** 2\.02\.3

Amazon Aurora MySQL 2\.02\.3 is generally available\. Please note that Aurora MySQL 2\.x versions are compatible with MySQL 5\.7 and Aurora MySQL 1\.x versions are compatible with MySQL 5\.6\.

When creating a new Aurora MySQL DB cluster, you have the option of choosing compatibility with either MySQL 5\.7 or MySQL 5\.6\. When restoring a MySQL 5\.6\-compatible snapshot, you have the option of choosing compatibility with either MySQL 5\.7 or MySQL 5\.6\.

You can restore snapshots of Aurora MySQL 1\.14\.\*, 1\.15\.\*, 1\.16\.\*, 1\.17\.\*, 2\.01\.\*, and 2\.02\.\* into Aurora MySQL 2\.02\.3\. You can also perform an in\-place upgrade from Aurora MySQL 2\.01\.\* or 2\.02\.\* to Aurora MySQL 2\.02\.3\.

We do not allow in\-place upgrade of Aurora MySQL 1\.\* clusters into Aurora MySQL 2\.02\.3 or restore to Aurora MySQL 2\.02\.3 from an Amazon S3 backup\. We plan to remove these restrictions in a later Aurora MySQL 2\.\* release\.

The performance schema is disabled for Aurora MySQL 5\.7\.

Should you have any questions or concerns, the AWS Support Team is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB Cluster](USER_UpgradeDBInstance.Maintenance.md)\.

## Improvements<a name="AuroraMySQL.Updates.2023.Improvements"></a>
+ Fixed an issue where an Aurora Replica can restart when using optimistic cursor restores while reading records\.
+ Updated the default value of the parameter `innodb_stats_persistent_sample_pages` to 128 to improve index statistics\.
+ Fixed an issue where an Aurora Replica may restart when it accesses a small table that is being concurrently modified on the Aurora primary instance\.
+ Fixed `ANALYZE TABLE` to stop flushing the table definition cache\.
+ Fixed an issue where the Aurora primary instance or an Aurora Replica may restart when converting a point query for geospatial to a search range\.