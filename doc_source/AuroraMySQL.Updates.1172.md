# Aurora MySQL Database Engine Updates 2018\-04\-27<a name="AuroraMySQL.Updates.1172"></a>

**Version:** 1\.17\.2

Aurora MySQL 1\.17\.2 is generally available\. All new Aurora MySQL database clusters with MySQL 5\.6 compatibility, including those restored from snapshots, will be created in Aurora MySQL 1\.17\.2\. You have the option, but are not required, to upgrade existing database clusters to Aurora MySQL 1\.17\.2\. You can create new database clusters in Aurora MySQL 1\.14\.4, Aurora MySQL 1\.15\.1, or Aurora MySQL 1\.16\. You can do so using the AWS CLI or the Amazon RDS API and specifying the engine version\. 

With version 1\.17\.2 of Aurora MySQL, we are using a cluster patching model where all nodes in an Aurora DB cluster are patched at the same time\. 

If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB Cluster](USER_UpgradeDBInstance.Maintenance.md)\.

## Improvements<a name="AuroraMySQL.Updates.1172.Improvements"></a>
+ Fixed an issue which was causing restarts during certain DDL partition operations\.
+ Fixed an issue which was causing support for invocation of AWS Lambda functions via native Aurora MySQL functions to be disabled\.
+ Fixed an issue with cache invalidation which was causing restarts on Aurora Replicas\.
+ Fixed an issue in lock manager which was causing restarts\.