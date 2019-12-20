# Aurora MySQL Database Engine Updates 2018\-06\-05<a name="AuroraMySQL.Updates.1173"></a>

**Version:** 1\.17\.3

Aurora MySQL 1\.17\.3 is generally available\. All new Aurora MySQL database clusters with MySQL 5\.6 compatibility, including those restored from snapshots, will be created in Aurora MySQL 1\.17\.3\. You have the option, but are not required, to upgrade existing database clusters to Aurora MySQL 1\.17\.3\. You can create new database clusters in Aurora MySQL 1\.14\.4, Aurora MySQL 1\.15\.1, or Aurora MySQL 1\.16\. You can do so using the AWS CLI or the Amazon RDS API and specifying the engine version\. 

With version 1\.17\.3 of Aurora MySQL, we are using a cluster patching model where all nodes in an Aurora DB cluster are patched at the same time\. 

**Note**  
 This version is currently not available in the AWS GovCloud \(US\-West\) \[us\-gov\-west\-1\] and China \(Beijing\) \[cn\-north\-1\] regions\. There will be a separate announcement once it is made available\. 

If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB Cluster](USER_UpgradeDBInstance.Maintenance.md)\.

## Improvements<a name="AuroraMySQL.Updates.1173.Improvements"></a>
+ Fixed an issue where an Aurora Replica can restart when using optimistic cursor restores while reading records\.
+ Fixed an issue where an Aurora Writer restarts when trying to kill a MySQL session \(kill "*<session id>*"\) with performance schema enabled\.
+ Fixed an issue where an Aurora Writer restarts when computing a threshold for garbage collection\.
+ Fixed an issue where an Aurora Writer can occasionally restart when tracking Aurora Replica progress in log application\.
+ Fixed an issue with the Query Cache when auto\-commit is off and that could potentially cause stale reads\.