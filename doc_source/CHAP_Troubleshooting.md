# Troubleshooting for Aurora<a name="CHAP_Troubleshooting"></a>

Use the following sections to help troubleshoot problems you have with DB instances in Amazon RDS and Aurora\.

**Topics**
+ [Can't Connect to Amazon RDS DB Instance](#CHAP_Troubleshooting.Connecting)
+ [Amazon RDS Security Issues](#CHAP_Troubleshooting.Security)
+ [Resetting the DB Instance Owner Password](#CHAP_Troubleshooting.ResetPassword)
+ [Amazon RDS DB Instance Outage or Reboot](#CHAP_Troubleshooting.Reboots)
+ [Amazon RDS DB Parameter Changes Not Taking Effect](#CHAP_Troubleshooting.Parameters)
+ [Amazon Aurora MySQL Out of Memory Issues](#CHAP_Troubleshooting.AuroraMySQLOOM)
+ [Amazon Aurora MySQL Replication Issues](#CHAP_Troubleshooting.MySQL)
+ [No Space Left on Device](#CHAP_Troubleshooting.Aurora.NoSpaceLeft)

 For information about debugging problems using the Amazon RDS API, see [Troubleshooting Applications on Aurora](APITroubleshooting.md)\. 

## Can't Connect to Amazon RDS DB Instance<a name="CHAP_Troubleshooting.Connecting"></a>

When you can't connect to a DB instance, the following are common causes:
+ **Inbound rules** – The access rules enforced by your local firewall and the IP addresses authorized to access your DB instance might not match\. The problem is most likely the inbound rules in your security group\.

  By default, DB instances don't allow access\. Access is granted through a security group associated with the VPC that allows traffic into and out of the DB instance\. If necessary, add inbound and outbound rules for your particular situation to the security group\. You can specify an IP address, a range of IP addresses, or another VPC security group\.
**Note**  
When adding a new inbound rule, you can choose **My IP** for **Source** to allow access to the DB instance from the IP address detected in your browser\.

  For more information about setting up security groups, see [Provide Access to the DB Cluster in the VPC by Creating a Security Group](CHAP_SettingUp_Aurora.md#CHAP_SettingUp_Aurora.SecurityGroup)\.
**Note**  
Client connections from IP addresses within the range 169\.254\.0\.0/16 aren't permitted\. This is the Automatic Private IP Addressing Range \(APIPA\), which is used for local\-link addressing\.
+ **Public accessibility** – To connect to your DB instance from outside of the VPC, such as by using a client application, the instance must have a public IP address assigned to it\.

  To make the instance publicly accessible, modify it and choose **Yes** under **Public accessibility**\. For more information, see [Hiding a DB Instance in a VPC from the Internet](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.Hiding)\.
+ **Port** – The port that you specified when you created the DB instance can't be used to send or receive communications due to your local firewall restrictions\. To determine if your network allows the specified port to be used for inbound and outbound communication, check with your network administrator\.
+ **Availability** – For a newly created DB instance, the DB instance has a status of `creating` until the DB instance is ready to use\. When the state changes to `available`, you can connect to the DB instance\. Depending on the size of your DB instance, it can take up to 20 minutes before an instance is available\.
+ **Internet gateway** – For a DB instance to be publicly accessible, the subnets in its DB subnet group must have an internet gateway\.

**To configure an internet gateway for a subnet**

  1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

  1. In the navigation pane, choose **Databases**, and then choose the name of the DB instance\.

  1. In the **Connectivity & security** tab, write down the values of the VPC ID under **VPC** and the subnet ID under **Subnets**\.

  1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

  1. In the navigation pane, choose **Internet Gateways**\. Verify that there is an internet gateway attached to your VPC\. Otherwise, choose **Create Internet Gateway** to create an internet gateway\. Select the internet gateway, and then choose **Attach to VPC** and follow the directions to attach it to your VPC\.

  1. In the navigation pane, choose **Subnets**, and then select your subnet\.

  1. On the **Route Table** tab, verify that there is a route with `0.0.0.0/0` as the destination and the internet gateway for your VPC as the target\. If you're connecting to your instance using its IPv6 address, verify that there is a route for all IPv6 traffic \(`::/0`\) that points to the internet gateway\. Otherwise, do the following:

     1. Choose the ID of the route table \(rtb\-*xxxxxxxx*\) to navigate to the route table\.

     1. On the **Routes** tab, choose **Edit routes**\. Choose **Add route**, use `0.0.0.0/0` as the destination and the internet gateway as the target\. For IPv6, choose **Add route**, use `::/0` as the destination and the internet gateway as the target\.

     1. Choose **Save routes**\.

  For more information, see [Working with a DB Instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md)\.

### Testing a Connection to a DB Instance<a name="CHAP_Troubleshooting.Connecting.Test"></a>

You can test your connection to a DB instance using common Linux or Microsoft Windows tools\. 

From a Linux or Unix terminal, you can test the connection by entering the following \(replace `DB-instance-endpoint` with the endpoint and `port` with the port of your DB instance\)\.

```
nc -zv DB-instance-endpoint port 
```

For example, the following shows a sample command and the return value\.

```
nc -zv postgresql1.c6c8mn7fake0.us-west-2.rds.amazonaws.com 8299

  Connection to postgresql1.c6c8mn7fake0.us-west-2.rds.amazonaws.com 8299 port [tcp/vvr-data] succeeded!
```

Windows users can use Telnet to test the connection to a DB instance\. Telnet actions aren't supported other than for testing the connection\. If a connection is successful, the action returns no message\. If a connection isn't successful, you receive an error message such as the following\.

```
C:\>telnet sg-postgresql1.c6c8mntfake0.us-west-2.rds.amazonaws.com 819

  Connecting To sg-postgresql1.c6c8mntfake0.us-west-2.rds.amazonaws.com...Could not open
  connection to the host, on port 819: Connect failed
```

If Telnet actions return success, your security group is properly configured\.

**Note**  
Amazon RDS doesn't accept internet control message protocol \(ICMP\) traffic, including ping\.

### Troubleshooting Connection Authentication<a name="CHAP_Troubleshooting.Connecting.Authorization"></a>

If you can connect to your DB instance but you get authentication errors, you might want to reset the master user password for the DB instance\. You can do this by modifying the RDS instance\. 

## Amazon RDS Security Issues<a name="CHAP_Troubleshooting.Security"></a>

To avoid security issues, never use your master AWS user name and password for a user account\. Best practice is to use your master AWS account to create AWS Identity and Access Management \(IAM\) users and assign those to DB user accounts\. You can also use your master account to create other user accounts, if necessary\.

For more information on creating IAM users, see [Create an IAM User](CHAP_SettingUp_Aurora.md#CHAP_SettingUp_Aurora.IAM)\.

### Error Message "Failed to retrieve account attributes, certain console functions may be impaired\."<a name="CHAP_Troubleshooting.Security.AccountAttributes"></a>

You can get this error for several reasons\. It might be because your account is missing permissions, or your account hasn't been properly set up\. If your account is new, you might not have waited for the account to be ready\. If this is an existing account, you might lack permissions in your access policies to perform certain actions such as creating a DB instance\. To fix the issue, your IAM administrator needs to provide the necessary roles to your account\. For more information, see [the IAM documentation](https://docs.aws.amazon.com/IAM/latest/UserGuide/)\.

## Resetting the DB Instance Owner Password<a name="CHAP_Troubleshooting.ResetPassword"></a>

If you get locked out of your DB cluster, you can log in as the master user\. Then you can reset the credentials for other administrative users or roles\. If you can't log in as the master user, the AWS account owner can reset the master user password\. For details of which administrative accounts or roles you might need to reset, see [Master User Account Privileges](UsingWithRDS.MasterAccounts.md)\.

You can change the DB instance password by using the Amazon RDS console, the AWS CLI command [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html), or by using the [ModifyDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) API operation\. For more information about modifying a DB instance in a DB cluster, see [Modify a DB Instance in a DB Cluster](Aurora.Modifying.md#Aurora.Modifying.Instance)\.

## Amazon RDS DB Instance Outage or Reboot<a name="CHAP_Troubleshooting.Reboots"></a>

A DB instance outage can occur when a DB instance is rebooted\. It can also occur when the DB instance is put into a state that prevents access to it, and when the database is restarted\. A reboot can occur when you either manually reboot your DB instance or change a DB instance setting that requires a reboot before it can take effect\.

 A DB instance reboot occurs when you change a setting that requires a reboot, or when you manually cause a reboot\. A reboot can occur immediately if you change a setting and request that the change take effect immediately or it can occur during the DB instance's maintenance window\.

 A DB instance reboot occurs immediately when one of the following occurs:
+ You change the backup retention period for a DB instance from 0 to a nonzero value or from a nonzero value to 0 and set **Apply Immediately** to `true`\. 
+ You change the DB instance class, and **Apply Immediately** is set to `true`\. 

A DB instance reboot occurs during the maintenance window when one of the following occurs:
+ You change the backup retention period for a DB instance from 0 to a nonzero value or from a nonzero value to 0, and **Apply Immediately** is set to `false`\. 
+ You change the DB instance class, and **Apply Immediately** is set to `false`\.

When you change a static parameter in a DB parameter group, the change doesn't take effect until the DB instance associated with the parameter group is rebooted\. The change requires a manual reboot\. The DB instance isn't automatically rebooted during the maintenance window\.

## Amazon RDS DB Parameter Changes Not Taking Effect<a name="CHAP_Troubleshooting.Parameters"></a>

In some cases, you might change a parameter in a DB parameter group but don't see the changes take effect\. If so, you likely need to reboot the DB instance associated with the DB parameter group\. When you change a dynamic parameter, the change takes effect immediately\. When you change a static parameter, the change doesn't take effect until you reboot the DB instance associated with the parameter group\.

You can reboot a DB instance using the RDS console or explicitly calling the [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RebootDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RebootDBInstance.html) API operation \(without failover, if the DB instance is in a Multi\-AZ deployment\)\. The requirement to reboot the associated DB instance after a static parameter change helps mitigate the risk of a parameter misconfiguration affecting an API call\. An example of this might be calling `ModifyDBInstance` to change the DB instance class\. For more information, see [Modifying Parameters in a DB Parameter Group](USER_WorkingWithParamGroups.md#USER_WorkingWithParamGroups.Modifying)\.

## Amazon Aurora MySQL Out of Memory Issues<a name="CHAP_Troubleshooting.AuroraMySQLOOM"></a>

The Aurora MySQL `aurora_oom_response` instance\-level parameter can enable the DB instance to monitor the system memory and estimate the memory consumed by various statements and connections\. If the system runs low on memory, it can perform a list of actions to release that memory in an attempt to avoid out\-of\-memory \(OOM\) and database restart\. The instance\-level parameter takes a string of comma\-separated actions that a DB instance should take when its memory is low\. Valid actions include `print`, `tune`, `decline`, `kill_query`, or any combination of these\. An empty string means that no action should be taken and effectively disables the feature\.

The following are usage examples for the `aurora_oom_response` parameter:
+ `print` – Only prints the queries taking high amount of memory\.
+ `tune` – Tunes the internal table caches to release some memory back to the system\.
+ `decline` – Declines new queries once the instance is low on memory\.
+ `kill_query` – Ends the queries in descending order of memory consumption until the instance memory surfaces above the low threshold\. Data definition language \(DDL\) statements aren't ended\.
+ `print, tune` – Performs actions described for both `print` and `tune`\.
+ `tune, decline, kill_query` – Performs the actions described for `tune`, `decline`, and `kill_query`\.

For the db\.t2\.small DB instance class, the `aurora_oom_response` parameter is set to `print, tune` by default\. For all other DB instance classes, the parameter value is empty by default \(disabled\)\.

## Amazon Aurora MySQL Replication Issues<a name="CHAP_Troubleshooting.MySQL"></a>

Some MySQL replication issues also apply to Aurora MySQL\. You can diagnose and correct these\.

**Topics**
+ [Diagnosing and Resolving Lag Between Read Replicas](#CHAP_Troubleshooting.MySQL.ReplicaLag)
+ [Diagnosing and Resolving a MySQL Read Replication Failure](#CHAP_Troubleshooting.MySQL.RR)
+ [Replication Stopped Error](#CHAP_Troubleshooting.MySQL.ReplicationStopped)

### Diagnosing and Resolving Lag Between Read Replicas<a name="CHAP_Troubleshooting.MySQL.ReplicaLag"></a>

After you create a MySQL read replica and the replica is available, Amazon RDS first replicates the changes made to the source DB instance from the time the read replica create operation started\. During this phase, the replication lag time for the read replica is greater than 0\. You can monitor this lag time in Amazon CloudWatch by viewing the Amazon RDS `AuroraBinlogReplicaLag` metric\.

The `AuroraBinlogReplicaLag` metric reports the value of the `Seconds_Behind_Master` field of the MySQL `SHOW SLAVE STATUS` command\. For more information, see [SHOW SLAVE STATUS](https://dev.mysql.com/doc/refman/8.0/en/show-slave-status.html)\. When the `AuroraBinlogReplicaLag` metric reaches 0, the replica has caught up to the source DB instance\. If the `AuroraBinlogReplicaLag` metric returns \-1, replication might not be active\. To troubleshoot a replication error, see [Diagnosing and Resolving a MySQL Read Replication Failure](#CHAP_Troubleshooting.MySQL.RR)\. A `AuroraBinlogReplicaLag` value of \-1 can also mean that the `Seconds_Behind_Master` value can't be determined or is `NULL`\.

The `AuroraBinlogReplicaLag` metric returns \-1 during a network outage or when a patch is applied during the maintenance window\. In this case, wait for network connectivity to be restored or for the maintenance window to end before you check the `AuroraBinlogReplicaLag` metric again\.

The MySQL read replication technology is asynchronous\. Thus, you can expect occasional increases for the `BinLogDiskUsage` metric on the source DB instance and for the `AuroraBinlogReplicaLag` metric on the read replica\. For example, consider a situation where a high volume of write operations to the source DB instance occur in parallel\. At the same time, write operations to the read replica are serialized using a single I/O thread\. Such a situation can lead to a lag between the source instance and read replica\. 

For more information about read replicas and MySQL, see [Replication Implementation Details](https://dev.mysql.com/doc/refman/8.0/en/replication-implementation-details.html) in the MySQL documentation\. 

You can reduce the lag between updates to a source DB instance and the subsequent updates to the read replica by doing the following:
+ Set the DB instance class of the read replica to have a storage size comparable to that of the source DB instance\.
+ Make sure that parameter settings in the DB parameter groups used by the source DB instance and the read replica are compatible\. For more information and an example, see the discussion of the `max_allowed_packet` parameter in the next section\.
+ Disable the query cache\. For tables that are modified often, using the query cache can increase replica lag because the cache is locked and refreshed often\. If this is the case, you might see less replica lag if you disable the query cache\. You can disable the query cache by setting the `query_cache_type parameter` to 0 in the DB parameter group for the DB instance\. For more information on the query cache, see [Query Cache Configuration](https://dev.mysql.com/doc/refman/5.7/en/query-cache-configuration.html)\.
+ Warm the buffer pool on the read replica for InnoDB for MySQL\. For example, suppose that you have a small set of tables that are being updated often and you're using the InnoDB or XtraDB table schema\. In this case, dump those tables on the read replica\. Doing this causes the database engine to scan through the rows of those tables from the disk and then cache them in the buffer pool\. This approach can reduce replica lag\. The following shows an example\.

  For Linux, macOS, or Unix:

  ```
  PROMPT> mysqldump \
      -h <endpoint> \
      --port=<port> \
      -u=<username> \
      -p <password> \
      database_name table1 table2 > /dev/null
  ```

  For Windows:

  ```
  PROMPT> mysqldump ^
      -h <endpoint> ^
      --port=<port> ^
      -u=<username> ^
      -p <password> ^
      database_name table1 table2 > /dev/null
  ```

### Diagnosing and Resolving a MySQL Read Replication Failure<a name="CHAP_Troubleshooting.MySQL.RR"></a>

Amazon RDS monitors the replication status of your read replicas and updates the **Replication State** field of the read replica instance to `Error` if replication stops for any reason\. You can review the details of the associated error thrown by the MySQL engines by viewing the **Replication Error** field\. Events that indicate the status of the read replica are also generated, including [RDS-EVENT-0045](USER_Events.md#RDS-EVENT-0045), [RDS-EVENT-0046](USER_Events.md#RDS-EVENT-0046), and [RDS-EVENT-0047](USER_Events.md#RDS-EVENT-0047)\. For more information about events and subscribing to events, see [Using Amazon RDS Event Notification](USER_Events.md)\. If a MySQL error message is returned, check the error in the [MySQL error message documentation](https://dev.mysql.com/doc/refman/8.0/en/server-error-reference.html)\. 

Common situations that can cause replication errors include the following:
+ The value for the `max_allowed_packet` parameter for a read replica is less than the `max_allowed_packet` parameter for the source DB instance\. 

  The `max_allowed_packet` parameter is a custom parameter that you can set in a DB parameter group\. The `max_allowed_packet` parameter is used to specify the maximum size of data manipulation language \(DML\) that can be run on the database\. If the `max_allowed_packet` value for the source DB instance is smaller than the `max_allowed_packet` value for the read replica, the replication process can throw an error and stop replication\. The most common error is `packet bigger than 'max_allowed_packet' bytes`\. You can fix the error by having the source and read replica use DB parameter groups with the same `max_allowed_packet` parameter values\.
+ Writing to tables on a read replica\. If you're creating indexes on a read replica, you need to have the `read_only` parameter set to *0* to create the indexes\. If you're writing to tables on the read replica, it can break replication\.
+ Using a nontransactional storage engine such as MyISAM\. Read replicas require a transactional storage engine\. Replication is only supported for the following storage engines: InnoDB for MySQL, InnoDB for MariaDB 10\.2 or higher, or XtraDB for MariaDB 10\.1 or lower\.

  You can convert a MyISAM table to InnoDB with the following command:

  `alter table <schema>.<table_name> engine=innodb;`
+ Using unsafe nondeterministic queries such as `SYSDATE()`\. For more information, see [Determination of Safe and Unsafe Statements in Binary Logging](https://dev.mysql.com/doc/refman/8.0/en/replication-rbr-safe-unsafe.html) in the MySQL documentation\. 

The following steps can help resolve your replication error: 
+ If you encounter a logical error and you can safely skip the error, follow the steps described in [Skipping the Current Replication Error](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Appendix.MySQL.CommonDBATasks.SkipError.html)\. Your Aurora MySQL DB instance must be running a version that includes the `mysql_rds_skip_repl_error` procedure\. For more information, see [mysql\_rds\_skip\_repl\_error](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/mysql_rds_skip_repl_error.html)\.
+ If you encounter a binary log \(binlog\) position issue, you can change the replica replay position with the `mysql_rds_next_master_log` command\. Your Aurora MySQL DB instance must be running a version that supports the `mysql_rds_next_master_log` command to change the replica replay position\. For version information, see [mysql\_rds\_next\_master\_log](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/mysql_rds_next_master_log.html)\.
+ If you encounter a temporary performance issue due to high DML load, you can set the `innodb_flush_log_at_trx_commit` parameter to 2 in the DB parameter group on the read replica\. Doing this can help the read replica catch up, though it temporarily reduces atomicity, consistency, isolation, and durability \(ACID\)\.
+ You can delete the read replica and create an instance using the same DB instance identifier so that the endpoint remains the same as that of your old read replica\.

If a replication error is fixed, the **Replication State** changes to **replicating**\. For more information, see [Troubleshooting a MySQL Read Replica Problem](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ReadRepl.Troubleshooting.html)\.

### Replication Stopped Error<a name="CHAP_Troubleshooting.MySQL.ReplicationStopped"></a>

When you call the `mysql.rds_skip_repl_error` command, you might receive the following error message: `Slave is down or disabled.`

This error message appears because replication is stopped and can't be restarted\.

If you need to skip a large number of errors, the replication lag can increase beyond the default retention period for binary log files\. In this case, you might encounter a fatal error due to binary log files being purged before they have been replayed on the replica\. This purge causes replication to stop, and you can no longer call the `mysql.rds_skip_repl_error` command to skip replication errors\. 

You can mitigate this issue by increasing the number of hours that binary log files are retained on your replication source\. After you have increased the binlog retention time, you can restart replication and call the `mysql.rds_skip_repl_error` command as needed\.

To set the binlog retention time, use the [mysql\_rds\_set\_configuration](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ReadRepl.Troubleshooting.html) procedure\. Specify a configuration parameter of 'binlog retention hours' along with the number of hours to retain binlog files on the DB cluster, up to 720 \(30 days\)\. The following example sets the retention period for binlog files to 48 hours\.

```
CALL mysql.rds_set_configuration('binlog retention hours', 48);
```

## No Space Left on Device<a name="CHAP_Troubleshooting.Aurora.NoSpaceLeft"></a>

You might encounter one of the following error messages\.
+ From Amazon Aurora MySQL:

  ```
  ERROR 3 (HY000): Error writing file '/rdsdbdata/tmp/XXXXXXXX' (Errcode: 28 - No space left on device)
  ```
+ From Amazon Aurora PostgreSQL:

  ```
  ERROR: could not write block XXXXXXXX of temporary file: No space left on device.
  ```

Each DB instance in an Amazon Aurora DB cluster uses local solid state drive \(SSD\) storage to store temporary tables for a session\. This local storage for temporary tables doesn't automatically grow like the Aurora cluster volume\. Instead, the amount of local storage is limited\. The limit is based on the DB instance class for DB instances in your DB cluster\. 

To show the amount of storage available for temporary tables and logs, you can use the CloudWatch metric `FreeLocalStorage`\. This metric is for per\-instance temporary volumes, not the cluster volume\. For more information on available metrics, see [Monitoring Amazon Aurora DB Cluster Metrics](Aurora.Monitoring.md)\.

In some cases, you can't modify your workload to reduce the amount temporary storage required\. If so, modify your DB instances to use a DB instance class that has more local SSD storage\. For more information, see [DB Instance Classes](Concepts.DBInstanceClass.md)\. 

For more troubleshooting information, see [ What is stored in Aurora for MySQL local storage, and how can I troubleshoot local storage issues?](https://aws.amazon.com/premiumsupport/knowledge-center/aurora-mysql-local-storage/) or [What is stored in Amazon Aurora for PostgreSQL storage, and how can I troubleshoot storage issues?](https://aws.amazon.com/premiumsupport/knowledge-center/postgresql-aurora-storage-issue/)\. 