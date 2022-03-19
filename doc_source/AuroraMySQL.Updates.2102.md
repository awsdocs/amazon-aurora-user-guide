# Aurora MySQL database engine updates 2022\-01\-26 \(version 2\.10\.2\)<a name="AuroraMySQL.Updates.2102"></a><a name="2102"></a><a name="2.10.2"></a>

 **Version:** 2\.10\.2 

 Aurora MySQL 2\.10\.2 is generally available\. Aurora MySQL 2\.x versions are compatible with MySQL 5\.7, and Aurora MySQL 1\.x versions are compatible with MySQL 5\.6\. 

 If you have any questions or concerns, AWS Support is available on the community forums and through [AWS Premium Support](http://aws.amazon.com/support)\. For more information, see [Maintaining an Amazon Aurora DB cluster](USER_UpgradeDBInstance.Maintenance.md)\. 

## Improvements<a name="AuroraMySQL.Updates.2102.Improvements"></a>

 **Security fixes:** 
+ [CVE\-2021\-35624](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-35624)
+ [CVE\-2021\-35604](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-35604)
+ [CVE\-2021\-2390](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-2390)
+ [CVE\-2021\-2389](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-2389)
+ [CVE\-2021\-2385](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-2385)
+ [CVE\-2021\-2356](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-2356)
+ [CVE\-2019\-17543](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-17543)
+ [CVE\-2019\-2960](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-2960)

 **General improvements:** 
+  Added a performance optimization to help reduce database IO latency in 24XL instance classes\. 
+  Added support for ECDHE SSL ciphers\. For more information on configuring your clients to use these SSL Ciphers please see the following MySQL documentation, [encrypted connection protocols ciphers](https://dev.mysql.com/doc/refman/5.7/en/encrypted-connection-protocols-ciphers.html#encrypted-connection-cipher-configuration) 
+  Fixed security issues related to Aurora MySQL integration with other AWS Services such as Amazon S3, Amazon ML, and AWS Lambda\. 
+  Fixed an issue which can cause a database instance restart to fail when the database has approximately over 1GB of user and privilege combinations\. 
+  Fixed an issue with Parallel Query which could cause the database to return incorrect groupings or sort order when executing queries with a GROUP BY clause and a WHERE clause that contain a range predicate\. 
+  Fixed an issue which causes general\_log and slow\_log tables to become inaccessible after an in\-place major version upgrade from Aurora MySQL 1\.x \(compatible with MySQL 5\.6\) to Aurora MySQL 2\.x \(compatible with MySQL 5\.7\)\. 
+  Fixed an issue which, in rare cases, causes the database instance to restart when innodb\_trx, innodb\_locks or innodb\_lockwaits tables are queried while the database is under heavy workload\. Monitoring tools such as Performance Insights may query such tables\. 
+  Fixed an issue where the value of a TIMESTAMP column of an existing row is updated to the latest timestamp when all of the following conditions are satisfied: 

  1. A trigger exists for the table\.

  1. An INSERT is performed on the table that has an ON DUPLICATE KEY UPDATE clause\.

  1. The inserted row causes a duplicate value violation in a UNIQUE index or PRIMARY KEY\.

  1. One or more columns are of TIMESTAMP data type and have a default value of CURRENT\_TIMESTAMP\.
+  Fixed an issue which, in rare cases, could prevent a binlog replica from connecting to an instance with binlog enabled\. 
+  Fixed an issue where, in rare conditions, transactions were unable to commit when running on an instance with binlog enabled\. 
+  Fixed an issue where new connections could not be established to an instance with binlog enabled\. 
+  Fixed an issue which can cause excessive internal logging when attempting zero downtime patching and restart causing local storage to fill up\. 
+  Fixed an issue that causes a binlog replica to stop with an HA\_ERR\_FOUND\_DUPP\_KEY error when replicating certain DDL and DCL statements\. The issue occurs when the source instance is configured with MIXED binary logging format and READ COMMITTED or READ UNCOMMITTED isolation level\. 
+  Fixed an issue where the binlog replication I/O thread is unable to keep up with the primary instance, when multi\-threaded replication is enabled 
+  Fixed an issue where, in rare conditions, a high number of active connections to the database instance may cause the CloudWatch CommitLatency metric to be incorrectly reported\. 
+  Fixed an issue which causes local storage on Graviton instances to fill up when performing LOAD FROM S3 or SELECT INTO S3\. 
+  Fixed an issue which can cause wrong query results when querying a table with a foreign key and both of the following conditions are met: 

  1.  Query cache is enabled 

  1.  A transaction with a cascading delete or update on that table is rolled back 
+  Fixed an issue which, in rare conditions, can cause Aurora reader instances to restart\. The chance of this issue occurring increases as the number of transaction rollbacks increases\. 
+  Fixed an issue where the number of mutex 'LOCK\_epoch\_id\_master' occurrences in Performance Schema increases when a session is opened and closed\. 
+  Fixed an issue which can cause an increasing number of deadlocks for workloads which have many transactions updating the same set of rows concurrently\. 
+  Fixed an issue which, in rare conditions, can cause the instances to restart when the database volume grows to a multiple of 160GB\. 
+  Fixed an issue with Parallel Query which could cause the database to restart when executing SQL statements with a LIMIT clause\. 
+  Fixed an issue which, in rare conditions, can cause the database instance to restart when using XA transactions with the READ COMMITTED isolation level\. 
+  Fixed an issue where, after an Aurora Read instance restarts, it may restart again if there is a heavy DDL workload during the restart\. 
+  Fixed an issue with incorrect reporting of Aurora reader replication lag\. 
+  Fixed an issue which, in rare conditions, can cause a writer instance to restart when an in\-memory data\-integrity check fails\. 
+  Fixed an issue which, in rare conditions, incorrectly shows the “Database Load” chart in Performance Insights \(PI\) sessions as actively using CPU even though the sessions have finished processing and are idle\. 
+  Fixed an issue which, in rare conditions, can cause the database server to restart when a query is processed using Parallel Query\. 
+  Fixed an issue which, in rare conditions, can cause the writer instance in a primary Global Database cluster to restart because of a race condition during Global Database replication\. 
+  Fixed an issue that can occur during a database instance restart, which can cause more than one restart\. 

## Integration of MySQL Community Edition bug fixes<a name="AuroraMySQL.Updates.2102.Patches"></a>
+  Fixed an issue in InnoDB where an error in code related to table statistics raised an assertion in the dict0stats\.cc source file\. \(Bug \#24585978\) 
+  Fixed an issue where a secondary index over a virtual column became corrupted when the index was built online\. For [UPDATE](https://dev.mysql.com/doc/refman/5.7/en/update.html) statements, we fix this as follows: If the virtual column value of the index record is set to NULL, then we generate this value from the cluster index record\. \(Bug \#30556595\)\) 
+  Fixed an issue in InnoDB where deleting marked rows were able to acquire an external read lock before a partial rollback was completed\. The external read lock prevented conversion of an implicit lock to an explicit lock during the partial rollback, causing an assertion failure\. \(Bug \#29195848\) 
+  Fixed an issue where the empty host names in accounts could cause the server to misbehave\. \(Bug \#28653104\) 
+  Fixed an issue in InnoDB where a query interruption during a lock wait caused an error\. \(Bug \#28068293\) 
+  Fixed an issue in replication where Interleaved transactions could sometimes deadlock the slave applier when the transaction isolation level was set to [REPEATABLE READ](https://dev.mysql.com/doc/refman/5.7/en/innodb-transaction-isolation-levels.html#isolevel_repeatable-read)\. \(Bug \#25040331\) 
+  Fixed an issue which can cause binlog replicas to stall due to lock wait timeout\. \(Bug \#27189701\) 

## Comparison with Aurora MySQL version 1<a name="AuroraMySQL.Updates.2102.Compare56"></a>

The following Amazon Aurora MySQL features are supported in Aurora MySQL version 1 \(compatible with MySQL 5\.6\), but these features are currently not supported in Aurora MySQL version 2 \(compatible with MySQL 5\.7\)\.
+ Asynchronous key prefetch \(AKP\)\. For more information, see [Optimizing Amazon Aurora indexed join queries with asynchronous key prefetch](AuroraMySQL.BestPractices.md#Aurora.BestPractices.AKP)\.
+ Hash joins\. For more information, see [Optimizing large Aurora MySQL join queries with hash joins](AuroraMySQL.BestPractices.md#Aurora.BestPractices.HashJoin)\.
+ Native functions for synchronously invoking AWS Lambda functions\. For more information, see [Invoking a Lambda function with an Aurora MySQL native function](AuroraMySQL.Integrating.Lambda.md#AuroraMySQL.Integrating.NativeLambda)\.
+ Scan batching\. For more information, see [Aurora MySQL database engine updates 2017\-12\-11 \(version 1\.16\) \(deprecated\)](AuroraMySQL.Updates.20171211.md)\.
+ Migrating data from MySQL using an Amazon S3 bucket\. For more information, see [Migrating data from MySQL by using an Amazon S3 bucket](AuroraMySQL.Migrating.ExtMySQL.md#AuroraMySQL.Migrating.ExtMySQL.S3)\.

## MySQL 5\.7 compatibility<a name="AuroraMySQL.Updates.2102.Compatibility"></a>

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