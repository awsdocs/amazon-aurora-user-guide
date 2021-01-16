# Replication between Aurora and MySQL or between Aurora and another Aurora DB cluster \(binary log replication\)<a name="AuroraMySQL.Replication.MySQL"></a>

Because Amazon Aurora MySQL is compatible with MySQL, you can set up replication between a MySQL database and an Amazon Aurora MySQL DB cluster\. This type of replication uses the MySQL binary log replication, also referred to as *binlog replication*\. If you use binary log replication with Aurora, we recommend that your MySQL database run MySQL version 5\.5 or later\. You can set up replication where your Aurora MySQL DB cluster is the replication source or the replica\. You can replicate with an Amazon RDS MySQL DB instance, a MySQL database external to Amazon RDS, or another Aurora MySQL DB cluster\. 

You can also replicate with an Amazon RDS MySQL DB instance or Aurora MySQL DB cluster in another AWS Region\. When you're performing replication across AWS Regions, ensure that your DB clusters and DB instances are publicly accessible\. Aurora MySQL DB clusters must be part of a public subnet in your VPC\.

If you want to configure replication between an Aurora MySQL DB cluster and an Aurora MySQL DB cluster in another region, you can create an Aurora MySQL DB cluster as a read replica in a different AWS Region than the source DB cluster\. For more information, see [Replicating Amazon Aurora MySQL DB clusters across AWS Regions](AuroraMySQL.Replication.CrossRegion.md)\.

 With Aurora MySQL 2\.04 and higher, you can replicate between Aurora MySQL and an external source or target that uses global transaction identifiers \(GTIDs\) for replication\. Ensure that the GTID\-related parameters in the Aurora MySQL DB cluster have settings that are compatible with the GTID status of the external database\. To learn how to do this, see [Using GTID\-based replication for Aurora MySQL](mysql-replication-gtid.md)\. 

**Warning**  
When you replicate between Aurora MySQL and MySQL, ensure that you use only InnoDB tables\. If you have MyISAM tables that you want to replicate, you can convert them to InnoDB before setting up replication with the following command\.  

```
alter table <schema>.<table_name> engine=innodb, algorithm=copy;
```

Setting up MySQL replication with Aurora MySQL involves the following steps, which are discussed in detail following in this topic:

[1\. Enable binary logging on the replication source](#AuroraMySQL.Replication.MySQL.EnableBinlog)

[2\. Retain binary logs on the replication source until no longer needed](#AuroraMySQL.Replication.MySQL.RetainBinlogs)

[3\. Create a snapshot of your replication source](#AuroraMySQL.Replication.MySQL.CreateSnapshot)

[4\. Load the snapshot into your replica target](#AuroraMySQL.Replication.MySQL.LoadSnapshot)

[5\. Enable replication on your replica target](#AuroraMySQL.Replication.MySQL.EnableReplication)

[6\. Monitor your replica](#AuroraMySQL.Replication.MySQL.Monitor)

## Setting up replication with MySQL or another Aurora DB cluster<a name="AuroraMySQL.Replication.MySQL.SettingUp"></a>

To set up Aurora replication with MySQL, take the following steps\. 

### 1\. Enable binary logging on the replication source<a name="AuroraMySQL.Replication.MySQL.EnableBinlog"></a>

Find instructions on how to enable binary logging on the replication source for your database engine following\.


| Database engine | Instructions | 
| --- | --- | 
|  Aurora  |  **To enable binary logging on an Aurora MySQL DB cluster** Set the `binlog_format` parameter to `ROW`, `STATEMENT`, or `MIXED`\. `MIXED` is recommended unless you have a need for a specific binlog format\. The `binlog_format` parameter is a cluster\-level parameter that is in the default cluster parameter group\. If you are changing the `log_bin` parameter from `OFF` to another value, then you need to reboot your Aurora DB cluster for the change to take effect\. For more information, see [Amazon Aurora DB cluster and DB instance parameters](USER_WorkingWithParamGroups.md#Aurora.Managing.ParameterGroups) and [Working with DB parameter groups and DB cluster parameter groups](USER_WorkingWithParamGroups.md)\.   | 
|  RDS MySQL  |  **To enable binary logging on an Amazon RDS DB instance** You cannot enable binary logging directly for an Amazon RDS DB instance, but you can enable it by doing one of the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Replication.MySQL.html)  | 
|  MySQL \(external\)  |  **To set up encrypted replication** To replicate data securely with Aurora MySQL version 5\.6, you can use encrypted replication\. Currently, encrypted replication with an external MySQL database is only supported for Aurora MySQL version 5\.6\.  If you don't need to use encrypted replication, you can skip these steps\.  The following are prerequisites for using encrypted replication: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Replication.MySQL.html) During encrypted replication, the Aurora MySQL DB cluster acts a client to the MySQL database server\. The certificates and keys for the Aurora MySQL client are in files in \.pem format\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Replication.MySQL.html) **To enable binary logging on an external MySQL database** [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Replication.MySQL.html)  | 

### 2\. Retain binary logs on the replication source until no longer needed<a name="AuroraMySQL.Replication.MySQL.RetainBinlogs"></a>

When you use MySQL binary log replication, Amazon RDS doesn't manage the replication process\. As a result, you need to ensure that the binlog files on your replication source are retained until after the changes have been applied to the replica\. This maintenance helps ensure that you can restore your source database in the event of a failure\.

Find instructions on how to retain binary logs for your database engine following\.


| Database engine | Instructions | 
| --- | --- | 
|  Aurora  |  **To retain binary logs on an Aurora MySQL DB cluster** You do not have access to the binlog files for an Aurora MySQL DB cluster\. As a result, you must choose a time frame to retain the binlog files on your replication source long enough to ensure that the changes have been applied to your replica before the binlog file is deleted by Amazon RDS\. You can retain binlog files on an Aurora MySQL DB cluster for up to 90 days\. If you are setting up replication with a MySQL database or RDS MySQL DB instance as the replica, and the database that you are creating a replica for is very large, choose a large time frame to retain binlog files until the initial copy of the database to the replica is complete and the replica lag has reached 0\. To set the binary log retention time frame, use the [ mysql\_rds\_set\_configuration](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/mysql_rds_set_configuration.html) procedure and specify a configuration parameter of `'binlog retention hours'` along with the number of hours to retain binlog files on the DB cluster, up to 2160 \(90 days\)\. The following example that sets the retention period for binlog files to 6 days: <pre>CALL mysql.rds_set_configuration('binlog retention hours', 144);</pre> After replication has been started, you can verify that changes have been applied to your replica by running the `SHOW SLAVE STATUS` command on your replica and checking the `Seconds behind master` field\. If the `Seconds behind master` field is 0, then there is no replica lag\. When there is no replica lag, reduce the length of time that binlog files are retained by setting the `binlog retention hours` configuration parameter to a smaller time frame\. If you specify a value for `'binlog retention hours'` that is higher than 2160, then 2160 is used\.  | 
|  RDS MySQL  |  **To retain binary logs on an Amazon RDS DB instance** You can retain binary log files on an Amazon RDS DB instance by setting the binlog retention hours just as with an Aurora MySQL DB cluster, described in the previous section\. You can also retain binlog files on an Amazon RDS DB instance by creating a read replica for the DB instance\. This read replica is temporary and solely for the purpose of retaining binlog files\. After the read replica has been created, call the [ mysql\_rds\_stop\_replication](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/mysql_rds_stop_replication.html) procedure on the read replica \(the `mysql.rds_stop_replication` procedure is only available for MySQL versions 5\.5, 5\.6 and later, and 5\.7 and later\)\. While replication is stopped, Amazon RDS doesn't delete any of the binlog files on the replication source\. After you have set up replication with your permanent replica, you can delete the read replica when the replica lag \(`Seconds behind master` field\) between your replication source and your permanent replica reaches 0\.  | 
|  MySQL \(external\)  |  **To retain binary logs on an external MySQL database** Because binlog files on an external MySQL database are not managed by Amazon RDS, they are retained until you delete them\. After replication has been started, you can verify that changes have been applied to your replica by running the `SHOW SLAVE STATUS` command on your replica and checking the `Seconds behind master` field\. If the `Seconds behind master` field is 0, then there is no replica lag\. When there is no replica lag, you can delete old binlog files\.  | 

### 3\. Create a snapshot of your replication source<a name="AuroraMySQL.Replication.MySQL.CreateSnapshot"></a>

You use a snapshot of your replication source to load a baseline copy of your data onto your replica and then start replicating from that point on\.

Find instructions on how to create a snapshot of your replication source for your database engine following\.


| Database engine | Instructions | 
| --- | --- | 
|  Aurora  |  **To create a snapshot of an Aurora MySQL DB cluster** [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Replication.MySQL.html)  | 
|  RDS MySQL  |  **To create a snapshot of an Amazon RDS DB instance** [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Replication.MySQL.html)  | 
|  MySQL \(external\)  |  **To create a snapshot of an external MySQL database** [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Replication.MySQL.html)  | 

### 4\. Load the snapshot into your replica target<a name="AuroraMySQL.Replication.MySQL.LoadSnapshot"></a>

If you plan to load data from a dump of a MySQL database that is external to Amazon RDS, then you might want to create an EC2 instance to copy the dump files to, and then load the data into your DB cluster or DB instance from that EC2 instance\. Using this approach, you can compress the dump file\(s\) before copying them to the EC2 instance in order to reduce the network costs associated with copying data to Amazon RDS\. You can also encrypt the dump file or files to secure the data as it is being transferred across the network\.

Find instructions on how to load the snapshot of your replication source into your replica target for your database engine following\.


| Database engine | Instructions | 
| --- | --- | 
|  Aurora  |  **To load a snapshot into an Aurora MySQL DB cluster** [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Replication.MySQL.html)  | 
|  RDS MySQL  |  **To load a snapshot into an Amazon RDS DB instance** [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Replication.MySQL.html)  | 
|  MySQL \(external\)  |  **To load a snapshot into an external MySQL database** You cannot load a DB snapshot or a DB cluster snapshot into an external MySQL database\. Instead, you must use the output from the `mysqldump` command\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Replication.MySQL.html)  | 

### 5\. Enable replication on your replica target<a name="AuroraMySQL.Replication.MySQL.EnableReplication"></a>

Before you enable replication, we recommend that you take a manual snapshot of the Aurora MySQL DB cluster or RDS MySQL DB instance replica target\. If a problem arises and you need to re\-establish replication with the DB cluster or DB instance replica target, you can restore the DB cluster or DB instance from this snapshot instead of having to import the data into your replica target again\.

Also, create a user ID that is used solely for replication\. The following is an example\.

```
mysql> CREATE USER 'repl_user'@'<domain_name>' IDENTIFIED BY '<password>';
```

The user requires the `REPLICATION CLIENT` and `REPLICATION SLAVE` privileges\. Grant these privileges to the user\.

```
GRANT REPLICATION CLIENT, REPLICATION SLAVE ON *.* TO 'repl_user'@'<domain_name>';
```

If you need to use encrypted replication, require SSL connections for the replication user\. For example, you can use one of the following statement to require SSL connections on the user account `repl_user`\.

```
GRANT USAGE ON *.* TO 'repl_user'@'<domain_name>' REQUIRE SSL;
```

**Note**  
If `REQUIRE SSL` isn't included, the replication connection might silently fall back to an unencrypted connection\.

Find instructions on how to enable replication for your database engine following\.


| Database engine | Instructions | 
| --- | --- | 
|  Aurora  |  **To enable replication from an Aurora MySQL DB cluster** [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Replication.MySQL.html)  | 
|  RDS MySQL  |  **To enable replication from an Amazon RDS DB instance** [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Replication.MySQL.html)  | 
|  MySQL \(external\)  |  **To enable replication from an external MySQL database** [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Replication.MySQL.html)  | 

### 6\. Monitor your replica<a name="AuroraMySQL.Replication.MySQL.Monitor"></a>

When you set up MySQL replication with an Aurora MySQL DB cluster, you must monitor failover events for the Aurora MySQL DB cluster when it is the replica target\. If a failover occurs, then the DB cluster that is your replica target might be recreated on a new host with a different network address\. For information on how to monitor failover events, see [Using Amazon RDS event notification](USER_Events.md)\. 

You can also monitor how far the replica target is behind the replication source by connecting to the replica target and running the `SHOW SLAVE STATUS` command\. In the command output, the `Seconds Behind Master` field tells you how far the replica target is behind the source\.

## Stopping replication between Aurora and MySQL or between Aurora and another Aurora DB cluster<a name="AuroraMySQL.Replication.MySQL.Stopping"></a>

To stop binary log replication with a MySQL DB instance, external MySQL database, or another Aurora DB cluster, follow these steps, discussed in detail following in this topic\.

[1\. Stop binary log replication on the replica target](#AuroraMySQL.Replication.MySQL.Stopping.StopReplication)

[2\. Disable binary logging on the replication source](#AuroraMySQL.Replication.MySQL.Stopping.DisableBinaryLogging)

### 1\. Stop binary log replication on the replica target<a name="AuroraMySQL.Replication.MySQL.Stopping.StopReplication"></a>

Find instructions on how to stop binary log replication for your database engine following\.


| Database engine | Instructions | 
| --- | --- | 
|  Aurora  |  **To stop binary log replication on an Aurora MySQL DB cluster replica target** Connect to the Aurora DB cluster that is the replica target, and call the [ mysql\_rds\_stop\_replication](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/mysql_rds_stop_replication.html) procedure\. The `mysql.rds_stop_replication` procedure is only available for MySQL versions 5\.5 and later, 5\.6 and later, and 5\.7 and later\.  | 
|  RDS MySQL  |  **To stop binary log replication on an Amazon RDS DB instance** Connect to the RDS DB instance that is the replica target and call the [ mysql\_rds\_stop\_replication](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/mysql_rds_stop_replication.html) procedure\. The `mysql.rds_stop_replication` procedure is only available for MySQL versions 5\.5 and later, 5\.6 and later, and 5\.7 and later\.  | 
|  MySQL \(external\)  |  **To stop binary log replication on an external MySQL database** Connect to the MySQL database and call the `STOP REPLICATION` command\.  | 

### 2\. Disable binary logging on the replication source<a name="AuroraMySQL.Replication.MySQL.Stopping.DisableBinaryLogging"></a>

Find instructions on how to disable binary logging on the replication source for your database engine following\.


| Database engine | Instructions | 
| --- | --- | 
|  Aurora  |  **To disable binary logging on an Amazon Aurora DB cluster** [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Replication.MySQL.html)  | 
|  RDS MySQL  |  **To disable binary logging on an Amazon RDS DB instance** You cannot disable binary logging directly for an Amazon RDS DB instance, but you can disable it by doing the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Replication.MySQL.html)  | 
|  MySQL \(external\)  |  **To disable binary logging on an external MySQL database** Connect to the MySQL database and call the `STOP REPLICATION` command\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Replication.MySQL.html)  | 

## Using Amazon Aurora to scale reads for your MySQL database<a name="AuroraMySQL.Replication.ReadScaling"></a>

You can use Amazon Aurora with your MySQL DB instance to take advantage of the read scaling capabilities of Amazon Aurora and expand the read workload for your MySQL DB instance\. To use Aurora to read scale your MySQL DB instance, create an Amazon Aurora MySQL DB cluster and make it a read replica of your MySQL DB instance\. This applies to an Amazon RDS MySQL DB instance, or a MySQL database running external to Amazon RDS\.

For information on creating an Amazon Aurora DB cluster, see [Creating an Amazon Aurora DB cluster](Aurora.CreateInstance.md)\.

When you set up replication between your MySQL DB instance and your Amazon Aurora DB cluster, be sure to follow these guidelines:
+ Use the Amazon Aurora DB cluster endpoint address when you reference your Amazon Aurora MySQL DB cluster\. If a failover occurs, then the Aurora Replica that is promoted to the primary instance for the Aurora MySQL DB cluster continues to use the DB cluster endpoint address\.
+ Maintain the binlogs on your writer instance until you have verified that they have been applied to the Aurora Replica\. This maintenance ensures that you can restore your writer instance in the event of a failure\.

**Important**  
When using self\-managed replication, you're responsible for monitoring and resolving any replication issues that may occur\. For more information, see [Diagnosing and resolving lag between read replicas](CHAP_Troubleshooting.md#CHAP_Troubleshooting.MySQL.ReplicaLag)\.

**Note**  
The permissions required to start replication on an Amazon Aurora MySQL DB cluster are restricted and not available to your Amazon RDS master user\. Because of this, you must use the Amazon RDS [ mysql\_rds\_set\_external\_master](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/mysql_rds_set_external_master.html) and [ mysql\_rds\_start\_replication](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/mysql_rds_start_replication.html) procedures to set up replication between your Amazon Aurora MySQL DB cluster and your MySQL DB instance\.

### Start replication between an external source instance and a MySQL DB instance on Amazon RDS<a name="AuroraMySQL.Replication.ReadScaling.Procedure"></a>

1. Make the source MySQL DB instance read\-only:

   ```
   mysql> FLUSH TABLES WITH READ LOCK;
   mysql> SET GLOBAL read_only = ON;
   ```

1. Run the `SHOW MASTER STATUS` command on the source MySQL DB instance to determine the binlog location\. You receive output similar to the following example:

   ```
   File                        Position
   ------------------------------------
    mysql-bin-changelog.000031      107
   ------------------------------------
   ```

1. Copy the database from the external MySQL DB instance to the Amazon Aurora MySQL DB cluster using `mysqldump`\. For very large databases, you might want to use the procedure in [ Importing data to an Amazon RDS MySQL or MariaDB DB instance with reduced downtime](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/MySQL.Procedural.Importing.NonRDSRepl.html) in the * Amazon Relational Database Service User Guide*\.

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

   For more information on making backups for use with replication, see [http://dev.mysql.com/doc/refman/5.6/en/replication-solutions-backups-read-only.html](http://dev.mysql.com/doc/refman/5.6/en/replication-solutions-backups-read-only.html) in the MySQL documentation\.

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

1. Make the Amazon Aurora DB cluster the replica\. Connect to the Amazon Aurora DB cluster as the master user and identify the source MySQL database as the replication master by using the [ mysql\_rds\_set\_external\_master](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/mysql_rds_set_external_master.html) procedure\. Use the master log file name and master log position that you determined in Step 2\. The following is an example\.

   ```
   CALL mysql.rds_set_external_master ('mymasterserver.mydomain.com', 3306,
       'repl_user', '<password>', 'mysql-bin-changelog.000031', 107, 0);
   ```

1. On the Amazon Aurora DB cluster, issue the [ mysql\_rds\_start\_replication](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/mysql_rds_start_replication.html) procedure to start replication\.

   ```
   CALL mysql.rds_start_replication; 
   ```

After you have established replication between your source MySQL DB instance and your Amazon Aurora DB cluster, you can add Aurora Replicas to your Amazon Aurora DB cluster\. You can then connect to the Aurora Replicas to read scale your data\. For information on creating an Aurora Replica, see [Adding Aurora replicas to a DB cluster](aurora-replicas-adding.md)\.

## Optimizing binary log replication<a name="binlog-optimization"></a>

 Following, you can learn how to optimize binary log replication performance and troubleshoot related issues in Aurora MySQL\. 

**Tip**  
 This discussion presumes that you are familiar with the MySQL binary log replication mechanism and how it works\. For background information, see [Replication Implementation](https://dev.mysql.com/doc/refman/5.7/en/replication-implementation.html) in the MySQL documentation\. 

 To optimize binary log replication for Aurora MySQL, you adjust the following cluster\-level optimization parameters\. These parameters help you to specify the right balance between latency on the binlog source instance and replication lag\. 
+  `aurora_binlog_use_large_read_buffer` 
+  `aurora_binlog_read_buffer_size` 
+  `aurora_binlog_replication_max_yield_seconds` 

**Note**  
 For MySQL 5\.7\-compatible clusters, you can use these parameters in Aurora MySQL version 2\.04\.5 and later\.   
 For MySQL 5\.6\-compatible clusters, you can use these parameters in Aurora MySQL version 1\.17\.6 and later\. 

**Topics**
+ [Overview of the large read buffer and max\-yield optimizations](#aurora_mysql_max_yield_overview)
+ [Related parameters](#aurora_mysql_max_yield_reference)
+ [Enabling binary log replication max\-yield mechanism](#aurora_mysql_max_yield_enabling)
+ [Turning off the binary log replication max\-yield optimization](#aurora_mysql_max_yield_disabling)
+ [Turning off the large read buffer](#aurora_mysql_large_read_buffer_disabling)

### Overview of the large read buffer and max\-yield optimizations<a name="aurora_mysql_max_yield_overview"></a>

 You might experience reduced binary log replication performance when the binary log dump thread accesses the Aurora cluster volume while the cluster processes a high number of transactions\. You can use the parameters `aurora_binlog_use_large_read_buffer`, `aurora_binlog_replication_max_yield_seconds`, and `aurora_binlog_read_buffer_size` to help minimize this type of contention\. 

 When `aurora_binlog_replication_max_yield_seconds` is set to greater than 0, and the current binlog file of the dump thread is active, binary log dump thread waits up to a specified number of seconds for the current binlog file to be filled by transactions\. This wait period avoids contention that can arise from replicating each binlog event individually\. However, doing so increases the replica lag for binary log replicas\. Those replicas can fall behind the source by the same number of seconds as the `aurora_binlog_replication_max_yield_seconds` setting\. 

 The current binlog file means the binlog file that the dump thread is currently reading to perform replication\. We consider that a binlog file is active when the binlog file is updating or open to be updated by incoming transactions\. After Aurora MySQL fills up the active binlog file, MySQL creates and switches to a new binlog file\. The old binlog file becomes inactive\. It isn't updated by incoming transactions any longer\. 

**Note**  
 Before adjusting these parameters, measure your transaction latency and throughput over time\. You might find that binary log replication performance is stable and has low latency even if there is occasional contention\. 

`aurora_binlog_use_large_read_buffer`  
 If this parameter is set to 1, Aurora MySQL optimizes binary log replication based on the settings of the parameters `aurora_binlog_read_buffer_size` and `aurora_binlog_replication_max_yield_seconds`\. If `aurora_binlog_use_large_read_buffer` is 0, Aurora MySQL ignores the values of the `aurora_binlog_read_buffer_size` and `aurora_binlog_replication_max_yield_seconds` parameters\. 

`aurora_binlog_read_buffer_size`  
 Binary log dump threads with larger read buffer minimize the number of read I/O operations by reading more events for each I/O\. The parameter `aurora_binlog_read_buffer_size` sets the read buffer size\. The large read buffer effectively reduces binlog contentions when updating transactions generate binlog events fast enough to fill up the read buffer size before the dump threads start to read them\.   
 This parameter only has an effect when the cluster also has the setting `aurora_binlog_use_large_read_buffer=1`\.   
 Increasing the size of the read buffer doesn't affect the performance of binary log replication\. Binary log dump threads don't wait for updating transactions to fill up the read buffer\. 

`aurora_binlog_replication_max_yield_seconds`  
 If your workload requires low transaction latency, and you can tolerate some replication lag, you can increase the `aurora_binlog_replication_max_yield_seconds` parameter\. This parameter controls the maximum yield property of binary log replication in your cluster\.   
 This parameter only has an effect when the cluster also has the setting `aurora_binlog_use_large_read_buffer=1`\. 

 Aurora MySQL recognizes any change to the `aurora_binlog_replication_max_yield_seconds` parameter value immediately\. You don't need to restart the DB instance\. However, when you enable this setting, the dump thread only starts to yield when the current binlog file reaches its maximum size of 128 MB and is rotated to a new file\. 

### Related parameters<a name="aurora_mysql_max_yield_reference"></a>

 Use the following DB cluster parameters to enable the binlog optimization\.  


**Binlog optimization parameters for Aurora MySQL version 2\.04\.5 and later**  

| Parameter | Default | Valid Values | Description | 
| --- | --- | --- | --- | 
| aurora\_binlog\_use\_large\_read\_buffer | 1 | 0, 1 |  Switch for turning on the feature of replication improvement\. When it is 1, the binary log dump thread uses aurora\_binlog\_read\_buffer\_size for binary log replication; otherwise default buffer size \(8K\) is used\.  | 
| aurora\_binlog\_read\_buffer\_size | 5242880 | 8192\-536870912 | Read buffer size used by binary log dump thread when the parameter aurora\_binlog\_use\_large\_read\_buffer is set to 1\. | 
| aurora\_binlog\_replication\_max\_yield\_seconds | 0 | 0\-36000 | For Aurora MySQL versions between 2\.04\.5 \- 2\.04\.8 and between 2\.05 \- 2\.08\.\* \(inclusive\), the maximum accepted value is 45\. You can tune it to a higher value on 2\.04\.9 and later versions of 2\.04\.\*, and on 2\.09 and later versions\. This parameter works only when the parameter aurora\_binlog\_use\_large\_read\_buffer is set to 1\. | 


**Binlog optimization parameters for Aurora MySQL version 1\.17\.6 and later**  

| Parameter | Default | Valid Values | Description | 
| --- | --- | --- | --- | 
| aurora\_binlog\_use\_large\_read\_buffer | 0 | 0, 1 | Switch for turning on the feature of replication improvement\. When it is 1, the binary log dump thread uses aurora\_binlog\_read\_buffer\_size for binary log replication\. Otherwise, the default buffer size \(8 KB\) is used\. | 
| aurora\_binlog\_read\_buffer\_size | 5242880 | 8192\-536870912 | Read buffer size used by binary log dump thread when the parameter aurora\_binlog\_use\_large\_read\_buffer is set to 1\. | 
| aurora\_binlog\_replication\_max\_yield\_seconds | 0 | 0\-36000 | Maximum seconds to yield when the binary log dump thread replicates the current binlog file \(the file used by foreground queries\) to replicas\. This parameter works only when the parameter aurora\_binlog\_use\_large\_read\_buffer is set to 1\. | 

### Enabling binary log replication max\-yield mechanism<a name="aurora_mysql_max_yield_enabling"></a>

 You can turn on the binary log replication max\-yield optimization as follows\. Doing so minimizes latency for transactions on the binlog source instance\. However, you might experience higher replication lag\. 

**To enable the max\-yield binlog optimization for an Aurora MySQL cluster**

1.  Create or edit a DB cluster parameter group using the following parameter settings: 
   +  `aurora_binlog_use_large_read_buffer`: turn on with a value of `ON` or 1\. 
   +  `aurora_binlog_replication_max_yield_seconds`: specify a value greater than 0\. 

1.  Associate the DB cluster parameter group with the Aurora MySQL cluster that works as the binlog source\. To do so, follow the procedures in [Working with DB parameter groups and DB cluster parameter groups](USER_WorkingWithParamGroups.md)\. 

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

### Turning off the binary log replication max\-yield optimization<a name="aurora_mysql_max_yield_disabling"></a>

 You can turn off the binary log replication max\-yield optimization as follows\. Doing so minimizes replication lag\. However, you might experience higher latency for transactions on the binlog source instance\. 

**To turn off the max\-yield optimization for an Aurora MySQL cluster**

1.  Make sure that the DB cluster parameter group associated with the Aurora MySQL cluster has `aurora_binlog_replication_max_yield_seconds` set to 0\. For more information about setting configuration parameters using parameter groups, see [Working with DB parameter groups and DB cluster parameter groups](USER_WorkingWithParamGroups.md)\. 

1. Confirm that the parameter change takes effect\. To do so, run the following query on the binlog source instance\.

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

### Turning off the large read buffer<a name="aurora_mysql_large_read_buffer_disabling"></a>

 You can disable the entire large read buffer feature as follows\. 

**To turn off the large binary log read buffer for an Aurora MySQL cluster**

1. Reset the `aurora_binlog_use_large_read_buffer` to `OFF` or 0\.

    Make sure that the DB cluster parameter group associated with the Aurora MySQL cluster has `aurora_binlog_use_large_read_buffer` set to 0\. For more information about setting configuration parameters using parameter groups, see [Working with DB parameter groups and DB cluster parameter groups](USER_WorkingWithParamGroups.md)\. 

1. On the binlog source instance, run the following query\.

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