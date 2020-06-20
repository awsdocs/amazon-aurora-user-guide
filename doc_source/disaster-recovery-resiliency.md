# Resilience in Amazon Aurora<a name="disaster-recovery-resiliency"></a>

The AWS global infrastructure is built around AWS Regions and Availability Zones\. AWS Regions provide multiple physically separated and isolated Availability Zones, which are connected with low\-latency, high\-throughput, and highly redundant networking\. With Availability Zones, you can design and operate applications and databases that automatically fail over between Availability Zones without interruption\. Availability Zones are more highly available, fault tolerant, and scalable than traditional single or multiple data center infrastructures\. 

For more information about AWS Regions and Availability Zones, see [AWS Global Infrastructure](https://aws.amazon.com/about-aws/global-infrastructure/)\.

In addition to the AWS global infrastructure, Aurora offers features to help support your data resiliency and backup needs\.

## Backup and Restore<a name="disaster-recovery-resiliency.backup-restore"></a>

Aurora backs up your cluster volume automatically and retains restore data for the length of the *backup retention period*\. Aurora backups are continuous and incremental so you can quickly restore to any point within the backup retention period\. No performance impact or interruption of database service occurs as backup data is being written\. You can specify a backup retention period, from 1 to 35 days, when you create or modify a DB cluster\.

If you want to retain a backup beyond the backup retention period, you can also take a snapshot of the data in your cluster volume\. Aurora retains incremental restore data for the entire backup retention period\. Thus, you need to create a snapshot only for data that you want to retain beyond the backup retention period\. You can create a new DB cluster from the snapshot\.

You can recover your data by creating a new Aurora DB cluster from the backup data that Aurora retains, or from a DB cluster snapshot that you have saved\. You can quickly create a new copy of a DB cluster from backup data to any point in time during your backup retention period\. The continuous and incremental nature of Aurora backups during the backup retention period means you don't need to take frequent snapshots of your data to improve restore times\.

For more information, see [Backing Up and Restoring an Amazon Aurora DB Cluster](BackupRestoreAurora.md)\.

## Replication<a name="disaster-recovery-resiliency.replication"></a>

Aurora Replicas are independent endpoints in an Aurora DB cluster, best used for scaling read operations and increasing availability\. Up to 15 Aurora Replicas can be distributed across the Availability Zones that a DB cluster spans within an AWS Region\. The DB cluster volume is made up of multiple copies of the data for the DB cluster\. However, the data in the cluster volume is represented as a single, logical volume to the primary DB instance and to Aurora Replicas in the DB cluster\. If the primary DB instance fails, an Aurora Replica is promoted to be the primary DB instance\.

Aurora also supports replication options that are specific to Aurora MySQL and Aurora PostgreSQL\.

For more information, see [Replication with Amazon Aurora](Aurora.Replication.md)\.

## Failover<a name="disaster-recovery-resiliency.failover"></a>

Aurora stores copies of the data in a DB cluster across multiple Availability Zones in a single AWS Region\. This storage occurs regardless of whether the DB instances in the DB cluster span multiple Availability Zones\. When you create Aurora Replicas across Availability Zones, Aurora automatically provisions and maintains them synchronously\. The primary DB instance is synchronously replicated across Availability Zones to Aurora Replicas to provide data redundancy, eliminate I/O freezes, and minimize latency spikes during system backups\. Running a DB cluster with high availability can enhance availability during planned system maintenance, and help protect your databases against failure and Availability Zone disruption\.

For more information, see [High Availability for Amazon Aurora](Concepts.AuroraHighAvailability.md)\.