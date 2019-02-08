# Aurora MySQL Database Engine Updates 2019\-02\-07<a name="AuroraMySQL.Updates.2034"></a>

**Version:** 2\.03\.4

Aurora MySQL 2\.03\.4 is generally available\. Aurora MySQL 2\.x versions are compatible with MySQL 5\.7 and Aurora MySQL 1\.x versions are compatible with MySQL 5\.6\.

When creating a new Aurora MySQL DB cluster \(including restoring a snapshot\), you can choose compatibility with either MySQL 5\.7 or MySQL 5\.6\.

We don't allow in\-place upgrade of Aurora MySQL 1\.\* clusters into Aurora MySQL 2\.03\.4 or restore to Aurora MySQL 2\.03\.4 from an Amazon S3 backup\. We plan to remove these restrictions in a later Aurora MySQL 2\.\* release\.

If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB Cluster](USER_UpgradeDBInstance.Maintenance.md)\.

**Note**  
 This version is currently not available in the AWS GovCloud \(US\-West\) \[us\-gov\-west\-1\] and China \(Beijing\) \[cn\-north\-1\] regions\. There will be a separate announcement once it is made available\. 

**Note**  
The procedure to upgrade your DB cluster has changed\. For more information, see [Database Upgrades and Patches for Amazon Aurora MySQL](AuroraMySQL.Updates.md#AuroraMySQL.Updates.Patching)\.

## Improvements<a name="AuroraMySQL.Updates.2034.Improvements"></a>
+  Support for UTF8MB4 Unicode 9\.0 accent\-sensitive and case\-insensitive collation, `utf8mb4_0900_as_ci`\. 