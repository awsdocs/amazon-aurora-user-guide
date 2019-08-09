# Working with Parallel Query for Amazon Aurora MySQL<a name="aurora-mysql-parallel-query"></a>

 Following, you can find a description of the parallel query performance optimization for Amazon Aurora with MySQL compatibility\. This feature uses a special execution path for certain data\-intensive queries, taking advantage of the Aurora shared storage architecture\. Currently, Aurora MySQL versions that are compatible with MySQL 5\.6 support parallel query\. Parallel query works best with Aurora MySQL DB clusters that have tables with millions of rows and analytic queries that take minutes or hours to complete\. 

**Topics**
+ [Overview of Parallel Query for Aurora MySQL](#aurora-mysql-parallel-query-overview)
+ [Administering a Parallel Query Cluster](#aurora-mysql-parallel-query-admin)
+ [Upgrade Considerations for Parallel Query](#aurora-mysql-parallel-query-upgrade)
+ [Creating a DB Cluster that Works with Parallel Query](#aurora-mysql-parallel-query-creating-cluster)
+ [Enabling and Disabling Parallel Query](#aurora-mysql-parallel-query-enabling)
+ [Performance Tuning for Parallel Query](#aurora-mysql-parallel-query-performance)
+ [Creating Schema Objects to Take Advantage of Parallel Query](#aurora-mysql-parallel-query-tables)
+ [Verifying Which Statements Use Parallel Query](#aurora-mysql-parallel-query-verifying)
+ [Monitoring Parallel Query](#aurora-mysql-parallel-query-monitoring)
+ [How Parallel Query Works with SQL Constructs](#aurora-mysql-parallel-query-sql)

## Overview of Parallel Query for Aurora MySQL<a name="aurora-mysql-parallel-query-overview"></a>

 Aurora MySQL parallel query is an optimization that parallelizes some of the I/O and computation involved in processing data\-intensive queries\. The work that is parallelized includes retrieving rows from storage, extracting column values, and determining which rows match the conditions in the `WHERE` clause and join clauses\. This data\-intensive work is delegated \(in database optimization terms, *pushed down*\) to multiple nodes in the Aurora distributed storage layer\. Without parallel query, each query brings all the scanned data to a single node within the Aurora MySQL cluster \(the head node\) and performs all the query processing there\. 

 When the parallel query feature is enabled, the Aurora MySQL engine automatically determines when queries can benefit, without requiring SQL changes such as hints or table attributes\. In the following sections, you can find an explanation of when parallel query is applied to a query\. You can also find how to make sure that parallel query is applied where it provides the most benefit\. 

**Note**  
 The parallel query optimization provides the most benefit for long\-running queries that take minutes or hours to complete\. Aurora MySQL generally doesn't perform parallel query optimization for inexpensive queries or if another optimization technique makes more sense, such as query caching, buffer pool caching, or index lookups\. See [Verifying Which Statements Use Parallel Query](#aurora-mysql-parallel-query-verifying) if you find parallel query isn't being used when you expect it\. 

**Topics**
+ [Benefits](#aurora-mysql-parallel-query-benefits)
+ [Architecture](#aurora-mysql-parallel-query-architecture)
+ [Limitations](#aurora-mysql-parallel-query-limitations)

### Benefits<a name="aurora-mysql-parallel-query-benefits"></a>

 With parallel query, you can run data\-intensive analytic queries on Aurora MySQL tables, in many cases with an order\-of\-magnitude performance improvement over the traditional division of labor for query processing\. 

 Benefits of parallel query include the following: 
+  Improved I/O performance, due to parallelizing physical read requests across multiple storage nodes\. 
+  Reduced network traffic\. Aurora doesn't transmit entire data pages from storage nodes to the head node and then filter out unnecessary rows and columns afterward\. Instead, Aurora transmits compact tuples containing only the column values needed for the result set\. 
+  Reduced CPU usage on the head node, due to pushing down function execution, row filtering, and column projection for the `WHERE` clause\. 
+  Reduced memory pressure on the buffer pool\. The pages processed by the parallel query aren't added to the buffer pool, which reduces the chance of a data\-intensive scan evicting frequently used data from the buffer pool\. 
+  Potentially reduced data duplication in your extract, transform, load \(ETL\) pipeline, by making it practical to perform long\-running analytic queries on existing data\. 

### Architecture<a name="aurora-mysql-parallel-query-architecture"></a>

 The parallel query feature uses the major architectural principles of Aurora MySQL: decoupling the database engine from the storage subsystem, and reducing network traffic by streamlining communication protocols\. Aurora MySQL uses these techniques to speed up write\-intensive operations such as redo log processing\. Parallel query applies the same principles to read operations\. 

**Note**  
 The architecture of Aurora MySQL parallel query differs from that of similarly named features in other database systems\. Aurora MySQL parallel query doesn't involve symmetric multiprocessing \(SMP\) and so doesn't depend on the CPU capacity of the database server\. The parallel execution happens in the storage layer, independent of the Aurora MySQL server that serves as the query coordinator\. 

 By default, without parallel query, the processing for an Aurora query involves transmitting raw data to a single node within the Aurora cluster \(the *head node*\) and performing all further processing in a single thread on that single node\. With parallel query, much of this I/O\-intensive and CPU\-intensive work is delegated to nodes in the storage layer\. Only the compact rows of the result set are transmitted back to the head node, with rows already filtered, and column values already extracted and transformed\. The performance benefit comes from the reduction in network traffic, reduction in CPU usage on the head node, and parallelizing the I/O across the storage nodes\. The amount of parallel I/O, filtering, and projection is independent of the number of DB instances in the Aurora cluster that runs the query\. 

### Limitations<a name="aurora-mysql-parallel-query-limitations"></a>

 The following limitations apply to the parallel query feature: 
+  Currently, Aurora MySQL versions that are compatible with MySQL 5\.6 support parallel query\. \(Note that the PostgreSQL database engine has an unrelated feature that is also called "parallel query"\.\)
+  Currently, you can only use parallel query with the following instance classes: 
  +  All instance classes in the db\.r3 series\. 
  +  All instance classes in the db\.r4 series\. 
**Note**  
 You can't create T2 instances for parallel query\. 
+  The parallel query option is available in the following regions: 
  + US East \(N\. Virginia\)
  + US East \(Ohio\)
  + US West \(Oregon\)
  + EU \(Ireland\)
  + Asia Pacific \(Tokyo\)
+  Using parallel query requires creating a new cluster, or restoring from an existing Aurora MySQL cluster snapshot, as described following\. 
+  The Performance Insights feature is currently not available for clusters that are enabled for parallel query\. 
+  The backtrack feature is currently not available for clusters that are enabled for parallel query\. 
+  You can't stop and start DB clusters that are enabled for parallel query\.  
+  Currently, partitioned tables aren't supported for parallel query\. You can use partitioned tables in parallel query clusters\. Queries against those tables use the non\-parallel query execution path\. 
**Note**  
 A join, union, or other multipart query can partially use parallel query, even if some query blocks refer to partitioned tables\. The query blocks that refer only to nonpartitioned tables can use the parallel query optimization\. 
+  To work with parallel query, currently a table must use the `COMPACT` row format, which requires the Antelope file format of the InnoDB storage engine\. 
+  `TEXT`, `BLOB`, and `GEOMETRY` data types aren't supported with parallel query\. A query that refers to any columns of these types can't use parallel query\. 
+  Variable\-length columns \(`VARCHAR` and `CHAR` data types\) are compatible with parallel query up to a maximum declared length of 768 bytes\. A query that refers to any columns of the types declared with a longer maximum length can't use parallel query\. For columns that use multibyte character sets, the byte limit takes into account the maximum number of bytes in the character set\. For example, for the character set `utf8mb4` \(which has a maximum character length of 4 bytes\), a `VARCHAR(192)` column is compatible with parallel query but a `VARCHAR(193)` column isn't\. 

   The `JSON` data type is a `BLOB`\-like type that is only available in Aurora is compatible with MySQL 5\.7\. parallel query is only available in the Aurora compatible with MySQL 5\.6\. Thus, this type currently can't be present in a table in a cluster that includes the parallel query feature\. 
+  Currently, parallel query isn't used for tables that contain a full\-text search index, regardless of whether the query refers to such indexed columns or uses the `MATCH()` operator\. 
+  Currently, parallel query isn't used for any query block that includes a `LIMIT` clause\. Parallel query might still be used for earlier query phases with `GROUP` by, `ORDER BY`, or joins\. 
+  Currently, correlated subqueries can't use the parallel query optimization\. 
+  Parallel query works with `SELECT` statements that do no writes or locking, and only under the `REPEATABLE READ` isolation level\. For example, parallel query doesn't work with `SELECT FOR UPDATE` or with the `WHERE` clauses of `UPDATE` or `DELETE` statements\. 
+  Parallel query is only available for tables for which no fast online data definition language \(DDL\) operations are pending\. 
+  The parallel query feature works with most, but not all, operators and functions in the `WHERE` clause\. For examples that illustrate compatible operations, see [How Parallel Query Works with SQL Constructs](#aurora-mysql-parallel-query-sql)\. 
+  Each Aurora DB instance can run only a certain number of parallel query sessions at one time\. If a query has multiple parts that use parallel query, such as subqueries, joins, or `UNION` operators, those phases run in sequence\. The statement only counts as a single parallel query session at any one time\. You can monitor the number of active sessions using the [parallel query status variables](#aurora-mysql-parallel-query-monitoring)\. You can check the limit on concurrent sessions for a given DB instance by querying the status variable `Aurora_pq_max_concurrent_requests`\. 

## Administering a Parallel Query Cluster<a name="aurora-mysql-parallel-query-admin"></a>

 Administering a cluster that is enabled for parallel query requires setup steps \(either creating or restoring a full Aurora MySQL cluster\) and deciding how broadly to enable parallel query across the cluster\. 

 You might need to create new versions of some large tables where parallel query is useful, for example to make the table nonpartitioned or to remove full\-text search indexes\. For details, see [Creating Schema Objects to Take Advantage of Parallel Query](#aurora-mysql-parallel-query-tables)\. 

 After setup is complete, ongoing administration involves monitoring performance and removing obstacles to parallel query usage\. For those instructions, see [Performance Tuning for Parallel Query](#aurora-mysql-parallel-query-performance)\. 

## Upgrade Considerations for Parallel Query<a name="aurora-mysql-parallel-query-upgrade"></a>

 Currently, you can't do an in\-place upgrade of an Aurora MySQL cluster to enable the parallel query feature\. Using parallel query requires creating a new cluster, or restoring from an existing Aurora MySQL 5\.6 cluster snapshot\. 

## Creating a DB Cluster that Works with Parallel Query<a name="aurora-mysql-parallel-query-creating-cluster"></a>

 To create an Aurora MySQL cluster with parallel query, add new instances to it, or perform other administrative operations, you use the same AWS Management Console and AWS CLI techniques that you do with other Aurora MySQL clusters\. You can create a new cluster to work with parallel query\. You can also create a DB cluster to work with parallel query by restoring from a snapshot of a MySQL 5\.6\-compatible database\. If you aren't familiar with the process for creating a new Aurora MySQL cluster, you can find background information and prerequisites in [Creating an Amazon Aurora DB Cluster](Aurora.CreateInstance.md)\. 

 However, certain options are different: 
+  When you choose an Aurora MySQL engine version, make sure to choose the latest engine that is compatible with MySQL 5\.6\. Currently, Aurora MySQL versions that are compatible with MySQL 5\.6 support parallel query\. 
+  When you create or restore the DB cluster, make sure to choose the **parallelquery** engine mode\. 

 Whether you create a new cluster or restore from a snapshot, you use the same techniques to add new DB instances that you do with other Aurora MySQL clusters\. 

### Creating a Parallel Query Cluster Using the Console<a name="aurora-mysql-parallel-query-creating-cluster-console"></a>

 You can create a new parallel query cluster with the console as described following\. 

**To create a parallel query cluster with the AWS Management Console**

1.  Follow the general AWS Management Console procedure in [Creating an Amazon Aurora DB Cluster](Aurora.CreateInstance.md)\. 

1.  On the **Select engine** screen, choose the MySQL 5\.6\-compatible edition of Aurora\. 

1. On the **Specify DB details** screen, for **Capacity type, **choose **Provisioned with Aurora parallel query enabled** as in the screenshot following\.   
![\[Choose the capacity type "Provisioned with Aurora parallel query enabled"\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/AuroraPQDBDetails.png)

**To restore a snapshot to a parallel query cluster with the AWS Management Console**

1.  Locate a snapshot of a MySQL 5\.6\-compatible database instance\. 

1.  Follow the general AWS Management Console procedure in [Restoring from a DB Cluster Snapshot](USER_RestoreFromSnapshot.md)\. 

1.  For **DB Engine Mode**, choose **parallelquery**, as shown in the screenshot following\.   
![\[Choose aurora-mysql-pq\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/AuroraPQRestoreInstance.png)

**To verify that a new cluster can use parallel query**

1. Create or restore a cluster using the preceding techniques\.

1. Check that the `aurora_pq_supported` configuration setting is true\.

   ```
   mysql> select @@aurora_pq_supported;
   +-----------------------+
   | @@aurora_pq_supported |
   +-----------------------+
   |                     1 |
   +-----------------------+
   ```

### Creating a Parallel Query Cluster Using the CLI<a name="aurora-mysql-parallel-query-creating-cluster-cli"></a>

 You can create a new parallel query cluster with the CLI as described following\. 

**To create a parallel query cluster with the AWS CLI**

1.  Check which combinations of Aurora MySQL version, AWS instance class, AWS Region, Availability Zone, and so on, are available for parallel query clusters\. To do so, use the `aws rds describe-orderable-db-instance-options` command, which produces output in JSON format\. The following code example shows how\. 

   ```
   # See all choices for all AWS Regions.
   aws rds describe-orderable-db-instance-options --engine aurora --engine-mode parallelquery
   
   # See choices only for a particular AWS Region.
   aws rds describe-orderable-db-instance-options --engine aurora --engine-mode parallelquery \
     --region us-east-2
   ```

1.  Follow the general AWS CLI procedure in [Creating an Amazon Aurora DB Cluster](Aurora.CreateInstance.md)\. 

1.  Specify the following set of options: 
   +  For the `--engine` option, use `aurora`\. 
   +  For the `--engine-mode` option, use `parallelquery`\. The `--engine-mode` parameter applies to the `create-db-cluster` operation\. Then the engine mode of the cluster is used automatically by subsequent `create-db-instance` operations\. 
   +  For the `--engine-version` option, use `5.6.10a`\. 

      The following code example shows how\. 

     ```
     aws rds create-db-cluster --db-cluster-identifier $CLUSTER_ID
       --engine aurora --engine-mode parallelquery --engine-version 5.6.10a \
       --master-username $MASTER_USER_ID --master-user-password $MASTER_USER_PW \
       --db-subnet-group-name $SUBNET_GROUP --vpc-security-group-ids $SECURITY_GROUP
     
     aws rds create-db-instance --db-instance-identifier ${INSTANCE_ID}-1 \
       --engine aurora \
       --db-cluster-identifier $CLUSTER_ID --db-instance-class $INSTANCE_CLASS
     ```

1.  Verify that a cluster you created or restored has the parallel query feature available\. Check that the `aurora_pq_supported` configuration setting is true\. 

   ```
   mysql> select @@aurora_pq_supported;
   +-----------------------+
   | @@aurora_pq_supported |
   +-----------------------+
   |                     1 |
   +-----------------------+
   ```

**To restore a snapshot to a parallel query cluster with the AWS CLI**

1. Check which combinations of Aurora MySQL version, AWS instance class, AWS Region, Availability Zone, and so on, are available for parallel query clusters\. To do so, use the `aws rds describe-orderable-db-instance-options` command, which produces output in JSON format\. The following code example shows how\. 

   ```
   # See all choices for all AWS Regions.
   aws rds describe-orderable-db-instance-options --engine aurora --engine-mode parallelquery
   
   # See choices only for a particular AWS Region.
   aws rds describe-orderable-db-instance-options --engine aurora --engine-mode parallelquery \
     --region us-east-2
   ```

1.  Locate a snapshot from a MySQL 5\.6\-compatible database\. 

1.  Follow the general AWS CLI procedure in [Restoring from a DB Cluster Snapshot](USER_RestoreFromSnapshot.md)\. 

1. For the `--engine-mode` option, specify `parallelquery`\. The following code example shows how\. 

   ```
   aws rds restore-db-instance-from-db-snapshot \
     --db-instance-identifier mynewdbinstance \
     --db-snapshot-identifier mydbsnapshot \
     --engine-mode parallelquery
   ```

1.  Verify that a cluster you created or restored has the parallel query feature available\. Check that the `aurora_pq_supported` configuration setting is true\. 

   ```
   mysql> select @@aurora_pq_supported;
   +-----------------------+
   | @@aurora_pq_supported |
   +-----------------------+
   |                     1 |
   +-----------------------+
   ```

## Enabling and Disabling Parallel Query<a name="aurora-mysql-parallel-query-enabling"></a>

 You can enable and disable parallel query dynamically at both the global and session level for a DB instance by using the **aurora\_pq** option\. On clusters where the parallel query feature is available, the parameter is enabled by default\. 

```
mysql> select @@aurora_pq;
+-------------+
| @@aurora_pq |
+-------------+
|           1 |
+-------------+
```

 To toggle the `aurora_pq` parameter at the session level, for example through the `mysql` command line or within a JDBC or ODBC application, use the standard methods to change a client configuration setting\. For example, the command on the standard MySQL client is `set session aurora_pq = {'ON'/'OFF'}`\. You can also add the session\-level parameter to the JDBC configuration or within your application code to enable or disable parallel query dynamically\. 

 Currently, when you change the global setting for the `aurora_pq` parameter, you must do so for the whole cluster, not for individual DB instances\. To toggle the `aurora_pq` parameter at the cluster level, use the techniques for working with parameter groups, as described in [Working with DB Parameter Groups and DB Cluster Parameter Groups](USER_WorkingWithParamGroups.md)\. Follow these steps: 

1.  Create a custom cluster parameter group\. 

1.  Update `aurora_pq` to the desired value\. 

1.  Attach the custom cluster parameter group to the Aurora cluster where you intend to use the parallel query feature\. 

1.  Restart all the DB instances of the cluster\. 

 You can modify the parallel query parameter by using the [ModifyDBClusterParameterGroup](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBClusterParameterGroup.html) API operation or the AWS Management Console\. 

**Note**  
 When parallel query is enabled, Aurora MySQL determines whether to use it at runtime for each query\. In the case of joins, unions, subqueries, and so on, Aurora MySQL determines whether to use parallel query at runtime for each query block\. For details, see [Verifying Which Statements Use Parallel Query](#aurora-mysql-parallel-query-verifying) and [How Parallel Query Works with SQL Constructs](#aurora-mysql-parallel-query-sql)\. 

### Enabling and Disabling Parallel Query for a DB Instance Using the Console<a name="aurora-mysql-parallel-query-enabling-console"></a>

 You can enable or disable parallel query at the DB instance level by working with parameter groups\. 

**To enable or disable parallel query for an Aurora MySQL cluster with the AWS Management Console**

1.  Create a custom parameter group, as described in [Working with DB Parameter Groups and DB Cluster Parameter Groups](USER_WorkingWithParamGroups.md)\. 

1.  Update **aurora\_pq** to **0** \(disabled\) or **1** \(enabled\), as shown in the following screenshot\. On clusters where the parallel query feature is available, **aurora\_pq** is enabled by default\.   
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/AuroraPQEnable.png)

1.  Attach the custom cluster parameter group to the Aurora DB cluster where you intend to use the parallel query feature\. 

### Enabling and Disabling Parallel Query for a DB Instance Using the CLI<a name="aurora-mysql-parallel-query-enabling-cli"></a>

 You can modify the parallel query parameter by using the `modify-db-cluster-parameter-group` command\. 

**To enable or disable parallel query for a DB instance with the CLI**
+  Modify the parallel query parameter by using the `modify-db-cluster-parameter-group` command\. 

 You can also enable or disable parallel query at the session level, for example through the `mysql` command line or within a JDBC or ODBC application\. To do so, use the standard methods to change a client configuration setting\. For example, the command on the standard MySQL client is `set session aurora_pq = {'ON'/'OFF'}`\. 

 You can also add the session\-level parameter to the JDBC configuration or within your application code to enable or disable parallel query dynamically\. 

## Performance Tuning for Parallel Query<a name="aurora-mysql-parallel-query-performance"></a>

 To manage the performance of a workload with parallel query, make sure that parallel query is used for the queries where this optimization helps the most\. 

 To do so, you can do the following: 
+  Monitor which queries use parallel query\. 
+  Verify that it is being used for the most data\-intensive and long\-running queries\. 
+  Fine\-tune conditions on your cluster to enable parallel query to apply to the queries you expect, and to run with the right level of concurrency for your workload\. 

## Creating Schema Objects to Take Advantage of Parallel Query<a name="aurora-mysql-parallel-query-tables"></a>

 Because parallel query requires tables to use the `ROW_FORMAT=Compact` setting, check your Aurora configuration settings for any changes to the `INNODB_FILE_FORMAT` configuration option\. \(Alternative `ROW_FORMAT` settings on the `CREATE TABLE` statement require the `INNODB_FILE_FORMAT` configuration option to already be set to `'Barracuda'`\.\) Issue the `SHOW TABLE STATUS` statement to confirm the row format for all the tables in a database\. 

 Parallel query currently requires tables to be nonpartitioned\. Thus, check your `CREATE TABLE` statements and `SHOW CREATE TABLE` output and remove any `PARTITION BY` clauses\. For existing partitioned tables, first copy the data into nonpartitioned tables with the same column definitions and indexes\. Then rename old and new tables so that the nonpartitioned table is used by existing queries and ETL workflows\. 

 Before changing your schema to enable parallel query to work with more tables, conduct tests to confirm if parallel query results in a net increase in performance for those tables\. Also, make sure that the schema requirements for parallel query are otherwise compatible with your goals\. 

 For example, before switching from `ROW_FORMAT=Compressed` to `ROW_FORMAT=Compact`, test the performance of workloads against the original and new tables\. Also, consider other potential effects such as increased data volume\. 

## Verifying Which Statements Use Parallel Query<a name="aurora-mysql-parallel-query-verifying"></a>

 In typical operation, you don't need to perform any special actions to take advantage of parallel query\. After a query meets the essential requirements for parallel query, the query optimizer automatically decides whether to use parallel query for each specific query\. 

 If you run experiments in a development or test environment, you might find that parallel query is not used because your tables are too small in number of rows or overall data volume\. The data for the table might also be entirely in the buffer pool, especially for tables you created recently to perform experiments\. 

 As you monitor or tune cluster performance, you need to decide whether parallel query is being used in the appropriate contexts\. You might adjust the database schema, settings, SQL queries, or even the cluster topology and application connection settings to take advantage of this feature\. 

 To check if a query is using parallel query, check the query execution plan \(also known as the "explain plan"\) by running the [EXPLAIN](https://dev.mysql.com/doc/refman/5.6/en/execution-plan-information.html) statement\. For examples of how SQL statements, clauses, and expressions affect `EXPLAIN` output for parallel query, see [How Parallel Query Works with SQL Constructs](#aurora-mysql-parallel-query-sql)\. 

 The following example demonstrates the difference between a traditional execution plan and a parallel query plan\. This query is Query 3 from the TPC\-H benchmark\. Many of the sample queries throughout this section use the tables from the TPC\-H dataset\.  

```
SELECT l_orderkey,
  sum(l_extendedprice * (1 - l_discount)) AS revenue,
  o_orderdate,
  o_shippriority
FROM customer,
  orders,
  lineitem
WHERE c_mktsegment = 'AUTOMOBILE'
AND c_custkey = o_custkey
AND l_orderkey = o_orderkey
AND o_orderdate < date '1995-03-13'
AND l_shipdate > date '1995-03-13'
GROUP BY l_orderkey,
  o_orderdate,
  o_shippriority
ORDER BY revenue DESC,
  o_orderdate LIMIT 10;
```

 With parallel query disabled, the query might have an execution plan like the following, which uses hash join but not parallel query\. 

```
+----+-------------+----------+...+-----------+-----------------------------------------------------------------+
| id | select_type | table    |...| rows      | Extra                                                           |
+----+-------------+----------+...+-----------+-----------------------------------------------------------------+
|  1 | SIMPLE      | customer |...|   5798330 | Using where; Using index; Using temporary; Using filesort       |
|  1 | SIMPLE      | orders   |...| 154545408 | Using where; Using join buffer (Hash Join Outer table orders)   |
|  1 | SIMPLE      | lineitem |...| 606119300 | Using where; Using join buffer (Hash Join Outer table lineitem) |
+----+-------------+----------+...+-----------+-----------------------------------------------------------------+
```

 After parallel query is enabled, two steps in this execution plan can use the parallel query optimization, as shown under the `Extra` column in the `EXPLAIN` output\. The I/O\-intensive and CPU\-intensive processing for those steps is pushed down to the storage layer\. 

```
+----+...+--------------------------------------------------------------------------------------------------------------------------------+
| id |...| Extra                                                                                                                          |
+----+...+--------------------------------------------------------------------------------------------------------------------------------+
|  1 |...| Using where; Using index; Using temporary; Using filesort                                                                      |
|  1 |...| Using where; Using join buffer (Hash Join Outer table orders); Using parallel query (4 columns, 1 filters, 1 exprs; 0 extra)   |
|  1 |...| Using where; Using join buffer (Hash Join Outer table lineitem); Using parallel query (4 columns, 1 filters, 1 exprs; 0 extra) |
+----+...+--------------------------------------------------------------------------------------------------------------------------------+
```

 For information about how to interpret `EXPLAIN` output for a parallel query and the parts of SQL statements that parallel query can apply to, see [How Parallel Query Works with SQL Constructs](#aurora-mysql-parallel-query-sql)\. 

 The following example output shows the results of running the preceding query on a db\.r4\.2xlarge instance with a cold buffer pool\. The query runs substantially faster when using parallel query\. 

**Note**  
 Because timings depend on many environmental factors, and this example query ran using an early version of parallel query, your results might be different\. Always conduct your own performance tests to confirm the findings with your own environment, workload, and so on\. 

```
-- Without parallel query
+------------+-------------+-------------+----------------+
| l_orderkey | revenue     | o_orderdate | o_shippriority |
+------------+-------------+-------------+----------------+
|   92511430 | 514726.4896 | 1995-03-06  |              0 |
.
.
|   28840519 | 454748.2485 | 1995-03-08  |              0 |
+------------+-------------+-------------+----------------+
10 rows in set (24 min 49.99 sec)
```

```
-- With parallel query
+------------+-------------+-------------+----------------+
| l_orderkey | revenue     | o_orderdate | o_shippriority |
+------------+-------------+-------------+----------------+
|   92511430 | 514726.4896 | 1995-03-06  |              0 |
.
.
|   28840519 | 454748.2485 | 1995-03-08  |              0 |
+------------+-------------+-------------+----------------+
10 rows in set (1 min 49.91 sec)
```

 Many of the sample queries throughout this section use the tables from this TPC\-H dataset, particularly the `PART` table, which has 20 million rows and the following definition\. 

```
+---------------+---------------+------+-----+---------+-------+
| Field         | Type          | Null | Key | Default | Extra |
+---------------+---------------+------+-----+---------+-------+
| p_partkey     | int(11)       | NO   | PRI | NULL    |       |
| p_name        | varchar(55)   | NO   |     | NULL    |       |
| p_mfgr        | char(25)      | NO   |     | NULL    |       |
| p_brand       | char(10)      | NO   |     | NULL    |       |
| p_type        | varchar(25)   | NO   |     | NULL    |       |
| p_size        | int(11)       | NO   |     | NULL    |       |
| p_container   | char(10)      | NO   |     | NULL    |       |
| p_retailprice | decimal(15,2) | NO   |     | NULL    |       |
| p_comment     | varchar(23)   | NO   |     | NULL    |       |
+---------------+---------------+------+-----+---------+-------+
```

 Experiment with your workload to get a sense of whether individual SQL statements can take advantage of parallel query\. Then, use the following monitoring techniques to help verify how often parallel query is used in real workloads over time\. For real workloads, extra factors such as concurrency limits apply\. 

## Monitoring Parallel Query<a name="aurora-mysql-parallel-query-monitoring"></a>

 In addition to the Amazon CloudWatch metrics described in [Monitoring Amazon Aurora DB Cluster Metrics](Aurora.Monitoring.md), Aurora provides other global status variables\. You can use these global status variables to help monitor parallel query execution and give you insights into why the optimizer might use or not use parallel query in a given situation\. To access these variables, you can use the `[SHOW GLOBAL STATUS](https://dev.mysql.com/doc/refman/5.6/en/server-status-variables.html)` command\. You can also find these variables listed following\. 

 A parallel query session isn't necessarily a one\-to\-one mapping with an executed query\. For example, suppose your execution plan has two steps that use parallel query\. In that case, the query involves two parallel sessions and the counters for requests attempted and requests successful are incremented by two\. 

 When you experiment with parallel query by issuing `EXPLAIN` statements, expect to see increases in the counters designated as "not chosen" even though the queries aren't actually running\. When you work with parallel query in production, you can check if the "not chosen" counters are increasing faster than you expect\. You can then adjust your cluster settings, query mix, DB instances where parallel query is enabled, and so on, so that parallel query runs for the queries that you expect\. 

 These counters are tracked at the DB instance level\. When you connect to a different endpoint, you might see different metrics because each DB instance runs its own set of parallel queries\. You might also see different metrics when the reader endpoint connects to a different DB instance for each session\. 


|  |  | 
| --- |--- |
|   **Name**   |   **Description**   | 
|   `Aurora_pq_request_attempted`   |   The number of parallel query sessions requested\. This value might represent more than one session per query, depending on SQL constructs such as subqueries and joins\.   | 
|   `Aurora_pq_request_executed`   |   The number of parallel query sessions run successfully\.   | 
|   `Aurora_pq_request_failed`   |   The number of parallel query sessions that returned an error to the client\. In some cases, a request for a parallel query might fail, for example due to a problem in the storage layer\. In these cases, the query part that failed is retried using the nonparallel query mechanism\. If the retried query also fails, an error is returned to the client and this counter is incremented\.   | 
|   `Aurora_pq_pages_pushed_down`   |   The number of data pages \(each with a fixed size of 16 KiB\) where parallel query avoided a network transmission to the head node\.   | 
|   `Aurora_pq_bytes_returned`   |   The number of bytes for the tuple data structures transmitted to the head node during parallel queries\. Divide by 16,384 to compare against `Aurora_pq_pages_pushed_down`\.   | 
|   `Aurora_pq_request_not_chosen`   |   The number of times parallel query wasn't chosen to satisfy a query\. This value is the sum of several other more granular counters\. This counter can be incremented by an `EXPLAIN` statement even though the query isn't actually performed\.   | 
|   `Aurora_pq_request_not_chosen_below_min_rows`   |   The number of times parallel query wasn't chosen due to the number of rows in the table\. This counter can be incremented by an `EXPLAIN` statement even though the query isn't actually performed\.   | 
|   `Aurora_pq_request_not_chosen_small_table`   |   The number of times parallel query wasn't chosen due to the overall size of the table, as determined by number of rows and average row length\. This counter can be incremented by an `EXPLAIN` statement even though the query isn't actually performed\.   | 
|   `Aurora_pq_request_not_chosen_high_buffer_pool_pct`   |   The number of times parallel query wasn't chosen because a high percentage of the table data \(currently, greater than 95 percent\) was already in the buffer pool\. In these cases, the optimizer determines that reading the data from the buffer pool is more efficient\. This counter can be incremented by an `EXPLAIN` statement even though the query isn't actually performed\.   | 
|   `Aurora_pq_request_not_chosen_few_pages_outside_buffer_pool`   |   The number of times parallel query wasn't chosen, even though less than 95 percent of the table data was in the buffer pool, because there wasn't enough unbuffered table data to make parallel query worthwhile\. This counter can be incremented by an `EXPLAIN` statement even though the query isn't actually performed\.   | 
|   `Aurora_pq_max_concurrent_requests`   |   The maximum number of parallel query sessions that can run concurrently on this Aurora DB instance\. This is a fixed number that depends on the AWS instance class\.   | 
|   `Aurora_pq_request_in_progress`   |   The number of parallel query sessions currently in progress\. This number applies to the particular Aurora DB instance you are connected to, not the entire Aurora DB cluster\. To see if a DB instance is close to its concurrency limit, compare this value to `Aurora_pq_max_concurrent_requests`\.   | 
|   `Aurora_pq_request_throttled`   |   The number of times parallel query wasn't chosen due to the maximum number of concurrent parallel queries already running on a particular Aurora DB instance\.   | 
|   `Aurora_pq_request_not_chosen_long_trx`   |   The number of parallel query requests that used the nonparallel query execution path, due to the query being started inside a long\-running transaction\. This counter can be incremented by an `EXPLAIN` statement even though the query isn't actually performed\.   | 
|   `Aurora_pq_request_not_chosen_unsupported_access`   |   The number of parallel query requests that use the nonparallel query execution path because the `WHERE` clause doesn't meet the criteria for parallel query\. This result can occur if the query doesn't require a data\-intensive scan, or if the query is a `DELETE` or `UPDATE` statement\.   | 

## How Parallel Query Works with SQL Constructs<a name="aurora-mysql-parallel-query-sql"></a>

 In the following section, you can find more detail about why particular SQL statements use or don't use parallel query and how Aurora MySQL features interact with parallel query\. These details can help you diagnose performance issues for a cluster that uses parallel query or understand how parallel query applies for your particular workload\. 

 The decision to use parallel query relies on many factors that occur at the moment that the statement runs\. Thus, parallel query might be used for certain queries always, never, or only under certain conditions\. 

**Topics**
+ [EXPLAIN statement](#aurora-mysql-parallel-query-sql-explain)
+ [WHERE Clause](#aurora-mysql-parallel-query-sql-where)
+ [Function Calls in WHERE Clause](#aurora-mysql-parallel-query-sql-functions)
+ [Aggregate Functions, GROUP BY Clauses, and HAVING Clauses](#aurora-mysql-parallel-query-sql-aggregation)
+ [Comparison Operators](#aurora-mysql-parallel-query-sql-comparisons)
+ [Joins](#aurora-mysql-parallel-query-sql-joins)
+ [Subqueries](#aurora-mysql-parallel-query-sql-subqueries)
+ [UNION](#aurora-mysql-parallel-query-sql-union)
+ [Views](#aurora-mysql-parallel-query-sql-views)
+ [Data Manipulation Language \(DML\) Statements](#aurora-mysql-parallel-query-sql-dml)
+ [Transactions and Locking](#aurora-mysql-parallel-query-sql-transactions)
+ [Indexes](#aurora-mysql-parallel-query-sql-indexes)
+ [Built\-In Caching Mechanisms](#aurora-mysql-parallel-query-sql-caching)
+ [MyISAM Temporary Tables](#aurora-mysql-parallel-query-sql-myisam)

### EXPLAIN statement<a name="aurora-mysql-parallel-query-sql-explain"></a>

 As shown in examples throughout this section, the `EXPLAIN` statement indicates whether each stage of a query is currently eligible for parallel query\. It also indicates which aspects of a query can be pushed down to the storage layer\. The most important items in the execution plan are the following: 
+  A value other than `NULL` for the `key` column suggests that the query can be performed efficiently using index lookups, and parallel query is unlikely\. 
+  A small value for the `rows` column \(that is, a value not in the millions\) suggests that the query isn't accessing enough data to make parallel query worthwhile, and parallel query is unlikely\. 
+  The `Extra` column shows you if parallel query is expected to be used\. This output looks like the following example\. 

  ```
  Using parallel query (A columns, B filters, C exprs; D extra)
  ```

   The `columns` number represents how many columns are referred to in the query block\. 

   The `filters` number represents the number of `WHERE` predicates representing a simple comparison of a column value to a constant\. The comparison can be for equality, inequality, or a range\. Aurora can parallelize these kinds of predicates most effectively\. 

   The `exprs` number represents the number of expressions such as function calls, operators, or other expressions that can also be parallelized, though not as effectively as a filter condition\. 

   The `extra` number represents how many expressions can't be pushed down and are performed by the head node\. 

 For example, consider the following `EXPLAIN` output\. 

```
mysql> explain select p_name, p_mfgr from part
    -> where p_brand is not null
    -> and upper(p_type) is not null
    -> and round(p_retailprice) is not null;
+----+-------------+-------+...+----------+----------------------------------------------------------------------------+
| id | select_type | table |...| rows     | Extra                                                                      |
+----+-------------+-------+...+----------+----------------------------------------------------------------------------+
|  1 | SIMPLE      | part  |...| 20427936 | Using where; Using parallel query (5 columns, 1 filters, 2 exprs; 0 extra) |
+----+-------------+-------+...+----------+----------------------------------------------------------------------------+
```

 The information from the `Extra` column shows that five columns are extracted from each row to evaluate the query conditions and construct the result set\. One `WHERE` predicate involves a filter, that is, a column that is directly tested in the `WHERE` clause\. Two `WHERE` clauses require evaluating more complicated expressions, in this case involving function calls\. The `0 extra` field confirms that all the operations in the `WHERE` clause are pushed down to the storage layer as part of parallel query processing\. 

 In cases where parallel query isn't chosen, you can typically deduce the reason from the other columns of the `EXPLAIN` output\. For example, the `rows` value might be too small, or the `possible_keys` column might indicate that the query can use an index lookup instead of a data\-intensive scan\. The following example shows a query where the optimizer can estimate, based on the characteristics of the primary key, that the query will scan only a small number of rows\. In this case, parallel query isn't required\. 

```
mysql> explain select count(*) from part where p_partkey between 1 and 100;
+----+-------------+-------+-------+---------------+---------+---------+------+------+--------------------------+
| id | select_type | table | type  | possible_keys | key     | key_len | ref  | rows | Extra                    |
+----+-------------+-------+-------+---------------+---------+---------+------+------+--------------------------+
|  1 | SIMPLE      | part  | range | PRIMARY       | PRIMARY | 4       | NULL |   99 | Using where; Using index |
+----+-------------+-------+-------+---------------+---------+---------+------+------+--------------------------+
```

 The output showing whether parallel query will be used takes into account all available factors at the moment that the `EXPLAIN` statement is run\. The optimizer might make a different choice when the query is actually run, if the situation changed in the meantime\. For example, `EXPLAIN` might report that a statement will use parallel query\. But when the query is actually run later, it might not use parallel query based on the conditions then\. Such conditions can include several other parallel queries running concurrently, rows being deleted from the table, a new index being created, too much time passing within an open transaction, and so on\. 

### WHERE Clause<a name="aurora-mysql-parallel-query-sql-where"></a>

 For a query to use the parallel query optimization, it *must* include a `WHERE` clause\. 

 The parallel query optimization speeds up many kinds of expressions used in the `WHERE` clause: 
+  Simple comparisons of a column value to a constant, known as *filters*\. These comparisons benefit the most from being pushed down to the storage layer\. The number of filter expressions in a query is reported in the `EXPLAIN` output\. 
+  Other kinds of expressions in the `WHERE` clause are also pushed down to the storage layer where possible\. The number of such expressions in a query is reported in the `EXPLAIN` output\. These expressions can be function calls, `LIKE` operators, `CASE` expressions, and so on\. 
+  Certain functions and operators aren't currently pushed down by parallel query\. The number of such expressions in a query is reported as the `extra` counter in the `EXPLAIN` output\. The rest of the query can still use parallel query\. 
+  While expressions in the select list aren't pushed down, queries containing such functions can still benefit from reduced network traffic for the intermediate results of parallel queries\. For example, queries that call aggregation functions in the select list can benefit from parallel query, even though the aggregation functions aren't pushed down\. 

 For example, the following query does a full\-table scan and processes all the values for the `P_BRAND` column\. However, it doesn't use parallel query because the query doesn't include any `WHERE` clause\. 

```
mysql> explain select count(*), p_brand from part group by p_brand;
+----+-------------+-------+------+---------------+------+---------+------+----------+---------------------------------+
| id | select_type | table | type | possible_keys | key  | key_len | ref  | rows     | Extra                           |
+----+-------------+-------+------+---------------+------+---------+------+----------+---------------------------------+
|  1 | SIMPLE      | part  | ALL  | NULL          | NULL | NULL    | NULL | 20427936 | Using temporary; Using filesort |
+----+-------------+-------+------+---------------+------+---------+------+----------+---------------------------------+
```

 In contrast, the following query includes `WHERE` predicates that filter the results, so parallel query can be applied: 

```
mysql> explain select count(*), p_brand from part where p_name is not null
    ->   and p_mfgr in ('Manufacturer#1', 'Manufacturer#3') and p_retailprice > 1000
    -> group by p_brand;
+----+...+----------+-------------------------------------------------------------------------------------------------------------+
| id |...| rows     | Extra                                                                                                       |
+----+...+----------+-------------------------------------------------------------------------------------------------------------+
|  1 |...| 20427936 | Using where; Using temporary; Using filesort; Using parallel query (5 columns, 1 filters, 2 exprs; 0 extra) |
+----+...+----------+-------------------------------------------------------------------------------------------------------------+
```

 If the optimizer estimates that the number of returned rows for a query block is small, parallel query isn't used for that query block\. The following example shows a case where a greater\-than operator on the primary key column applies to millions of rows, which causes parallel query to be used\. The converse less\-than test is estimated to apply to only a few rows and doesn't use parallel query\. 

```
mysql> explain select count(*) from part where p_partkey > 10;
+----+...+----------+----------------------------------------------------------------------------+
| id |...| rows     | Extra                                                                      |
+----+...+----------+----------------------------------------------------------------------------+
|  1 |...| 20427936 | Using where; Using parallel query (1 columns, 1 filters, 0 exprs; 0 extra) |
+----+...+----------+----------------------------------------------------------------------------+

mysql> explain select count(*) from part where p_partkey < 10;
+----+...+------+--------------------------+
| id |...| rows | Extra                    |
+----+...+------+--------------------------+
|  1 |...|    9 | Using where; Using index |
+----+...+------+--------------------------+
```

### Function Calls in WHERE Clause<a name="aurora-mysql-parallel-query-sql-functions"></a>

 Aurora can apply the parallel query optimization to calls to most built\-in functions in the `WHERE` clause\. Parallelizing these function calls offloads some CPU work from the head node\. Evaluating the predicate functions in parallel during the earliest query stage helps Aurora minimize the amount of data transmitted and processed during later stages\. 

 Currently, the parallelization doesn't apply to function calls in the select list\. Those functions are evaluated by the head node, even if identical function calls appear in the `WHERE` clause\. The original values from relevant columns are included in the tuples transmitted from the storage nodes back to the head node\. The head node performs any transformations such as `UPPER()`, `CONCATENATE()`, and so on to produce the final values for the result set\. 

 In the following example, parallel query parallelizes the call to `LOWER()` because it appears in the `WHERE` clause\. Parallel query doesn't affect the calls to `SUBSTR()` and `UPPER()` because they appear in the select list\. 

```
mysql> explain select sql_no_cache distinct substr(upper(p_name),1,5) from part
    -> where lower(p_name) like '%cornflower%' or lower(p_name) like '%goldenrod%';
+----+...+---------------------------------------------------------------------------------------------+
| id |...| Extra                                                                                       |
+----+...+---------------------------------------------------------------------------------------------+
|  1 |...| Using where; Using temporary; Using parallel query (2 columns, 0 filters, 1 exprs; 0 extra) |
+----+...+---------------------------------------------------------------------------------------------+
```

 The same considerations apply to other expressions, such as `CASE` expressions or `LIKE` operators\. For example, the following example shows that parallel query evaluates the `CASE` expression and `LIKE` operators in the `WHERE` clause\. 

```
mysql> explain select p_mfgr, p_retailprice from part
    -> where p_retailprice > case p_mfgr
    ->   when 'Manufacturer#1' then 1000
    ->   when 'Manufacturer#2' then 1200
    ->   else 950
    -> end
    -> and p_name like '%vanilla%'
    -> group by p_retailprice;
+----+...+-------------------------------------------------------------------------------------------------------------+
| id |...| Extra                                                                                                       |
+----+...+-------------------------------------------------------------------------------------------------------------+
|  1 |...| Using where; Using temporary; Using filesort; Using parallel query (4 columns, 0 filters, 2 exprs; 0 extra) |
+----+...+-------------------------------------------------------------------------------------------------------------+
```

### Aggregate Functions, GROUP BY Clauses, and HAVING Clauses<a name="aurora-mysql-parallel-query-sql-aggregation"></a>

 Queries involving aggregate functions are often good candidates for parallel query, because they involve scanning large numbers of rows within large tables\. Aggregate function calls in the select list or the `HAVING` clause aren't pushed down to the storage layer\. However, parallel query can still improve the performance of such queries with aggregate functions\. It does so by first extracting column values from the raw data pages in parallel at the storage layer\. It then transmits those values back to the head node in a compact tuple format instead of as entire data pages\. As always, the query requires at least one `WHERE` predicate for parallel query to be activated\. 

 The following simple examples illustrate the kinds of aggregate queries that can benefit from parallel query\. They do so by returning intermediate results in compact form to the head node, filtering nonmatching rows from the intermediate results, or both\. 

```
mysql> explain select sql_no_cache count(distinct p_brand) from part where p_mfgr = 'Manufacturer#5';
+----+...+----------------------------------------------------------------------------+
| id |...| Extra                                                                      |
+----+...+----------------------------------------------------------------------------+
|  1 |...| Using where; Using parallel query (2 columns, 1 filters, 0 exprs; 0 extra) |
+----+...+----------------------------------------------------------------------------+

mysql> explain select sql_no_cache p_mfgr from part where p_retailprice > 1000 group by p_mfgr having count(*) > 100;
+----+...+-------------------------------------------------------------------------------------------------------------+
| id |...| Extra                                                                                                       |
+----+...+-------------------------------------------------------------------------------------------------------------+
|  1 |...| Using where; Using temporary; Using filesort; Using parallel query (3 columns, 0 filters, 1 exprs; 0 extra) |
+----+...+-------------------------------------------------------------------------------------------------------------+
```

### Comparison Operators<a name="aurora-mysql-parallel-query-sql-comparisons"></a>

 The optimizer estimates how many rows to scan to evaluate comparison operators, and determines whether to use parallel query based on that estimate\. 

 The first example following shows that an equality comparison against the primary key column can be performed efficiently without parallel query\. The second example following shows that a similar comparison against an unindexed column requires scanning millions of rows and therefore can benefit from parallel query\. 

```
mysql> explain select * from part where p_partkey = 10;
+----+...+------+-------+
| id |...| rows | Extra |
+----+...+------+-------+
|  1 |...|    1 | NULL  |
+----+...+------+-------+

mysql> explain select * from part where p_type = 'LARGE BRUSHED BRASS';
+----+...+----------+----------------------------------------------------------------------------+
| id |...| rows     | Extra                                                                      |
+----+...+----------+----------------------------------------------------------------------------+
|  1 |...| 20427936 | Using where; Using parallel query (9 columns, 1 filters, 0 exprs; 0 extra) |
+----+...+----------+----------------------------------------------------------------------------+
```

 The same considerations apply for not\-equals tests and for range comparisons such as less than, greater than or equal to, or `BETWEEN`\. The optimizer estimates the number of rows to scan, and determines whether parallel query is worthwhile based on the overall volume of I/O\. 

### Joins<a name="aurora-mysql-parallel-query-sql-joins"></a>

 Join queries with large tables typically involve data\-intensive operations that benefit from the parallel query optimization\. The comparisons of column values between multiple tables \(that is, the join predicates themselves\) currently aren't parallelized\. However, parallel query can push down some of the internal processing for other join phases, such as constructing the Bloom filter during a hash join\. Parallel query can apply to join queries even without a `WHERE` clause\. Therefore, a join query is an exception to the rule that a `WHERE` clause is required to use parallel query\. 

 Each phase of join processing is evaluated to check if it is eligible for parallel query\. If more than one phase can use parallel query, these phases are executed in sequence\. Thus, each join query counts as a single parallel query session in terms of concurrency limits\. 

 For example, when a join query includes `WHERE` predicates to filter the rows from one of the joined tables, that filtering option can use parallel query\. As another example, suppose that a join query uses the hash join mechanism, for example to join a big table with a small table\. In this case, the table scan to produce the Bloom filter data structure might be able to use parallel query\. 

**Note**  
 Although currently the hash join feature requires enabling lab mode, hash joins are always available in parallel query\-enabled clusters\. 

```
mysql> explain select count(*) from orders join customer where o_custkey = c_custkey;
+----+...+----------+-------+---------------+-------------+...+-----------+-----------------------------------------------------------------------------------------------------------------+
| id |...| table    | type  | possible_keys | key         |...| rows      | Extra                                                                                                           |
+----+...+----------+-------+---------------+-------------+...+-----------+-----------------------------------------------------------------------------------------------------------------+
|  1 |...| customer | index | PRIMARY       | c_nationkey |...|  15051972 | Using index                                                                                                     |
|  1 |...| orders   | ALL   | o_custkey     | NULL        |...| 154545408 | Using join buffer (Hash Join Outer table orders); Using parallel query (1 columns, 0 filters, 1 exprs; 0 extra) |
+----+...+----------+-------+---------------+-------------+...+-----------+-----------------------------------------------------------------------------------------------------------------+
```

 For a join query that uses the nested loop mechanism, the outermost nested loop block might use parallel query\. The use of parallel query depends on the same factors as usual, such as the presence of additional filter conditions in the `WHERE` clause\. 

```
mysql> -- Nested loop join with extra filter conditions can use parallel query.
mysql> explain select count(*) from part, partsupp where p_partkey != ps_partkey and p_name is not null and ps_availqty > 0;
+----+-------------+----------+...+----------+----------------------------------------------------------------------------+
| id | select_type | table    |...| rows     | Extra                                                                      |
+----+-------------+----------+...+----------+----------------------------------------------------------------------------+
|  1 | SIMPLE      | part     |...| 20427936 | Using where; Using parallel query (2 columns, 1 filters, 0 exprs; 0 extra) |
|  1 | SIMPLE      | partsupp |...| 78164450 | Using where; Using join buffer (Block Nested Loop)                         |
+----+-------------+----------+...+----------+----------------------------------------------------------------------------+
```

### Subqueries<a name="aurora-mysql-parallel-query-sql-subqueries"></a>

 The outer query block and inner subquery block might each use parallel query, or not, based on the usual characteristics of the table, `WHERE` clause, and so on, for each block\. For example, the following query uses parallel query for the subquery block but not the outer block\. 

```
mysql> explain select count(*) from part where
   --> p_partkey < (select max(p_partkey) from part where p_name like '%vanilla%');
+----+-------------+...+----------+----------------------------------------------------------------------------+
| id | select_type |...| rows     | Extra                                                                      |
+----+-------------+...+----------+----------------------------------------------------------------------------+
|  1 | PRIMARY     |...|     NULL | Impossible WHERE noticed after reading const tables                        |
|  2 | SUBQUERY    |...| 20427936 | Using where; Using parallel query (2 columns, 0 filters, 1 exprs; 0 extra) |
+----+-------------+...+----------+----------------------------------------------------------------------------+
```

### UNION<a name="aurora-mysql-parallel-query-sql-union"></a>

 Each query block in a `UNION` query can use parallel query, or not, based on the usual characteristics of the table, `WHERE` clause, and so on, for each part of the `UNION`\. 

```
mysql> explain select p_partkey from part where p_name like '%choco_ate%'
    -> union select p_partkey from part where p_name like '%vanil_a%';
+----+----------------+...+----------+----------------------------------------------------------------------------+
| id | select_type    |...| rows     | Extra                                                                      |
+----+----------------+...+----------+----------------------------------------------------------------------------+
|  1 | PRIMARY        |...| 20427936 | Using where; Using parallel query (2 columns, 0 filters, 1 exprs; 0 extra) |
|  2 | UNION          |...| 20427936 | Using where; Using parallel query (2 columns, 0 filters, 1 exprs; 0 extra) |
| NULL | UNION RESULT | <union1,2> |...|     NULL | Using temporary                                           |
+----+--------------+...+----------+----------------------------------------------------------------------------+
```

**Note**  
 Each `UNION` clause within the query is run sequentially\. Even if the query includes multiple stages that all use parallel query, it only runs a single parallel query at any one time\. Therefore, even a complex multistage query only counts as 1 toward the limit of concurrent parallel queries\. 

### Views<a name="aurora-mysql-parallel-query-sql-views"></a>

 The optimizer rewrites any query using a view as a longer query using the underlying tables\. Thus, parallel query works the same whether table references are views or real tables\. All the same considerations about whether to use parallel query for a query, and which parts are pushed down, apply to the final rewritten query\. 

 For example, the following execution plan shows a view definition that usually doesn't use parallel query\. When the view is queried with additional `WHERE` clauses, Aurora MySQL uses parallel query\. 

```
mysql> create view part_view as select * from part;

mysql> explain select count(*) from part_view where p_partkey is not null;
+----+...+----------+----------------------------------------------------------------------------+
| id |...| rows     | Extra                                                                      |
+----+...+----------+----------------------------------------------------------------------------+
|  1 |...| 20427936 | Using where; Using parallel query (1 columns, 0 filters, 0 exprs; 1 extra) |
+----+...+----------+----------------------------------------------------------------------------+
```

### Data Manipulation Language \(DML\) Statements<a name="aurora-mysql-parallel-query-sql-dml"></a>

 The `INSERT` statement can use parallel query for the `SELECT` phase of processing, if the `SELECT` part meets the other conditions for parallel query\. 

```
mysql> explain insert into part_subset select * from part where p_mfgr = 'Manufacturer#1';
+----+...+----------+----------------------------------------------------------------------------+
| id |...| rows     | Extra                                                                      |
+----+...+----------+----------------------------------------------------------------------------+
|  1 |...| 20427936 | Using where; Using parallel query (9 columns, 1 filters, 0 exprs; 0 extra) |
+----+...+----------+----------------------------------------------------------------------------+
```

**Note**  
 Typically, after an `INSERT` statement, the data for the newly inserted rows is in the buffer pool\. Therefore, a table might not be eligible for parallel query immediately after inserting a large number of rows\. Later, after the data is evicted from the buffer pool during normal operation, queries against the table might begin using parallel query again\. 

 The `CREATE TABLE AS SELECT` statement doesn't use parallel query, even if the `SELECT` portion of the statement would otherwise be eligible for parallel query\. The DDL aspect of this statement makes it incompatible with parallel query processing\. In contrast, in the `INSERT ... SELECT` statement, the `SELECT` portion can use parallel query\. 

 Parallel query is never used for `DELETE` or `UPDATE` statements, regardless of the size of the table and predicates in the `WHERE` clause\. 

```
mysql> explain delete from part where p_name is not null;
+----+-------------+...+----------+-------------+
| id | select_type |...| rows     | Extra       |
+----+-------------+...+----------+-------------+
|  1 | SIMPLE      |...| 20427936 | Using where |
+----+-------------+...+----------+-------------+
```

### Transactions and Locking<a name="aurora-mysql-parallel-query-sql-transactions"></a>

 Parallel query only applies to statements executed under the `REPEATABLE READ` isolation level\. This isolation level is the only one that you can set on Aurora DB instances that are Read Replicas\. You can use all the isolation levels on the Aurora primary instance\. 

 After a big transaction is finished, the table statistics might be stale\. Such stale statistics might require an `ANALYZE TABLE` statement before Aurora can accurately estimate the number of rows\. A large\-scale DML statement might also bring a substantial portion of the table data into the buffer pool\. Having this data in the buffer pool can lead to parallel query being chosen less frequently for that table until the data is evicted from the pool\. 

 When your session is inside a long\-running transaction \(by default, 10 minutes\), further queries inside that session don't use parallel query\. A timeout can also occur during a single long\-running query\. This type of timeout might happen if the query runs for longer than the maximum interval \(currently 10 minutes\) before the parallel query processing starts\. 

 You can reduce the chance of starting long\-running transactions accidentally by setting `autocommit=1` in `mysql` sessions where you perform ad hoc \(one\-time\) queries\. Even a `SELECT` statement against a table begins a transaction by creating a read view\. A *read view *is a consistent set of data for subsequent queries that remains until the transaction is committed\. Be aware of this restriction also when using JDBC or ODBC applications with Aurora, because such applications might run with the `autocommit` setting turned off\. 

 The following example shows how, with the `autocommit` setting turned off, running a query against a table creates a read view that implicitly begins a transaction\. Queries that are run shortly afterward can still use parallel query\. However, after a pause of several minutes, queries are no longer eligible for parallel query\. Ending the transaction with `COMMIT` or `ROLLBACK` restores parallel query eligibility\. 

```
mysql> set autocommit=0;

mysql> explain select sql_no_cache count(*) from part_txn where p_retailprice > 10.0;
+----+...+---------+----------------------------------------------------------------------------+
| id |...| rows    | Extra                                                                      |
+----+...+---------+----------------------------------------------------------------------------+
|  1 |...| 2976129 | Using where; Using parallel query (1 columns, 1 filters, 0 exprs; 0 extra) |
+----+...+---------+----------------------------------------------------------------------------+

mysql> select sleep(720); explain select sql_no_cache count(*) from part_txn where p_retailprice > 10.0;
+------------+
| sleep(720) |
+------------+
|          0 |
+------------+
1 row in set (12 min 0.00 sec)

+----+...+---------+-------------+
| id |...| rows    | Extra       |
+----+...+---------+-------------+
|  1 |...| 2976129 | Using where |
+----+...+---------+-------------+

mysql> commit;

mysql> explain select sql_no_cache count(*) from part_txn where p_retailprice > 10.0;
+----+...+---------+----------------------------------------------------------------------------+
| id |...| rows    | Extra                                                                      |
+----+...+---------+----------------------------------------------------------------------------+
|  1 |...| 2976129 | Using where; Using parallel query (1 columns, 1 filters, 0 exprs; 0 extra) |
+----+...+---------+----------------------------------------------------------------------------+
```

 To see how many times queries weren't eligible for parallel query because they were inside long\-running transactions, check the status variable `Aurora_pq_not_chosen_long_trx`\. 

```
mysql> show global status like '%pq%trx%';
+-------------------------------+-------+
| Variable_name                 | Value |
+-------------------------------+-------+
| Aurora_pq_not_chosen_long_trx | 4     |
+-------------------------------+-------+
```

 Any `SELECT` statement that acquires locks, such as the `SELECT FOR UPDATE` or `SELECT LOCK IN SHARE MODE` syntax, can't use parallel query\. 

 Parallel query can work for a table that is locked by a `LOCK TABLES` statement\. 

```
mysql> explain select o_orderpriority, o_shippriority from orders where o_clerk = 'Clerk#000095055';
+----+...+-----------+----------------------------------------------------------------------------+
| id |...| rows      | Extra                                                                      |
+----+...+-----------+----------------------------------------------------------------------------+
|  1 |...| 154545408 | Using where; Using parallel query (3 columns, 1 filters, 0 exprs; 0 extra) |
+----+...+-----------+----------------------------------------------------------------------------+

mysql> explain select o_orderpriority, o_shippriority from orders where o_clerk = 'Clerk#000095055' for update;
+----+...+-----------+-------------+
| id |...| rows      | Extra       |
+----+...+-----------+-------------+
|  1 |...| 154545408 | Using where |
+----+...+-----------+-------------+
```

### Indexes<a name="aurora-mysql-parallel-query-sql-indexes"></a>

 The statistics gathered by the `ANALYZE TABLE` statement help the optimizer to decide when to use parallel query or index lookups, based on the characteristics of the data for each column\. Keep statistics current by running `ANALYZE TABLE` after DML operations that make substantial changes to the data within a table\. 

 If index lookups can perform a query efficiently without a data\-intensive scan, Aurora might use index lookups\. Doing so avoids the overhead of parallel query processing\. There are also concurrency limits on the number of parallel queries that can run simultaneously on any Aurora DB cluster\. Make sure to use best practices for indexing your tables, so that your most frequent and most highly concurrent queries use index lookups\. 

### Built\-In Caching Mechanisms<a name="aurora-mysql-parallel-query-sql-caching"></a>

 Aurora includes built\-in caching mechanisms, namely the buffer pool and the query cache\. The Aurora optimizer chooses between these caching mechanisms and parallel query depending on which one is most effective for a particular query\. 

 When a parallel query filters rows and transforms and extracts column values, data is transmitted back to the head node as tuples rather than as data pages\. Therefore, running a parallel query doesn't add any pages to the buffer pool, or evict pages that are already in the buffer pool\. 

 Aurora checks the number of pages of table data that are present in the buffer pool, and what proportion of the table data that number represents\. Aurora uses that information to determine whether it is more efficient to use parallel query \(and bypass the data in the buffer pool\)\. Alternatively, Aurora might use the nonparallel query execution path, which uses data cached in the buffer pool\. Which pages are cached and how data\-intensive queries affect caching and eviction depends on configuration settings related to the buffer pool\. Therefore, it can be hard to predict whether any particular query uses parallel query, because the choice depends on the ever\-changing data within the buffer pool\. 

 Also, Aurora imposes concurrency limits on parallel queries\. Because not every query uses parallel query, tables that are accessed by multiple queries simultaneously typically have a substantial portion of their data in the buffer pool\. Therefore, Aurora often doesn't choose these tables for parallel queries\. 

 When you run a sequence of nonparallel queries on the same table, the first query might be slow due to the data not being in the buffer pool\. Then the second and subsequent queries are much faster because the buffer pool is now "warmed up"\. Parallel queries typically show consistent performance from the very first query against the table\. When conducting performance tests, benchmark the nonparallel queries with both a cold and a warm buffer pool\. In some cases, the results with a warm buffer pool can compare well to parallel query times\. In these cases, consider factors such as the frequency of queries against that table and whether it is worthwhile to keep the data for that table in the buffer pool\. 

 The query cache avoids re\-executing a query when an identical query is submitted and the underlying table data hasn't changed\. Queries optimized by parallel query feature can go into the query cache, effectively making them instantaneous when run again\. 

**Note**  
 When conducting performance comparisons, the query cache can produce artificially low timing numbers\. Therefore, in benchmark\-like situations, you can use the `sql_no_cache` hint\. This hint prevents the result from being served from the query cache, even if the same query had been run previously\. The hint comes immediately after the `SELECT` statement in a query\. Many parallel query examples in this topic include this hint, to make query times comparable between versions of the query that are enabled with parallel query and not\.   
 Make sure that you remove this hint from your source when you move to production use of parallel query\. 

### MyISAM Temporary Tables<a name="aurora-mysql-parallel-query-sql-myisam"></a>

The parallel query optimization only applies to InnoDB tables\. Because Aurora MySQL uses MyISAM behind the scenes for temporary tables, internal query phases involving temporary tables never use parallel query\. These query phases are indicated by `Using temporary` in the `EXPLAIN` output\. 