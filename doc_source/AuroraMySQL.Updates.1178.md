# Aurora MySQL Database Engine Updates 2019\-01\-17<a name="AuroraMySQL.Updates.1178"></a>

**Version:** 1\.17\.8

Aurora MySQL 1\.17\.8 is generally available\. All new Aurora MySQL database clusters with MySQL 5\.6 compatibility, including those restored from snapshots, will be created in Aurora MySQL 1\.17\.8\. You have the option, but are not required, to upgrade existing database clusters to Aurora MySQL 1\.17\.8\. To use an older version, you can create new database clusters in Aurora MySQL 1\.14\.4, 1\.15\.1, 1\.16, or 1\.17\.7\. You can do so using the AWS CLI or the Amazon RDS API and specifying the engine version\. 

With version 1\.17\.8 of Aurora MySQL, we are using a cluster patching model where all nodes in an Aurora DB cluster are patched at the same time\. 

**Note**  
 This version is currently not available in the AWS GovCloud \(US\-West\) \[us\-gov\-west\-1\] and China \(Beijing\) \[cn\-north\-1\] regions\. There will be a separate announcement once it is made available\. 

If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB Cluster](USER_UpgradeDBInstance.Maintenance.md)\.

## Improvements<a name="AuroraMySQL.Updates.1178.Improvements"></a>
+  Fixed a performance issue that increased the CPU utilization on an Aurora Replica after a restart\. 
+  Fixed a stability issue for `SELECT` queries that used hash join\. 

## Integration of MySQL community edition bug fixes<a name="AuroraMySQL.Updates.1178.Patches"></a>
+  BUG \#13418638: CREATE TABLE IF NOT EXISTS METADATA LOCK IS TOO RESTRICTIVE 