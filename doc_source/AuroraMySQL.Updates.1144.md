# Aurora MySQL Database Engine Updates: 2018\-03\-13<a name="AuroraMySQL.Updates.1144"></a>

**Version:** 1\.14\.4

Aurora MySQL 1\.14\.4 is generally available\. You can create new DB clusters in Aurora 1\.14\.4, using the AWS CLI or the Amazon RDS API and specifying the engine version\. You have the option, but are not required, to upgrade existing 1\.14\.x DB clusters to Aurora 1\.14\.4\.

With version 1\.14\.4 of Aurora, we are using a cluster\-patching model where all nodes in an Aurora DB cluster are patched at the same time\. We support zero\-downtime patching, which works on a best\-effort basis to preserve client connections through the patching process\. For more information, see [Maintaining an Amazon Aurora DB Cluster](USER_UpgradeDBInstance.Maintenance.md)\.

If you have any questions or concerns, AWS Support is available on the community forums and through AWS Premium Support at [http://aws\.amazon\.com/support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB Cluster](USER_UpgradeDBInstance.Maintenance.md)\.

## Zero\-Downtime Patching<a name="AuroraMySQL.Updates.1144.ZDP"></a>

The zero\-downtime patching \(ZDP\) feature attempts, on a *best\-effort* basis, to preserve client connections through an engine patch\. For more information about ZDP, see [Zero\-Downtime Patching](AuroraMySQL.Updates.md#AuroraMySQL.Updates.ZDP)\. 

## New Features<a name="AuroraMySQL.Updates.1144.New"></a>
+ Aurora MySQL now supports db\.r4 instance classes\.

## Improvements<a name="AuroraMySQL.Updates.1144.Improvements"></a>
+ Fixed an issue where `LOST_EVENTS` were generated when writing large binlog events\.

## Integration of MySQL Bug Fixes<a name="AuroraMySQL.Updates.1144.BugFixes"></a>
+ Ignorable events don't work and are not tested \(Bug \#74683\)
+ NEW\->OLD ASSERT FAILURE 'GTID\_MODE > 0' \(Bug \#20436436\)