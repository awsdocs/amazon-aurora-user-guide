# Aurora MySQL Database Engine Updates 2018\-09\-06<a name="AuroraMySQL.Updates.1176"></a>

**Version:** 1\.17\.6

Aurora MySQL 1\.17\.6 is generally available\. All new Aurora MySQL database clusters with MySQL 5\.6 compatibility, including those restored from snapshots, will be created in Aurora MySQL 1\.17\.6\. You have the option, but are not required, to upgrade existing database clusters to Aurora MySQL 1\.17\.6\. To use an older version, you can create new database clusters in Aurora MySQL 1\.14\.4, 1\.15\.1, 1\.16, or 1\.17\.5\. You can do so using the AWS CLI or the Amazon RDS API and specifying the engine version\. 

With version 1\.17\.6 of Aurora MySQL, we are using a cluster patching model where all nodes in an Aurora DB cluster are patched at the same time\. 

**Note**  
 This version is currently not available in the AWS GovCloud \(US\-West\) \[us\-gov\-west\-1\] and China \(Beijing\) \[cn\-north\-1\] regions\. There will be a separate announcement once it is made available\. 

If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB Cluster](USER_UpgradeDBInstance.Maintenance.md)\.

## Improvements<a name="AuroraMySQL.Updates.1176.Improvements"></a>
+  Fixed a stability issue on the Aurora Reader for `SELECT` queries while the Aurora Writer is performing DDL operations on the same table\. 
+  Fixed a stability issue caused by the creation and deletion of DDL logs for temporary tables that use Heap/Memory engine\. 
+  Fixed a stability issue on the binlog worker when DDL statements are being replicated while the connection to the Binlog Master is unstable\. 
+  Fixed a stability issue encountered while writing to the slow query log\. 
+  Fixed an issue with the replica status table that exposed incorrect Aurora Reader lag information\. 

## Integration of MySQL community edition bug fixes<a name="AuroraMySQL.Updates.1176.Patches"></a>
+  For an [ALTER TABLE](https://dev.mysql.com/doc/refman/5.6/en/alter-table.html) statement that renamed or changed the default value of a [BINARY](https://dev.mysql.com/doc/refman/5.6/en/binary-varbinary.html) column, the alteration was done using a table copy and not in place\. \(Bug \#67141, Bug \#14735373, Bug \#69580, Bug \#17024290\) 
+  An outer join between a regular table and a derived table that is implicitly groups could cause a server exit\. \(Bug \#16177639\) 