# Amazon Aurora MySQL Database Engine Updates 2018\-03\-13<a name="AuroraMySQL.Updates.117"></a>

**Version:** 1\.17

Amazon Aurora 1\.17 is generally available\. Please note that 1\.x versions are only compatible with MySQL 5\.6, and not MySQL 5\.7\. All new 5\.6\-compatible database clusters, including those restored from snapshots, will be created in Aurora v1\.17\. You have the option, but are not required, to upgrade existing database clusters to Aurora v1\.17\. If you wish to create new DB clusters in Aurora v1\.14\.1, Aurora 1\.15\.1, or Aurora 1\.16, you can do so using the AWS CLI or the Amazon RDS API and specifying the engine version\.

With version 1\.17 of Aurora, we are using a cluster patching model where all nodes in an Aurora DB cluster are patched at the same time\. We support zero\-downtime patching, which works on a best\-effort basis to preserve client connections through the patching process\. For more information, see [Maintaining an Amazon Aurora DB Cluster](USER_UpgradeDBInstance.Maintenance.md)\. 

Should you have any questions or concerns, the AWS Support Team is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\.

## Zero\-Downtime Patching<a name="AuroraMySQL.Updates.117.ZDP"></a>

The zero\-downtime patching \(ZDP\) attempts, on a *best\-effort* basis, to preserve client connections through an engine patch\. For more information about ZDP, see [Zero\-Downtime Patching](AuroraMySQL.Updates.20170515.md#AuroraMySQL.Updates.20170515.ZDP)\.

## New Features<a name="AuroraMySQL.Updates.117.New"></a>
+  Aurora MySQL now supports lock compression, which optimizes the lock manager’s memory usage\. Starting in version 1\.17, you can use this feature without enabling lab mode\. 

## Improvements<a name="AuroraMySQL.Updates.117.Improvements"></a>
+ Fixed an issue predominantly seen on instances with fewer cores where a single core may have 100% CPU utilization even when the database is idle\.
+ Improved the performance of fetching binary logs from Aurora clusters\.
+ Fixed an issue where Aurora Replicas attempt to write table statistics to persistent storage, and crash\.
+ Fixed an issue where query cache did not work as expected on Aurora Replicas\.
+ Fixed a race condition in lock manager that resulted in an engine restart\.
+ Fixed an issue where locks taken by read\-only, auto\-commit transactions resulted in an engine restart\.
+ Fixed an issue where some queries are not written to the audit logs\.
+ Fixed an issue with recovery of certain partition maintenance operations on failover\.

## Integration of MySQL Bug Fixes<a name="AuroraMySQL.Updates.117.BugFixes"></a>
+ LAST\_INSERT\_ID is replicated incorrectly if replication filters are used \(Bug \#69861\)
+ Query returns different results depending on whether INDEX\_MERGE setting \(Bug \#16862316\)
+ Query proc re\-execute of stored routine, inefficient query plan \(Bug \#16346367\)
+ INNODB FTS : Assert in FTS\_CACHE\_APPEND\_DELETED\_DOC\_IDS \(BUG \#18079671\)
+ Assert RBT\_EMPTY\(INDEX\_CACHE\->WORDS\) in ALTER TABLE CHANGE COLUMN \(BUG \#17536995\)
+ INNODB fulltext search doesn't find records when savepoints are involved \(BUG \#70333, BUG \#17458835\)