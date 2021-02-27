# Aurora MySQL database engine updates 2021\-02\-26 \(version 2\.09\.2\)<a name="AuroraMySQL.Updates.2092"></a>

 **Version:** 2\.09\.2 

 Aurora MySQL 2\.09\.2 is generally available\. Aurora MySQL 2\.x versions are compatible with MySQL 5\.7 and Aurora MySQL 1\.x versions are compatible with MySQL 5\.6\. 

 Currently supported Aurora MySQL releases are 1\.14\.\*, 1\.15\.\*, 1\.16\.\*, 1\.17\.\*, 1\.18\.\*, 1\.19\.\*, 1\.20\.\*, 1\.21\.\*, 1\.22\.\*, 1\.23\.\*, 2\.01\.\*, 2\.02\.\*, 2\.03\.\*, 2\.04\.\*, 2\.05\.\*, 2\.06\.\*, 2\.07\.\*, 2\.08\.\*, and 2\.09\.\*\. 

 You can restore a snapshot from a currently supported Aurora MySQL release into Aurora MySQL 2\.09\.2\. You also have the option to upgrade existing Aurora MySQL 2\.\* database clusters to Aurora MySQL 2\.09\.2\. You can't upgrade an existing Aurora MySQL 1\.\* cluster directly to 2\.09\.2; however, you can restore its snapshot to Aurora MySQL 2\.09\.2\. 

 To create a cluster with an older version of Aurora MySQL, specify the engine version through the AWS Management Console, the AWS CLI, or the Amazon RDS API\. 

 If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB cluster](USER_UpgradeDBInstance.Maintenance.md)\. 

**Note**  
 For information on how to upgrade your Aurora MySQL database cluster, see [Upgrading the minor version or patch level of an Aurora MySQL DB cluster](AuroraMySQL.Updates.Patching.md)\. 

## Improvements<a name="AuroraMySQL.Updates.2092.Improvements"></a>

 **Security fixes:** 

 Fixes and other enhancements to fine\-tune handling in a managed environment\. Additional CVE fixes below: 
+  [CVE\-2020\-14775](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14775) 
+  [CVE\-2020\-14793](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14793) 
+  [CVE\-2020\-14765](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14765) 
+  [CVE\-2020\-14769](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14769) 
+  [CVE\-2020\-14812](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14812) 
+  [CVE\-2020\-14760](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14760) 
+  [CVE\-2020\-14672](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14672) 
+  [CVE\-2020\-14790](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14790) 
+  [CVE\-2020\-1971](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-1971) 

 **Availability improvements:** 
+  Fixed an issue introduced in 2\.09\.0 that can cause elevated write latency during the scaling of the cluster storage volume\. 
+  Fixed an issue in the dynamic resizing feature that could cause Aurora Read Replicas to restart\. 
+  Fixed an issue that could cause longer downtime during upgrade from 1\.23\.\* to 2\.09\.\*\. 
+  Fixed an issue where a DDL or DML could cause engine restart during a page prefetch request\. 
+  Fixed an issue that caused a binlog replica to stop with an error if the replicated transaction contains a DDL statement and a large number of row changes\. 
+  Fixed an issue where a database acting as a binlog replica could restart while replicating a DDL event on the mysql `time_zone` table\. 
+  Fixed an issue that could cause large transactions to generate incorrect binlog events if the `binlog_checksum` parameter was set to `NONE`\. 
+  Fixed an issue that caused a binlog replica to stop with an `HA_ERR_KEY_NOT_FOUND` error\. 
+  Improved overall stability\. 