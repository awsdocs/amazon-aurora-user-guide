# Working with parallel query for Amazon Aurora MySQL<a name="aurora-mysql-parallel-query"></a><a name="parallel_query"></a><a name="pq"></a>

This topic describes the parallel query performance optimization for Amazon Aurora MySQL\-Compatible Edition\. This feature uses a special processing path for certain data\-intensive queries, taking advantage of the Aurora shared storage architecture\. Parallel query works best with Aurora MySQL DB clusters that have tables with millions of rows and analytic queries that take minutes or hours to complete\.  

**Contents**
+ [Overview of parallel query for Aurora MySQL](#aurora-mysql-parallel-query-overview)
  + [Benefits](#aurora-mysql-parallel-query-benefits)
  + [Architecture](#aurora-mysql-parallel-query-architecture)
  + [Prerequisites](#aurora-mysql-parallel-query-prereqs)
  + [Limitations](#aurora-mysql-parallel-query-limitations)
  + [I/O costs with parallel query](#aurora-mysql-parallel-query-cost)
+ [Planning for a parallel query cluster](#aurora-mysql-parallel-query-planning)
  + [Checking Aurora MySQL version compatibility for parallel query](#aurora-mysql-parallel-query-checking-compatibility)
+ [Creating a DB cluster that works with parallel query](#aurora-mysql-parallel-query-creating-cluster)
  + [Creating a parallel query cluster using the console](#aurora-mysql-parallel-query-creating-cluster-console)
  + [Creating a parallel query cluster using the CLI](#aurora-mysql-parallel-query-creating-cluster-cli)
+ [Turning parallel query on and off](#aurora-mysql-parallel-query-enabling)
  + [Turning on hash join for parallel query clusters](#aurora-mysql-parallel-query-enabling-hash-join)
  + [Turning on and turning off parallel query using the console](#aurora-mysql-parallel-query-enabling-console)
  + [Turning on and turning off parallel query using the CLI](#aurora-mysql-parallel-query-enabling-cli)
  + [Overriding the parallel query optimizer](#aurora-mysql-parallel-query-enabling.aurora_pq_force)
+ [Upgrade considerations for parallel query](#aurora-mysql-parallel-query-upgrade)
  + [Upgrading parallel query clusters to Aurora MySQL version 3](#aurora-mysql-parallel-query-upgrade-pqv2)
  + [Upgrading to Aurora MySQL 2\.09 and higher](#aurora-mysql-parallel-query-upgrade-2.09)
+ [Performance tuning for parallel query](#aurora-mysql-parallel-query-performance)
+ [Creating schema objects to take advantage of parallel query](#aurora-mysql-parallel-query-tables)
+ [Verifying which statements use parallel query](#aurora-mysql-parallel-query-verifying)
+ [Monitoring parallel query](#aurora-mysql-parallel-query-monitoring)
+ [How parallel query works with SQL constructs](#aurora-mysql-parallel-query-sql)
  + [EXPLAIN statement](#aurora-mysql-parallel-query-sql-explain)
  + [WHERE clause](#aurora-mysql-parallel-query-sql-where)
  + [Data definition language \(DDL\)](#aurora-mysql-parallel-query-sql-ddl)
  + [Column data types](#aurora-mysql-parallel-query-sql-datatypes)
  + [Partitioned tables](#aurora-mysql-parallel-query-sql-partitioning)
  + [Aggregate functions, GROUP BY clauses, and HAVING clauses](#aurora-mysql-parallel-query-sql-aggregation)
  + [Function calls in WHERE clause](#aurora-mysql-parallel-query-sql-functions)
  + [LIMIT clause](#aurora-mysql-parallel-query-sql-limit)
  + [Comparison operators](#aurora-mysql-parallel-query-sql-comparisons)
  + [Joins](#aurora-mysql-parallel-query-sql-joins)
  + [Subqueries](#aurora-mysql-parallel-query-sql-subqueries)
  + [UNION](#aurora-mysql-parallel-query-sql-union)
  + [Views](#aurora-mysql-parallel-query-sql-views)
  + [Data manipulation language \(DML\) statements](#aurora-mysql-parallel-query-sql-dml)
  + [Transactions and locking](#aurora-mysql-parallel-query-sql-transactions)
  + [B\-tree indexes](#aurora-mysql-parallel-query-sql-indexes)
  + [Full\-text search \(FTS\) indexes](#aurora-mysql-parallel-query-sql-fts)
  + [Virtual columns](#aurora-mysql-parallel-query-sql-virtual-column)
  + [Built\-in caching mechanisms](#aurora-mysql-parallel-query-sql-caching)
  + [Optimizer hints](#aurora-mysql-parallel-query-hints)
  + [MyISAM temporary tables](#aurora-mysql-parallel-query-sql-myisam)

## Overview of parallel query for Aurora MySQL<a name="aurora-mysql-parallel-query-overview"></a>

 Aurora MySQL parallel query is an optimization that parallelizes some of the I/O and computation involved in processing data\-intensive queries\. The work that is parallelized includes retrieving rows from storage, extracting column values, and determining which rows match the conditions in the `WHERE` clause and join clauses\. This data\-intensive work is delegated \(in database optimization terms, *pushed down*\) to multiple nodes in the Aurora distributed storage layer\. Without parallel query, each query brings all the scanned data to a single node within the Aurora MySQL cluster \(the head node\) and performs all the query processing there\. 

**Tip**  
The PostgreSQL database engine also has a feature called "parallel query\." That feature is unrelated to Aurora parallel query\.

 When the parallel query feature is turned on, the Aurora MySQL engine automatically determines when queries can benefit, without requiring SQL changes such as hints or table attributes\. In the following sections, you can find an explanation of when parallel query is applied to a query\. You can also find how to make sure that parallel query is applied where it provides the most benefit\. 

**Note**  
 The parallel query optimization provides the most benefit for long\-running queries that take minutes or hours to complete\. Aurora MySQL generally doesn't perform parallel query optimization for inexpensive queries\. It also generally doesn't perform parallel query optimization if another optimization technique makes more sense, such as query caching, buffer pool caching, or index lookups\. If you find that parallel query isn't being used when you expect it, see [Verifying which statements use parallel query](#aurora-mysql-parallel-query-verifying)\. 

**Topics**
+ [Benefits](#aurora-mysql-parallel-query-benefits)
+ [Architecture](#aurora-mysql-parallel-query-architecture)
+ [Prerequisites](#aurora-mysql-parallel-query-prereqs)
+ [Limitations](#aurora-mysql-parallel-query-limitations)
+ [I/O costs with parallel query](#aurora-mysql-parallel-query-cost)

### Benefits<a name="aurora-mysql-parallel-query-benefits"></a>

 With parallel query, you can run data\-intensive analytic queries on Aurora MySQL tables\. In many cases, you can get an order\-of\-magnitude performance improvement over the traditional division of labor for query processing\. 

 Benefits of parallel query include the following: 
+  Improved I/O performance, due to parallelizing physical read requests across multiple storage nodes\. 
+  Reduced network traffic\. Aurora doesn't transmit entire data pages from storage nodes to the head node and then filter out unnecessary rows and columns afterward\. Instead, Aurora transmits compact tuples containing only the column values needed for the result set\. 
+  Reduced CPU usage on the head node, due to pushing down function processing, row filtering, and column projection for the `WHERE` clause\. 
+  Reduced memory pressure on the buffer pool\. The pages processed by the parallel query aren't added to the buffer pool\. This approach reduces the chance of a data\-intensive scan evicting frequently used data from the buffer pool\. 
+  Potentially reduced data duplication in your extract, transform, load \(ETL\) pipeline, by making it practical to perform long\-running analytic queries on existing data\. 

### Architecture<a name="aurora-mysql-parallel-query-architecture"></a>

 The parallel query feature uses the major architectural principles of Aurora MySQL: decoupling the database engine from the storage subsystem, and reducing network traffic by streamlining communication protocols\. Aurora MySQL uses these techniques to speed up write\-intensive operations such as redo log processing\. Parallel query applies the same principles to read operations\. 

**Note**  
 The architecture of Aurora MySQL parallel query differs from that of similarly named features in other database systems\. Aurora MySQL parallel query doesn't involve symmetric multiprocessing \(SMP\) and so doesn't depend on the CPU capacity of the database server\. The parallel processing happens in the storage layer, independent of the Aurora MySQL server that serves as the query coordinator\. 

 By default, without parallel query, the processing for an Aurora query involves transmitting raw data to a single node within the Aurora cluster \(the *head node*\)\. Aurora then performs all further processing for that query in a single thread on that single node\. With parallel query, much of this I/O\-intensive and CPU\-intensive work is delegated to nodes in the storage layer\. Only the compact rows of the result set are transmitted back to the head node, with rows already filtered, and column values already extracted and transformed\. The performance benefit comes from the reduction in network traffic, reduction in CPU usage on the head node, and parallelizing the I/O across the storage nodes\. The amount of parallel I/O, filtering, and projection is independent of the number of DB instances in the Aurora cluster that runs the query\. 

### Prerequisites<a name="aurora-mysql-parallel-query-prereqs"></a>

To use all features of parallel query requires an Aurora MySQL DB cluster that's running version 2\.09 or higher\. If you already have a cluster that you want to use with parallel query, you can upgrade it to a compatible version and turn on parallel query afterward\. In that case, make sure to follow the upgrade procedure in [Upgrade considerations for parallel query](#aurora-mysql-parallel-query-upgrade) because the configuration setting names and default values are different in these newer versions\.

The DB instances in your cluster must use the `db.r*` instance classes\.

Make sure that hash join optimization is turned on for your cluster\. To learn how, see [Turning on hash join for parallel query clusters](#aurora-mysql-parallel-query-enabling-hash-join)\.

 To customize parameters such as `aurora_parallel_query` and `aurora_disable_hash_join`, you must have a custom parameter group that you use with your cluster\. You can specify these parameters individually for each DB instance by using a DB parameter group\. However, we recommend that you specify them in a DB cluster parameter group\. That way, all DB instances in your cluster inherit the same settings for these parameters\. 

### Limitations<a name="aurora-mysql-parallel-query-limitations"></a>

The following limitations apply to the parallel query feature:
+ Parallel query isn't supported with the Aurora I/O\-Optimized DB cluster storage configuration\.
+ You can't use parallel query with the db\.t2 or db\.t3 instance classes\. This limitation applies even if you request parallel query using the `aurora_pq_force` session variable\.
+  Parallel query doesn't apply to tables using the `COMPRESSED` or `REDUNDANT` row formats\. Use the `COMPACT` or `DYNAMIC` row formats for tables you plan to use with parallel query\. 
+  Aurora uses a cost\-based algorithm to determine whether to use the parallel query mechanism for each SQL statement\. Using certain SQL constructs in a statement can prevent parallel query or make parallel query unlikely for that statement\. For information about compatibility of SQL constructs with parallel query, see [How parallel query works with SQL constructs](#aurora-mysql-parallel-query-sql)\. 
+  Each Aurora DB instance can run only a certain number of parallel query sessions at one time\. If a query has multiple parts that use parallel query, such as subqueries, joins, or `UNION` operators, those phases run in sequence\. The statement only counts as a single parallel query session at any one time\. You can monitor the number of active sessions using the [parallel query status variables](#aurora-mysql-parallel-query-monitoring)\. You can check the limit on concurrent sessions for a given DB instance by querying the status variable `Aurora_pq_max_concurrent_requests`\. 
+  Parallel query is available in all AWS Regions that Aurora supports\. For most AWS Regions, the minimum required Aurora MySQL version to use parallel query is 2\.09\.
+ Parallel query is designed to improve the performance of data\-intensive queries\. It isn't designed for lightweight queries\.
+ We recommend that you use reader nodes for SELECT statements, especially data\-intensive ones\.

### I/O costs with parallel query<a name="aurora-mysql-parallel-query-cost"></a>

If your Aurora MySQL cluster uses parallel query, you might see an increase in `VolumeReadIOPS` values\. Parallel queries don't use the buffer pool\. Thus, although the queries are fast, this optimized processing can result in an increase in read operations and associated charges\.

Parallel query I/O costs for your query are metered at the storage layer, and will be the same or larger with parallel query turned on\. Your benefit is the improvement in query performance\. There are two reasons for potentially higher I/O costs with parallel query:
+ Even if some of the data in a table is in the buffer pool, parallel query requires all data to be scanned at the storage layer, incurring I/O costs\.
+ Running a parallel query doesn't warm up the buffer pool\. As a result, consecutive runs of the same parallel query incur the full I/O cost\.

## Planning for a parallel query cluster<a name="aurora-mysql-parallel-query-planning"></a>

Planning for a DB cluster that has parallel query turned on requires making some choices\. These include performing setup steps \(either creating or restoring a full Aurora MySQL cluster\) and deciding how broadly to turn on parallel query across your DB cluster\.

Consider the following as part of planning:
+ If you use Aurora MySQL that's compatible with MySQL 5\.7, you must choose Aurora MySQL 2\.09 or higher\. In this case, you always create a provisioned cluster\. Then you turn on parallel query using the `aurora_parallel_query` parameter\.

  If you have an existing Aurora MySQL cluster that's running version 2\.09 or higher, you don't have to create a new cluster to use parallel query\. You can associate your cluster, or specific DB instances in the cluster, with a parameter group that has the `aurora_parallel_query` parameter turned on\. By doing so, you can reduce the time and effort to set up the relevant data to use with parallel query\.
+ If your queries frequently use parallel query, then we recommend that you choose the Aurora I/O\-Optimized storage type\. You pay a fixed price for I/O operations as part of the storage costs\. For more information, see [Storage configurations for Amazon Aurora DB clusters](Aurora.Overview.StorageReliability.md#aurora-storage-type)\.
+ Plan for any large tables that you need to reorganize so that you can use parallel query when accessing them\. You might need to create new versions of some large tables where parallel query is useful\. For example, you might need to remove full\-text search indexes\. For details, see [Creating schema objects to take advantage of parallel query](#aurora-mysql-parallel-query-tables)\.

### Checking Aurora MySQL version compatibility for parallel query<a name="aurora-mysql-parallel-query-checking-compatibility"></a>

To check which Aurora MySQL versions are compatible with parallel query clusters, use the `describe-db-engine-versions` AWS CLI command and check the value of the `SupportsParallelQuery` field\. The following code example shows how to check which combinations are available for parallel query clusters in a specified AWS Region\. Make sure to specify the full `--query` parameter string on a single line\.

```
aws rds describe-db-engine-versions --region us-east-1 --engine aurora-mysql \
--query '*[]|[?SupportsParallelQuery == `true`].[EngineVersion]' --output text
```

The preceding commands produce output similar to the following\. The output might vary depending on which Aurora MySQL versions are available in the specified AWS Region\.

```
5.7.mysql_aurora.2.11.1
8.0.mysql_aurora.3.01.0
8.0.mysql_aurora.3.01.1
8.0.mysql_aurora.3.02.0
8.0.mysql_aurora.3.02.1
8.0.mysql_aurora.3.02.2
8.0.mysql_aurora.3.03.0
```

 After you start using parallel query with a cluster, you can monitor performance and remove obstacles to parallel query usage\. For those instructions, see [Performance tuning for parallel query](#aurora-mysql-parallel-query-performance)\. 

## Creating a DB cluster that works with parallel query<a name="aurora-mysql-parallel-query-creating-cluster"></a>

 To create an Aurora MySQL cluster with parallel query, add new instances to it, or perform other administrative operations, you use the same AWS Management Console and AWS CLI techniques that you do with other Aurora MySQL clusters\. You can create a new cluster to work with parallel query\. You can also create a DB cluster to work with parallel query by restoring from a snapshot of a MySQL\-compatible Aurora DB cluster\. If you aren't familiar with the process for creating a new Aurora MySQL cluster, you can find background information and prerequisites in [Creating an Amazon Aurora DB cluster](Aurora.CreateInstance.md)\. 

When you choose an Aurora MySQL engine version, we recommend that you choose the latest one available\. Currently, Aurora MySQL versions 2\.09 and higher support parallel query\. You have more flexibility to turn parallel query on and off, or use parallel query with existing clusters, if you use Aurora MySQL 2\.09 and higher\.

 Whether you create a new cluster or restore from a snapshot, you use the same techniques to add new DB instances that you do with other Aurora MySQL clusters\. 

### Creating a parallel query cluster using the console<a name="aurora-mysql-parallel-query-creating-cluster-console"></a>

 You can create a new parallel query cluster with the console as described following\. 

**To create a parallel query cluster with the AWS Management Console**

1.  Follow the general AWS Management Console procedure in [Creating an Amazon Aurora DB cluster](Aurora.CreateInstance.md)\. 

1.  On the **Select engine** screen, choose Aurora MySQL\.

   For **Engine version**, choose Aurora MySQL 2\.09 or higher\. With these versions, you have the fewest limitations on parallel query usage\. Those versions also have the most flexibility to turn parallel query on or off at any time\.

    If it isn't practical to use a recent Aurora MySQL version for this cluster, choose **Show versions that support the parallel query feature**\. Doing so filters the **Version** menu to show only the specific Aurora MySQL versions that are compatible with parallel query\. 

1. For **Additional configuration**, choose a parameter group that you created for **DB cluster parameter group**\. Using such a custom parameter group is required for Aurora MySQL 2\.09 and higher\. In your DB cluster parameter group, specify the parameter settings `aurora_parallel_query=ON` and `aurora_disable_hash_join=OFF`\. Doing so turns on parallel query for the cluster, and turns on the hash join optimization that works in combination with parallel query\.

**To verify that a new cluster can use parallel query**

1. Create a cluster using the preceding technique\.

1. \(For Aurora MySQL version 2 or 3\) Check that the `aurora_parallel_query` configuration setting is true\.

   ```
   mysql> select @@aurora_parallel_query;
   +-------------------------+
   | @@aurora_parallel_query |
   +-------------------------+
   |                       1 |
   +-------------------------+
   ```

1. \(For Aurora MySQL version 2\) Check that the `aurora_disable_hash_join` setting is false\.

   ```
   mysql> select @@aurora_disable_hash_join;
   +----------------------------+
   | @@aurora_disable_hash_join |
   +----------------------------+
   |                          0 |
   +----------------------------+
   ```

1.  With some large tables and data\-intensive queries, check the query plans to confirm that some of your queries are using the parallel query optimization\. To do so, follow the procedure in [Verifying which statements use parallel query](#aurora-mysql-parallel-query-verifying)\. 

### Creating a parallel query cluster using the CLI<a name="aurora-mysql-parallel-query-creating-cluster-cli"></a>

 You can create a new parallel query cluster with the CLI as described following\. 

**To create a parallel query cluster with the AWS CLI**

1.  \(Optional\) Check which Aurora MySQL versions are compatible with parallel query clusters\. To do so, use the `describe-db-engine-versions` command and check the value of the `SupportsParallelQuery` field\. For an example, see [Checking Aurora MySQL version compatibility for parallel query](#aurora-mysql-parallel-query-checking-compatibility)\. 

1.  \(Optional\) Create a custom DB cluster parameter group with the settings `aurora_parallel_query=ON` and `aurora_disable_hash_join=OFF`\. Use commands such as the following\. 

   ```
   aws rds create-db-cluster-parameter-group --db-parameter-group-family aurora-mysql5.7 --db-cluster-parameter-group-name pq-enabled-57-compatible
   aws rds modify-db-cluster-parameter-group --db-cluster-parameter-group-name pq-enabled-57-compatible \
     --parameters ParameterName=aurora_parallel_query,ParameterValue=ON,ApplyMethod=pending-reboot
   aws rds modify-db-cluster-parameter-group --db-cluster-parameter-group-name pq-enabled-57-compatible \
     --parameters ParameterName=aurora_disable_hash_join,ParameterValue=OFF,ApplyMethod=pending-reboot
   ```

    If you perform this step, specify the option `--db-cluster-parameter-group-name my_cluster_parameter_group` in the subsequent `create-db-cluster` statement\. Substitute the name of your own parameter group\. If you omit this step, you create the parameter group and associate it with the cluster later, as described in [Turning parallel query on and off](#aurora-mysql-parallel-query-enabling)\. 

1.  Follow the general AWS CLI procedure in [Creating an Amazon Aurora DB cluster](Aurora.CreateInstance.md)\. 

1. Specify the following set of options:
   + For the `--engine` option, use `aurora-mysql`\. These values produce parallel query clusters that are compatible with MySQL 5\.7 or 8\.0\.
   +  For the `--db-cluster-parameter-group-name` option, specify the name of a DB cluster parameter group that you created and specified the parameter value `aurora_parallel_query=ON`\. If you omit this option, you can create the cluster with a default parameter group and later modify it to use such a custom parameter group\. 
   + For the `--engine-version` option, use an Aurora MySQL version that's compatible with parallel query\. Use the procedure from [Planning for a parallel query cluster](#aurora-mysql-parallel-query-planning) to get a list of versions if necessary\. Use at least version 2\.09\.0\. These versions and all higher ones contain substantial enhancements to parallel query\.

      The following code example shows how\. Substitute your own value for each of the environment variables such as *$CLUSTER\_ID*\. 

     ```
     aws rds create-db-cluster --db-cluster-identifier $CLUSTER_ID \
       --engine aurora-mysql --engine-version 5.7.mysql_aurora.2.11.1 \
       --master-username $MASTER_USER_ID --master-user-password $MASTER_USER_PW \
       --db-cluster-parameter-group-name $CUSTOM_CLUSTER_PARAM_GROUP
     
     aws rds create-db-instance --db-instance-identifier ${INSTANCE_ID}-1 \
       --engine same_value_as_in_create_cluster_command \
       --db-cluster-identifier $CLUSTER_ID --db-instance-class $INSTANCE_CLASS
     ```

1. Verify that a cluster you created or restored has the parallel query feature available\.

   Check that the `aurora_parallel_query` configuration setting exists\. If this setting has the value 1, parallel query is ready for you to use\. If this setting has the value 0, set it to 1 before you can use parallel query\. Either way, the cluster is capable of performing parallel queries\.

   ```
   mysql> select @@aurora_parallel_query;
   +------------------------+
   | @@aurora_parallel_query|
   +------------------------+
   |                      1 |
   +------------------------+
   ```

**To restore a snapshot to a parallel query cluster with the AWS CLI**

1.  Check which Aurora MySQL versions are compatible with parallel query clusters\. To do so, use the `describe-db-engine-versions` command and check the value of the `SupportsParallelQuery` field\. For an example, see [Checking Aurora MySQL version compatibility for parallel query](#aurora-mysql-parallel-query-checking-compatibility)\. Decide which version to use for the restored cluster\. Choose Aurora MySQL 2\.09\.0 or higher for a MySQL 5\.7\-compatible cluster\.

1.  Locate an Aurora MySQL\-compatible cluster snapshot\. 

1. Follow the general AWS CLI procedure in [Restoring from a DB cluster snapshot](aurora-restore-snapshot.md)\.

   ```
   aws rds restore-db-cluster-from-snapshot \
     --db-cluster-identifier mynewdbcluster \
     --snapshot-identifier mydbclustersnapshot \
     --engine aurora-mysql
   ```

1.  Verify that a cluster you created or restored has the parallel query feature available\. Use the same verification procedure as in [Creating a parallel query cluster using the CLI](#aurora-mysql-parallel-query-creating-cluster-cli)\. 

## Turning parallel query on and off<a name="aurora-mysql-parallel-query-enabling"></a>

When parallel query is turned on, Aurora MySQL determines whether to use it at runtime for each query\. In the case of joins, unions, subqueries, and so on, Aurora MySQL determines whether to use parallel query at runtime for each query block\. For details, see [Verifying which statements use parallel query](#aurora-mysql-parallel-query-verifying) and [How parallel query works with SQL constructs](#aurora-mysql-parallel-query-sql)\.

You can turn on and turn off parallel query dynamically at both the global and session level for a DB instance by using the **aurora\_parallel\_query** option\. You can change the `aurora_parallel_query` setting in your DB cluster group to turn parallel query on or off by default\.

```
mysql> select @@aurora_parallel_query;
+------------------------+
| @@aurora_parallel_query|
+------------------------+
|                      1 |
+------------------------+
```

 To toggle the `aurora_parallel_query` parameter at the session level, use the standard methods to change a client configuration setting\. For example, you can do so through the `mysql` command line or within a JDBC or ODBC application\. The command on the standard MySQL client is `set session aurora_parallel_query = {'ON'/'OFF'}`\. You can also add the session\-level parameter to the JDBC configuration or within your application code to turn on or turn off parallel query dynamically\. 

 You can permanently change the setting for the `aurora_parallel_query` parameter, either for a specific DB instance or for your whole cluster\. If you specify the parameter value in a DB parameter group, that value only applies to specific DB instance in your cluster\. If you specify the parameter value in a DB cluster parameter group, all DB instances in the cluster inherit the same setting\. To toggle the `aurora_parallel_query` parameter, use the techniques for working with parameter groups, as described in [Working with parameter groups](USER_WorkingWithParamGroups.md)\. Follow these steps: 

1.  Create a custom cluster parameter group \(recommended\) or a custom DB parameter group\. 

1.  In this parameter group, update `parallel_query` to the value that you want\. 

1.  Depending on whether you created a DB cluster parameter group or a DB parameter group, attach the parameter group to your Aurora cluster or to the specific DB instances where you plan to use the parallel query feature\. 
**Tip**  
Because `aurora_parallel_query` is a dynamic parameter, it doesn't require a cluster restart after changing this setting\. However, any connections that were using parallel query before toggling the option will continue to do so until the connection is closed, or the instance is rebooted\.

 You can modify the parallel query parameter by using the [ModifyDBClusterParameterGroup](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBClusterParameterGroup.html) or [ModifyDBParameterGroup](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBParameterGroup.html) API operation or the AWS Management Console\. 

### Turning on hash join for parallel query clusters<a name="aurora-mysql-parallel-query-enabling-hash-join"></a>

Parallel query is typically used for the kinds of resource\-intensive queries that benefit from the hash join optimization\. Thus, it's helpful to make sure that hash joins are turned on for clusters where you plan to use parallel query\. For information about how to use hash joins effectively, see [Optimizing large Aurora MySQL join queries with hash joins](AuroraMySQL.BestPractices.md#Aurora.BestPractices.HashJoin)\.

### Turning on and turning off parallel query using the console<a name="aurora-mysql-parallel-query-enabling-console"></a>

 You can turn on or turn off parallel query at the DB instance level or the DB cluster level by working with parameter groups\. 

**To turn on or turn off parallel query for a DB cluster with the AWS Management Console**

1.  Create a custom parameter group, as described in [Working with parameter groups](USER_WorkingWithParamGroups.md)\. 

1. Update **aurora\_parallel\_query** to **1** \(turned on\) or **0** \(turned off\)\. For clusters where the parallel query feature is available, **aurora\_parallel\_query** is turned off by default\.

1.  If you use a custom cluster parameter group, attach it to the Aurora DB cluster where you plan to use the parallel query feature\. If you use a custom DB parameter group, attach it to one or more DB instances in the cluster\. We recommend using a cluster parameter group\. Doing so makes sure that all DB instances in the cluster have the same settings for parallel query and associated features such as hash join\. 

### Turning on and turning off parallel query using the CLI<a name="aurora-mysql-parallel-query-enabling-cli"></a>

 You can modify the parallel query parameter by using the `modify-db-cluster-parameter-group` or `modify-db-parameter-group` command\. Choose the appropriate command depending on whether you specify the value of `aurora_parallel_query` through a DB cluster parameter group or a DB parameter group\. 

**To turn on or turn off parallel query for a DB cluster with the CLI**
+  Modify the parallel query parameter by using the `modify-db-cluster-parameter-group` command\. Use a command such as the following\. Substitute the appropriate name for your own custom parameter group\. Substitute either `ON` or `OFF` for the `ParameterValue` portion of the `--parameters` option\. 

  ```
  $ aws rds modify-db-cluster-parameter-group --db-cluster-parameter-group-name cluster_param_group_name \
    --parameters ParameterName=aurora_parallel_query,ParameterValue=ON,ApplyMethod=pending-reboot
  {
      "DBClusterParameterGroupName": "cluster_param_group_name"
  }
  
  aws rds modify-db-cluster-parameter-group --db-cluster-parameter-group-name cluster_param_group_name \
    --parameters ParameterName=aurora_pq,ParameterValue=ON,ApplyMethod=pending-reboot
  ```

 You can also turn on or turn off parallel query at the session level, for example through the `mysql` command line or within a JDBC or ODBC application\. To do so, use the standard methods to change a client configuration setting\. For example, the command on the standard MySQL client is `set session aurora_parallel_query = {'ON'/'OFF'}` for Aurora MySQL\.

 You can also add the session\-level parameter to the JDBC configuration or within your application code to turn on or turn off parallel query dynamically\. 

### Overriding the parallel query optimizer<a name="aurora-mysql-parallel-query-enabling.aurora_pq_force"></a>

You can use the `aurora_pq_force` session variable to override the parallel query optimizer and request parallel query for every query\. We recommend that you do this only for testing purposes The following example shows how to use `aurora_pq_force` in a session\.

```
set SESSION aurora_parallel_query = ON;
set SESSION aurora_pq_force = ON;
```

To turn off the override, do the following:

```
set SESSION aurora_pq_force = OFF;
```

## Upgrade considerations for parallel query<a name="aurora-mysql-parallel-query-upgrade"></a>

 Depending on the original and destination versions when you upgrade a parallel query cluster, you might find enhancements in the types of queries that parallel query can optimize\. You might also find that you don't need to specify a special engine mode parameter for parallel query\. The following sections explain the considerations when you upgrade a cluster that has parallel query turned on\. 

### Upgrading parallel query clusters to Aurora MySQL version 3<a name="aurora-mysql-parallel-query-upgrade-pqv2"></a>

 Several SQL statements, clauses, and data types have new or improved parallel query support starting in Aurora MySQL version 3\. When you upgrade from a release that's earlier than version 3, check whether additional queries can benefit from parallel query optimizations\. For information about these parallel query enhancements, see [Column data types](#aurora-mysql-parallel-query-sql-datatypes), [Partitioned tables](#aurora-mysql-parallel-query-sql-partitioning), and [Aggregate functions, GROUP BY clauses, and HAVING clauses](#aurora-mysql-parallel-query-sql-aggregation)\. 

If you're upgrading a parallel query cluster from Aurora MySQL 2\.08 or lower, also learn about changes in how to turn on parallel query\. To do so, read [Upgrading to Aurora MySQL 2\.09 and higher](#aurora-mysql-parallel-query-upgrade-2.09)\.

In Aurora MySQL version 3, hash join optimization is turned on by default\. The `aurora_disable_hash_join` configuration option from earlier versions isn't used\.

### Upgrading to Aurora MySQL 2\.09 and higher<a name="aurora-mysql-parallel-query-upgrade-2.09"></a>

In Aurora MySQL version 2\.09 and higher, parallel query works for provisioned clusters and doesn't require the `parallelquery` engine mode parameter\. Thus, you don't need to create a new cluster or restore from an existing snapshot to use parallel query with these versions\. You can use the upgrade procedures described in [Upgrading the minor version or patch level of an Aurora MySQL DB cluster](AuroraMySQL.Updates.Patching.md) to upgrade your cluster to such a version\. You can upgrade an older cluster regardless of whether it was a parallel query cluster or a provisioned cluster\. To reduce the number of choices in the **Engine version** menu, you can choose **Show versions that support the parallel query feature** to filter the entries in that menu\. Then choose Aurora MySQL 2\.09 or higher\.

After you upgrade an earlier parallel query cluster to Aurora MySQL 2\.09 or higher, you turn on parallel query in the upgraded cluster\. Parallel query is turned off by default in these versions, and the procedure for enabling it is different\. The hash join optimization is also turned off by default and must be turned on separately\. Thus, make sure that you turn on these settings again after the upgrade\. For instructions on doing so, see [Turning parallel query on and off](#aurora-mysql-parallel-query-enabling) and [Turning on hash join for parallel query clusters](#aurora-mysql-parallel-query-enabling-hash-join)\.

In particular, you turn on parallel query by using the configuration parameters `aurora_parallel_query=ON` and `aurora_disable_hash_join=OFF` instead of `aurora_pq_supported` and `aurora_pq`\. The `aurora_pq_supported` and `aurora_pq` parameters are deprecated in the newer Aurora MySQL versions\.

 In the upgraded cluster, the `EngineMode` attribute has the value `provisioned` instead of `parallelquery`\. To check whether parallel query is available for a specified engine version, now you check the value of the `SupportsParallelQuery` field in the output of the `describe-db-engine-versions` AWS CLI command\. In earlier Aurora MySQL versions, you checked for the presence of `parallelquery` in the `SupportedEngineModes` list\. 

After you upgrade to Aurora MySQL version 2\.09 or higher, you can take advantage of the following features\. These features aren't available to parallel query clusters running older Aurora MySQL versions\.
+ Performance Insights\. For more information, see [Monitoring DB load with Performance Insights on Amazon Aurora](USER_PerfInsights.md)\.
+ Backtracking\. For more information, see [Backtracking an Aurora DB cluster](AuroraMySQL.Managing.Backtrack.md)\.
+ Stopping and starting the cluster\. For more information, see [Stopping and starting an Amazon Aurora DB cluster](aurora-cluster-stop-start.md)\.

## Performance tuning for parallel query<a name="aurora-mysql-parallel-query-performance"></a>

 To manage the performance of a workload with parallel query, make sure that parallel query is used for the queries where this optimization helps the most\. 

 To do so, you can do the following: 
+  Make sure that your biggest tables are compatible with parallel query\. You might change table properties or recreate some tables so that queries for those tables can take advantage of the parallel query optimization\. To learn how, see [Creating schema objects to take advantage of parallel query](#aurora-mysql-parallel-query-tables)\. 
+  Monitor which queries use parallel query\. To learn how, see [Monitoring parallel query](#aurora-mysql-parallel-query-monitoring)\. 
+  Verify that parallel query is being used for the most data\-intensive and long\-running queries, and with the right level of concurrency for your workload\. To learn how, see [Verifying which statements use parallel query](#aurora-mysql-parallel-query-verifying)\. 
+  Fine\-tune your SQL code to turn on parallel query to apply to the queries that you expect\. To learn how, see [How parallel query works with SQL constructs](#aurora-mysql-parallel-query-sql)\. 

## Creating schema objects to take advantage of parallel query<a name="aurora-mysql-parallel-query-tables"></a>

 Before you create or modify tables that you plan to use for parallel query, make sure to familiarize yourself with the requirements described in [Prerequisites](#aurora-mysql-parallel-query-prereqs) and [Limitations](#aurora-mysql-parallel-query-limitations)\. 

 Because parallel query requires tables to use the `ROW_FORMAT=Compact` or `ROW_FORMAT=Dynamic` setting, check your Aurora configuration settings for any changes to the `INNODB_FILE_FORMAT` configuration option\. Issue the `SHOW TABLE STATUS` statement to confirm the row format for all the tables in a database\. 

 Before changing your schema to turn on parallel query to work with more tables, make sure to test\. Your tests should confirm if parallel query results in a net increase in performance for those tables\. Also, make sure that the schema requirements for parallel query are otherwise compatible with your goals\. 

 For example, before switching from `ROW_FORMAT=Compressed` to `ROW_FORMAT=Compact` or `ROW_FORMAT=Dynamic`, test the performance of workloads for the original and new tables\. Also, consider other potential effects such as increased data volume\. 

## Verifying which statements use parallel query<a name="aurora-mysql-parallel-query-verifying"></a>

 In typical operation, you don't need to perform any special actions to take advantage of parallel query\. After a query meets the essential requirements for parallel query, the query optimizer automatically decides whether to use parallel query for each specific query\. 

 If you run experiments in a development or test environment, you might find that parallel query isn't used because your tables are too small in number of rows or overall data volume\. The data for the table might also be entirely in the buffer pool, especially for tables that you created recently to perform experiments\. 

 As you monitor or tune cluster performance, make sure to decide whether parallel query is being used in the appropriate contexts\. You might adjust the database schema, settings, SQL queries, or even the cluster topology and application connection settings to take advantage of this feature\. 

 To check if a query is using parallel query, check the query plan \(also known as the "explain plan"\) by running the [EXPLAIN](https://dev.mysql.com/doc/refman/5.7/en/execution-plan-information.html) statement\. For examples of how SQL statements, clauses, and expressions affect `EXPLAIN` output for parallel query, see [How parallel query works with SQL constructs](#aurora-mysql-parallel-query-sql)\. 

 The following example demonstrates the difference between a traditional query plan and a parallel query plan\. This explain plan is from Query 3 from the TPC\-H benchmark\. Many of the sample queries throughout this section use the tables from the TPC\-H dataset\. You can get the table definitions, queries, and the `dbgen` program that generates sample data from [the TPC\-h website](http://www.tpc.org/tpch/)\. 

```
EXPLAIN SELECT l_orderkey,
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

 By default, the query might have a plan like the following\. If you don't see hash join used in the query plan, make sure that optimization is turned on first\. 

```
+----+-------------+----------+------------+------+---------------+------+---------+------+----------+----------+----------------------------------------------------+
| id | select_type | table    | partitions | type | possible_keys | key  | key_len | ref  | rows     | filtered | Extra                                              |
+----+-------------+----------+------------+------+---------------+------+---------+------+----------+----------+----------------------------------------------------+
|  1 | SIMPLE      | customer | NULL       | ALL  | NULL          | NULL | NULL    | NULL |  1480234 |    10.00 | Using where; Using temporary; Using filesort       |
|  1 | SIMPLE      | orders   | NULL       | ALL  | NULL          | NULL | NULL    | NULL | 14875240 |     3.33 | Using where; Using join buffer (Block Nested Loop) |
|  1 | SIMPLE      | lineitem | NULL       | ALL  | NULL          | NULL | NULL    | NULL | 59270573 |     3.33 | Using where; Using join buffer (Block Nested Loop) |
+----+-------------+----------+------------+------+---------------+------+---------+------+----------+----------+----------------------------------------------------+
```

For Aurora MySQL version 3, you turn on hash join at the session level by issuing the following statement\.

```
SET optimizer_switch='block_nested_loop=on';
```

For Aurora MySQL version 2\.09 and higher, you set the `aurora_disable_hash_join` DB parameter or DB cluster parameter to `0` \(off\)\. Turning off `aurora_disable_hash_join` sets the value of `optimizer_switch` to `hash_join=on`\.

After you turn on hash join, try running the `EXPLAIN` statement again\. For information about how to use hash joins effectively, see [Optimizing large Aurora MySQL join queries with hash joins](AuroraMySQL.BestPractices.md#Aurora.BestPractices.HashJoin)\.

 With hash join turned on but parallel query turned off, the query might have a plan like the following, which uses hash join but not parallel query\. 

```
+----+-------------+----------+...+-----------+-----------------------------------------------------------------+
| id | select_type | table    |...| rows      | Extra                                                           |
+----+-------------+----------+...+-----------+-----------------------------------------------------------------+
|  1 | SIMPLE      | customer |...|   5798330 | Using where; Using index; Using temporary; Using filesort       |
|  1 | SIMPLE      | orders   |...| 154545408 | Using where; Using join buffer (Hash Join Outer table orders)   |
|  1 | SIMPLE      | lineitem |...| 606119300 | Using where; Using join buffer (Hash Join Outer table lineitem) |
+----+-------------+----------+...+-----------+-----------------------------------------------------------------+
```

 After parallel query is turned on, two steps in this query plan can use the parallel query optimization, as shown under the `Extra` column in the `EXPLAIN` output\. The I/O\-intensive and CPU\-intensive processing for those steps is pushed down to the storage layer\. 

```
+----+...+--------------------------------------------------------------------------------------------------------------------------------+
| id |...| Extra                                                                                                                          |
+----+...+--------------------------------------------------------------------------------------------------------------------------------+
|  1 |...| Using where; Using index; Using temporary; Using filesort                                                                      |
|  1 |...| Using where; Using join buffer (Hash Join Outer table orders); Using parallel query (4 columns, 1 filters, 1 exprs; 0 extra)   |
|  1 |...| Using where; Using join buffer (Hash Join Outer table lineitem); Using parallel query (4 columns, 1 filters, 1 exprs; 0 extra) |
+----+...+--------------------------------------------------------------------------------------------------------------------------------+
```

 For information about how to interpret `EXPLAIN` output for a parallel query and the parts of SQL statements that parallel query can apply to, see [How parallel query works with SQL constructs](#aurora-mysql-parallel-query-sql)\. 

 The following example output shows the results of running the preceding query on a db\.r4\.2xlarge instance with a cold buffer pool\. The query runs substantially faster when using parallel query\. 

**Note**  
 Because timings depend on many environmental factors, your results might be different\. Always conduct your own performance tests to confirm the findings with your own environment, workload, and so on\. 

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

 Experiment with your workload to get a sense of whether individual SQL statements can take advantage of parallel query\. Then use the following monitoring techniques to help verify how often parallel query is used in real workloads over time\. For real workloads, extra factors such as concurrency limits apply\. 

## Monitoring parallel query<a name="aurora-mysql-parallel-query-monitoring"></a>

 If your Aurora MySQL cluster uses parallel query, you might see an increase in `VolumeReadIOPS` values\. Parallel queries don't use the buffer pool\. Thus, although the queries are fast, this optimized processing can result in an increase in read operations and associated charges\. 

 In addition to the Amazon CloudWatch metrics described in [Viewing metrics in the Amazon RDS console](USER_Monitoring.md), Aurora provides other global status variables\. You can use these global status variables to help monitor parallel query execution\. They can give you insights into why the optimizer might use or not use parallel query in a given situation\. To access these variables, you can use the `[SHOW GLOBAL STATUS](https://dev.mysql.com/doc/refman/5.7/en/server-status-variables.html)` command\. You can also find these variables listed following\. 

 A parallel query session isn't necessarily a one\-to\-one mapping with the queries performed by the database\. For example, suppose that your query plan has two steps that use parallel query\. In that case, the query involves two parallel sessions and the counters for requests attempted and requests successful are incremented by two\. 

 When you experiment with parallel query by issuing `EXPLAIN` statements, expect to see increases in the counters designated as "not chosen" even though the queries aren't actually running\. When you work with parallel query in production, you can check if the "not chosen" counters are increasing faster than you expect\. At this point, you can adjust so that parallel query runs for the queries that you expect\. To do so, you can change your cluster settings, query mix, DB instances where parallel query is turned on, and so on\.

 These counters are tracked at the DB instance level\. When you connect to a different endpoint, you might see different metrics because each DB instance runs its own set of parallel queries\. You might also see different metrics when the reader endpoint connects to a different DB instance for each session\. 


|  |  | 
| --- |--- |
|   **Name**   |   **Description**   | 
|   `Aurora_pq_request_attempted`   |   The number of parallel query sessions requested\. This value might represent more than one session per query, depending on SQL constructs such as subqueries and joins\.   | 
|   `Aurora_pq_request_executed`   |   The number of parallel query sessions run successfully\.   | 
|   `Aurora_pq_request_failed`   |   The number of parallel query sessions that returned an error to the client\. In some cases, a request for a parallel query might fail, for example due to a problem in the storage layer\. In these cases, the query part that failed is retried using the nonparallel query mechanism\. If the retried query also fails, an error is returned to the client and this counter is incremented\.   | 
|   `Aurora_pq_pages_pushed_down`   |   The number of data pages \(each with a fixed size of 16 KiB\) where parallel query avoided a network transmission to the head node\.   | 
|   `Aurora_pq_bytes_returned`   |   The number of bytes for the tuple data structures transmitted to the head node during parallel queries\. Divide by 16,384 to compare against `Aurora_pq_pages_pushed_down`\.   | 
|   `Aurora_pq_request_not_chosen`   |   The number of times parallel query wasn't chosen to satisfy a query\. This value is the sum of several other more granular counters\. An `EXPLAIN` statement can increment this counter even though the query isn't actually performed\.   | 
|   `Aurora_pq_request_not_chosen_below_min_rows`   |   The number of times parallel query wasn't chosen due to the number of rows in the table\. An `EXPLAIN` statement can increment this counter even though the query isn't actually performed\.   | 
|   `Aurora_pq_request_not_chosen_small_table`   |   The number of times parallel query wasn't chosen due to the overall size of the table, as determined by number of rows and average row length\. An `EXPLAIN` statement can increment this counter even though the query isn't actually performed\.   | 
|   `Aurora_pq_request_not_chosen_high_buffer_pool_pct`   |   The number of times parallel query wasn't chosen because a high percentage of the table data \(currently, greater than 95 percent\) was already in the buffer pool\. In these cases, the optimizer determines that reading the data from the buffer pool is more efficient\. An `EXPLAIN` statement can increment this counter even though the query isn't actually performed\.   | 
|   `Aurora_pq_request_not_chosen_few_pages_outside_buffer_pool`   |   The number of times parallel query wasn't chosen, even though less than 95 percent of the table data was in the buffer pool, because there wasn't enough unbuffered table data to make parallel query worthwhile\.   | 
|   `Aurora_pq_max_concurrent_requests`   |   The maximum number of parallel query sessions that can run concurrently on this Aurora DB instance\. This is a fixed number that depends on the AWS DB instance class\.   | 
|   `Aurora_pq_request_in_progress`   |   The number of parallel query sessions currently in progress\. This number applies to the particular Aurora DB instance that you are connected to, not the entire Aurora DB cluster\. To see if a DB instance is close to its concurrency limit, compare this value to `Aurora_pq_max_concurrent_requests`\.   | 
|   `Aurora_pq_request_throttled`   |   The number of times parallel query wasn't chosen due to the maximum number of concurrent parallel queries already running on a particular Aurora DB instance\.   | 
|   `Aurora_pq_request_not_chosen_long_trx`   |   The number of parallel query requests that used the nonparallel query processing path, due to the query being started inside a long\-running transaction\. An `EXPLAIN` statement can increment this counter even though the query isn't actually performed\.   | 
|   `Aurora_pq_request_not_chosen_unsupported_access`   |   The number of parallel query requests that use the nonparallel query processing path because the `WHERE` clause doesn't meet the criteria for parallel query\. This result can occur if the query doesn't require a data\-intensive scan, or if the query is a `DELETE` or `UPDATE` statement\.   | 
|   `Aurora_pq_request_not_chosen_column_bit`   |   The number of parallel query requests that use the nonparallel query processing path because of an unsupported data type in the list of projected columns\.   | 
|   `Aurora_pq_request_not_chosen_column_geometry`   |   The number of parallel query requests that use the nonparallel query processing path because the table has columns with the `GEOMETRY` data type\. For information about Aurora MySQL versions that remove this limitation, see [Upgrading parallel query clusters to Aurora MySQL version 3](#aurora-mysql-parallel-query-upgrade-pqv2)\.   | 
|   `Aurora_pq_request_not_chosen_column_lob`   |   The number of parallel query requests that use the nonparallel query processing path because the table has columns with a `LOB` data type, or `VARCHAR` columns that are stored externally due to the declared length\. For information about Aurora MySQL versions that remove this limitation, see [Upgrading parallel query clusters to Aurora MySQL version 3](#aurora-mysql-parallel-query-upgrade-pqv2)\.   | 
|   `Aurora_pq_request_not_chosen_column_virtual`   |   The number of parallel query requests that use the nonparallel query processing path because the table contains a virtual column\.   | 
|   `Aurora_pq_request_not_chosen_custom_charset`   |   The number of parallel query requests that use the nonparallel query processing path because the table has columns with a custom character set\.   | 
|   `Aurora_pq_request_not_chosen_fast_ddl`   |   The number of parallel query requests that use the nonparallel query processing path because the table is currently being altered by a fast DDL `ALTER` statement\.   | 
|   `Aurora_pq_request_not_chosen_full_text_index`   |   The number of parallel query requests that use the nonparallel query processing path because the table has full\-text indexes\.   | 
|   `Aurora_pq_request_not_chosen_index_hint`   |   The number of parallel query requests that use the nonparallel query processing path because the query includes an index hint\.   | 
|   `Aurora_pq_request_not_chosen_innodb_table_format`   |   The number of parallel query requests that use the nonparallel query processing path because the table uses an unsupported InnoDB row format\. Aurora parallel query only applies to the `COMPACT`, `REDUNDANT`, and `DYNAMIC` row formats\.   | 
|   `Aurora_pq_request_not_chosen_no_where_clause`   |   The number of parallel query requests that use the nonparallel query processing path because the query doesn't include any `WHERE` clause\.   | 
|   `Aurora_pq_request_not_chosen_range_scan`   |   The number of parallel query requests that use the nonparallel query processing path because the query uses a range scan on an index\.   | 
|   `Aurora_pq_request_not_chosen_row_length_too_long`   |   The number of parallel query requests that use the nonparallel query processing path because the total combined length of all the columns is too long\.   | 
|   `Aurora_pq_request_not_chosen_temporary_table`   |   The number of parallel query requests that use the nonparallel query processing path because the query refers to temporary tables that use the unsupported `MyISAM` or `memory` table types\.   | 
|   `Aurora_pq_request_not_chosen_tx_isolation`   |   The number of parallel query requests that use the nonparallel query processing path because query uses an unsupported transaction isolation level\. On reader DB instances, parallel query only applies to the `REPEATABLE READ` and `READ COMMITTED` isolation levels\.   | 
|   `Aurora_pq_request_not_chosen_update_delete_stmts`   |   The number of parallel query requests that use the nonparallel query processing path because the query is part of an `UPDATE` or `DELETE` statement\.   | 

## How parallel query works with SQL constructs<a name="aurora-mysql-parallel-query-sql"></a>

 In the following section, you can find more detail about why particular SQL statements use or don't use parallel query\. This section also details how Aurora MySQL features interact with parallel query\. This information can help you diagnose performance issues for a cluster that uses parallel query or understand how parallel query applies for your particular workload\. 

 The decision to use parallel query relies on many factors that occur at the moment that the statement runs\. Thus, parallel query might be used for certain queries always, never, or only under certain conditions\. 

**Tip**  
 When you view these examples in HTML, you can use the **Copy** widget in the upper\-right corner of each code listing to copy the SQL code to try yourself\. Using the **Copy** widget avoids copying the extra characters around the `mysql>` prompt and `->` continuation lines\. 

**Topics**
+ [EXPLAIN statement](#aurora-mysql-parallel-query-sql-explain)
+ [WHERE clause](#aurora-mysql-parallel-query-sql-where)
+ [Data definition language \(DDL\)](#aurora-mysql-parallel-query-sql-ddl)
+ [Column data types](#aurora-mysql-parallel-query-sql-datatypes)
+ [Partitioned tables](#aurora-mysql-parallel-query-sql-partitioning)
+ [Aggregate functions, GROUP BY clauses, and HAVING clauses](#aurora-mysql-parallel-query-sql-aggregation)
+ [Function calls in WHERE clause](#aurora-mysql-parallel-query-sql-functions)
+ [LIMIT clause](#aurora-mysql-parallel-query-sql-limit)
+ [Comparison operators](#aurora-mysql-parallel-query-sql-comparisons)
+ [Joins](#aurora-mysql-parallel-query-sql-joins)
+ [Subqueries](#aurora-mysql-parallel-query-sql-subqueries)
+ [UNION](#aurora-mysql-parallel-query-sql-union)
+ [Views](#aurora-mysql-parallel-query-sql-views)
+ [Data manipulation language \(DML\) statements](#aurora-mysql-parallel-query-sql-dml)
+ [Transactions and locking](#aurora-mysql-parallel-query-sql-transactions)
+ [B\-tree indexes](#aurora-mysql-parallel-query-sql-indexes)
+ [Full\-text search \(FTS\) indexes](#aurora-mysql-parallel-query-sql-fts)
+ [Virtual columns](#aurora-mysql-parallel-query-sql-virtual-column)
+ [Built\-in caching mechanisms](#aurora-mysql-parallel-query-sql-caching)
+ [Optimizer hints](#aurora-mysql-parallel-query-hints)
+ [MyISAM temporary tables](#aurora-mysql-parallel-query-sql-myisam)

### EXPLAIN statement<a name="aurora-mysql-parallel-query-sql-explain"></a>

 As shown in examples throughout this section, the `EXPLAIN` statement indicates whether each stage of a query is currently eligible for parallel query\. It also indicates which aspects of a query can be pushed down to the storage layer\. The most important items in the query plan are the following: 
+  A value other than `NULL` for the `key` column suggests that the query can be performed efficiently using index lookups, and parallel query is unlikely\. 
+  A small value for the `rows` column \(a value not in the millions\) suggests that the query isn't accessing enough data to make parallel query worthwhile\. This means that parallel query is unlikely\. 
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

 In cases where parallel query isn't chosen, you can typically deduce the reason from the other columns of the `EXPLAIN` output\. For example, the `rows` value might be too small, or the `possible_keys` column might indicate that the query can use an index lookup instead of a data\-intensive scan\. The following example shows a query where the optimizer can estimate that the query will scan only a small number of rows\. It does so based on the characteristics of the primary key\. In this case, parallel query isn't required\. 

```
mysql> explain select count(*) from part where p_partkey between 1 and 100;
+----+-------------+-------+-------+---------------+---------+---------+------+------+--------------------------+
| id | select_type | table | type  | possible_keys | key     | key_len | ref  | rows | Extra                    |
+----+-------------+-------+-------+---------------+---------+---------+------+------+--------------------------+
|  1 | SIMPLE      | part  | range | PRIMARY       | PRIMARY | 4       | NULL |   99 | Using where; Using index |
+----+-------------+-------+-------+---------------+---------+---------+------+------+--------------------------+
```

 The output showing whether parallel query will be used takes into account all available factors at the moment that the `EXPLAIN` statement is run\. The optimizer might make a different choice when the query is actually run, if the situation changed in the meantime\. For example, `EXPLAIN` might report that a statement will use parallel query\. But when the query is actually run later, it might not use parallel query based on the conditions then\. Such conditions can include several other parallel queries running concurrently\. They can also include rows being deleted from the table, a new index being created, too much time passing within an open transaction, and so on\. 

### WHERE clause<a name="aurora-mysql-parallel-query-sql-where"></a>

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

### Data definition language \(DDL\)<a name="aurora-mysql-parallel-query-sql-ddl"></a>

In Aurora MySQL version 2, parallel query is only available for tables for which no fast data definition language \(DDL\) operations are pending\. In Aurora MySQL version 3, you can use parallel query on a table at the same time as an instant DDL operation\.

Instant DDL in Aurora MySQL version 3 replaces the fast DDL feature in Aurora MySQL version 2\. For information about instant DDL, see [Instant DDL \(Aurora MySQL version 3\)](AuroraMySQL.Managing.FastDDL.md#AuroraMySQL.mysql80-instant-ddl)\.  

### Column data types<a name="aurora-mysql-parallel-query-sql-datatypes"></a>

 In Aurora MySQL version 3, parallel query can work with tables containing columns with data types `TEXT`, `BLOB`, `JSON`, and `GEOMETRY`\. It can also work with `VARCHAR` and `CHAR` columns with a maximum declared length longer than 768 bytes\. If your query refers to any columns containing such large object types, the additional work to retrieve them does add some overhead to query processing\. In that case, check if the query can omit the references to those columns\. If not, run benchmarks to confirm if such queries are faster with parallel query turned on or turned off\. 

In Aurora MySQL version 2, parallel query has these limitations for large object types:
+ `TEXT`, `BLOB`, `JSON`, and `GEOMETRY` data types aren't supported with parallel query\. A query that refers to any columns of these types can't use parallel query\.
+ Variable\-length columns \(`VARCHAR` and `CHAR` data types\) are compatible with parallel query up to a maximum declared length of 768 bytes\. A query that refers to any columns of the types declared with a longer maximum length can't use parallel query\. For columns that use multibyte character sets, the byte limit takes into account the maximum number of bytes in the character set\. For example, for the character set `utf8mb4` \(which has a maximum character length of 4 bytes\), a `VARCHAR(192)` column is compatible with parallel query but a `VARCHAR(193)` column isn't\.

### Partitioned tables<a name="aurora-mysql-parallel-query-sql-partitioning"></a>

 You can use partitioned tables with parallel query in Aurora MySQL version 3\. Because partitioned tables are represented internally as multiple smaller tables, a query that uses parallel query on a nonpartitioned table might not use parallel query on an identical partitioned table\. Aurora MySQL considers whether each partition is large enough to qualify for the parallel query optimization, instead of evaluating the size of the entire table\. Check whether the `Aurora_pq_request_not_chosen_small_table` status variable is incremented if a query on a partitioned table doesn't use parallel query when you expect it to\. 

 For example, consider one table partitioned with `PARTITION BY HASH (column) PARTITIONS 2` and another table partitioned with `PARTITION BY HASH (column) PARTITIONS 10`\. In the table with two partititions, the partitions are five times as large as the table with ten partitions\. Thus, parallel query is more likely to be used for queries against the table with fewer partitions\. In the following example, the table `PART_BIG_PARTITIONS` has two partitions and `PART_SMALL_PARTITIONS` has ten partitions\. With identical data, parallel query is more likely to be used for the table with fewer big partitions\. 

```
mysql> explain select count(*), p_brand from part_big_partitions where p_name is not null
    ->   and p_mfgr in ('Manufacturer#1', 'Manufacturer#3') and p_retailprice > 1000 group by p_brand;
+----+-------------+---------------------+------------+-------------------------------------------------------------------------------------------------------------------+
| id | select_type | table               | partitions | Extra                                                                                                             |
+----+-------------+---------------------+------------+-------------------------------------------------------------------------------------------------------------------+
|  1 | SIMPLE      | part_big_partitions | p0,p1      | Using where; Using temporary; Using parallel query (4 columns, 1 filters, 1 exprs; 0 extra; 1 group-bys, 1 aggrs) |
+----+-------------+---------------------+------------+-------------------------------------------------------------------------------------------------------------------+

mysql> explain select count(*), p_brand from part_small_partitions where p_name is not null
    ->   and p_mfgr in ('Manufacturer#1', 'Manufacturer#3') and p_retailprice > 1000 group by p_brand;
+----+-------------+-----------------------+-------------------------------+------------------------------+
| id | select_type | table                 | partitions                    | Extra                        |
+----+-------------+-----------------------+-------------------------------+------------------------------+
|  1 | SIMPLE      | part_small_partitions | p0,p1,p2,p3,p4,p5,p6,p7,p8,p9 | Using where; Using temporary |
+----+-------------+-----------------------+-------------------------------+------------------------------+
```

### Aggregate functions, GROUP BY clauses, and HAVING clauses<a name="aurora-mysql-parallel-query-sql-aggregation"></a>

 Queries involving aggregate functions are often good candidates for parallel query, because they involve scanning large numbers of rows within large tables\. 

 In Aurora MySQL 3, parallel query can optimize aggregate function calls in the select list and the `HAVING` clause\. 

 Before Aurora MySQL 3, aggregate function calls in the select list or the `HAVING` clause aren't pushed down to the storage layer\. However, parallel query can still improve the performance of such queries with aggregate functions\. It does so by first extracting column values from the raw data pages in parallel at the storage layer\. It then transmits those values back to the head node in a compact tuple format instead of as entire data pages\. As always, the query requires at least one `WHERE` predicate for parallel query to be activated\. 

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

### Function calls in WHERE clause<a name="aurora-mysql-parallel-query-sql-functions"></a>

 Aurora can apply the parallel query optimization to calls to most built\-in functions in the `WHERE` clause\. Parallelizing these function calls offloads some CPU work from the head node\. Evaluating the predicate functions in parallel during the earliest query stage helps Aurora minimize the amount of data transmitted and processed during later stages\. 

 Currently, the parallelization doesn't apply to function calls in the select list\. Those functions are evaluated by the head node, even if identical function calls appear in the `WHERE` clause\. The original values from relevant columns are included in the tuples transmitted from the storage nodes back to the head node\. The head node performs any transformations such as `UPPER`, `CONCATENATE`, and so on to produce the final values for the result set\. 

 In the following example, parallel query parallelizes the call to `LOWER` because it appears in the `WHERE` clause\. Parallel query doesn't affect the calls to `SUBSTR` and `UPPER` because they appear in the select list\. 

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

### LIMIT clause<a name="aurora-mysql-parallel-query-sql-limit"></a>

 Currently, parallel query isn't used for any query block that includes a `LIMIT` clause\. Parallel query might still be used for earlier query phases with `GROUP` by, `ORDER BY`, or joins\. 

### Comparison operators<a name="aurora-mysql-parallel-query-sql-comparisons"></a>

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

 Each phase of join processing is evaluated to check if it is eligible for parallel query\. If more than one phase can use parallel query, these phases are performed in sequence\. Thus, each join query counts as a single parallel query session in terms of concurrency limits\. 

 For example, when a join query includes `WHERE` predicates to filter the rows from one of the joined tables, that filtering option can use parallel query\. As another example, suppose that a join query uses the hash join mechanism, for example to join a big table with a small table\. In this case, the table scan to produce the Bloom filter data structure might be able to use parallel query\. 

**Note**  
 Parallel query is typically used for the kinds of resource\-intensive queries that benefit from the hash join optimization\. The method for turning on the hash join optimization depends on the Aurora MySQL version\. For details for each version, see [Turning on hash join for parallel query clusters](#aurora-mysql-parallel-query-enabling-hash-join)\. For information about how to use hash joins effectively, see [Optimizing large Aurora MySQL join queries with hash joins](AuroraMySQL.BestPractices.md#Aurora.BestPractices.HashJoin)\. 

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

 The outer query block and inner subquery block might each use parallel query, or not\. Whether they do is based on the usual characteristics of the table, `WHERE` clause, and so on, for each block\. For example, the following query uses parallel query for the subquery block but not the outer block\. 

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

 Currently, correlated subqueries can't use the parallel query optimization\. 

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

 For example, the following query plan shows a view definition that usually doesn't use parallel query\. When the view is queried with additional `WHERE` clauses, Aurora MySQL uses parallel query\. 

```
mysql> create view part_view as select * from part;
mysql> explain select count(*) from part_view where p_partkey is not null;
+----+...+----------+----------------------------------------------------------------------------+
| id |...| rows     | Extra                                                                      |
+----+...+----------+----------------------------------------------------------------------------+
|  1 |...| 20427936 | Using where; Using parallel query (1 columns, 0 filters, 0 exprs; 1 extra) |
+----+...+----------+----------------------------------------------------------------------------+
```

### Data manipulation language \(DML\) statements<a name="aurora-mysql-parallel-query-sql-dml"></a>

 The `INSERT` statement can use parallel query for the `SELECT` phase of processing, if the `SELECT` part meets the other conditions for parallel query\. 

```
mysql> create table part_subset like part;
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

### Transactions and locking<a name="aurora-mysql-parallel-query-sql-transactions"></a>

 You can use all the isolation levels on the Aurora primary instance\. 

On Aurora reader DB instances, parallel query applies to statements performed under the `REPEATABLE READ` isolation level\. Aurora MySQL version 2\.09 or higher can also use the `READ COMMITTED` isolation level on reader DB instances\. `REPEATABLE READ` is the default isolation level for Aurora reader DB instances\. To use `READ COMMITTED` isolation level on reader DB instances requires setting the `aurora_read_replica_read_committed` configuration option at the session level\. The `READ COMMITTED` isolation level for reader instances complies with SQL standard behavior\. However, the isolation is less strict on reader instances than when queries use `READ COMMITTED` isolation level on the writer instance\.

 For more information about Aurora isolation levels, especially the differences in `READ COMMITTED` between writer and reader instances, see [Aurora MySQL isolation levels](AuroraMySQL.Reference.IsolationLevels.md)\. 

 After a big transaction is finished, the table statistics might be stale\. Such stale statistics might require an `ANALYZE TABLE` statement before Aurora can accurately estimate the number of rows\. A large\-scale DML statement might also bring a substantial portion of the table data into the buffer pool\. Having this data in the buffer pool can lead to parallel query being chosen less frequently for that table until the data is evicted from the pool\. 

 When your session is inside a long\-running transaction \(by default, 10 minutes\), further queries inside that session don't use parallel query\. A timeout can also occur during a single long\-running query\. This type of timeout might happen if the query runs for longer than the maximum interval \(currently 10 minutes\) before the parallel query processing starts\. 

 You can reduce the chance of starting long\-running transactions accidentally by setting `autocommit=1` in `mysql` sessions where you perform ad hoc \(one\-time\) queries\. Even a `SELECT` statement against a table begins a transaction by creating a read view\. A *read view *is a consistent set of data for subsequent queries that remains until the transaction is committed\. Be aware of this restriction also when using JDBC or ODBC applications with Aurora, because such applications might run with the `autocommit` setting turned off\. 

 The following example shows how, with the `autocommit` setting turned off, running a query against a table creates a read view that implicitly begins a transaction\. Queries that are run shortly afterward can still use parallel query\. However, after a pause of several minutes, queries are no longer eligible for parallel query\. Ending the transaction with `COMMIT` or `ROLLBACK` restores parallel query eligibility\. 

```
mysql> set autocommit=0;

mysql> explain select sql_no_cache count(*) from part where p_retailprice > 10.0;
+----+...+---------+----------------------------------------------------------------------------+
| id |...| rows    | Extra                                                                      |
+----+...+---------+----------------------------------------------------------------------------+
|  1 |...| 2976129 | Using where; Using parallel query (1 columns, 1 filters, 0 exprs; 0 extra) |
+----+...+---------+----------------------------------------------------------------------------+

mysql> select sleep(720); explain select sql_no_cache count(*) from part where p_retailprice > 10.0;
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

mysql> explain select sql_no_cache count(*) from part where p_retailprice > 10.0;
+----+...+---------+----------------------------------------------------------------------------+
| id |...| rows    | Extra                                                                      |
+----+...+---------+----------------------------------------------------------------------------+
|  1 |...| 2976129 | Using where; Using parallel query (1 columns, 1 filters, 0 exprs; 0 extra) |
+----+...+---------+----------------------------------------------------------------------------+
```

 To see how many times queries weren't eligible for parallel query because they were inside long\-running transactions, check the status variable `Aurora_pq_request_not_chosen_long_trx`\. 

```
mysql> show global status like '%pq%trx%';
+---------------------------------------+-------+
| Variable_name                         | Value |
+---------------------------------------+-------+
| Aurora_pq_request_not_chosen_long_trx | 4     |
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

### B\-tree indexes<a name="aurora-mysql-parallel-query-sql-indexes"></a>

 The statistics gathered by the `ANALYZE TABLE` statement help the optimizer to decide when to use parallel query or index lookups, based on the characteristics of the data for each column\. Keep statistics current by running `ANALYZE TABLE` after DML operations that make substantial changes to the data within a table\. 

 If index lookups can perform a query efficiently without a data\-intensive scan, Aurora might use index lookups\. Doing so avoids the overhead of parallel query processing\. There are also concurrency limits on the number of parallel queries that can run simultaneously on any Aurora DB cluster\. Make sure to use best practices for indexing your tables, so that your most frequent and most highly concurrent queries use index lookups\. 

### Full\-text search \(FTS\) indexes<a name="aurora-mysql-parallel-query-sql-fts"></a>

 Currently, parallel query isn't used for tables that contain a full\-text search index, regardless of whether the query refers to such indexed columns or uses the `MATCH` operator\. 

### Virtual columns<a name="aurora-mysql-parallel-query-sql-virtual-column"></a>

 Currently, parallel query isn't used for tables that contain a virtual column, regardless of whether the query refers to any virtual columns\. 

### Built\-in caching mechanisms<a name="aurora-mysql-parallel-query-sql-caching"></a>

 Aurora includes built\-in caching mechanisms, namely the buffer pool and the query cache\. The Aurora optimizer chooses between these caching mechanisms and parallel query depending on which one is most effective for a particular query\. 

 When a parallel query filters rows and transforms and extracts column values, data is transmitted back to the head node as tuples rather than as data pages\. Therefore, running a parallel query doesn't add any pages to the buffer pool, or evict pages that are already in the buffer pool\. 

 Aurora checks the number of pages of table data that are present in the buffer pool, and what proportion of the table data that number represents\. Aurora uses that information to determine whether it is more efficient to use parallel query \(and bypass the data in the buffer pool\)\. Alternatively, Aurora might use the nonparallel query processing path, which uses data cached in the buffer pool\. Which pages are cached and how data\-intensive queries affect caching and eviction depends on configuration settings related to the buffer pool\. Therefore, it can be hard to predict whether any particular query uses parallel query, because the choice depends on the ever\-changing data within the buffer pool\. 

 Also, Aurora imposes concurrency limits on parallel queries\. Because not every query uses parallel query, tables that are accessed by multiple queries simultaneously typically have a substantial portion of their data in the buffer pool\. Therefore, Aurora often doesn't choose these tables for parallel queries\. 

 When you run a sequence of nonparallel queries on the same table, the first query might be slow due to the data not being in the buffer pool\. Then the second and subsequent queries are much faster because the buffer pool is now "warmed up"\. Parallel queries typically show consistent performance from the very first query against the table\. When conducting performance tests, benchmark the nonparallel queries with both a cold and a warm buffer pool\. In some cases, the results with a warm buffer pool can compare well to parallel query times\. In these cases, consider factors such as the frequency of queries against that table\. Also consider whether it is worthwhile to keep the data for that table in the buffer pool\. 

 The query cache avoids rerunning a query when an identical query is submitted and the underlying table data hasn't changed\. Queries optimized by parallel query feature can go into the query cache, effectively making them instantaneous when run again\. 

**Note**  
 When conducting performance comparisons, the query cache can produce artificially low timing numbers\. Therefore, in benchmark\-like situations, you can use the `sql_no_cache` hint\. This hint prevents the result from being served from the query cache, even if the same query had been run previously\. The hint comes immediately after the `SELECT` statement in a query\. Many parallel query examples in this topic include this hint, to make query times comparable between versions of the query for which parallel query is turned on and turned off\.   
 Make sure that you remove this hint from your source when you move to production use of parallel query\. 

### Optimizer hints<a name="aurora-mysql-parallel-query-hints"></a>

Another way to control the optimizer is by using optimizer hints, which can be specified within individual statements\. For example, you can turn on an optimization for one table in a statement, and then turn off the optimization for a different table\. For more information about these hints, see [Optimizer Hints](https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html) in the *MySQL Reference Manual*\.

You can use SQL hints with Aurora MySQL queries to fine\-tune performance\. You can also use hints to prevent execution plans for important queries from changing because of unpredictable conditions\.

We have extended the SQL hints feature to help you control optimizer choices for your query plans\. These hints apply to queries that use parallel query optimization\. For more information, see [Aurora MySQL hints](AuroraMySQL.Reference.Hints.md)\.

### MyISAM temporary tables<a name="aurora-mysql-parallel-query-sql-myisam"></a>

The parallel query optimization only applies to InnoDB tables\. Because Aurora MySQL uses MyISAM behind the scenes for temporary tables, internal query phases involving temporary tables never use parallel query\. These query phases are indicated by `Using temporary` in the `EXPLAIN` output\. 