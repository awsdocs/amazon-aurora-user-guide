# Amazon Aurora PostgreSQL parameters<a name="AuroraPostgreSQL.Reference.ParameterGroups"></a>

You manage your Amazon Aurora DB cluster in the same way that you manage Amazon RDS DB instances, by using parameters in a DB parameter group\. However, Amazon Aurora differs from Amazon RDS in that an Aurora DB cluster has multiple DB instances\. Some of the parameters that you use to manage your Amazon Aurora DB cluster apply to the entire cluster, while other parameters apply only to a given DB instance in the DB cluster, as follows:
+ **DB cluster parameter group** – A DB cluster parameter group contains the set of engine configuration parameters that apply throughout the Aurora DB cluster\. For example, cluster cache management is a feature of an Aurora DB cluster that's controlled by the `apg_ccm_enabled` parameter which is part of the DB cluster parameter group\. The DB cluster parameter group also contains default settings for the DB parameter group for the DB instances that make up the cluster\. 
+ **DB parameter group** – A DB parameter group is the set of engine configuration values that apply to a specific DB instance of that engine type\. The DB parameter groups for the PostgreSQL DB engine are used by an RDS for PostgreSQL DB instance\. and Aurora PostgreSQL DB cluster\. These configuration settings apply to properties that can vary among the DB instances within an Aurora cluster, such as the sizes for memory buffers\. 

You manage cluster\-level parameters in DB cluster parameter groups\. You manage instance\-level parameters in DB parameter groups\. You can manage parameters using the Amazon RDS console, the AWS CLI, or the Amazon RDS API\. There are separate commands for managing cluster\-level parameters and instance\-level parameters\.
+ To manage cluster\-level parameters in a DB cluster parameter group, use the [modify\-db\-cluster\-parameter\-group](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster-parameter-group.html) AWS CLI command\.
+ To manage instance\-level parameters in a DB parameter group for a DB instance in a DB cluster, use the [modify\-db\-parameter\-group](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-parameter-group.html) AWS CLI command\.

To learn more about the AWS CLI, see [Using the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-using.html) in the *AWS Command Line Interface User Guide*\.

For more information about parameter groups, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\.

## Viewing Aurora PostgreSQL DB cluster and DB parameters<a name="AuroraPostgreSQL.Reference.ParameterGroups-viewing-parameters"></a>

You can view all available default parameter groups for RDS for PostgreSQL DB instances and for Aurora PostgreSQL DB clusters in the AWS Management Console\. The default parameter groups for all DB engines and DB cluster types and versions are listed for each AWS Region\. Any custom parameter groups are also listed\. 

Rather than viewing in the AWS Management Console, you can also list parameters contained in DB cluster parameter groups and DB parameter groups by using the AWS CLI or the Amazon RDS API\. For example, to list parameters in a DB cluster parameter group you use the [describe\-db\-cluster\-parameters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-cluster-parameters.html) AWS CLI command as follows:

```
aws rds describe-db-cluster-parameters --db-cluster-parameter-group-name default.aurora-postgresql12
```

The command returns detailed JSON descriptions of each parameter\. To reduce the amount of information returned, you can specify what you want by using the `--query` option\. For example, you can get the parameter name, its description, and allowed values for the default Aurora PostgreSQL 12 DB cluster parameter group as follows: 

For Linux, macOS, or Unix:

```
aws rds describe-db-cluster-parameters --db-cluster-parameter-group-name default.aurora-postgresql12 \
    --query 'Parameters[].[{ParameterName:ParameterName,Description:Description,ApplyType:ApplyType,AllowedValues:AllowedValues}]'
```

For Windows:

```
aws rds describe-db-cluster-parameters --db-cluster-parameter-group-name default.aurora-postgresql12 ^
    --query "Parameters[].[{ParameterName:ParameterName,Description:Description,ApplyType:ApplyType,AllowedValues:AllowedValues}]"
```

An Aurora DB cluster parameter group includes the DB instance parameter group and default values for a given Aurora DB engine\. You can get the list of DB parameters from the same default Aurora PostgreSQL default parameter group by usng the [describe\-db\-parameters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-parameters.html) AWS CLI command as shown following\.

For Linux, macOS, or Unix:

```
aws rds describe-db-parameters --db-parameter-group-name default.aurora-postgresql12 \
    --query 'Parameters[].[{ParameterName:ParameterName,Description:Description,ApplyType:ApplyType,AllowedValues:AllowedValues}]'
```

For Windows:

```
aws rds describe-db-parameters --db-parameter-group-name default.aurora-postgresql12 ^
    --query "Parameters[].[{ParameterName:ParameterName,Description:Description,ApplyType:ApplyType,AllowedValues:AllowedValues}]"
```

The preceding commands return lists of parameters from the DB cluster or DB parameter group with descriptions and other details specified in the query\. Following is an example response\.

```
[
    [
        {
            "ParameterName": "apg_enable_batch_mode_function_execution",
            "ApplyType": "dynamic",
            "Description": "Enables batch-mode functions to process sets of rows at a time.",
            "AllowedValues": "0,1"
        }
    ],
    [
        {
            "ParameterName": "apg_enable_correlated_any_transform",
            "ApplyType": "dynamic",
            "Description": "Enables the planner to transform correlated ANY Sublink (IN/NOT IN subquery) to JOIN when possible.",
            "AllowedValues": "0,1"
        }
    ],...
```

Following are tables containing values for the default DB cluster parameter and DB parameter for Aurora PostgreSQL version 14\.

## Aurora PostgreSQL cluster\-level parameters<a name="AuroraPostgreSQL.Reference.Parameters.Cluster"></a>

The following table lists some of parameters available in the default DB cluster parameter group for Aurora PostgreSQL version 14\. If you create an Aurora PostgreSQL DB cluster without specifying your own custom DB parameter group, your DB cluster is created using the default Aurora DB cluster parameter group for the version chosen, such as `default.aurora-postgresql14`, `default.aurora-postgresql13`, and so on\. 

For a listing of the DB instance parameters for this same default DB cluster parameter group, see [Aurora PostgreSQL instance\-level parameters](#AuroraPostgreSQL.Reference.Parameters.Instance)\.


| Parameter name | Description | Default | 
| --- | --- | --- | 
| ansi\_constraint\_trigger\_ordering | Change the firing order of constraint triggers to be compatible with the ANSI SQL standard\. | –  | 
| ansi\_force\_foreign\_key\_checks | Ensure referential actions such as cascaded delete or cascaded update will always occur regardless of the various trigger contexts that exist for the action\. | –  | 
| ansi\_qualified\_update\_set\_target | Support table and schema qualifiers in UPDATE \.\.\. SET statements\. | –  | 
| apg\_ccm\_enabled | Enable or disable cluster cache management for the cluster\. | –  | 
| apg\_enable\_batch\_mode\_function\_execution | Enables batch\-mode functions to process sets of rows at a time\. | –  | 
| apg\_enable\_correlated\_any\_transform | Enables the planner to transform correlated ANY Sublink \(IN/NOT IN subquery\) to JOIN when possible\. | –  | 
| apg\_enable\_function\_migration | Enables the planner to migrate eligible scalar functions to the FROM clause\. | –  | 
| apg\_enable\_not\_in\_transform | Enables the planner to transform NOT IN subquery to ANTI JOIN when possible\. | –  | 
| apg\_enable\_remove\_redundant\_inner\_joins | Enables the planner to remove redundant inner joins\. | –  | 
| apg\_enable\_semijoin\_push\_down | Enables the use of semijoin filters for hash joins\. | –  | 
| apg\_plan\_mgmt\.capture\_plan\_baselines | Capture plan baseline mode\. manual \- enable plan capture for any SQL statement, off \- disable plan capture, automatic \- enable plan capture for for statements in pg\_stat\_statements that satisfy the eligibility criteria\. | off  | 
| apg\_plan\_mgmt\.max\_databases | Sets the maximum number of databases that that may manage queries using apg\_plan\_mgmt\. | 10  | 
| apg\_plan\_mgmt\.max\_plans | Sets the maximum number of plans that may be cached by apg\_plan\_mgmt\. | 10000  | 
| apg\_plan\_mgmt\.plan\_retention\_period | Maximum number of days since a plan was last\_used before a plan will be automatically deleted\. | 32  | 
| apg\_plan\_mgmt\.unapproved\_plan\_execution\_threshold | Estimated total plan cost below which an Unapproved plan will be executed\. | 0  | 
| apg\_plan\_mgmt\.use\_plan\_baselines | Use only approved or fixed plans for managed statements\. | false  | 
| application\_name | Sets the application name to be reported in statistics and logs\. | –  | 
| array\_–s | Enable input of – elements in arrays\. | –  | 
| authentication\_timeout | \(s\) Sets the maximum allowed time to complete client authentication\. | –  | 
| auto\_explain\.log\_analyze | Use EXPLAIN ANALYZE for plan logging\. | –  | 
| auto\_explain\.log\_buffers | Log buffers usage\. | –  | 
| auto\_explain\.log\_format | EXPLAIN format to be used for plan logging\. | –  | 
| auto\_explain\.log\_min\_duration | Sets the minimum execution time above which plans will be logged\. | –  | 
| auto\_explain\.log\_nested\_statements | Log nested statements\. | –  | 
| auto\_explain\.log\_timing | Collect timing data, not just row counts\. | –  | 
| auto\_explain\.log\_triggers | Include trigger statistics in plans\. | –  | 
| auto\_explain\.log\_verbose | Use EXPLAIN VERBOSE for plan logging\. | –  | 
| auto\_explain\.sample\_rate | Fraction of queries to process\. | –  | 
| autovacuum | Starts the autovacuum subprocess\. | –  | 
| autovacuum\_analyze\_scale\_factor | Number of tuple inserts, updates or deletes prior to analyze as a fraction of reltuples\. | 0\.05  | 
| autovacuum\_analyze\_threshold | Minimum number of tuple inserts, updates or deletes prior to analyze\. | –  | 
| autovacuum\_freeze\_max\_age | Age at which to autovacuum a table to prevent transaction ID wraparound\. | –  | 
| autovacuum\_max\_workers | Sets the maximum number of simultaneously running autovacuum worker processes\. | GREATEST\(DBInstanceClassMemory/64371566592,3\)  | 
| autovacuum\_multixact\_freeze\_max\_age | Multixact age at which to autovacuum a table to prevent multixact wraparound\. | –  | 
| autovacuum\_naptime | \(s\) Time to sleep between autovacuum runs\. | 5  | 
| autovacuum\_vacuum\_cost\_delay | \(ms\) Vacuum cost delay in milliseconds, for autovacuum\. | 5  | 
| autovacuum\_vacuum\_cost\_limit | Vacuum cost amount available before napping, for autovacuum\. | GREATEST\(log\(DBInstanceClassMemory/21474836480\)\*600,200\)  | 
| autovacuum\_vacuum\_insert\_scale\_factor | Number of tuple inserts prior to vacuum as a fraction of reltuples\. | –  | 
| autovacuum\_vacuum\_insert\_threshold | Minimum number of tuple inserts prior to vacuum, or \-1 to disable insert vacuums\. | –  | 
| autovacuum\_vacuum\_scale\_factor | Number of tuple updates or deletes prior to vacuum as a fraction of reltuples\. | 0\.1  | 
| autovacuum\_vacuum\_threshold | Minimum number of tuple updates or deletes prior to vacuum\. | –  | 
| autovacuum\_work\_mem | \(kB\) Sets the maximum memory to be used by each autovacuum worker process\. | GREATEST\(DBInstanceClassMemory/32768,131072\)  | 
| babelfishpg\_tds\.default\_server\_name | Default Babelfish server name | Microsoft SQL Server  | 
| babelfishpg\_tds\.listen\_addresses | Sets the host name or IP address\(es\) to listen TDS to\. | \*  | 
| babelfishpg\_tds\.port | Sets the TDS TCP port the server listens on\. | 1433  | 
| babelfishpg\_tds\.tds\_debug\_log\_level | Sets logging level in TDS, 0 disables logging | 1  | 
| babelfishpg\_tds\.tds\_default\_numeric\_precision | Sets the default precision of numeric type to be sent in the TDS column metadata if the engine does not specify one\. | 38  | 
| babelfishpg\_tds\.tds\_default\_numeric\_scale | Sets the default scale of numeric type to be sent in the TDS column metadata if the engine does not specify one\. | 8  | 
| babelfishpg\_tds\.tds\_default\_packet\_size | Sets the default packet size for all the SQL Server clients being connected | 4096  | 
| babelfishpg\_tds\.tds\_default\_protocol\_version | Sets a default TDS protocol version for all the clients being connected | DEFAULT  | 
| babelfishpg\_tds\.tds\_ssl\_encrypt | Sets the SSL Encryption option | 0  | 
| babelfishpg\_tds\.tds\_ssl\_max\_protocol\_version | Sets the maximum SSL/TLS protocol version to use for tds session\. | TLSv1\.2  | 
| babelfishpg\_tds\.tds\_ssl\_min\_protocol\_version | Sets the minimum SSL/TLS protocol version to use for tds session\. | TLSv1  | 
| babelfishpg\_tsql\.default\_locale | Default locale to be used for collations created by CREATE COLLATION\. | en\-US  | 
| babelfishpg\_tsql\.migration\_mode | Defines if multiple user databases are supported | single\-db  | 
| babelfishpg\_tsql\.server\_collation\_name | Name of the default server collation | sql\_latin1\_general\_cp1\_ci\_as  | 
| babelfishpg\_tsql\.version | Sets the output of @@VERSION variable | default  | 
| backend\_flush\_after | \(8Kb\) Number of pages after which previously performed writes are flushed to disk\. | –  | 
| backslash\_quote | Sets whether \\\\ is allowed in string literals\. | –  | 
| backtrace\_functions | Log backtrace for errors in these functions\. | –  | 
| bytea\_output | Sets the output format for bytea\. | –  | 
| check\_function\_bodies | Check function bodies during CREATE FUNCTION\. | –  | 
| client\_connection\_check\_interval | Sets the time interval between checks for disconnection while running queries\. | –  | 
| client\_encoding | Sets the clients character set encoding\. | UTF8  | 
| client\_min\_messages | Sets the message levels that are sent to the client\. | –  | 
| compute\_query\_id | Compute query identifiers\. | auto  | 
| config\_file | Sets the servers main configuration file\. | /rdsdbdata/config/postgresql\.conf  | 
| constraint\_exclusion | Enables the planner to use constraints to optimize queries\. | –  | 
| cpu\_index\_tuple\_cost | Sets the planners estimate of the cost of processing each index entry during an index scan\. | –  | 
| cpu\_operator\_cost | Sets the planners estimate of the cost of processing each operator or function call\. | –  | 
| cpu\_tuple\_cost | Sets the planners estimate of the cost of processing each tuple \(row\)\. | –  | 
| cron\.database\_name | Sets the database to store pg\_cron metadata tables | postgres  | 
| cron\.log\_run | Log all jobs runs into the job\_run\_details table | on  | 
| cron\.log\_statement | Log all cron statements prior to execution\. | off  | 
| cron\.max\_running\_jobs | Maximum number of jobs that can run concurrently\. | 5  | 
| cron\.use\_background\_workers | Enables background workers for pg\_cron | on  | 
| cursor\_tuple\_fraction | Sets the planners estimate of the fraction of a cursors rows that will be retrieved\. | –  | 
| data\_directory | Sets the servers data directory\. | /rdsdbdata/db  | 
| datestyle | Sets the display format for date and time values\. | –  | 
| db\_user\_namespace | Enables per\-database user names\. | –  | 
| deadlock\_timeout | \(ms\) Sets the time to wait on a lock before checking for deadlock\. | –  | 
| debug\_pretty\_print | Indents parse and plan tree displays\. | –  | 
| debug\_print\_parse | Logs each querys parse tree\. | –  | 
| debug\_print\_plan | Logs each querys execution plan\. | –  | 
| debug\_print\_rewritten | Logs each querys rewritten parse tree\. | –  | 
| default\_statistics\_target | Sets the default statistics target\. | –  | 
| default\_tablespace | Sets the default tablespace to create tables and indexes in\. | –  | 
| default\_toast\_compression | Sets the default compression method for compressible values\. | –  | 
| default\_transaction\_deferrable | Sets the default deferrable status of new transactions\. | –  | 
| default\_transaction\_isolation | Sets the transaction isolation level of each new transaction\. | –  | 
| default\_transaction\_read\_only | Sets the default read\-only status of new transactions\. | –  | 
| effective\_cache\_size | \(8kB\) Sets the planners assumption about the size of the disk cache\. | SUM\(DBInstanceClassMemory/12038,\-50003\)  | 
| effective\_io\_concurrency | Number of simultaneous requests that can be handled efficiently by the disk subsystem\. | –  | 
| enable\_async\_append | Enables the planners use of async append plans\. | –  | 
| enable\_bitmapscan | Enables the planners use of bitmap\-scan plans\. | –  | 
| enable\_gathermerge | Enables the planners use of gather merge plans\. | –  | 
| enable\_hashagg | Enables the planners use of hashed aggregation plans\. | –  | 
| enable\_hashjoin | Enables the planners use of hash join plans\. | –  | 
| enable\_incremental\_sort | Enables the planners use of incremental sort steps\. | –  | 
| enable\_indexonlyscan | Enables the planners use of index\-only\-scan plans\. | –  | 
| enable\_indexscan | Enables the planners use of index\-scan plans\. | –  | 
| enable\_material | Enables the planners use of materialization\. | –  | 
| enable\_memoize | Enables the planners use of memoization | –  | 
| enable\_mergejoin | Enables the planners use of merge join plans\. | –  | 
| enable\_nestloop | Enables the planners use of nested\-loop join plans\. | –  | 
| enable\_parallel\_append | Enables the planners use of parallel append plans\. | –  | 
| enable\_parallel\_hash | Enables the planners user of parallel hash plans\. | –  | 
| enable\_partition\_pruning | Enable plan\-time and run\-time partition pruning\. | –  | 
| enable\_partitionwise\_aggregate | Enables partitionwise aggregation and grouping\. | –  | 
| enable\_partitionwise\_join | Enables partitionwise join\. | –  | 
| enable\_seqscan | Enables the planners use of sequential\-scan plans\. | –  | 
| enable\_sort | Enables the planners use of explicit sort steps\. | –  | 
| enable\_tidscan | Enables the planners use of TID scan plans\. | –  | 
| escape\_string\_warning | Warn about backslash escapes in ordinary string literals\. | –  | 
| exit\_on\_error | Terminate session on any error\. | –  | 
| extra\_float\_digits | Sets the number of digits displayed for floating\-point values\. | –  | 
| force\_parallel\_mode | Forces use of parallel query facilities\. | –  | 
| from\_collapse\_limit | Sets the FROM\-list size beyond which subqueries are not collapsed\. | –  | 
| geqo | Enables genetic query optimization\. | –  | 
| geqo\_effort | GEQO: effort is used to set the default for other GEQO parameters\. | –  | 
| geqo\_generations | GEQO: number of iterations of the algorithm\. | –  | 
| geqo\_pool\_size | GEQO: number of individuals in the population\. | –  | 
| geqo\_seed | GEQO: seed for random path selection\. | –  | 
| geqo\_selection\_bias | GEQO: selective pressure within the population\. | –  | 
| geqo\_threshold | Sets the threshold of FROM items beyond which GEQO is used\. | –  | 
| gin\_fuzzy\_search\_limit | Sets the maximum allowed result for exact search by GIN\. | –  | 
| gin\_pending\_list\_limit | \(kB\) Sets the maximum size of the pending list for GIN index\. | –  | 
| hash\_mem\_multiplier | Multiple of work\_mem to use for hash tables\. | –  | 
| hba\_file | Sets the servers hba configuration file\. | /rdsdbdata/config/pg\_hba\.conf  | 
| hot\_standby\_feedback | Allows feedback from a hot standby to the primary that will avoid query conflicts\. | on  | 
| huge\_pages | Use of huge pages on Linux\. | on  | 
| ident\_file | Sets the servers ident configuration file\. | /rdsdbdata/config/pg\_ident\.conf  | 
| idle\_in\_transaction\_session\_timeout | \(ms\) Sets the maximum allowed duration of any idling transaction\. | 86400000  | 
| idle\_session\_timeout | Terminate any session that has been idle \(that is, waiting for a client query\), but not within an open transaction, for longer than the specified amount of time | –  | 
| intervalstyle | Sets the display format for interval values\. | –  | 
| join\_collapse\_limit | Sets the FROM\-list size beyond which JOIN constructs are not flattened\. | –  | 
| krb\_caseins\_users  | Sets whether GSSAPI \(Generic Security Service API\) user names should be treated case\-insensitively \(true\) or not\. By default, this parameter is set to false, so Kerberos expects user names to be case sensitive\. For more information, see [GSSAPI Authentication](https://www.postgresql.org/docs/current/gssapi-auth.html) in the PostgreSQL documentation\.  | false | 
| lc\_messages | Sets the language in which messages are displayed\. | –  | 
| lc\_monetary | Sets the locale for formatting monetary amounts\. | –  | 
| lc\_numeric | Sets the locale for formatting numbers\. | –  | 
| lc\_time | Sets the locale for formatting date and time values\. | –  | 
| listen\_addresses | Sets the host name or IP address\(es\) to listen to\. | \*  | 
| lo\_compat\_privileges | Enables backward compatibility mode for privilege checks on large objects\. | 0  | 
| log\_autovacuum\_min\_duration | \(ms\) Sets the minimum execution time above which autovacuum actions will be logged\. | 10000  | 
| log\_connections | Logs each successful connection\. | –  | 
| log\_destination | Sets the destination for server log output\. | stderr  | 
| log\_directory | Sets the destination directory for log files\. | /rdsdbdata/log/error  | 
| log\_disconnections | Logs end of a session, including duration\. | –  | 
| log\_duration | Logs the duration of each completed SQL statement\. | –  | 
| log\_error\_verbosity | Sets the verbosity of logged messages\. | –  | 
| log\_executor\_stats | Writes executor performance statistics to the server log\. | –  | 
| log\_file\_mode | Sets the file permissions for log files\. | 0644  | 
| log\_filename | Sets the file name pattern for log files\. | postgresql\.log\.%Y\-%m\-%d\-%H%M  | 
| logging\_collector | Start a subprocess to capture stderr output and/or csvlogs into log files\. | 1  | 
| log\_hostname | Logs the host name in the connection logs\. | 0  | 
| logical\_decoding\_work\_mem | \(kB\) This much memory can be used by each internal reorder buffer before spilling to disk\. | –  | 
| log\_line\_prefix | Controls information prefixed to each log line\. | %t:%r:%u@%d:%p\]:  | 
| log\_lock\_waits | Logs long lock waits\. | –  | 
| log\_min\_duration\_sample | \(ms\) Sets the minimum execution time above which a sample of statements will be logged\. Sampling is determined by log\_statement\_sample\_rate\. | –  | 
| log\_min\_duration\_statement | \(ms\) Sets the minimum execution time above which statements will be logged\. | –  | 
| log\_min\_error\_statement | Causes all statements generating error at or above this level to be logged\. | –  | 
| log\_min\_messages | Sets the message levels that are logged\. | –  | 
| log\_parameter\_max\_length | \(B\) When logging statements, limit logged parameter values to first N bytes\. | –  | 
| log\_parameter\_max\_length\_on\_error | \(B\) When reporting an error, limit logged parameter values to first N bytes\. | –  | 
| log\_parser\_stats | Writes parser performance statistics to the server log\. | –  | 
| log\_planner\_stats | Writes planner performance statistics to the server log\. | –  | 
| log\_replication\_commands | Logs each replication command\. | –  | 
| log\_rotation\_age | \(min\) Automatic log file rotation will occur after N minutes\. | 60  | 
| log\_rotation\_size | \(kB\) Automatic log file rotation will occur after N kilobytes\. | 100000  | 
| log\_statement | Sets the type of statements logged\. | –  | 
| log\_statement\_sample\_rate | Fraction of statements exceeding log\_min\_duration\_sample to be logged\. | –  | 
| log\_statement\_stats | Writes cumulative performance statistics to the server log\. | –  | 
| log\_temp\_files | \(kB\) Log the use of temporary files larger than this number of kilobytes\. | –  | 
| log\_timezone | Sets the time zone to use in log messages\. | UTC  | 
| log\_transaction\_sample\_rate | Set the fraction of transactions to log for new transactions\. | –  | 
| log\_truncate\_on\_rotation | Truncate existing log files of same name during log rotation\. | 0  | 
| maintenance\_io\_concurrency | A variant of effective\_io\_concurrency that is used for maintenance work\. | 1  | 
| maintenance\_work\_mem | \(kB\) Sets the maximum memory to be used for maintenance operations\. | GREATEST\(DBInstanceClassMemory/63963136\*1024,65536\)  | 
| max\_connections | Sets the maximum number of concurrent connections\. | LEAST\(DBInstanceClassMemory/9531392,5000\)  | 
| max\_files\_per\_process | Sets the maximum number of simultaneously open files for each server process\. | –  | 
| max\_locks\_per\_transaction | Sets the maximum number of locks per transaction\. | 64  | 
| max\_logical\_replication\_workers | Maximum number of logical replication worker processes\. | –  | 
| max\_parallel\_maintenance\_workers | Sets the maximum number of parallel processes per maintenance operation\. | –  | 
| max\_parallel\_workers | Sets the maximum number of parallel workers than can be active at one time\. | GREATEST\($DBInstanceVCPU/2,8\)  | 
| max\_parallel\_workers\_per\_gather | Sets the maximum number of parallel processes per executor node\. | –  | 
| max\_pred\_locks\_per\_page | Sets the maximum number of predicate\-locked tuples per page\. | –  | 
| max\_pred\_locks\_per\_relation | Sets the maximum number of predicate\-locked pages and tuples per relation\. | –  | 
| max\_pred\_locks\_per\_transaction | Sets the maximum number of predicate locks per transaction\. | –  | 
| max\_prepared\_transactions | Sets the maximum number of simultaneously prepared transactions\. | 0  | 
| max\_replication\_slots | Sets the maximum number of replication slots that the server can support\. | 20  | 
| max\_slot\_wal\_keep\_size | \(MB\) Replication slots will be marked as failed, and segments released for deletion or recycling, if this much space is occupied by WAL on disk\. | –  | 
| max\_stack\_depth | \(kB\) Sets the maximum stack depth, in kilobytes\. | 6144  | 
| max\_standby\_streaming\_delay | \(ms\) Sets the maximum delay before canceling queries when a hot standby server is processing streamed WAL data\. | 14000  | 
| max\_sync\_workers\_per\_subscription | Maximum number of synchronization workers per subscription | 2  | 
| max\_wal\_senders | Sets the maximum number of simultaneously running WAL sender processes\. | 10  | 
| max\_worker\_processes | Sets the maximum number of concurrent worker processes\. | GREATEST\($DBInstanceVCPU\*2,8\)  | 
| min\_dynamic\_shared\_memory | \(MB\) Amount of dynamic shared memory reserved at startup\. | –  | 
| min\_parallel\_index\_scan\_size | \(8kB\) Sets the minimum amount of index data for a parallel scan\. | –  | 
| min\_parallel\_table\_scan\_size | \(8kB\) Sets the minimum amount of table data for a parallel scan\. | –  | 
| old\_snapshot\_threshold | \(min\) Time before a snapshot is too old to read pages changed after the snapshot was taken\. | –  | 
| orafce\.nls\_date\_format | Emulate oracles date output behaviour\. | –  | 
| orafce\.timezone | Specify timezone used for sysdate function\. | –  | 
| parallel\_leader\_participation | Controls whether Gather and Gather Merge also run subplans\. | –  | 
| parallel\_setup\_cost | Sets the planners estimate of the cost of starting up worker processes for parallel query\. | –  | 
| parallel\_tuple\_cost | Sets the planners estimate of the cost of passing each tuple \(row\) from worker to master backend\. | –  | 
| password\_encryption | Encrypt passwords\. | –  | 
| pgaudit\.log | Specifies which classes of statements will be logged by session audit logging\. | –  | 
| pgaudit\.log\_catalog | Specifies that session logging should be enabled in the case where all relations in a statement are in pg\_catalog\. | –  | 
| pgaudit\.log\_level | Specifies the log level that will be used for log entries\. | –  | 
| pgaudit\.log\_parameter | Specifies that audit logging should include the parameters that were passed with the statement\. | –  | 
| pgaudit\.log\_relation | Specifies whether session audit logging should create a separate log entry for each relation \(TABLE, VIEW, etc\.\) referenced in a SELECT or DML statement\. | –  | 
| pgaudit\.log\_statement\_once | Specifies whether logging will include the statement text and parameters with the first log entry for a statement/substatement combination or with every entry\. | –  | 
| pgaudit\.role | Specifies the master role to use for object audit logging\. | –  | 
| pg\_bigm\.enable\_recheck | It specifies whether to perform Recheck which is an internal process of full text search\. | on  | 
| pg\_bigm\.gin\_key\_limit | It specifies the maximum number of 2\-grams of the search keyword to be used for full text search\. | 0  | 
| pg\_bigm\.last\_update | It reports the last updated date of the pg\_bigm module\. | 2013\.11\.22  | 
| pg\_bigm\.similarity\_limit | It specifies the minimum threshold used by the similarity search\. | 0\.3  | 
| pg\_hint\_plan\.debug\_print | Logs results of hint parsing\. | –  | 
| pg\_hint\_plan\.enable\_hint | Force planner to use plans specified in the hint comment preceding to the query\. | –  | 
| pg\_hint\_plan\.enable\_hint\_table | Force planner to not get hint by using table lookups\. | –  | 
| pg\_hint\_plan\.message\_level | Message level of debug messages\. | –  | 
| pg\_hint\_plan\.parse\_messages | Message level of parse errors\. | –  | 
| pglogical\.batch\_inserts | Batch inserts if possible | –  | 
| pglogical\.conflict\_log\_level | Sets log level used for logging resolved conflicts\. | –  | 
| pglogical\.conflict\_resolution | Sets method used for conflict resolution for resolvable conflicts\. | –  | 
| pglogical\.extra\_connection\_options | connection options to add to all peer node connections | –  | 
| pglogical\.synchronous\_commit | pglogical specific synchronous commit value | –  | 
| pglogical\.use\_spi | Use SPI instead of low\-level API for applying changes | –  | 
| pg\_prewarm\.autoprewarm | Starts the autoprewarm worker\. | –  | 
| pg\_prewarm\.autoprewarm\_interval | Sets the interval between dumps of shared buffers | –  | 
| pg\_similarity\.block\_is\_normalized | Sets if the result value is normalized or not\. | –  | 
| pg\_similarity\.block\_threshold | Sets the threshold used by the Block similarity function\. | –  | 
| pg\_similarity\.block\_tokenizer | Sets the tokenizer for Block similarity function\. | –  | 
| pg\_similarity\.cosine\_is\_normalized | Sets if the result value is normalized or not\. | –  | 
| pg\_similarity\.cosine\_threshold | Sets the threshold used by the Cosine similarity function\. | –  | 
| pg\_similarity\.cosine\_tokenizer | Sets the tokenizer for Cosine similarity function\. | –  | 
| pg\_similarity\.dice\_is\_normalized | Sets if the result value is normalized or not\. | –  | 
| pg\_similarity\.dice\_threshold | Sets the threshold used by the Dice similarity measure\. | –  | 
| pg\_similarity\.dice\_tokenizer | Sets the tokenizer for Dice similarity measure\. | –  | 
| pg\_similarity\.euclidean\_is\_normalized | Sets if the result value is normalized or not\. | –  | 
| pg\_similarity\.euclidean\_threshold | Sets the threshold used by the Euclidean similarity measure\. | –  | 
| pg\_similarity\.euclidean\_tokenizer | Sets the tokenizer for Euclidean similarity measure\. | –  | 
| pg\_similarity\.hamming\_is\_normalized | Sets if the result value is normalized or not\. | –  | 
| pg\_similarity\.hamming\_threshold | Sets the threshold used by the Block similarity metric\. | –  | 
| pg\_similarity\.jaccard\_is\_normalized | Sets if the result value is normalized or not\. | –  | 
| pg\_similarity\.jaccard\_threshold | Sets the threshold used by the Jaccard similarity measure\. | –  | 
| pg\_similarity\.jaccard\_tokenizer | Sets the tokenizer for Jaccard similarity measure\. | –  | 
| pg\_similarity\.jaro\_is\_normalized | Sets if the result value is normalized or not\. | –  | 
| pg\_similarity\.jaro\_threshold | Sets the threshold used by the Jaro similarity measure\. | –  | 
| pg\_similarity\.jarowinkler\_is\_normalized | Sets if the result value is normalized or not\. | –  | 
| pg\_similarity\.jarowinkler\_threshold | Sets the threshold used by the Jarowinkler similarity measure\. | –  | 
| pg\_similarity\.levenshtein\_is\_normalized | Sets if the result value is normalized or not\. | –  | 
| pg\_similarity\.levenshtein\_threshold | Sets the threshold used by the Levenshtein similarity measure\. | –  | 
| pg\_similarity\.matching\_is\_normalized | Sets if the result value is normalized or not\. | –  | 
| pg\_similarity\.matching\_threshold | Sets the threshold used by the Matching Coefficient similarity measure\. | –  | 
| pg\_similarity\.matching\_tokenizer | Sets the tokenizer for Matching Coefficient similarity measure\. | –  | 
| pg\_similarity\.mongeelkan\_is\_normalized | Sets if the result value is normalized or not\. | –  | 
| pg\_similarity\.mongeelkan\_threshold | Sets the threshold used by the Monge\-Elkan similarity measure\. | –  | 
| pg\_similarity\.mongeelkan\_tokenizer | Sets the tokenizer for Monge\-Elkan similarity measure\. | –  | 
| pg\_similarity\.nw\_gap\_penalty | Sets the gap penalty used by the Needleman\-Wunsch similarity measure\. | –  | 
| pg\_similarity\.nw\_is\_normalized | Sets if the result value is normalized or not\. | –  | 
| pg\_similarity\.nw\_threshold | Sets the threshold used by the Needleman\-Wunsch similarity measure\. | –  | 
| pg\_similarity\.overlap\_is\_normalized | Sets if the result value is normalized or not\. | –  | 
| pg\_similarity\.overlap\_threshold | Sets the threshold used by the Overlap Coefficient similarity measure\. | –  | 
| pg\_similarity\.overlap\_tokenizer | Sets the tokenizer for Overlap Coefficientsimilarity measure\. | –  | 
| pg\_similarity\.qgram\_is\_normalized | Sets if the result value is normalized or not\. | –  | 
| pg\_similarity\.qgram\_threshold | Sets the threshold used by the Q\-Gram similarity measure\. | –  | 
| pg\_similarity\.qgram\_tokenizer | Sets the tokenizer for Q\-Gram measure\. | –  | 
| pg\_similarity\.swg\_is\_normalized | Sets if the result value is normalized or not\. | –  | 
| pg\_similarity\.swg\_threshold | Sets the threshold used by the Smith\-Waterman\-Gotoh similarity measure\. | –  | 
| pg\_similarity\.sw\_is\_normalized | Sets if the result value is normalized or not\. | –  | 
| pg\_similarity\.sw\_threshold | Sets the threshold used by the Smith\-Waterman similarity measure\. | –  | 
| pg\_stat\_statements\.max | Sets the maximum number of statements tracked by pg\_stat\_statements\. | –  | 
| pg\_stat\_statements\.save | Save pg\_stat\_statements statistics across server shutdowns\. | –  | 
| pg\_stat\_statements\.track | Selects which statements are tracked by pg\_stat\_statements\. | –  | 
| pg\_stat\_statements\.track\_planning | Selects whether planning duration is tracked by pg\_stat\_statements\. | –  | 
| pg\_stat\_statements\.track\_utility | Selects whether utility commands are tracked by pg\_stat\_statements\. | –  | 
| plan\_cache\_mode | Controls the planner selection of custom or generic plan\. | –  | 
| port | Sets the TCP port the server listens on\. | EndPointPort  | 
| postgis\.gdal\_enabled\_drivers | Enable or disable GDAL drivers used with PostGIS in Postgres 9\.3\.5 and above\. | ENABLE\_ALL  | 
| quote\_all\_identifiers | When generating SQL fragments, quote all identifiers\. | –  | 
| random\_page\_cost | Sets the planners estimate of the cost of a nonsequentially fetched disk page\. | –  | 
| rdkit\.dice\_threshold | Lower threshold of Dice similarity\. Molecules with similarity lower than threshold are not similar by \# operation\. | –  | 
| rdkit\.do\_chiral\_sss | Should stereochemistry be taken into account in substructure matching\. If false, no stereochemistry information is used in substructure matches\. | –  | 
| rdkit\.tanimoto\_threshold | Lower threshold of Tanimoto similarity\. Molecules with similarity lower than threshold are not similar by % operation\. | –  | 
| rds\.accepted\_password\_auth\_method | Force authentication for connections with password stored locally\. | md5\+scram  | 
| rds\.adaptive\_autovacuum | RDS parameter to enable/disable adaptive autovacuum\. | 1  | 
| rds\.babelfish\_status | RDS parameter to enable/disable Babelfish for Aurora PostgreSQL\. | off  | 
| rds\.enable\_plan\_management | Enable or disable the apg\_plan\_mgmt extension\. | 0  | 
| rds\.extensions | List of extensions provided by RDS | address\_standardizer, address\_standardizer\_data\_us, apg\_plan\_mgmt, aurora\_stat\_utils, amcheck, autoinc, aws\_commons, aws\_ml, aws\_s3, aws\_lambda, bool\_plperl, bloom, btree\_gin, btree\_gist, citext, cube, dblink, dict\_int, dict\_xsyn, earthdistance, fuzzystrmatch, hll, hstore, hstore\_plperl, insert\_username, intagg, intarray, ip4r, isn, jsonb\_plperl, lo, log\_fdw, ltree, moddatetime, old\_snapshot, oracle\_fdw, orafce, pgaudit, pgcrypto, pglogical, pgrouting, pgrowlocks, pgstattuple, pgtap, pg\_bigm, pg\_buffercache, pg\_cron, pg\_freespacemap, pg\_hint\_plan, pg\_partman, pg\_prewarm, pg\_proctab, pg\_repack, pg\_similarity, pg\_stat\_statements, pg\_trgm, pg\_visibility, plcoffee, plls, plperl, plpgsql, plprofiler, pltcl, plv8, postgis, postgis\_tiger\_geocoder, postgis\_raster, postgis\_topology, postgres\_fdw, prefix, rdkit, rds\_tools, refint, sslinfo, tablefunc, tds\_fdw, test\_parser, tsm\_system\_rows, tsm\_system\_time, unaccent, uuid\-ossp  | 
| rds\.force\_admin\_logging\_level | See log messages for RDS admin user actions in customer databases\. | –  | 
| rds\.force\_autovacuum\_logging\_level | See log messages related to autovacuum operations\. | WARNING  | 
| rds\.force\_ssl | Force SSL connections\. | 0  | 
| rds\.global\_db\_rpo | \(s\) Recovery point objective threshold, in seconds, that blocks user commits when it is violated\. | –  | 
| rds\.logical\_replication | Enables logical decoding\. | 0  | 
| rds\.logically\_replicate\_unlogged\_tables | Unlogged tables are logically replicated\. | 1  | 
| rds\.log\_retention\_period | Amazon RDS will delete PostgreSQL log that are older than N minutes\. | 4320  | 
| rds\.pg\_stat\_ramdisk\_size | Size of the stats ramdisk in MB\. A nonzero value will setup the ramdisk\. | 0  | 
| rds\.rds\_superuser\_reserved\_connections | Sets the number of connection slots reserved for rds\_superusers\. | 2  | 
| rds\.restrict\_password\_commands | restricts password\-related commands to members of rds\_password | –  | 
| rds\.superuser\_variables | List of superuser\-only variables for which we elevate rds\_superuser modification statements\. | session\_replication\_role  | 
| recovery\_init\_sync\_method | Sets the method for synchronizing the data directory before crash recovery\. | syncfs  | 
| remove\_temp\_files\_after\_crash | Remove temporary files after backend crash\. | 0  | 
| restart\_after\_crash | Reinitialize server after backend crash\. | –  | 
| row\_security | Enable row security\. | –  | 
| search\_path | Sets the schema search order for names that are not schema\-qualified\. | –  | 
| seq\_page\_cost | Sets the planners estimate of the cost of a sequentially fetched disk page\. | –  | 
| session\_replication\_role | Sets the sessions behavior for triggers and rewrite rules\. | –  | 
| shared\_buffers | \(8kB\) Sets the number of shared memory buffers used by the server\. | SUM\(DBInstanceClassMemory/12038,\-50003\)  | 
| shared\_preload\_libraries | Lists shared libraries to preload into server\. | pg\_stat\_statements  | 
| ssl | Enables SSL connections\. | 1  | 
| ssl\_ca\_file | Location of the SSL server authority file\. | /rdsdbdata/rds\-metadata/ca\-cert\.pem  | 
| ssl\_cert\_file | Location of the SSL server certificate file\. | /rdsdbdata/rds\-metadata/server\-cert\.pem  | 
| ssl\_ciphers | Sets the list of allowed TLS ciphers to be used on secure connections\. | –  | 
| ssl\_crl\_dir | Location of the SSL certificate revocation list directory\. | /rdsdbdata/rds\-metadata/ssl\_crl\_dir/  | 
| ssl\_key\_file |  Location of the SSL server private key file | /rdsdbdata/rds\-metadata/server\-key\.pem  | 
| ssl\_max\_protocol\_version | Sets the maximum SSL/TLS protocol version allowed | –  | 
| ssl\_min\_protocol\_version | Sets the minimum SSL/TLS protocol version allowed | TLSv1\.2  | 
| standard\_conforming\_strings | Causes \.\.\. strings to treat backslashes literally\. | –  | 
| statement\_timeout | \(ms\) Sets the maximum allowed duration of any statement\. | –  | 
| stats\_temp\_directory | Writes temporary statistics files to the specified directory\. | /rdsdbdata/db/pg\_stat\_tmp  | 
| superuser\_reserved\_connections | Sets the number of connection slots reserved for superusers\. | 3  | 
| synchronize\_seqscans | Enable synchronized sequential scans\. | –  | 
| synchronous\_commit | Sets the current transactions synchronization level\. | on  | 
| tcp\_keepalives\_count | Maximum number of TCP keepalive retransmits\. | –  | 
| tcp\_keepalives\_idle | \(s\) Time between issuing TCP keepalives\. | –  | 
| tcp\_keepalives\_interval | \(s\) Time between TCP keepalive retransmits\. | –  | 
| temp\_buffers | \(8kB\) Sets the maximum number of temporary buffers used by each session\. | –  | 
| temp\_file\_limit | Constrains the total amount disk space in kilobytes that a given PostgreSQL process can use for temporary files, excluding space used for explicit temporary tables | \-1  | 
| temp\_tablespaces | Sets the tablespace\(s\) to use for temporary tables and sort files\. | –  | 
| timezone | Sets the time zone for displaying and interpreting time stamps\. | UTC  | 
| track\_activities | Collects information about executing commands\. | –  | 
| track\_activity\_query\_size | Sets the size reserved for pg\_stat\_activity\.current\_query, in bytes\. | 4096  | 
| track\_commit\_timestamp | Collects transaction commit time\. | –  | 
| track\_counts | Collects statistics on database activity\. | –  | 
| track\_functions | Collects function\-level statistics on database activity\. | pl  | 
| track\_io\_timing | Collects timing statistics on database IO activity\. | 1  | 
| track\_wal\_io\_timing | Collects timing statistics for WAL I/O activity\. | –  | 
| transform\_–\_equals | Treats expr=– as expr IS –\. | –  | 
| update\_process\_title | Updates the process title to show the active SQL command\. | –  | 
| vacuum\_cost\_delay | \(ms\) Vacuum cost delay in milliseconds\. | –  | 
| vacuum\_cost\_limit | Vacuum cost amount available before napping\. | –  | 
| vacuum\_cost\_page\_dirty | Vacuum cost for a page dirtied by vacuum\. | –  | 
| vacuum\_cost\_page\_hit | Vacuum cost for a page found in the buffer cache\. | –  | 
| vacuum\_cost\_page\_miss | Vacuum cost for a page not found in the buffer cache\. | 0  | 
| vacuum\_defer\_cleanup\_age | Number of transactions by which VACUUM and HOT cleanup should be deferred, if any\. | –  | 
| vacuum\_failsafe\_age | Age at which VACUUM should trigger failsafe to avoid a wraparound outage\. | 1200000000  | 
| vacuum\_freeze\_min\_age | Minimum age at which VACUUM should freeze a table row\. | –  | 
| vacuum\_freeze\_table\_age | Age at which VACUUM should scan whole table to freeze tuples\. | –  | 
| vacuum\_multixact\_failsafe\_age | Multixact age at which VACUUM should trigger failsafe to avoid a wraparound outage\. | 1200000000  | 
| vacuum\_multixact\_freeze\_min\_age | Minimum age at which VACUUM should freeze a MultiXactId in a table row\. | –  | 
| vacuum\_multixact\_freeze\_table\_age | Multixact age at which VACUUM should scan whole table to freeze tuples\. | –  | 
| wal\_buffers | \(8kB\) Sets the number of disk\-page buffers in shared memory for WAL\. | –  | 
| wal\_receiver\_create\_temp\_slot | Sets whether a WAL receiver should create a temporary replication slot if no permanent slot is configured\. | 0  | 
| wal\_receiver\_status\_interval | \(s\) Sets the maximum interval between WAL receiver status reports to the primary\. | –  | 
| wal\_receiver\_timeout | \(ms\) Sets the maximum wait time to receive data from the primary\. | 30000  | 
| wal\_sender\_timeout | \(ms\) Sets the maximum time to wait for WAL replication\. | –  | 
| work\_mem | \(kB\) Sets the maximum memory to be used for query workspaces\. | –  | 
| xmlbinary | Sets how binary values are to be encoded in XML\. | –  | 
| xmloption | Sets whether XML data in implicit parsing and serialization operations is to be considered as documents or content fragments\. | – | 

## Aurora PostgreSQL instance\-level parameters<a name="AuroraPostgreSQL.Reference.Parameters.Instance"></a>

The following table lists the parameters that apply to a specific DB instance in an Aurora PostgreSQL DB cluster\. This list was generated by running the [describe\-db\-parameters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-parameters.html) AWS CLI command with `default.aurora-postgresql14` for the `--db-parameter-group-name` value\. 

For a listing of the DB cluster parameters for this same default DB parameter group, see [Aurora PostgreSQL cluster\-level parameters](#AuroraPostgreSQL.Reference.Parameters.Cluster)\.


| Parameter name | Description | Default | 
| --- | --- | --- | 
| apg\_enable\_batch\_mode\_function\_execution | Enables batch\-mode functions to process sets of rows at a time\. | –  | 
| apg\_enable\_correlated\_any\_transform | Enables the planner to transform correlated ANY Sublink \(IN/NOT IN subquery to JOIN when possible\. | –  | 
| apg\_enable\_function\_migration | Enables the planner to migrate eligible scalar functions to the FROM clause\. | –  | 
| apg\_enable\_not\_in\_transform | Enables the planner to transform NOT IN subquery to ANTI JOIN when possible\. | –  | 
| apg\_enable\_remove\_redundant\_inner\_joins | Enables the planner to remove redundant inner joins\. | –  | 
| apg\_enable\_semijoin\_push\_down | Enables the use of semijoin filters for hash joins\. | –  | 
| apg\_plan\_mgmt\.capture\_plan\_baselines | Capture plan baseline mode\. manual \- enable plan capture for any SQL statement, off \- disable plan capture, automatic \- enable plan capture for for statements in pg\_stat\_statements that satisfy the eligibility criteria\. | off  | 
| apg\_plan\_mgmt\.max\_databases | Sets the maximum number of databases that that may manage queries using apg\_plan\_mgmt\. | 10  | 
| apg\_plan\_mgmt\.max\_plans | Sets the maximum number of plans that may be cached by apg\_plan\_mgmt\. | 10000  | 
| apg\_plan\_mgmt\.plan\_retention\_period | Maximum number of days since a plan was last\_used before a plan will be automatically deleted\. | 32  | 
| apg\_plan\_mgmt\.unapproved\_plan\_execution\_threshold | Estimated total plan cost below which an Unapproved plan will be executed\. | 0  | 
| apg\_plan\_mgmt\.use\_plan\_baselines | Use only approved or fixed plans for managed statements\. | false  | 
| application\_name | Sets the application name to be reported in statistics and logs\. | –  | 
| authentication\_timeout | \(s Sets the maximum allowed time to complete client authentication\. | –  | 
| auto\_explain\.log\_analyze | Use EXPLAIN ANALYZE for plan logging\. | –  | 
| auto\_explain\.log\_buffers | Log buffers usage\. | –  | 
| auto\_explain\.log\_format | EXPLAIN format to be used for plan logging\. | –  | 
| auto\_explain\.log\_min\_duration | Sets the minimum execution time above which plans will be logged\. | –  | 
| auto\_explain\.log\_nested\_statements | Log nested statements\. | –  | 
| auto\_explain\.log\_timing | Collect timing data, not just row counts\. | –  | 
| auto\_explain\.log\_triggers | Include trigger statistics in plans\. | –  | 
| auto\_explain\.log\_verbose | Use EXPLAIN VERBOSE for plan logging\. | –  | 
| auto\_explain\.sample\_rate | Fraction of queries to process\. | –  | 
| babelfishpg\_tds\.listen\_addresses | Sets the host name or IP address\(es to listen TDS to\. | \*  | 
| babelfishpg\_tds\.tds\_debug\_log\_level | Sets logging level in TDS, 0 disables logging | 1  | 
| backend\_flush\_after | \(8Kb Number of pages after which previously performed writes are flushed to disk\. | –  | 
| bytea\_output | Sets the output format for bytea\. | –  | 
| check\_function\_bodies | Check function bodies during CREATE FUNCTION\. | –  | 
| client\_connection\_check\_interval | Sets the time interval between checks for disconnection while running queries\. | –  | 
| client\_min\_messages | Sets the message levels that are sent to the client\. | –  | 
| config\_file | Sets the servers main configuration file\. | /rdsdbdata/config/postgresql\.conf  | 
| constraint\_exclusion | Enables the planner to use constraints to optimize queries\. | –  | 
| cpu\_index\_tuple\_cost | Sets the planners estimate of the cost of processing each index entry during an index scan\. | –  | 
| cpu\_operator\_cost | Sets the planners estimate of the cost of processing each operator or function call\. | –  | 
| cpu\_tuple\_cost | Sets the planners estimate of the cost of processing each tuple \(row\. | –  | 
| cron\.database\_name | Sets the database to store pg\_cron metadata tables | postgres  | 
| cron\.log\_run | Log all jobs runs into the job\_run\_details table | on  | 
| cron\.log\_statement | Log all cron statements prior to execution\. | off  | 
| cron\.max\_running\_jobs | Maximum number of jobs that can run concurrently\. | 5  | 
| cron\.use\_background\_workers | Enables background workers for pg\_cron | on  | 
| cursor\_tuple\_fraction | Sets the planners estimate of the fraction of a cursors rows that will be retrieved\. | –  | 
| db\_user\_namespace | Enables per\-database user names\. | –  | 
| deadlock\_timeout | \(ms Sets the time to wait on a lock before checking for deadlock\. | –  | 
| debug\_pretty\_print | Indents parse and plan tree displays\. | –  | 
| debug\_print\_parse | Logs each querys parse tree\. | –  | 
| debug\_print\_plan | Logs each querys execution plan\. | –  | 
| debug\_print\_rewritten | Logs each querys rewritten parse tree\. | –  | 
| default\_statistics\_target | Sets the default statistics target\. | –  | 
| default\_transaction\_deferrable | Sets the default deferrable status of new transactions\. | –  | 
| default\_transaction\_isolation | Sets the transaction isolation level of each new transaction\. | –  | 
| default\_transaction\_read\_only | Sets the default read\-only status of new transactions\. | –  | 
| effective\_cache\_size | \(8kB Sets the planners assumption about the size of the disk cache\. | SUM\(DBInstanceClassMemory/12038,\-50003  | 
| effective\_io\_concurrency | Number of simultaneous requests that can be handled efficiently by the disk subsystem\. | –  | 
| enable\_async\_append | Enables the planners use of async append plans\. | –  | 
| enable\_bitmapscan | Enables the planners use of bitmap\-scan plans\. | –  | 
| enable\_gathermerge | Enables the planners use of gather merge plans\. | –  | 
| enable\_hashagg | Enables the planners use of hashed aggregation plans\. | –  | 
| enable\_hashjoin | Enables the planners use of hash join plans\. | –  | 
| enable\_incremental\_sort | Enables the planners use of incremental sort steps\. | –  | 
| enable\_indexonlyscan | Enables the planners use of index\-only\-scan plans\. | –  | 
| enable\_indexscan | Enables the planners use of index\-scan plans\. | –  | 
| enable\_material | Enables the planners use of materialization\. | –  | 
| enable\_memoize | Enables the planners use of memoization | –  | 
| enable\_mergejoin | Enables the planners use of merge join plans\. | –  | 
| enable\_nestloop | Enables the planners use of nested\-loop join plans\. | –  | 
| enable\_parallel\_append | Enables the planners use of parallel append plans\. | –  | 
| enable\_parallel\_hash | Enables the planners user of parallel hash plans\. | –  | 
| enable\_partition\_pruning | Enable plan\-time and run\-time partition pruning\. | –  | 
| enable\_partitionwise\_aggregate | Enables partitionwise aggregation and grouping\. | –  | 
| enable\_partitionwise\_join | Enables partitionwise join\. | –  | 
| enable\_seqscan | Enables the planners use of sequential\-scan plans\. | –  | 
| enable\_sort | Enables the planners use of explicit sort steps\. | –  | 
| enable\_tidscan | Enables the planners use of TID scan plans\. | –  | 
| escape\_string\_warning | Warn about backslash escapes in ordinary string literals\. | –  | 
| exit\_on\_error | Terminate session on any error\. | –  | 
| force\_parallel\_mode | Forces use of parallel query facilities\. | –  | 
| from\_collapse\_limit | Sets the FROM\-list size beyond which subqueries are not collapsed\. | –  | 
| geqo | Enables genetic query optimization\. | –  | 
| geqo\_effort | GEQO: effort is used to set the default for other GEQO parameters\. | –  | 
| geqo\_generations | GEQO: number of iterations of the algorithm\. | –  | 
| geqo\_pool\_size | GEQO: number of individuals in the population\. | –  | 
| geqo\_seed | GEQO: seed for random path selection\. | –  | 
| geqo\_selection\_bias | GEQO: selective pressure within the population\. | –  | 
| geqo\_threshold | Sets the threshold of FROM items beyond which GEQO is used\. | –  | 
| gin\_fuzzy\_search\_limit | Sets the maximum allowed result for exact search by GIN\. | –  | 
| gin\_pending\_list\_limit | \(kB Sets the maximum size of the pending list for GIN index\. | –  | 
| hash\_mem\_multiplier | Multiple of work\_mem to use for hash tables\. | –  | 
| hba\_file | Sets the servers hba configuration file\. | /rdsdbdata/config/pg\_hba\.conf  | 
| hot\_standby\_feedback | Allows feedback from a hot standby to the primary that will avoid query conflicts\. | on  | 
| ident\_file | Sets the servers ident configuration file\. | /rdsdbdata/config/pg\_ident\.conf  | 
| idle\_in\_transaction\_session\_timeout | \(ms Sets the maximum allowed duration of any idling transaction\. | 86400000  | 
| idle\_session\_timeout | Terminate any session that has been idle \(that is, waiting for a client query, but not within an open transaction, for longer than the specified amount of time | –  | 
| join\_collapse\_limit | Sets the FROM\-list size beyond which JOIN constructs are not flattened\. | –  | 
| lc\_messages | Sets the language in which messages are displayed\. | –  | 
| listen\_addresses | Sets the host name or IP address\(es to listen to\. | \*  | 
| lo\_compat\_privileges | Enables backward compatibility mode for privilege checks on large objects\. | 0  | 
| log\_connections | Logs each successful connection\. | –  | 
| log\_destination | Sets the destination for server log output\. | stderr  | 
| log\_directory | Sets the destination directory for log files\. | /rdsdbdata/log/error  | 
| log\_disconnections | Logs end of a session, including duration\. | –  | 
| log\_duration | Logs the duration of each completed SQL statement\. | –  | 
| log\_error\_verbosity | Sets the verbosity of logged messages\. | –  | 
| log\_executor\_stats | Writes executor performance statistics to the server log\. | –  | 
| log\_file\_mode | Sets the file permissions for log files\. | 0644  | 
| log\_filename | Sets the file name pattern for log files\. | postgresql\.log\.%Y\-%m\-%d\-%H%M  | 
| logging\_collector | Start a subprocess to capture stderr output and/or csvlogs into log files\. | 1  | 
| log\_hostname | Logs the host name in the connection logs\. | 0  | 
| logical\_decoding\_work\_mem | \(kB This much memory can be used by each internal reorder buffer before spilling to disk\. | –  | 
| log\_line\_prefix | Controls information prefixed to each log line\. | %t:%r:%u@%d:%p\]:  | 
| log\_lock\_waits | Logs long lock waits\. | –  | 
| log\_min\_duration\_sample | \(ms Sets the minimum execution time above which a sample of statements will be logged\. Sampling is determined by log\_statement\_sample\_rate\. | –  | 
| log\_min\_duration\_statement | \(ms Sets the minimum execution time above which statements will be logged\. | –  | 
| log\_min\_error\_statement | Causes all statements generating error at or above this level to be logged\. | –  | 
| log\_min\_messages | Sets the message levels that are logged\. | –  | 
| log\_parameter\_max\_length | \(B When logging statements, limit logged parameter values to first N bytes\. | –  | 
| log\_parameter\_max\_length\_on\_error | \(B When reporting an error, limit logged parameter values to first N bytes\. | –  | 
| log\_parser\_stats | Writes parser performance statistics to the server log\. | –  | 
| log\_planner\_stats | Writes planner performance statistics to the server log\. | –  | 
| log\_replication\_commands | Logs each replication command\. | –  | 
| log\_rotation\_age | \(min Automatic log file rotation will occur after N minutes\. | 60  | 
| log\_rotation\_size | \(kB Automatic log file rotation will occur after N kilobytes\. | 100000  | 
| log\_statement | Sets the type of statements logged\. | –  | 
| log\_statement\_sample\_rate | Fraction of statements exceeding log\_min\_duration\_sample to be logged\. | –  | 
| log\_statement\_stats | Writes cumulative performance statistics to the server log\. | –  | 
| log\_temp\_files | \(kB Log the use of temporary files larger than this number of kilobytes\. | –  | 
| log\_timezone | Sets the time zone to use in log messages\. | UTC  | 
| log\_truncate\_on\_rotation | Truncate existing log files of same name during log rotation\. | 0  | 
| maintenance\_io\_concurrency | A variant of effective\_io\_concurrency that is used for maintenance work\. | 1  | 
| maintenance\_work\_mem | \(kB Sets the maximum memory to be used for maintenance operations\. | GREATEST\(DBInstanceClassMemory/63963136\*1024,65536  | 
| max\_connections | Sets the maximum number of concurrent connections\. | LEAST\(DBInstanceClassMemory/9531392,5000  | 
| max\_files\_per\_process | Sets the maximum number of simultaneously open files for each server process\. | –  | 
| max\_locks\_per\_transaction | Sets the maximum number of locks per transaction\. | 64  | 
| max\_parallel\_maintenance\_workers | Sets the maximum number of parallel processes per maintenance operation\. | –  | 
| max\_parallel\_workers | Sets the maximum number of parallel workers than can be active at one time\. | GREATEST\($DBInstanceVCPU/2,8  | 
| max\_parallel\_workers\_per\_gather | Sets the maximum number of parallel processes per executor node\. | –  | 
| max\_pred\_locks\_per\_page | Sets the maximum number of predicate\-locked tuples per page\. | –  | 
| max\_pred\_locks\_per\_relation | Sets the maximum number of predicate\-locked pages and tuples per relation\. | –  | 
| max\_pred\_locks\_per\_transaction | Sets the maximum number of predicate locks per transaction\. | –  | 
| max\_slot\_wal\_keep\_size | \(MB Replication slots will be marked as failed, and segments released for deletion or recycling, if this much space is occupied by WAL on disk\. | –  | 
| max\_stack\_depth | \(kB Sets the maximum stack depth, in kilobytes\. | 6144  | 
| max\_standby\_streaming\_delay | \(ms Sets the maximum delay before canceling queries when a hot standby server is processing streamed WAL data\. | 14000  | 
| max\_worker\_processes | Sets the maximum number of concurrent worker processes\. | GREATEST\($DBInstanceVCPU\*2,8  | 
| min\_dynamic\_shared\_memory | \(MB Amount of dynamic shared memory reserved at startup\. | –  | 
| min\_parallel\_index\_scan\_size | \(8kB Sets the minimum amount of index data for a parallel scan\. | –  | 
| min\_parallel\_table\_scan\_size | \(8kB Sets the minimum amount of table data for a parallel scan\. | –  | 
| old\_snapshot\_threshold | \(min Time before a snapshot is too old to read pages changed after the snapshot was taken\. | –  | 
| parallel\_leader\_participation | Controls whether Gather and Gather Merge also run subplans\. | –  | 
| parallel\_setup\_cost | Sets the planners estimate of the cost of starting up worker processes for parallel query\. | –  | 
| parallel\_tuple\_cost | Sets the planners estimate of the cost of passing each tuple \(row from worker to master backend\. | –  | 
| pgaudit\.log | Specifies which classes of statements will be logged by session audit logging\. | –  | 
| pgaudit\.log\_catalog | Specifies that session logging should be enabled in the case where all relations in a statement are in pg\_catalog\. | –  | 
| pgaudit\.log\_level | Specifies the log level that will be used for log entries\. | –  | 
| pgaudit\.log\_parameter | Specifies that audit logging should include the parameters that were passed with the statement\. | –  | 
| pgaudit\.log\_relation | Specifies whether session audit logging should create a separate log entry for each relation \(TABLE, VIEW, etc\. referenced in a SELECT or DML statement\. | –  | 
| pgaudit\.log\_statement\_once | Specifies whether logging will include the statement text and parameters with the first log entry for a statement/substatement combination or with every entry\. | –  | 
| pgaudit\.role | Specifies the master role to use for object audit logging\. | –  | 
| pg\_bigm\.enable\_recheck | It specifies whether to perform Recheck which is an internal process of full text search\. | on  | 
| pg\_bigm\.gin\_key\_limit | It specifies the maximum number of 2\-grams of the search keyword to be used for full text search\. | 0  | 
| pg\_bigm\.last\_update | It reports the last updated date of the pg\_bigm module\. | 2013\.11\.22  | 
| pg\_bigm\.similarity\_limit | It specifies the minimum threshold used by the similarity search\. | 0\.3  | 
| pg\_hint\_plan\.debug\_print | Logs results of hint parsing\. | –  | 
| pg\_hint\_plan\.enable\_hint | Force planner to use plans specified in the hint comment preceding to the query\. | –  | 
| pg\_hint\_plan\.enable\_hint\_table | Force planner to not get hint by using table lookups\. | –  | 
| pg\_hint\_plan\.message\_level | Message level of debug messages\. | –  | 
| pg\_hint\_plan\.parse\_messages | Message level of parse errors\. | –  | 
| pglogical\.batch\_inserts | Batch inserts if possible | –  | 
| pglogical\.conflict\_log\_level | Sets log level used for logging resolved conflicts\. | –  | 
| pglogical\.conflict\_resolution | Sets method used for conflict resolution for resolvable conflicts\. | –  | 
| pglogical\.extra\_connection\_options | connection options to add to all peer node connections | –  | 
| pglogical\.synchronous\_commit | pglogical specific synchronous commit value | –  | 
| pglogical\.use\_spi | Use SPI instead of low\-level API for applying changes | –  | 
| pg\_similarity\.block\_is\_normalized | Sets if the result value is normalized or not\. | –  | 
| pg\_similarity\.block\_threshold | Sets the threshold used by the Block similarity function\. | –  | 
| pg\_similarity\.block\_tokenizer | Sets the tokenizer for Block similarity function\. | –  | 
| pg\_similarity\.cosine\_is\_normalized | Sets if the result value is normalized or not\. | –  | 
| pg\_similarity\.cosine\_threshold | Sets the threshold used by the Cosine similarity function\. | –  | 
| pg\_similarity\.cosine\_tokenizer | Sets the tokenizer for Cosine similarity function\. | –  | 
| pg\_similarity\.dice\_is\_normalized | Sets if the result value is normalized or not\. | –  | 
| pg\_similarity\.dice\_threshold | Sets the threshold used by the Dice similarity measure\. | –  | 
| pg\_similarity\.dice\_tokenizer | Sets the tokenizer for Dice similarity measure\. | –  | 
| pg\_similarity\.euclidean\_is\_normalized | Sets if the result value is normalized or not\. | –  | 
| pg\_similarity\.euclidean\_threshold | Sets the threshold used by the Euclidean similarity measure\. | –  | 
| pg\_similarity\.euclidean\_tokenizer | Sets the tokenizer for Euclidean similarity measure\. | –  | 
| pg\_similarity\.hamming\_is\_normalized | Sets if the result value is normalized or not\. | –  | 
| pg\_similarity\.hamming\_threshold | Sets the threshold used by the Block similarity metric\. | –  | 
| pg\_similarity\.jaccard\_is\_normalized | Sets if the result value is normalized or not\. | –  | 
| pg\_similarity\.jaccard\_threshold | Sets the threshold used by the Jaccard similarity measure\. | –  | 
| pg\_similarity\.jaccard\_tokenizer | Sets the tokenizer for Jaccard similarity measure\. | –  | 
| pg\_similarity\.jaro\_is\_normalized | Sets if the result value is normalized or not\. | –  | 
| pg\_similarity\.jaro\_threshold | Sets the threshold used by the Jaro similarity measure\. | –  | 
| pg\_similarity\.jarowinkler\_is\_normalized | Sets if the result value is normalized or not\. | –  | 
| pg\_similarity\.jarowinkler\_threshold | Sets the threshold used by the Jarowinkler similarity measure\. | –  | 
| pg\_similarity\.levenshtein\_is\_normalized | Sets if the result value is normalized or not\. | –  | 
| pg\_similarity\.levenshtein\_threshold | Sets the threshold used by the Levenshtein similarity measure\. | –  | 
| pg\_similarity\.matching\_is\_normalized | Sets if the result value is normalized or not\. | –  | 
| pg\_similarity\.matching\_threshold | Sets the threshold used by the Matching Coefficient similarity measure\. | –  | 
| pg\_similarity\.matching\_tokenizer | Sets the tokenizer for Matching Coefficient similarity measure\. | –  | 
| pg\_similarity\.mongeelkan\_is\_normalized | Sets if the result value is normalized or not\. | –  | 
| pg\_similarity\.mongeelkan\_threshold | Sets the threshold used by the Monge\-Elkan similarity measure\. | –  | 
| pg\_similarity\.mongeelkan\_tokenizer | Sets the tokenizer for Monge\-Elkan similarity measure\. | –  | 
| pg\_similarity\.nw\_gap\_penalty | Sets the gap penalty used by the Needleman\-Wunsch similarity measure\. | –  | 
| pg\_similarity\.nw\_is\_normalized | Sets if the result value is normalized or not\. | –  | 
| pg\_similarity\.nw\_threshold | Sets the threshold used by the Needleman\-Wunsch similarity measure\. | –  | 
| pg\_similarity\.overlap\_is\_normalized | Sets if the result value is normalized or not\. | –  | 
| pg\_similarity\.overlap\_threshold | Sets the threshold used by the Overlap Coefficient similarity measure\. | –  | 
| pg\_similarity\.overlap\_tokenizer | Sets the tokenizer for Overlap Coefficientsimilarity measure\. | –  | 
| pg\_similarity\.qgram\_is\_normalized | Sets if the result value is normalized or not\. | –  | 
| pg\_similarity\.qgram\_threshold | Sets the threshold used by the Q\-Gram similarity measure\. | –  | 
| pg\_similarity\.qgram\_tokenizer | Sets the tokenizer for Q\-Gram measure\. | –  | 
| pg\_similarity\.swg\_is\_normalized | Sets if the result value is normalized or not\. | –  | 
| pg\_similarity\.swg\_threshold | Sets the threshold used by the Smith\-Waterman\-Gotoh similarity measure\. | –  | 
| pg\_similarity\.sw\_is\_normalized | Sets if the result value is normalized or not\. | –  | 
| pg\_similarity\.sw\_threshold | Sets the threshold used by the Smith\-Waterman similarity measure\. | –  | 
| pg\_stat\_statements\.max | Sets the maximum number of statements tracked by pg\_stat\_statements\. | –  | 
| pg\_stat\_statements\.save | Save pg\_stat\_statements statistics across server shutdowns\. | –  | 
| pg\_stat\_statements\.track | Selects which statements are tracked by pg\_stat\_statements\. | –  | 
| pg\_stat\_statements\.track\_planning | Selects whether planning duration is tracked by pg\_stat\_statements\. | –  | 
| pg\_stat\_statements\.track\_utility | Selects whether utility commands are tracked by pg\_stat\_statements\. | –  | 
| postgis\.gdal\_enabled\_drivers | Enable or disable GDAL drivers used with PostGIS in Postgres 9\.3\.5 and above\. | ENABLE\_ALL  | 
| quote\_all\_identifiers | When generating SQL fragments, quote all identifiers\. | –  | 
| random\_page\_cost | Sets the planners estimate of the cost of a nonsequentially fetched disk page\. | –  | 
| rds\.force\_admin\_logging\_level | See log messages for RDS admin user actions in customer databases\. | –  | 
| rds\.log\_retention\_period | Amazon RDS will delete PostgreSQL log that are older than N minutes\. | 4320  | 
| rds\.memory\_allocation\_guard | RDS parameter reserved for future use\. | –  | 
| rds\.pg\_stat\_ramdisk\_size | Size of the stats ramdisk in MB\. A nonzero value will setup the ramdisk\. | 0  | 
| rds\.rds\_superuser\_reserved\_connections | Sets the number of connection slots reserved for rds\_superusers\. | 2  | 
| rds\.superuser\_variables | List of superuser\-only variables for which we elevate rds\_superuser modification statements\. | session\_replication\_role  | 
| remove\_temp\_files\_after\_crash | Remove temporary files after backend crash\. | 0  | 
| restart\_after\_crash | Reinitialize server after backend crash\. | –  | 
| row\_security | Enable row security\. | –  | 
| search\_path | Sets the schema search order for names that are not schema\-qualified\. | –  | 
| seq\_page\_cost | Sets the planners estimate of the cost of a sequentially fetched disk page\. | –  | 
| session\_replication\_role | Sets the sessions behavior for triggers and rewrite rules\. | –  | 
| shared\_buffers | \(8kB Sets the number of shared memory buffers used by the server\. | SUM\(DBInstanceClassMemory/12038,\-50003  | 
| shared\_preload\_libraries | Lists shared libraries to preload into server\. | pg\_stat\_statements  | 
| ssl\_ca\_file | Location of the SSL server authority file\. | /rdsdbdata/rds\-metadata/ca\-cert\.pem  | 
| ssl\_cert\_file | Location of the SSL server certificate file\. | /rdsdbdata/rds\-metadata/server\-cert\.pem  | 
| ssl\_crl\_dir | Location of the SSL certificate revocation list directory\. | /rdsdbdata/rds\-metadata/ssl\_crl\_dir/  | 
| ssl\_key\_file |  Location of the SSL server private key file | /rdsdbdata/rds\-metadata/server\-key\.pem  | 
| standard\_conforming\_strings | Causes \.\.\. strings to treat backslashes literally\. | –  | 
| statement\_timeout | \(ms Sets the maximum allowed duration of any statement\. | –  | 
| stats\_temp\_directory | Writes temporary statistics files to the specified directory\. | /rdsdbdata/db/pg\_stat\_tmp  | 
| superuser\_reserved\_connections | Sets the number of connection slots reserved for superusers\. | 3  | 
| synchronize\_seqscans | Enable synchronized sequential scans\. | –  | 
| tcp\_keepalives\_count | Maximum number of TCP keepalive retransmits\. | –  | 
| tcp\_keepalives\_idle | \(s Time between issuing TCP keepalives\. | –  | 
| tcp\_keepalives\_interval | \(s Time between TCP keepalive retransmits\. | –  | 
| temp\_buffers | \(8kB Sets the maximum number of temporary buffers used by each session\. | –  | 
| temp\_file\_limit | Constrains the total amount disk space in kilobytes that a given PostgreSQL process can use for temporary files, excluding space used for explicit temporary tables | \-1  | 
| temp\_tablespaces | Sets the tablespace\(s to use for temporary tables and sort files\. | –  | 
| track\_activities | Collects information about executing commands\. | –  | 
| track\_activity\_query\_size | Sets the size reserved for pg\_stat\_activity\.current\_query, in bytes\. | 4096  | 
| track\_counts | Collects statistics on database activity\. | –  | 
| track\_functions | Collects function\-level statistics on database activity\. | pl  | 
| track\_io\_timing | Collects timing statistics on database IO activity\. | 1  | 
| transform\_–\_equals | Treats expr=– as expr IS –\. | –  | 
| update\_process\_title | Updates the process title to show the active SQL command\. | –  | 
| wal\_receiver\_status\_interval | \(s Sets the maximum interval between WAL receiver status reports to the primary\. | –  | 
| work\_mem | \(kB Sets the maximum memory to be used for query workspaces\. | –  | 
| xmlbinary | Sets how binary values are to be encoded in XML\. | –  | 
| xmloption | Sets whether XML data in implicit parsing and serialization operations is to be considered as documents or content fragments\. | – | 