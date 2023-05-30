# Replication between Aurora and MySQL or between Aurora and another Aurora DB cluster \(binary log replication\)<a name="AuroraMySQL.Replication.MySQL"></a><a name="binlog_replication"></a><a name="binlog"></a>

Because Amazon Aurora MySQL is compatible with MySQL, you can set up replication between a MySQL database and an Amazon Aurora MySQL DB cluster\. This type of replication uses the MySQL binary log replication, also referred to as *binlog replication*\. If you use binary log replication with Aurora, we recommend that your MySQL database run MySQL version 5\.5 or later\. You can set up replication where your Aurora MySQL DB cluster is the replication source or the replica\. You can replicate with an Amazon RDS MySQL DB instance, a MySQL database external to Amazon RDS, or another Aurora MySQL DB cluster\.

**Note**  
You can't use binlog replication to or from certain types of Aurora DB clusters\. In particular, binlog replication isn't available for Aurora Serverless v1 clusters\. If the `SHOW MASTER STATUS` and `SHOW SLAVE STATUS` \(Aurora MySQL version 2\) or `SHOW REPLICA STATUS` \(Aurora MySQL version 3\) statement returns no output, check that the cluster you're using supports binlog replication\.  
In Aurora MySQL version 3, binary log replication doesn't replicate to the `mysql` system database\. Passwords and accounts aren't replicated by binlog replication in Aurora MySQL version 3\. Therefore, Data Control Language \(DCL\) statements such as `CREATE USER`, `GRANT`, and `REVOKE` aren't replicated\.

You can also replicate with an RDS for MySQL DB instance or Aurora MySQL DB cluster in another AWS Region\. When you're performing replication across AWS Regions, make sure that your DB clusters and DB instances are publicly accessible\. If the Aurora MySQL DB clusters are in private subnets in your VPC, use VPC peering between the AWS Regions\. For more information, see [A DB cluster in a VPC accessed by an EC2 instance in a different VPC](USER_VPC.Scenarios.md#USER_VPC.Scenario3)\.

If you want to configure replication between an Aurora MySQL DB cluster and an Aurora MySQL DB cluster in another AWS Region, you can create an Aurora MySQL DB cluster as a read replica in a different AWS Region from the source DB cluster\. For more information, see [Replicating Amazon Aurora MySQL DB clusters across AWS Regions](AuroraMySQL.Replication.CrossRegion.md)\.

With Aurora MySQL version 2 and 3, you can replicate between Aurora MySQL and an external source or target that uses global transaction identifiers \(GTIDs\) for replication\. Ensure that the GTID\-related parameters in the Aurora MySQL DB cluster have settings that are compatible with the GTID status of the external database\. To learn how to do this, see [Using GTID\-based replication for Amazon Aurora MySQL](mysql-replication-gtid.md)\. In Aurora MySQL version 3\.01 and higher, you can choose how to assign GTIDs to transactions that are replicated from a source that doesn't use GTIDs\. For information about the stored procedure that controls that setting, see [mysql\.rds\_assign\_gtids\_to\_anonymous\_transactions \(Aurora MySQL version 3\)](mysql-stored-proc-replicating.md#mysql_assign_gtids_to_anonymous_transactions)\.

**Warning**  
 When you replicate between Aurora MySQL and MySQL, make sure that you use only InnoDB tables\. If you have MyISAM tables that you want to replicate, you can convert them to InnoDB before setting up replication with the following command\.   

```
alter table <schema>.<table_name> engine=innodb, algorithm=copy;
```

## Setting up replication with MySQL or another Aurora DB cluster<a name="AuroraMySQL.Replication.MySQL.SettingUp"></a>

Setting up MySQL replication with Aurora MySQL involves the following steps, which are discussed in detail:

[1\. Turn on binary logging on the replication source](#AuroraMySQL.Replication.MySQL.EnableBinlog)

[2\. Retain binary logs on the replication source until no longer needed](#AuroraMySQL.Replication.MySQL.RetainBinlogs)

[3\. Create a snapshot or dump of your replication source](#AuroraMySQL.Replication.MySQL.CreateSnapshot)

[4\. Load the snapshot or dump into your replica target](#AuroraMySQL.Replication.MySQL.LoadSnapshot)

[5\. Create a replication user on your replication source](#AuroraMySQL.Replication.MySQL.CreateReplUser)

[6\. Turn on replication on your replica target](#AuroraMySQL.Replication.MySQL.EnableReplication)

[7\. Monitor your replica](#AuroraMySQL.Replication.MySQL.Monitor)

### 1\. Turn on binary logging on the replication source<a name="AuroraMySQL.Replication.MySQL.EnableBinlog"></a>

 Find instructions on how to turn on binary logging on the replication source for your database engine following\. 


|  Database engine  |  Instructions  | 
| --- | --- | 
|   Aurora MySQL   |   **To turn on binary logging on an Aurora MySQL DB cluster**  Set the `binlog_format` DB cluster parameter to `ROW`, `STATEMENT`, or `MIXED`\. `MIXED` is recommended unless you have a need for a specific binlog format\. \(The default value is `OFF`\.\) To change the `binlog_format` parameter, create a custom DB cluster parameter group and associate that custom parameter group with your DB cluster\. You can't change parameters in the default DB cluster parameter group\. If you're changing the `binlog_format` parameter from `OFF` to another value, reboot your Aurora DB cluster for the change to take effect\.  For more information, see [Amazon Aurora DB cluster and DB instance parameters](USER_WorkingWithDBClusterParamGroups.md#Aurora.Managing.ParameterGroups) and [Working with parameter groups](USER_WorkingWithParamGroups.md)\.   | 
|   RDS for MySQL   |   **To turn on binary logging on an Amazon RDS DB instance**   You can't turn on binary logging directly for an Amazon RDS DB instance, but you can turn it on by doing one of the following:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Replication.MySQL.html)  | 
|   MySQL \(external\)  |  **To set up encrypted replication** To replicate data securely with Aurora MySQL version 2, you can use encrypted replication\.   If you don't need to use encrypted replication, you can skip these steps\.    The following are prerequisites for using encrypted replication:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Replication.MySQL.html)  During encrypted replication, the Aurora MySQL DB cluster acts a client to the MySQL database server\. The certificates and keys for the Aurora MySQL client are in files in \.pem format\.  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Replication.MySQL.html)  **To turn on binary logging on an external MySQL database**  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Replication.MySQL.html)  | 

### 2\. Retain binary logs on the replication source until no longer needed<a name="AuroraMySQL.Replication.MySQL.RetainBinlogs"></a>

When you use MySQL binary log replication, Amazon RDS doesn't manage the replication process\. As a result, you need to ensure that the binlog files on your replication source are retained until after the changes have been applied to the replica\. This maintenance helps you to restore your source database in the event of a failure\.

Use the following instructions to retain binary logs for your database engine\.


|  Database engine  |  Instructions  | 
| --- | --- | 
|   Aurora MySQL  |  **To retain binary logs on an Aurora MySQL DB cluster** You don't have access to the binlog files for an Aurora MySQL DB cluster\. As a result, you must choose a time frame to retain the binlog files on your replication source long enough to ensure that the changes have been applied to your replica before the binlog file is deleted by Amazon RDS\. You can retain binlog files on an Aurora MySQL DB cluster for up to 90 days\. If you're setting up replication with a MySQL database or RDS for MySQL DB instance as the replica, and the database that you are creating a replica for is very large, choose a large time frame to retain binlog files until the initial copy of the database to the replica is complete and the replica lag has reached 0\. To set the binary log retention time frame, use the [mysql\.rds\_set\_configuration](mysql-stored-proc-configuring.md#mysql_rds_set_configuration) procedure and specify a configuration parameter of `'binlog retention hours'` along with the number of hours to retain binlog files on the DB cluster\. The maximum value for Aurora MySQL version 2\.11\.0 and higher and version 3 is 2160 \(90 days\)\. The following example sets the retention period for binlog files to 6 days: <pre>CALL mysql.rds_set_configuration('binlog retention hours', 144);</pre> After replication has been started, you can verify that changes have been applied to your replica by running the `SHOW SLAVE STATUS` \(Aurora MySQL version 2\) or `SHOW REPLICA STATUS` \(Aurora MySQL version 3\) command on your replica and checking the `Seconds behind master` field\. If the `Seconds behind master` field is 0, then there is no replica lag\. When there is no replica lag, reduce the length of time that binlog files are retained by setting the `binlog retention hours` configuration parameter to a smaller time frame\. If this setting isn't specified, the default for Aurora MySQL is 24 \(1 day\)\. If you specify a value for `'binlog retention hours'` that is higher than the maximum value, then Aurora MySQL uses the maximum\.  | 
|   RDS for MySQL   |   **To retain binary logs on an Amazon RDS DB instance**   You can retain binary log files on an Amazon RDS DB instance by setting the binlog retention hours just as with an Aurora MySQL DB cluster, described in the previous row\. You can also retain binlog files on an Amazon RDS DB instance by creating a read replica for the DB instance\. This read replica is temporary and solely for the purpose of retaining binlog files\. After the read replica has been created, call the [mysql\.rds\_stop\_replication](mysql-stored-proc-replicating.md#mysql_rds_stop_replication) procedure on the read replica\. While replication is stopped, Amazon RDS doesn't delete any of the binlog files on the replication source\. After you have set up replication with your permanent replica, you can delete the read replica when the replica lag \(`Seconds behind master` field\) between your replication source and your permanent replica reaches 0\.  | 
|   MySQL \(external\)   |  **To retain binary logs on an external MySQL database** Because binlog files on an external MySQL database are not managed by Amazon RDS, they are retained until you delete them\. After replication has been started, you can verify that changes have been applied to your replica by running the `SHOW SLAVE STATUS` \(Aurora MySQL version 2\) or `SHOW REPLICA STATUS` \(Aurora MySQL version 3\) command on your replica and checking the `Seconds behind master` field\. If the `Seconds behind master` field is 0, then there is no replica lag\. When there is no replica lag, you can delete old binlog files\.  | 

### 3\. Create a snapshot or dump of your replication source<a name="AuroraMySQL.Replication.MySQL.CreateSnapshot"></a>

 You use a snapshot or dump of your replication source to load a baseline copy of your data onto your replica and then start replicating from that point on\. 

Use the following instructions to create a snapshot or dump of the replication source for your database engine\.


| Database engine | Instructions | 
| --- | --- | 
|   Aurora MySQL   |  **To create a snapshot of an Aurora MySQL DB cluster** [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Replication.MySQL.html)  | 
|  RDS for MySQL  |  **To create a snapshot of an Amazon RDS DB instance** Create a read replica of your Amazon RDS DB instance\. For more information, see [Creating a read replica](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ReadRepl.html#USER_ReadRepl.Create) in the *Amazon Relational Database Service User Guide*\.  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Replication.MySQL.html)  | 
|  MySQL \(external\)  |  **To create a dump of an external MySQL database** [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Replication.MySQL.html)  | 

### 4\. Load the snapshot or dump into your replica target<a name="AuroraMySQL.Replication.MySQL.LoadSnapshot"></a>

If you plan to load data from a dump of a MySQL database that is external to Amazon RDS, then you might want to create an EC2 instance to copy the dump files to, and then load the data into your DB cluster or DB instance from that EC2 instance\. Using this approach, you can compress the dump file\(s\) before copying them to the EC2 instance in order to reduce the network costs associated with copying data to Amazon RDS\. You can also encrypt the dump file or files to secure the data as it is being transferred across the network\.

Use the following instructions to load the snapshot or dump of your replication source into your replica target for your database engine\.


| Database engine | Instructions | 
| --- | --- | 
|  Aurora MySQL   |   **To load a snapshot or dump into an Aurora MySQL DB cluster**  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Replication.MySQL.html)  | 
|   RDS for MySQL   |  **To load a dump into an Amazon RDS DB instance** [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Replication.MySQL.html)  | 
|  MySQL \(external\)  |  **To load a dump into an external MySQL database** You can't load a DB snapshot or a DB cluster snapshot into an external MySQL database\. Instead, you must use the output from the `mysqldump` command\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Replication.MySQL.html)  | 

### 5\. Create a replication user on your replication source<a name="AuroraMySQL.Replication.MySQL.CreateReplUser"></a>

Create a user ID on the source that is used solely for replication\. The following is an example\.

```
mysql> CREATE USER 'repl_user'@'<domain_name>' IDENTIFIED BY '<password>';
```

The user requires the `REPLICATION CLIENT` and `REPLICATION SLAVE` privileges\. Grant these privileges to the user\.

If you need to use encrypted replication, require SSL connections for the replication user\. For example, you can use one of the following statement to require SSL connections on the user account `repl_user`\.

```
GRANT REPLICATION CLIENT, REPLICATION SLAVE ON *.* TO 'repl_user'@'<domain_name>';
```

```
GRANT USAGE ON *.* TO 'repl_user'@'<domain_name>' REQUIRE SSL;
```

**Note**  
If `REQUIRE SSL` isn't included, the replication connection might silently fall back to an unencrypted connection\.

### 6\. Turn on replication on your replica target<a name="AuroraMySQL.Replication.MySQL.EnableReplication"></a>

Before you turn on replication, we recommend that you take a manual snapshot of the Aurora MySQL DB cluster or RDS for MySQL DB instance replica target\. If a problem arises and you need to re\-establish replication with the DB cluster or DB instance replica target, you can restore the DB cluster or DB instance from this snapshot instead of having to import the data into your replica target again\.

Use the following instructions to turn on replication for your database engine\.


|  Database engine  |  Instructions  | 
| --- | --- | 
|   Aurora MySQL   |  **To turn on replication from an Aurora MySQL DB cluster**  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Replication.MySQL.html) To use SSL encryption, set the final value to `1` instead of `0`\.  | 
|   RDS for MySQL   |   **To turn on replication from an Amazon RDS DB instance**  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Replication.MySQL.html) To use SSL encryption, set the final value to `1` instead of `0`\.  | 
|   MySQL \(external\)   |   **To turn on replication from an external MySQL database**  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Replication.MySQL.html)  | 

If replication fails, it can result in a large increase in unintentional I/O on the replica, which can degrade performance\. If replication fails or is no longer needed, you can run the [mysql\.rds\_reset\_external\_master \(Aurora MySQL version 2\)](mysql-stored-proc-replicating.md#mysql_rds_reset_external_master) or [mysql\.rds\_reset\_external\_source \(Aurora MySQL version 3\)](mysql-stored-proc-replicating.md#mysql_rds_reset_external_source) stored procedure to remove the replication configuration\.

### 7\. Monitor your replica<a name="AuroraMySQL.Replication.MySQL.Monitor"></a>

 When you set up MySQL replication with an Aurora MySQL DB cluster, you must monitor failover events for the Aurora MySQL DB cluster when it is the replica target\. If a failover occurs, then the DB cluster that is your replica target might be recreated on a new host with a different network address\. For information on how to monitor failover events, see [Working with Amazon RDS event notification](USER_Events.md)\. 

 You can also monitor how far the replica target is behind the replication source by connecting to the replica target and running the `SHOW SLAVE STATUS` \(Aurora MySQL version 2\) or `SHOW REPLICA STATUS` \(Aurora MySQL version 3\) command\. In the command output, the `Seconds Behind Master` field tells you how far the replica target is behind the source\. 

## Synchronizing passwords between replication source and target<a name="AuroraMySQL.Replication.passwords"></a>

 When you change user accounts and passwords on the replication source using SQL statements, those changes are replicated to the replication target automatically\. 

 If you use the AWS Management Console, the AWS CLI, or the RDS API to change the master password on the replication source, those changes are not automatically replicated to the replication target\. If you want to synchronize the master user and master password between the source and target systems, you must make the same change on the replication target yourself\. 

## Stopping replication between Aurora and MySQL or between Aurora and another Aurora DB cluster<a name="AuroraMySQL.Replication.MySQL.Stopping"></a>

To stop binary log replication with a MySQL DB instance, external MySQL database, or another Aurora DB cluster, follow these steps, discussed in detail following in this topic\.

[1\. Stop binary log replication on the replica target](#AuroraMySQL.Replication.MySQL.Stopping.StopReplication)

[2\. Turn off binary logging on the replication source](#AuroraMySQL.Replication.MySQL.Stopping.DisableBinaryLogging)

### 1\. Stop binary log replication on the replica target<a name="AuroraMySQL.Replication.MySQL.Stopping.StopReplication"></a>

Use the following instructions to stop binary log replication for your database engine\.


|  Database engine  |  Instructions  | 
| --- | --- | 
|   Aurora MySQL   |  **To stop binary log replication on an Aurora MySQL DB cluster replica target** Connect to the Aurora DB cluster that is the replica target, and call the [mysql\.rds\_stop\_replication](mysql-stored-proc-replicating.md#mysql_rds_stop_replication) procedure\.  | 
|   RDS for MySQL   |  **To stop binary log replication on an Amazon RDS DB instance** Connect to the RDS DB instance that is the replica target and call the [mysql\.rds\_stop\_replication](mysql-stored-proc-replicating.md#mysql_rds_stop_replication) procedure\.  | 
|   MySQL \(external\)   |  **To stop binary log replication on an external MySQL database** Connect to the MySQL database and run the `STOP SLAVE` \(version 5\.7\) or `STOP REPLICA` \(version 8\.0\) command\.  | 

### 2\. Turn off binary logging on the replication source<a name="AuroraMySQL.Replication.MySQL.Stopping.DisableBinaryLogging"></a>

Use the instructions in the following table to turn off binary logging on the replication source for your database engine\.


| Database engine | Instructions | 
| --- | --- | 
|   Aurora MySQL   |  **To turn off binary logging on an Amazon Aurora DB cluster** [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Replication.MySQL.html)  | 
|   RDS for MySQL   |  **To turn off binary logging on an Amazon RDS DB instance** You can't turn off binary logging directly for an Amazon RDS DB instance, but you can turn it off by doing the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Replication.MySQL.html)  | 
|   MySQL \(external\)   |  **To turn off binary logging on an external MySQL database** Connect to the MySQL database and call the `STOP REPLICATION` command\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Replication.MySQL.html)  | 

## Using Amazon Aurora to scale reads for your MySQL database<a name="AuroraMySQL.Replication.ReadScaling"></a>

You can use Amazon Aurora with your MySQL DB instance to take advantage of the read scaling capabilities of Amazon Aurora and expand the read workload for your MySQL DB instance\. To use Aurora to scale reads for your MySQL DB instance, create an Amazon Aurora MySQL DB cluster and make it a read replica of your MySQL DB instance\. This applies to an RDS for MySQL DB instance, or a MySQL database running external to Amazon RDS\.

For information on creating an Amazon Aurora DB cluster, see [Creating an Amazon Aurora DB cluster](Aurora.CreateInstance.md)\.

When you set up replication between your MySQL DB instance and your Amazon Aurora DB cluster, be sure to follow these guidelines:
+ Use the Amazon Aurora DB cluster endpoint address when you reference your Amazon Aurora MySQL DB cluster\. If a failover occurs, then the Aurora Replica that is promoted to the primary instance for the Aurora MySQL DB cluster continues to use the DB cluster endpoint address\.
+ Maintain the binlogs on your writer instance until you have verified that they have been applied to the Aurora Replica\. This maintenance ensures that you can restore your writer instance in the event of a failure\.

**Important**  
When using self\-managed replication, you're responsible for monitoring and resolving any replication issues that may occur\. For more information, see [Diagnosing and resolving lag between read replicas](CHAP_Troubleshooting.md#CHAP_Troubleshooting.MySQL.ReplicaLag)\.

**Note**  
The permissions required to start replication on an Aurora MySQL DB cluster are restricted and not available to your Amazon RDS master user\. Therefore, you must use the [mysql\.rds\_set\_external\_master \(Aurora MySQL version 2\)](mysql-stored-proc-replicating.md#mysql_rds_set_external_master) or [mysql\.rds\_set\_external\_source \(Aurora MySQL version 3\)](mysql-stored-proc-replicating.md#mysql_rds_set_external_source) and [mysql\.rds\_start\_replication](mysql-stored-proc-replicating.md#mysql_rds_start_replication) procedures to set up replication between your Aurora MySQL DB cluster and your MySQL DB instance\.

### Start replication between an external source instance and an Aurora MySQL DB cluster<a name="AuroraMySQL.Replication.ReadScaling.Procedure"></a>

1.  Make the source MySQL DB instance read\-only: 

   ```
   mysql> FLUSH TABLES WITH READ LOCK;
   mysql> SET GLOBAL read_only = ON;
   ```

1.  Run the `SHOW MASTER STATUS` command on the source MySQL DB instance to determine the binlog location\. You receive output similar to the following example: 

   ```
   File                        Position
   ------------------------------------
    mysql-bin-changelog.000031      107
   ------------------------------------
   ```

1. Copy the database from the external MySQL DB instance to the Amazon Aurora MySQL DB cluster using `mysqldump`\. For very large databases, you might want to use the procedure in [Importing data to a MySQL or MariaDB DB instance with reduced downtime](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/MySQL.Procedural.Importing.NonRDSRepl.html) in the * Amazon Relational Database Service User Guide*\.

   For Linux, macOS, or Unix:

   ```
   mysqldump \
       --databases <database_name> \
       --single-transaction \
       --compress \
       --order-by-primary \
       -u <local_user> \
       -p <local_password> | mysql \
           --host aurora_cluster_endpoint_address \
           --port 3306 \
           -u <RDS_user_name> \
           -p <RDS_password>
   ```

   For Windows:

   ```
   mysqldump ^
       --databases <database_name> ^
       --single-transaction ^
       --compress ^
       --order-by-primary ^
       -u <local_user> ^
       -p <local_password> | mysql ^
           --host aurora_cluster_endpoint_address ^
           --port 3306 ^
           -u <RDS_user_name> ^
           -p <RDS_password>
   ```
**Note**  
Make sure that there is not a space between the `-p` option and the entered password\.

   Use the `--host`, `--user (-u)`, `--port` and `-p` options in the `mysql` command to specify the hostname, user name, port, and password to connect to your Aurora DB cluster\. The host name is the DNS name from the Amazon Aurora DB cluster endpoint, for example, `mydbcluster.cluster-123456789012.us-east-1.rds.amazonaws.com`\. You can find the endpoint value in the cluster details in the Amazon RDS Management Console\.

1. Make the source MySQL DB instance writeable again:

   ```
   mysql> SET GLOBAL read_only = OFF;
   mysql> UNLOCK TABLES;
   ```

   For more information on making backups for use with replication, see [http://dev.mysql.com/doc/refman/8.0/en/replication-solutions-backups-read-only.html](http://dev.mysql.com/doc/refman/8.0/en/replication-solutions-backups-read-only.html) in the MySQL documentation\.

1. In the Amazon RDS Management Console, add the IP address of the server that hosts the source MySQL database to the VPC security group for the Amazon Aurora DB cluster\. For more information on modifying a VPC security group, see [Security groups for your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html) in the *Amazon Virtual Private Cloud User Guide*\.

   You might also need to configure your local network to permit connections from the IP address of your Amazon Aurora DB cluster, so that it can communicate with your source MySQL instance\. To find the IP address of the Amazon Aurora DB cluster, use the `host` command\.

   ```
   host <aurora_endpoint_address>
   ```

   The host name is the DNS name from the Amazon Aurora DB cluster endpoint\.

1. Using the client of your choice, connect to the external MySQL instance and create a MySQL user to be used for replication\. This account is used solely for replication and must be restricted to your domain to improve security\. The following is an example\.

   ```
   CREATE USER 'repl_user'@'mydomain.com' IDENTIFIED BY '<password>';
   ```

1. For the external MySQL instance, grant `REPLICATION CLIENT` and `REPLICATION SLAVE` privileges to your replication user\. For example, to grant the `REPLICATION CLIENT` and `REPLICATION SLAVE` privileges on all databases for the '`repl_user`' user for your domain, issue the following command\.

   ```
   GRANT REPLICATION CLIENT, REPLICATION SLAVE ON *.* TO 'repl_user'@'mydomain.com'
       IDENTIFIED BY '<password>';
   ```

1. Take a manual snapshot of the Aurora MySQL DB cluster to be the read replica before setting up replication\. If you need to reestablish replication with the DB cluster as a read replica, you can restore the Aurora MySQL DB cluster from this snapshot instead of having to import the data from your MySQL DB instance into a new Aurora MySQL DB cluster\.

1. Make the Amazon Aurora DB cluster the replica\. Connect to the Amazon Aurora DB cluster as the master user and identify the source MySQL database as the replication master by using the [mysql\.rds\_set\_external\_master \(Aurora MySQL version 2\)](mysql-stored-proc-replicating.md#mysql_rds_set_external_master) or [mysql\.rds\_set\_external\_source \(Aurora MySQL version 3\)](mysql-stored-proc-replicating.md#mysql_rds_set_external_source) and [mysql\.rds\_start\_replication](mysql-stored-proc-replicating.md#mysql_rds_start_replication) procedures\.

   Use the master log file name and master log position that you determined in Step 2\. The following is an example\.

   ```
   For Aurora MySQL version 2:
   CALL mysql.rds_set_external_master ('mymasterserver.mydomain.com', 3306,
       'repl_user', '<password>', 'mysql-bin-changelog.000031', 107, 0);
   
   For Aurora MySQL version 3:
   CALL mysql.rds_set_external_source ('mymasterserver.mydomain.com', 3306,
       'repl_user', '<password>', 'mysql-bin-changelog.000031', 107, 0);
   ```

1. On the Amazon Aurora DB cluster, call the [mysql\.rds\_start\_replication](mysql-stored-proc-replicating.md#mysql_rds_start_replication) procedure to start replication\.

   ```
   CALL mysql.rds_start_replication; 
   ```

After you have established replication between your source MySQL DB instance and your Amazon Aurora DB cluster, you can add Aurora Replicas to your Amazon Aurora DB cluster\. You can then connect to the Aurora Replicas to read scale your data\. For information on creating an Aurora Replica, see [Adding Aurora Replicas to a DB cluster](aurora-replicas-adding.md)\.

## Optimizing binary log replication<a name="binlog-optimization"></a>

 Following, you can learn how to optimize binary log replication performance and troubleshoot related issues in Aurora MySQL\. 

**Tip**  
 This discussion presumes that you are familiar with the MySQL binary log replication mechanism and how it works\. For background information, see [Replication Implementation](https://dev.mysql.com/doc/refman/8.0/en/replication-implementation.html) in the MySQL documentation\. 

### Multithreaded binary log replication \(Aurora MySQL version 3 and higher\)<a name="binlog-optimization-multithreading"></a>

 With multithreaded binary log replication, a SQL thread reads events from the relay log and queues them up for SQL worker threads to apply\. The SQL worker threads are managed by a coordinator thread\. The binary log events are applied in parallel when possible\. 

 When an Aurora MySQL instance is configured to use binary log replication, by default the replica instance uses single\-threaded replication\. To enable multithreaded replication, you update the `replica_parallel_workers` parameter to a value greater than zero in your custom parameter group\. 

 The following configuration options help you to fine\-tune multithreaded replication\. For usage information, see [Replication and Binary Logging Options and Variables](https://dev.mysql.com/doc/refman/8.0/en/replication-options.html) in the *MySQL Reference Manual*\. 

 Optimal configuration depends on several factors\. For example, performance for binary log replication is influenced by your database workload characteristics and the DB instance class the replica is running on\. Thus, we recommend that you thoroughly test all changes to these configuration parameters before applying new parameter settings to a production instance\. 
+  `replica_parallel_workers` 
+  `replica_parallel_type` 
+  `replica_preserve_commit_order` 
+  `binlog_transaction_dependency_tracking` 
+  `binlog_transaction_dependency_history_size` 
+  `binlog_group_commit_sync_delay` 
+  `binlog_group_commit_sync_no_delay_count` 

### Optimizing binlog replication \(Aurora MySQL 2\.10 and higher\)<a name="binlog-optimization-binlog-io-cache"></a><a name="binlog_boost"></a><a name="binlog_io_cache"></a>

 In Aurora MySQL 2\.10 and higher, Aurora automatically applies an optimization known as the binlog I/O cache to binary log replication\. By caching the most recently committed binlog events, this optimization is designed to improve binlog dump thread performance while limiting the impact to foreground transactions on the binlog source instance\. 

**Note**  
 This memory used for this feature is independent of the MySQL `binlog_cache` setting\.   
 This feature doesn't apply to Aurora DB instances that use the `db.t2` and `db.t3` instance classes\. 

 You don't need to adjust any configuration parameters to turn on this optimization\. In particular, if you adjust the configuration parameter `aurora_binlog_replication_max_yield_seconds` to a nonzero value in earlier Aurora MySQL versions, set it back to zero for Aurora MySQL 2\.10 and higher\. 

 The status variables `aurora_binlog_io_cache_reads` and `aurora_binlog_io_cache_read_requests` are available in Aurora MySQL 2\.10 and higher\. These status variables help you to monitor how often the data is read from the binlog I/O cache\. 
+  `aurora_binlog_io_cache_read_requests` shows the number of binlog I/O read requests from the cache\. 
+  `aurora_binlog_io_cache_reads` shows the number of binlog I/O reads that retrieve information from the cache\. 

 The following SQL query computes the percentage of binlog read requests that take advantage of the cached information\. In this case, the closer the ratio is to 100, the better it is\. 

```
mysql> SELECT
  (SELECT VARIABLE_VALUE FROM INFORMATION_SCHEMA.GLOBAL_STATUS
    WHERE VARIABLE_NAME='aurora_binlog_io_cache_reads')
  / (SELECT VARIABLE_VALUE FROM INFORMATION_SCHEMA.GLOBAL_STATUS
    WHERE VARIABLE_NAME='aurora_binlog_io_cache_read_requests')
  * 100
  as binlog_io_cache_hit_ratio;
+---------------------------+
| binlog_io_cache_hit_ratio |
+---------------------------+
|         99.99847949080622 |
+---------------------------+
```

 The binlog I/O cache feature also includes new metrics related to the binlog dump threads\. *Dump threads* are the threads that are created when new binlog replicas are connected to the binlog source instance\. 

The dump thread metrics are printed to the database log every 60 seconds with the prefix `[Dump thread metrics]`\. The metrics include information for each binlog replica such as `Secondary_id`, `Secondary_uuid`, binlog file name, and the position that each replica is reading\. The metrics also include `Bytes_behind_primary` representing the distance in bytes between replication source and replica\. This metric measures the lag of the replica I/O thread\. That figure is different from the lag of the replica SQL applier thread, which is represented by the `seconds_behind_master` metric on the binlog replica\. You can determine whether binlog replicas are catching up to the source or falling behind by checking whether the distance decreases or increases\. 

### Optimizing binlog replication \(Aurora MySQL version 2 through 2\.09\)<a name="binlog-optimization-pre-2.10"></a>

 To optimize binary log replication for Aurora MySQL, you adjust the following cluster\-level optimization parameters\. These parameters help you to specify the right balance between latency on the binlog source instance and replication lag\. 
+  `aurora_binlog_use_large_read_buffer` 
+  `aurora_binlog_read_buffer_size` 
+  `aurora_binlog_replication_max_yield_seconds` 

**Note**  
For MySQL 5\.7\-compatible clusters, you can use these parameters in Aurora MySQL version 2 through 2\.09\.\*\. In Aurora MySQL 2\.10\.0 and higher, these parameters are superseded by the binlog I/O cache optimization and you don't need to use them\.

**Topics**
+ [Overview of the large read buffer and max\-yield optimizations](#aurora_mysql_max_yield_overview)
+ [Related parameters](#aurora_mysql_max_yield_reference)
+ [Enabling the max\-yield mechanism for binary log replication](#aurora_mysql_max_yield_enabling)
+ [Turning off the binary log replication max\-yield optimization](#aurora_mysql_max_yield_disabling)
+ [Turning off the large read buffer](#aurora_mysql_large_read_buffer_disabling)

#### Overview of the large read buffer and max\-yield optimizations<a name="aurora_mysql_max_yield_overview"></a>

 You might experience reduced binary log replication performance when the binary log dump thread accesses the Aurora cluster volume while the cluster processes a high number of transactions\. You can use the parameters `aurora_binlog_use_large_read_buffer`, `aurora_binlog_replication_max_yield_seconds`, and `aurora_binlog_read_buffer_size` to help minimize this type of contention\. 

Suppose that you have a situation where `aurora_binlog_replication_max_yield_seconds` is set to greater than 0 and the current binlog file of the dump thread is active\. In this case, the binary log dump thread waits up to a specified number of seconds for the current binlog file to be filled by transactions\. This wait period avoids contention that can arise from replicating each binlog event individually\. However, doing so increases the replica lag for binary log replicas\. Those replicas can fall behind the source by the same number of seconds as the `aurora_binlog_replication_max_yield_seconds` setting\. 

 The current binlog file means the binlog file that the dump thread is currently reading to perform replication\. We consider that a binlog file is active when the binlog file is updating or open to be updated by incoming transactions\. After Aurora MySQL fills up the active binlog file, MySQL creates and switches to a new binlog file\. The old binlog file becomes inactive\. It isn't updated by incoming transactions any longer\. 

**Note**  
 Before adjusting these parameters, measure your transaction latency and throughput over time\. You might find that binary log replication performance is stable and has low latency even if there is occasional contention\. 

`aurora_binlog_use_large_read_buffer`  
 If this parameter is set to 1, Aurora MySQL optimizes binary log replication based on the settings of the parameters `aurora_binlog_read_buffer_size` and `aurora_binlog_replication_max_yield_seconds`\. If `aurora_binlog_use_large_read_buffer` is 0, Aurora MySQL ignores the values of the `aurora_binlog_read_buffer_size` and `aurora_binlog_replication_max_yield_seconds` parameters\. 

`aurora_binlog_read_buffer_size`  
 Binary log dump threads with larger read buffer minimize the number of read I/O operations by reading more events for each I/O\. The parameter `aurora_binlog_read_buffer_size` sets the read buffer size\. The large read buffer can reduce binary log contention for workloads that generate a large amount of binlog data\.   
 This parameter only has an effect when the cluster also has the setting `aurora_binlog_use_large_read_buffer=1`\.   
 Increasing the size of the read buffer doesn't affect the performance of binary log replication\. Binary log dump threads don't wait for updating transactions to fill up the read buffer\. 

`aurora_binlog_replication_max_yield_seconds`  
 If your workload requires low transaction latency, and you can tolerate some replication lag, you can increase the `aurora_binlog_replication_max_yield_seconds` parameter\. This parameter controls the maximum yield property of binary log replication in your cluster\.   
 This parameter only has an effect when the cluster also has the setting `aurora_binlog_use_large_read_buffer=1`\. 

 Aurora MySQL recognizes any change to the `aurora_binlog_replication_max_yield_seconds` parameter value immediately\. You don't need to restart the DB instance\. However, when you turn on this setting, the dump thread only starts to yield when the current binlog file reaches its maximum size of 128 MB and is rotated to a new file\. 

#### Related parameters<a name="aurora_mysql_max_yield_reference"></a>

Use the following DB cluster parameters to turn on binlog optimization\.


|  Parameter  |  Default  |  Valid Values  |  Description  | 
| --- | --- | --- | --- | 
|  aurora\_binlog\_use\_large\_read\_buffer  |  1  |  0, 1  | Switch for turning on the feature of replication improvement\. When its value is 1, the binary log dump thread uses aurora\_binlog\_read\_buffer\_size for binary log replication; otherwise default buffer size \(8K\) is used\. Not used in Aurora MySQL version 3\. | 
|  aurora\_binlog\_read\_buffer\_size  |  5242880  |  8192\-536870912  | Read buffer size used by binary log dump thread when the parameter aurora\_binlog\_use\_large\_read\_buffer is set to 1\. Not used in Aurora MySQL version 3\. | 
|  aurora\_binlog\_replication\_max\_yield\_seconds  |  0  |  0\-36000  |  For Aurora MySQL version 2\.07\.\*, the maximum accepted value is 45\. You can tune it to a higher value on 2\.09 and later versions\. For version 2, this parameter works only when the parameter `aurora_binlog_use_large_read_buffer` is set to 1\.  | 

#### Enabling the max\-yield mechanism for binary log replication<a name="aurora_mysql_max_yield_enabling"></a>

 You can turn on the binary log replication max\-yield optimization as follows\. Doing so minimizes latency for transactions on the binlog source instance\. However, you might experience higher replication lag\. 

**To turn on the max\-yield binlog optimization for an Aurora MySQL cluster**

1.  Create or edit a DB cluster parameter group using the following parameter settings: 
   +  `aurora_binlog_use_large_read_buffer`: turn on with a value of `ON` or 1\. 
   +  `aurora_binlog_replication_max_yield_seconds`: specify a value greater than 0\. 

1.  Associate the DB cluster parameter group with the Aurora MySQL cluster that works as the binlog source\. To do so, follow the procedures in [Working with parameter groups](USER_WorkingWithParamGroups.md)\. 

1.  Confirm that the parameter change takes effect\. To do so, run the following query on the binlog source instance\. 

   ```
   SELECT @@aurora_binlog_use_large_read_buffer, @@aurora_binlog_replication_max_yield_seconds;
   ```

    Your output should be similar to the following\. 

   ```
   +---------------------------------------+-----------------------------------------------+
   | @@aurora_binlog_use_large_read_buffer | @@aurora_binlog_replication_max_yield_seconds |
   +---------------------------------------+-----------------------------------------------+
   |                                     1 |                                            45 |
   +---------------------------------------+-----------------------------------------------+
   ```

#### Turning off the binary log replication max\-yield optimization<a name="aurora_mysql_max_yield_disabling"></a>

 You can turn off the binary log replication max\-yield optimization as follows\. Doing so minimizes replication lag\. However, you might experience higher latency for transactions on the binlog source instance\. 

**To turn off the max\-yield optimization for an Aurora MySQL cluster**

1.  Make sure that the DB cluster parameter group associated with the Aurora MySQL cluster has `aurora_binlog_replication_max_yield_seconds` set to 0\. For more information about setting configuration parameters using parameter groups, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\. 

1.  Confirm that the parameter change takes effect\. To do so, run the following query on the binlog source instance\. 

   ```
   SELECT @@aurora_binlog_replication_max_yield_seconds;
   ```

    Your output should be similar to the following\. 

   ```
   +-----------------------------------------------+
   | @@aurora_binlog_replication_max_yield_seconds |
   +-----------------------------------------------+
   |                                             0 |
   +-----------------------------------------------+
   ```

#### Turning off the large read buffer<a name="aurora_mysql_large_read_buffer_disabling"></a>

 You can turn off the entire large read buffer feature as follows\. 

**To turn off the large binary log read buffer for an Aurora MySQL cluster**

1.  Reset the `aurora_binlog_use_large_read_buffer` to `OFF` or 0\. 

    Make sure that the DB cluster parameter group associated with the Aurora MySQL cluster has `aurora_binlog_use_large_read_buffer` set to 0\. For more information about setting configuration parameters using parameter groups, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\. 

1.  On the binlog source instance, run the following query\. 

   ```
   SELECT @@ aurora_binlog_use_large_read_buffer;
   ```

    Your output should be similar to the following\. 

   ```
   +---------------------------------------+
   | @@aurora_binlog_use_large_read_buffer |
   +---------------------------------------+
   |                                     0 |
   +---------------------------------------+
   ```

## Setting up enhanced binlog<a name="AuroraMySQL.Enhanced.binlog"></a>

Enhanced binlog reduces the compute performance overhead caused by turning on binlog, which can reach up to 50% in certain cases\. With enhanced binlog, this overhead can be reduced to about 13%\. To reduce overhead, enhanced binlog writes the binary and transactions logs to storage in parallel, which minimizes the data written at the transaction commit time\.

Using enhanced binlog also improves database recovery time after restarts and failovers by up to 99% compared to community MySQL binlog\. The enhanced binlog is compatible with existing binlog\-based workloads, and you interact with it the same way you interact with the community MySQL binlog\.

The enhanced binlog is available on Aurora MySQL 3\.03\.1 version \(compatible with MySQL 8\.0\.26\) and higher\. 

### Configuring enhanced binlog parameters<a name="AuroraMySQL.Enhanced.binlog.enhancedbinlog.parameters"></a>

You can switch between community MySQL binlog and enhanced binlog by turning on/off the enhanced binlog parameters\. The existing binlog consumers can continue to read and consume the binlog files without any gaps in the binlog file sequence\.


**To turn on enhanced binlog**  

|  Parameter  |  Default  |  Description  | 
| --- | --- | --- | 
|  binlog\_format  | – | Set the binlog\_format parameter to the binary logging format of your choice to turn on enhanced binlog\. Make sure the binlog\_format parameter isn't set to OFF\. For more information, see [Configuring Aurora MySQL binary logging](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/USER_LogAccess.MySQL.BinaryFormat.html)\. | 
|  aurora\_enhanced\_binlog  | 0 | Set the value of this parameter to 1 in the DB cluster parameter group associated with the Aurora MySQL cluster\. When you change the value of this parameter, you must reboot the writer instance when the DBClusterParameterGroupStatus value is shown as pending\-reboot\. | 
|  binlog\_backup  | 1 |  Turn off this parameter to turn on enhanced binlog\. To do so, set the value of this parameter to 0\.  | 
|  binlog\_replication\_globaldb  | 1 |  Turn off this parameter to turn on enhanced binlog\. To do so, set the value of this parameter to 0\.  | 

**Important**  
You can turn off the `binlog_backup` and `binlog_replication_globaldb` parameters only when you use enhanced binlog\.


**To turn off the enhanced binlog**  

|  Parameter  |  Description  | 
| --- | --- | 
|  aurora\_enhanced\_binlog  | Set the value of this parameter to 0 in the DB cluster parameter group associated with the Aurora MySQL cluster\. Whenever you change the value of this parameter, you must reboot the writer instance when the DBClusterParameterGroupStatus value is shown as pending\-reboot\. | 
|  binlog\_backup  |  Turn on this parameter when you turn off enhanced binlog\. To do so, set the value of this parameter to 1\.  | 
|  binlog\_replication\_globaldb  |  Turn on this parameter when you turn off enhanced binlog\. To do so, set the value of this parameter to 1\.  | 

To check whether enhanced binlog is turned on, use the following command in the MySQL client:

```
mysql>show status like 'aurora_enhanced_binlog';
              
+------------------------+--------+
| Variable_name          | Value  |
+------------------------+--------+
| aurora_enhanced_binlog | ACTIVE |
+------------------------+--------+
1 row in set (0.00 sec)
```

When enhanced binlog is turned on, the output shows `ACTIVE` for `aurora_enhanced_binlog`\.

### Other related parameters<a name="AuroraMySQL.Enhanced.binlog.other.parameters"></a>

When you turn on the enhanced binlog, the following parameters are affected:
+ The `max_binlog_size` parameter is visible but not modifiable\. It's default value `134217728` is automatically adjusted to `268435456` when enhanced binlog is turned on\.
+ Unlike in community MySQL binlog, the `binlog_checksum` doesn't act as a dynamic parameter when the enhanced binlog is turned on\. For the change to this parameter to take effect, you must manually reboot the DB cluster even when the `ApplyMethod` is `immediate`\.
+ The value you set on the `binlog_order_commits` parameter has no effect on the order of the commits when enhanced binlog is turned on\. The commits are always ordered without any further performance implications\.

### Differences between enhanced binlog and community MySQL binlog<a name="AuroraMySQL.Enhanced.binlog.differences"></a>

Enhanced binlog interacts differently with clones, backups and Aurora global database when compared to community MySQL binlog\. We recommend that you understand the following differences before using enhanced binlog\.
+ Enhanced binlog ﬁles from the source DB cluster aren't available on a cloned DB cluster\.
+ Enhanced binlog ﬁles aren't included in Aurora backups\. Therefore, enhanced binlog files from the source DB cluster aren't available after restoring a DB cluster despite any retention period set on it\.
+ When used with an Aurora global database, the enhanced binlog files of the primary DB cluster aren't replicated to the DB cluster in the secondary regions\.

****Examples****  
The following examples illustrate the differences between enhanced binlog and community MySQL binlog\.

**On a restored or cloned DB cluster**

When enhanced binlog is turned on, the historical binlog files aren't available in the restored or cloned DB cluster\. After a restore or clone operation, if binlog is turned on, the new DB cluster starts writing its own sequence of binlog files, starting from 1 \(mysql\-bin\-changelog\.000001\)\.

To turn on enhanced binlog after a restore or clone operation, set the required DB cluster parameters on the restored or cloned DB cluster\. For more information, see [Configuring enhanced binlog parameters](#AuroraMySQL.Enhanced.binlog.enhancedbinlog.parameters)\.

**Example Clone or restore operation performed when enhanced binlog is turned on**  
Source DB Cluster:  

```
mysql> show binary logs;
                      
+----------------------------+-----------+-----------+
| Log_name                   | File_size | Encrypted |
+----------------------------+-----------+-----------+
| mysql-bin-changelog.000001 |       156 | No        |
| mysql-bin-changelog.000002 |       156 | No        |
| mysql-bin-changelog.000003 |       156 | No        |
| mysql-bin-changelog.000004 |       156 | No        | --> Enhanced Binlog turned on
| mysql-bin-changelog.000005 |       156 | No        | --> Enhanced Binlog turned on
| mysql-bin-changelog.000006 |       156 | No        | --> Enhanced Binlog turned on
+----------------------------+-----------+-----------+
6 rows in set (0.00 sec)
```
 On a restored or cloned DB cluster, binlog files aren't backed up when enhanced binlog is turned on\. To avoid discontinuity in the binlog data, the binlog files written before turning on the enhanced binlog are also not available\.   

```
mysql>show binary logs;
                      
+----------------------------+-----------+-----------+
| Log_name                   | File_size | Encrypted |
+----------------------------+-----------+-----------+
| mysql-bin-changelog.000001 |       156 | No        | --> New sequence of Binlog files
+----------------------------+-----------+-----------+ 
1 row in set (0.00 sec)
```

**Example Clone or restore operation performed when Enhanced Binlog is turned off**  
Source DB Cluster:  

```
mysql>show binary logs;
                                                
+----------------------------+-----------+-----------+
| Log_name                   | File_size | Encrypted |
+----------------------------+-----------+-----------+
| mysql-bin-changelog.000001 |       156 | No        |
| mysql-bin-changelog.000002 |       156 | No        | --> Enhanced Binlog enabled
| mysql-bin-changelog.000003 |       156 | No        | --> Enhanced Binlog enabled
| mysql-bin-changelog.000004 |       156 | No        | 
| mysql-bin-changelog.000005 |       156 | No        | 
| mysql-bin-changelog.000006 |       156 | No        |
+----------------------------+-----------+-----------+
6 rows in set (0.00 sec)
```
On a restored or cloned DB cluster, binlog files written after turning off the enhanced binlog are available\.  

```
mysql>show binary logs;
                      
+----------------------------+-----------+-----------+
| Log_name                   | File_size | Encrypted |
+----------------------------+-----------+-----------+
| mysql-bin-changelog.000004 |       156 | No        | 
| mysql-bin-changelog.000005 |       156 | No        | 
| mysql-bin-changelog.000006 |       156 | No        |
+----------------------------+-----------+-----------+
1 row in set (0.00 sec)
```

**On an Amazon Aurora global database**

On an Amazon Aurora global database, the binlog data of the primary DB cluster isn't replicated to the secondary DB clusters\. After a cross\-Region failover process, the binlog data isn't available in the newly promoted primary DB cluster\. If binlog is turned on, the newly promoted DB cluster starts its own sequence of binlog files, starting from 1 \(mysql\-bin\-changelog\.000001\)\.

To turn on enhanced binlog after failover, you must set the required DB cluster parameters on the secondary DB cluster\. For more information, see [Configuring enhanced binlog parameters](#AuroraMySQL.Enhanced.binlog.enhancedbinlog.parameters)\.

**Example Global database failover operation is performed when enhanced binlog is turned on**  
Old primary DB Cluster \(before failover\):  

```
mysql>show binary logs;
                  
+----------------------------+-----------+-----------+
| Log_name                   | File_size | Encrypted |
+----------------------------+-----------+-----------+
| mysql-bin-changelog.000001 |       156 | No        |
| mysql-bin-changelog.000002 |       156 | No        |
| mysql-bin-changelog.000003 |       156 | No        |
| mysql-bin-changelog.000004 |       156 | No        | --> Enhanced Binlog enabled
| mysql-bin-changelog.000005 |       156 | No        | --> Enhanced Binlog enabled
| mysql-bin-changelog.000006 |       156 | No        | --> Enhanced Binlog enabled
+----------------------------+-----------+-----------+
6 rows in set (0.00 sec)
```
New primary DB cluster \(after failover\):  
Binlog files aren't replicated to secondary regions when enhanced binlog is turned on\. To avoid discontinuity in the binlog data, the binlog files written before turning on the enhanced binlog aren't available\.  

```
mysql>show binary logs;
                      
+----------------------------+-----------+-----------+
| Log_name                   | File_size | Encrypted |
+----------------------------+-----------+-----------+
| mysql-bin-changelog.000001 |       156 | No        | --> Fresh sequence of Binlog files
+----------------------------+-----------+-----------+ 
1 row in set (0.00 sec)
```

**Example Global database failover operation is performed when enhanced binlog is turned off**  
Source DB Cluster:  

```
mysql>show binary logs;
                  
+----------------------------+-----------+-----------+
| Log_name                   | File_size | Encrypted |
+----------------------------+-----------+-----------+
| mysql-bin-changelog.000001 |       156 | No        |
| mysql-bin-changelog.000002 |       156 | No        | --> Enhanced Binlog enabled
| mysql-bin-changelog.000003 |       156 | No        | --> Enhanced Binlog enabled
| mysql-bin-changelog.000004 |       156 | No        | 
| mysql-bin-changelog.000005 |       156 | No        | 
| mysql-bin-changelog.000006 |       156 | No        |
+----------------------------+-----------+-----------+
6 rows in set (0.00 sec)
```
**Restored or cloned DB cluster:**  
Binlog files that are written after turning off the enhanced binlog are replicated and are available in the newly promoted DB cluster\.  

```
mysql>show binary logs;
                  
+----------------------------+-----------+-----------+
| Log_name                   | File_size | Encrypted |
+----------------------------+-----------+-----------+
| mysql-bin-changelog.000004 |       156 | No        | 
| mysql-bin-changelog.000005 |       156 | No        | 
| mysql-bin-changelog.000006 |       156 | No        |
+----------------------------+-----------+-----------+
3 rows in set (0.00 sec)
```

### Amazon CloudWatch metrics for enhanced binlog<a name="AuroraMySQL.Enhanced.binlog.cloudwatch.metrics"></a>

 The following Amazon CloudWatch metrics are published only when enhanced binlog is turned on\. 


|  CloudWatch metric  |  Description  |  Units  | 
| --- | --- | --- | 
|  ChangeLogBytesUsed  |  The amount of storage used by the enhanced binlog\.  |  Bytes  | 
|  ChangeLogReadIOPs  |  The number of read I/O operations performed in the enhanced binlog within a 5\-minute interval\.  |  Count per 5 minutes  | 
|  ChangeLogWriteIOPs  |  The number of write disk I/O operations performed in the enhanced binlog within a 5\-minute interval\.  |  Count per 5 minutes  | 

### Enhanced binlog limitations<a name="AuroraMySQL.Enhanced.binlog.limitations"></a>

 The following limitations apply to Amazon Aurora DB clusters when enhanced binlog is turned on\. 
+ Enhanced binlog is only supported on Aurora MySQL 3\.03\.1 version \(compatible with MySQL 8\.0\.26\) and higher\.
+ The enhanced binlog ﬁles written on the primary DB cluster aren't copied to the cloned or restored DB clusters\.
+ When used with Amazon Aurora global database, the enhanced binlog files of the primary DB cluster aren't replicated to the secondary DB clusters\. Therefore, after the failover process, the historical binlog data isn't available in the new primary DB cluster\.
+ The following binlog conﬁguration parameters are ignored:
  + `binlog_group_commit_sync_delay`
  + `binlog_group_commit_sync_no_delay_count`
  + `binlog_max_flush_queue_time`
+ You can't drop or rename a corrupted table in a database\. To drop these tables, you can contact AWS Support\.
+ The binlog I/O cache is disabled when enhanced binlog is turned on\. For more information, see [Optimizing binary log replication](#binlog-optimization)\.
**Note**  
Enhanced binlog provides similar read performance improvements as binlog I/O cache and better write performance improvements\. 
+ The backtrack feature is not supported\. Enhanced binlog can't be turned on in a DB cluster under the following conditions:
  + DB cluster with the backtrack feature currently enabled\.
  + DB cluster with the backtrack feature previously enabled but not disabled\.
  + DB cluster restored from a source DB cluster or a snapshot with the backtrack feature enabled\.