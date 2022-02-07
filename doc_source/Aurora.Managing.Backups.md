# Overview of backing up and restoring an Aurora DB cluster<a name="Aurora.Managing.Backups"></a>

In the following sections, you can find information about Aurora backups and how to restore your Aurora DB cluster using the AWS Management Console\.

**Tip**  
 The Aurora high availability features and automatic backup capabilities help to keep your data safe without requiring extensive setup from you\. Before you implement a backup strategy, learn about the ways that Aurora maintains multiple copies of your data and helps you to access them across multiple DB instances and AWS Regions\. For details, see [High availability for Amazon Aurora](Concepts.AuroraHighAvailability.md)\.

## Backups<a name="Aurora.Managing.Backups.Backup"></a>

Aurora backs up your cluster volume automatically and retains restore data for the length of the *backup retention period*\. Aurora backups are continuous and incremental so you can quickly restore to any point within the backup retention period\. No performance impact or interruption of database service occurs as backup data is being written\. You can specify a backup retention period, from 1 to 35 days, when you create or modify a DB cluster\. Aurora backups are stored in Amazon S3\.

If you want to retain a backup beyond the backup retention period, you can also take a snapshot of the data in your cluster volume\. Because Aurora retains incremental restore data for the entire backup retention period, you only need to create a snapshot for data that you want to retain beyond the backup retention period\. You can create a new DB cluster from the snapshot\.

**Note**  
For Amazon Aurora DB clusters, the default backup retention period is one day regardless of how the DB cluster is created\.
You can't disable automated backups on Aurora\. The backup retention period for Aurora is managed by the DB cluster\.

 Your costs for backup storage depend upon the amount of Aurora backup and snapshot data you keep and how long you keep it\. For information about the storage associated with Aurora backups and snapshots, see [Understanding Aurora backup storage usage](aurora-storage-backup.md)\. For pricing information about Aurora backup storage, see [Amazon RDS for Aurora pricing](https://aws.amazon.com/rds/aurora/pricing)\. After the Aurora cluster associated with a snapshot is deleted, storing that snapshot incurs the standard backup storage charges for Aurora\. 

**Note**  
You can also use AWS Backup to manage backups of Amazon Aurora DB clusters\. Backups managed by AWS Backup are considered manual DB cluster snapshots, but don't count toward the DB cluster snapshot quota for Aurora\. Backups that were created with AWS Backup have names ending in `awsbackup:AWS-Backup-job-number`\. For information about AWS Backup, see the [https://docs.aws.amazon.com/aws-backup/latest/devguide](https://docs.aws.amazon.com/aws-backup/latest/devguide)\.

## Backup window<a name="Aurora.Managing.Backups.BackupWindow"></a>

Automated backups occur daily during the preferred backup window\. If the backup requires more time than allotted to the backup window, the backup continues after the window ends, until it finishes\. The backup window can't overlap with the weekly maintenance window for the DB cluster\. 

Aurora backups are continuous and incremental, but the backup window is used to create a daily system backup that is preserved within the backup retention period\. You can copy it to preserve it outside of the retention period\.

**Note**  
When you create a DB cluster using the AWS Management Console, you can't specify a backup window\. However, you can specify a backup window when you create a DB cluster using the AWS CLI or RDS API\.

If you don't specify a preferred backup window when you create the DB cluster, Aurora assigns a default 30\-minute backup window\. This window is selected at random from an 8\-hour block of time for each AWS Region\. The following table lists the time blocks for each AWS Region from which the default backup windows are assigned\. 


****  
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Managing.Backups.html)

## Restoring data<a name="Aurora.Managing.Backups.Restore"></a>

You can recover your data by creating a new Aurora DB cluster from the backup data that Aurora retains, or from a DB cluster snapshot that you have saved\. You can quickly restore a new copy of a DB cluster created from backup data to any point in time during your backup retention period\. The continuous and incremental nature of Aurora backups during the backup retention period means you don't need to take frequent snapshots of your data to improve restore times\.

To determine the latest or earliest restorable time for a DB cluster, look for the `Latest restore time` or `Earliest restorable time` values on the RDS console\. For information about viewing these values, see [Viewing an Amazon Aurora DB cluster](accessing-monitoring.md#Aurora.Viewing)\. The latest restorable time for a DB cluster is the most recent point at which you can restore your DB cluster, typically within 5 minutes of the current time\. The earliest restorable time specifies how far back within the backup retention period that you can restore your cluster volume\.

You can determine when the restore of a DB cluster is complete by checking the `Latest Restorable Time` and `Earliest Restorable Time` values\. The `Latest Restorable Time` and `Earliest Restorable Time` values return NULL until the restore operation is complete\. You can't request a backup or restore operation if `Latest Restorable Time` or `Earliest Restorable Time` returns NULL\.

For information about restoring a DB cluster to a specified time, see [Restoring a DB cluster to a specified time](aurora-pitr.md)\.

### Database cloning for Aurora<a name="Aurora.Managing.Backups.Restore.Cloning"></a>

You can also use database cloning to clone the databases of your Aurora DB cluster to a new DB cluster, instead of restoring a DB cluster snapshot\. The clone databases use only minimal additional space when first created\. Data is copied only as data changes, either on the source databases or the clone databases\. You can make multiple clones from the same DB cluster, or create additional clones even from other clones\. For more information, see [Cloning a volume for an Aurora DB cluster](Aurora.Managing.Clone.md)\.

## Backtrack<a name="Aurora.Managing.Backups.Backtrack"></a>

Aurora MySQL now supports "rewinding" a DB cluster to a specific time, without restoring data from a backup\. For more information, see [Backtracking an Aurora DB cluster](AuroraMySQL.Managing.Backtrack.md)\.