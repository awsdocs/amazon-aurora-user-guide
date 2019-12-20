# Aurora MySQL Database Engine Updates 2017\-11\-20<a name="AuroraMySQL.Updates.20171120"></a>

**Version:** 1\.15\.1

Aurora MySQL 1\.15\.1 is generally available\. All new database clusters, including those restored from snapshots, will be created in Aurora 1\.15\.1\. You have the option, but are not required, to upgrade existing DB clusters to Aurora 1\.15\.1\. You can create new DB clusters in Aurora 1\.14\.1\. You can do so using the AWS CLI or the Amazon RDS API and specifying the engine version\.

With version 1\.15\.1 of Aurora, we are using a cluster patching model where all nodes in an Aurora DB cluster are patched at the same time\. We are enabling zero\-downtime patching, which works on a best\-effort basis to preserve client connections through the patching process\. For more information, see [Maintaining an Amazon Aurora DB Cluster](USER_UpgradeDBInstance.Maintenance.md)\.

If you have any questions or concerns, AWS Support is available on the community forums and through AWS Premium Support at [http://aws\.amazon\.com/support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB Cluster](USER_UpgradeDBInstance.Maintenance.md)\.

## Zero\-Downtime Patching<a name="AuroraMySQL.Updates.20171120.ZDP"></a>

The zero\-downtime patching \(ZDP\) feature attempts, on a *best\-effort* basis, to preserve client connections through an engine patch\. For more information about ZDP, see [Zero\-Downtime Patching](AuroraMySQL.Updates.md#AuroraMySQL.Updates.ZDP)\. 

## Improvements<a name="AuroraMySQL.Updates.20171120.Improvements"></a>
+ Fixed an issue in the adaptive segment selector for a read request that would cause it to choose the same segment twice causing a spike in read latency under certain conditions\.
+ Fixed an issue that stems from an optimization in Aurora MySQL for the thread scheduler\. This problem manifests itself into what are spurious errors while writing to the slow log, while the associated queries themselves perform fine\.
+ Fixed an issue with stability of read replicas on large \(> 5 TB\) volumes\.
+ Fixed an issue where worker thread count increases continuously due to a bogus outstanding connection count\.
+ Fixed an issue with table locks that caused long semaphore waits during insert workloads\.
+ Reverted the following MySQL bug fixes included in Aurora MySQL 1\.15:
  + MySQL instance stalling “doing SYNC index” \(Bug \#73816\)
  + Assert RBT\_EMPTY\(INDEX\_CACHE\->WORDS\) in ALTER TABLE CHANGE COLUMN \(Bug \#17536995\)
  + InnoDB Fulltext search doesn't find records when savepoints are involved \(Bug \#70333\)

## Integration of MySQL Bug Fixes<a name="AuroraMySQL.Updates.20171024.BugFixes"></a>

None