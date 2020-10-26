# High availability for Amazon Aurora<a name="Concepts.AuroraHighAvailability"></a>

 The Amazon Aurora architecture involves separation of storage and compute\. Aurora includes some high availability features that apply to the data in your DB cluster\. The data remains safe even if some or all of the DB instances in the cluster become unavailable\. Other high availability features apply to the DB instances\. These features help to make sure that one or more DB instances are ready to handle database requests from your application\. 

**Topics**
+ [High availability for Aurora data](#Concepts.AuroraHighAvailability.Data)
+ [High availability for Aurora DB instances](#Concepts.AuroraHighAvailability.Instances)
+ [High availability across AWS Regions with Aurora global databases](#Concepts.AuroraHighAvailability.GlobalDB)
+ [Fault tolerance for an Aurora DB cluster](#Aurora.Managing.FaultTolerance)

## High availability for Aurora data<a name="Concepts.AuroraHighAvailability.Data"></a>

Aurora stores copies of the data in a DB cluster across multiple Availability Zones in a single AWS Region\. Aurora stores these copies regardless of whether the instances in the DB cluster span multiple Availability Zones\. For more information on Aurora, see [Managing an Amazon Aurora DB cluster](CHAP_Aurora.md)\. 

When data is written to the primary DB instance, Aurora synchronously replicates the data across Availability Zones to six storage nodes associated with your cluster volume\. Doing so provides data redundancy, eliminates I/O freezes, and minimizes latency spikes during system backups\. Running a DB instance with high availability can enhance availability during planned system maintenance, and help protect your databases against failure and Availability Zone disruption\. For more information on Availability Zones, see [ Regions and Availability Zones ](Concepts.RegionsAndAvailabilityZones.md)\.

## High availability for Aurora DB instances<a name="Concepts.AuroraHighAvailability.Instances"></a>

For a cluster using single\-master replication, after you create the primary instance, you can create up to 15 read\-only Aurora Replicas\. The Aurora Replicas are also known as reader instances\. 

 During day\-to\-day operations, you can offload some of the work for read\-intensive applications by using the reader instances to process `SELECT` queries\. When a problem affects the primary instance, one of these reader instances takes over as the primary instance\. This mechanism is known as *failover*\. Many Aurora features apply to the failover mechanism\. For example, Aurora detects database problems and activates the failover mechanism automatically when necessary\. Aurora also has features that reduce the time for failover to complete\. Doing so minimizes the time that the database is unavailable for writing during a failover\. 

 To use a connection string that stays the same even when a failover promotes a new primary instance, you connect to the cluster endpoint\. The *cluster endpoint* always represents the current primary instance in the cluster\. For more information about the cluster endpoint, see [Amazon Aurora connection management](Aurora.Overview.Endpoints.md)\.  

**Tip**  
Within each AWS Region, Availability Zones represent locations that are distinct from each other to provide isolation in case of outages\. We recommend that you distribute the primary instance and reader instances in your DB cluster over multiple Availability Zones to improve the availability of your DB cluster\. That way, an issue that affects an entire Availability Zone doesn't cause an outage for your cluster\.   
You can set up a Multi\-AZ cluster by making a simple choice when you create the cluster\. The choice is simple whether you use the AWS Management Console, the AWS CLI, or the Amazon RDS API\. You can also make an existing Aurora cluster into a Multi\-AZ cluster by adding a new reader instance and specifying a different Availability Zone\. 

## High availability across AWS Regions with Aurora global databases<a name="Concepts.AuroraHighAvailability.GlobalDB"></a>

 For high availability across multiple AWS Regions, you can set up Aurora global databases\. Each Aurora global database spans multiple AWS Regions, enabling low latency global reads and disaster recovery from outages across an AWS Region\. Aurora automatically handles replicating all data and updates from the primary AWS Region to each of the secondary Regions\. For more information, see [Using Amazon Aurora global databases](aurora-global-database.md)\. 

## Fault tolerance for an Aurora DB cluster<a name="Aurora.Managing.FaultTolerance"></a>

An Aurora DB cluster is fault tolerant by design\. The cluster volume spans multiple Availability Zones in a single AWS Region, and each Availability Zone contains a copy of the cluster volume data\. This functionality means that your DB cluster can tolerate a failure of an Availability Zone without any loss of data and only a brief interruption of service\.

If the primary instance in a DB cluster using single\-master replication fails, Aurora automatically fails over to a new primary instance in one of two ways:
+ By promoting an existing Aurora Replica to the new primary instance
+ By creating a new primary instance

If the DB cluster has one or more Aurora Replicas, then an Aurora Replica is promoted to the primary instance during a failure event\. A failure event results in a brief interruption, during which read and write operations fail with an exception\. However, service is typically restored in less than 120 seconds, and often less than 60 seconds\. To increase the availability of your DB cluster, we recommend that you create at least one or more Aurora Replicas in two or more different Availability Zones\. 

You can customize the order in which your Aurora Replicas are promoted to the primary instance after a failure by assigning each replica a priority\. Priorities range from 0 for the first priority to 15 for the last priority\. If the primary instance fails, Amazon RDS promotes the Aurora Replica with the better priority to the new primary instance\. You can modify the priority of an Aurora Replica at any time\. Modifying the priority doesn't trigger a failover\. 

More than one Aurora Replica can share the same priority, resulting in promotion tiers\. If two or more Aurora Replicas share the same priority, then Amazon RDS promotes the replica that is largest in size\. If two or more Aurora Replicas share the same priority and size, then Amazon RDS promotes an arbitrary replica in the same promotion tier\. 

If the DB cluster doesn't contain any Aurora Replicas, then the primary instance is recreated during a failure event\. A failure event results in an interruption during which read and write operations fail with an exception\. Service is restored when the new primary instance is created, which typically takes less than 10 minutes\. Promoting an Aurora Replica to the primary instance is much faster than creating a new primary instance\.

**Note**  
Amazon Aurora also supports replication with an external MySQL database, or an RDS MySQL DB instance\. For more information, see [Replication between Aurora and MySQL or between Aurora and another Aurora DB cluster \(binlog replication\)](AuroraMySQL.Replication.MySQL.md)\.