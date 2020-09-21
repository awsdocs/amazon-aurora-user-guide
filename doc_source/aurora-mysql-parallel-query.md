# Working with Parallel Query for Amazon Aurora MySQL<a name="aurora-mysql-parallel-query"></a>

 Following, you can find a description of the parallel query performance optimization for Amazon Aurora with MySQL compatibility\. This feature uses a special processing path for certain data\-intensive queries, taking advantage of the Aurora shared storage architecture\. Currently, Aurora MySQL versions that are compatible with MySQL 5\.6 or MySQL 5\.7 support parallel query\. Parallel query works best with Aurora MySQL DB clusters that have tables with millions of rows and analytic queries that take minutes or hours to complete\. 

**Topics**
+ [Overview of Parallel Query for Aurora MySQL](#aurora-mysql-parallel-query-overview)
+ [Planning for a Parallel Query Cluster](#aurora-mysql-parallel-query-planning)
+ [Creating a DB Cluster that Works with Parallel Query](#aurora-mysql-parallel-query-creating-cluster)
+ [Enabling and Disabling Parallel Query](#aurora-mysql-parallel-query-enabling)
+ [Upgrade Considerations for Parallel Query](#aurora-mysql-parallel-query-upgrade)
+ [Performance Tuning for Parallel Query](#aurora-mysql-parallel-query-performance)
+ [Creating Schema Objects to Take Advantage of Parallel Query](#aurora-mysql-parallel-query-tables)
+ [Verifying Which Statements Use Parallel Query](#aurora-mysql-parallel-query-verifying)
+ [Monitoring Parallel Query](#aurora-mysql-parallel-query-monitoring)
+ [How Parallel Query Works with SQL Constructs](#aurora-mysql-parallel-query-sql)

## Overview of Parallel Query for Aurora MySQL<a name="aurora-mysql-parallel-query-overview"></a>

 Aurora MySQL parallel query is an optimization that parallelizes some of the I/O and computation involved in processing data\-intensive queries\. The work that is parallelized includes retrieving rows from storage, extracting column values, and determining which rows match the conditions in the `WHERE` clause and join clauses\. This data\-intensive work is delegated \(in database optimization terms, *pushed down*\) to multiple nodes in the Aurora distributed storage layer\. Without parallel query, each query brings all the scanned data to a single node within the Aurora MySQL cluster \(the head node\) and performs all the query processing there\. 

**Tip**  
 The PostgreSQL database engine also has a feature that's also called "parallel query"\. That feature is unrelated to Aurora parallel query\. 

 When the parallel query feature is enabled, the Aurora MySQL engine automatically determines when queries can benefit, without requiring SQL changes such as hints or table attributes\. In the following sections, you can find an explanation of when parallel query is applied to a query\. You can also find how to make sure that parallel query is applied where it provides the most benefit\. 

**Note**  
 The parallel query optimization provides the most benefit for long\-running queries that take minutes or hours to complete\. Aurora MySQL generally doesn't perform parallel query optimization for inexpensive queries\. It also generally doesn't perform parallel query optimization if another optimization technique makes more sense, such as query caching, buffer pool caching, or index lookups\. If you find that parallel query isn't being used when you expect it, see [Verifying Which Statements Use Parallel Query](#aurora-mysql-parallel-query-verifying)\. 

**Topics**
+ [Benefits](#aurora-mysql-parallel-query-benefits)
+ [Architecture](#aurora-mysql-parallel-query-architecture)
+ [Prerequisites](#aurora-mysql-parallel-query-prereqs)
+ [Limitations](#aurora-mysql-parallel-query-limitations)

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

 To use all features of parallel query requires an Aurora MySQL DB cluster that's running version 1\.23 or 2\.09 and higher\. If you already have a cluster that you want to use with parallel query, you can upgrade it to a compatible version and enable parallel query afterward\. In that case, make sure to follow the upgrade procedure in [Upgrade Considerations for Parallel Query](#aurora-mysql-parallel-query-upgrade) because the configuration setting names and default values are different in these newer versions\. 

 You can also use parallel query with certain older Aurora MySQL versions that are compatible with MySQL 5\.6: 1\.22\.2, 1\.20\.1, 1\.19\.6, and 5\.6\.10a\. The parallel query support for these older versions is only available in certain AWS Regions\. Those older versions have additional limitations, as described following\. Using parallel query with an older Aurora MySQL version also requires creating a dedicated DB cluster with a special engine mode parameter that can't be changed later\. For those reasons, we recommend using parallel query with Aurora MySQL 1\.23 or 2\.09 and higher where practical\. 

 The DB instances in your cluster must be using the `db.r*` instance classes\. 

 Your tables must be nonpartitioned for the parallel query optimization to apply to them\. 

 Make sure that the hash join optimization is turned on for your cluster\. The procedure to do so is different depending on whether your cluster is running an Aurora MySQL version higher or lower than 1\.23 or 2\.09\. To learn how, see [Enabling Hash Join for Parallel Query Clusters](#aurora-mysql-parallel-query-enabling-hash-join)\. 

 To customize parameters such as `aurora_parallel_query` and `aurora_disable_hash_join`, you must have a custom parameter group that you use with your cluster\. You can specify these parameters individually for each DB instance by using a DB parameter group\. However, we recommend that you specify them in a DB cluster parameter group\. That way, all DB instances in your cluster inherit the same settings for these parameters\. 

### Limitations<a name="aurora-mysql-parallel-query-limitations"></a>

 The following limitations apply to the parallel query feature: 
+  You can't use parallel query with the db\.t2 or db\.t3 instance classes\. This limitation applies even if you request parallel query using the `aurora_pq_force` SQL hint\. 
+  Parallel query doesn't apply to tables using the `COMPRESSED` or `REDUNDANT` row formats\. Use the `COMPACT` or `DYNAMIC` row formats for tables you plan to use with parallel query\. 
+  Currently, partitioned tables aren't supported for parallel query\. You can use partitioned tables in parallel query clusters\. Queries against those tables use the non\-parallel query processing path\. 
+  Aurora uses a cost\-based algorithm to determine whether to use the parallel query mechanism for each SQL statement\. Using certain SQL constructs in a statement can prevent parallel query or make parallel query unlikely for that statement\. For information about compatibility of SQL constructs with parallel query, see [How Parallel Query Works with SQL Constructs](#aurora-mysql-parallel-query-sql)\. 
+  Each Aurora DB instance can run only a certain number of parallel query sessions at one time\. If a query has multiple parts that use parallel query, such as subqueries, joins, or `UNION` operators, those phases run in sequence\. The statement only counts as a single parallel query session at any one time\. You can monitor the number of active sessions using the [parallel query status variables](#aurora-mysql-parallel-query-monitoring)\. You can check the limit on concurrent sessions for a given DB instance by querying the status variable `Aurora_pq_max_concurrent_requests`\. 
+  The parallel query option isn't available in the AWS Regions China \(Beijing\) and China \(Ningxia\)\. Parallel query is available in all other AWS Regions that Aurora supports\. For most AWS Regions, the minimum required Aurora MySQL version to use parallel query is 1\.23 or 2\.09\. 
+  Aurora MySQL 1\.22\.2, 1\.20\.1, 1\.19\.6, and 5\.6\.10a only: Using parallel query with these older versions involves creating a new cluster, or restoring from an existing Aurora MySQL cluster snapshot\. 
+  Aurora MySQL 1\.22\.2, 1\.20\.1, 1\.19\.6, and 5\.6\.10a only: Parallel query doesn't support AWS Identity and Access Management \(IAM\) database authentication\. 

## Planning for a Parallel Query Cluster<a name="aurora-mysql-parallel-query-planning"></a>

 Planning for a DB cluster that's enabled for parallel query requires making some choices\. These include performing setup steps \(either creating or restoring a full Aurora MySQL cluster\) and deciding how broadly to enable parallel query across your DB cluster\. 

 Consider the following as part of planning: 
+  Which Aurora MySQL version do you plan to use for the cluster? Depending on your choice, you can use one of these ways to enable parallel query for the cluster: 

   If you use Aurora MySQL that's compatible with MySQL 5\.7, you must choose Aurora MySQL 2\.09 or higher\. In this case, you always create a provisioned cluster\. Then you enable parallel query using the `aurora_parallel_query` parameter\. We recommend this choice if you are starting with Aurora parallel query for the first time\. 

   If you use Aurora MySQL that's compatible with MySQL 5\.6, you can choose version 1\.23 or certain lower versions\. With version 1\.23 or higher, you create a provisioned cluster and then enable parallel query using the `aurora_parallel_query` DB cluster parameter\. With a version lower than 1\.23, you choose the `parallelquery` engine mode when creating the cluster\. In this case, parallel query is permanently turned on for the cluster\. The `parallelquery` engine mode imposes limitations on interoperating with other kinds of Aurora MySQL clusters\. If you have a choice, we recommend choosing version 1\.23 or higher for Aurora MySQL with MySQL 5\.6 compatibility\. 

   If you have an existing Aurora MySQL cluster that's running version 1\.23 or higher, or 2\.09 or higher, you don't have to create a new cluster to use parallel query\. You can associate your cluster, or specific DB instances in the cluster, with a parameter group that has the `aurora_parallel_query` parameter enabled\. By doing so, you can reduce the time and effort to set up the relevant data to use with parallel query\. 
+  Plan for any large tables that you need to reorganize so that you can use parallel query when accessing them\. You might need to create new versions of some large tables where parallel query is useful\. For example, you might need to make the table nonpartitioned or to remove full\-text search indexes\. For details, see [Creating Schema Objects to Take Advantage of Parallel Query](#aurora-mysql-parallel-query-tables)\. 

### Checking Aurora MySQL Version Compatibility for Parallel Query<a name="aurora-mysql-parallel-query-checking-compatibility"></a>

 To check which Aurora MySQL versions are compatible with parallel query clusters, use the `describe-db-engine-versions` AWS CLI command and check the value of the `SupportsParallelQuery` field\. The following code example shows how to check which combinations are available for parallel query clusters in a specified AWS Region\. Make sure to specify the full `--query` parameter string on a single line\. 

```
aws rds describe-db-engine-versions --region us-east-1 --engine aurora --query '*[]|[?SupportsParallelQuery == `true`].[EngineVersion]' --output text

aws rds describe-db-engine-versions --region us-east-1 --engine aurora-mysql --query '*[]|[?SupportsParallelQuery == `true`].[EngineVersion]' --output text
```

 The preceding commands produce output similar to the following\. The output might vary depending on which Aurora MySQL versions are available in the specified AWS Region\. 

```
5.6.10a
5.6.mysql_aurora.1.19.0
5.6.mysql_aurora.1.19.1
5.6.mysql_aurora.1.19.2
5.6.mysql_aurora.1.19.3
5.6.mysql_aurora.1.19.3.1
5.6.mysql_aurora.1.19.3.90
5.6.mysql_aurora.1.19.4
5.6.mysql_aurora.1.19.4.1
5.6.mysql_aurora.1.19.4.2
5.6.mysql_aurora.1.19.4.3
5.6.mysql_aurora.1.19.4.4
5.6.mysql_aurora.1.19.4.5
5.6.mysql_aurora.1.19.5
5.6.mysql_aurora.1.19.5.90
5.6.mysql_aurora.1.19.6
5.6.mysql_aurora.1.20.1
5.6.mysql_aurora.1.22.0
5.6.mysql_aurora.1.22.2
5.6.mysql_aurora.1.23.0

5.7.mysql_aurora.2.09.0
```

 After you start using parallel query with a cluster, you can monitor performance and remove obstacles to parallel query usage\. For those instructions, see [Performance Tuning for Parallel Query](#aurora-mysql-parallel-query-performance)\. 

## Creating a DB Cluster that Works with Parallel Query<a name="aurora-mysql-parallel-query-creating-cluster"></a>

 To create an Aurora MySQL cluster with parallel query, add new instances to it, or perform other administrative operations, you use the same AWS Management Console and AWS CLI techniques that you do with other Aurora MySQL clusters\. You can create a new cluster to work with parallel query\. You can also create a DB cluster to work with parallel query by restoring from a snapshot of a MySQL\-compatible Aurora DB cluster\. If you aren't familiar with the process for creating a new Aurora MySQL cluster, you can find background information and prerequisites in [Creating an Amazon Aurora DB Cluster](Aurora.CreateInstance.md)\. 

 However, certain options are different: 
+  When you choose an Aurora MySQL engine version, we recommend that you choose the latest engine that is compatible with MySQL 5\.7\. Currently, Aurora MySQL 2\.09 or higher, and certain Aurora MySQL versions that are compatible with MySQL 5\.6 support parallel query\. You have more flexibility to turn parallel query on and off, or use parallel query with existing clusters, if you use Aurora MySQL 1\.23 or 2\.09 and higher\. 
+  Only for Aurora MySQL before version 1\.23: When you create or restore the DB cluster, make sure to choose the **parallelquery** engine mode\. 

 Whether you create a new cluster or restore from a snapshot, you use the same techniques to add new DB instances that you do with other Aurora MySQL clusters\. 

### Creating a Parallel Query Cluster Using the Console<a name="aurora-mysql-parallel-query-creating-cluster-console"></a>

 You can create a new parallel query cluster with the console as described following\. 

**To create a parallel query cluster with the AWS Management Console**

1.  Follow the general AWS Management Console procedure in [Creating an Amazon Aurora DB Cluster](Aurora.CreateInstance.md)\. 

1.  On the **Select engine** screen, choose Aurora MySQL\.

    For **Engine version**, choose Aurora MySQL 2\.09 or higher, or Aurora MySQL 1\.23 or higher if practical\. With those versions, you have the fewest limitations on parallel query usage\. Those versions also have the most flexibility to turn parallel query on or off at any time\. 

    If it isn't practical to use a recent Aurora MySQL version for this cluster, choose **Show versions that support the parallel query feature**\. Doing so filters the **Version** menu to show only the specific Aurora MySQL versions that are compatible with parallel query\. 

1. \(For older versions only\) For **Capacity type**, choose **Provisioned with Aurora parallel query enabled**\. The AWS Management Console only displays this choice when you select an Aurora MySQL version lower than 1\.23\. For Aurora MySQL 1\.23 or 2\.09 and higher, you don't need to make any special choice to make the cluster compatible with parallel query\.

1. \(For recent versions only\) For **Additional configuration**, choose a parameter group that you created for **DB cluster parameter group**\. Using such a custom parameter group is required for Aurora MySQL 1\.23 or 2\.09 and higher\. In your DB cluster parameter group, specify the parameter settings `aurora_parallel_query=ON` and `aurora_disable_hash_join=OFF`\. Doing so turns on parallel query for the cluster, and enables the hash join optimization which works in combination with parallel query\. 

**To verify that a new cluster can use parallel query**

1. Create a cluster using the preceding technique\.

1. \(For recent versions only\) Check that the `aurora_parallel_query` configuration setting is true, and that the `aurora_disable_hash_join` setting is false\.

   ```
   mysql> select @@aurora_parallel_query;
   +-------------------------+
   | @@aurora_parallel_query |
   +-------------------------+
   |                       1 |
   +-------------------------+
   mysql> select @@aurora_disable_hash_join;
   +----------------------------+
   | @@aurora_disable_hash_join |
   +----------------------------+
   |                          0 |
   +----------------------------+
   ```

1. \(For older versions only\) Check that the `aurora_pq_supported` configuration setting is true\.

   ```
   mysql> select @@aurora_pq_supported;
   +-----------------------+
   | @@aurora_pq_supported |
   +-----------------------+
   |                     1 |
   +-----------------------+
   ```

1.  With some large tables and data\-intensive queries, check the query plans to confirm that some of your queries are using the parallel query optimization\. To do so, follow the procedure in [Verifying Which Statements Use Parallel Query](#aurora-mysql-parallel-query-verifying)\. 

### Creating a Parallel Query Cluster Using the CLI<a name="aurora-mysql-parallel-query-creating-cluster-cli"></a>

 You can create a new parallel query cluster with the CLI as described following\. 

**To create a parallel query cluster with the AWS CLI**

1.  \(Optional\) Check which Aurora MySQL versions are compatible with parallel query clusters\. To do so, use the `describe-db-engine-versions` command and check the value of the `SupportsParallelQuery` field\. For an example, see [Checking Aurora MySQL Version Compatibility for Parallel Query](#aurora-mysql-parallel-query-checking-compatibility)\. 

1.  \(Optional\) Create a custom DB cluster parameter group with the settings `aurora_parallel_query=ON` and `aurora_disable_hash_join=OFF`\. Use commands such as the following\. 

   ```
   aws rds create-db-cluster-parameter-group --db-parameter-group-family aurora-mysql5.7 --db-cluster-parameter-group-name pq-enabled-57-compatible
   aws rds modify-db-cluster-parameter-group --db-cluster-parameter-group-name pq-enabled-57-compatible \
     --parameters ParameterName=aurora_parallel_query,ParameterValue=ON,ApplyMethod=pending-reboot
   aws rds modify-db-cluster-parameter-group --db-cluster-parameter-group-name pq-enabled-57-compatible \
     --parameters ParameterName=aurora_disable_hash_join,ParameterValue=OFF,ApplyMethod=pending-reboot
   ```

    If you perform this step, specify the option `--db-cluster-parameter-group-name my_cluster_parameter_group` in the subsequent `create-db-cluster` statement\. Substitute the name of your own parameter group\. If you omit this step, you create the parameter group and associate it with the cluster later, as described in [Enabling and Disabling Parallel Query](#aurora-mysql-parallel-query-enabling)\. 

1.  Follow the general AWS CLI procedure in [Creating an Amazon Aurora DB Cluster](Aurora.CreateInstance.md)\. 

1.  Specify the following set of options: 
   +  For the `--engine` option, use `aurora` or `aurora-mysql`\. These values produce parallel query clusters that are compatible with MySQL 5\.6 or MySQL 5\.7 respectively\. 
   +  The value to use for the `--engine-mode` parameter depends on the engine version that you choose\. 

      For Aurora MySQL 1\.23 or higher, or 2\.09 or higher, specify `--engine-mode provisioned`\. You can also omit the `--engine-mode` parameter, because `provisioned` is the default\. In these versions, you can turn parallel query on or off for the default kind of Aurora MySQL clusters, instead of creating clusters dedicated to always using parallel query\. 

      Before Aurora MySQL 1\.23, for the `--engine-mode` option, use `parallelquery`\. The `--engine-mode` parameter applies to the `create-db-cluster` operation\. Then the engine mode of the cluster is used automatically by subsequent `create-db-instance` operations\. 
   +  For the `--db-cluster-parameter-group-name` option, specify the name of a DB cluster parameter group that you created and specified the parameter value `aurora_parallel_query=ON`\. If you omit this option, you can create the cluster with a default parameter group and later modify it to use such a custom parameter group\. 
   +  For the `--engine-version` option, use an Aurora MySQL version that's compatible with parallel query\. Use the procedure from [Planning for a Parallel Query Cluster](#aurora-mysql-parallel-query-planning) to get a list of versions if necessary\. If practical, use at least 1\.23\.0 or 2\.09\.0\. These versions and all higher ones contain substantial enhancements to parallel query\. 

      The following code example shows how\. Substitute your own value for each of the environment variables such as *$CLUSTER\_ID*\. 

     ```
     aws rds create-db-cluster --db-cluster-identifier $CLUSTER_ID--engine aurora-mysql --engine-version 5.7.mysql_aurora.2.09.0 \
       --master-username $MASTER_USER_ID --master-user-password $MASTER_USER_PW \
       --db-cluster-parameter-group-name $CUSTOM_CLUSTER_PARAM_GROUP
     
     aws rds create-db-cluster --db-cluster-identifier $CLUSTER_ID
       --engine aurora --engine-version 5.6.mysql_aurora.1.23.0 \
       --master-username $MASTER_USER_ID --master-user-password $MASTER_USER_PW \
       --db-cluster-parameter-group-name $CUSTOM_CLUSTER_PARAM_GROUP
     
     aws rds create-db-instance --db-instance-identifier ${INSTANCE_ID}-1 \
       --engine same_value_as_in_create_cluster_command \
       --db-cluster-identifier $CLUSTER_ID --db-instance-class $INSTANCE_CLASS
     ```

1.  Verify that a cluster you created or restored has the parallel query feature available\. 

    For Aurora MySQL 1\.23 and 2\.09 or higher: Check that the `aurora_parallel_query` configuration setting exists\. If this setting has the value 1, parallel query is ready for you to use\. If this setting has the value 0, set it to 1 before you can use parallel query\. Either way, the cluster is capable of performing parallel queries\. 

   ```
   mysql> select @@aurora_parallel_query;
   +------------------------+
   | @@aurora_parallel_query|
   +------------------------+
   |                      1 |
   +------------------------+
   ```

    Before Aurora MySQL 1\.23: Check that the `aurora_pq_supported` configuration setting is true\. 

   ```
   mysql> select @@aurora_pq_supported;
   +-----------------------+
   | @@aurora_pq_supported |
   +-----------------------+
   |                     1 |
   +-----------------------+
   ```

**To restore a snapshot to a parallel query cluster with the AWS CLI**

1.  Check which Aurora MySQL versions are compatible with parallel query clusters\. To do so, use the `describe-db-engine-versions` command and check the value of the `SupportsParallelQuery` field\. For an example, see [Checking Aurora MySQL Version Compatibility for Parallel Query](#aurora-mysql-parallel-query-checking-compatibility)\. Decide which version to use for the restored cluster\. If practical, choose Aurora MySQL 2\.09\.0 or higher for a MySQL 5\.7\-compatible cluster, or 1\.23\.0 or higher for a MySQL 5\.6\-compatible cluster\. 

1.  Locate a Aurora MySQL\-compatible cluster snapshot\. 

1.  Follow the general AWS CLI procedure in [Restoring from a DB Cluster Snapshot](USER_RestoreFromSnapshot.md)\. 

1.  The value to use for the `--engine-mode` parameter depends on the engine version that you choose\. 

    For Aurora MySQL 1\.23 or higher, or 2\.09 or higher, specify `--engine-mode provisioned`\. You can also omit the `--engine-mode` parameter, because `provisioned` is the default\. In these versions, you can turn parallel query on or off for your Aurora MySQL clusters, instead of creating clusters dedicated to always using parallel query\. 

    Before Aurora MySQL 1\.23, specify `--engine-mode parallelquery`\. The `--engine-mode` parameter applies to the `create-db-cluster` operation\. Then the engine mode of the cluster is used automatically by subsequent `create-db-instance` operations\. 

   ```
   aws rds restore-db-instance-from-db-snapshot \
     --db-instance-identifier mynewdbinstance \
     --db-snapshot-identifier mydbsnapshot \
     --engine-mode parallelquery
   ```

1.  Verify that a cluster you created or restored has the parallel query feature available\. Use the same verification procedure as in [Creating a Parallel Query Cluster Using the CLI](#aurora-mysql-parallel-query-creating-cluster-cli)\. 

## Enabling and Disabling Parallel Query<a name="aurora-mysql-parallel-query-enabling"></a>

**Note**  
 When parallel query is enabled, Aurora MySQL determines whether to use it at runtime for each query\. In the case of joins, unions, subqueries, and so on, Aurora MySQL determines whether to use parallel query at runtime for each query block\. For details, see [Verifying Which Statements Use Parallel Query](#aurora-mysql-parallel-query-verifying) and [How Parallel Query Works with SQL Constructs](#aurora-mysql-parallel-query-sql)\. 

### Aurora MySQL 1\.23 and 2\.09 or Higher<a name="aurora-mysql-parallel-query-enabling-modern"></a>

 In Aurora MySQL 1\.23 and 2\.09 or higher, you can enable and disable parallel query dynamically at both the global and session level for a DB instance by using the **aurora\_parallel\_query** option\. You can change the `aurora_parallel_query` setting in your DB cluster group to turn parallel query on or off by default\. 

```
mysql> select @@aurora_parallel_query;
+------------------------+
| @@aurora_parallel_query|
+------------------------+
|                      1 |
+------------------------+
```

 To toggle the `aurora_parallel_query` parameter at the session level, use the standard methods to change a client configuration setting\. For example, you can do so through the `mysql` command line or within a JDBC or ODBC application\. The command on the standard MySQL client is `set session aurora_pq = {'ON'/'OFF'}`\. You can also add the session\-level parameter to the JDBC configuration or within your application code to enable or disable parallel query dynamically\. 

 You can permanently change the setting for the `aurora_parallel_query` parameter, either for a specific DB instance or for your whole cluster\. If you specify the parameter value in a DB parameter group, that value only applies to specific DB instance in your cluster\. If you specify the parameter value in a DB cluster parameter group, all DB instances in the cluster inherit the same setting\. To toggle the `aurora_parallel_query` parameter, use the techniques for working with parameter groups, as described in [Working with DB Parameter Groups and DB Cluster Parameter Groups](USER_WorkingWithParamGroups.md)\. Follow these steps: 

1.  Create a custom cluster parameter group \(recommended\) or a custom DB parameter group\. 

1.  In this parameter group, update `parallel_query` to the value that you want\. 

1.  Depending on whether you created a DB cluster parameter group or a DB parameter group, attach the parameter group to your Aurora cluster or to the specific DB instances where you plan to use the parallel query feature\. 
**Tip**  
 Because `aurora_parallel_query` is a dynamic parameter, you don't need to restart your cluster after changing this setting\. 

 You can modify the parallel query parameter by using the [ModifyDBClusterParameterGroup](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBClusterParameterGroup.html) or [ModifyDBParameterGroup](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBParameterGroup.html) API operation or the AWS Management Console\. 

### Before Aurora MySQL 1\.23<a name="aurora-mysql-parallel-query-enabling-56"></a>

 For these older versions, you can enable and disable parallel query dynamically at both the global and session level for a DB instance by using the **aurora\_pq** option\. On clusters where the parallel query feature is available, the parameter is enabled by default\. 

```
mysql> select @@aurora_pq;
+-------------+
| @@aurora_pq |
+-------------+
|           1 |
+-------------+
```

 To toggle the `aurora_pq` parameter at the session level, for example through the `mysql` command line or within a JDBC or ODBC application, use the standard methods to change a client configuration setting\. For example, the command on the standard MySQL client is `set session aurora_pq = {'ON'/'OFF'}`\. You can also add the session\-level parameter to the JDBC configuration or within your application code to enable or disable parallel query dynamically\. 

 To toggle the `aurora_pq` parameter permanently, use the techniques for working with parameter groups, as described in [Working with DB Parameter Groups and DB Cluster Parameter Groups](USER_WorkingWithParamGroups.md)\. Follow these steps: 

1.  Create a custom cluster parameter group or DB instance parameter group\. We recommend using a cluster parameter group, so that all DB instance in the cluster inherit the same settings\. 

1.  In this parameter group, update `aurora_pq` to the value that you want\. 

1.  Associate the custom cluster parameter group with the Aurora cluster where you plan to use the parallel query feature\. Or, for a custom DB parameter group, associate it with one or more DB instances in the cluster\. 

1.  Restart all the DB instances of the cluster\. 

 You can modify the parallel query parameter by using the [ModifyDBClusterParameterGroup](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBClusterParameterGroup.html) or [ModifyDBParameterGroup](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBParameterGroup.html) API operation or the AWS Management Console\. 

**Note**  
 When parallel query is enabled, Aurora MySQL determines whether to use it at runtime for each query\. In the case of joins, unions, subqueries, and so on, Aurora MySQL determines whether to use parallel query at runtime for each query block\. For details, see [Verifying Which Statements Use Parallel Query](#aurora-mysql-parallel-query-verifying) and [How Parallel Query Works with SQL Constructs](#aurora-mysql-parallel-query-sql)\. 

### Enabling Hash Join for Parallel Query Clusters<a name="aurora-mysql-parallel-query-enabling-hash-join"></a>

 Parallel query is typically used for the kinds of resource\-intensive queries that benefit from the hash join optimization\. Thus, it's helpful to ensure that hash joins are enabled for clusters where you plan to use parallel query\. 
+  For Aurora MySQL 5\.6\-compatible clusters before version 1\.23, hash joins are always available in parallel query\-enabled clusters\. In this case, you don't need to take any action for the hash join feature\. If you upgrade such clusters in the future, you do need to enable hash joins at that time\. 
+  In Aurora MySQL 1\.23 or 2\.09 and higher, the parallel query and hash join settings are both turned off by default\. When you enable parallel query for such a cluster, enable hash joins also\. The simplest way to do so is to set the cluster configuration parameter `aurora_disable_hash_join=OFF`\. For information about how to enable hash joins and use them effectively, see [Working with Hash Joins in Aurora MySQL](AuroraMySQL.BestPractices.md#Aurora.BestPractices.HashJoin)\. 

### Enabling and Disabling Parallel Query Using the Console<a name="aurora-mysql-parallel-query-enabling-console"></a>

 You can enable or disable parallel query at the DB instance level or the DB cluster level by working with parameter groups\. 

**To enable or disable parallel query for a DB cluster with the AWS Management Console**

1.  Create a custom parameter group, as described in [Working with DB Parameter Groups and DB Cluster Parameter Groups](USER_WorkingWithParamGroups.md)\. 

1.  For Aurora MySQL 1\.23 and 2\.09 or higher: Update **aurora\_parallel\_query** to **1** \(enabled\) or **0** \(disabled\)\. On clusters where the parallel query feature is available, **aurora\_parallel\_query** is disabled by default\. 

    For Aurora MySQL before 1\.23: Update **aurora\_pq** to **1** \(enabled\) or **0** \(disabled\)\. On clusters where the parallel query feature is available, **aurora\_pq** is enabled by default\. 

1.  If you use a custom cluster parameter group, attach it to the Aurora DB cluster where you plan to use the parallel query feature\. If you use a custom DVB parameter group, attach it to one or more DB instances in the cluster\. We recommend using a cluster parameter group\. Doing so makes sure that all DB instances in the cluster have the same settings for parallel query and associated features such as hash join\. 

### Enabling and Disabling Parallel Query Using the CLI<a name="aurora-mysql-parallel-query-enabling-cli"></a>

 You can modify the parallel query parameter by using the `modify-db-cluster-parameter-group` or `modify-db-parameter-group` command\. Choose the appropriate command depending on whether you specify the value of `aurora_parallel_query` through a DB cluster parameter group or a DB parameter group\. 

**To enable or disable parallel query for a DB cluster with the CLI**
+  Modify the parallel query parameter by using the `modify-db-cluster-parameter-group` command\. Use a command such as the following\. Substitute the appropriate name for your own custom parameter group\. Substitute either `ON` or `OFF` for the `ParameterValue` portion of the `--parameters` option\. 

  ```
  # Aurora MySQL 1.23 or 2.09 and higher:
  $ aws rds modify-db-cluster-parameter-group --db-cluster-parameter-group-name cluster_param_group_name \
    --parameters ParameterName=aurora_parallel_query,ParameterValue=ON,ApplyMethod=pending-reboot
  {
      "DBClusterParameterGroupName": "cluster_param_group_name"
  }
  
  # Before Aurora MySQL 1.23:
  $ aws rds modify-db-cluster-parameter-group --db-cluster-parameter-group-name cluster_param_group_name \
    --parameters ParameterName=aurora_pq,ParameterValue=ON,ApplyMethod=pending-reboot
  {
      "DBClusterParameterGroupName": "cluster_param_group_name"
  }
  ```

 You can also enable or disable parallel query at the session level, for example through the `mysql` command line or within a JDBC or ODBC application\. To do so, use the standard methods to change a client configuration setting\. For example, the command on the standard MySQL client is `set session aurora_parallel_query = {'ON'/'OFF'}` for Aurora MySQL 1\.23 or 2\.09 and higher\. Before Aurora MySQL 1\.23, the command is `set session aurora_pq = {'ON'/'OFF'}`\. 

 You can also add the session\-level parameter to the JDBC configuration or within your application code to enable or disable parallel query dynamically\. 

## Upgrade Considerations for Parallel Query<a name="aurora-mysql-parallel-query-upgrade"></a>

 In Aurora MySQL 1\.23 or 2\.09 and higher, parallel query works for provisioned clusters and doesn't require the `parallelquery` engine mode parameter\. Thus, you don't need to create a new cluster or restore from an existing snapshot to use parallel query with these versions\. You can use the upgrade procedures described in [Database Upgrades and Patches for Amazon Aurora MySQL](AuroraMySQL.Updates.md#AuroraMySQL.Updates.Patching) to upgrade your cluster to such a version\. You can upgrade an older cluster regardless of whether it was a parallel query cluster or a provisioned cluster\. To reduce the number of choices in the **Engine version** menu, you can choose **Show versions that support the parallel query feature** to filter the entries in that menu\. Then choose Aurora MySQL 1\.23 or 2\.09 and higher\. 

 After you upgrade an earlier parallel query cluster to Aurora MySQL 1\.23 or 2\.09 and higher, you enable parallel query in the upgraded cluster\. Parallel query is turned off by default in these versions, and the procedure for enabling it is different\. The hash join optimization is also turned off by default and must be enabled separately\. Thus, make sure that you enable these settings again after the upgrade\. For instructions on doing so, see [Enabling and Disabling Parallel Query](#aurora-mysql-parallel-query-enabling) and [Enabling Hash Join for Parallel Query Clusters](#aurora-mysql-parallel-query-enabling-hash-join)\. 

 In particular, you enable parallel query by using the configuration parameters `aurora_parallel_query=ON` and `aurora_disable_hash_join=OFF` instead of `aurora_pq_supported` and `aurora_pq`\. The `aurora_pq_supported` and `aurora_pq` parameters are deprecated in the newer Aurora MySQL versions\. 

 In the upgraded cluster, the `EngineMode` attribute has the value `provisioned` instead of `parallelquery`\. To check whether parallel query is available for a specified engine version, now you check the value of the `SupportsParallelQuery` field in the output of the `describe-db-engine-versions` AWS CLI command\. In earlier Aurora MySQL versions, you checked for the presence of `parallelquery` in the `SupportedEngineModes` list\. 

 After you upgrade to Aurora MySQL 1\.23 or 2\.09 and higher, you can take advantage of the following features\. These features aren't available to parallel query clusters running older Aurora MySQL versions\. 
+  Performance Insights\. For more information, see [Using Amazon RDS Performance Insights](USER_PerfInsights.md)\. 
+  Backtracking\. For more information, see [Backtracking an Aurora DB Cluster](AuroraMySQL.Managing.Backtrack.md)\. 
+  Stopping and starting the cluster\. For more information, see [Stopping and Starting an Amazon Aurora DB Cluster](aurora-cluster-stop-start.md)\. 

## Performance Tuning for Parallel Query<a name="aurora-mysql-parallel-query-performance"></a>

 To manage the performance of a workload with parallel query, make sure that parallel query is used for the queries where this optimization helps the most\. 

 To do so, you can do the following: 
+  Make sure that your biggest tables are compatible with parallel query\. You might change table properties or recreate some tables so that queries for those tables can take advantage of the parallel query optimization\. To learn how, see [Creating Schema Objects to Take Advantage of Parallel Query](#aurora-mysql-parallel-query-tables)\. 
+  Monitor which queries use parallel query\. To learn how, see [Monitoring Parallel Query](#aurora-mysql-parallel-query-monitoring)\. 
+  Verify that parallel query is being used for the most data\-intensive and long\-running queries, and with the right level of concurrency for your workload\. To learn how, see [Verifying Which Statements Use Parallel Query](#aurora-mysql-parallel-query-verifying)\. 
+  Fine\-tune your SQL code to enable parallel query to apply to the queries that you expect\. To learn how, see [How Parallel Query Works with SQL Constructs](#aurora-mysql-parallel-query-sql)\. 

## Creating Schema Objects to Take Advantage of Parallel Query<a name="aurora-mysql-parallel-query-tables"></a>

 Before you create or modify tables that you plan to use for parallel query, make sure to familiarize yourself with the requirements described in [Prerequisites](#aurora-mysql-parallel-query-prereqs) and [Limitations](#aurora-mysql-parallel-query-limitations)\. 

 Because parallel query requires tables to use the `ROW_FORMAT=Compact` or `ROW_FORMAT=Dynamic` setting, check your Aurora configuration settings for any changes to the `INNODB_FILE_FORMAT` configuration option\. Issue the `SHOW TABLE STATUS` statement to confirm the row format for all the tables in a database\. 

 Parallel query currently requires tables to be nonpartitioned\. Thus, check your `CREATE TABLE` statements and `SHOW CREATE TABLE` output and remove any `PARTITION BY` clauses\. For existing partitioned tables, first copy the data into nonpartitioned tables with the same column definitions and indexes\. Then rename old and new tables so that the nonpartitioned table is used by existing queries and ETL workflows\. 

 Before changing your schema to enable parallel query to work with more tables, make sure to test\. Your tests should confirm if parallel query results in a net increase in performance for those tables\. Also, make sure that the schema requirements for parallel query are otherwise compatible with your goals\. 

 For example, before switching from `ROW_FORMAT=Compressed` to `ROW_FORMAT=Compact` or `ROW_FORMAT=Dynamic`, test the performance of workloads for the original and new tables\. Also, consider other potential effects such as increased data volume\. 

## Verifying Which Statements Use Parallel Query<a name="aurora-mysql-parallel-query-verifying"></a>

 In typical operation, you don't need to perform any special actions to take advantage of parallel query\. After a query meets the essential requirements for parallel query, the query optimizer automatically decides whether to use parallel query for each specific query\. 

 If you run experiments in a development or test environment, you might find that parallel query isn't used because your tables are too small in number of rows or overall data volume\. The data for the table might also be entirely in the buffer pool, especially for tables you created recently to perform experiments\. 

 As you monitor or tune cluster performance, make sure to decide whether parallel query is being used in the appropriate contexts\. You might adjust the database schema, settings, SQL queries, or even the cluster topology and application connection settings to take advantage of this feature\. 

 To check if a query is using parallel query, check the query plan \(also known as the "explain plan"\) by running the [EXPLAIN](https://dev.mysql.com/doc/refman/5.7/en/execution-plan-information.html) statement\. For examples of how SQL statements, clauses, and expressions affect `EXPLAIN` output for parallel query, see [How Parallel Query Works with SQL Constructs](#aurora-mysql-parallel-query-sql)\. 

 The following example demonstrates the difference between a traditional query plan and a parallel query plan\. This explain plan is from Query 3 from the TPC\-H benchmark\. Many of the sample queries throughout this section use the tables from the TPC\-H dataset\. You can get the table definitions, queries, and the `dbgen` program that generates sample data from [the TPC\-H website](http://www.tpc.org/tpch/)\. 

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

 By default, the query might have a plan like the following\. If you don't see hash join used in the query plan, make sure that optimization is enabled first\. 

```
+----+-------------+----------+------------+------+---------------+------+---------+------+----------+----------+----------------------------------------------------+
| id | select_type | table    | partitions | type | possible_keys | key  | key_len | ref  | rows     | filtered | Extra                                              |
+----+-------------+----------+------------+------+---------------+------+---------+------+----------+----------+----------------------------------------------------+
|  1 | SIMPLE      | customer | NULL       | ALL  | NULL          | NULL | NULL    | NULL |  1480234 |    10.00 | Using where; Using temporary; Using filesort       |
|  1 | SIMPLE      | orders   | NULL       | ALL  | NULL          | NULL | NULL    | NULL | 14875240 |     3.33 | Using where; Using join buffer (Block Nested Loop) |
|  1 | SIMPLE      | lineitem | NULL       | ALL  | NULL          | NULL | NULL    | NULL | 59270573 |     3.33 | Using where; Using join buffer (Block Nested Loop) |
+----+-------------+----------+------------+------+---------------+------+---------+------+----------+----------+----------------------------------------------------+
```

 You can enable hash join at the session level by issuing the following statement\. Afterwards, try the `EXPLAIN` statement again\. 

```
SET optimizer_switch='hash_join=on';
```

 For information about how to enable hash joins permanently and use them effectively, see [Working with Hash Joins in Aurora MySQL](AuroraMySQL.BestPractices.md#Aurora.BestPractices.HashJoin)\. 

 With hash join enabled but parallel query disabled, the query might have a plan like the following, which uses hash join but not parallel query\. 

```
+----+-------------+----------+...+-----------+-----------------------------------------------------------------+
| id | select_type | table    |...| rows      | Extra                                                           |
+----+-------------+----------+...+-----------+-----------------------------------------------------------------+
|  1 | SIMPLE      | customer |...|   5798330 | Using where; Using index; Using temporary; Using filesort       |
|  1 | SIMPLE      | orders   |...| 154545408 | Using where; Using join buffer (Hash Join Outer table orders)   |
|  1 | SIMPLE      | lineitem |...| 606119300 | Using where; Using join buffer (Hash Join Outer table lineitem) |
+----+-------------+----------+...+-----------+-----------------------------------------------------------------+
```

 After parallel query is enabled, two steps in this query plan can use the parallel query optimization, as shown under the `Extra` column in the `EXPLAIN` output\. The I/O\-intensive and CPU\-intensive processing for those steps is pushed down to the storage layer\. 

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

 Experiment with your workload to get a sense of whether individual SQL statements can take advantage of parallel query\. Then use the following monitoring techniques to help verify how often parallel query is used in real workloads over time\. For real workloads, extra factors such as concurrency limits apply\. 

## Monitoring Parallel Query<a name="aurora-mysql-parallel-query-monitoring"></a>

 In addition to the Amazon CloudWatch metrics described in [Monitoring Amazon Aurora DB Cluster Metrics](Aurora.Monitoring.md), Aurora provides other global status variables\. You can use these global status variables to help monitor parallel query execution\. They can give you insights into why the optimizer might use or not use parallel query in a given situation\. To access these variables, you can use the `[SHOW GLOBAL STATUS](https://dev.mysql.com/doc/refman/5.7/en/server-status-variables.html)` command\. You can also find these variables listed following\. 

 A parallel query session isn't necessarily a one\-to\-one mapping with the queries performed by the database\. For example, suppose that your query plan has two steps that use parallel query\. In that case, the query involves two parallel sessions and the counters for requests attempted and requests successful are incremented by two\. 

 When you experiment with parallel query by issuing `EXPLAIN` statements, expect to see increases in the counters designated as "not chosen" even though the queries aren't actually running\. When you work with parallel query in production, you can check if the "not chosen" counters are increasing faster than you expect\. At this point, you can adjust so that parallel query runs for the queries that you expect\. To do so, you can change your cluster settings, query mix, DB instances where parallel query is enabled, and so on\.

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
|   `Aurora_pq_request_not_chosen_column_geometry`   |   The number of parallel query requests that use the nonparallel query processing path because the table has columns with the `GEOMETRY` data type\.   | 
|   `Aurora_pq_request_not_chosen_column_lob`   |   The number of parallel query requests that use the nonparallel query processing path because the table has columns with a `LOB` data type, or `VARCHAR` columns that are stored externally due the declared length\.   | 
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

## How Parallel Query Works with SQL Constructs<a name="aurora-mysql-parallel-query-sql"></a>

 In the following section, you can find more detail about why particular SQL statements use or don't use parallel query\. This section also details how Aurora MySQL features interact with parallel query\. This information can help you diagnose performance issues for a cluster that uses parallel query or understand how parallel query applies for your particular workload\. 

 The decision to use parallel query relies on many factors that occur at the moment that the statement runs\. Thus, parallel query might be used for certain queries always, never, or only under certain conditions\. 

**Tip**  
 When you view these examples in HTML, you can use the **Copy** widget in the upper\-right corner of each code listing to copy the SQL code to try yourself\. Using the **Copy** widget avoids copying the extra characters around the `mysql>` prompt and `->` continuation lines\. 

**Topics**
+ [EXPLAIN statement](#aurora-mysql-parallel-query-sql-explain)
+ [WHERE Clause](#aurora-mysql-parallel-query-sql-where)
+ [Data Definition Language \(DDL\)](#aurora-mysql-parallel-query-sql-ddl)
+ [Column Data Types](#aurora-mysql-parallel-query-sql-datatypes)
+ [Partitioned Tables](#aurora-mysql-parallel-query-sql-partitioning)
+ [Function Calls in WHERE Clause](#aurora-mysql-parallel-query-sql-functions)
+ [Aggregate Functions, GROUP BY Clauses, and HAVING Clauses](#aurora-mysql-parallel-query-sql-aggregation)
+ [LIMIT Clause](#aurora-mysql-parallel-query-sql-limit)
+ [Comparison Operators](#aurora-mysql-parallel-query-sql-comparisons)
+ [Joins](#aurora-mysql-parallel-query-sql-joins)
+ [Subqueries](#aurora-mysql-parallel-query-sql-subqueries)
+ [UNION](#aurora-mysql-parallel-query-sql-union)
+ [Views](#aurora-mysql-parallel-query-sql-views)
+ [Data Manipulation Language \(DML\) Statements](#aurora-mysql-parallel-query-sql-dml)
+ [Transactions and Locking](#aurora-mysql-parallel-query-sql-transactions)
+ [B\-Tree Indexes](#aurora-mysql-parallel-query-sql-indexes)
+ [Full\-Text Search \(FTS\) Indexes](#aurora-mysql-parallel-query-sql-fts)
+ [Full\-Text Search \(FTS\) Indexes](#aurora-mysql-parallel-query-sql-virtual-column)
+ [Built\-In Caching Mechanisms](#aurora-mysql-parallel-query-sql-caching)
+ [MyISAM Temporary Tables](#aurora-mysql-parallel-query-sql-myisam)

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

### Data Definition Language \(DDL\)<a name="aurora-mysql-parallel-query-sql-ddl"></a>

 Parallel query is only available for tables for which no fast data definition language \(DDL\) operations are pending\. For information about fast DDL, see [Altering Tables in Amazon Aurora Using Fast DDL](AuroraMySQL.Managing.FastDDL.md)\. 

### Column Data Types<a name="aurora-mysql-parallel-query-sql-datatypes"></a>

 `TEXT`, `BLOB`, `JSON`, and `GEOMETRY` data types aren't supported with parallel query\. A query that refers to any columns of these types can't use parallel query\. 

 Variable\-length columns \(`VARCHAR` and `CHAR` data types\) are compatible with parallel query up to a maximum declared length of 768 bytes\. A query that refers to any columns of the types declared with a longer maximum length can't use parallel query\. For columns that use multibyte character sets, the byte limit takes into account the maximum number of bytes in the character set\. For example, for the character set `utf8mb4` \(which has a maximum character length of 4 bytes\), a `VARCHAR(192)` column is compatible with parallel query but a `VARCHAR(193)` column isn't\. 

### Partitioned Tables<a name="aurora-mysql-parallel-query-sql-partitioning"></a>

 Currently, partitioned tables aren't supported for parallel query\. You can use partitioned tables in parallel query clusters\. Queries against those tables use the nonparallel query processing path\. 

**Note**  
 A join, union, or other multipart query can partially use parallel query, even if some query blocks refer to partitioned tables\. The query blocks that refer only to nonpartitioned tables can use the parallel query optimization\. 

### Function Calls in WHERE Clause<a name="aurora-mysql-parallel-query-sql-functions"></a>

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

### LIMIT Clause<a name="aurora-mysql-parallel-query-sql-limit"></a>

 Currently, parallel query isn't used for any query block that includes a `LIMIT` clause\. Parallel query might still be used for earlier query phases with `GROUP` by, `ORDER BY`, or joins\. 

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

 Each phase of join processing is evaluated to check if it is eligible for parallel query\. If more than one phase can use parallel query, these phases are performed in sequence\. Thus, each join query counts as a single parallel query session in terms of concurrency limits\. 

 For example, when a join query includes `WHERE` predicates to filter the rows from one of the joined tables, that filtering option can use parallel query\. As another example, suppose that a join query uses the hash join mechanism, for example to join a big table with a small table\. In this case, the table scan to produce the Bloom filter data structure might be able to use parallel query\. 

**Note**  
 Parallel query is typically used for the kinds of resource\-intensive queries that benefit from the hash join optimization\. Before Aurora MySQL 1\.23, hash joins are always available in parallel query\-enabled clusters\. In Aurora MySQL 1\.23 and 2\.09 or higher, the parallel query and hash join settings are both turned off by default\. When you enable parallel query for such a cluster, enable hash joins also\. For information about how to enable hash joins and use them effectively, see [Working with Hash Joins in Aurora MySQL](AuroraMySQL.BestPractices.md#Aurora.BestPractices.HashJoin)\. 

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

 You can use all the isolation levels on the Aurora primary instance\. 

 On Aurora reader DB instances, parallel query applies to statements performed under the `REPEATABLE READ` isolation level\. Aurora MySQL versions 1\.23 and 2\.09 or higher can also use the `READ COMMITTED` isolation level on reader DB instances\. `REPEATABLE READ` is the default isolation level for Aurora reader DB instances\. To use `READ COMMITTED` isolation level on reader DB instances requires setting the `aurora_read_replica_read_committed` configuration option\. 

 For more information about Aurora isolation levels, see [Aurora MySQL Isolation Levels](AuroraMySQL.Reference.md#AuroraMySQL.Reference.IsolationLevels)\. 

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

### B\-Tree Indexes<a name="aurora-mysql-parallel-query-sql-indexes"></a>

 The statistics gathered by the `ANALYZE TABLE` statement help the optimizer to decide when to use parallel query or index lookups, based on the characteristics of the data for each column\. Keep statistics current by running `ANALYZE TABLE` after DML operations that make substantial changes to the data within a table\. 

 If index lookups can perform a query efficiently without a data\-intensive scan, Aurora might use index lookups\. Doing so avoids the overhead of parallel query processing\. There are also concurrency limits on the number of parallel queries that can run simultaneously on any Aurora DB cluster\. Make sure to use best practices for indexing your tables, so that your most frequent and most highly concurrent queries use index lookups\. 

### Full\-Text Search \(FTS\) Indexes<a name="aurora-mysql-parallel-query-sql-fts"></a>

 Currently, parallel query isn't used for tables that contain a full\-text search index, regardless of whether the query refers to such indexed columns or uses the `MATCH` operator\. 

### Full\-Text Search \(FTS\) Indexes<a name="aurora-mysql-parallel-query-sql-virtual-column"></a>

 Currently, parallel query isn't used for tables that contain a virtual column, regardless of whether the query refers to any virtual columns\. 

### Built\-In Caching Mechanisms<a name="aurora-mysql-parallel-query-sql-caching"></a>

 Aurora includes built\-in caching mechanisms, namely the buffer pool and the query cache\. The Aurora optimizer chooses between these caching mechanisms and parallel query depending on which one is most effective for a particular query\. 

 When a parallel query filters rows and transforms and extracts column values, data is transmitted back to the head node as tuples rather than as data pages\. Therefore, running a parallel query doesn't add any pages to the buffer pool, or evict pages that are already in the buffer pool\. 

 Aurora checks the number of pages of table data that are present in the buffer pool, and what proportion of the table data that number represents\. Aurora uses that information to determine whether it is more efficient to use parallel query \(and bypass the data in the buffer pool\)\. Alternatively, Aurora might use the nonparallel query processing path, which uses data cached in the buffer pool\. Which pages are cached and how data\-intensive queries affect caching and eviction depends on configuration settings related to the buffer pool\. Therefore, it can be hard to predict whether any particular query uses parallel query, because the choice depends on the ever\-changing data within the buffer pool\. 

 Also, Aurora imposes concurrency limits on parallel queries\. Because not every query uses parallel query, tables that are accessed by multiple queries simultaneously typically have a substantial portion of their data in the buffer pool\. Therefore, Aurora often doesn't choose these tables for parallel queries\. 

 When you run a sequence of nonparallel queries on the same table, the first query might be slow due to the data not being in the buffer pool\. Then the second and subsequent queries are much faster because the buffer pool is now "warmed up"\. Parallel queries typically show consistent performance from the very first query against the table\. When conducting performance tests, benchmark the nonparallel queries with both a cold and a warm buffer pool\. In some cases, the results with a warm buffer pool can compare well to parallel query times\. In these cases, consider factors such as the frequency of queries against that table\. Also consider whether it is worthwhile to keep the data for that table in the buffer pool\. 

 The query cache avoids rerunning a query when an identical query is submitted and the underlying table data hasn't changed\. Queries optimized by parallel query feature can go into the query cache, effectively making them instantaneous when run again\. 

**Note**  
 When conducting performance comparisons, the query cache can produce artificially low timing numbers\. Therefore, in benchmark\-like situations, you can use the `sql_no_cache` hint\. This hint prevents the result from being served from the query cache, even if the same query had been run previously\. The hint comes immediately after the `SELECT` statement in a query\. Many parallel query examples in this topic include this hint, to make query times comparable between versions of the query that are enabled with parallel query and not\.   
 Make sure that you remove this hint from your source when you move to production use of parallel query\. 

### MyISAM Temporary Tables<a name="aurora-mysql-parallel-query-sql-myisam"></a>

The parallel query optimization only applies to InnoDB tables\. Because Aurora MySQL uses MyISAM behind the scenes for temporary tables, internal query phases involving temporary tables never use parallel query\. These query phases are indicated by `Using temporary` in the `EXPLAIN` output\. 