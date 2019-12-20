# Aurora MySQL Database Engine Updates 2019\-03\-25 \(Version 2\.04\.1\)<a name="AuroraMySQL.Updates.2041"></a>

**Version:** 2\.04\.1

Aurora MySQL 2\.04\.1 is generally available\. Aurora MySQL 2\.x versions are compatible with MySQL 5\.7 and Aurora MySQL 1\.x versions are compatible with MySQL 5\.6\.

When creating a new Aurora MySQL DB cluster \(including restoring a snapshot\), you have the option of choosing compatibility with either MySQL 5\.7 or MySQL 5\.6\. We do not allow in\-place upgrade of Aurora MySQL 1\.\* clusters or restore of Aurora MySQL 1\.\* clusters from an Amazon S3 backup into Aurora MySQL 2\.04\.1\. We plan to remove these restrictions in a later Aurora MySQL 2\.\* release\.

You can restore snapshots of Aurora MySQL 1\.14\.\*, 1\.15\.\*, 1\.16\.\*, 1\.17\.\*, 1\.18\.\*, 1\.19\.\*, 2\.01\.\*, 2\.02\.\*, 2\.03\.\*, 2\.04\.0 into Aurora MySQL 2\.04\.1\.

If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB Cluster](USER_UpgradeDBInstance.Maintenance.md)\.

**Note**  
 This version is currently not available in the AWS GovCloud \(US\-West\) \[us\-gov\-west\-1\] region\. There will be a separate announcement once it is made available\. 

**Note**  
The procedure to upgrade your DB cluster has changed\. For more information, see [Database Upgrades and Patches for Amazon Aurora MySQL](AuroraMySQL.Updates.md#AuroraMySQL.Updates.Patching)\.

## Improvements<a name="AuroraMySQL.Updates.2041.Improvements"></a>
+  Fixed an issue where an Aurora MySQL 5\.6 snapshot for versions lower than 1\.16 could not be restored to the latest Aurora MySQL 5\.7 cluster\. 