# Aurora MySQL Database Engine Updates 2018\-08\-07<a name="AuroraMySQL.Updates.1174"></a>

**Version:** 1\.17\.4

Aurora MySQL 1\.17\.4 is generally available\. All new Aurora MySQL database clusters with MySQL 5\.6 compatibility, including those restored from snapshots, will be created in Aurora MySQL 1\.17\.4\. You have the option, but are not required, to upgrade existing database clusters to Aurora MySQL 1\.17\.4\. To use an older version, you can create new database clusters in Aurora MySQL 1\.14\.4, 1\.15\.1, 1\.16, or 1\.17\.3\. You can do so using the AWS CLI or the Amazon RDS API and specifying the engine version\. 

With version 1\.17\.4 of Aurora MySQL, we are using a cluster patching model where all nodes in an Aurora DB cluster are patched at the same time\. 

**Note**  
 This version is currently not available in the AWS GovCloud \(US\-West\) \[us\-gov\-west\-1\] and China \(Beijing\) \[cn\-north\-1\] regions\. There will be a separate announcement once it is made available\. 

If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB Cluster](USER_UpgradeDBInstance.Maintenance.md)\.

## Improvements<a name="AuroraMySQL.Updates.1174.Improvements"></a>
+  Replication improvements: 
  +  Reduced network traffic by not transmitting binlog records to cluster replicas\. This improvement is enabled by default\. 
  +  Reduced network traffic by compressing replication messages\. This improvement is enabled by default for 8xlarge and 16xlarge instance classes\. Such large instances can sustain a heavy volume of write traffic that results in substantial network traffic for replication messages\. 
  +  Fixes to the replica query cache\. 
+  Fixed an issue where `ORDER BY LOWER(col_name)` could produce incorrect ordering while using the `utf8_bin` collation\. 
+  Fixed an issue where DDL statements \(especially `TRUNCATE TABLE`\) could cause problems on Aurora replicas, including instability or missing tables\. 
+  Fixed an issue where sockets are left in a half\-open state when storage nodes are restarted\. 
+ The following new DB cluster parameters are available:
  + `aurora_enable_zdr` – Allow connections opened on an Aurora Replica to stay active on replica restart\.
  + `aurora_enable_replica_log_compression` – Enable compression of replication payloads to improve network bandwidth utilization between the master and Aurora Replicas\.
  + `aurora_enable_repl_bin_log_filtering` – Enable filtering of replication records that are unusable by Aurora Replicas on the master\.