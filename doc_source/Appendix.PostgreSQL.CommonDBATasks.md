# Working with extensions and foreign data wrappers<a name="Appendix.PostgreSQL.CommonDBATasks"></a>

To extend the functionality to your Aurora PostgreSQL\-Compatible Edition DB cluster, you can install and use various PostgreSQL *extensions*\. For example, if your use case calls for intensive data entry across very large tables, you can install the `[pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html)` extension to partition your data and thus spread the workload\.

In some cases, rather than installing an extension, you might add a specific *module* to the list of `shared_preload_libraries` in your Aurora PostgreSQL DB cluster's custom DB cluster parameter group\. Typically, the default DB cluster parameter group loads only the `pg_stat_statements`, but several other modules are available to add to the list\. For example, you can add scheduling capability by adding the `pg_cron` module, as detailed in [Scheduling maintenance with the PostgreSQL pg\_cron extension](PostgreSQL_pg_cron.md)\. As another example, you can log query execution plans by loading the `auto_explain` module\. To learn more, see [Logging execution plans of queries](https://aws.amazon.com/premiumsupport/knowledge-center/rds-postgresql-tune-query-performance/#) in the AWS knowledge center\. 

An extension that provides access to external data is more specifically known as a *foreign data wrapper* \(FDW\)\. As one example, the `oracle_fdw` extension allows your Aurora PostgreSQL DB cluster to work with Oracle databases\. 

Following, you can find information about setting up and using some of the extensions, modules, and FDWs available for Aurora PostgreSQL\. For simplicity's sake, these are all referred to as "extensions\." You can find listings of extensions that you can use with the currently available Aurora PostgreSQL versions, see [Extension versions for Amazon Aurora PostgreSQL](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Extensions.html) in the *Release Notes for Aurora PostgreSQL*\.
+ [Managing large objects with the lo module](PostgreSQL_large_objects_lo_extension.md)
+ [Managing spatial data with the PostGIS extension](Appendix.PostgreSQL.CommonDBATasks.PostGIS.md)
+ [Managing PostgreSQL partitions with the pg\_partman extension](PostgreSQL_Partitions.md)
+ [Scheduling maintenance with the PostgreSQL pg\_cron extension](PostgreSQL_pg_cron.md)
+ [Using pgAudit to log database activity](#Appendix.PostgreSQL.CommonDBATasks.pgaudit)
+ [Working with Oracle databases by using the oracle\_fdw extension](Appendix.PostgreSQL.CommonDBATasks.Extensions.foreign-data-wrappers.md#postgresql-oracle-fdw)
+ [Working with SQL Server databases by using the tds\_fdw extension](Appendix.PostgreSQL.CommonDBATasks.Extensions.foreign-data-wrappers.md#postgresql-tds-fdw)

## Using pgAudit to log database activity<a name="Appendix.PostgreSQL.CommonDBATasks.pgaudit"></a>

Financial institutions, government agencies, and many industries need to keep *audit logs* to meet regulatory requirements\. By using the PostgreSQL Audit extension \(pgAudit\) with your Aurora PostgreSQL DB cluster, you can capture the detailed records that are typically needed by auditors or to meet regulatory requirements\. For example, you can set up the pgAudit extension to track changes made to specific databases and tables, to record the user who made the change, and many other details\.

The pgAudit extension builds on the functionality of the native PostgreSQL logging infrastructure by extending the log messages with more detail\. In other words, you use the same approach to view your audit log as you do to view any log messages\. For more information about PostgreSQL logging, see [Aurora PostgreSQL database log files](USER_LogAccess.Concepts.PostgreSQL.md)\. 

The pgAudit extension redacts sensitive data such as cleartext passwords from the logs\. If your Aurora PostgreSQL DB cluster is configured to log data manipulation language \(DML\) statements as detailed in [Turning on query logging for your Aurora PostgreSQL DB cluster](USER_LogAccess.Concepts.PostgreSQL.md#USER_LogAccess.Concepts.PostgreSQL.Query_Logging), you can avoid the cleartext password issue by using the PostgreSQL Audit extension\. 

You can configure auditing on your database instances with a great degree of specificity\. You can audit all databases and all users\. Or, you can choose to audit only certain databases, users, and other objects\. You can also explicitly exclude certain users and databases from being audited\. For more information, see [Excluding users or databases from audit logging](#Appendix.PostgreSQL.CommonDBATasks.pgaudit.exclude-user-db)\. 

Given the amount of detail that can be captured, we recommend that if you do use pgAudit, you monitor your storage consumption\. 

The pgAudit extension is supported on all available Aurora PostgreSQL versions\. For a list of pgAudit versions supported by Aurora PostgreSQL version, see [Extension versions for Amazon Aurora PostgreSQL](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Extensions.html) in the *Release Notes for Aurora PostgreSQL*\. 

**Topics**
+ [Setting up the pgAudit extension](#Appendix.PostgreSQL.CommonDBATasks.pgaudit.basic-setup)
+ [Auditing database objects](#Appendix.PostgreSQL.CommonDBATasks.pgaudit.auditing)
+ [Excluding users or databases from audit logging](#Appendix.PostgreSQL.CommonDBATasks.pgaudit.exclude-user-db)
+ [Reference for the pgAudit extension](#Appendix.PostgreSQL.CommonDBATasks.pgaudit.reference)

### Setting up the pgAudit extension<a name="Appendix.PostgreSQL.CommonDBATasks.pgaudit.basic-setup"></a>

To set up the pgAudit extension on your Aurora PostgreSQL DB cluster, you first add pgAudit to the shared libraries on the custom DB cluster parameter group for your Aurora PostgreSQL DB cluster\. For information about creating a custom DB cluster parameter group, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\.  Next, you install the pgAudit extension\. Finally, you specify the databases and objects that you want to audit\. The procedures in this section show you how\. You can use the AWS Management Console or the AWS CLI\. 

You must have permissions as the `rds_superuser` role to perform all these tasks\.

The steps following assume that your Aurora PostgreSQL DB cluster is associated with a custom DB cluster parameter group\. 

#### Console<a name="Appendix.PostgreSQL.CommonDBATasks.pgaudit.basic-setup.CON"></a>

**To set up the pgAudit extension**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose your Aurora PostgreSQL DB cluster's Writer instance \.

1. Open the **Configuration** tab for your Aurora PostgreSQL DB cluster writer instance\.  Among the Instance details, find the **Parameter group** link\. 

1. Choose the link to open the custom parameters associated with your Aurora PostgreSQL DB cluster\. 

1. In the **Parameters** search field, type `shared_pre` to find the `shared_preload_libraries` parameter\.

1. Choose **Edit parameters** to access the property values\.

1. Add `pgaudit` to the list in the **Values** field\. Use a comma to separate items in the list of values\.   
![\[Image of the shared_preload_libaries parameter with pgAudit added.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/apg_rpg_shared_preload_pgaudit.png)

1. Reboot the writer instance of your Aurora PostgreSQL DB cluster so that your change to the `shared_preload_libraries` parameter takes effect\. 

1. When the instance is available, verify that pgAudit has been initialized\. Use `psql` to connect to the writer instance of your Aurora PostgreSQL DB cluster, and then run the following command\.

   ```
   SHOW shared_preload_libraries;
   shared_preload_libraries 
   --------------------------
   rdsutils,pgaudit
   (1 row)
   ```

1. With pgAudit initialized, you can now create the extension\. You need to create the extension after initializing the library because the `pgaudit` extension installs event triggers for auditing data definition language \(DDL\) statements\. 

   ```
   CREATE EXTENSION pgaudit;
   ```

1. Close the `psql` session\.

   ```
   labdb=> \q
   ```

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Find the `pgaudit.log` parameter in the list and set to the appropriate value for your use case\. For example, setting the `pgaudit.log` parameter to `write` as shown in the following image captures inserts, updates, deletes, and some other types changes to the log\.   
![\[Image of the pgaudit.log parameter with setting.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/rpg_set_pgaudit-log-level.png)

   You can also choose one of the following values for the `pgaudit.log` parameter\.
   + none – This is the default\. No database changes are logged\. 
   + all – Logs everything \(read, write, function, role, ddl, misc\)\. 
   + ddl – Logs all data definition language \(DDL\) statements that aren't included in the `ROLE` class\.
   + function – Logs function calls and `DO` blocks\.
   + misc – Logs miscellaneous commands, such as `DISCARD`, `FETCH`, `CHECKPOINT`, `VACUUM`, and `SET`\.
   + read – Logs `SELECT` and `COPY` when the source is a relation \(such as a table\) or a query\.
   + role – Logs statements related to roles and privileges, such as `GRANT`, `REVOKE`, `CREATE ROLE`, `ALTER ROLE`, and `DROP ROLE`\.
   + write – Logs `INSERT`, `UPDATE`, `DELETE`, `TRUNCATE`, and `COPY` when the destination is a relation \(table\)\.

1. Choose **Save changes**\.

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose your Aurora PostgreSQL DB cluster's writer instance from the Databases list to select it, and then choose **Reboot** from the Actions menu\.

#### AWS CLI<a name="Appendix.PostgreSQL.CommonDBATasks.pgaudit.basic-setup.CLI"></a>

**To setup pgAudit**

To setup pgAudit using the AWS CLI, you call the [modify\-db\-parameter\-group](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-parameter-group.html) operation to modify the audit log parameters in your custom parameter group, as shown in the following procedure\.

1. Use the following AWS CLI command to add `pgaudit` to the `shared_preload_libraries` parameter\.

   ```
   aws rds modify-db-parameter-group \
      --db-parameter-group-name custom-param-group-name \
      --parameters "ParameterName=shared_preload_libraries,ParameterValue=pgaudit,ApplyMethod=pending-reboot" \
      --region aws-region
   ```

1. Use the following AWS CLI command to reboot the writer instance of your Aurora PostgreSQL DB cluster so that the pgaudit library is initialized\.

   ```
   aws rds reboot-db-instance \
       --db-instance-identifier writer-instance \
       --region aws-region
   ```

1. When the instance is available, you can verify that `pgaudit` has been initialized\. Use `psql` to connect to the writer instance of your Aurora PostgreSQL DB cluster, and then run the following command\.

   ```
   SHOW shared_preload_libraries;
   shared_preload_libraries 
   --------------------------
   rdsutils,pgaudit
   (1 row)
   ```

   With pgAudit initialized, you can now create the extension\.

   ```
   CREATE EXTENSION pgaudit;
   ```

1. Close the `psql` session so that you can use the AWS CLI\.

   ```
   labdb=> \q
   ```

1. Use the following AWS CLI command to specify the classes of statement that want logged by session audit logging\. The example sets the `pgaudit.log` parameter to `write`, which captures inserts, updates, and deletes to the log\.

   ```
   aws rds modify-db-parameter-group \
      --db-parameter-group-name custom-param-group-name \
      --parameters "ParameterName=pgaudit.log,ParameterValue=write,ApplyMethod=pending-reboot" \
      --region aws-region
   ```

   You can also choose one of the following values for the `pgaudit.log` parameter\.
   + none – This is the default\. No database changes are logged\. 
   + all – Logs everything \(read, write, function, role, ddl, misc\)\. 
   + ddl – Logs all data definition language \(DDL\) statements that aren't included in the `ROLE` class\.
   + function – Logs function calls and `DO` blocks\.
   + misc – Logs miscellaneous commands, such as `DISCARD`, `FETCH`, `CHECKPOINT`, `VACUUM`, and `SET`\.
   + read – Logs `SELECT` and `COPY` when the source is a relation \(such as a table\) or a query\.
   + role – Logs statements related to roles and privileges, such as `GRANT`, `REVOKE`, `CREATE ROLE`, `ALTER ROLE`, and `DROP ROLE`\.
   + write – Logs `INSERT`, `UPDATE`, `DELETE`, `TRUNCATE`, and `COPY` when the destination is a relation \(table\)\.

   Reboot the writer instance of your Aurora PostgreSQL DB cluster using the following AWS CLI command\.

   ```
   aws rds reboot-db-instance \
       --db-instance-identifier writer-instance \
       --region aws-region
   ```

### Auditing database objects<a name="Appendix.PostgreSQL.CommonDBATasks.pgaudit.auditing"></a>

With pgAudit set up on your Aurora PostgreSQL DB cluster and configured for your requirements, more detailed information is captured in the PostgreSQL log\. For example, while the default PostgreSQL logging configuration identifies the date and time that a change was made in a database table, with the pgAudit extension the log entry can include the schema, user who made the change, and other details depending on how the extension parameters are configured\. You can set up auditing to track changes in the following ways\.
+ For each session, by user\. For the session level, you can capture the fully qualified command text\.
+ For each object, by user and by database\. 

The object auditing capability is activated when you create the `rds_pgaudit` role on your system and then add this role to the `pgaudit.role` parameter in your custom parameter parameter group\. By default, the `pgaudit.role` parameter is unset and the only allowable value is `rds_pgaudit`\. The following steps assume that `pgaudit` has been initialized and that you have created the `pgaudit` extension by following the procedure in [Setting up the pgAudit extension](#Appendix.PostgreSQL.CommonDBATasks.pgaudit.basic-setup)\. 

![\[Image of the PostgreSQL log file after setting up pgAudit.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/pgaudit-log-example.png)

As shown in this example, the "LOG: AUDIT: SESSION" line provides information about the table and its schema, among other details\. 

**To set up object auditing**

1. Use `psql` to connect to the writer instance of your Aurora PostgreSQL DB cluster\.

   ```
   psql --host=your-instance-name.aws-region.rds.amazonaws.com --port=5432 --username=postgrespostgres --password --dbname=labdb
   ```

1. Create a database role named `rds_pgaudit` using the following command\.

   ```
   labdb=> CREATE ROLE rds_pgaudit;
   CREATE ROLE
   labdb=>
   ```

1. Close the `psql` session\.

   ```
   labdb=> \q
   ```

   In the next few steps, use the AWS CLI to modify the audit log parameters in your custom parameter group\. 

1. Use the following AWS CLI command to set the `pgaudit.role` parameter to `rds_pgaudit`\. By default, this parameter is empty, and `rds_pgaudit` is the only allowable value\.

   ```
   aws rds modify-db-parameter-group \
      --db-parameter-group-name custom-param-group-name \
      --parameters "ParameterName=pgaudit.role,ParameterValue=rds_pgaudit,ApplyMethod=pending-reboot" \
      --region aws-region
   ```

1. Use the following AWS CLI command to reboot the writer instance of your Aurora PostgreSQL DB cluster so that your changes to the parameters take effect\.

   ```
   aws rds reboot-db-instance \
       --db-instance-identifier writer-instance \
       --region aws-region
   ```

1. Run the following command to confirm that the `pgaudit.role` is set to `rds_pgaudit`\.

   ```
   SHOW pgaudit.role;
   pgaudit.role 
   ------------------
   rds_pgaudit
   ```

To test pgAudit logging, you can run several example commands that you want to audit\. For example, you might run the following commands\.

```
CREATE TABLE t1 (id int);
GRANT SELECT ON t1 TO rds_pgaudit;
SELECT * FROM t1;
id 
----
(0 rows)
```

The database logs should contain an entry similar to the following\.

```
...
2017-06-12 19:09:49 UTC:...:rds_test@postgres:[11701]:LOG: AUDIT:
OBJECT,1,1,READ,SELECT,TABLE,public.t1,select * from t1;
...
```

For information on viewing the logs, see [Monitoring Amazon Aurora log files](USER_LogAccess.md)\.

To learn more about the pgAudit extension, see [pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md) on GitHub\.

### Excluding users or databases from audit logging<a name="Appendix.PostgreSQL.CommonDBATasks.pgaudit.exclude-user-db"></a>

As discussed in [Aurora PostgreSQL database log files](USER_LogAccess.Concepts.PostgreSQL.md), PostgreSQL logs consume storage space\. Using the pgAudit extension adds to the volume of data gathered in your logs to varying degrees, depending on the changes that you track\. You might not need to audit every user or database in your Aurora PostgreSQL DB cluster\. 

To minimize impacts to your storage and to avoid needlessly capturing audit records, you can exclude users and databases from being audited\. You can also change logging within a given session\. The following examples show you how\. 

**Note**  
Parameter settings at the session level take precedence over the settings in the custom DB cluster parameter group for the Aurora PostgreSQL DB cluster's writer instance\. If you don't want database users to bypass your audit logging configuration settings, be sure to change their permissions\. 

Suppose that your Aurora PostgreSQL DB cluster is configured to audit the same level of activity for all users and databases\. You then decide that you don't want to audit the user `myuser`\. You can turn off auditing for `myuser` with the following SQL command\.

```
ALTER USER myuser SET pgaudit.log TO 'NONE';
```

Then, you can use the following query to check the `user_specific_settings` column for `pgaudit.log` to confirm that the parameter is set to `NONE`\.

```
SELECT
    usename AS user_name,
    useconfig AS user_specific_settings
FROM
    pg_user
WHERE
    usename = 'myuser';
```

You see output such as the following\.

```
 user_name | user_specific_settings
-----------+------------------------
 myuser    | {pgaudit.log=NONE}
(1 row)
```

You can turn off logging for a given user in the midst of their session with the database with the following command\.

```
ALTER USER myuser IN DATABASE mydatabase SET pgaudit.log TO 'none';
```

Use the following query to check the settings column for pgaudit\.log for a specific user and database combination\. 

```
SELECT
    usename AS "user_name",
    datname AS "database_name",
    pg_catalog.array_to_string(setconfig, E'\n') AS "settings"
FROM
    pg_catalog.pg_db_role_setting s
    LEFT JOIN pg_catalog.pg_database d ON d.oid = setdatabase
    LEFT JOIN pg_catalog.pg_user r ON r.usesysid = setrole
WHERE
    usename = 'myuser'
    AND datname = 'mydatabase'
ORDER BY
    1,
    2;
```

You see output similar to the following\.

```
  user_name | database_name |     settings
-----------+---------------+------------------
 myuser    | mydatabase    | pgaudit.log=none
(1 row)
```

After turning off auditing for `myuser`, you decide that you don't want to track changes to `mydatabase`\. You turn off auditing for that specific database by using the following command\.

```
ALTER DATABASE mydatabase SET pgaudit.log to 'NONE';
```

Then, use the following query to check the database\_specific\_settings column to confirm that pgaudit\.log is set to NONE\.

```
SELECT
a.datname AS database_name,
b.setconfig AS database_specific_settings
FROM
pg_database a
FULL JOIN pg_db_role_setting b ON a.oid = b.setdatabase
WHERE
a.datname = 'mydatabase';
```

You see output such as the following\.

```
 database_name | database_specific_settings
---------------+----------------------------
 mydatabase    | {pgaudit.log=NONE}
(1 row)
```

To return settings to the default setting for myuser, use the following command:

```
ALTER USER myuser RESET pgaudit.log;
```

To return settings to their default setting for a database, use the following command\.

```
ALTER DATABASE mydatabase RESET pgaudit.log;
```

To reset user and database to the default setting, use the following command\.

```
ALTER USER myuser IN DATABASE mydatabase RESET pgaudit.log;
```

You can also capture specific events to the log by setting the `pgaudit.log` to one of the other allowed values for the `pgaudit.log` parameter\. For more information, see [List of allowable settings for the `pgaudit.log` parameter](#Appendix.PostgreSQL.CommonDBATasks.pgaudit.reference.pgaudit-log-settings)\.

```
ALTER USER myuser SET pgaudit.log TO 'read';
ALTER DATABASE mydatabase SET pgaudit.log TO 'function';
ALTER USER myuser IN DATABASE mydatabase SET pgaudit.log TO 'read,function'
```

### Reference for the pgAudit extension<a name="Appendix.PostgreSQL.CommonDBATasks.pgaudit.reference"></a>

You can specify the level of detail that you want for your audit log by changing one or more of the parameters listed in this section\. 

#### Controlling pgAudit behavior<a name="Appendix.PostgreSQL.CommonDBATasks.pgaudit.reference.basic-setup.parameters"></a>

You can control the audit logging by changing one or more of the parameters listed in the following table\. 


| Parameter | Description | 
| --- | --- | 
| `pgaudit.log`  | Specifies the statement classes that will be logged by session audit logging\. Allowable values include ddl, function, misc, read, role, write, none, all\. For more information, see [List of allowable settings for the `pgaudit.log` parameter](#Appendix.PostgreSQL.CommonDBATasks.pgaudit.reference.pgaudit-log-settings)\.  | 
| `pgaudit.log_catalog` | When turned on \(set to 1\), adds statements to audit trail if all relations in a statement are in pg\_catalog\. | 
| `pgaudit.log_level` | Specifies the log level to use for log entries\. Allowed values: debug5, debug4, debug3, debug2, debug1, info, notice, warning, log | 
| `pgaudit.log_parameter` | When turned on \(set to 1\), parameters passed with the statement are captured in the audit log\. | 
| `pgaudit.log_relation` | When turned on \(set to 1\), the audit log for the session creates a separate log entry for each relation \(TABLE, VIEW, and so on\) referenced in a SELECT or DML statement\. | 
| `pgaudit.log_statement_once` | Specifies whether logging will include the statement text and parameters with the first log entry for a statement/substatement combination or with every entry\. | 
| `pgaudit.role` | Specifies the master role to use for object audit logging\. The only allowable entry is `rds_pgaudit`\. | 

#### List of allowable settings for the `pgaudit.log` parameter<a name="Appendix.PostgreSQL.CommonDBATasks.pgaudit.reference.pgaudit-log-settings"></a>

 


| Value | Description | 
| --- | --- | 
| none | This is the default\. No database changes are logged\.  | 
| all | Logs everything \(read, write, function, role, ddl, misc\)\.  | 
| ddl | Logs all data definition language \(DDL\) statements that aren't included in the `ROLE` class\. | 
| function | Logs function calls and `DO` blocks\. | 
| misc | Logs miscellaneous commands, such as `DISCARD`, `FETCH`, `CHECKPOINT`, `VACUUM`, and `SET`\. | 
| read | Logs `SELECT` and `COPY` when the source is a relation \(such as a table\) or a query\. | 
| role | Logs statements related to roles and privileges, such as `GRANT`, `REVOKE`, `CREATE ROLE`, `ALTER ROLE`, and `DROP ROLE`\. | 
| write | Logs `INSERT`, `UPDATE`, `DELETE`, `TRUNCATE`, and `COPY` when the destination is a relation \(table\)\. | 

To log multiple event types with session auditing, use a comma\-separated list\. To log all event types, set `pgaudit.log` to `ALL`\. Reboot your DB instance to apply the changes\.

With object auditing, you can refine audit logging to work with specific relations\. For example, you can specify that you want audit logging for `READ` operations on one or more tables\.