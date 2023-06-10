# Amazon RDS event categories and event messages<a name="USER_Events.Messages"></a>

Amazon RDS generates a significant number of events in categories that you can subscribe to using the Amazon RDS Console, AWS CLI, or the API\.

**Topics**
+ [DB cluster events](#USER_Events.Messages.cluster)
+ [DB instance events](#USER_Events.Messages.instance)
+ [DB parameter group events](#USER_Events.Messages.parameter-group)
+ [DB security group events](#USER_Events.Messages.security-group)
+ [DB cluster snapshot events](#USER_Events.Messages.cluster-snapshot)
+ [RDS Proxy events](#USER_Events.Messages.rds-proxy)
+ [Blue/green deployment events](#USER_Events.Messages.BlueGreenDeployments)

## DB cluster events<a name="USER_Events.Messages.cluster"></a>

The following table shows the event category and a list of events when a DB cluster is the source type\.

**Note**  
No event category exists for Aurora Serverless in the DB cluster event type\. The Aurora Serverless events range from RDS\-EVENT\-0141 to RDS\-EVENT\-0149\.


|  Category  | RDS event ID |  Message  |  Notes  | 
| --- | --- | --- | --- | 
|  configuration change  | RDS\-EVENT\-0179 |  Database Activity Streams is started on your database cluster\.  |  For more information see [Monitoring Amazon Aurora with Database Activity Streams](DBActivityStreams.md)\.  | 
|  configuration change  | RDS\-EVENT\-0180 |  Database Activity Streams is stopped on your database cluster\.  | For more information see [Monitoring Amazon Aurora with Database Activity Streams](DBActivityStreams.md)\. | 
| creation | RDS\-EVENT\-0170 |  DB cluster created\.  |    | 
| deletion | RDS\-EVENT\-0171 |  DB cluster deleted\.  |    | 
|  failover  | RDS\-EVENT\-0069 |  Cluster failover failed, check the health of your cluster instances and try again\.  |    | 
|  failover  | RDS\-EVENT\-0070 |  Promoting previous primary again: *name*\.  |    | 
|  failover  | RDS\-EVENT\-0071 |  Completed failover to DB instance: *name*\.  |    | 
|  failover  | RDS\-EVENT\-0072 |  Started same AZ failover to DB instance: *name*\.  |    | 
|  failover  | RDS\-EVENT\-0073 |  Started cross AZ failover to DB instance: *name*\.  |    | 
|  failure  | RDS\-EVENT\-0083 |  Amazon RDS has been unable to create credentials to access your Amazon S3 Bucket for your DB cluster *name*\. This is due to the S3 snapshot ingestion IAM role not being configured correctly in your account or the specified Amazon S3 bucket cannot be found\. Please refer to the troubleshooting section in the Amazon RDS documentation for further details\.  |  For more information, see [Migrating data from MySQL by using an Amazon S3 bucket](AuroraMySQL.Migrating.ExtMySQL.md#AuroraMySQL.Migrating.ExtMySQL.S3) \.   | 
|  failure  | RDS\-EVENT\-0143 |  The DB cluster failed to scale from *units* to *units* for this reason: *reason*\.  |  Scaling failed for the Aurora Serverless DB cluster\.  | 
|  global failover  | RDS\-EVENT\-0181 |  Global failover to DB cluster *name* in Region *name* started\.  |  The process can be delayed because other operations are running on the DB cluster\.  | 
|  global failover  | RDS\-EVENT\-0182 |  Old primary DB cluster *name* in Region *name* successfully shut down\.  |  The old primary instance in the global database isn't accepting writes\. All volumes are synchronized\.  | 
|  global failover  | RDS\-EVENT\-0183 |  Waiting for data synchronization across global cluster members\. Current lags behind primary DB cluster: *reason*\.  |  A replication lag is occurring during the synchronization phase of the global database failover\.  | 
|  global failover  | RDS\-EVENT\-0184 |  New primary DB cluster *name* in Region *name* was successfully promoted\.  |  The volume topology of the global database is reestablished with the new primary volume\.  | 
|  global failover  | RDS\-EVENT\-0185 |  Global failover to DB cluster *name* in Region *name* finished\.  |  The global database failover is finished on the primary DB cluster\. Replicas might take long to come online after the failover completes\.  | 
|  global failover  | RDS\-EVENT\-0186 |  Global failover to DB cluster *name* in Region *name* is cancelled\.  |    | 
|  global failover  | RDS\-EVENT\-0187 |  Global failover to DB cluster *name* in Region *name* failed\.  |    | 
|  maintenance  | RDS\-EVENT\-0156 |  The DB cluster has a DB engine minor version upgrade available\.  |    | 
|  maintenance  | RDS\-EVENT\-0173 |  Database cluster engine version has been upgraded\.  |  Patching of the DB cluster has completed\.  | 
|  maintenance  | RDS\-EVENT\-0176 |  Database cluster engine major version has been upgraded\.  |  | 
|  maintenance  | RDS\-EVENT\-0286 |  Database cluster engine version upgrade started\.  |  | 
|  maintenance  | RDS\-EVENT\-0287 |  Operating system upgrade requirement detected\.  |  | 
|  maintenance  | RDS\-EVENT\-0288 |  Cluster operating system upgrade starting\.  |  | 
|  maintenance  | RDS\-EVENT\-0289 |  Cluster operating system upgrade completed\.  |  | 
|  maintenance  | RDS\-EVENT\-0290 |  Database cluster has been patched: source version *version\_number* => *new\_version\_number*\.  |  | 
|  notification  | RDS\-EVENT\-0076 |  Failed to migrate from *name* to *name*\. Reason: *reason*\.  |  Migration to an Aurora DB cluster failed\.  | 
|  notification  | RDS\-EVENT\-0077 |  Failed to convert *name*\.*name* to InnoDB\. Reason: *reason*\.  |  An attempt to convert a table from the source database to InnoDB failed during the migration to an Aurora DB cluster\.  | 
|  notification  | RDS\-EVENT\-0085 |  Unable to upgrade DB cluster *name* because the instance *name* has a status of *name*\. Resolve the issue or delete the instance and try again\.  |  An error occurred while attempting to patch the Aurora DB cluster\. Check your instance status, resolve the issue, and try again\. For more information see [Maintaining an Amazon Aurora DB cluster](USER_UpgradeDBInstance.Maintenance.md)\.  | 
|  notification  | RDS\-EVENT\-0141 |  Scaling DB cluster from *units* to *units* for this reason: *reason*\.  |  Scaling initiated for the Aurora Serverless DB cluster\.  | 
|  notification  | RDS\-EVENT\-0142 |  The DB cluster has scaled from *units* to *units*\.  |  Scaling completed for the Aurora Serverless DB cluster\.  | 
|  notification  | RDS\-EVENT\-0144 |  The DB cluster is being paused\.  |  An automatic pause was initiated for the Aurora Serverless DB cluster\.  | 
|  notification  | RDS\-EVENT\-0145 |  The DB cluster is paused\.  |  The Aurora Serverless DB cluster has been paused\.  | 
|  notification  | RDS\-EVENT\-0146 |  Pause was canceled for the DB cluster\.  |  The pause was canceled for the Aurora Serverless DB cluster\.  | 
|  notification  | RDS\-EVENT\-0147 |  The DB cluster is being resumed\.  | A resume operation was initiated for the Aurora Serverless DB cluster\. | 
|  notification  | RDS\-EVENT\-0148 |  The DB cluster is resumed\.  | The resume operation completed for the Aurora Serverless DB cluster\. | 
|  notification  | RDS\-EVENT\-0149 |  The DB cluster has scaled from *units* to *units*, but scaling wasn't seamless for this reason: *reason*\.  |  Seamless scaling completed with the force option for the Aurora Serverless DB cluster\. Connections might have been interrupted as required\.  | 
|  notification  | RDS\-EVENT\-0150 |  DB cluster stopped\.  |    | 
|  notification  | RDS\-EVENT\-0151 |  DB cluster started\.  |    | 
|  notification  | RDS\-EVENT\-0152 |  DB cluster stop failed\.  |    | 
|  notification  | RDS\-EVENT\-0153 |  DB cluster is being started due to it exceeding the maximum allowed time being stopped\.  |    | 
| notification | RDS\-EVENT\-0172 |  Renamed cluster from *name* to *name*\.  |    | 
|  notification  | RDS\-EVENT\-0234 |  Export task failed\.  |  The DB cluster export task failed\.  | 
|  notification  | RDS\-EVENT\-0235 |  Export task canceled\.  |  The DB cluster export task was canceled\.  | 
|  notification  | RDS\-EVENT\-0236 |  Export task completed\.  |  The DB cluster export task completed\.  | 

## DB instance events<a name="USER_Events.Messages.instance"></a>

The following table shows the event category and a list of events when a DB instance is the source type\.


|  Category  | RDS event ID |  Message  |  Notes  | 
| --- | --- | --- | --- | 
|  availability  | RDS\-EVENT\-0006 |  DB instance restarted\.  |  | 
|  availability  | RDS\-EVENT\-0004 |  DB instance shutdown\.  |  | 
|  availability  | RDS\-EVENT\-0022 |  Error restarting mysql: *message*\.  | An error has occurred while restarting Aurora MySQL or RDS for MariaDB\. | 
|  backtrack  | RDS\-EVENT\-0131 |  The actual Backtrack window is smaller than the target Backtrack window you specified\. Consider reducing the number of hours in your target Backtrack window\.  | For more information about backtracking, see [Backtracking an Aurora DB cluster](AuroraMySQL.Managing.Backtrack.md)\. | 
|  backtrack  | RDS\-EVENT\-0132 |  The actual Backtrack window is the same as the target Backtrack window\.  |  | 
|  configuration change  | RDS\-EVENT\-0009 |  Added security group *name* to DB instance\.  |  | 
|  configuration change  | RDS\-EVENT\-0012 |  Applying modification to database instance class\.   |  | 
|  configuration change  | RDS\-EVENT\-0011 |  Updated to use DBParameterGroup *name*\.  |  | 
|  configuration change  | RDS\-EVENT\-0092 |  Finished updating DB parameter group\.  |  | 
|  configuration change  | RDS\-EVENT\-0033 |  There are *number* users matching the master username; only resetting the one not tied to a specific host\.  |  | 
|  configuration change  | RDS\-EVENT\-0025 |  Finished applying modification to convert to a Multi\-AZ DB instance\.  |  | 
|  configuration change  | RDS\-EVENT\-0029 |  Finished applying modification to convert to a standard \(Single\-AZ\) DB instance\.  |  | 
|  configuration change  | RDS\-EVENT\-0014 |  Finished applying modification to DB instance class\.  |  | 
|  configuration change  | RDS\-EVENT\-0017 |  Finished applying modification to allocated storage\.  |  | 
|  configuration change  | RDS\-EVENT\-0010 |  Removed security group *name* from DB instance\.  |  | 
|  configuration change  | RDS\-EVENT\-0016 |  Reset master credentials\.  |  | 
|  configuration change  | RDS\-EVENT\-0067 |  Unable to reset your password\. Error information: *message*\.  |  | 
|  configuration change  | RDS\-EVENT\-0078 |  Monitoring Interval changed to *number*\.  |  The Enhanced Monitoring configuration has been changed\. | 
|  creation  | RDS\-EVENT\-0005 |  DB instance created\.  |  | 
|  deletion  | RDS\-EVENT\-0003 |  DB instance deleted\.  |  | 
|  failure  | RDS\-EVENT\-0035 |  Database instance put into *state*\. *message*\.  | The DB instance has invalid parameters\. For example, if the DB instance could not start because a memory\-related parameter is set too high for this instance class, your action would be to modify the memory parameter and reboot the DB instance\. | 
|  failure  | RDS\-EVENT\-0036 |  Database instance in *state*\. *message*\.  | The DB instance is in an incompatible network\. Some of the specified subnet IDs are invalid or do not exist\. | 
|  failure  | RDS\-EVENT\-0079 |  Amazon RDS has been unable to create credentials for enhanced monitoring and this feature has been disabled\. This is likely due to the rds\-monitoring\-role not being present and configured correctly in your account\. Please refer to the troubleshooting section in the Amazon RDS documentation for further details\.  |  Enhanced Monitoring can't be enabled without the Enhanced Monitoring IAM role\. For information about creating the IAM role, see [To create an IAM role for Amazon RDS enhanced monitoring](USER_Monitoring.OS.Enabling.md#USER_Monitoring.OS.IAMRole)\.  | 
|  failure  | RDS\-EVENT\-0080 |  Amazon RDS has been unable to configure enhanced monitoring on your instance: *name* and this feature has been disabled\. This is likely due to the rds\-monitoring\-role not being present and configured correctly in your account\. Please refer to the troubleshooting section in the Amazon RDS documentation for further details\.  |  Enhanced Monitoring was disabled because an error occurred during the configuration change\. It is likely that the Enhanced Monitoring IAM role is configured incorrectly\. For information about creating the enhanced monitoring IAM role, see [To create an IAM role for Amazon RDS enhanced monitoring](USER_Monitoring.OS.Enabling.md#USER_Monitoring.OS.IAMRole)\.  | 
|  failure  | RDS\-EVENT\-0082 |  Amazon RDS has been unable to create credentials to access your Amazon S3 Bucket for your DB instance *name*\. This is due to the S3 snapshot ingestion IAM role not being configured correctly in your account or the specified Amazon S3 bucket cannot be found\. Please refer to the troubleshooting section in the Amazon RDS documentation for further details\.  |  Aurora was unable to copy backup data from an Amazon S3 bucket\. It is likely that the permissions for Aurora to access the Amazon S3 bucket are configured incorrectly\. For more information, see [Migrating data from MySQL by using an Amazon S3 bucket](AuroraMySQL.Migrating.ExtMySQL.md#AuroraMySQL.Migrating.ExtMySQL.S3) \.   | 
| failure | RDS\-EVENT\-0254 |  Underlying storage quota for this customer account has exceeded the limit\. Please increase the allowed storage quota to let the scaling go through on the instance\.  |  | 
|  low storage  | RDS\-EVENT\-0007 |  Allocated storage has been exhausted\. Allocate additional storage to resolve\.  |  The allocated storage for the DB instance has been consumed\. To resolve this issue, allocate additional storage for the DB instance\. For more information, see the [RDS FAQ](https://aws.amazon.com/rds/faqs)\. You can monitor the storage space for a DB instance using the **Free Storage Space** metric\.  | 
|  low storage  | RDS\-EVENT\-0089 |  The free storage capacity for DB instance: *name* is low at *percentage* of the provisioned storage \[Provisioned Storage: *size*, Free Storage: *size*\]\. You may want to increase the provisioned storage to address this issue\.  |  The DB instance has consumed more than 90% of its allocated storage\. You can monitor the storage space for a DB instance using the **Free Storage Space** metric\.  | 
|  low storage  | RDS\-EVENT\-0227 |  Your Aurora cluster's storage is dangerously low with only *amount* terabytes remaining\. Please take measures to reduce the storage load on your cluster\.  |  The Aurora storage subsystem is running low on space\.  | 
|  maintenance  | RDS\-EVENT\-0026 |  Applying off\-line patches to DB instance\.  |  Offline maintenance of the DB instance is taking place\. The DB instance is currently unavailable\.  | 
|  maintenance  | RDS\-EVENT\-0027 |  Finished applying off\-line patches to DB instance\.  |  Offline maintenance of the DB instance is complete\. The DB instance is now available\.  | 
|  maintenance  | RDS\-EVENT\-0047 |  Database instance patched\.  |  | 
|  maintenance  | RDS\-EVENT\-0155 |  The DB instance has a DB engine minor version upgrade available\.  |  | 
|  notification  | RDS\-EVENT\-0044 |  *message*  | This is an operator\-issued notification\. For more information, see the event message\. | 
|  notification  | RDS\-EVENT\-0048 |  Delaying database engine upgrade since this instance has read replicas that need to be upgraded first\.  | Patching of the DB instance has been delayed\. | 
|  notification  | RDS\-EVENT\-0087 |  DB instance stopped\.   |  | 
|  notification  | RDS\-EVENT\-0088 |  DB instance started\.  |  | 
|  read replica  | RDS\-EVENT\-0045 |  Replication has stopped\.  |  Replication on your DB instance has been stopped due to insufficient storage\. Scale storage or reduce the maximum size of your redo logs to let replication continue\. To accommodate redo logs of size %d MiB you need at least %d MiB free storage\.  | 
|  read replica  | RDS\-EVENT\-0046 |  Replication for the Read Replica resumed\.  | This message appears when you first create a read replica, or as a monitoring message confirming that replication is functioning properly\. If this message follows an `RDS-EVENT-0045` notification, then replication has resumed following an error or after replication was stopped\. | 
|  read replica  | RDS\-EVENT\-0057 |  Replication streaming has been terminated\.  |  | 
|  recovery  | RDS\-EVENT\-0020 |  Recovery of the DB instance has started\. Recovery time will vary with the amount of data to be recovered\.  |  | 
|  recovery  | RDS\-EVENT\-0021 |  Recovery of the DB instance is complete\.  |  | 
|  recovery  | RDS\-EVENT\-0023 |  Emergent Snapshot Request: *message*\.  |  A manual backup has been requested but Amazon RDS is currently in the process of creating a DB snapshot\. Submit the request again after Amazon RDS has completed the DB snapshot\.  | 
|  recovery  | RDS\-EVENT\-0052 |  Multi\-AZ instance recovery started\.  | Recovery time will vary with the amount of data to be recovered\. | 
|  recovery  | RDS\-EVENT\-0053 |  Multi\-AZ instance recovery completed\. Pending failover or activation\.  |  | 
|  restoration  | RDS\-EVENT\-0019 |  Restored from DB instance *name* to *name*\.  |  The DB instance has been restored from a point\-in\-time backup\.  | 
|  security patching  | RDS\-EVENT\-0230 |  A system update is available for your DB instance\. For information about applying updates, see 'Maintaining a DB instance' in the RDS User Guide\.  |   A new Operating System patch is available\.  A new, minor version, operating system update is available for your DB instance\. For information about applying updates, see [Working with operating system updates](USER_UpgradeDBInstance.Maintenance.md#OS_Updates)\.  | 

## DB parameter group events<a name="USER_Events.Messages.parameter-group"></a>

The following table shows the event category and a list of events when a DB parameter group is the source type\.


|  Category  | RDS event ID |  Message  |  Notes  | 
| --- | --- | --- | --- | 
|  configuration change  | RDS\-EVENT\-0037 |  Updated parameter *name* to *value* with apply method *method*\.   |    | 

## DB security group events<a name="USER_Events.Messages.security-group"></a>

The following table shows the event category and a list of events when a DB security group is the source type\.

**Note**  
DB security groups are resources for EC2\-Classic\. EC2\-Classic was retired on August 15, 2022\. If you haven't migrated from EC2\-Classic to a VPC, we recommend that you migrate as soon as possible\. For more information, see [Migrate from EC2\-Classic to a VPC](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/vpc-migrate.html) in the *Amazon EC2 User Guide* and the blog [ EC2\-Classic Networking is Retiring – Here’s How to Prepare](http://aws.amazon.com/blogs/aws/ec2-classic-is-retiring-heres-how-to-prepare/)\.


|  Category  | RDS event ID |  Message  |  Notes  | 
| --- | --- | --- | --- | 
|  configuration change  | RDS\-EVENT\-0038 |  Applied change to security group\.  |    | 
|  failure  | RDS\-EVENT\-0039 |  Revoking authorization as *user*\.  |  The security group owned by *user* doesn't exist\. The authorization for the security group has been revoked because it is invalid\.  | 

## DB cluster snapshot events<a name="USER_Events.Messages.cluster-snapshot"></a>

The following table shows the event category and a list of events when a DB cluster snapshot is the source type\.


|  Category  | RDS event ID |  Message  |  Notes  | 
| --- | --- | --- | --- | 
|  backup  | RDS\-EVENT\-0074 |  Creating manual cluster snapshot\.  |  | 
|  backup  | RDS\-EVENT\-0075 |  Manual cluster snapshot created\.  |  | 
| notification | RDS\-EVENT\-0162 |  The cluster snapshot export task failed\.  |  | 
| notification | RDS\-EVENT\-0163 |  The cluster snapshot export task was canceled\.  |  | 
| notification | RDS\-EVENT\-0164 |  The cluster snapshot export task completed\.  |  | 
| backup | RDS\-EVENT\-0168 |  Creating automated cluster snapshot\.  |  | 
| backup | RDS\-EVENT\-0169 |  Automated cluster snapshot created\.  |  | 

## RDS Proxy events<a name="USER_Events.Messages.rds-proxy"></a>

The following table shows the event category and a list of events when an RDS Proxy is the source type\.


|  Category  | RDS event ID |  Message  |  Notes  | 
| --- | --- | --- | --- | 
| configuration change | RDS\-EVENT\-0204 |  RDS modified DB proxy *name*\.  |  | 
| configuration change | RDS\-EVENT\-0207 |  RDS modified the end point of the DB proxy *name*\.  |  | 
| configuration change | RDS\-EVENT\-0213 |  RDS detected the addition of the DB instance and automatically added it to the target group of the DB proxy *name*\.  |  | 
|  configuration change  | RDS\-EVENT\-0213 | RDS detected creation of DB instance *name* and automatically added it to target group *name* of DB proxy *name*\.  |  | 
|  configuration change  | RDS\-EVENT\-0214 |  RDS detected deletion of DB instance *name* and automatically removed it from target group *name* of DB proxy *name*\.  |  | 
|  configuration change  | RDS\-EVENT\-0215 |  RDS detected deletion of DB cluster *name* and automatically removed it from target group *name* of DB proxy *name*\.  |  | 
|  creation  | RDS\-EVENT\-0203 |  RDS created DB proxy *name*\.  |  | 
|  creation  | RDS\-EVENT\-0206 |  RDS created endpoint *name* for DB proxy *name*\.  |  | 
| deletion | RDS\-EVENT\-0205 |  RDS deleted DB proxy *name*\.  |  | 
|  deletion  | RDS\-EVENT\-0208 |  RDS deleted endpoint *name* for DB proxy *name*\.  |  | 
|  failure  | RDS\-EVENT\-0243 |  RDS failed to provision capacity for proxy *name* because there aren't enough IP addresses available in your subnets: *name*\. To fix the issue, make sure that your subnets have the minimum number of unused IP addresses as recommended in the RDS Proxy documentation\.  |  To determine the recommended number for your instance class, see [Planning for IP address capacity](rds-proxy-setup.md#rds-proxy-network-prereqs.plan-ip-address)\.  | 
|  failure | RDS\-EVENT\-0275 |  RDS throttled some connections to DB proxy \(RDS Proxy\)\.  |  | 

## Blue/green deployment events<a name="USER_Events.Messages.BlueGreenDeployments"></a>

The following table shows the event category and a list of events when a blue/green deployment is the source type\.

For more information about blue/green deployments, see [Using Amazon RDS Blue/Green Deployments for database updates](blue-green-deployments.md)\.


|  Category  | Amazon RDS event ID |  Message  |  Notes  | 
| --- | --- | --- | --- | 
|  creation  | RDS\-EVENT\-0244 |  Blue/green deployment tasks completed\. You can make more modifications to the green environment databases or switch over the deployment\.  |  | 
|  deletion  | RDS\-EVENT\-0246 |  Blue/green deployment deleted\.  |  | 
|  failure  | RDS\-EVENT\-0245 |  Creation of blue/green deployment failed because the \(source/target\) DB \(instance/cluster\) wasn't found\.   |  | 
|  failure  | RDS\-EVENT\-0249 |  Switchover canceled on blue/green deployment\.  |  | 
|  failure  | RDS\-EVENT\-0252 |  Switchover from primary source to target canceled due to *reason*\.  |  | 
|  failure  | RDS\-EVENT\-0261 |  Switchover from source to target was canceled\.  |  | 
|  notification  | RDS\-EVENT\-0247 |  Switchover started on blue/green deployment\.  |  | 
|  notification  | RDS\-EVENT\-0248 |  Switchover completed on blue/green deployment\.  |  | 
|  notification  | RDS\-EVENT\-0250 |  Switchover from primary source to target started\.  |  | 
|  notification  | RDS\-EVENT\-0251 |  Switchover from primary source to target completed\. Renamed databases\.  |  | 
|  notification  | RDS\-EVENT\-0259 |  Switchover from source to target started\.  |  | 
|  notification  | RDS\-EVENT\-0260 |  Switchover from source to target completed\. Renamed databases\.  |  | 