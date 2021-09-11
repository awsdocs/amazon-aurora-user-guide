# Aurora MySQL database engine updates 2020\-11\-10 \(version 2\.07\.3\)<a name="AuroraMySQL.Updates.2073"></a>

**Version:** 2\.07\.3

Aurora MySQL 2\.07\.3 is generally available\. Aurora MySQL 2\.\* versions are compatible with MySQL 5\.7 and Aurora MySQL 1\.\* versions are compatible with MySQL 5\.6\.

 Currently supported Aurora MySQL releases are 1\.14\.\*, 1\.15\.\*, 1\.16\.\*, 1\.17\.\*, 1\.18\.\*, 1\.19\.\*, 1\.20\.\*, 1\.21\.\*, 1\.22\.\*, 1\.23\.\*, 2\.01\.\*, 2\.02\.\*, 2\.03\.\*, 2\.04\.\*, 2\.05\.\*, 2\.06\.\*, 2\.07\.\*, 2\.08\.\*, and 2\.09\.\*\. 

 You can restore a snapshot from a currently supported Aurora MySQL release into Aurora MySQL 2\.07\.3\. You also have the option to upgrade existing Aurora MySQL 2\.\* database clusters to Aurora MySQL 2\.07\.3\. You can't upgrade an existing Aurora MySQL 1\.\* cluster directly to 2\.07\.3; however, you can restore its snapshot to Aurora MySQL 2\.07\.3\. 

 To create a cluster with an older version of Aurora MySQL, please specify the engine version through the AWS Management Console, the AWS CLI, or the RDS API\. 

**Note**  <a name="lts_notice_2073"></a>
 This version is designated as a long\-term support \(LTS\) release\. For more information, see [Aurora MySQL long\-term support \(LTS\) releases](AuroraMySQL.Updates.Versions.md#AuroraMySQL.Updates.LTS)\. 

If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB cluster](USER_UpgradeDBInstance.Maintenance.md)\.

## Improvements<a name="AuroraMySQL.Updates.2073.Improvements"></a>

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
+ [CVE\-2019\-2740](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-2740)

 **Incompatible changes:** 

 This version introduces a permission change that affects the behavior of the `mysqldump` command\. Users must have the `PROCESS` privilege to access the `INFORMATION_SCHEMA.FILES` table\. To run the `mysqldump` command without any changes, grant the `PROCESS` privilege to the database user that the `mysqldump` command connects to\. You can also run the `mysqldump` command with the `--no-tablespaces` option\. With that option, the `mysqldump `output doesn't include any `CREATE LOGFILE GROUP` or `CREATE TABLESPACE` statements\. In that case, the `mysqldump` command doesn't access the `INFORMATION_SCHEMA.FILES` table, and you don't need to grant the `PROCESS` permission\. 

 **Availability improvements:** 
+  Fixed a race condition in the lock manager between the killing of a connection/query and the termination of the session resulting in a database restart\. 
+  Fixed an issue that results in a database restart after a multi\-query statement that accesses multiple tables or databases is executed with the query cache enabled\. 
+  Fixed an issue that might cause repeated restarts due to updates of virtual columns with secondary indexes\. 

## Integration of MySQL community edition bug fixes<a name="AuroraMySQL.Updates.2073.Patches"></a>
+  *InnoDB:* Concurrent XA transactions that ran successfully to the XA prepare stage on the master conflicted when replayed on the slave, resulting in a lock wait timeout in the applier thread\. The conflict was due to the GAP lock range which differed when the transactions were replayed serially on the slave\. To prevent this type of conflict, GAP locks taken by XA transactions in [READ COMMITTED](https://dev.mysql.com/doc/refman/5.7/en/innodb-transaction-isolation-levels.html#isolevel_read-committed) isolation level are now released \(and no longer inherited\) when XA transactions reach the prepare stage\. \(Bug \#27189701, Bug \#25866046\) 
+  *InnoDB:* A gap lock was taken unnecessarily during foreign key validation while using the [READ COMMITTED](https://dev.mysql.com/doc/refman/5.7/en/innodb-transaction-isolation-levels.html#isolevel_read-committed) isolation level\. \(Bug \#25082593\) 
+  *Replication:* When using XA transactions, if a lock wait timeout or deadlock occurred for the applier \(SQL\) thread on a replication slave, the automatic retry did not work\. The cause was that while the SQL thread would do a rollback, it would not roll the XA transaction back\. This meant that when the transaction was retried, the first event was XA START which was invalid as the XA transaction was already in progress, leading to an XAER\_RMFAIL error\. \(Bug \#24764800\) 
+  *Replication:* Interleaved transactions could sometimes deadlock the slave applier when the transaction isolation level was set to [REPEATABLE READ](https://dev.mysql.com/doc/refman/5.7/en/innodb-transaction-isolation-levels.html#isolevel_repeatable-read)\. \(Bug \#25040331\) 
+  *Replication:* The value returned by a [SHOW SLAVE STATUS](https://dev.mysql.com/doc/refman/5.6/en/show-slave-status.html) statement for the total combined size of all existing relay log files \(Relay\_Log\_Space\) could become much larger than the actual disk space used by the relay log files\. The I/O thread did not lock the variable while it updated the value, so the SQL thread could automatically delete a relay log file and write a reduced value before the I/O thread finished updating the value\. The I/O thread then wrote its original size calculation, ignoring the SQL thread’s update and so adding back the space for the deleted file\. The Relay\_Log\_Space value is now locked during updates to prevent concurrent updates and ensure an accurate calculation\. \(Bug \#26997096, Bug \#87832\) 
+  For an [INSERT](https://dev.mysql.com/doc/refman/5.7/en/insert.html) statement for which the VALUES list produced values for the second or later row using a subquery containing a join, the server could exit after failing to resolve the required privileges\. \(Bug \#23762382\) 
+  For a table having a [TIMESTAMP](https://dev.mysql.com/doc/refman/5.7/en/datetime.html) or [DATETIME](https://dev.mysql.com/doc/refman/5.7/en/datetime.html) column having a default of [CURRENT\_TIMESTAMP](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_current-timestamp), the column could be initialized to `0000-00-00 00:00:00` if the table had a `BEFORE INSERT` trigger\. \(Bug \#25209512, Bug \#84077\) 
+  A server exit could result from simultaneous attempts by multiple threads to register and deregister metadata Performance Schema objects\. \(Bug \#26502135\) 
+  Executing a stored procedure containing a statement that created a table from the contents of certain [SELECT](https://dev.mysql.com/doc/refman/5.7/en/select.html) statements could result in a memory leak\. \(Bug \#25586773\) 
+  Executing a stored procedure containing a query that accessed a view could allocate memory that was not freed until the session ended\. \(Bug \#25053286\) 
+  Certain cases of subquery materialization could cause a server exit\. These queries now produce an error suggesting that materialization be disabled\. \(Bug \#26402045\) 
+  Queries with many left joins were slow if join buffering was used \(for example, using the block nested loop algorithm\)\. \(Bug \#18898433, Bug \#72854\) 
+  The optimizer skipped the second column in a composite index when executing an inner join with a `LIKE` clause against the second column\. \(Bug \#28086754\) 

## Comparison with Aurora MySQL version 1<a name="AuroraMySQL.Updates.2073.Compare56"></a>

The following Amazon Aurora MySQL features are supported in Aurora MySQL Version 1 \(compatible with MySQL 5\.6\), but these features are currently not supported in Aurora MySQL Version 2 \(compatible with MySQL 5\.7\)\.
+ Asynchronous key prefetch \(AKP\)\. For more information, see [Optimizing Amazon Aurora indexed join queries with asynchronous key prefetch](AuroraMySQL.BestPractices.md#Aurora.BestPractices.AKP)\.
+ Hash joins\. For more information, see [Optimizing large Aurora MySQL join queries with hash joins](AuroraMySQL.BestPractices.md#Aurora.BestPractices.HashJoin)\.
+ Native functions for synchronously invoking AWS Lambda functions\. For more information, see [Invoking a Lambda function with an Aurora MySQL native function](AuroraMySQL.Integrating.Lambda.md#AuroraMySQL.Integrating.NativeLambda)\.
+ Scan batching\. For more information, see [Aurora MySQL database engine updates 2017\-12\-11](AuroraMySQL.Updates.20171211.md)\.
+ Migrating data from MySQL using an Amazon S3 bucket\. For more information, see [Migrating data from MySQL by using an Amazon S3 bucket](AuroraMySQL.Migrating.ExtMySQL.md#AuroraMySQL.Migrating.ExtMySQL.S3)\.

## MySQL 5\.7 compatibility<a name="AuroraMySQL.Updates.2073.Compatibility"></a>

This Aurora MySQL version is wire\-compatible with MySQL 5\.7 and includes features such as JSON support, spatial indexes, and generated columns\. Aurora MySQL uses a native implementation of spatial indexing using z\-order curves to deliver >20x better write performance and >10x better read performance than MySQL 5\.7 for spatial datasets\.

This Aurora MySQL version does not currently support the following MySQL 5\.7 features:
+ Group replication plugin
+ Increased page size
+ InnoDB buffer pool loading at startup
+ InnoDB full\-text parser plugin
+ Multisource replication
+ Online buffer pool resizing
+ Password validation plugin
+ Query rewrite plugins
+ Replication filtering
+ The `CREATE TABLESPACE` SQL statement