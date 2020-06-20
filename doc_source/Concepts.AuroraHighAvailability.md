# High Availability for Amazon Aurora<a name="Concepts.AuroraHighAvailability"></a>

 The Amazon Aurora architecture involves separation of storage and compute\. Aurora includes some high availability features that apply to the data in your DB cluster\. The data remains safe even if some or all of the DB instances in the cluster become unavailable\. Other high availability features apply to the DB instances\. These features help to make sure that one or more DB instances are ready to handle database requests from your application\. 

**Topics**
+ [High Availability for Aurora Data](#Concepts.AuroraHighAvailability.Data)
+ [High Availability for Aurora DB Instances](#Concepts.AuroraHighAvailability.Instances)
+ [High Availability Across AWS Regions with Aurora Global Databases](#Concepts.AuroraHighAvailability.GlobalDB)

## High Availability for Aurora Data<a name="Concepts.AuroraHighAvailability.Data"></a>

Aurora stores copies of the data in a DB cluster across multiple Availability Zones in a single AWS Region\. Aurora stores these copies regardless of whether the instances in the DB cluster span multiple Availability Zones\. For more information on Aurora, see [Managing an Amazon Aurora DB Cluster](CHAP_Aurora.md)\. 

When data is written to the primary DB instance, Aurora synchronously replicates the data across Availability Zones to six storage nodes associated with your cluster volume\. Doing so provides data redundancy, eliminates I/O freezes, and minimizes latency spikes during system backups\. Running a DB instance with high availability can enhance availability during planned system maintenance, and help protect your databases against failure and Availability Zone disruption\. For more information on Availability Zones, see [ Regions and Availability Zones ](Concepts.RegionsAndAvailabilityZones.md)\.

## High Availability for Aurora DB Instances<a name="Concepts.AuroraHighAvailability.Instances"></a>

For a cluster using single\-master replication, after you create the primary instance, you can create up to 15 read\-only Aurora Replicas\. The Aurora Replicas are also known as reader instances\. 

 During day\-to\-day operations, you can offload some of the work for read\-intensive applications by using the reader instances to process `SELECT` queries\. When a problem affects the primary instance, one of these reader instances takes over as the primary instance\. This mechanism is known as *failover*\. Many Aurora features apply to the failover mechanism\. For example, Aurora detects database problems and activates the failover mechanism automatically when necessary\. Aurora also has features that reduce the time for failover to complete\. Doing so minimizes the time that the database is unavailable for writing during a failover\. 

 To use a connection string that stays the same even when a failover promotes a new primary instance, you connect to the cluster endpoint\. The *cluster endpoint* always represents the current primary instance in the cluster\. For more information about the cluster endpoint, see [Amazon Aurora Connection Management](Aurora.Overview.Endpoints.md)\.  

**Tip**  
Within each AWS Region, Availability Zones represent locations that are distinct from each other to provide isolation in case of outages\. We recommend that you distribute the primary instance and reader instances in your DB cluster over multiple Availability Zones to improve the availability of your DB cluster\. That way, an issue that affects an entire Availability Zone doesn't cause an outage for your cluster\.   
You can set up a Multi\-AZ cluster by making a simple choice when you create the cluster\. The choice is simple whether you use the AWS Management Console, the AWS CLI, or the Amazon RDS API\. You can also make an existing Aurora cluster into a Multi\-AZ cluster by adding a new reader instance and specifying a different Availability Zone\. 

## High Availability Across AWS Regions with Aurora Global Databases<a name="Concepts.AuroraHighAvailability.GlobalDB"></a>

 For high availability across multiple AWS Regions, you can set up Aurora global databases\. Each Aurora global database spans multiple AWS Regions, enabling low latency global reads and disaster recovery from outages across an AWS Region\. Aurora automatically handles replicating all data and updates from the primary AWS Region to each of the secondary Regions\. For more information, see [Using Amazon Aurora Global Databases](aurora-global-database.md)\. 