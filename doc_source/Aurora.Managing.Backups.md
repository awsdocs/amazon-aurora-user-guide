# Overview of Backing Up and Restoring an Aurora DB Cluster<a name="Aurora.Managing.Backups"></a>

In the following sections, you can find information about Aurora backups and how to restore your Aurora DB cluster using the AWS Management Console\.

**Note**  
You can also use AWS Backup to manage backups of Aurora DB clusters\. Backups managed by AWS Backup are considered manual cluster snapshots for the manual cluster snapshots quota\. Backups that were created with AWS Backup have names ending in `awsbackup:AWS-Backup-job-number`\. For information about AWS Backup, see the [https://docs.aws.amazon.com/aws-backup/latest/devguide](https://docs.aws.amazon.com/aws-backup/latest/devguide)\.

## Fault Tolerance for an Aurora DB Cluster<a name="Aurora.Managing.FaultTolerance"></a>

An Aurora DB cluster is fault tolerant by design\. The cluster volume spans multiple Availability Zones in a single AWS Region, and each Availability Zone contains a copy of the cluster volume data\. This functionality means that your DB cluster can tolerate a failure of an Availability Zone without any loss of data and only a brief interruption of service\.

If the primary instance in a DB cluster using single\-master replication fails, Aurora automatically fails over to a new primary instance in one of two ways:
+ By promoting an existing Aurora Replica to the new primary instance
+ By creating a new primary instance

If the DB cluster has one or more Aurora Replicas, then an Aurora Replica is promoted to the primary instance during a failure event\. A failure event results in a brief interruption, during which read and write operations fail with an exception\. However, service is typically restored in less than 120 seconds, and often less than 60 seconds\. To increase the availability of your DB cluster, we recommend that you create at least one or more Aurora Replicas in two or more different Availability Zones\. 

You can customize the order in which your Aurora Replicas are promoted to the primary instance after a failure by assigning each replica a priority\. Priorities range from 0 for the first priority to 15 for the last priority\. If the primary instance fails, Amazon RDS promotes the Aurora Replica with the better priority to the new primary instance\. You can modify the priority of an Aurora Replica at any time\. Modifying the priority doesn't trigger a failover\. 

More than one Aurora Replica can share the same priority, resulting in promotion tiers\. If two or more Aurora Replicas share the same priority, then Amazon RDS promotes the replica that is largest in size\. If two or more Aurora Replicas share the same priority and size, then Amazon RDS promotes an arbitrary replica in the same promotion tier\. 

If the DB cluster doesn't contain any Aurora Replicas, then the primary instance is recreated during a failure event\. A failure event results in an interruption during which read and write operations fail with an exception\. Service is restored when the new primary instance is created, which typically takes less than 10 minutes\. Promoting an Aurora Replica to the primary instance is much faster than creating a new primary instance\.

**Note**  
Amazon Aurora also supports replication with an external MySQL database, or an RDS MySQL DB instance\. For more information, see [Replication Between Aurora and MySQL or Between Aurora and Another Aurora DB Cluster \(Binlog Replication\)](AuroraMySQL.Replication.MySQL.md)\.

## Backups<a name="Aurora.Managing.Backups.Backup"></a>

Aurora backs up your cluster volume automatically and retains restore data for the length of the *backup retention period*\. Aurora backups are continuous and incremental so you can quickly restore to any point within the backup retention period\. No performance impact or interruption of database service occurs as backup data is being written\. You can specify a backup retention period, from 1 to 35 days, when you create or modify a DB cluster\. Aurora backups are stored in Amazon S3\.

If you want to retain a backup beyond the backup retention period, you can also take a snapshot of the data in your cluster volume\. Because Aurora retains incremental restore data for the entire backup retention period, you only need to create a snapshot for data that you want to retain beyond the backup retention period\. You can create a new DB cluster from the snapshot\.

**Note**  
For Amazon Aurora DB clusters, the default backup retention period is one day regardless of how the DB cluster is created\.
You cannot disable automated backups on Aurora\. The backup retention period for Aurora is managed by the DB cluster\. 

 Your costs for backup storage depend upon the amount of Aurora backup and snapshot data you keep and how long you keep it\. For information about the storage associated with Aurora backups and snapshots, see [Understanding Aurora Backup Storage Usage](aurora-storage-backup.md)\. For pricing information about Aurora backup storage, see [Amazon RDS for Aurora Pricing](https://aws.amazon.com/rds/aurora/pricing)\. After the Aurora cluster associated with a snapshot is deleted, storing that snapshot incurs the standard backup storage charges for Aurora\. 

## Restoring Data<a name="Aurora.Managing.Backups.Restore"></a>

You can recover your data by creating a new Aurora DB cluster from the backup data that Aurora retains, or from a DB cluster snapshot that you have saved\. You can quickly restore a new copy of a DB cluster created from backup data to any point in time during your backup retention period\. The continuous and incremental nature of Aurora backups during the backup retention period means you don't need to take frequent snapshots of your data to improve restore times\.

To determine the latest or earliest restorable time for a DB instance, look for the `Latest Restorable Time` or `Earliest Restorable Time` values on the RDS console\. For information about viewing these values, see [Viewing an Amazon Aurora DB Cluster](Aurora.Viewing.md)\. The latest restorable time for a DB cluster is the most recent point at which you can restore your DB cluster, typically within 5 minutes of the current time\. The earliest restorable time specifies how far back within the backup retention period that you can restore your cluster volume\.

You can determine when the restore of a DB cluster is complete by checking the `Latest Restorable Time` and `Earliest Restorable Time` values\. The `Latest Restorable Time` and `Earliest Restorable Time` values return NULL until the restore operation is complete\. You can't request a backup or restore operation if `Latest Restorable Time` or `Earliest Restorable Time` returns NULL\.

For information about restoring a DB cluster to a specified time, see [Restoring a DB Cluster to a Specified Time](USER_PIT.md)\.

### Database Cloning for Aurora<a name="Aurora.Managing.Backups.Restore.Cloning"></a>

You can also use database cloning to clone the databases of your Aurora DB cluster to a new DB cluster, instead of restoring a DB cluster snapshot\. The clone databases use only minimal additional space when first created\. Data is copied only as data changes, either on the source databases or the clone databases\. You can make multiple clones from the same DB cluster, or create additional clones even from other clones\. For more information, see [Cloning an Aurora DB Cluster Volume](Aurora.Managing.Clone.md)\.

## Backtrack<a name="Aurora.Managing.Backups.Backtrack"></a>

Aurora MySQL now supports "rewinding" a DB cluster to a specific time, without restoring data from a backup\. For more information, see [Backtracking an Aurora DB Cluster](AuroraMySQL.Managing.Backtrack.md)\.