# Amazon RDS event categories and event messages<a name="USER_Events.Messages"></a>

 Amazon RDS generates a significant number of events in categories that you can subscribe to using the Amazon RDS Console, AWS CLI, or the API\. Each category applies to a source type\.

**Topics**
+ [DB instance events](#USER_Events.Messages.instance)
+ [DB parameter group events](#USER_Events.Messages.parameter-group)
+ [DB security group events](#USER_Events.Messages.security-group)
+ [DB cluster events](#USER_Events.Messages.cluster)
+ [DB cluster snapshot events](#USER_Events.Messages.cluster-snapshot)

## DB instance events<a name="USER_Events.Messages.instance"></a>

The following table shows the event category and a list of events when a DB instance is the source type\.


|  Category  | Amazon RDS event ID |  Description  | 
| --- | --- | --- | 
|  availability  | RDS\-EVENT\-0006 |  The DB instance restarted\.  | 
|  availability  | RDS\-EVENT\-0004 |  DB instance shutdown\.  | 
|  availability  | RDS\-EVENT\-0022 |  An error has occurred while restarting MySQL or MariaDB\.  | 
|  backtrack  | RDS\-EVENT\-0131 |  The actual Backtrack window is smaller than the target Backtrack window you specified\. Consider reducing the number of hours in your target Backtrack window\. For more information about backtracking, see [Backtracking an Aurora DB cluster](AuroraMySQL.Managing.Backtrack.md)\.  | 
|  backtrack  | RDS\-EVENT\-0132 |  The actual Backtrack window is the same as the target Backtrack window\.  | 
|  backup  | RDS\-EVENT\-0001 |  Backing up the DB instance\.  | 
|  backup  | RDS\-EVENT\-0002 |  Finished DB Instance backup\.  | 
|  backup  | RDS\-EVENT\-0075 |  RDS finished creating a user\-initiated snapshot\.  | 
|  configuration change  | RDS\-EVENT\-0009 |  The DB instance has been added to a security group\.  | 
|  configuration change  | RDS\-EVENT\-0024 |  The DB instance is being converted to a Multi\-AZ DB instance\.  | 
|  configuration change  | RDS\-EVENT\-0030 |  The DB instance is being converted to a Single\-AZ DB instance\.  | 
|  configuration change  | RDS\-EVENT\-0012 |  Applying modification to database instance class\.   | 
|  configuration change  | RDS\-EVENT\-0018 |  The current storage settings for this DB instance are being changed\.  | 
|  configuration change  | RDS\-EVENT\-0011 |  A parameter group for this DB instance has changed\.  | 
|  configuration change  | RDS\-EVENT\-0092 |  A parameter group for this DB instance has finished updating\.  | 
|  configuration change  | RDS\-EVENT\-0028 |  Automatic backups for this DB instance have been disabled\.  | 
|  configuration change  | RDS\-EVENT\-0032 |  Automatic backups for this DB instance have been enabled\.  | 
|  configuration change  | RDS\-EVENT\-0033 |  There are \[count\] users that match the master user name\. Users not tied to a specific host have been reset\.  | 
|  configuration change  | RDS\-EVENT\-0025 |  The DB instance has been converted to a Multi\-AZ DB instance\.  | 
|  configuration change  | RDS\-EVENT\-0029 |  The DB instance has been converted to a Single\-AZ DB instance\.  | 
|  configuration change  | RDS\-EVENT\-0014 |  The DB instance class for this DB instance has changed\.  | 
|  configuration change  | RDS\-EVENT\-0017 |  The storage settings for this DB instance have changed\.  | 
|  configuration change  | RDS\-EVENT\-0010 |  The DB instance has been removed from a security group\.  | 
|  configuration change  | RDS\-EVENT\-0016 |  The master password for the DB instance has been reset\.  | 
|  configuration change  | RDS\-EVENT\-0067 |  An attempt to reset the master password for the DB instance has failed\.  | 
|  configuration change  | RDS\-EVENT\-0078 |  The Enhanced Monitoring configuration has been changed\.  | 
|  creation  | RDS\-EVENT\-0005 |  DB instance created\.  | 
|  deletion  | RDS\-EVENT\-0003 |  The DB instance has been deleted\.  | 
|  failover  | RDS\-EVENT\-0034 |  Amazon RDS is not attempting a requested failover because a failover recently occurred on the DB instance\.  | 
|  failover  | RDS\-EVENT\-0013 |  A Multi\-AZ failover that resulted in the promotion of a standby instance has started\.  | 
|  failover  | RDS\-EVENT\-0015 |  A Multi\-AZ failover that resulted in the promotion of a standby instance is complete\. It may take several minutes for the DNS to transfer to the new primary DB instance\.  | 
|  failover  | RDS\-EVENT\-0065 |  The instance has recovered from a partial failover\.  | 
|  failover  | RDS\-EVENT\-0049 | A Multi\-AZ failover has completed\. | 
|  failover  | RDS\-EVENT\-0050 |  A Multi\-AZ activation has started after a successful instance recovery\.  | 
|  failover  | RDS\-EVENT\-0051 |  A Multi\-AZ activation is complete\. Your database should be accessible now\.  | 
|  failure  | RDS\-EVENT\-0031 |  The DB instance has failed due to an incompatible configuration or an underlying storage issue\. Begin a point\-in\-time\-restore for the DB instance\.  | 
|  failure  | RDS\-EVENT\-0036 |  The DB instance is in an incompatible network\. Some of the specified subnet IDs are invalid or do not exist\.  | 
|  failure  | RDS\-EVENT\-0035 |  The DB instance has invalid parameters\. For example, if the DB instance could not start because a memory\-related parameter is set too high for this instance class, the customer action would be to modify the memory parameter and reboot the DB instance\.  | 
|  failure  | RDS\-EVENT\-0058 |  Error while creating Statspack user account PERFSTAT\. Please drop the account before adding the Statspack option\.  | 
|  failure  | RDS\-EVENT\-0079 |  Enhanced Monitoring cannot be enabled without the enhanced monitoring IAM role\. For information on creating the enhanced monitoring IAM role, see [To create an IAM role for Amazon RDS enhanced monitoring](USER_Monitoring.OS.Enabling.md#USER_Monitoring.OS.IAMRole)\.  | 
|  failure  | RDS\-EVENT\-0080 |  Enhanced Monitoring was disabled due to an error making the configuration change\. It is likely that the enhanced monitoring IAM role is configured incorrectly\. For information on creating the enhanced monitoring IAM role, see [To create an IAM role for Amazon RDS enhanced monitoring](USER_Monitoring.OS.Enabling.md#USER_Monitoring.OS.IAMRole)\.  | 
|  failure  | RDS\-EVENT\-0082 |  Aurora was unable to copy backup data from an Amazon S3 bucket\. It is likely that the permissions for Aurora to access the Amazon S3 bucket are configured incorrectly\. For more information, see [Migrating data from MySQL by using an Amazon S3 bucket](AuroraMySQL.Migrating.ExtMySQL.md#AuroraMySQL.Migrating.ExtMySQL.S3) \.   | 
|  low storage  | RDS\-EVENT\-0089 |  The DB instance has consumed more than 90% of its allocated storage\. You can monitor the storage space for a DB instance using the **Free Storage Space** metric\.  | 
|  low storage  | RDS\-EVENT\-0007 |  The allocated storage for the DB instance has been consumed\. To resolve this issue, allocate additional storage for the DB instance\. For more information, see the [RDS FAQ](https://aws.amazon.com/rds/faqs/#20)\. You can monitor the storage space for a DB instance using the **Free Storage Space** metric\.  | 
|  maintenance  | RDS\-EVENT\-0026 |  Offline maintenance of the DB instance is taking place\. The DB instance is currently unavailable\.  | 
|  maintenance  | RDS\-EVENT\-0027 |  Offline maintenance of the DB instance is complete\. The DB instance is now available\.  | 
| maintenance | RDS\-EVENT\-0047 |  Patching of the DB instance has completed\.  | 
|  maintenance  | RDS\-EVENT\-0155 |  The DB instance has a DB engine minor version upgrade available\.  | 
| maintenance, notification | RDS\-EVENT\-0191 |  An Oracle time zone file update is available\. If you update your Oracle engine, Amazon RDS generates this event if you haven't chosen a time zone file upgrade and the database doesn’t use the latest DST time zone file available on the instance\.   | 
|  maintenance, notification  | RDS\-EVENT\-0192 |  The upgrade of your Oracle time zone file has begun\.   | 
|  maintenance, notification  | RDS\-EVENT\-0193 |  Your Oracle DB instance is using latest time zone file version, and either of the following is true: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/USER_Events.Messages.html)  | 
|  maintenance, notification  | RDS\-EVENT\-0194 |  The upgrade of your Oracle time zone file has completed\.   | 
|  maintenance, failure  | RDS\-EVENT\-0195 |  The upgrade of the time zone file failed\.  | 
| notification | RDS\-EVENT\-0044 | Operator\-issued notification\. For more information, see the event message\. | 
| notification | RDS\-EVENT\-0048 | Patching of the DB instance has been delayed\. | 
|  notification  | RDS\-EVENT\-0054 | The MySQL storage engine you are using is not InnoDB, which is the recommended MySQL storage engine for Amazon RDS\. For information about MySQL storage engines, see [ Supported storage engines for MySQL on Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/MySQL.Concepts.Storage.html)\. | 
|  notification  | RDS\-EVENT\-0055 |  The number of tables you have for your DB instance exceeds the recommended best practices for Amazon RDS\. Please reduce the number of tables on your DB instance\.     For information about recommended best practices, see [Best practices with Amazon Aurora](Aurora.BestPractices.md)\.  | 
|  notification  | RDS\-EVENT\-0056 |  The number of databases you have for your DB instance exceeds the recommended best practices for Amazon RDS\. Please reduce the number of databases on your DB instance\.     For information about recommended best practices, see [Best practices with Amazon Aurora](Aurora.BestPractices.md)\.  | 
|  notification  | RDS\-EVENT\-0087 |  The DB instance has been stopped\.   | 
|  notification  | RDS\-EVENT\-0088 |  The DB instance has been started\.  | 
|  notification  | RDS\-EVENT\-0154 |  The DB instance is being started due to it exceeding the maximum allowed time being stopped\.  | 
|  notification  | RDS\-EVENT\-0157 |  RDS can't modify the DB instance class because the target instance class can't support the number of databases that exist on the source DB instance\. The error message appears as: "The instance has *N* databases, but after conversion it would only support *N*"\.  | 
|  notification  | RDS\-EVENT\-0158 |  DB instance is in a state that can't be upgraded\.  | 
|  read replica  | RDS\-EVENT\-0045 |  An error has occurred in the read replication process\. For more information, see the event message\.    For information on troubleshooting read replica errors, see [ Troubleshooting a MySQL read replica problem](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_MySQL.Replication.ReadReplicas.html#USER_ReadRepl.Troubleshooting)\.  | 
|  read replica  | RDS\-EVENT\-0046 | The read replica has resumed replication\. This message appears when you first create a read replica, or as a monitoring message confirming that replication is functioning properly\. If this message follows an RDS\-EVENT\-0045 notification, then replication has resumed following an error or after replication was stopped\. | 
|  read replica  | RDS\-EVENT\-0057 |  Replication on the read replica was terminated\.  | 
|  read replica  | RDS\-EVENT\-0062 |  Replication on the read replica was manually stopped\.  | 
|  read replica  | RDS\-EVENT\-0063 |  Replication on the read replica was reset\.  | 
|  recovery  | RDS\-EVENT\-0020 |  Recovery of the DB instance has started\. Recovery time will vary with the amount of data to be recovered\.  | 
|  recovery  | RDS\-EVENT\-0021 |  Recovery of the DB instance is complete\.  | 
|  recovery  | RDS\-EVENT\-0023 |  A manual backup has been requested but Amazon RDS is currently in the process of creating a DB snapshot\. Submit the request again after Amazon RDS has completed the DB snapshot\.  | 
|  recovery  | RDS\-EVENT\-0052 |  Recovery of the Multi\-AZ instance has started\. Recovery time will vary with the amount of data to be recovered\.  | 
|  recovery  | RDS\-EVENT\-0053 |  Recovery of the Multi\-AZ instance is complete\.  | 
|  restoration  | RDS\-EVENT\-0008 |  The DB instance has been restored from a DB snapshot\.  | 
|  restoration  | RDS\-EVENT\-0019 |  The DB instance has been restored from a point\-in\-time backup\.  | 

## DB parameter group events<a name="USER_Events.Messages.parameter-group"></a>

The following table shows the event category and a list of events when a DB parameter group is the source type\.


|  Category  | RDS event ID |  Description  | 
| --- | --- | --- | 
|  configuration change  | RDS\-EVENT\-0037 |  The parameter group was modified\.  | 

## DB security group events<a name="USER_Events.Messages.security-group"></a>

The following table shows the event category and a list of events when a DB security group is the source type\.


|  Category  | RDS event ID |  Description  | 
| --- | --- | --- | 
|  configuration change  | RDS\-EVENT\-0038 |  The security group has been modified\.  | 
|  failure  | RDS\-EVENT\-0039 |  The security group owned by \[user\] does not exist; authorization for the security group has been revoked\.  | 

## DB cluster events<a name="USER_Events.Messages.cluster"></a>

The following table shows the event category and a list of events when an Aurora DB cluster is the source type\.

**Note**  
No event category exists for Aurora Serverless in the DB cluster event type\. The Aurora Serverless events range from RDS\-EVENT\-0141 to RDS\-EVENT\-0149\.


|  Category  | RDS event ID |  Description  | 
| --- | --- | --- | 
|  configuration change  | RDS\-EVENT\-0179 |  Database Activity Streams is started on your database cluster\. For more information see [Monitoring Amazon Aurora using Database Activity Streams](DBActivityStreams.md)\.  | 
|  configuration change  | RDS\-EVENT\-0180 |  Database Activity Streams is stopped on your database cluster\. For more information see [Monitoring Amazon Aurora using Database Activity Streams](DBActivityStreams.md)\.  | 
| creation | RDS\-EVENT\-0170 |  DB cluster created\.  | 
| deletion | RDS\-EVENT\-0171 |  DB cluster deleted\.  | 
|  failover  | RDS\-EVENT\-0069 |  A failover for the DB cluster has failed\.  | 
|  failover  | RDS\-EVENT\-0070 |  A failover for the DB cluster has restarted\.  | 
|  failover  | RDS\-EVENT\-0071 |  A failover for the DB cluster has finished\.  | 
|  failover  | RDS\-EVENT\-0072 |  A failover for the DB cluster has begun within the same Availability Zone\.  | 
|  failover  | RDS\-EVENT\-0073 |  A failover for the DB cluster has begun across Availability Zones\.  | 
|  failure  | RDS\-EVENT\-0083 |  Aurora was unable to copy backup data from an Amazon S3 bucket\. It is likely that the permissions for Aurora to access the Amazon S3 bucket are configured incorrectly\. For more information, see [Migrating data from MySQL by using an Amazon S3 bucket](AuroraMySQL.Migrating.ExtMySQL.md#AuroraMySQL.Migrating.ExtMySQL.S3) \.   | 
|  failure  | RDS\-EVENT\-0143 |  Scaling failed for the Aurora Serverless DB cluster\.  | 
|  maintenance  | RDS\-EVENT\-0156 |  The DB cluster has a DB engine minor version upgrade available\.  | 
|  notification  | RDS\-EVENT\-0076 |  Migration to an Aurora DB cluster failed\.  | 
|  notification  | RDS\-EVENT\-0077 |  An attempt to convert a table from the source database to InnoDB failed during the migration to an Aurora DB cluster\.  | 
|  notification  | RDS\-EVENT\-0085 |  An error occurred while attempting to patch the Aurora DB cluster\. Check your instance status, resolve the issue, and try again\. For more information see [Maintaining an Amazon Aurora DB cluster](USER_UpgradeDBInstance.Maintenance.md)\.  | 
|  notification  | RDS\-EVENT\-0141 |  Scaling initiated for the Aurora Serverless DB cluster\.  | 
|  notification  | RDS\-EVENT\-0142 |  Scaling completed for the Aurora Serverless DB cluster\.  | 
|  notification  | RDS\-EVENT\-0144 |  Automatic pause initiated for the Aurora Serverless DB cluster\.  | 
|  notification  | RDS\-EVENT\-0145 |  The Aurora Serverless DB cluster paused\.  | 
|  notification  | RDS\-EVENT\-0146 |  Pause cancelled for the Aurora Serverless DB cluster\.  | 
|  notification  | RDS\-EVENT\-0147 |  Resume initiated for the Aurora Serverless DB cluster\.  | 
|  notification  | RDS\-EVENT\-0148 |  Resume completed for the Aurora Serverless DB cluster\.  | 
|  notification  | RDS\-EVENT\-0149 |  Seamless scaling completed with the force option for the Aurora Serverless DB cluster\. Connections might have been interrupted as required\.  | 
|  notification  | RDS\-EVENT\-0150 |  The DB cluster stopped\.  | 
|  notification  | RDS\-EVENT\-0151 |  The DB cluster started\.  | 
|  notification  | RDS\-EVENT\-0152 |  The DB cluster stop failed\.  | 
|  notification  | RDS\-EVENT\-0153 |  The DB cluster is being started due to it exceeding the maximum allowed time being stopped\.  | 
|  notification  | RDS\-EVENT\-0173 |  Patching of the DB cluster has completed\.  | 

## DB cluster snapshot events<a name="USER_Events.Messages.cluster-snapshot"></a>

The following table shows the event category and a list of events when an Aurora DB cluster snapshot is the source type\.


|  Category  | RDS event ID |  Description  | 
| --- | --- | --- | 
|  backup  | RDS\-EVENT\-0074 |  Creation of a manual DB cluster snapshot has started\.  | 
|  backup  | RDS\-EVENT\-0075 |  A manual DB cluster snapshot has been created\.  | 
| notification | RDS\-EVENT\-0162 |  DB cluster snapshot export task failed\.  | 
| notification | RDS\-EVENT\-0163 |  DB cluster snapshot export task canceled\.  | 
| notification | RDS\-EVENT\-0164 |  DB cluster snapshot export task completed\.  | 
| backup | RDS\-EVENT\-0168 |  Creating automated cluster snapshot\.  | 
| backup | RDS\-EVENT\-0169 |  Automated cluster snapshot created\.  | 
| notification | RDS\-EVENT\-0172 |  Renamed DB cluster from \[old DB cluster name\] to \[new DB cluster name\]\.  | 