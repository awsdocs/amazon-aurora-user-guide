# Single\-master replication with Amazon Aurora MySQL<a name="AuroraMySQL.Replication"></a><a name="replication"></a>

 The Aurora MySQL replication features are key to the high availability and performance of your cluster\. Aurora makes it easy to create or resize clusters with up to 15 Aurora Replicas\. 

 All the replicas work from the same underlying data\. If some database instances go offline, others remain available to continue processing queries or to take over as the writer if needed\. Aurora automatically spreads your read\-only connections across multiple database instances, helping an Aurora cluster to support query\-intensive workloads\. 

 Following, you can find information about how Aurora MySQL replication works and how to fine\-tune replication settings for best availability and performance\. 

**Note**  
 Following, you can learn about replication features for Aurora clusters using single\-master replication\. This kind of cluster is the default for Aurora\. For information about Aurora multi\-master clusters, see [Working with Aurora multi\-master clusters](aurora-multi-master.md)\. 

**Topics**
+ [Using Aurora Replicas](#AuroraMySQL.Replication.Replicas)
+ [Replication options for Amazon Aurora MySQL](#AuroraMySQL.Replication.Options)
+ [Performance considerations for Amazon Aurora MySQL replication](#AuroraMySQL.Replication.Performance)
+ [Zero\-downtime restart \(ZDR\) for Amazon Aurora MySQL](#AuroraMySQL.Replication.Availability)
+ [Configuring replication filters with Aurora MySQL](#AuroraMySQL.Replication.Filters)
+ [Monitoring Amazon Aurora MySQL replication](#AuroraMySQL.Replication.Monitoring)
+ [Replicating Amazon Aurora MySQL DB clusters across AWS Regions](AuroraMySQL.Replication.CrossRegion.md)
+ [Replication between Aurora and MySQL or between Aurora and another Aurora DB cluster \(binary log replication\)](AuroraMySQL.Replication.MySQL.md)
+ [Using GTID\-based replication for Amazon Aurora MySQL](mysql-replication-gtid.md)

## Using Aurora Replicas<a name="AuroraMySQL.Replication.Replicas"></a>

 Aurora Replicas are independent endpoints in an Aurora DB cluster, best used for scaling read operations and increasing availability\. Up to 15 Aurora Replicas can be distributed across the Availability Zones that a DB cluster spans within an AWS Region\. Although the DB cluster volume is made up of multiple copies of the data for the DB cluster, the data in the cluster volume is represented as a single, logical volume to the primary instance and to Aurora Replicas in the DB cluster\. For more information about Aurora Replicas, see [Aurora Replicas](Aurora.Replication.md#Aurora.Replication.Replicas)\. 

 Aurora Replicas work well for read scaling because they are fully dedicated to read operations on your cluster volume\. Write operations are managed by the primary instance\. Because the cluster volume is shared among all instances in your Aurora MySQL DB cluster, no additional work is required to replicate a copy of the data for each Aurora Replica\. In contrast, MySQL read replicas must replay, on a single thread, all write operations from the source DB instance to their local data store\. This limitation can affect the ability of MySQL read replicas to support large volumes of read traffic\. 

 With Aurora MySQL, when an Aurora Replica is deleted, its instance endpoint is removed immediately, and the Aurora Replica is removed from the reader endpoint\. If there are statements running on the Aurora Replica that is being deleted, there is a three minute grace period\. Existing statements can finish gracefully during the grace period\. When the grace period ends, the Aurora Replica is shut down and deleted\. 

**Important**  
 Aurora Replicas for Aurora MySQL always use the `REPEATABLE READ` default transaction isolation level for operations on InnoDB tables\. You can use the `SET TRANSACTION ISOLATION LEVEL` command to change the transaction level only for the primary instance of an Aurora MySQL DB cluster\. This restriction avoids user\-level locks on Aurora Replicas, and allows Aurora Replicas to scale to support thousands of active user connections while still keeping replica lag to a minimum\. 

**Note**  
 DDL statements that run on the primary instance might interrupt database connections on the associated Aurora Replicas\. If an Aurora Replica connection is actively using a database object, such as a table, and that object is modified on the primary instance using a DDL statement, the Aurora Replica connection is interrupted\. 

**Note**  
 The China \(Ningxia\) Region does not support cross\-Region read replicas\. 

## Replication options for Amazon Aurora MySQL<a name="AuroraMySQL.Replication.Options"></a>

 You can set up replication between any of the following options: 
+  Two Aurora MySQL DB clusters in different AWS Regions, by creating a cross\-Region read replica of an Aurora MySQL DB cluster\. 

   For more information, see [Replicating Amazon Aurora MySQL DB clusters across AWS Regions](AuroraMySQL.Replication.CrossRegion.md)\. 
+  Two Aurora MySQL DB clusters in the same AWS Region, by using MySQL binary log \(binlog\) replication\. 

   For more information, see [Replication between Aurora and MySQL or between Aurora and another Aurora DB cluster \(binary log replication\)](AuroraMySQL.Replication.MySQL.md)\. 
+  An RDS for MySQL DB instance as the source and an Aurora MySQL DB cluster, by creating an Aurora read replica of an RDS for MySQL DB instance\. 

   You can use this approach to bring existing and ongoing data changes into Aurora MySQL during migration to Aurora\. For more information, see [Migrating data from a MySQL DB instance to an Amazon Aurora MySQL DB cluster by using a DB snapshot](AuroraMySQL.Migrating.RDSMySQL.md)\. 

   You can also use this approach to increase the scalability of read queries for your data\. You do so by querying the data using one or more DB instances within a read\-only Aurora MySQL cluster\. For more information, see [Using Amazon Aurora to scale reads for your MySQL database](AuroraMySQL.Replication.MySQL.md#AuroraMySQL.Replication.ReadScaling)\. 
+  An Aurora MySQL DB cluster in one AWS Region and up to five Aurora read\-only Aurora MySQL DB clusters in different Regions, by creating an Aurora global database\. 

   You can use an Aurora global database to support applications with a world\-wide footprint\. The primary Aurora MySQL DB cluster has a Writer instance and up to 15 Aurora Replicas\. The read\-only secondary Aurora MySQL DB clusters can each be made up of as many as 16 Aurora Replicas\. For more information, see [Using Amazon Aurora global databases](aurora-global-database.md)\. 

**Note**  
 Rebooting the primary instance of an Amazon Aurora DB cluster also automatically reboots the Aurora Replicas for that DB cluster, to re\-establish an entry point that guarantees read/write consistency across the DB cluster\. 

## Performance considerations for Amazon Aurora MySQL replication<a name="AuroraMySQL.Replication.Performance"></a>

 The following features help you to fine\-tune the performance of Aurora MySQL replication\. 

 Starting in Aurora MySQL 1\.17\.4, the replica log compression feature automatically reduces network bandwidth for replication messages\. Because each message is transmitted to all Aurora Replicas, the benefits are greater for larger clusters\. This feature involves some CPU overhead on the writer node to perform the compression\. Thus, the feature is only available on the `8xlarge` and `16xlarge` instance classes, which have high CPU capacity\. It is enabled by default on these instance classes\. You can control this feature by turning off the `aurora_enable_replica_log_compression` parameter\. For example, you might turn off replica log compression for larger instance classes if your writer node is near its maximum CPU capacity\. 

 Starting in Aurora MySQL 1\.17\.4, the binlog filtering feature automatically reduces network bandwidth for replication messages\. Because the Aurora Replicas don't use the binlog information that is included in the replication messages, that data is omitted from the messages sent to those nodes\. You control this feature by changing the `aurora_enable_repl_bin_log_filtering` parameter\. This parameter is on by default\. Because this optimization is intended to be transparent, you might turn off this setting only during diagnosis or troubleshooting for issues related to replication\. For example, you can do so to match the behavior of an older Aurora MySQL cluster where this feature was not available\. 

## Zero\-downtime restart \(ZDR\) for Amazon Aurora MySQL<a name="AuroraMySQL.Replication.Availability"></a><a name="zdr"></a>

 The zero\-downtime restart \(ZDR\) feature can preserve some or all of the active connections to DB instances during certain kinds of restarts\. ZDR applies to restarts that Aurora performs automatically to resolve error conditions, for example when a replica begins to lag too far behind the source\. 

**Important**  
 The ZDR mechanism operates on a best\-effort basis\. The Aurora MySQL versions, instance classes, error conditions, compatible SQL operations, and other factors that determine where ZDR applies are subject to change at any time\. 

 In Aurora MySQL 1\.\* versions where ZDR is available, you turn on this feature by turning on the `aurora_enable_zdr` parameter in the cluster parameter group\. ZDR for Aurora MySQL 2\.\* requires version 2\.10 and higher\. ZDR is available in all minor versions of Aurora MySQL 3\.\*\. In Aurora MySQL version 2 and 3, the ZDR mechanism is turned on by default and Aurora doesn't use the `aurora_enable_zdr` parameter\. 

 Aurora reports on the **Events** page activities related to zero\-downtime restart\. Aurora records an event when it attempts a restart using the ZDR mechanism\. This event states why Aurora performs the restart\. Then Aurora records another event when the restart finishes\. This final event reports how long the process took, and how many connections were preserved or dropped during the restart\. You can consult the database error log to see more details about what happened during the restart\. 

 Although connections remain intact following a successful ZDR operation, some variables and features are reinitialized\. The following kinds of information aren't preserved through a restart caused by zero\-downtime restart: 
+  Global variables\. Aurora restores session variables, but it doesn't restore global variables after the restart\. 
+  Status variables\. In particular, the uptime value reported by the engine status is reset\. 
+  `LAST_INSERT_ID`\. 
+  In\-memory `auto_increment` state for tables\. The in\-memory auto\-increment state is reinitialized\. For more information about auto\-increment values, see [MySQL Reference Manual](https://dev.mysql.com/doc/refman/8.0/en/innodb-auto-increment-handling.html#innodb-auto-increment-initialization)\. 
+  Diagnostic information from `INFORMATION_SCHEMA` and `PERFORMANCE_SCHEMA` tables\. This diagnostic information also appears in the output of commands such as `SHOW PROFILE` and `SHOW PROFILES`\.  

 The following table shows the versions, instance roles, instance classes, and other circumstances that determine whether Aurora can use the ZDR mechanism when restarting DB instances in your cluster\. 


|  Aurora MySQL version  |  Does ZDR apply to the writer?  |  Does ZDR apply to readers?  |  Notes  | 
| --- | --- | --- | --- | 
|   Aurora MySQL version 1\.\*, 1\.17\.3 and lower   |   No   |   No   |   ZDR isn't available for these versions\.   | 
|   Aurora MySQL version 1\.\*, 1\.17\.4 and higher   |   No   |   Yes   |   In these Aurora MySQL versions, the following conditions apply to the ZDR mechanism:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Replication.html)  | 
|   Aurora MySQL version 2\.\*, before 2\.10\.0   |   No   |   No   |   ZDR isn't available for these versions\. The `aurora_enable_zdr` parameter isn't available in the default cluster parameter group for Aurora MySQL version 2\.   | 
|   Aurora MySQL version 2\.\*, 2\.10\.0 and higher   |   Yes   |   Yes   |   The ZDR mechanism is always enabled\.   In these Aurora MySQL versions, the following conditions apply to the ZDR mechanism:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Replication.html)  | 
|   Aurora MySQL version 3\.\*   |   Yes   |   Yes   |   The ZDR mechanism is always enabled\.   The same conditions apply as in Aurora MySQL version 2\.10\. ZDR applies to all instance classes\.   | 

## Configuring replication filters with Aurora MySQL<a name="AuroraMySQL.Replication.Filters"></a>

You can use replication filters to specify which databases and tables are replicated with a read replica\. Replication filters can include databases and tables in replication or exclude them from replication\.

The following are some use cases for replication filters:
+ To reduce the size of a read replica\. With replication filtering, you can exclude the databases and tables that aren't needed on the read replica\.
+ To exclude databases and tables from read replicas for security reasons\.
+ To replicate different databases and tables for specific use cases at different read replicas\. For example, you might use specific read replicas for analytics or sharding\.
+ For a DB cluster that has read replicas in different AWS Regions, to replicate different databases or tables in different AWS Regions\.
+ To specify which databases and tables are replicated with an Aurora MySQL DB cluster that is configured as a replica in an inbound replication topology\. For more information about this configuration, see [Replication between Aurora and MySQL or between Aurora and another Aurora DB cluster \(binary log replication\)](AuroraMySQL.Replication.MySQL.md)\.

**Topics**
+ [Setting replication filtering parameters for Aurora MySQL](#AuroraMySQL.Replication.Filters.Configuring)
+ [Replication filtering limitations for Aurora MySQL](#AuroraMySQL.Replication.Filters.Limitations)
+ [Replication filtering examples for Aurora MySQL](#AuroraMySQL.Replication.Filters.Examples)
+ [Viewing the replication filters for a read replica](#AuroraMySQL.Replication.Filters.Viewing)

### Setting replication filtering parameters for Aurora MySQL<a name="AuroraMySQL.Replication.Filters.Configuring"></a>

To configure replication filters, set the following replication filtering parameters on the Aurora read replica:
+ `binlog-do-db` – Replicate changes to the specified binary logs\. When you set this parameter for a read replica, only the binary logs specified in the parameter are replicated\.
+ `binlog-ignore-db` – Don't replicate changes to the specified binary logs\. When the `binlog-do-db` parameter is set for a read replica, this parameter isn't evaluated\.
+ `replicate-do-db` – Replicate changes to the specified databases\. When you set this parameter for a read replica, only the databases specified in the parameter are replicated\.
+ `replicate-ignore-db` – Don't replicate changes to the specified databases\. When the `replicate-do-db` parameter is set for a read replica, this parameter isn't evaluated\.
+ `replicate-do-table` – Replicate changes to the specified tables\. When you set this parameter for a read replica, only the tables specified in the parameter are replicated\. Also, when the `replicate-do-db` or `replicate-ignore-db` parameter is set, make sure to include the database that includes the specified tables in replication with the read replica\.
+ `replicate-ignore-table` – Don't replicate changes to the specified tables\. When the `replicate-do-table` parameter is set for a read replica, this parameter isn't evaluated\.
+ `replicate-wild-do-table` – Replicate tables based on the specified database and table name patterns\. The `%` and `_` wildcard characters are supported\. When the `replicate-do-db` or `replicate-ignore-db` parameter is set, make sure to include the database that includes the specified tables in replication with the read replica\.
+ `replicate-wild-ignore-table` – Don't replicate tables based on the specified database and table name patterns\. The `%` and `_` wildcard characters are supported\. When the `replicate-do-table` or `replicate-wild-do-table` parameter is set for a read replica, this parameter isn't evaluated\.

The parameters are evaluated in the order that they are listed\. For more information about how these parameters work, see the MySQL documentation:
+ For general information, see [ Replica Server Options and Variables](https://dev.mysql.com/doc/refman/8.0/en/replication-options-replica.html)\.
+ For information about how database replication filtering parameters are evaluated, see [ Evaluation of Database\-Level Replication and Binary Logging Options](https://dev.mysql.com/doc/refman/8.0/en/replication-rules-db-options.html)\.
+ For information about how table replication filtering parameters are evaluated, see [ Evaluation of Table\-Level Replication Options](https://dev.mysql.com/doc/refman/8.0/en/replication-rules-table-options.html)\.

By default, each of these parameters has an empty value\. On each read replica, you can use these parameters to set, change, and delete replication filters\. When you set one of these parameters, separate each filter from others with a comma\.

You can use the `%` and `_` wildcard characters in the `replicate-wild-do-table` and `replicate-wild-ignore-table` parameters\. The `%` wildcard matches any number of characters, and the `_` wildcard matches only one character\. 

The binary logging format of the source DB instance is important for replication because it determines the record of data changes\. The setting of the `binlog_format` parameter determines whether the replication is row\-based or statement\-based\. For more information, see [Configuring Aurora MySQL binary logging](USER_LogAccess.MySQL.BinaryFormat.md)\.

**Note**  
All data definition language \(DDL\) statements are replicated as statements, regardless of the `binlog_format` setting on the source DB instance\. 

### Replication filtering limitations for Aurora MySQL<a name="AuroraMySQL.Replication.Filters.Limitations"></a>

The following limitations apply to replication filtering for Aurora MySQL:
+ Replication filters are supported only for Aurora MySQL version 3\.
+ Each replication filtering parameter has a 2,000\-character limit\.
+ Commas aren't supported in replication filters\.
+ Replication filtering doesn't support XA transactions\.

  For more information, see [ Restrictions on XA Transactions](https://dev.mysql.com/doc/refman/8.0/en/xa-restrictions.html) in the MySQL documentation\.

### Replication filtering examples for Aurora MySQL<a name="AuroraMySQL.Replication.Filters.Examples"></a>

To configure replication filtering for a read replica, modify the replication filtering parameters in the DB cluster parameter group associated with the read replica\.

**Note**  
You can't modify a default DB cluster parameter group\. If the read replica is using a default parameter group, create a new parameter group and associate it with the read replica\. For more information on DB cluster parameter groups, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\.

You can set parameters in a DB cluster parameter group using the AWS Management Console, AWS CLI, or RDS API\. For information about setting parameters, see [Modifying parameters in a DB parameter group](USER_WorkingWithDBInstanceParamGroups.md#USER_WorkingWithParamGroups.Modifying)\. When you set parameters in a DB cluster parameter group, all of the DB clusters associated with the parameter group use the parameter settings\. If you set the replication filtering parameters in a DB cluster parameter group, make sure that the parameter group is associated only with read replica clusters\. Leave the replication filtering parameters empty for source DB instances\.

The following examples set the parameters using the AWS CLI\. These examples set `ApplyMethod` to `immediate` so that the parameter changes occur immediately after the CLI command completes\. If you want a pending change to be applied after the read replica is rebooted, set `ApplyMethod` to `pending-reboot`\. 

The following examples set replication filters:
+ [Including databases in replication](#rep-filter-in-dbs-ams)
+ [Including tables in replication](#rep-filter-in-tables-ams)
+ [Including tables in replication with wildcard characters](#rep-filter-in-tables-wildcards-ams)
+ [Excluding databases from replication](#rep-filter-ex-dbs-ams)
+ [Excluding tables from replication](#rep-filter-ex-tables-ams)
+ [Excluding tables from replication using wildcard characters](#rep-filter-ex-tables-wildcards-ams)<a name="rep-filter-in-dbs-ams"></a>

**Example Including databases in replication**  
The following example includes the `mydb1` and `mydb2` databases in replication\.  
For Linux, macOS, or Unix:  

```
aws rds modify-db-cluster-parameter-group \
  --db-cluster-parameter-group-name myparametergroup \
  --parameters "ParameterName=replicate-do-db,ParameterValue='mydb1,mydb2',ApplyMethod=immediate"
```
For Windows:  

```
aws rds modify-db-cluster-parameter-group ^
  --db-cluster-parameter-group-name myparametergroup ^
  --parameters "ParameterName=replicate-do-db,ParameterValue='mydb1,mydb2',ApplyMethod=immediate"
```<a name="rep-filter-in-tables-ams"></a>

**Example Including tables in replication**  
The following example includes the `table1` and `table2` tables in database `mydb1` in replication\.  
For Linux, macOS, or Unix:  

```
aws rds modify-db-cluster-parameter-group \
  --db-cluster-parameter-group-name myparametergroup \
  --parameters "ParameterName=replicate-do-table,ParameterValue='mydb1.table1,mydb1.table2',ApplyMethod=immediate"
```
For Windows:  

```
aws rds modify-db-cluster-parameter-group ^
  --db-cluster-parameter-group-name myparametergroup ^
  --parameters "ParameterName=replicate-do-table,ParameterValue='mydb1.table1,mydb1.table2',ApplyMethod=immediate"
```<a name="rep-filter-in-tables-wildcards-ams"></a>

**Example Including tables in replication using wildcard characters**  
The following example includes tables with names that begin with `order` and `return` in database `mydb` in replication\.  
For Linux, macOS, or Unix:  

```
aws rds modify-db-cluster-parameter-group \
  --db-cluster-parameter-group-name myparametergroup \
  --parameters "ParameterName=replicate-wild-do-table,ParameterValue='mydb.order%,mydb.return%',ApplyMethod=immediate"
```
For Windows:  

```
aws rds modify-db-cluster-parameter-group ^
  --db-cluster-parameter-group-name myparametergroup ^
  --parameters "ParameterName=replicate-wild-do-table,ParameterValue='mydb.order%,mydb.return%',ApplyMethod=immediate"
```<a name="rep-filter-ex-dbs-ams"></a>

**Example Excluding databases from replication**  
The following example excludes the `mydb5` and `mydb6` databases from replication\.  
For Linux, macOS, or Unix:  

```
aws rds modify-db-cluster-parameter-group \
  --db-cluster-parameter-group-name myparametergroup \
  --parameters "ParameterName=replicate-ignore-db,ParameterValue='mydb5,mydb6',ApplyMethod=immediate"
```
For Windows:  

```
aws rds modify-db-cluster-parameter-group ^
  --db-cluster-parameter-group-name myparametergroup ^
  --parameters "ParameterName=replicate-ignore-db,ParameterValue='mydb5,mydb6,ApplyMethod=immediate"
```<a name="rep-filter-ex-tables-ams"></a>

**Example Excluding tables from replication**  
The following example excludes tables `table1` in database `mydb5` and `table2` in database `mydb6` from replication\.  
For Linux, macOS, or Unix:  

```
aws rds modify-db-cluster-parameter-group \
  --db-cluster-parameter-group-name myparametergroup \
  --parameters "ParameterName=replicate-ignore-table,ParameterValue='mydb5.table1,mydb6.table2',ApplyMethod=immediate"
```
For Windows:  

```
aws rds modify-db-cluster-parameter-group ^
  --db-cluster-parameter-group-name myparametergroup ^
  --parameters "ParameterName=replicate-ignore-table,ParameterValue='mydb5.table1,mydb6.table2',ApplyMethod=immediate"
```<a name="rep-filter-ex-tables-wildcards-ams"></a>

**Example Excluding tables from replication using wildcard characters**  
The following example excludes tables with names that begin with `order` and `return` in database `mydb7` from replication\.  
For Linux, macOS, or Unix:  

```
aws rds modify-db-cluster-parameter-group \
  --db-cluster-parameter-group-name myparametergroup \
  --parameters "ParameterName=replicate-wild-ignore-table,ParameterValue='mydb7.order%,mydb7.return%',ApplyMethod=immediate"
```
For Windows:  

```
aws rds modify-db-cluster-parameter-group ^
  --db-cluster-parameter-group-name myparametergroup ^
  --parameters "ParameterName=replicate-wild-ignore-table,ParameterValue='mydb7.order%,mydb7.return%',ApplyMethod=immediate"
```

### Viewing the replication filters for a read replica<a name="AuroraMySQL.Replication.Filters.Viewing"></a>

You can view the replication filters for a read replica in the following ways:
+ Check the settings of the replication filtering parameters in the parameter group associated with the read replica\.

  For instructions, see [Viewing parameter values for a DB parameter group](USER_WorkingWithDBInstanceParamGroups.md#USER_WorkingWithParamGroups.Viewing)\.
+ In a MySQL client, connect to the read replica and run the `SHOW REPLICA STATUS` statement\.

  In the output, the following fields show the replication filters for the read replica:
  + `Binlog_Do_DB`
  + `Binlog_Ignore_DB`
  + `Replicate_Do_DB`
  + `Replicate_Ignore_DB`
  + `Replicate_Do_Table`
  + `Replicate_Ignore_Table`
  + `Replicate_Wild_Do_Table`
  + `Replicate_Wild_Ignore_Table`

  For more information about these fields, see [Checking Replication Status](https://dev.mysql.com/doc/refman/8.0/en/replication-administration-status.html) in the MySQL documentation\.

## Monitoring Amazon Aurora MySQL replication<a name="AuroraMySQL.Replication.Monitoring"></a>

 Read scaling and high availability depend on minimal lag time\. You can monitor how far an Aurora Replica is lagging behind the primary instance of your Aurora MySQL DB cluster by monitoring the Amazon CloudWatch `AuroraReplicaLag` metric\. The `AuroraReplicaLag` metric is recorded in each Aurora Replica\. 

 The primary DB instance also records the `AuroraReplicaLagMaximum` and `AuroraReplicaLag` Amazon CloudWatch metrics\. The `AuroraReplicaLagMaximum` metric records the maximum amount of lag between the primary DB instance and each Aurora Replica in the DB cluster\. The `AuroraReplicaLag` metric records the minimum amount of lag between the primary DB instance and each Aurora Replica in the DB cluster\. 

If you need the most current value for Aurora Replica lag, you can check the `AuroraReplicaLag` metric in Amazon CloudWatch\. The Aurora Replica lag is also recorded on each Aurora Replica of your Aurora MySQL DB cluster in the `information_schema.replica_host_status` table\. For more information on this table, see [information\_schema\.replica\_host\_status](AuroraMySQL.Reference.ISTables.md#AuroraMySQL.Reference.ISTables.replica_host_status)\.

For more information on monitoring RDS instances and CloudWatch metrics, see [Monitoring metrics in an Amazon Aurora cluster](MonitoringAurora.md)\.