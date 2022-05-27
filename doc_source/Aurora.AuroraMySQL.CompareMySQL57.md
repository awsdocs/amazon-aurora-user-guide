# Aurora MySQL version 2 compatible with MySQL 5\.7<a name="Aurora.AuroraMySQL.CompareMySQL57"></a>

The following features are supported in MySQL 5\.7\.12 but are currently not supported in Aurora MySQL version 2:
+ `CREATE TABLESPACE` SQL statement
+ Group replication plugin
+ Increased page size
+ InnoDB buffer pool loading at startup
+ InnoDB full\-text parser plugin
+ Multisource replication
+ Online buffer pool resizing
+ Password validation plugin – You can install the plugin, but it isn't supported\. You can't customize the plugin\.
+ Query rewrite plugins
+ Replication filtering
+ X Protocol

For more information about these features, see the [MySQL 5\.7 documentation](https://dev.mysql.com/doc/refman/5.7/en/)\.

## Comparison of Aurora MySQL version 1 and Aurora MySQL version 2<a name="Aurora.AuroraMySQL.Compare-56-57"></a>

The following Amazon Aurora MySQL features are supported in Aurora MySQL version 1, but currently aren't supported in Aurora MySQL version 2\.
+ Asynchronous key prefetch \(AKP\)\. For more information, see [Optimizing Amazon Aurora indexed join queries with asynchronous key prefetch](AuroraMySQL.BestPractices.md#Aurora.BestPractices.AKP)\.
+ Scan batching\. For more information, see [ Aurora MySQL database engine updates 2017\-12\-11](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraMySQLReleaseNotes/AuroraMySQL.Updates.20171211.html) in the *Release Notes for Aurora MySQL*\.

Currently, Aurora MySQL version 2 doesn't support features added in Aurora MySQL version 1\.16 and later\. For information about Aurora MySQL version 1\.16, see [ Aurora MySQL database engine updates 2017\-12\-11](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraMySQLReleaseNotes/AuroraMySQL.Updates.20171211.html) in the *Release Notes for Aurora MySQL*\.

The performance schema isn't available for early releases of Aurora MySQL version 2\. Upgrade to Aurora 2\.03 or higher for performance schema support\.