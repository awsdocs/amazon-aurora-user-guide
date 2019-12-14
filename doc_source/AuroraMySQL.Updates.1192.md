# Aurora MySQL Database Engine Updates 2019\-06\-05 \(Version 1\.19\.2\)<a name="AuroraMySQL.Updates.1192"></a>

**Version:** 1\.19\.2

 Aurora MySQL 1\.19\.2 is generally available\. All new Aurora MySQL database clusters with MySQL 5\.6 compatibility, including those restored from snapshots, can be created with 1\.17\.8, 1\.19\.0, 1\.19\.1, or 1\.19\.2\. You have the option, but are not required, to upgrade existing database clusters to Aurora MySQL 1\.19\.2\. To use an older version, you can create new database clusters in Aurora MySQL 1\.14\.4, Aurora MySQL 1\.15\.1, Aurora MySQL 1\.16, Aurora MySQL 1\.17\.8, or Aurora MySQL 1\.18\. You can do so using the AWS CLI or the Amazon RDS API and specifying the engine version\. 

 If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB Cluster](USER_UpgradeDBInstance.Maintenance.md)\. 

**Note**  
 This version is currently not available in the AWS GovCloud \(US\-West\) \[us\-gov\-west\-1\], Europe \(Stockholm\) \[eu\-north\-1\], China \(Ningxia\) \[cn\-northwest\-1\], and Asia Pacific \(Hong Kong\) \[ap\-east\-1\] AWS Regions\. There will be a separate announcement once it is made available\. 

**Note**  
The procedure to upgrade your DB cluster has changed\. For more information, see [Database Upgrades and Patches for Amazon Aurora MySQL](AuroraMySQL.Updates.md#AuroraMySQL.Updates.Patching)\.

## Improvements<a name="AuroraMySQL.Updates.1192.Improvements"></a>
+  Fixed an issue that could cause failures when loading data into Aurora from Amazon S3\. 
+  Fixed an issue that could cause failures when uploading data from Aurora to Amazon S3\. 
+  Fixed an issue that created zombie sessions left in a killed state\. 
+  Fixed an issue that caused aborted connections when handling an error in network protocol management\. 
+  Fixed an issue that could cause a crash when dealing with partitioned tables\. 
+  Fixed an issue related to binlog replication of trigger creation\. 