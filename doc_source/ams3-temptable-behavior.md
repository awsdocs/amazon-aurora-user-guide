# New temporary table behavior in Aurora MySQL version 3<a name="ams3-temptable-behavior"></a>

Aurora MySQL version 3 handles temporary tables differently from earlier Aurora MySQL versions\. This new behavior is inherited from MySQL 8\.0 Community Edition\. There are two types of temporary tables that can be created with Aurora MySQL version 3:
+ Internal \(or *implicit*\) temporary tables – Created by the Aurora MySQL engine to handle operations such as sorting aggregation, derived tables, or common table expressions \(CTEs\)\.
+ User\-created \(or *explicit*\) temporary tables – Created by the Aurora MySQL engine when you use the `CREATE TEMPORARY TABLE` statement\.

There are additional considerations for both internal and user\-created temporary tables on Aurora reader DB instances\. We discuss these changes in the following sections\.

**Topics**
+ [Storage engine for internal \(implicit\) temporary tables](#ams3-temptable-behavior-engine)
+ [Mitigating fullness issues for internal temporary tables on Aurora Replicas](#ams3-temptable-behavior-mitigate)
+ [User\-created \(explicit\) temporary tables on reader DB instances](#ams3-temptable-behavior.user)
+ [Temporary table creation errors and mitigation](#ams3-temptable-behavior.errors)

## Storage engine for internal \(implicit\) temporary tables<a name="ams3-temptable-behavior-engine"></a>

When generating intermediate result sets, Aurora MySQL initially attempts to write to in\-memory temporary tables\. If this is unsuccessful, because of either incompatible data types or configured limits, the temporary table is converted to an on\-disk temporary table rather than being held in memory\. More information on this can be found in the [Internal Temporary Table Use in MySQL](https://dev.mysql.com/doc/refman/8.0/en/internal-temporary-tables.html) in the MySQL documentation\.

In Aurora MySQL version 3, the way internal temporary tables work is different from earlier Aurora MySQL versions\. Instead of choosing between the InnoDB and MyISAM storage engines for such temporary tables, now you choose between the `TempTable` and InnoDB storage engines\.

With the `TempTable` storage engine, you can make an additional choice for how to handle certain data\. The data affected overflows the memory pool that holds all the internal temporary tables for the DB instance\.

Those choices can influence the performance for queries that generate high volumes of temporary data, for example while performing aggregations such as `GROUP BY` on large tables\.

**Tip**  
If your workload includes queries that generate internal temporary tables, confirm how your application performs with this change by running benchmarks and monitoring performance\-related metrics\.   
In some cases, the amount of temporary data fits within the `TempTable` memory pool or only overflows the memory pool by a small amount\. In these cases, we recommend using the `TempTable` setting for internal temporary tables and memory\-mapped files to hold any overflow data\. This setting is the default\.

The `TempTable` storage engine is the default\. `TempTable` uses a common memory pool for all temporary tables that use this engine, instead of a maximum memory limit per table\. The size of this memory pool is specified by the [temptable\_max\_ram](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_temptable_max_ram) parameter\. It defaults to 1 GiB on DB instances with 16 or more GiB of memory, and 16 MB on DB instances with less than 16 GiB of memory\. The size of the memory pool influences session\-level memory consumption\.

If you use the `TempTable` storage engine and the temporary data exceeds the size of the memory pool, Aurora MySQL stores the overflow data using a secondary mechanism\.

You can set the [temptable\_max\_mmap](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_temptable_max_mmap) parameter to specify if the data overflows to memory\-mapped temporary files or to InnoDB internal temporary tables on disk\. The different data formats and overflow criteria of these overflow mechanisms can affect query performance\. They do so by influencing the amount of data written to disk and the demand on disk storage throughput\.

 Aurora MySQL stores the overflow data differently depending on your choice of data overflow destination and whether the query runs on a writer or reader DB instance:
+ On the writer instance, data that overflows to InnoDB internal temporary tables is stored in the Aurora cluster volume\.
+ On the writer instance, data that overflows to memory\-mapped temporary files resides on local storage on the Aurora MySQL version 3 instance\.
+ On reader instances, overflow data always resides on memory\-mapped temporary files on local storage\. That's because read\-only instances can't store any data on the Aurora cluster volume\.

**Note**  
The configuration parameters related to internal temporary tables apply differently to the writer and reader instances in your cluster\. For reader instances, Aurora MySQL always uses the `TempTable` storage engine and a value of 1 for `temptable_use_mmap`\. The size for `temptable_max_mmap` defaults to 1 GiB, for both writer and reader instances, regardless of the DB instance memory size\. You can adjust this value the same way as on the writer instance, except that you can't specify a value of zero for `temptable_max_mmap` on reader instances\.

## Mitigating fullness issues for internal temporary tables on Aurora Replicas<a name="ams3-temptable-behavior-mitigate"></a>

To avoid size limitation issues for temporary tables, set the `temptable_max_ram` and `temptable_max_mmap` parameters to a combined value that can fit the requirements of your workload\.

Be careful when setting the value of the `temptable_max_ram` parameter\. Setting the value too high reduces the available memory on the database instance, which can cause an out\-of\-memory condition\. Monitor the average freeable memory on the DB instance, and then determine an appropriate value for `temptable_max_ram` so that you will still have a reasonable amount of free memory left on the instance\. For more information, see [Freeable memory issues in Amazon Aurora](CHAP_Troubleshooting.md#Troubleshooting.FreeableMemory)\.

It is also important to monitor the size of the local storage and the temporary table space consumption\. For more information on monitoring local storage on an instance, see the AWS Knowledge Center article [What is stored in Aurora MySQL\-compatible local storage, and how can I troubleshoot local storage issues?](https://aws.amazon.com/premiumsupport/knowledge-center/aurora-mysql-local-storage/)\.

**Example 1**  
You know that your temporary tables grow to a cumulative size of 20 GiB\. You want to set in\-memory temporary tables to 2 GiB and to grow to a maximum of 20 GiB on disk\.  
Set `temptable_max_ram` to **2,147,483,648** and `temptable_max_mmap` to **21,474,836,480**\. These values are in bytes\.  
These parameter settings make sure that your temporary tables can grow to a cumulative total of 22 GiB\.

**Example 2**  
Your current instance size is 16xlarge or larger\. You don't know the total size of the temporary tables that you might need\. You want to be able to use up to 4 GiB in memory and up to the maximum available storage size on disk\.  
Set `temptable_max_ram` to **4,294,967,296** and `temptable_max_mmap` to **1,099,511,627,776**\. These values are in bytes\.  
Here you're setting `temptable_max_mmap` to 1 TiB, which is less than the maximum local storage of 1\.2 TiB on a 16xlarge Aurora DB instance\.  
On a smaller instance size, adjust the value of `temptable_max_mmap` so that it doesn't fill up the available local storage\. For example, a 2xlarge instance has only 160 GiB of local storage available\. Hence, we recommend setting the value to less than 160 GiB\. For more information on the available local storage for DB instance sizes, see [Temporary storage limits for Aurora MySQLTemporary storage limits](AuroraMySQL.Managing.Performance.md#AuroraMySQL.Managing.TempStorage)\.

## User\-created \(explicit\) temporary tables on reader DB instances<a name="ams3-temptable-behavior.user"></a>

You can create explicit temporary tables using the `TEMPORARY` keyword in your `CREATE TABLE` statement\. Explicit temporary tables are supported on the writer DB instance in an Aurora DB cluster\. You can also use explicit temporary tables on reader DB instances, but the tables can't enforce the use of the InnoDB storage engine\.

To avoid errors while creating explicit temporary tables on Aurora reader DB instances, make sure that all `CREATE TEMPORARY TABLE` statements on reader DB instances are run in either of the following ways:
+ Without specifying the `ENGINE=InnoDB` clause
+ With the SQL mode `NO_ENGINE_SUBSTITUTION=OFF`

## Temporary table creation errors and mitigation<a name="ams3-temptable-behavior.errors"></a>

The error that you receive is different depending on whether you use a plain `CREATE TEMPORARY TABLE` statement or the variation `CREATE TEMPORARY TABLE AS SELECT`\. The following examples show the different kinds of errors\.

This temporary table behavior only applies to read\-only instances\. This first example confirms that's the kind of instance the session is connected to\.

```
mysql> select @@innodb_read_only;
+--------------------+
| @@innodb_read_only |
+--------------------+
|                  1 |
+--------------------+
```

For plain `CREATE TEMPORARY TABLE` statements, the statement fails when the `NO_ENGINE_SUBSTITUTION` SQL mode is turned on\. When `NO_ENGINE_SUBSTITUTION` is turned off \(default\), the appropriate engine substitution is made, and the temporary table creation succeeds\.

```
mysql> set sql_mode = 'NO_ENGINE_SUBSTITUTION';

mysql>  CREATE TEMPORARY TABLE tt2 (id int) ENGINE=InnoDB;
ERROR 3161 (HY000): Storage engine InnoDB is disabled (Table creation is disallowed).
mysql> SET sql_mode = '';

mysql> CREATE TEMPORARY TABLE tt4 (id int) ENGINE=InnoDB;

mysql> SHOW CREATE TABLE tt4\G
*************************** 1. row ***************************
       Table: tt4
Create Table: CREATE TEMPORARY TABLE `tt4` (
  `id` int DEFAULT NULL
) ENGINE=MyISAM DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
```

For `CREATE TEMPORARY TABLE AS SELECT` statements, the statement fails whether or not the `NO_ENGINE_SUBSTITUTION` SQL mode is turned on\. MySQL Community Edition doesn't support storage engine substitution with `CREATE TABLE AS SELECT` or `CREATE TEMPORARY TABLE AS SELECT` statements\. For those statements, remove the `ENGINE=InnoDB` clause from your SQL code\.

```
mysql> set sql_mode = 'NO_ENGINE_SUBSTITUTION';

mysql> CREATE TEMPORARY TABLE tt1 ENGINE=InnoDB AS SELECT * FROM t1;
ERROR 3161 (HY000): Storage engine InnoDB is disabled (Table creation is disallowed).

mysql> SET sql_mode = '';

mysql> CREATE TEMPORARY TABLE tt3 ENGINE=InnoDB AS SELECT * FROM t1;
ERROR 1874 (HY000): InnoDB is in read only mode.
```

For more information about the storage aspects and performance implications of temporary tables in Aurora MySQL version 3, see the blog post [Use the TempTable storage engine on Amazon RDS for MySQL and Amazon Aurora MySQL](https://aws.amazon.com/blogs/database/use-the-temptable-storage-engine-on-amazon-rds-for-mysql-and-amazon-aurora-mysql/)\.