# synch/cond/mysys/thread\_var::suspend<a name="ams-waits.suspend"></a>

The `synch/cond/mysys/thread_var::suspend` wait event indicates that threads are suspended because they are waiting on a condition\.

**Topics**
+ [Supported engine versions](#ams-waits.suspend.context.supported)
+ [Context](#ams-waits.suspend.context)
+ [Likely causes of increased waits](#ams-waits.suspend.causes)
+ [Actions](#ams-waits.suspend.actions)

## Supported engine versions<a name="ams-waits.suspend.context.supported"></a>

This wait event information is supported for the following versions:
+ Aurora MySQL version 2 up to 2\.09\.2
+ Aurora MySQL version 1 up to 1\.23\.1

## Context<a name="ams-waits.suspend.context"></a>

The event `synch/cond/mysys/thread_var::suspend` indicates that threads are suspended because they are waiting on a condition\. For example, this wait event occurs when threads are waiting for a table\-level lock\. In this case, we recommend that you investigate your workload to determine which threads might be acquiring table locks on your DB instance\.

## Likely causes of increased waits<a name="ams-waits.suspend.causes"></a>

When the `synch/cond/mysys/thread_var::suspend` event appears more than normal, possibly indicating a performance problem, typical causes include the following:

**Thread waiting on a table\-level lock**  
One or more threads are waiting on a table\-level lock\. In this case, the thread state is `Waiting for table level lock`\.

**Data being sent to the mysqldump client**  
One or more threads are waiting because you are using mysqldump, and the result is being sent to the mysqldump client\. In this case, the thread state is: `Writing to net`\.

## Actions<a name="ams-waits.suspend.actions"></a>

We recommend different actions depending on the causes of your wait event\.

**Topics**
+ [Avoid locking tables](#ams-waits.suspend.actions.locks)
+ [Make sure that backup tools don't lock tables](#ams-waits.suspend.actions.mysqldump)
+ [Long\-running sessions that lock tables](#ams-waits.suspend.actions.long-running-sessions)
+ [Non\-InnoDB temporary table](#ams-waits.suspend.actions.enable-pfs)

### Avoid locking tables<a name="ams-waits.suspend.actions.locks"></a>

Make sure that the application is not explicitly locking the tables using the `LOCK TABLE` statement\. You can check the statements run by applications using Advanced Auditing\. For more information, see [Using Advanced Auditing with an Amazon Aurora MySQL DB cluster](AuroraMySQL.Auditing.md)\.

### Make sure that backup tools don't lock tables<a name="ams-waits.suspend.actions.mysqldump"></a>

If you are using a backup tool, make sure that it isn't locking tables\. For example, if you are using mysqldump, use the `--single-transaction` option so that it doesn't lock tables\. 

### Long\-running sessions that lock tables<a name="ams-waits.suspend.actions.long-running-sessions"></a>

There might be long\-running sessions that have explicitly locked tables\. Run the following SQL statement to check for such sessions\.

```
SELECT 
p.id as session_id, p.user, p.host, p.db, p.command, p.time, p.state, 
SUBSTRING(p.info, 1, 50) AS INFO, 
t.trx_started, unix_timestamp(now()) - unix_timestamp(t.trx_started) as trx_age_seconds, t.trx_rows_modified, t.trx_isolation_level
FROM information_schema.processlist p 
LEFT JOIN information_schema.innodb_trx t 
ON p.id = t.trx_mysql_thread_id;
```

When you identify the session, your options include the following:
+ Contact the application owner or the user\.
+ If the blocking session is idle, consider ending the blocking session\. This action might trigger a long rollback\. To learn how to end a session, see [Ending a session or query](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Appendix.MySQL.CommonDBATasks.html#Appendix.MySQL.CommonDBATasks.End) in the *Amazon RDS User Guide*\.

For more information about identifying blocking transactions, see [Using InnoDB Transaction and Locking Information](https://dev.mysql.com/doc/refman/5.7/en/innodb-information-schema-examples.html) in the MySQL documentation\.

### Non\-InnoDB temporary table<a name="ams-waits.suspend.actions.enable-pfs"></a>

If you are using a non\-InnoDB temporary table, then the database doesn't use row\-level locking, which can result in table locks\. `MyISAM` and `MEMORY` tables are examples of a non\-InnoDB temporary table\. If you are using a non\-InnoDB temporary table, consider switching to an InnoDB memory table\.