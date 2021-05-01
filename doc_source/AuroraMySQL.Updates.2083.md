# Aurora MySQL database engine updates 2020\-11\-12 \(version 2\.08\.3\)<a name="AuroraMySQL.Updates.2083"></a>

**Version:** 2\.08\.3

Aurora MySQL 2\.08\.3 is generally available\. Aurora MySQL 2\.x versions are compatible with MySQL 5\.7 and Aurora MySQL 1\.x versions are compatible with MySQL 5\.6\.

 Currently supported Aurora MySQL releases for upgrade to 2\.08\.3 are: 1\.14\.\*, 1\.15\.\*, 1\.16\.\*, 1\.17\.\*, 1\.18\.\*, 1\.19\.\*, 1\.20\.\*, 1\.21\.\*, 1\.22\.\*, 2\.01\.\*, 2\.02\.\*, 2\.03\.\*, 2\.04\.\*, 2\.05\.\*, 2\.06\.\*, 2\.07\.\*, and 2\.08\.\*\. 

 You can upgrade existing Aurora MySQL 2\.\* database clusters directly to Aurora MySQL 2\.08\.3\. You can upgrade an existing Aurora MySQL 1\.\* cluster directly to 2\.07\.3 or higher and then directly upgrade to 2\.08\.3\. 

 To create a cluster with an older version of Aurora MySQL, specify the engine version through the AWS Management Console, the AWS CLI, or the RDS API\. 

If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB cluster](USER_UpgradeDBInstance.Maintenance.md)\.

**Note**  
For information on how to upgrade your Aurora MySQL database cluster, see [Upgrading the minor version or patch level of an Aurora MySQL DB cluster](AuroraMySQL.Updates.Patching.md)\.

## Improvements<a name="AuroraMySQL.Updates.2083.Improvements"></a>

 **Security fixes:** 

 Fixes and other enhancements to fine\-tune handling in a managed environment\. Additional CVE fixes below: 
+ [CVE\-2020\-14567](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14567)
+ [CVE\-2020\-14559](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14559)
+ [CVE\-2020\-14553](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14553)
+ [CVE\-2020\-14547](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14547)
+ [CVE\-2020\-14540](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14540)
+ [CVE\-2020\-2812](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-2812)
+ [CVE\-2020\-2806](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-2806)
+ [CVE\-2020\-2780](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-2780)
+ [CVE\-2020\-2765](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-2765)
+ [CVE\-2020\-2763](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-2763)
+ [CVE\-2020\-2760](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-2760)
+ [CVE\-2020\-2579](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-2579)

 **Incompatible changes:** 

 This version introduces a permission change that affects the behavior of the `mysqldump` command\. Users must have the `PROCESS` privilege to access the `INFORMATION_SCHEMA.FILES` table\. To run the `mysqldump` command without any changes, grant the `PROCESS` privilege to the database user that the `mysqldump` command connects to\. You can also run the `mysqldump` command with the `--no-tablespaces` option\. With that option, the `mysqldump `output doesn't include any `CREATE LOGFILE GROUP` or `CREATE TABLESPACE` statements\. In that case, the `mysqldump` command doesn't access the `INFORMATION_SCHEMA.FILES` table, and you don't need to grant the `PROCESS` permission\. 

## Integration of MySQL community edition bug fixes<a name="AuroraMySQL.Updates.2083.Patches"></a>
+  Bug \#23762382 \- INSERT VALUES QUERY WITH JOIN IN A SELECT CAUSES INCORRECT BEHAVIOR\. 
+  Bug \#25209512 \- CURRENT\_TIMESTAMP PRODUCES ZEROS IN TRIGGER\. 