# Aurora MySQL version 2 compatible with MySQL 5\.7<a name="Aurora.AuroraMySQL.CompareMySQL57"></a>

The following features are supported in MySQL 5\.7\.12 but are currently not supported in Aurora MySQL 5\.7:
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
+ X Protocol

For more information about these features, see the [MySQL 5\.7 documentation](https://dev.mysql.com/doc/refman/5.7/en/)\.

## Comparison of Aurora MySQL 5\.6 and Aurora MySQL 5\.7<a name="Aurora.AuroraMySQL.Compare-56-57"></a>

The following Amazon Aurora MySQL features are supported in Aurora MySQL 5\.6, but these features are currently not supported in Aurora MySQL 5\.7\.
+ Asynchronous key prefetch \(AKP\)\. For more information, see [Optimizing Amazon Aurora indexed join queries with asynchronous key prefetch](AuroraMySQL.BestPractices.md#Aurora.BestPractices.AKP)\.
+ Hash joins\. For more information, see [Optimizing large Aurora MySQL join queries with hash joins](AuroraMySQL.BestPractices.md#Aurora.BestPractices.HashJoin)\.
+ Native functions for synchronously invoking AWS Lambda functions\. You can asynchronously invoke AWS Lambda functions from Aurora MySQL 5\.7\. For more information, see [Invoking a Lambda function with an Aurora MySQL native function](AuroraMySQL.Integrating.Lambda.md#AuroraMySQL.Integrating.NativeLambda)\.
+ Scan batching\. For more information, see [Aurora MySQL database engine updates 2017\-12\-11 \(version 1\.16\) \(deprecated\)](AuroraMySQL.Updates.20171211.md)\.

Currently, Aurora MySQL 5\.7 does not support features added in Aurora MySQL version 1\.16 and later\. For information about Aurora MySQL version 1\.16, see [Aurora MySQL database engine updates 2017\-12\-11 \(version 1\.16\) \(deprecated\)](AuroraMySQL.Updates.20171211.md)\.

The performance schema isn't available for early releases of Aurora MySQL 5\.7\. Upgrade to Aurora 2\.03 or higher for performance schema support\.