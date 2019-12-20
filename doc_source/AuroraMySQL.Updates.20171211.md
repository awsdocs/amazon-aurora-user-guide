# Aurora MySQL Database Engine Updates 2017\-12\-11<a name="AuroraMySQL.Updates.20171211"></a>

**Version:** 1\.16

Aurora MySQL 1\.16 is generally available\. All new database clusters, including those restored from snapshots, will be created in Aurora 1\.16\. You have the option, but are not required, to upgrade existing database clusters to Aurora 1\.16\. You can create new DB clusters in Aurora 1\.14\.1 or Aurora 1\.15\.1\. You can do so using the AWS CLI or the Amazon RDS API and specifying the engine version\.

With version 1\.16 of Aurora, we are using a cluster patching model where all nodes in an Aurora DB cluster are patched at the same time\. We are enabling zero\-downtime patching, which works on a best\-effort basis to preserve client connections through the patching process\. For more information, see [Maintaining an Amazon Aurora DB Cluster](USER_UpgradeDBInstance.Maintenance.md)\.

If you have any questions or concerns, AWS Support is available on the community forums and through AWS Premium Support at [http://aws\.amazon\.com/support](http://aws.amazon.com/support)\.

## Zero\-Downtime Patching<a name="AuroraMySQL.Updates.20171211.ZDP"></a>

The zero\-downtime patching \(ZDP\) feature attempts, on a *best\-effort* basis, to preserve client connections through an engine patch\. For more information about ZDP, see [Zero\-Downtime Patching](AuroraMySQL.Updates.md#AuroraMySQL.Updates.ZDP)\. 

## New Features<a name="AuroraMySQL.Updates.20171211.New"></a>
+ Aurora MySQL now supports synchronous AWS Lambda invocations via the native function `lambda_sync()`\. Also available is native function `lambda_async()`, which can be used as an alternative to the existing stored procedure for asynchronous Lambda invocation\. For more information, see [Invoking a Lambda Function from an Amazon Aurora MySQL DB Cluster](AuroraMySQL.Integrating.Lambda.md)\.
+ Aurora MySQL now supports hash joins to speed up equijoin queries\. Aurora's cost\-based optimizer can automatically decide when to use hash joins; you can also force their use in a query plan\. For more information, see [Working with Hash Joins in Aurora MySQL](AuroraMySQL.BestPractices.md#Aurora.BestPractices.HashJoin)\.
+ Aurora MySQL now supports scan batching to speed up in\-memory scan\-oriented queries significantly\. The feature boosts the performance of table full scans, index full scans, and index range scans by batch processing\.

## Improvements<a name="AuroraMySQL.Updates.20171211.Improvements"></a>
+ Fixed an issue where read replicas crashed when running queries on tables that have just been dropped on the master\. 
+ Fixed an issue where restarting the writer on a database cluster with a very large number of `FULLTEXT` indexes results in longer than expected recovery\.
+ Fixed an issue where flushing binary logs causes `LOST_EVENTS` incidents in binlog events\.
+ Fixed stability issues with the scheduler when performance schema is enabled\.
+ Fixed an issue where a subquery that uses temporary tables could return partial results\.

## Integration of MySQL Bug Fixes<a name="AuroraMySQL.Updates.20171211.BugFixes"></a>

None