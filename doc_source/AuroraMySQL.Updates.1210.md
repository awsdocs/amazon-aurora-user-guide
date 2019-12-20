# Aurora MySQL Database Engine Updates 2019\-11\-25 \(Version 1\.21\.0\)<a name="AuroraMySQL.Updates.1210"></a>

**Version:** 1\.21\.0

 Aurora MySQL 1\.21\.0 is generally available\. Aurora MySQL 1\.\* versions are compatible with MySQL 5\.6 and Aurora MySQL 2\.\* versions are compatible with MySQL 5\.7\. 

 Currently supported Aurora MySQL releases are 1\.14\.\*, 1\.15\.\*, 1\.16\.\*, 1\.17\.\*, 1\.18\.\*, 1\.19\.\*, 1\.20\.\*, 1\.21\.\*, 2\.01\.\*, 2\.02\.\*, 2\.03\.\* and 2\.04\.\*\. To create a cluster with an older version of Aurora MySQL, please specify the engine version through the AWS Management Console, the AWS CLI or the RDS API\. You have the option to upgrade existing Aurora MySQL 1\.\* database clusters to Aurora MySQL 1\.21\.0\. 

**Note**  
 This version is currently not available in the following AWS Regions: AWS GovCloud \(US\-East\) \[us\-gov\-east\-1\], AWS GovCloud \(US\-West\) \[us\-gov\-west\-1\], China \(Ningxia\) \[cn\-northwest\-1\], Asia Pacific \(Hong Kong\) \[ap\-east\-1\], Europe \(Stockholm\) \[eu\-north\-1\], and Middle East \(Bahrain\) \[me\-south\-1\]\. There will be a separate announcement once it is made available\. 

 If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB Cluster](USER_UpgradeDBInstance.Maintenance.md)\. 

**Note**  
The procedure to upgrade your DB cluster has changed\. For more information, see [Database Upgrades and Patches for Amazon Aurora MySQL](AuroraMySQL.Updates.md#AuroraMySQL.Updates.Patching)\.

## Improvements<a name="AuroraMySQL.Updates.1210.Improvements"></a>

 **Critical fixes:** 
+ [CVE\-2018\-0734](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-0734)
+ [CVE\-2019\-2534](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-2534)
+ [CVE\-2018\-2612](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-2612)
+ [CVE\-2017\-3599](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-3599)
+ [CVE\-2018\-2562](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-2562)
+ [CVE\-2017\-3329](https://nvd.nist.gov/vuln/detail/CVE-2017-3329)
+ [CVE\-2018\-2696](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-2696)
+ [CVE\-2015\-4737](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2015-4737)

 **High priority fixes:** 
+   Customers with database size close to 64 TiB are strongly advised to upgrade to this version to avoid downtime due to stability bugs affecting volumes close to the Aurora storage limit\. 

 **General stability fixes:** 
+  Fixed a parallel query abort error on Aurora reader instances while a heavy write workload is running on the Aurora writer instance\. 
+  Fixed an issue on Aurora reader instances that reduced free memory during long\-running transactions while there is a heavy transaction commit traffic on the writer instance\. 
+  The value of the parameter `aurora_disable_hash_join` is now persisted after database restart or host replacement\. 
+  Fixed an issue related to the Full Text Search cache that caused the Aurora instance to run out of memory\. Customers using Full Text Search should upgrade\. 
+  Improved stability of the database when the hash join feature is enabled and the instance is low on memory\. Customers using hash join should upgrade\. 
+  Fixed an issue in the query cache where the "Too many connections" error could cause a reboot\. 
+  Fixed the free memory calculation on T2 instances to include swap memory space to prevent unnecessary reboots\. 

## Integration of MySQL community edition bug fixes<a name="AuroraMySQL.Updates.1210.Patches"></a>
+  Bug \#19929406: HANDLE\_FATAL\_SIGNAL \(SIG=11\) IN \_\_MEMMOVE\_SSSE3\_BACK FROM STRING::COPY 
+  Bug \#17059925: For [UNION](https://dev.mysql.com/doc/refman/5.6/en/union.html) statements, the rows\-examined value was calculated incorrectly\. This was manifested as too\-large values for the `ROWS_EXAMINED` column of Performance Schema statement tables \(such as [events\_statements\_current](https://dev.mysql.com/doc/refman/5.6/en/events-statements-current-table.html)\)\. 
+  Bug \#11827369: Some queries with `SELECT ... FROM DUAL` nested subqueries raised an assertion\. 
+  Bug \#16311231: Incorrect results were returned if a query contained a subquery in an `IN` clause that contained an [XOR](https://dev.mysql.com/doc/refman/5.6/en/logical-operators.html#operator_xor) operation in the `WHERE` clause\. 