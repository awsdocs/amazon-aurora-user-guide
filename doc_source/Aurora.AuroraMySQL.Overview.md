# Overview of Amazon Aurora MySQL<a name="Aurora.AuroraMySQL.Overview"></a>

The following sections provide an overview of Amazon Aurora MySQL\.

**Topics**
+ [Amazon Aurora MySQL Performance Enhancements](#Aurora.AuroraMySQL.Performance)
+ [Amazon Aurora MySQL and Spatial Data](#Aurora.AuroraMySQL.Spatial)
+ [Comparison of Aurora MySQL 5\.6 and Aurora MySQL 5\.7](#Aurora.AuroraMySQL.CompareReleases)
+ [Comparison of Aurora MySQL 5\.7 and MySQL 5\.7](#Aurora.AuroraMySQL.CompareMySQL57)

## Amazon Aurora MySQL Performance Enhancements<a name="Aurora.AuroraMySQL.Performance"></a>

Amazon Aurora includes performance enhancements to support the diverse needs of high\-end commercial databases\.

### Fast Insert<a name="Aurora.AuroraMySQL.Performance.FastInsert"></a>

Fast insert accelerates parallel inserts sorted by primary key and applies specifically to `LOAD DATA` and `INSERT INTO ... SELECT ...` statements\. Fast insert caches the position of a cursor in an index traversal while executing the statement\. This avoids unnecessarily traversing the index again\.

You can monitor the following metrics to determine the effectiveness of fast insert for your DB cluster:
+ `aurora_fast_insert_cache_hits`: A counter that is incremented when the cached cursor is successfully retrieved and verified\. 
+ `aurora_fast_insert_cache_misses`: A counter that is incremented when the cached cursor is no longer valid and Aurora performs a normal index traversal\.

You can retrieve the current value of the fast insert metrics using the following command:

```
mysql> show global status like 'Aurora_fast_insert%';                
```

You will get output similar to the following:

```
+---------------------------------+-----------+
| Variable_name                   | Value     |
+---------------------------------+-----------+
| Aurora_fast_insert_cache_hits   | 3598300   |
| Aurora_fast_insert_cache_misses | 436401336 |
+---------------------------------+-----------+
```

## Amazon Aurora MySQL and Spatial Data<a name="Aurora.AuroraMySQL.Spatial"></a>

 The following list summarizes the main Aurora MySQL spatial features and explains how they correspond to spatial features in MySQL\. 
+  Aurora MySQL 1\.x supports the same [spatial data types](https://dev.mysql.com/doc/refman/5.6/en/spatial-types.html) and [spatial relation functions](https://dev.mysql.com/doc/refman/5.6/en/spatial-relation-functions-object-shapes.html) as MySQL 5\.6\. 
+  Aurora MySQL 2\.x supports the same [spatial data types](https://dev.mysql.com/doc/refman/5.7/en/spatial-types.html) and [spatial relation functions](https://dev.mysql.com/doc/refman/5.7/en/spatial-relation-functions-object-shapes.html) as MySQL 5\.7\. 
+  Aurora MySQL 1\.x and 2\.x both support spatial indexing on InnoDB tables\. Spatial indexing improves query performance on large datasets for queries on spatial data\. In MySQL, spatial indexing for InnoDB tables isn't available in MySQL 5\.6, but is available in MySQL 5\.7\. Both Aurora MySQL 1\.x and 2\.x use a different spatial indexing strategy than MySQL for high performance with spatial queries\. The Aurora spatial index implementation uses a space\-filling curve on a B\-tree, which is intended to provide higher performance for spatial range scans than an R\-tree\. 

The following data definition language \(DDL\) statements are supported for creating indexes on columns that use spatial data types\.

### CREATE TABLE<a name="Aurora.AuroraMySQL.Spatial.create_table"></a>

 You can use the `SPATIAL INDEX` keywords in a `CREATE TABLE` statement to add a spatial index to a column in a new table\. Following is an example\.

```
CREATE TABLE test (shape POLYGON NOT NULL, SPATIAL INDEX(shape));
```

### ALTER TABLE<a name="Aurora.AuroraMySQL.Spatial.alter_table"></a>

You can use the `SPATIAL INDEX` keywords in an `ALTER TABLE` statement to add a spatial index to a column in an existing table\. Following is an example\.

```
ALTER TABLE test ADD SPATIAL INDEX(shape);
```

### CREATE INDEX<a name="Aurora.AuroraMySQL.Spatial.create_index"></a>

You can use the `SPATIAL` keyword in a `CREATE INDEX` statement to add a spatial index to a column in an existing table\. Following is an example\.

```
CREATE SPATIAL INDEX shape_index ON test (shape);
```

## Comparison of Aurora MySQL 5\.6 and Aurora MySQL 5\.7<a name="Aurora.AuroraMySQL.CompareReleases"></a>

The following Amazon Aurora MySQL features are supported in Aurora MySQL 5\.6, but these features are currently not supported in Aurora MySQL 5\.7\.
+ Asynchronous key prefetch \(AKP\)\. For more information, see [Working with Asynchronous Key Prefetch in Amazon Aurora](AuroraMySQL.BestPractices.md#Aurora.BestPractices.AKP)\.
+ Hash joins\. For more information, see [Working with Hash Joins in Aurora MySQL](AuroraMySQL.BestPractices.md#Aurora.BestPractices.HashJoin)\.
+ Native functions for synchronously invoking AWS Lambda functions\. You can asynchronously invoke AWS Lambda functions from Aurora MySQL 5\.7\. For more information, see [Invoking a Lambda Function with an Aurora MySQL Native Function](AuroraMySQL.Integrating.Lambda.md#AuroraMySQL.Integrating.NativeLambda)\.
+ Scan batching\. For more information, see [Aurora MySQL Database Engine Updates 2017\-12\-11](AuroraMySQL.Updates.20171211.md)\.
+ Migrating data from MySQL using an Amazon S3 bucket\. For more information, see [Migrating Data from MySQL by Using an Amazon S3 Bucket](AuroraMySQL.Migrating.ExtMySQL.md#AuroraMySQL.Migrating.ExtMySQL.S3)\.

Currently, Aurora MySQL 5\.7 does not support features added in Aurora MySQL version 1\.16 and later\. For information about Aurora MySQL version 1\.16, see [Aurora MySQL Database Engine Updates 2017\-12\-11](AuroraMySQL.Updates.20171211.md)\.

The performance schema isn't available for early release of Aurora MySQL 5\.7\. Upgrade to Aurora 2\.03 or higher for performance schema support\.

## Comparison of Aurora MySQL 5\.7 and MySQL 5\.7<a name="Aurora.AuroraMySQL.CompareMySQL57"></a>

The following features are supported in MySQL 5\.7\.12 but are currently not supported in Aurora MySQL 5\.7:
+ Group replication plugin
+ Increased page size
+ InnoDB buffer pool loading at startup
+ InnoDB full\-text parser plugin
+ Multisource replication
+ Online buffer pool resizing
+ Password validation plugin
+ Query rewrite plugins
+ Replication filtering
+ The `CREATE TABLESPACE` SQL statement
+ X Protocol

For more information about these features, see the [MySQL 5\.7 documentation](https://dev.mysql.com/doc/refman/5.7/en/)\.