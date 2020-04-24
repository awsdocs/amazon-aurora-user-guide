# PostgreSQL Database Log Files<a name="USER_LogAccess.Concepts.PostgreSQL"></a>

Amazon RDS for PostgreSQL generates query and error logs\. RDS PostgreSQL writes autovacuum information and rds\_admin actions to the error log\. PostgreSQL also logs connections, disconnections, and checkpoints to the error log\. For more information, see the [Error Reporting and Logging](https://www.postgresql.org/docs/current/runtime-config-logging.html) in the PostgreSQL documentation\.

To set logging parameters for a DB instance, set the parameters in a DB parameter group and associate that parameter group with the DB instance\. For more information, see [Working with DB Parameter Groups and DB Cluster Parameter Groups](USER_WorkingWithParamGroups.md)\.

**Topics**
+ [Setting the Log Retention Period](#USER_LogAccess.PostgreSQL.log_retention_period)
+ [Setting Log File Rotation](#USER_LogAccess.PostgreSQL.log_rotation)
+ [Using Query Logging](#USER_LogAccess.PostgreSQL.Query_Logging)

## Setting the Log Retention Period<a name="USER_LogAccess.PostgreSQL.log_retention_period"></a>

To set the retention period for system logs, use the `rds.log_retention_period` parameter\. You can find `rds.log_retention_period` in the DB parameter group associated with your DB instance\. The unit for this parameter is minutes\. For example, a setting of 1,440 retains logs for one day\. The default value is 4,320 \(three days\)\. The maximum value is 10,080 \(seven days\)\. Your instance must have enough allocated storage to contain the retained log files\. 

Amazon Aurora compresses older PostgreSQL logs when storage for the DB instance reaches a threshold\. Aurora compresses the files using the gzip compression utility; for information on gzip, see the [gzip](https://www.gzip.org/) website\. When storage for the DB instance is low and all available logs are compressed, you get a warning like the following\.

```
Warning: local storage for PostgreSQL log files is critically low for 
this Aurora PostgreSQL instance, and could lead to a database outage.
```

**Note**  
If storage gets too low, Aurora might delete compressed PostgreSQL logs before the retention period expires\. If logs are deleted early, you get a message like the following\.  

```
 The oldest PostgreSQL log files were deleted due to local storage constraints.
```

To retain older logs, publish them to Amazon CloudWatch Logs\. For more information, see [Publishing Aurora PostgreSQL Logs to Amazon CloudWatch Logs](AuroraPostgreSQL.CloudWatch.md)\. After you set up CloudWatch publishing, Aurora doesn't delete a log until it's published to CloudWatch Logs\.  

## Setting Log File Rotation<a name="USER_LogAccess.PostgreSQL.log_rotation"></a>

To control PostgreSQL log file rotation, set two parameters in the DB parameter group associated with your DB instance: [https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-ROTATION-AGE](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-ROTATION-AGE) and [ `log_rotation_size`](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-ROTATION-SIZE) \. These two settings control when a new, distinct log file is created\. 

The log file names are based on the file name pattern of the [ `log_filename`](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-FILENAME) parameter\. For example, to provide log files names with a granularity of less than an hour, set `log_filename` to the minute format: `postgresql.log.%Y-%m-%d-%H%M`\. Granularity of less than an hour is only supported for PostgreSQL version 10 and higher\. To use a granularity in hours for log file names, set `log_filename` to the hour format: `postgresql.log.%Y-%m-%d-%H`\. 

To control log file rotation based on time, set the `log_rotation_age` parameter to anywhere from 1 minute to 1,440 minutes \(24 hours\)\. The `log_rotation_age` default is 60 minutes\. If you set the `log_rotation_age` parameter to less than 60 minutes, also set the `log_filename` parameter to the minute format\.

To control log file rotation based on file size, set the `log_rotation_size` parameter to anywhere from 50,000 to 1,000,000 KB\. The default is 100,000 KB\. We recommend that you also set the `log_filename` parameter to the minute format\. Doing this makes sure that you can create a new log file in less than an hour if the `log_rotation_age` parameter is 60 minutes or greater\. 

## Using Query Logging<a name="USER_LogAccess.PostgreSQL.Query_Logging"></a>

To enable query logging for your PostgreSQL DB instance, set two parameters in the DB parameter group associated with your DB instance: `log_statement` and `log_min_duration_statement`\. 

The `log_statement` parameter controls which SQL statements are logged\. We recommend that you set this parameter to `all` to log all statements when you debug issues in your DB instance\. The default value is `none`\. To log all data definition language \(DDL\) statements \(CREATE, ALTER, DROP, and so on\), set this value to `ddl`\. To log all DDL and data modification language \(DML\) statements \(INSERT, UPDATE, DELETE, and so on\), set this value to `mod`\.

The `log_min_duration_statement` parameter sets the limit in milliseconds of a statement to be logged\. All SQL statements that run longer than the parameter setting are logged\. This parameter is disabled and set to \-1 by default\. Enabling this parameter can help you find unoptimized queries\. 

To set up query logging, take the following steps:

1. Set the `log_statement` parameter to `all`\. The following example shows the information that is written to the `postgres.log` file\.

   ```
   2013-11-05 16:48:56 UTC::@:[2952]:LOG:  received SIGHUP, reloading configuration files
   2013-11-05 16:48:56 UTC::@:[2952]:LOG:  parameter "log_statement" changed to "all"
   ```

   Additional information is written to the postgres\.log file when you run a query\. The following example shows the type of information written to the file after a query\.

   ```
   2013-11-05 16:41:07 UTC::@:[2955]:LOG:  checkpoint starting: time
   2013-11-05 16:41:07 UTC::@:[2955]:LOG:  checkpoint complete: wrote 1 buffers (0.3%); 0 transaction log file(s) added, 0 removed, 1 recycled; write=0.000 s, sync=0.003 s, total=0.012 s; sync files=1, longest=0.003 s, average=0.003 s
   2013-11-05 16:45:14 UTC:[local]:master@postgres:[8839]:LOG:  statement: SELECT d.datname as "Name",
   	       pg_catalog.pg_get_userbyid(d.datdba) as "Owner",
   	       pg_catalog.pg_encoding_to_char(d.encoding) as "Encoding",
   	       d.datcollate as "Collate",
   	       d.datctype as "Ctype",
   	       pg_catalog.array_to_string(d.datacl, E'\n') AS "Access privileges"
   	FROM pg_catalog.pg_database d
   	ORDER BY 1;
   2013-11-05 16:45:
   ```

1. Set the `log_min_duration_statement` parameter\. The following example shows the information that is written to the `postgres.log` file when the parameter is set to `1`\.

   ```
   2013-11-05 16:48:56 UTC::@:[2952]:LOG:  received SIGHUP, reloading configuration files
   2013-11-05 16:48:56 UTC::@:[2952]:LOG:  parameter "log_min_duration_statement" changed to "1"
   ```

   Additional information is written to the `postgres.log` file when you run a query that exceeds the duration parameter setting\. The following example shows the type of information written to the file after a query\.

   ```
   2013-11-05 16:51:10 UTC:[local]:master@postgres:[9193]:LOG:  statement: SELECT c2.relname, i.indisprimary, i.indisunique, i.indisclustered, i.indisvalid, pg_catalog.pg_get_indexdef(i.indexrelid, 0, true),
   	  pg_catalog.pg_get_constraintdef(con.oid, true), contype, condeferrable, condeferred, c2.reltablespace
   	FROM pg_catalog.pg_class c, pg_catalog.pg_class c2, pg_catalog.pg_index i
   	  LEFT JOIN pg_catalog.pg_constraint con ON (conrelid = i.indrelid AND conindid = i.indexrelid AND contype IN ('p','u','x'))
   	WHERE c.oid = '1255' AND c.oid = i.indrelid AND i.indexrelid = c2.oid
   	ORDER BY i.indisprimary DESC, i.indisunique DESC, c2.relname;
   2013-11-05 16:51:10 UTC:[local]:master@postgres:[9193]:LOG:  duration: 3.367 ms
   2013-11-05 16:51:10 UTC:[local]:master@postgres:[9193]:LOG:  statement: SELECT c.oid::pg_catalog.regclass FROM pg_catalog.pg_class c, pg_catalog.pg_inherits i WHERE c.oid=i.inhparent AND i.inhrelid = '1255' ORDER BY inhseqno;
   2013-11-05 16:51:10 UTC:[local]:master@postgres:[9193]:LOG:  duration: 1.002 ms
   2013-11-05 16:51:10 UTC:[local]:master@postgres:[9193]:LOG:  statement: SELECT c.oid::pg_catalog.regclass FROM pg_catalog.pg_class c, pg_catalog.pg_inherits i WHERE c.oid=i.inhrelid AND i.inhparent = '1255' ORDER BY c.oid::pg_catalog.regclass::pg_catalog.text;
   2013-11-05 16:51:18 UTC:[local]:master@postgres:[9193]:LOG:  statement: select proname from pg_proc;
   2013-11-05 16:51:18 UTC:[local]:master@postgres:[9193]:LOG:  duration: 3.469 ms
   ```