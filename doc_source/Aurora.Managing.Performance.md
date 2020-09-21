# Managing Performance and Scaling for Aurora DB Clusters<a name="Aurora.Managing.Performance"></a>

You can use the following options to manage performance and scaling for Aurora DB clusters and DB instances:

**Topics**
+ [Storage Scaling](#Aurora.Managing.Performance.StorageScaling)
+ [Instance Scaling](#Aurora.Managing.Performance.InstanceScaling)
+ [Read Scaling](#Aurora.Managing.Performance.ReadScaling)
+ [Managing Connections](#Aurora.Managing.MaxConnections)
+ [Managing Query Execution Plans](#Aurora.Managing.Optimizing)

## Storage Scaling<a name="Aurora.Managing.Performance.StorageScaling"></a>

Aurora storage automatically scales with the data in your cluster volume\. As your data grows, your cluster volume storage grows in 10 gibibyte \(GiB\) increments up to 128 TiB for MySQL and 64 TiB for PostgreSQL\.

The size of your cluster volume is checked on an hourly basis to determine your storage costs\. For pricing information, see the [Aurora pricing page](https://aws.amazon.com/rds/aurora/pricing)\.

 When Aurora data is removed, such as by dropping a table or partition, the overall allocated space remains the same\. The free space is reused automatically when data volume increases in the future\. 

**Note**  
Storage costs are based on the storage "high water mark," the maximum amount that was allocated for the Aurora cluster at any point in time\. Thus, you can manage costs by avoiding extract, transform, load \(ETL\) practices that create large volumes of temporary information\. Similarly, you can manage costs by avoiding ETL practices that load large volumes of new data before removing unneeded older data\. 

 If removing data from an Aurora cluster results in a substantial amount of allocated but unused space, resetting the high water mark requires doing a logical data dump and restore to a new cluster, using a tool such as `mysqldump`\. Creating and restoring a snapshot does *not* reduce the allocated storage because the physical layout of the underlying storage remains the same in the restored snapshot\. 

## Instance Scaling<a name="Aurora.Managing.Performance.InstanceScaling"></a>

You can scale your Aurora DB cluster as needed by modifying the DB instance class for each DB instance in the DB cluster\. Aurora supports several DB instance classes optimized for Aurora, depending on database engine compatibility\.


| Database Engine | Instance Scaling | 
| --- | --- | 
|  Amazon Aurora MySQL  |  See [Scaling Aurora MySQL DB Instances](AuroraMySQL.Managing.Performance.md#AuroraMySQL.Managing.Performance.InstanceScaling)  | 
|  Amazon Aurora PostgreSQL  |  See [Scaling Aurora PostgreSQL DB Instances](AuroraPostgreSQL.Managing.md#AuroraPostgreSQL.Managing.Performance.InstanceScaling)  | 

## Read Scaling<a name="Aurora.Managing.Performance.ReadScaling"></a>

You can achieve read scaling for your Aurora DB cluster by creating up to 15 Aurora Replicas in a DB cluster that uses single\-master replication\. Each Aurora Replica returns the same data from the cluster volume with minimal replica lag—usually considerably less than 100 milliseconds after the primary instance has written an update\. As your read traffic increases, you can create additional Aurora Replicas and connect to them directly to distribute the read load for your DB cluster\. Aurora Replicas don't have to be of the same DB instance class as the primary instance\.

For information about adding Aurora Replicas to a DB cluster, see [Adding Aurora Replicas to a DB Cluster](aurora-replicas-adding.md)\.

## Managing Connections<a name="Aurora.Managing.MaxConnections"></a>

The maximum number of connections allowed to an Aurora DB instance is determined by the `max_connections` parameter in the instance\-level parameter group for the DB instance\. The default value of that parameter varies depends on the DB instance class used for the DB instance and database engine compatibility\.


| Database Engine | max\_connections default Value | 
| --- | --- | 
|  Amazon Aurora MySQL  |  See [Maximum Connections to an Aurora MySQL DB Instance](AuroraMySQL.Managing.Performance.md#AuroraMySQL.Managing.MaxConnections)  | 
|  Amazon Aurora PostgreSQL  |  See [Maximum Connections to an Aurora PostgreSQL DB Instance](AuroraPostgreSQL.Managing.md#AuroraPostgreSQL.Managing.MaxConnections)  | 

## Managing Query Execution Plans<a name="Aurora.Managing.Optimizing"></a>

If you use query plan management for Aurora PostgreSQL, you gain control over which plans the optimizer runs\. For more information, see [Managing Query Execution Plans for Aurora PostgreSQL](AuroraPostgreSQL.Optimize.md)\.