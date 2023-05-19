# Aurora MySQL isolation levels<a name="AuroraMySQL.Reference.IsolationLevels"></a>

Learn how DB instances in an Aurora MySQL cluster implement the database property of isolation\. This topic explains how the Aurora MySQL default behavior balances between strict consistency and high performance\. You can use this information to help you decide when to change the default settings based on the traits of your workload\. 

## Available isolation levels for writer instances<a name="AuroraMySQL.Reference.IsolationLevels.writer"></a>

You can use the isolation levels `REPEATABLE READ`, `READ COMMITTED`, `READ UNCOMMITTED`, and `SERIALIZABLE` on the primary instance of an Aurora MySQL DB cluster\. These isolation levels work the same in Aurora MySQL as in RDS for MySQL\.

## REPEATABLE READ isolation level for reader instances<a name="AuroraMySQL.Reference.IsolationLevels.reader"></a>

By default, Aurora MySQL DB instances that are configured as read\-only Aurora Replicas always use the `REPEATABLE READ` isolation level\. These DB instances ignore any `SET TRANSACTION ISOLATION LEVEL` statements and continue using the `REPEATABLE READ` isolation level\.

You can't set the isolation level for reader DB instances using DB parameters or DB cluster parameters\.

## READ COMMITTED isolation level for reader instances<a name="AuroraMySQL.Reference.IsolationLevels.relaxed"></a>

If your application includes a write\-intensive workload on the primary instance and long\-running queries on the Aurora Replicas, you might experience substantial purge lag\. *Purge lag* happens when internal garbage collection is blocked by long\-running queries\. The symptom that you see is a high value for `history list length` in the output from the `SHOW ENGINE INNODB STATUS` command\. You can monitor this value using the `RollbackSegmentHistoryListLength` metric in CloudWatch\. Substantial purge lag can reduce the effectiveness of secondary indexes, decrease overall query performance, and lead to wasted storage space\.

If you experience such issues, you can set an Aurora MySQL session\-level configuration setting, `aurora_read_replica_read_committed`, to use the `READ COMMITTED` isolation level on Aurora Replicas\. When you apply this setting, you can help reduce slowdowns and wasted space that can result from performing long\-running queries at the same time as transactions that modify your tables\.

We recommend making sure that you understand the specific Aurora MySQL behavior of the `READ COMMITTED` isolation before using this setting\. The Aurora Replica `READ COMMITTED` behavior complies with the ANSI SQL standard\. However, the isolation is less strict than typical MySQL `READ COMMITTED` behavior that you might be familiar with\. Therefore, you might see different query results under `READ COMMITTED` on an Aurora MySQL read replica than you might see for the same query under `READ COMMITTED` on the Aurora MySQL primary instance or on RDS for MySQL\. You might consider using the `aurora_read_replica_read_committed` setting for such cases as a comprehensive report that scans a very large database\. In contrast, you might avoid it for short queries with small result sets, where precision and repeatability are important\.

The `READ COMMITTED` isolation level isn't available for sessions within a secondary cluster in an Aurora global database that use the write forwarding feature\. For information about write forwarding, see [Using write forwarding in an Amazon Aurora global database](aurora-global-database-write-forwarding.md)\.

### Using READ COMMITTED for readers<a name="AuroraMySQL.Reference.IsolationLevels.relaxed.enabling"></a>

To use the `READ COMMITTED` isolation level for Aurora Replicas, set the `aurora_read_replica_read_committed` configuration setting to `ON`\. Use this setting at the session level while connected to a specific Aurora Replica\. To do so, run the following SQL commands\.

```
set session aurora_read_replica_read_committed = ON;
set session transaction isolation level read committed;
```

You might use this configuration setting temporarily to perform interactive, one\-time queries\. You might also want to run a reporting or data analysis application that benefits from the `READ COMMITTED` isolation level, while leaving the default setting unchanged for other applications\.

When the `aurora_read_replica_read_committed` setting is turned on, use the `SET TRANSACTION ISOLATION LEVEL` command to specify the isolation level for the appropriate transactions\.

```
set transaction isolation level read committed;
```

### Differences in READ COMMITTED behavior on Aurora replicas<a name="AuroraMySQL.Reference.IsolationLevels.relaxed.behavior"></a>

The `aurora_read_replica_read_committed` setting makes the `READ COMMITTED` isolation level available for an Aurora Replica, with consistency behavior that is optimized for long\-running transactions\. The `READ COMMITTED` isolation level on Aurora Replicas has less strict isolation than on Aurora primary instances\. For that reason, enable this setting only on Aurora Replicas where you know that your queries can accept the possibility of certain types of inconsistent results\.

Your queries can experience certain kinds of read anomalies when the `aurora_read_replica_read_committed` setting is turned on\. Two kinds of anomalies are especially important to understand and handle in your application code\. A *non\-repeatable read* occurs when another transaction commits while your query is running\. A long\-running query can see different data at the start of the query than it sees at the end\. A *phantom read* occurs when other transactions cause existing rows to be reorganized while your query is running, and one or more rows are read twice by your query\.

Your queries might experience inconsistent row counts as a result of phantom reads\. Your queries might also return incomplete or inconsistent results due to non\-repeatable reads\. For example, suppose that a join operation refers to tables that are concurrently modified by SQL statements such as `INSERT` or `DELETE`\. In this case, the join query might read a row from one table but not the corresponding row from another table\.

The ANSI SQL standard allows both of these behaviors for the `READ COMMITTED` isolation level\. However, those behaviors are different than the typical MySQL implementation of `READ COMMITTED`\. Thus, before enabling the `aurora_read_replica_read_committed` setting, check any existing SQL code to verify if it operates as expected under the looser consistency model\.

Row counts and other results might not be strongly consistent under the `READ COMMITTED` isolation level while this setting is enabled\. Thus, you typically enable the setting only while running analytic queries that aggregate large amounts of data and don't require absolute precision\. If you don't have these kinds of long\-running queries alongside a write\-intensive workload, you probably don't need the `aurora_read_replica_read_committed` setting\. Without the combination of long\-running queries and a write\-intensive workload, you're unlikely to encounter issues with the length of the history list\.

**Example Queries showing isolation behavior for READ COMMITTED on Aurora replicas**  
The following example shows how `READ COMMITTED` queries on an Aurora Replica might return non\-repeatable results if transactions modify the associated tables at the same time\. The table `BIG_TABLE` contains 1 million rows before any queries start\. Other data manipulation language \(DML\) statements add, remove, or change rows while they are running\.  
The queries on the Aurora primary instance under the `READ COMMITTED` isolation level produce predictable results\. However, the overhead of keeping the consistent read view for the lifetime of every long\-running query can lead to expensive garbage collection later\.  
The queries on the Aurora Replica under the `READ COMMITTED` isolation level are optimized to minimize this garbage collection overhead\. The tradeoff is that the results might vary depending on whether the queries retrieve rows that are added, removed, or reorganized by transactions that are committed while the query is running\. The queries are allowed to consider these rows, but aren't required to\. For demonstration purposes, the queries check only the number of rows in the table by using the `COUNT(*)` function\.  


|  Time  |  DML statement on Aurora primary instance  |  Query on Aurora primary instance with READ COMMITTED  |  Query on Aurora replica with READ COMMITTED  | 
| --- | --- | --- | --- | 
|  T1  |  INSERT INTO big\_table SELECT \* FROM other\_table LIMIT 1000000; COMMIT;   |  |  | 
|  T2  |  |  Q1: SELECT COUNT\(\*\) FROM big\_table;  |  Q2: SELECT COUNT\(\*\) FROM big\_table;  | 
|  T3  |  INSERT INTO big\_table \(c1, c2\) VALUES \(1, 'one more row'\); COMMIT;   |  |  | 
|  T4  |  |  If Q1 finishes now, result is 1,000,000\.  |  If Q2 finishes now, result is 1,000,000 or 1,000,001\.  | 
|  T5  |  DELETE FROM big\_table LIMIT 2; COMMIT;   |  |  | 
|  T6  |  |  If Q1 finishes now, result is 1,000,000\.  |  If Q2 finishes now, result is 1,000,000 or 1,000,001 or 999,999 or 999,998\.  | 
|  T7  |  UPDATE big\_table SET c2 = CONCAT\(c2,c2,c2\); COMMIT;   |  |  | 
|  T8  |  |  If Q1 finishes now, result is 1,000,000\.  |  If Q2 finishes now, result is 1,000,000 or 1,000,001 or 999,999, or possibly some higher number\.  | 
|  T9  |  |  Q3: SELECT COUNT\(\*\) FROM big\_table;  |  Q4: SELECT COUNT\(\*\) FROM big\_table;  | 
|  T10  |  |  If Q3 finishes now, result is 999,999\.  |  If Q4 finishes now, result is 999,999\.  | 
|  T11  |  |  Q5: SELECT COUNT\(\*\) FROM parent\_table p JOIN child\_table c ON \(p\.id = c\.id\) WHERE p\.id = 1000;  |  Q6: SELECT COUNT\(\*\) FROM parent\_table p JOIN child\_table c ON \(p\.id = c\.id\) WHERE p\.id = 1000;  | 
|  T12  |   INSERT INTO parent\_table \(id, s\) VALUES \(1000, 'hello'\); INSERT INTO child\_table \(id, s\) VALUES \(1000, 'world'\); COMMIT;   |  |  | 
|  T13  |  |  If Q5 finishes now, result is 0\.  |  If Q6 finishes now, result is 0 or 1\.  | 
If the queries finish quickly, before any other transactions perform DML statements and commit, the results are predictable and the same between the primary instance and the Aurora Replica\. Let's examine the differences in behavior in detail, starting with the first query\.  
The results for Q1 are highly predictable because `READ COMMITTED` on the primary instance uses a strong consistency model similar to the `REPEATABLE READ` isolation level\.  
The results for Q2 might vary depending on what transactions are committed while that query is running\. For example, suppose that other transactions perform DML statements and commit while the queries are running\. In this case, the query on the Aurora Replica with the `READ COMMITTED` isolation level might or might not take the changes into account\. The row counts aren't predictable in the same way as under the `REPEATABLE READ` isolation level\. They also aren't as predictable as queries running under the `READ COMMITTED` isolation level on the primary instance, or on an RDS for MySQL instance\.  
The `UPDATE` statement at T7 doesn't actually change the number of rows in the table\. However, by changing the length of a variable\-length column, this statement can cause rows to be reorganized internally\. A long\-running `READ COMMITTED` transaction might see the old version of a row, and later within the same query see the new version of the same row\. The query can also skip both the old and new versions of the row, so the row count might be different than expected\.  
The results of Q5 and Q6 might be identical or slightly different\. Query Q6 on the Aurora Replica under `READ COMMITTED` is able to see, but is not required to see, the new rows that are committed while the query is running\. It might also see the row from one table, but not from the other table\. If the join query doesn't find a matching row in both tables, it returns a count of zero\. If the query does find both the new rows in `PARENT_TABLE` and `CHILD_TABLE`, the query returns a count of one\. In a long\-running query, the lookups from the joined tables might happen at widely separated times\.  
These differences in behavior depend on the timing of when transactions are committed and when the queries process the underlying table rows\. Thus, you're most likely to see such differences in report queries that take minutes or hours and that run on Aurora clusters processing OLTP transactions at the same time\. These are the kinds of mixed workloads that benefit the most from the `READ COMMITTED` isolation level on Aurora Replicas\.