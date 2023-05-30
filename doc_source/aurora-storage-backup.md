# Understanding Amazon Aurora backup storage usage<a name="aurora-storage-backup"></a>

Amazon Aurora stores automated backups \(within the backup retention period\) and snapshots in Aurora backup storage\. To control your backup storage usage and related costs, you can reduce the backup retention interval, remove old manual snapshots when they're no longer needed, or both\. Because Aurora automated backups are incremental, you can also review updates to the database, so that continuous backup stores less data, and you incur fewer costs\.

For general information about Aurora backups, see [Backups](Aurora.Managing.Backups.md#Aurora.Managing.Backups.Backup)\. For pricing information about Aurora backup storage, see the [Amazon Aurora pricing](https://aws.amazon.com/rds/aurora/pricing) page\.

## Amazon CloudWatch metrics for Aurora backup storage<a name="aurora-storage-backup.metrics"></a>

You can monitor your Aurora clusters and create reports using Amazon CloudWatch metrics through the [CloudWatch console](https://console.aws.amazon.com/cloudwatch/)\. You can use the following CloudWatch metrics to review and monitor the amount of storage used by your Aurora backups\. These metrics are computed independently for each Aurora DB cluster\.
+ `BackupRetentionPeriodStorageUsed` – Represents the amount of backup storage used, in bytes, for storing automated backups at the current time\. This value depends on the size of the cluster volume and the amount of changes you make during the retention period\.

  For billing purposes, `BackupRetentionPeriodStorageUsed` doesn't exceed the cumulative cluster volume size during the retention period\. There is no additional charge for backup storage of up to 100% of your total Aurora database storage for each Aurora DB cluster\. There is also no additional charge for automated backups retained up to one day, such as system snapshots\. Backups that you retain for more than one day might be charged depending on the amount of data changes performed on the database\.

  For example, your retention period is two days\. Your DB cluster's `VolumeBytesUsed` size is 107,374,182,400 bytes \(100 GiB\) on the first day, and 161,061,273,600 bytes \(150 GiB\) on the second day, then the maximum value of `BackupRetentionPeriodStorageUsed` is 268,435,456,000 bytes \(250 GiB\)\. The most that you can be charged is for 250 GiB, and it is usually less than this\.
+ `SnapshotStorageUsed` – Represents the amount of backup storage used, in bytes, for storing manual snapshots beyond the backup retention period\. The size of each snapshot is the size of the cluster volume at the time you take the snapshot\. Snapshots are full backups, not incremental\.

  Manual snapshots don't count against your snapshot backup storage \(that is, they're not charged\) while their creation timestamp is within the retention period\. Outside the retention period, they're charged fully\. The `SnapshotStorageUsed` value depends on the number of snapshots you keep and the size of each snapshot\.

  For example, you have one manual snapshot outside the retention period, and the DB cluster's `VolumeBytesUsed` size was 100 GiB when that snapshot was taken\. The amount of `SnapshotStorageUsed` is 107,374,182,400 bytes \(100 GiB\)\.
+ `TotalBackupStorageBilled` – Represents the sum, in bytes, of `BackupRetentionPeriodStorageUsed` and `SnapshotStorageUsed`, minus an amount of free backup storage, which equals the size of the cluster volume for one day\. The free backup storage is equal to the latest volume size\.

  For example, your retention period is two days\. Your cluster's `VolumeBytesUsed` size is 100 GiB on the first day, and 150 GiB on the second day\. You have one manual snapshot outside the retention period\. Then the `TotalBackupStorageBilled` is at most 214,748,364,800 bytes \(200 GiB\) as shown here, and likely less:

  ```
  250 GiB (100 GiB + 150 GiB cluster volumes) + 100 GiB (manual snapshot) - 150 GiB (free backup storage) = 200 GiB
  ```

For more information about how to use CloudWatch metrics, see [Availability of Aurora metrics in the Amazon RDS console](Aurora.Monitoring.Metrics.RDSAvailability.md)\.

**Example of backup storage billing**  
The following detailed example shows backup storage billing over a week\. In this example, the retention period is seven days\. Aurora backups are stored on Amazon S3\. Aurora backup usage on S3 includes both updates and inserts, and is billed for usage above the cluster volume within the retention period\.  


| Day | Cluster volume size \(GiB\) | S3 usage | 
| --- | --- | --- | 
| 1 | 100 | 10 | 
| 2 | 110 | 0 | 
| 3 | 125 | 25 | 
| 4 | 130 | 20 | 
| 5 | 140 | 10 | 
| 6 | 150 | 25 | 
| 7 | 150 | 30 | 
| Total | 150 | 120 | 
We also have 90 GiB of S3 usage from outside the retention window that's required to restore to any point in time within the retention period \(point\-in\-time recovery\)\. This is part of the incremental behavior of Aurora continuous backup\.  
The total continuous backup usage is the S3 usage minus the cluster volume size:  

```
90 GiB (S3 usage before retention period) + 120 GiB (S3 usage during retention period) - 150 GiB (cluster volume) = 60 GiB
```

## Other considerations for Aurora backup storage and pricing<a name="aurora-storage-backup.other"></a>

The following also apply to Aurora backup storage and pricing:
+ Backup storage is billed by the GB\-month\. This means that the backup storage usage is charged as the weighted average of the usage over the given month\. For example, in a 30\-day month you can sum the daily usage values over the month, and then divide by 30 to obtain the usage in GB\-months\.
+ The backtrack setting for an Aurora DB cluster doesn't affect the volume of backup data for that cluster\. Amazon bills the storage for backtracking data separately\. For pricing information about Aurora backtracking, see the [Amazon Aurora pricing](https://aws.amazon.com/rds/aurora/pricing) page\.
+ If you share a snapshot with another user, you're still the owner of that snapshot\. The storage costs apply to the snapshot owner\. If you delete a shared snapshot that you own, nobody can access it\. To keep access to a shared snapshot owned by someone else, you can copy that snapshot\. Doing so makes you the owner of the new snapshot\. Any storage costs for the copied snapshot apply to your account\.

  For more information on sharing snapshots, see [Sharing a DB cluster snapshot](aurora-share-snapshot.md)\. For more information on copying snapshots, see [Copying a DB cluster snapshot](aurora-copy-snapshot.md)\.