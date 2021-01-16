# Aurora MySQL database engine updates 2020\-12\-11 \(version 2\.09\.1\)<a name="AuroraMySQL.Updates.2091"></a>

 **Version:** 2\.09\.1 

 Aurora MySQL 2\.09\.1 is generally available\. Aurora MySQL 2\.x versions are compatible with MySQL 5\.7 and Aurora MySQL 1\.x versions are compatible with MySQL 5\.6\. 

 Currently supported Aurora MySQL releases are 1\.14\.\*, 1\.15\.\*, 1\.16\.\*, 1\.17\.\*, 1\.18\.\*, 1\.19\.\*, 1\.20\.\*, 1\.21\.\*, 1\.22\.\*, 1\.23\.\*, 2\.01\.\*, 2\.02\.\*, 2\.03\.\*, 2\.04\.\*, 2\.05\.\*, 2\.06\.\*, 2\.07\.\*, 2\.08\.\*, and 2\.09\.\*\. 

 You can restore a snapshot from Aurora MySQL 1\.23\.\* into Aurora MySQL 2\.09\.1\. You also have the option to upgrade existing Aurora MySQL 2\.\* database clusters to Aurora MySQL 2\.09\.1\. You can't upgrade an existing Aurora MySQL 1\.23\.\* cluster directly to 2\.09\.1; however, you can restore its snapshot to Aurora MySQL 2\.09\.1\. 

 To create a cluster with an older version of Aurora MySQL, specify the engine version through the AWS Management Console, the AWS CLI, or the RDS API\. 

 If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB cluster](USER_UpgradeDBInstance.Maintenance.md)\. 

**Note**  
 For information on how to upgrade your Aurora MySQL database cluster, see [Upgrading the minor version or patch level of an Aurora MySQL DB cluster](AuroraMySQL.Updates.Patching.md)\. 

## Improvements<a name="AuroraMySQL.Updates.2091.Improvements"></a>

 **Security fixes:** 

 Fixes and other enhancements to fine\-tune handling in a managed environment\. Additional CVE fixes below: 
+  [CVE\-2020\-14567](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14567) 
+  [CVE\-2020\-14559](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14559) 
+  [CVE\-2020\-14553](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14553) 
+  [CVE\-2020\-14547](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14547) 
+  [CVE\-2020\-14540](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14540) 
+  [CVE\-2020\-2812](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-2812) 
+  [CVE\-2020\-2806](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-2806) 
+  [CVE\-2020\-2780](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-2780) 
+  [CVE\-2020\-2765](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-2765) 
+  [CVE\-2020\-2763](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-2763) 
+  [CVE\-2020\-2760](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-2760) 
+  [CVE\-2020\-2579](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-2579) 

 **Availability improvements:** 
+  Fixed an issue that might cause a client session to hang when the database engine encounters an error while reading from or writing to the network\. 
+  Fixed a memory leak in dynamic resizing feature, introduced in 2\.09\.0\. 

 **Global databases:** 
+  Fixed multiple issues where a global database secondary Region's replicas might restart when upgraded to release 2\.09\.0 while the primary Region writer was on an older release version\. 

## Integration of MySQL community edition bug fixes<a name="AuroraMySQL.Updates.2091.Patches"></a>
+  **Replication:** Interleaved transactions could sometimes deadlock the slave applier when the transaction isolation level was set to [REPEATABLE READ](https://dev.mysql.com/doc/refman/5.7/en/innodb-transaction-isolation-levels.html#isolevel_repeatable-read)\. \(Bug \#25040331\) 
+  For a table having a [TIMESTAMP](https://dev.mysql.com/doc/refman/5.7/en/datetime.html) or [DATETIME](https://dev.mysql.com/doc/refman/5.7/en/datetime.html) column having a default of [CURRENT\_TIMESTAMP](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_current-timestamp), the column could be initialized to `0000-00-00 00:00:00` if the table had a `BEFORE INSERT` trigger\. \(Bug \#25209512, Bug \#84077\) 
+  For an [INSERT](https://dev.mysql.com/doc/refman/5.7/en/insert.html) statement for which the `VALUES` list produced values for the second or later row using a subquery containing a join, the server could exit after failing to resolve the required privileges\. \(Bug \#23762382\) 