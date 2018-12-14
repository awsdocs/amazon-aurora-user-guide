# Amazon Aurora MySQL Database Engine Updates 2018\-10\-24<a name="AuroraMySQL.Updates.2031"></a>

**Version:** 2\.03\.1

Amazon Aurora MySQL 2\.03\.1 is generally available\. Please note that Aurora MySQL 2\.x versions are compatible with MySQL 5\.7 and Aurora MySQL 1\.x versions are compatible with MySQL 5\.6\.

When creating a new Aurora MySQL DB cluster, you have the option of choosing compatibility with either MySQL 5\.7 or MySQL 5\.6\. When restoring a MySQL 5\.6\-compatible snapshot, you have the option of choosing compatibility with either MySQL 5\.7 or MySQL 5\.6\.

You can restore snapshots of Aurora MySQL 1\.14\.\*, 1\.15\.\*, 1\.16\.\*, 1\.17\.\*, 1\.18\.\*, 2\.01\.\*, 2\.02\.\*, and 2\.03 into Aurora MySQL 2\.03\.1\. In\-place upgrade to Aurora MySQL 2\.03\.1 is a 2\-step process because a fleet\-wide mandatory upgrade to 2\.02\.5 has been announced\. You first need to upgrade from Aurora MySQL 2\.01\.\* or 2\.02\.\* to Aurora MySQL 2\.02\.5 and then upgrade from Aurora MySQL 2\.02\.5 to Aurora MySQL 2\.03\.1\.

We do not allow in\-place upgrade of Aurora MySQL 1\.\* clusters into Aurora MySQL 2\.03\.1 or restore to Aurora MySQL 2\.03\.1 from an Amazon S3 backup\. We plan to remove these restrictions in a later Aurora MySQL 2\.\* release\.

Should you have any questions or concerns, the AWS Support Team is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB Cluster](USER_UpgradeDBInstance.Maintenance.md)\.

**Note**  
 This version is currently not available in the regions AWS GovCloud \(US\-West\) \[us\-gov\-west\-1\] and China \(Beijing\) \[cn\-north\-1\]\. There will be a separate announcement once it is made available\. 

## Improvements<a name="AuroraMySQL.Updates.2031.Improvements"></a>
+  Fix an issue where the Aurora Writer may restart when running transaction deadlock detection\. 