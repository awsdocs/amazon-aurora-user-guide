# Backing up and restoring an Amazon Aurora DB cluster<a name="BackupRestoreAurora"></a>

These topics provide information about backing up and restoring Amazon Aurora DB clusters\.

**Tip**  
The Aurora high availability features and automatic backup capabilities help to keep your data safe without requiring extensive setup from you\. Before you implement a backup strategy, learn about the ways that Aurora maintains multiple copies of your data and helps you to access them across multiple DB instances and AWS Regions\. For details, see [High availability for Amazon Aurora](Concepts.AuroraHighAvailability.md)\.

**Topics**
+ [Overview of backing up and restoring an Aurora DB cluster](Aurora.Managing.Backups.md)
+ [Understanding Aurora backup storage usage](aurora-storage-backup.md)
+ [Creating a DB cluster snapshot](USER_CreateSnapshotCluster.md)
+ [Restoring from a DB cluster snapshot](aurora-restore-snapshot.md)
+ [Copying a DB cluster snapshot](aurora-copy-snapshot.md)
+ [Sharing a DB cluster snapshot](aurora-share-snapshot.md)
+ [Exporting DB cluster snapshot data to Amazon S3](aurora-export-snapshot.md)
+ [Restoring a DB cluster to a specified time](aurora-pitr.md)
+ [Deleting a DB cluster snapshot](aurora-delete-snapshot.md)