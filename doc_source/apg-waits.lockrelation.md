# Lock:Relation<a name="apg-waits.lockrelation"></a>

The `Lock:Relation` event occurs when a query is waiting to acquire a lock on a table or view \(relation\) that's currently locked by another transaction\.

**Topics**
+ [Supported engine versions](#apg-waits.lockrelation.context.supported)
+ [Context](#apg-waits.lockrelation.context)
+ [Likely causes of increased waits](#apg-waits.lockrelation.causes)
+ [Actions](#apg-waits.lockrelation.actions)

## Supported engine versions<a name="apg-waits.lockrelation.context.supported"></a>

This wait event information is supported for all versions of Aurora PostgreSQL\.

## Context<a name="apg-waits.lockrelation.context"></a>

Most PostgreSQL commands implicitly use locks to control concurrent access to data in tables\. You can also use these locks explicitly in your application code with the `LOCK` command\. Many lock modes aren't compatible with each other, and they can block transactions when they're trying to access the same object\. When this happens, Aurora PostgreSQL generates a `Lock:Relation` event\. Some common examples are the following:
+ Exclusive locks such as `ACCESS EXCLUSIVE` can block all concurrent access\. Data definition language \(DDL\) operations such as `DROP TABLE`, `TRUNCATE`, `VACUUM FULL`, and `CLUSTER` acquire `ACCESS EXCLUSIVE` locks implicitly\. `ACCESS EXCLUSIVE` is also the default lock mode for `LOCK TABLE` statements that don't specify a mode explicitly\.
+ Using `CREATE INDEX` (without `CONCURRENTLY`) on a table conflicts with data manipulation language \(DML\) statements `UPDATE`, `DELETE`, and `INSERT`, which acquire `ROW EXCLUSIVE` locks\.

For more information about table\-level locks and conflicting lock modes, see [Explicit Locking](https://www.postgresql.org/docs/13/explicit-locking.html) in the PostgreSQL documentation\.

Blocking queries and transactions typically unblock in one of the following ways:
+ Blocking query – The application can cancel the query or the user can end the process\. The engine can also force the query to end because of a session's statement\-timeout or a deadlock detection mechanism\.
+ Blocking transaction – A transaction stops blocking when it runs a `ROLLBACK` or `COMMIT` statement\. Rollbacks also happen automatically when sessions are disconnected by a client or by network issues, or are ended\. Sessions can be ended when the database engine is shut down, when the system is out of memory, and so forth\.

## Likely causes of increased waits<a name="apg-waits.lockrelation.causes"></a>

When the `Lock:Relation` event occurs more frequently than normal, it can indicate a performance issue\. Typical causes include the following:

**Increased concurrent sessions with conflicting table locks**  
There might be an increase in the number of concurrent sessions with queries that lock the same table with conflicting locking modes\.

**Maintenance operations**  
Health maintenance operations such as `VACUUM` and `ANALYZE` can significantly increase the number of conflicting locks\. `VACUUM FULL` acquires an `ACCESS EXCLUSIVE` lock, and `ANALYSE` acquires a `SHARE UPDATE EXCLUSIVE` lock\. Both types of locks can cause a `Lock:Relation` wait event\. Application data maintenance operations such as refreshing a materialized view can also increase blocked queries and transactions\.

**Locks on reader instances**  
There might be a conflict between the relation locks held by the writer and readers\. Currently, only `ACCESS EXCLUSIVE` relation locks are replicated to reader instances\. However, the `ACCESS EXCLUSIVE` relation lock will conflict with any `ACCESS SHARE` relation locks held by the reader\. This can cause an increase in lock relation wait events on the reader\. 

## Actions<a name="apg-waits.lockrelation.actions"></a>

We recommend different actions depending on the causes of your wait event\.

**Topics**
+ [Reduce the impact of blocking SQL statements](#apg-waits.lockrelation.actions.reduce-blocks)
+ [Minimize the effect of maintenance operations](#apg-waits.lockrelation.actions.maintenance)
+ [Check for reader locks](#apg-waits.lockrelation.actions.readerlocks)

### Reduce the impact of blocking SQL statements<a name="apg-waits.lockrelation.actions.reduce-blocks"></a>

To reduce the impact of blocking SQL statements, modify your application code where possible\. Following are two common techniques for reducing blocks:
+ Use the `NOWAIT` option – Some SQL commands, such as `SELECT` and `LOCK` statements, support this option\. The `NOWAIT` directive cancels the lock\-requesting query if the lock can't be acquired immediately\. This technique can help prevent a blocking session from causing a pile\-up of blocked sessions behind it\.

  For example: Assume that transaction A is waiting on a lock held by  transaction B\. Now, if B requests a lock on a table that’s locked by transaction C, transaction A might be blocked until transaction C completes\. But if transaction B uses a `NOWAIT` when it requests the lock on C, it can fail fast and ensure that transaction A doesn't have to wait indefinitely\.
+ Use `SET lock_timeout` – Set a `lock_timeout` value to limit the time a SQL statement waits to acquire a lock on a relation\. If the lock isn't acquired within the timeout specified, the transaction requesting the lock is cancelled\. Set this value at the session level\.

### Minimize the effect of maintenance operations<a name="apg-waits.lockrelation.actions.maintenance"></a>

Maintenance operations such as `VACUUM` and `ANALYZE` are important\. We recommend that you don't turn them off because you find `Lock:Relation` wait events related to these maintenance operations\. The following approaches can minimize the effect of these operations:
+ Run maintenance operations manually during off\-peak hours\.
+ To reduce `Lock:Relation` waits caused by autovacuum tasks, perform any needed autovacuum tuning\. For information about tuning autovacuum, see [ Working with PostgreSQL autovacuum on Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Appendix.PostgreSQL.CommonDBATasks.Autovacuum.html) in the * Amazon RDS User Guide*\.

### Check for reader locks<a name="apg-waits.lockrelation.actions.readerlocks"></a>

You can see how concurrent sessions on a writer and readers might be holding locks that block each other\. One way to do this is by running queries that return the lock type and relation\. In the table you can find a sequence of queries to two such concurrent sessions, a writer session \(left\-hand column\) and a reader session \(right\-hand column\)\.

The replay process waits for the duration of `max_standby_streaming_delay` before cancelling the reader query\. As shown in the example, the lock timeout of 100ms is well below the default `max_standby_streaming_delay` of 30 seconds\. The lock times out before it's an issue\. 


| Writer session | Reader session | 
| --- |--- |
|  <pre>export WRITER=aurorapg1.12345678910.us-west-1.rds.amazonaws.com<br /><br />psql -h $WRITER<br />psql (15devel, server 10.14) <br />Type "help" for help. </pre>  |  <pre>export READER=aurorapg2.12345678910.us-west-1.rds.amazonaws.com<br /><br />psql -h $READER<br />psql (15devel, server 10.14)<br />Type "help" for help.</pre>  | 
| The writer session creates table `t1` on the writer instance\. The `ACCESS EXCLUSIVE` lock is acquired on the writer immediately, assuming that there are no conflicting queries on the writer\. | 
| --- |
|  <pre>postgres=> CREATE TABLE t1(b integer);<br />CREATE TABLE</pre>  |  | 
| The reader session sets a lock timeout interval of 100 milliseconds\. | 
| --- |
|  |  <pre>postgres=> SET lock_timeout=100;<br />SET</pre>  | 
| The reader session tries to read data from table `t1` on the reader instance\. | 
| --- |
|  |  <pre>postgres=> SELECT * FROM t1;<br /> b<br />---<br />(0 rows)</pre>  | 
| The writer session drops `t1`\. | 
| --- |
|  <pre>postgres=> BEGIN;<br />BEGIN<br />postgres=> DROP TABLE t1;<br />DROP TABLE<br />postgres=></pre>  |  | 
| The query times out and is canceled on the reader\. | 
| --- |
|  |  <pre>postgres=> SELECT * FROM t1;<br />ERROR:  canceling statement due to lock timeout<br />LINE 1: SELECT * FROM t1;<br />                      ^</pre>  | 
| The reader session queries `pg_locks` and `pg_stat_activity` to determine the cause of the error\. The result indicates that the `aurora wal replay` process is holding an `ACCESS EXCLUSIVE` lock on table `t1`\. | 
| --- |
|  |  <pre>postgres=> SELECT locktype, relation, mode, backend_type<br />postgres-> FROM pg_locks l, pg_stat_activity t1<br />postgres-> WHERE l.pid=t1.pid AND relation = 't1'::regclass::oid;<br /> locktype | relation |        mode         |   backend_type<br />----------+----------+---------------------+-------------------<br /> relation | 68628525 | AccessExclusiveLock | aurora wal replay<br />(1 row)</pre>  | 
