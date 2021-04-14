# Aurora MySQL database engine updates 2021\-03\-18 \(version 1\.23\.2\)<a name="AuroraMySQL.Updates.1232"></a>

**Version:** 1\.23\.2

Aurora MySQL 1\.23\.2 is generally available\. Aurora MySQL 1\.\* versions are compatible with MySQL 5\.6 and Aurora MySQL 2\.\* versions are compatible with MySQL 5\.7\.

 Currently supported Aurora MySQL releases are 1\.19\.\*, 1\.20\.\*, 1\.21\.\*, 1\.22\.\*, 1\.23\.\*, and 2\.04\.\*, 2\.05\.\*, 2\.06\.\*,2\.07\.\*, 2\.08\.\* and 2\.09\.\*\. You can restore the snapshot of an Aurora MySQL 1\.\* database into Aurora MySQL 1\.23\.2\. 

 To create a cluster with an older version of Aurora MySQL, specify the engine version through the RDS Console, the AWS CLI, or the Amazon RDS API\. 

**Note**  
 This version is currently not available in the following regions: AWS GovCloud \(US\-East\) \[us\-gov\-east\-1\], AWS GovCloud \(US\-West\) \[us\-gov\-west\-1\]\. There will be a separate announcement once it is made available\. 

 If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB cluster](USER_UpgradeDBInstance.Maintenance.md)\. 

## Improvements<a name="AuroraMySQL.Updates.1232.Improvements"></a>

 **High priority fixes:** 
+ [CVE\-2020\-14867](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14867)
+ [CVE\-2020\-14812](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14812)
+ [CVE\-2020\-14769](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14769)
+ [CVE\-2020\-14765](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14765)
+ [CVE\-2020\-14793](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14793)
+ [CVE\-2020\-14672](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14672)
+ [CVE\-2020\-1971](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-1971)
+ [CVE\-2018\-3143](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-3143)

 **Availability improvements:** 
+  Improved binary log \(binlog\) processing to reduce crash recovery time and commit time latency when very large transactions are involved\. 
+  Fixed an issue in the dynamic cluster storage resizing feature that could cause reader DB instances to restart\. 
+  Fixed a failover issue due to a race condition in `RESET QUERY CACHE` statement\. 
+  Fixed a crash in a nested stored procedure call with query cache\. 
+  Fixed an issue to prevent repeated restart of `mysqld` when recovering from an incomplete truncation of partitioned or sub\-partitioned tables\. 
+  Fixed an issue that could cause migration from on\-prem or RDS for MySQL to Aurora MySQL to not succeed\. 
+  Fixed a rare race condition where the database can restart during the scaling of the storage volume\. 
+  Fixed an issue in the lock manager where a race condition can cause a lock to be shared by two transactions, causing the database to restart\. 
+  Fixed an issue related to transaction lock memory management with long\-running write transactions resulting in a database restart\. 
+  Fixed a race condition in the lock manager that resulted in a database restart or failover during transaction rollback\. 
+  Fixed an issue during upgrade from 5\.6 to 5\.7 when the table had Fast Online DDL enabled in lab mode in 5\.6\. 
+  Fixed multiple issues where the the engine might restart during zero\-downtime patching while checking for a quiesced point in database activity for patching\. 
+  Fixed multiple issues related to repeated restarts due to interrupted DDL operations, such as `DROP TRIGGER`, `ALTER TABLE`, and specifically `ALTER TABLE` that modifies the type of partitioning or number of partitions in a table\. 
+  Updated the default value of `table_open_cache` on 16XL and 24XL instances to avoid repeated restarts and high CPU utilization on large instances classes \(R4/R5\-16XL, R5\-12XL, R5\-24XL\)\. This impacted 1\.21\.x and 1\.22\.x releases\. 
