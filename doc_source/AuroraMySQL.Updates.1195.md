# Aurora MySQL Database Engine Updates 2019\-09\-19 \(Version 1\.19\.5\)<a name="AuroraMySQL.Updates.1195"></a>

**Version:** 1\.19\.5

 Aurora MySQL 1\.19\.5 is generally available\. Aurora MySQL 1\.\* versions are compatible with MySQL 5\.6 and Aurora MySQL 2\.\* versions are compatible with MySQL 5\.7\. 

 You have the option to upgrade existing database clusters to Aurora MySQL 1\.19\.5\. You can restore snapshots of Aurora MySQL 1\.14\.\*, 1\.15\.\*, 1\.16\.\*, 1\.17\.\*, 1\.18\.\*, 1\.19\.1, and 1\.19\.2 into Aurora MySQL 1\.19\.5\. 

 To use an older version of Aurora MySQL, you can create new database clusters by specifying the engine version through the AWS Management Console, the AWS CLI, or the RDS API\. 

 If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB Cluster](USER_UpgradeDBInstance.Maintenance.md)\. 

**Note**  
 This version is currently not available in the following AWS Regions: Europe \(London\) \[eu\-west\-2\], AWS GovCloud \(US\-East\) \[us\-gov\-east\-1\], AWS GovCloud \(US\-West\) \[us\-gov\-west\-1\], China \(Ningxia\) \[cn\-northwest\-1\], and Asia Pacific \(Hong Kong\) \[ap\-east\-1\]\. There will be a separate announcement once it is made available\. 

**Note**  
The procedure to upgrade your DB cluster has changed\. For more information, see [Database Upgrades and Patches for Amazon Aurora MySQL](AuroraMySQL.Updates.md#AuroraMySQL.Updates.Patching)\.

## Improvements<a name="AuroraMySQL.Updates.1195.Improvements"></a>
+  Fixed an issue on Aurora reader instances that reduced free memory during long\-running transactions while there is a heavy transaction commit traffic on the writer instance\. 
+  Fixed a parallel query abort error on Aurora reader instances while a heavy write workload is running on the Aurora writer instance\. 
+  The value of the parameter `aurora_disable_hash_join` is now persisted after database restart or host replacement\. 
+  Fixed an issue related to the Full Text Search cache that caused the Aurora instance to run out of memory\. 
+  Improved stability of the database when the volume size is close to the 64 TiB volume limit by reserving 160 GB of space for the recovery workflow to complete without a failover\. 
+  Improved stability of the database when the hash join feature is enabled and the instance is low on memory\. 
+  Fixed the free memory calculation to include swap memory space on T2 instances that caused them to reboot prematurely\. 
+  Fixed an issue in the query cache where the "Too many connections" error could cause a reboot\. 

## Integration of MySQL community edition bug fixes<a name="AuroraMySQL.Updates.1195.Patches"></a>
+  [CVE\-2018\-2696](http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-2696) 
+  [CVE\-2015\-4737](http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2015-4737) 
+  Bug \#19929406: HANDLE\_FATAL\_SIGNAL \(SIG=11\) IN \_\_MEMMOVE\_SSSE3\_BACK FROM STRING::COPY 
+  Bug \#17059925: For [UNION](https://dev.mysql.com/doc/refman/5.6/en/union.html) statements, the rows\-examined value was calculated incorrectly\. This was manifested as too\-large values for the `ROWS_EXAMINED` column of Performance Schema statement tables \(such as [events\_statements\_current](https://dev.mysql.com/doc/refman/5.6/en/events-statements-current-table.html)\)\. 
+  Bug \#11827369: Some queries with `SELECT ... FROM DUAL` nested subqueries raised an assertion\. 
+  Bug \#16311231: Incorrect results were returned if a query contained a subquery in an `IN` clause that contained an [XOR](https://dev.mysql.com/doc/refman/5.6/en/logical-operators.html#operator_xor) operation in the `WHERE` clause\. 