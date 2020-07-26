# Using GTID\-Based Replication for Aurora MySQL<a name="mysql-replication-gtid"></a>

 Following, you can learn how to use global transaction identifiers \(GTIDs\) with binary log \(binlog\) replication between an Aurora MySQL cluster and an external source\. 

**Note**  
For Aurora, you can only use this feature with Aurora MySQL clusters that use binlog replication to or from an external MySQL database\. The other database might be an Amazon RDS MySQL instance, an on\-premises MySQL database, or an Aurora DB cluster in a different AWS Region\. To learn how to configure that kind of replication, see [Replication Between Aurora and MySQL or Between Aurora and Another Aurora DB Cluster \(Binlog Replication\)](AuroraMySQL.Replication.MySQL.md)\. 

If you use binlog replication and aren't familiar with GTID\-based replication with MySQL, see [Replication with Global Transaction Identifiers](https://dev.mysql.com/doc/refman/5.7/en/replication-gtids.html) in the MySQL documentation for background\.

**Note**  
GTID\-based replication is supported for MySQL 5\.7\-compatible clusters in Aurora MySQL version 2\.04 and later\. GTID\-based replication isn't supported for MySQL 5\.6\-compatible clusters in Aurora MySQL version 1\.

**Topics**
+ [Overview of Global Transaction Identifiers \(GTIDs\)](#mysql-replication-gtid.overview)
+ [Parameters for GTID\-Based Replication](#mysql-replication-gtid.parameters)
+ [Configuring GTID\-Based Replication for an Aurora MySQL Cluster](#mysql-replication-gtid.configuring-aurora)
+ [Disabling GTID\-Based Replication for an Aurora MySQL DB Cluster](#mysql-replication-gtid.disabling)

## Overview of Global Transaction Identifiers \(GTIDs\)<a name="mysql-replication-gtid.overview"></a>

*Global transaction identifiers \(GTIDs\)* are unique identifiers generated for committed MySQL transactions\. You can use GTIDs to make binlog replication simpler and easier to troubleshoot\.

**Note**  
When Aurora synchronizes data among the DB instances in a cluster, that replication mechanism doesn't involve the binary log \(binlog\)\. For Aurora MySQL, GTID\-based replication only applies when you also use binlog replication to replicate into or out of an Aurora MySQL DB cluster from an external MySQL\-compatible database\. 

MySQL uses two different types of transactions for binlog replication:
+ *GTID transactions* – Transactions that are identified by a GTID\.
+ *Anonymous transactions* – Transactions that don't have a GTID assigned\.

In a replication configuration, GTIDs are unique across all DB instances\. GTIDs simplify replication configuration because when you use them, you don't have to refer to log file positions\. GTIDs also make it easier to track replicated transactions and determine whether the source instance and replicas are consistent\.

 You typically use GTID\-based replication with Aurora when replicating from an external MySQL\-compatible database into an Aurora cluster\. You can set up this replication configuration as part of a migration from an on\-premises or Amazon RDS database into Aurora MySQL\. If the external database already uses GTIDs, enabling GTID\-based replication for the Aurora cluster simplifies the replication process\. 

 You configure GTID\-based replication for an Aurora MySQL cluster by first setting the relevant configuration parameters in a DB cluster parameter group\. You then associate that parameter group with the cluster\. 

## Parameters for GTID\-Based Replication<a name="mysql-replication-gtid.parameters"></a>

Use the following parameters to configure GTID\-based replication\.


****  

| Parameter | Valid Values | Description | 
| --- | --- | --- | 
|  `gtid_mode`  |  `OFF`, `OFF_PERMISSIVE`, `ON_PERMISSIVE`, `ON`  |  `OFF` specifies that new transactions are anonymous transactions \(that is, don't have GTIDs\), and a transaction must be anonymous to be replicated\.  `OFF_PERMISSIVE` specifies that new transactions are anonymous transactions, but all transactions can be replicated\.  `ON_PERMISSIVE` specifies that new transactions are GTID transactions, but all transactions can be replicated\.  `ON` specifies that new transactions are GTID transactions, and a transaction must be a GTID transaction to be replicated\.   | 
|  `enforce_gtid_consistency`  |  `OFF`, `ON`, `WARN`  |  `OFF` allows transactions to violate GTID consistency\.  `ON` prevents transactions from violating GTID consistency\.  `WARN` allows transactions to violate GTID consistency but generates a warning when a violation occurs\.   | 

**Note**  
In the AWS Management Console, the `gtid_mode` parameter appears as `gtid-mode`\.

For GTID\-based replication, use these settings for the DB cluster parameter group for your Aurora MySQL DB cluster: 
+ `ON` and `ON_PERMISSIVE` apply only to outgoing replication from an RDS DB instance or Aurora MySQL cluster\. Both of these values cause your RDS DB instance or Aurora DB cluster to use GTIDs for transactions that are replicated to an external database\. `ON` requires that the external database also use GTID\-based replication\. `ON_PERMISSIVE` makes GTID\-based replication optional on the external database\. 
+ `OFF_PERMISSIVE`, if set, means that your RDS DB instances or Aurora DB cluster can accept incoming replication from an external database\. It can do this whether the external database uses GTID\-based replication or not\. 
+  `OFF`, if set, means that your RDS DB instances or Aurora DB cluster only accept incoming replication from external databases that don't use GTID\-based replication\. 

**Tip**  
Incoming replication is the most common binlog replication scenario for Aurora MySQL clusters\. For incoming replication, we recommend that you set the GTID mode to `OFF_PERMISSIVE`\. That setting allows incoming replication from external databases regardless of the GTID settings at the replication source\. 

For more information about parameter groups, see [Working with DB Parameter Groups and DB Cluster Parameter Groups](USER_WorkingWithParamGroups.md)\.

## Configuring GTID\-Based Replication for an Aurora MySQL Cluster<a name="mysql-replication-gtid.configuring-aurora"></a>

When GTID\-based replication is enabled for an Aurora MySQL DB cluster, the GTID settings apply to both inbound and outbound binlog replication\. 

**To enable GTID\-based replication for an Aurora MySQL cluster**

1. Create or edit a DB cluster parameter group using the following parameter settings:
   + `gtid_mode` – `ON` or `ON_PERMISSIVE`
   + `enforce_gtid_consistency` – `ON`

1. Associate the DB cluster parameter group with the Aurora MySQL cluster\. To do so, follow the procedures in [Working with DB Parameter Groups and DB Cluster Parameter Groups](USER_WorkingWithParamGroups.md)\.

## Disabling GTID\-Based Replication for an Aurora MySQL DB Cluster<a name="mysql-replication-gtid.disabling"></a>

You can disable GTID\-based replication for an Aurora MySQL DB cluster\. Doing so means that the Aurora cluster can't perform inbound or outbound binlog replication with external databases that use GTID\-based replication\. 

**Note**  
In the following procedure, *read replica* means the replication target in an Aurora configuration with binlog replication to or from an external database\. It doesn't mean the read\-only Aurora Replica DB instances\. For example, when an Aurora cluster accepts incoming replication from an external source, the Aurora primary instance acts as the read replica for binlog replication\. 

For more details about the stored procedures mentioned in this section, see [Aurora MySQL Stored Procedures](AuroraMySQL.Reference.md#AuroraMySQL.Reference.StoredProcs)\. 

**To disable GTID\-based replication for an Aurora MySQL DB cluster**

1. On the Aurora primary instance, run the following procedure\.

   ```
   CALL mysql.rds_set_master_auto_position(0);
   ```

1. Reset the `gtid_mode` to `ON_PERMISSIVE`\.

   1. Make sure that the DB cluster parameter group associated with the Aurora MySQL cluster has `gtid_mode` set to `ON_PERMISSIVE`\.

      For more information about setting configuration parameters using parameter groups, see [Working with DB Parameter Groups and DB Cluster Parameter Groups](USER_WorkingWithParamGroups.md)\.

   1. Restart the Aurora MySQL DB cluster\.

1. Reset the `gtid_mode` to `OFF_PERMISSIVE`:

   1. Make sure that the DB cluster parameter group associated with the Aurora MySQL cluster has `gtid_mode` set to `OFF_PERMISSIVE`\.

   1. Restart the Aurora MySQL DB cluster\.

1. 

   1. On the Aurora primary instance, run the `SHOW MASTER STATUS` command\.

      Your output should be similar to the following\.

      ```
      File                        Position
      ------------------------------------
      mysql-bin-changelog.000031      107
      ------------------------------------
      ```

      Note the file and position in your output\.

   1. On each read replica, use the file and position information from its source instance in the previous step to run the following query\.

      ```
      SELECT MASTER_POS_WAIT('file', position);
      ```

      For example, if the file name is `mysql-bin-changelog.000031` and the position is `107`, run the following statement\.

      ```
      SELECT MASTER_POS_WAIT('mysql-bin-changelog.000031', 107);
      ```

      If the read replica is past the specified position, the query returns immediately\. Otherwise, the function waits\. When the query returns for all read replicas, go to the next step\. 

1. Reset the GTID parameters to disable GTID\-based replication:

   1. Make sure that the DB cluster parameter group associated with the Aurora MySQL cluster has the following parameter settings:
      + `gtid_mode` – `OFF`
      + `enforce_gtid_consistency` – `OFF`

   1. Restart the Aurora MySQL DB cluster\.