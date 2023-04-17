# Aurora PostgreSQL database log files<a name="USER_LogAccess.Concepts.PostgreSQL"></a>

Aurora PostgreSQL logs database activities to the default PostgreSQL log file\. For an on\-premises PostgreSQL DB instance, these messages are stored locally in `log/postgresql.log`\. For an Aurora PostgreSQL DB cluster, the log file is available on the Aurora cluster\. Also, you must use the Amazon RDS Console to view or download its contents\. The default logging level captures login failures, fatal server errors, deadlocks, and query failures\.

For more information about how you can view, download, and watch file\-based database logs, see [Monitoring Amazon Aurora log files](USER_LogAccess.md)\. To learn more about PostgreSQL logs, see [Working with Amazon RDS and Aurora PostgreSQL logs: Part 1](http://aws.amazon.com/blogs/database/working-with-rds-and-aurora-postgresql-logs-part-1/) and [ Working with Amazon RDS and Aurora PostgreSQL logs: Part 2](http://aws.amazon.com/blogs/database/working-with-rds-and-aurora-postgresql-logs-part-2/)\. 

In addition to the standard PostgreSQL logs discussed in this topic, Aurora PostgreSQL also supports the PostgreSQL Audit extension \(`pgAudit`\)\. Most regulated industries and government agencies need to maintain an audit log or audit trail of changes made to data to comply with legal requirements\. For information about installing and using pgAudit, see [Using pgAudit to log database activity](Appendix.PostgreSQL.CommonDBATasks.md#Appendix.PostgreSQL.CommonDBATasks.pgaudit)\.

**Topics**
+ [Parameters that affect logging behavior](#USER_LogAccess.Concepts.PostgreSQL.overview.parameter-groups)
+ [Turning on query logging for your Aurora PostgreSQL DB cluster](#USER_LogAccess.Concepts.PostgreSQL.Query_Logging)

## Parameters that affect logging behavior<a name="USER_LogAccess.Concepts.PostgreSQL.overview.parameter-groups"></a>

You can customize the logging behavior for your Aurora PostgreSQL DB cluster by modifying various parameters\. In the following table you can find the parameters that affect how long the logs are stored, when to rotate the log, and whether to output the log as a CSV \(comma\-separated value\) format\. You can also find the text output sent to STDERR, among other settings\. To change settings for the parameters that are modifiable, use a custom DB cluster parameter group for your Aurora PostgreSQL DB cluster\. For more information, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\. As noted in the table, the `log_line_prefix` can't be changed\. 


| Parameter | Default | Description | 
| --- | --- | --- | 
| log\_destination | stderr | Sets the output format for the log\. The default is `stderr` but you can also specify comma\-separated value \(CSV\) by adding `csvlog` to the setting\. For more information, see [Setting the log destination \(`stderr`, `csvlog`\)](#USER_LogAccess.Concepts.PostgreSQL.Log_Format)  | 
| log\_filename | postgresql\.log\.%Y\-%m\-%d\-%H%M  | Specifies the pattern for the log file name\. In addition to the default, this parameter supports `postgresql.log.%Y-%m-%d` and `postgresql.log.%Y-%m-%d-%H` for the filename pattern\.  | 
| log\_line\_prefix | %t:%r:%u@%d:\[%p\]: | Defines the prefix for each log line that gets written to `stderr`, to note the time \(%t\), remote host \(%r\), user \(%u\), database \(%d\), and process ID \(%p\)\. You can't modify this parameter\. | 
| log\_rotation\_age | 60 | Minutes after which log file is automatically rotated\. You can change this value to between 1 and 1440 minutes\. For more information, see [Setting log file rotation](#USER_LogAccess.Concepts.PostgreSQL.log_rotation)\.  | 
| log\_rotation\_size | – | The size \(kB\) at which the log is automatically rotated\. By default, this parameter isn't used because logs are rotated based on the `log_rotation_age` parameter\. To learn more, see [Setting log file rotation](#USER_LogAccess.Concepts.PostgreSQL.log_rotation)\. | 
| rds\.log\_retention\_period | 4320 | PostgreSQL logs that are older than the specified number of minutes are deleted\. The default value of 4320 minutes deletes log files after 3 days\. For more information, see [Setting the log retention period](#USER_LogAccess.Concepts.PostgreSQL.log_retention_period)\. | 

To identify application issues, you can look for query failures, login failures, deadlocks, and fatal server errors in the log\. For example, suppose that you converted a legacy application from Oracle to Aurora PostgreSQL, but not all queries converted correctly\. These incorrectly formatted queries generate error messages that you can find in the logs to help identify problems\. For more information about logging queries, see [Turning on query logging for your Aurora PostgreSQL DB cluster](#USER_LogAccess.Concepts.PostgreSQL.Query_Logging)\. 

In the following topics, you can find information about how to set various parameters that control the basic details for your PostgreSQL logs\. 

**Topics**
+ [Setting the log retention period](#USER_LogAccess.Concepts.PostgreSQL.log_retention_period)
+ [Setting log file rotation](#USER_LogAccess.Concepts.PostgreSQL.log_rotation)
+ [Setting the log destination \(`stderr`, `csvlog`\)](#USER_LogAccess.Concepts.PostgreSQL.Log_Format)
+ [Understanding the log\_line\_prefix parameter](#USER_LogAccess.Concepts.PostgreSQL.Log_Format.log-line-prefix)

### Setting the log retention period<a name="USER_LogAccess.Concepts.PostgreSQL.log_retention_period"></a>

The `rds.log_retention_period` parameter specifies how long your Aurora PostgreSQL DB cluster keeps its log files\. The default setting is 3 days \(4,320 minutes\), but you can set this value to anywhere from 1 day \(1,440 minutes\) to 7 days \(10,080 minutes\)\. Be sure that your Aurora PostgreSQL DB cluster has sufficient storage to hold the log files for the period of time\.

We recommend that you have your logs routinely published to Amazon CloudWatch Logs so that you can view and analyze system data long after the logs have been removed from your Aurora PostgreSQL DB cluster\. For more information, see [Publishing Aurora PostgreSQL logs to Amazon CloudWatch Logs](AuroraPostgreSQL.CloudWatch.md)\. After you set up CloudWatch publishing, Aurora doesn't delete a log until after it's published to CloudWatch Logs\.  

Amazon Aurora compresses older PostgreSQL logs when storage for the DB instance reaches a threshold\. Aurora compresses the files using the gzip compression utility\. For more information, see the [gzip](https://www.gzip.org) website\.

When storage for the DB instance is low and all available logs are compressed, you get a warning such as the following:

```
Warning: local storage for PostgreSQL log files is critically low for 
this Aurora PostgreSQL instance, and could lead to a database outage.
```

If there's not enough storage, Aurora might delete compressed PostgreSQL logs before the end of a specified retention period\. If that happens, you see a message similar to the following:

```
The oldest PostgreSQL log files were deleted due to local storage constraints.
```

### Setting log file rotation<a name="USER_LogAccess.Concepts.PostgreSQL.log_rotation"></a>

Aurora creates new log files every hour by default\. The timing is controlled by the `log_rotation_age` parameter\. This parameter has a default value of 60 \(minutes\), but you can set it to anywhere from 1 minute to 24 hours \(1,440 minutes\)\. When it's time for rotation, a new distinct log file is created\. The file is named according to the pattern specified by the `log_filename` parameter\. 

Log files can also be rotated according to their size, as specified in the `log_rotation_size` parameter\. This parameter specifies that the log should be rotated when it reaches the specified size \(in kilobytes\)\. The default `log_rotation_size` is 100000 kB \(kilobytes\) for an Aurora PostgreSQL DB cluster, but you can set this value to anywhere from 50,000 to 1,000,000 kilobytes\. 

The log file names are based on the file name pattern specified in the `log_filename` parameter\. The available settings for this parameter are as follows:
+ `postgresql.log.%Y-%m-%d` – Default format for the log file name\. Includes the year, month, and date in the name of the log file\.
+ `postgresql.log.%Y-%m-%d-%H` – Includes the hour in the log file name format\.
+ `postgresql.log.%Y-%m-%d-%H%M` – Includes hour:minute in the log file name format\.

If you set `log_rotation_age` parameter to less than 60 minutes, set the `log_filename` parameter to the minute format\.

For more information, see [https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-ROTATION-AGE](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-ROTATION-AGE) and [https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-ROTATION-SIZE](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-ROTATION-SIZE) in the PostgreSQL documentation\.

### Setting the log destination \(`stderr`, `csvlog`\)<a name="USER_LogAccess.Concepts.PostgreSQL.Log_Format"></a>

By default, Aurora PostgreSQL generates logs in standard error \(stderr\) format\. This format is the default setting for the `log_destination` parameter\. Each message is prefixed using the pattern specified in the `log_line_prefix` parameter\. For more information, see [Understanding the log\_line\_prefix parameter](#USER_LogAccess.Concepts.PostgreSQL.Log_Format.log-line-prefix)\. 

Aurora PostgreSQL can also generate the logs in `csvlog` format\. The `csvlog` is useful for analyzing the log data as comma\-separated values \(CSV\) data\. For example, suppose that you use the `log_fdw` extension to work with your logs as foreign tables\. The foreign table created on `stderr` log files contains a single column with log event data\. By adding `csvlog` to the `log_destination` parameter, you get the log file in the CSV format with demarcations for the multiple columns of the foreign table\. You can now sort and analyze your logs more easily\. 

If you specify `csvlog` for this parameter, be aware that both `stderr` and `csvlog` files are generated\. Be sure to monitor the storage consumed by the logs, taking into account the `rds.log_retention_period` and other settings that affect log storage and turnover\. Using `stderr` and `csvlog` more than doubles the storage consumed by the logs\.

If you add `csvlog` to `log_destination` and you want to revert to the `stderr` alone, you need to reset the parameter\. To do so, open the Amazon RDS Console and then open the custom DB cluster parameter group for your instance\. Choose the `log_destination` parameter, choose **Edit parameter**, and then choose **Reset**\. 

For more information about configuring logging, see [ Working with Amazon RDS and Aurora PostgreSQL logs: Part 1](http://aws.amazon.com/blogs/database/working-with-rds-and-aurora-postgresql-logs-part-1/)\.

### Understanding the log\_line\_prefix parameter<a name="USER_LogAccess.Concepts.PostgreSQL.Log_Format.log-line-prefix"></a>

The `stderr` log format prefixes each log message with the details specified by the `log_line_prefix` parameter, as follows\.

```
%t:%r:%u@%d:[%p]:t
```

You can't change this setting\. Each log entry sent to `stderr` includes the following information\.
+ `%t` – Time of log entry
+  `%r` – Remote host address
+  `%u@%d` – User name @ database name
+  `[%p]` – Process ID if available

## Turning on query logging for your Aurora PostgreSQL DB cluster<a name="USER_LogAccess.Concepts.PostgreSQL.Query_Logging"></a>

You can collect more detailed information about your database activities, including queries, queries waiting for locks, checkpoints, and many other details by setting some of the parameters listed in the following table\. This topic focuses on logging queries\.


| Parameter | Default | Description | 
| --- | --- | --- | 
| log\_connections | – | Logs each successful connection\. To learn how to use this parameter with `log_disconnections` to detect connection churn, see [Managing Aurora PostgreSQL connection churn with pooling ](AuroraPostgreSQL.BestPractices.connection_pooling.md)\.  | 
| log\_disconnections | – | Logs the end of each session and its duration\. To learn how to use this parameter with `log_connections` to detect connection churn, see [Managing Aurora PostgreSQL connection churn with pooling ](AuroraPostgreSQL.BestPractices.connection_pooling.md)\. | 
| log\_checkpoints | 1 | Logs each checkpoint\. | 
| log\_lock\_waits | – | Logs long lock waits\. By default, this parameter isn't set\. | 
| log\_min\_duration\_sample | – | \(ms\) Sets the minimum execution time above which a sample of statements is logged\. Sample size is set using the log\_statement\_sample\_rate parameter\. | 
| log\_min\_duration\_statement | – | Any SQL statement that runs atleast for the specified amount of time or longer gets logged\. By default, this parameter isn't set\. Turning on this parameter can help you find unoptimized queries\. | 
| log\_statement | – | Sets the type of statements logged\. By default, this parameter isn't set, but you can change it to `all`, `ddl`, or `mod` to specify the types of SQL statements that you want logged\. If you specify anything other than `none` for this parameter, you should also take additional steps to prevent the exposure of passwords in the log files\. For more information, see [Mitigating risk of password exposure when using query loggingMitigating password exposure risk](#USER_LogAccess.Concepts.PostgreSQL.Query_Logging.mitigate-risk)\.  | 
| log\_statement\_sample\_rate | – | The percentage of statements exceeding the time specified in `log_min_duration_sample` to be logged, expressed as a floating point value between 0\.0 and 1\.0\.  | 
| log\_statement\_stats | – | Writes cumulative performance statistics to the server log\. | 

### Using logging to find slow performing queries<a name="USER_LogAccess.Concepts.PostgreSQL.Query_Logging.using"></a>

You can log SQL statements and queries to help find slow performing queries\. You turn on this capability by modifying the settings in the `log_statement` and `log_min_duration` parameters as outlined in this section\. Before turning on query logging for your Aurora PostgreSQL DB cluster, you should be aware of possible password exposure in the logs and how to mitigate the risks\. For more information, see [Mitigating risk of password exposure when using query loggingMitigating password exposure risk](#USER_LogAccess.Concepts.PostgreSQL.Query_Logging.mitigate-risk)\. 

Following, you can find reference information about the `log_statement` and `log_min_duration` parameters\.log\_statement

This parameter specifies the type of SQL statements that should get sent to the log\. The default value is `none`\. If you change this parameter to `all`, `ddl`, or `mod`, be sure to apply recommended actions to mitigate the risk of exposing passwords in the logs\. For more information, see [Mitigating risk of password exposure when using query loggingMitigating password exposure risk](#USER_LogAccess.Concepts.PostgreSQL.Query_Logging.mitigate-risk)\. 

**all**  
Logs all statements\. This setting is recommended for debugging purposes\.

**ddl**  
Logs all data definition language \(DDL\) statements, such as CREATE, ALTER, DROP, and so on\.

**mod**  
Logs all DDL statements and data manipulation language \(DML\) statements, such as INSERT, UPDATE, and DELETE, which modify the data\.

**none**  
No SQL statements get logged\. We recommend this setting to avoid the risk of exposing passwords in the logs\.log\_min\_duration\_statement

Any SQL statement that runs atleast for the specified amount of time or longer gets logged\. By default, this parameter isn't set\. Turning on this parameter can help you find unoptimized queries\.

**–1–2147483647**  
The number of milliseconds \(ms\) of runtime over which a statement gets logged\.

**To set up query logging**

These steps assume that your Aurora PostgreSQL DB cluster uses a custom DB cluster parameter group\. 

1. Set the `log_statement` parameter to `all`\. The following example shows the information that is written to the `postgresql.log` file with this parameter setting\.

   ```
   2022-10-05 22:05:52 UTC:52.95.4.1(11335):postgres@labdb:[3639]:LOG: statement: SELECT feedback, s.sentiment,s.confidence
   FROM support,aws_comprehend.detect_sentiment(feedback, 'en') s
   ORDER BY s.confidence DESC;
   2022-10-05 22:05:52 UTC:52.95.4.1(11335):postgres@labdb:[3639]:LOG: QUERY STATISTICS
   2022-10-05 22:05:52 UTC:52.95.4.1(11335):postgres@labdb:[3639]:DETAIL: ! system usage stats:
   ! 0.017355 s user, 0.000000 s system, 0.168593 s elapsed
   ! [0.025146 s user, 0.000000 s system total]
   ! 36644 kB max resident size
   ! 0/8 [0/8] filesystem blocks in/out
   ! 0/733 [0/1364] page faults/reclaims, 0 [0] swaps
   ! 0 [0] signals rcvd, 0/0 [0/0] messages rcvd/sent
   ! 19/0 [27/0] voluntary/involuntary context switches
   2022-10-05 22:05:52 UTC:52.95.4.1(11335):postgres@labdb:[3639]:STATEMENT: SELECT feedback, s.sentiment,s.confidence
   FROM support,aws_comprehend.detect_sentiment(feedback, 'en') s
   ORDER BY s.confidence DESC;
   2022-10-05 22:05:56 UTC:52.95.4.1(11335):postgres@labdb:[3639]:ERROR: syntax error at or near "ORDER" at character 1
   2022-10-05 22:05:56 UTC:52.95.4.1(11335):postgres@labdb:[3639]:STATEMENT: ORDER BY s.confidence DESC;
   ----------------------- END OF LOG ----------------------
   ```

1. Set the `log_min_duration_statement` parameter\. The following example shows the information that is written to the `postgresql.log` file when the parameter is set to `1`\.

   Queries that exceed the duration specified in the `log_min_duration_statement` parameter are logged\. The following shows an example\. You can view the log file for your Aurora PostgreSQL DB cluster in the Amazon RDS Console\. 

   ```
   2022-10-05 19:05:19 UTC:52.95.4.1(6461):postgres@labdb:[6144]:LOG: statement: DROP table comments;
   2022-10-05 19:05:19 UTC:52.95.4.1(6461):postgres@labdb:[6144]:LOG: duration: 167.754 ms
   2022-10-05 19:08:07 UTC::@:[355]:LOG: checkpoint starting: time
   2022-10-05 19:08:08 UTC::@:[355]:LOG: checkpoint complete: wrote 11 buffers (0.0%); 0 WAL file(s) added, 0 removed, 0 recycled; write=1.013 s, sync=0.006 s, total=1.033 s; sync files=8, longest=0.004 s, average=0.001 s; distance=131028 kB, estimate=131028 kB
   ----------------------- END OF LOG ----------------------
   ```

#### Mitigating risk of password exposure when using query logging<a name="USER_LogAccess.Concepts.PostgreSQL.Query_Logging.mitigate-risk"></a>

We recommend that you keep `log_statement` set to `none` to avoid exposing passwords\. If you set `log_statement` to `all`, `ddl`, or `mod`, we recommend that you take one or more of the following steps\.
+ For the client, encrypt sensitive information\. For more information, see [Encryption Options](https://www.postgresql.org/docs/current/encryption-options.html) in the PostgreSQL documentation\. Use the `ENCRYPTED` \(and `UNENCRYPTED`\) options of the `CREATE` and `ALTER` statements\. For more information, see [CREATE USER](https://www.postgresql.org/docs/current/sql-createuser.html) in the PostgreSQL documentation\.
+ For your Aurora PostgreSQL DB cluster, set up and use the PostgreSQL Auditing \(pgAudit\) extension\. This extension redacts sensitive information in CREATE and ALTER statements sent to the log\. For more information, see [Using pgAudit to log database activity](Appendix.PostgreSQL.CommonDBATasks.md#Appendix.PostgreSQL.CommonDBATasks.pgaudit)\. 
+ Restrict access to the CloudWatch logs\.
+ Use stronger authentication mechanisms such as IAM\.

 