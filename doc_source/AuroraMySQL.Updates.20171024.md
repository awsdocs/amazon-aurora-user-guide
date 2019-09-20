# Aurora MySQL Database Engine Updates 2017\-10\-24<a name="AuroraMySQL.Updates.20171024"></a>

**Version:** 1\.15

Aurora MySQL 1\.15 is generally available\. All new database clusters, including those restored from snapshots, will be created in Aurora 1\.15\. You have the option, but are not required, to upgrade existing DB clusters to Aurora 1\.15\. You can create new DB clusters in Aurora 1\.14\.1\. You can do so using the AWS CLI or the Amazon RDS API and specifying the engine version\.

With version 1\.15 of Aurora, we are using a cluster patching model where all nodes in an Aurora DB cluster are patched at the same time\. Updates require a database restart, so you will experience 20 to 30 seconds of downtime, after which you can resume using your DB cluster or clusters\. If your DB clusters are currently running Aurora 1\.14 or Aurora 1\.14\.1, the zero\-downtime patching feature in Aurora MySQL might allow client connections to your Aurora MySQL primary instance to persist through the upgrade, depending on your workload\.

If you have any questions or concerns, AWS Support is available on the community forums and through AWS Premium Support at [http://aws\.amazon\.com/support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB Cluster](USER_UpgradeDBInstance.Maintenance.md)\.

## Zero\-Downtime Patching<a name="AuroraMySQL.Updates.20171024.ZDP"></a>

The zero\-downtime patching \(ZDP\) feature attempts, on a *best\-effort* basis, to preserve client connections through an engine patch\. For more information about ZDP, see [Zero\-Downtime Patching](AuroraMySQL.Updates.md#AuroraMySQL.Updates.ZDP)\. 

## New Features<a name="AuroraMySQL.Updates.20171024.New"></a>
+ **Asynchronous Key Prefetch** – Asynchronous key prefetch \(AKP\) is a feature targeted to improve the performance of non\-cached index joins, by prefetching keys in memory ahead of when they are needed\. The primary use case targeted by AKP is an index join between a small outer and large inner table, where the index is highly selective on the larger table\. Also, when the Multi\-Range Read \(MRR\) interface is enabled, AKP will be leveraged for a secondary to primary index lookup\. Smaller instances which have memory constraints might in some cases be able to leverage AKP, given the right key cardinality\. For more information, see [Working with Asynchronous Key Prefetch in Amazon Aurora](AuroraMySQL.BestPractices.md#Aurora.BestPractices.AKP)\.
+ **Fast DDL** – We have extended the feature that was released in [Aurora 1\.13](AuroraMySQL.Updates.20170515.md) to operations that include default values\. With this extension, Fast DDL is applicable for operations that add a nullable column at the end of a table, with or without default values\. The feature remains under Aurora lab mode\. For more information, see [Altering Tables in Amazon Aurora Using Fast DDL](AuroraMySQL.Managing.FastDDL.md)\.

## Improvements<a name="AuroraMySQL.Updates.20171024.Improvements"></a>
+ Fixed a calculation error during optimization of WITHIN/CONTAINS spatial queries which previously resulted in an empty result set\.
+ Fixed `SHOW VARIABLE` command to show the updated `innodb_buffer_pool_size` parameter value whenever it is changed in the parameter group\.
+ Improved stability of primary instance during bulk insert into a table altered using Fast DDL when adaptive hash indexing is disabled and the record to be inserted is the first record of a page\.
+ Improved stability of Aurora when the user attempts to set **server\_audit\_events** DB cluster parameter value to **default**\.
+ Fixed an issue in which a database character set change for an ALTER TABLE statement that ran on the Aurora primary instance was not being replicated on the Aurora Replicas until they were restarted\.
+ Improved stability by fixing a race condition on the primary instance which previously allowed it to register an Aurora Replica even if the primary instance had closed its own volume\.
+ Improved performance of the primary instance during index creation on a large table by changing the locking protocol to enable concurrent data manipulation language \(DML\) statements during index build\.
+ Fixed InnoDB metadata inconsistency during ALTER TABLE RENAME query which improved stability\. Example: When columns of table t1\(c1, c2\) are renamed cyclically to t1\(c2,c3\) within the same ALTER statement\.
+ Improved stability of Aurora Replicas for the scenario where an Aurora Replica has no active workload and the primary instance is unresponsive\.
+ Improved availability of Aurora Replicas for a scenario in which the Aurora Replica holds an explicit lock on a table and blocks the replication thread from applying any DDL changes received from the primary instance\.
+ Improved stability of the primary instance when a foreign key and a column are being added to a table from two separate sessions at the same time and Fast DDL has been enabled\.
+ Improved stability of the purge thread on the primary instance during a heavy write workload by blocking truncate of undo records until they have been purged\.
+ Improved stability by fixing the lock release order during commit process of transactions which drop tables\.
+ Fixed a defect for Aurora Replicas in which the DB instance could not complete startup and complained that port 3306 was already in use\.
+ Fixed a race condition in which a SELECT query run on certain information\_schema tables \(innodb\_trx, innodb\_lock, innodb\_lock\_waits\) increased cluster instability\.

## Integration of MySQL Bug Fixes<a name="AuroraMySQL.Updates.20171024.BugFixes"></a>
+ CREATE USER accepts plugin and password hash, but ignores the password hash \(Bug \#78033\)
+ The partitioning engine adds fields to the read bit set to be able to return entries sorted from a partitioned index\. This leads to the join buffer will try to read unneeded fields\. Fixed by not adding all partitioning fields to the read\_set,but instead only sort on the already set prefix fields in the read\_set\. Added a DBUG\_ASSERT that if doing key\_cmp, at least the first field must be read \(Bug \#16367691\)\.
+ MySQL instance stalling “doing SYNC index” \(Bug \#73816\)
+ Assert RBT\_EMPTY\(INDEX\_CACHE\->WORDS\) in ALTER TABLE CHANGE COLUMN \(Bug \#17536995\)
+ InnoDB Fulltext search doesn't find records when savepoints are involved \(Bug \#70333\)