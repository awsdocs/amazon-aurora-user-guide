# Altering tables in Amazon Aurora using fast DDL<a name="AuroraMySQL.Managing.FastDDL"></a>

Amazon Aurora includes optimizations to run an `ALTER TABLE` operation in place, nearly instantaneously\. The operation completes without requiring the table to be copied and without having a material impact on other DML statements\. Because the operation doesn't consume temporary storage for a table copy, it makes DDL statements practical even for large tables on small instance classes\.

 Aurora MySQL version 3 is compatible with the MySQL 8\.0 feature called instant DDL\. Aurora MySQL versions 1 and 2 use a different implementation called fast DDL\. 

**Topics**
+ [Instant DDL \(Aurora MySQL version 3\)](#AuroraMySQL.mysql80-instant-ddl)
+ [Fast DDL \(Aurora MySQL version 1 and 2\)](#AuroraMySQL.Managing.FastDDL-v2)

## Instant DDL \(Aurora MySQL version 3\)<a name="AuroraMySQL.mysql80-instant-ddl"></a><a name="instant_ddl"></a>

 The optimization performed by Aurora MySQL version 3 to improve the efficiency of some DDL operations is called instant DDL\. 

 Aurora MySQL version 3 is compatible with the instant DDL from community MySQL 8\.0\. You perform an instant DDL operation by using the clause `ALGORITHM=INSTANT` with the `ALTER TABLE` statement\. For syntax and usage details about instant DDL, see [ALTER TABLE](https://dev.mysql.com/doc/refman/8.0/en/alter-table.html) and [Online DDL Operations](https://dev.mysql.com/doc/refman/8.0/en/innodb-online-ddl-operations.html) in the MySQL documentation\. 

 The following examples demonstrate the instant DDL feature\. The `ALTER TABLE` statements add columns and change default column values\. The examples include both regular and virtual columns, and both regular and partitioned tables\. At each step, you can see the results by issuing `SHOW CREATE TABLE` and `DESCRIBE` statements\. 

```
mysql> CREATE TABLE t1 (a INT, b INT, KEY(b)) PARTITION BY KEY(b) PARTITIONS 6;
Query OK, 0 rows affected (0.02 sec)

mysql> ALTER TABLE t1 RENAME TO t2, ALGORITHM = INSTANT;
Query OK, 0 rows affected (0.01 sec)

mysql> ALTER TABLE t2 ALTER COLUMN b SET DEFAULT 100, ALGORITHM = INSTANT;
Query OK, 0 rows affected (0.00 sec)

mysql> ALTER TABLE t2 ALTER COLUMN b DROP DEFAULT, ALGORITHM = INSTANT;
Query OK, 0 rows affected (0.01 sec)

mysql> ALTER TABLE t2 ADD COLUMN c ENUM('a', 'b', 'c'), ALGORITHM = INSTANT;
Query OK, 0 rows affected (0.01 sec)

mysql> ALTER TABLE t2 MODIFY COLUMN c ENUM('a', 'b', 'c', 'd', 'e'), ALGORITHM = INSTANT;
Query OK, 0 rows affected (0.01 sec)

mysql> ALTER TABLE t2 ADD COLUMN (d INT GENERATED ALWAYS AS (a + 1) VIRTUAL), ALGORITHM = INSTANT;
Query OK, 0 rows affected (0.02 sec)

mysql> ALTER TABLE t2 ALTER COLUMN a SET DEFAULT 20,
    ->   ALTER COLUMN b SET DEFAULT 200, ALGORITHM = INSTANT;
Query OK, 0 rows affected (0.01 sec)

mysql> CREATE TABLE t2 (a INT, b INT) PARTITION BY LIST(a)(
    ->   PARTITION mypart1 VALUES IN (1,3,5),
    ->   PARTITION MyPart2 VALUES IN (2,4,6)
    -> );
Query OK, 0 rows affected (0.03 sec)

mysql> ALTER TABLE t3 ALTER COLUMN a SET DEFAULT 20, ALTER COLUMN b SET DEFAULT 200, ALGORITHM = INSTANT;
Query OK, 0 rows affected (0.01 sec)

mysql> CREATE TABLE t4 (a INT, b INT) PARTITION BY RANGE(a)
    ->   (PARTITION p0 VALUES LESS THAN(100), PARTITION p1 VALUES LESS THAN(1000),
    ->   PARTITION p2 VALUES LESS THAN MAXVALUE);
Query OK, 0 rows affected (0.05 sec)

mysql> ALTER TABLE t4 ALTER COLUMN a SET DEFAULT 20,
    ->   ALTER COLUMN b SET DEFAULT 200, ALGORITHM = INSTANT;
Query OK, 0 rows affected (0.01 sec)

/* Sub-partitioning example */
mysql> CREATE TABLE ts (id INT, purchased DATE, a INT, b INT)
    ->   PARTITION BY RANGE( YEAR(purchased) )
    ->     SUBPARTITION BY HASH( TO_DAYS(purchased) )
    ->     SUBPARTITIONS 2 (
    ->       PARTITION p0 VALUES LESS THAN (1990),
    ->       PARTITION p1 VALUES LESS THAN (2000),
    ->       PARTITION p2 VALUES LESS THAN MAXVALUE
    ->    );
Query OK, 0 rows affected (0.10 sec)

mysql> ALTER TABLE ts ALTER COLUMN a SET DEFAULT 20,
    ->   ALTER COLUMN b SET DEFAULT 200, ALGORITHM = INSTANT;
Query OK, 0 rows affected (0.01 sec)
```

## Fast DDL \(Aurora MySQL version 1 and 2\)<a name="AuroraMySQL.Managing.FastDDL-v2"></a>

 <a name="fast_ddl"></a>

In MySQL, many data definition language \(DDL\) operations have a significant performance impact\.

For example, suppose that you use an `ALTER TABLE` operation to add a column to a table\. Depending on the algorithm specified for the operation, this operation can involve the following:
+ Creating a full copy of the table
+ Creating a temporary table to process concurrent data manipulation language \(DML\) operations
+ Rebuilding all indexes for the table
+ Applying table locks while applying concurrent DML changes
+ Slowing concurrent DML throughput

 The optimization performed by Aurora MySQL version 1 and 2 to improve the efficiency of some DDL operations is called fast DDL\. 

 In Aurora MySQL version 3, Aurora uses the MySQL 8\.0 feature called instant DDL\. Aurora MySQL versions 1 and 2 use a different implementation called fast DDL\. 

**Important**  
Currently, Aurora lab mode must be enabled to use fast DDL for Aurora MySQL\. We don't recommend using fast DDL for production DB clusters\. For information about enabling Aurora lab mode, see [Amazon Aurora MySQL lab mode](AuroraMySQL.Updates.LabMode.md)\.

### Fast DDL limitations<a name="AuroraMySQL.FastDDL.Limitations"></a>

Currently, fast DDL has the following limitations:
+ Fast DDL only supports adding nullable columns, without default values, to the end of an existing table\.
+ Fast DDL doesn't work for partitioned tables\.
+ Fast DDL doesn't work for InnoDB tables that use the REDUNDANT row format\.
+  Fast DDL doesn't work for tables with full\-text search indexes\. 
+ If the maximum possible record size for the DDL operation is too large, fast DDL is not used\. A record size is too large if it is greater than half the page size\. The maximum size of a record is computed by adding the maximum sizes of all columns\. For variable sized columns, according to InnoDB standards, extern bytes are not included for computation\.
**Note**  
The maximum record size check was added in Aurora 1\.15\.

### Fast DDL syntax<a name="AuroraMySQL.FastDDL.Syntax"></a>

```
ALTER TABLE tbl_name ADD COLUMN col_name column_definition
```

This statement takes the following options:
+ **`tbl_name` — **The name of the table to be modified\.
+ **`col_name` — **The name of the column to be added\.
+ **`col_definition` — **The definition of the column to be added\.
**Note**  
You must specify a nullable column definition without a default value\. Otherwise, fast DDL isn't used\.

### Fast DDL examples<a name="AuroraMySQL.FastDDL.Examples"></a>

 The following examples demonstrate the speedup from fast DDL operations\. The first SQL example runs `ALTER TABLE` statements on a large table without using fast DDL\. This operation takes substantial time\. A CLI example shows how to enable fast DDL for the cluster\. Then another SQL example runs the same `ALTER TABLE` statements on an identical table\. With fast DDL enabled, the operation is very fast\. 

 This example uses the `ORDERS` table from the TPC\-H benchmark, containing 150 million rows\. This cluster intentionally uses a relatively small instance class, to demonstrate how long `ALTER TABLE` statements can take when you can't use fast DDL\. The example creates a clone of the original table containing identical data\. Checking the `aurora_lab_mode` setting confirms that the cluster can't use fast DDL, because lab mode isn't enabled\. Then `ALTER TABLE ADD COLUMN` statements take substantial time to add new columns at the end of the table\. 

```
mysql> create table orders_regular_ddl like orders;
Query OK, 0 rows affected (0.06 sec)

mysql> insert into orders_regular_ddl select * from orders;
Query OK, 150000000 rows affected (1 hour 1 min 25.46 sec)

mysql> select @@aurora_lab_mode;
+-------------------+
| @@aurora_lab_mode |
+-------------------+
|                 0 |
+-------------------+

mysql> ALTER TABLE orders_regular_ddl ADD COLUMN o_refunded boolean;
Query OK, 0 rows affected (40 min 31.41 sec)

mysql> ALTER TABLE orders_regular_ddl ADD COLUMN o_coverletter varchar(512);
Query OK, 0 rows affected (40 min 44.45 sec)
```

 This example does the same preparation of a large table as the previous example\. However, you can't simply enable lab mode within an interactive SQL session\. That setting must be enabled in a custom parameter group\. Doing so requires switching out of the `mysql` session and running some AWS CLI commands or using the AWS Management Console\. 

```
mysql> create table orders_fast_ddl like orders;
Query OK, 0 rows affected (0.02 sec)

mysql> insert into orders_fast_ddl select * from orders;
Query OK, 150000000 rows affected (58 min 3.25 sec)

mysql> set aurora_lab_mode=1;
ERROR 1238 (HY000): Variable 'aurora_lab_mode' is a read only variable
```

 Enabling lab mode for the cluster requires some work with a parameter group\. This AWS CLI example uses a cluster parameter group, to ensure that all DB instances in the cluster use the same value for the lab mode setting\.  

```
$ aws rds create-db-cluster-parameter-group \
  --db-parameter-group-family aurora5.6 \
    --db-cluster-parameter-group-name lab-mode-enabled-56 --description 'TBD'
$ aws rds describe-db-cluster-parameters \
  --db-cluster-parameter-group-name lab-mode-enabled-56 \
    --query '*[*].[ParameterName,ParameterValue]' \
      --output text | grep aurora_lab_mode
aurora_lab_mode 0
$ aws rds modify-db-cluster-parameter-group \
  --db-cluster-parameter-group-name lab-mode-enabled-56 \
    --parameters ParameterName=aurora_lab_mode,ParameterValue=1,ApplyMethod=pending-reboot
{
    "DBClusterParameterGroupName": "lab-mode-enabled-56"
}

# Assign the custom parameter group to the cluster that's going to use fast DDL.
$ aws rds modify-db-cluster --db-cluster-identifier tpch100g \
  --db-cluster-parameter-group-name lab-mode-enabled-56
{
  "DBClusterIdentifier": "tpch100g",
  "DBClusterParameterGroup": "lab-mode-enabled-56",
  "Engine": "aurora",
  "EngineVersion": "5.6.mysql_aurora.1.22.2",
  "Status": "available"
}

# Reboot the primary instance for the cluster tpch100g:
$ aws rds reboot-db-instance --db-instance-identifier instance-2020-12-22-5208
{
  "DBInstanceIdentifier": "instance-2020-12-22-5208",
  "DBInstanceStatus": "rebooting"
}

$ aws rds describe-db-clusters --db-cluster-identifier tpch100g \
  --query '*[].[DBClusterParameterGroup]' --output text
lab-mode-enabled-56

$ aws rds describe-db-cluster-parameters \
  --db-cluster-parameter-group-name lab-mode-enabled-56 \
    --query '*[*].{ParameterName:ParameterName,ParameterValue:ParameterValue}' \
      --output text | grep aurora_lab_mode
aurora_lab_mode 1
```

 The following example shows the remaining steps after the parameter group change takes effect\. It tests the `aurora_lab_mode` setting to make sure that the cluster can use fast DDL\. Then it runs `ALTER TABLE` statements to add columns to the end of another large table\. This time, the statements finish very quickly\. 

```
mysql> select @@aurora_lab_mode;
+-------------------+
| @@aurora_lab_mode |
+-------------------+
|                 1 |
+-------------------+

mysql> ALTER TABLE orders_fast_ddl ADD COLUMN o_refunded boolean;
Query OK, 0 rows affected (1.51 sec)

mysql> ALTER TABLE orders_fast_ddl ADD COLUMN o_coverletter varchar(512);
Query OK, 0 rows affected (0.40 sec)
```