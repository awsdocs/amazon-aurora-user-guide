# Altering tables in Amazon Aurora using fast DDL<a name="AuroraMySQL.Managing.FastDDL"></a>

In MySQL, many data definition language \(DDL\) operations have a significant performance impact\. Performance impacts occur even with recent online DDL improvements\.

For example, suppose that you use an `ALTER TABLE` operation to add a column to a table\. Depending on the algorithm specified for the operation, this operation can involve the following:
+ Creating a full copy of the table
+ Creating a temporary table to process concurrent data manipulation language \(DML\) operations
+ Rebuilding all indexes for the table
+ Applying table locks while applying concurrent DML changes
+ Slowing concurrent DML throughput

In Amazon Aurora, you can use fast DDL to run an `ALTER TABLE` operation in place, nearly instantaneously\. The operation completes without requiring the table to be copied and without having a material impact on other DML statements\. Because the operation doesn't consume temporary storage for a table copy, it makes DDL statements practical even for large tables on small instance classes\.

**Important**  
Currently, Aurora lab mode must be enabled to use fast DDL for Aurora MySQL\. We don't recommended using fast DDL for production DB clusters\. For information about enabling Aurora lab mode, see [Amazon Aurora MySQL lab mode](AuroraMySQL.Updates.LabMode.md)\.

## Fast DDL limitations<a name="AuroraMySQL.FastDDL.Limitations"></a>

Currently, fast DDL has the following limitations:
+ Fast DDL only supports adding nullable columns, without default values, to the end of an existing table\.
+ Fast DDL doesn't work for partitioned tables\.
+ Fast DDL doesn't work for InnoDB tables that use the REDUNDANT row format\.
+  Fast DDL doesn't work for tables with full\-text search indexes\. 
+ If the maximum possible record size for the DDL operation is too large, fast DDL is not used\. A record size is too large if it is greater than half the page size\. The maximum size of a record is computed by adding the maximum sizes of all columns\. For variable sized columns, according to InnoDB standards, extern bytes are not included for computation\.
**Note**  
The maximum record size check was added in Aurora 1\.15\.

## Fast DDL syntax<a name="AuroraMySQL.FastDDL.Syntax"></a>

```
ALTER TABLE tbl_name ADD COLUMN col_name column_definition
```

This statement takes the following options:
+ **`tbl_name` — **The name of the table to be modified\.
+ **`col_name` — **The name of the column to be added\.
+ **`col_definition` — **The definition of the column to be added\.
**Note**  
You must specify a nullable column definition without a default value\. Otherwise, fast DDL isn't used\.

## Fast DDL examples<a name="AuroraMySQL.FastDDL.Examples"></a>

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