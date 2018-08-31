# Amazon Aurora MySQL Database Engine Updates 2018\-08\-14<a name="AuroraMySQL.Updates.1175"></a>

**Version:** 1\.17\.5

Amazon Aurora MySQL 1\.17\.5 is generally available\. All new Aurora MySQL database clusters with MySQL 5\.6 compatibility, including those restored from snapshots, will be created in Aurora MySQL 1\.17\.5\. You have the option, but are not required, to upgrade existing database clusters to Aurora MySQL 1\.17\.5\. To use an older version, you can create new database clusters in Aurora MySQL 1\.14\.4, 1\.15\.1, 1\.16, or 1\.17\.4\. You can do so using the AWS CLI or the Amazon RDS API and specifying the engine version\. 

With version 1\.17\.5 of Aurora MySQL, we are using a cluster patching model where all nodes in an Aurora DB cluster are patched at the same time\. 

Should you have any questions or concerns, the AWS Support Team is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB Cluster](USER_UpgradeDBInstance.Maintenance.md)\.

## Improvements<a name="AuroraMySQL.Updates.1175.Improvements"></a>
+  Fixed an issue where an Aurora Writer might experience a restart after an Aurora cluster is patched using the Zero\-Downtime Patching feature\. 