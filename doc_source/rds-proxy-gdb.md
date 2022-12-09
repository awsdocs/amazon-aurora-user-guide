# Using RDS Proxy with Aurora global databases<a name="rds-proxy-gdb"></a>

An *Aurora global database* is a single database that spans multiple AWS Regions, allowing for low\-latency global reads and disaster recovery from any Region\-wide outage\. It provides built\-in fault tolerance for your deployment because the DB instance relies not on a single AWS Region, but upon multiple Regions and different Availability Zones\. For more information, see [Using Amazon Aurora global databases](aurora-global-database.md)\.

You can use RDS Proxy with any DB cluster in an Aurora global database\. Before you begin to use these features together, make sure that you familiarize yourself with the following information\. 

**Important**  
If the DB cluster is part of a global database with write forwarding turned on, reduce your proxy's `MaxConnectionsPercent` value by the quota that's allotted for write forwarding\. The write forwarding quota is set in the DB cluster parameter `aurora_fwd_writer_max_connections_pct`\. For information about write forwarding, see [Using write forwarding in an Amazon Aurora global database](aurora-global-database-write-forwarding.md)\.

## Limitations for RDS Proxy with global databases<a name="rds-proxy-gdb.limitations"></a>

When the Aurora DB cluster has write forwarding turned on, RDS Proxy doesn't support the `SESSION` value for the `aurora_replica_read_consistency` variable\. Setting this value can cause unexpected behavior\.

## How RDS Proxy endpoints work with global databases<a name="rds-proxy-gdb.endpoints"></a>

When you understand how RDS Proxy endpoints work with global databases, you can better manage your applications that use Aurora databases with both of these features\.

For a proxy with a global database's primary cluster as the registered target, the proxy endpoints work the same way as with any Aurora DB cluster\. The proxy's read/write endpoints send all requests to the writer instance of the cluster\. The proxy's read\-only endpoints send all requests to the reader instances\. If a reader becomes unavailable while a connection is open, RDS Proxy redirects subsequent queries on the connection to another reader instance\. For a proxy with a secondary cluster as the registered target, requests that are sent to the proxy's read\-only endpoints are also sent to the reader instances\. Because the cluster has no writer instances, requests that are sent to read/write endpoints fail with the error "`The target group doesn't have any associated read/write instances`"\.  

A global failover operation involves a role switch between the primary and one of the secondary DB clusters\. When the selected secondary cluster becomes the new primary, one of its reader instances is promoted to a writer\. This DB instance is now the new writer instance for the global cluster\. Make sure to redirect your application's write operations to the appropriate read/write endpoint of the proxy that's associated with the new primary cluster\. This proxy endpoint might be the default endpoint or a custom read/write endpoint\. 

RDS Proxy queues all requests through read/write endpoints and sends them to the writer instance of the new primary cluster as soon as it's available\. It does so regardless of whether the failover operation has completed\. During failover, the default endpoint of the proxy for the old primary cluster still accepts write operations\. However, as soon as that cluster becomes a secondary cluster, all of the write operations fail\. To learn how and when to perform specific global failover tasks, see the following topics:
+ Manual unplanned failover – [Recovering an Amazon Aurora global database from an unplanned outage](aurora-global-database-disaster-recovery.md#aurora-global-database-failover)
+ Managed planned failover – [Performing managed planned failovers for Amazon Aurora global databases](aurora-global-database-disaster-recovery.md#aurora-global-database-disaster-recovery.managed-failover)