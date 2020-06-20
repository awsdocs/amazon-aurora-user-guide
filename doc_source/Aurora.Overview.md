# Amazon Aurora DB Clusters<a name="Aurora.Overview"></a>

 An Amazon Aurora *DB cluster* consists of one or more DB instances and a cluster volume that manages the data for those DB instances\. An Aurora *cluster volume* is a virtual database storage volume that spans multiple Availability Zones, with each Availability Zone having a copy of the DB cluster data\. Two types of DB instances make up an Aurora DB cluster: 
+  **Primary DB instance** – Supports read and write operations, and performs all of the data modifications to the cluster volume\. Each Aurora DB cluster has one primary DB instance\. 
+  **Aurora Replica** – Connects to the same storage volume as the primary DB instance and supports only read operations\. Each Aurora DB cluster can have up to 15 Aurora Replicas in addition to the primary DB instance\. Maintain high availability by locating Aurora Replicas in separate Availability Zones\. Aurora automatically fails over to an Aurora Replica in case the primary DB instance becomes unavailable\. You can specify the failover priority for Aurora Replicas\. Aurora Replicas can also offload read workloads from the primary DB instance\. 
+ For Aurora multi\-master clusters, all DB instances have read/write capability\. In this case, the distinction between primary instance and Aurora Replica doesn't apply\. For discussing replication topology where the clusters can use either single\-master or multi\-master replication, we call these *writer* and *reader* DB instances\.

The following diagram illustrates the relationship between the cluster volume, the primary DB instance, and Aurora Replicas in an Aurora DB cluster\. 

![\[Amazon Aurora Architecture\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/AuroraArch001.png)

**Note**  
 The preceding information applies to all the Aurora clusters that use single\-master replication\. These include provisioned clusters, parallel query clusters, global database clusters, serverless clusters, and all MySQL 5\.7\-compatible and PostgreSQL\-compatible clusters\.   
 Aurora clusters that use multi\-master replication have a different arrangement of read/write and read\-only DB instances\. All DB instances in a multi\-master cluster can perform write operations\. There isn't a single DB instance that performs all the write operations, and there aren't any read\-only DB instances\. Therefore, the terms *primary instance* and *Aurora Replica* don't apply to multi\-master clusters\. When we discuss clusters that might use multi\-master replication, we refer to *writer* DB instances and *reader* DB instances\. 

 The Aurora cluster illustrates the separation of compute capacity and storage\. For example, an Aurora configuration with only a single DB instance is still a cluster, because the underlying storage volume involves multiple storage nodes distributed across multiple Availability Zones \(AZs\)\. 