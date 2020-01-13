# Aurora MySQL Database Engine Updates 2018\-09\-20<a name="AuroraMySQL.Updates.1180"></a>

**Version:** 1\.18\.0

Aurora MySQL 1\.18\.0 is generally available\. All new Aurora MySQL parallel query clusters with MySQL 5\.6 compatibility, including those restored from snapshots, will be created in Aurora MySQL 1\.18\.0\. You have the option, but are not required, to upgrade existing parallel query clusters to Aurora MySQL 1\.18\.0\. You can create new DB clusters in Aurora MySQL 1\.14\.4, Aurora MySQL 1\.15\.1, Aurora MySQL 1\.16, or Aurora MySQL 1\.17\.6\. You can do so using the AWS CLI or the Amazon RDS API and specifying the engine version\. 

With version 1\.18\.0 of Aurora MySQL, we are using a cluster patching model where all nodes in an Aurora DB cluster are patched at the same time\. 

**Important**  
 Aurora MySQL 1\.18\.0 only applies to Aurora parallel query clusters\. If you upgrade a provisioned 5\.6\.10a cluster, the resulting version is 1\.17\.8\. If you upgrade a parallel query 5\.6\.10a cluster, the resulting version is 1\.18\.0\. 

If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB Cluster](USER_UpgradeDBInstance.Maintenance.md)\.

## Features<a name="AuroraMySQL.Updates.1180.Features"></a>
+  **Parallel Query** is available with this release, for new clusters and restored snapshots\. Aurora MySQL parallel query is an optimization that parallelizes some of the I/O and computation involved in processing data\-intensive queries\. The work that is parallelized includes retrieving rows from storage, extracting column values, and determining which rows match the conditions in the `WHERE` clause and join clauses\. This data\-intensive work is delegated \(in database optimization terms, pushed down\) to multiple nodes in the Aurora distributed storage layer\. Without parallel query, each query brings all the scanned data to a single node within the Aurora MySQL cluster \(the head node\) and performs all the query processing there\. 
  + When the parallel query feature is enabled, the Aurora MySQL engine automatically determines when queries can benefit, without requiring SQL changes such as hints or table attributes\.

  For more information, see [Working with Parallel Query for Amazon Aurora MySQL](aurora-mysql-parallel-query.md)\.
+  **OOM Avoidance:** This feature monitors the system memory and tracks memory consumed by various components of the database\. Once the system runs low on memory, it performs a list of actions to release memory from various tracked components in an attempt to save the database from running into Out of Memory \(OOM\) and thereby avoiding a database restart\. This best\-effort feature is enabled by default for t2 instances and can be enabled on other instance classes via a new instance parameter named `aurora_oom_response`\. The instance parameter takes a string of comma separated actions that an instance should take when its memory is low\. Valid actions include "print", "tune", "decline", "kill\_query" or any combination of these\. Any empty string means there should be no actions taken and effectively renders the feature to be disabled\. Note that the default actions for the feature is "print, tune"\. Usage examples: 
  + "print" – Only prints the queries taking high amount of memory\.
  + "tune" – Tunes the internal table caches to release some memory back to the system\.
  + "decline" – Declines new queries once the instance is low on memory\.
  + "kill\_query" – Kills the queries in descending order of memory consumption until the instance memory surfaces above the low threshold\. Data definition language \(DDL\) statements are not killed\.
  + "print, tune" – Performs actions described for both "print" and "tune"\.
  + "tune, decline, kill\_query" – Performs the actions described for "tune", "decline", and "kill\_query"\.

   For information about handling out\-of\-memory conditions and other troubleshooting advice, see [ Amazon Aurora MySQL Out of Memory Issues ](CHAP_Troubleshooting.md#CHAP_Troubleshooting.AuroraMySQLOOM)\. 