# Aurora MySQL database engine updates 2020\-11\-09 \(version 1\.22\.3\)<a name="AuroraMySQL.Updates.1223"></a><a name="1223"></a><a name="1.22.3"></a>

**Version:** 1\.22\.3

Aurora MySQL 1\.22\.3 is generally available\. Aurora MySQL 1\.\* versions are compatible with MySQL 5\.6 and Aurora MySQL 2\.\* versions are compatible with MySQL 5\.7\.

 Currently supported Aurora MySQL releases for upgrade to 1\.22\.3 are 1\.14\.\*, 1\.15\.\*, 1\.16\.\*, 1\.17\.\*, 1\.18\.\*, 1\.19\.\*, 1\.20\.\*, 1\.21\.\*, and 1\.22\.\*\. 

 To create a cluster with an older version of Aurora MySQL, specify the engine version through the RDS Console, the AWS CLI, or the Amazon RDS API\. 

**Note**  <a name="lts_notice_1223"></a>
 This version is designated as a long\-term support \(LTS\) release\. For more information, see [Aurora MySQL long\-term support \(LTS\) releases](AuroraMySQL.Updates.Versions.md#AuroraMySQL.Updates.LTS)\. 

 If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB cluster](USER_UpgradeDBInstance.Maintenance.md)\. 

## Improvements<a name="AuroraMySQL.Updates.1223.Improvements"></a>

 **Security fixes:** 

 Fixes and other enhancements to fine\-tune handling in a managed environment\. Additional CVE fixes below: 
+ [CVE\-2020\-14559](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14559)
+ [CVE\-2020\-14539](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14539)
+ [CVE\-2020\-2579](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-2579)
+ [CVE\-2020\-2812](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-2812)
+ [CVE\-2020\-2780](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-2780)
+ [CVE\-2020\-2763](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-2763)

 **Incompatible changes:** 

 This version introduces a permission change that affects the behavior of the `mysqldump` command\. Users must have the `PROCESS` privilege to access the `INFORMATION_SCHEMA.FILES` table\. To run the `mysqldump` command without any changes, grant the `PROCESS` privilege to the database user that the `mysqldump` command connects to\. You can also run the `mysqldump` command with the `--no-tablespaces` option\. With that option, the `mysqldump `output doesn't include any `CREATE LOGFILE GROUP` or `CREATE TABLESPACE` statements\. In that case, the `mysqldump` command doesn't access the `INFORMATION_SCHEMA.FILES` table, and you don't need to grant the `PROCESS` permission\. 

 **Availability improvements:** 
+  Fixed issues that might cause server restarts during recovery of a DDL statement that was not committed\. 
+  Fixed race conditions in the lock manager that can cause a server restart\. 
+  Fixed an issue that might cause the monitoring agent to restart the server during recovery of a large transaction 

 **General improvements:** 
+  Changed the behavior to map `MIXED` `binlog_format` to `ROW` instead of `STATEMENT` when executing `LOAD DATA FROM INFILE | S3`\. 
+  Fixed an issue where a binlog replica connected to an Aurora MySQL binlog primary might show incomplete data when the primary executed `LOAD DATA FROM S3` and `binlog_format` is set to `STATEMENT`\. 

## Integration of MySQL community edition bug fixes<a name="AuroraMySQL.Updates.1223.Patches"></a>
+  Bug \#26654685: A corrupt index ID encountered during a foreign key check raised an assertion 
+  Bug \#15831300: By default, when promoting integers from a smaller type on the master to a larger type on the slave \(for example, from a [SMALLINT](https://dev.mysql.com/doc/refman/5.6/en/integer-types.html) column on the master to a [BIGINT](https://dev.mysql.com/doc/refman/5.6/en/integer-types.html) column on the slave\), the promoted values are treated as though they are signed\. Now in such cases it is possible to modify or override this behavior using one or both of `ALL_SIGNED`, `ALL_UNSIGNED` in the set of values specified for the [slave\_type\_conversions](https://dev.mysql.com/doc/refman/5.6/en/replication-options-replica.html#sysvar_slave_type_conversions) server system variable\. For more information, see [Row\-based replication: attribute promotion and demotion](https://dev.mysql.com/doc/refman/5.6/en/replication-features-differing-tables.html#replication-features-attribute-promotion), as well as the description of the variable\. 
+  Bug \#17449901: With `foreign_key_checks=0`, InnoDB permitted an index required by a foreign key constraint to be dropped, placing the table into an inconsistent and causing the foreign key check that occurs at table load to fail\. InnoDB now prevents dropping an index required by a foreign key constraint, even with foreign\_key\_checks=0\. The foreign key constraint must be removed before dropping the foreign key index\. 
+  BUG \#20768847: An [ALTER TABLE \.\.\. DROP INDEX](https://dev.mysql.com/doc/refman/5.7/en/alter-table.html) operation on a table with foreign key dependencies raised an assertion\. 