# Aurora MySQL Database Engine Updates 2018\-10\-08<a name="AuroraMySQL.Updates.1177"></a>

**Version:** 1\.17\.7

Aurora MySQL 1\.17\.7 is generally available\. All new Aurora MySQL database clusters with MySQL 5\.6 compatibility, including those restored from snapshots, will be created in Aurora MySQL 1\.17\.7\. You have the option, but are not required, to upgrade existing database clusters to Aurora MySQL 1\.17\.7\. To use an older version, you can create new database clusters in Aurora MySQL 1\.14\.4, 1\.15\.1, 1\.16, or 1\.17\.6\. You can do so using the AWS CLI or the Amazon RDS API and specifying the engine version\. 

With version 1\.17\.7 of Aurora MySQL, we are using a cluster patching model where all nodes in an Aurora DB cluster are patched at the same time\. 

**Note**  
 This version is currently not available in the AWS GovCloud \(US\-West\) \[us\-gov\-west\-1\] and China \(Beijing\) \[cn\-north\-1\] regions\. There will be a separate announcement once it is made available\. 

If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB Cluster](USER_UpgradeDBInstance.Maintenance.md)\.

## Improvements<a name="AuroraMySQL.Updates.1177.Improvements"></a>
+  The InnoDB status variable `innodb_buffer_pool_size` has been made publicly visible for the customers to modify\. 
+  Fixed a stability issue on the Aurora cluster that occurred during failovers\. 
+  Improved cluster availability by fixing a DDL recovery issue that occurred after an unsuccessful `TRUNCATE` operation\. 
+  Fixed a stability issue related to the `mysql.innodb_table_stats` table update, triggered by DDL operations\. 
+  Fixed Aurora Replica stability issues triggered during query cache invalidation after a DDL operation\. 
+  Fixed a stability issue triggered by invalid memory access during periodic dictionary cache eviction in the background\. 

## Integration of MySQL community edition bug fixes<a name="AuroraMySQL.Updates.1177.Patches"></a>
+  Bug \#16208542: Drop index on a foreign key column leads to missing table\. 
+  Bug \#76349: memory leak in add\_derived\_key\(\)\. 
+  Bug \#16862316: For partitioned tables, queries could return different results depending on whether Index Merge was used\. 
+  Bug \#17588348: Queries using the index\_merge optimization \(see [Index Merge Optimization](https://dev.mysql.com/doc/refman/5.6/en/index-merge-optimization.html)\) could return invalid results when run against tables that were partitioned by HASH\. 