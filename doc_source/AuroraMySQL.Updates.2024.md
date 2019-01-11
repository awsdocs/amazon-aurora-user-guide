# Aurora MySQL Database Engine Updates 2018\-09\-21<a name="AuroraMySQL.Updates.2024"></a>

**Version:** 2\.02\.4

Aurora MySQL 2\.02\.4 is generally available\. Aurora MySQL 2\.x versions are compatible with MySQL 5\.7 and Aurora MySQL 1\.x versions are compatible with MySQL 5\.6\.

When creating a new Aurora MySQL DB cluster, you can choose compatibility with either MySQL 5\.7 or MySQL 5\.6\. When restoring a MySQL 5\.6\-compatible snapshot, you can choose compatibility with either MySQL 5\.7 or MySQL 5\.6\.

You can restore snapshots of Aurora MySQL 1\.14\.\*, 1\.15\.\*, 1\.16\.\*, 1\.17\.\*, 1\.18\.\*, 2\.01\.\*, and 2\.02\.\* into Aurora MySQL 2\.02\.4\. You can also perform an in\-place upgrade from Aurora MySQL 2\.01\.\* or 2\.02\.\* to Aurora MySQL 2\.02\.4\.

We don't allow in\-place upgrade of Aurora MySQL 1\.\* clusters into Aurora MySQL 2\.02\.4 or restore to Aurora MySQL 2\.02\.4 from an Amazon S3 backup\. We plan to remove these restrictions in a later Aurora MySQL 2\.\* release\.

The performance schema is disabled for this release of Aurora MySQL 5\.7\. Upgrade to Aurora 2\.03 for performance schema support\.

If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB Cluster](USER_UpgradeDBInstance.Maintenance.md)\.

## Improvements<a name="AuroraMySQL.Updates.2024.Improvements"></a>
+ Fixed a stability issue related to Full Text Search indexes on tables restored from an Aurora MySQL 5\.6 snapshot\.