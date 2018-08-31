# Amazon Aurora Endpoints<a name="Aurora.Overview.Endpoints"></a>

You can connect to DB instances in an Amazon Aurora DB cluster by using an endpoint\. An endpoint is a URL that contains a host address and a port\. The following endpoints are available from an Aurora DB cluster\.

**Cluster endpoint**  
A cluster endpoint is an endpoint for an Aurora DB cluster that connects to the current primary DB instance for that DB cluster\. Each Aurora DB cluster has a cluster endpoint and one primary DB instance\.  
The cluster endpoint provides failover support for read/write connections to the DB cluster\. Use the cluster endpoint for all write operations on the DB cluster, including inserts, updates, deletes, and data definition language \(DDL\) changes\. You can also use the cluster endpoint for read operations, such as queries\.  
If the current primary DB instance of a DB cluster fails, Aurora automatically fails over to a new primary DB instance\. During a failover, the DB cluster continues to serve connection requests to the cluster endpoint from the new primary DB instance, with minimal interruption of service\.  
The following example illustrates a cluster endpoint for an Aurora MySQL DB cluster\.  
`mydbcluster.cluster-123456789012.us-east-1.rds.amazonaws.com:3306`

**Reader endpoint**  
A reader endpoint is an endpoint for an Aurora DB cluster that connects to one of the available Aurora Replicas for that DB cluster\. Each Aurora DB cluster has a reader endpoint\. If there is more than one Aurora Replica, the reader endpoint directs each connection request to one of the Aurora Replicas\.  
The reader endpoint provides load balancing support for read\-only connections to the DB cluster\. Use the reader endpoint for read operations, such as queries\. You can't use the reader endpoint for write operations\.  
The DB cluster distributes connection requests to the reader endpoint among available Aurora Replicas\. If the DB cluster contains only a primary DB instance, the reader endpoint serves connection requests from the primary DB instance\. If an Aurora Replica is created for that DB cluster, the reader endpoint continues to serve connection requests to the reader endpoint from the new Aurora Replica, with minimal interruption in service\.  
The following example illustrates a reader endpoint for an Aurora MySQL DB cluster\.  
`mydbcluster.cluster-ro-123456789012.us-east-1.rds.amazonaws.com:3306`

**Instance endpoint**  
An instance endpoint is an endpoint for a DB instance in an Aurora DB cluster that connects to that specific DB instance\. Each DB instance in a DB cluster, regardless of instance type, has its own unique instance endpoint\. So, there is one instance endpoint for the current primary DB instance of the DB cluster, and there is one instance endpoint for each of the Aurora Replicas in the DB cluster\.  
The instance endpoint provides direct control over connections to the DB cluster, for scenarios where using the cluster endpoint or reader endpoint might not be appropriate\. For example, your client application might require more fine\-grained load balancing based on workload type\. In this case, you can configure multiple clients to connect to different Aurora Replicas in a DB cluster to distribute read workloads\. For an example that uses instance endpoints to improve connection speed after a failover for Aurora PostgreSQL, see [Fast Failover with Amazon Aurora PostgreSQL](AuroraPostgreSQL.BestPractices.md#AuroraPostgreSQL.BestPractices.FastFailover)\. For an example that uses instance endpoints to improve connection speed after a failover for Aurora MySQL, see [ MariaDB Connector/J failover support – case Amazon Aurora](https://mariadb.org/mariadb-connectorj-failover-support-case-amazon-aurora/)\.  
The following example illustrates an instance endpoint for a DB instance in an Aurora MySQL DB cluster\.  
`mydbinstance.123456789012.us-east-1.rds.amazonaws.com:3306`

## Endpoint Considerations<a name="Aurora.Overview.Endpoints.Considerations"></a>

Some considerations for working with Aurora endpoints are as follows:
+ Before using an instance endpoint to connect to a specific DB instance in a DB cluster, consider using the cluster endpoint or reader endpoint for the DB cluster instead\.

  The cluster endpoint and reader endpoint provide support for high\-availability scenarios\. If the primary DB instance of a DB cluster fails, Aurora automatically fails over to a new primary DB instance\. It does so by either promoting an existing Aurora Replica to a new primary DB instance or creating a new primary DB instance\. If a failover occurs, you can use the cluster endpoint to reconnect to the newly promoted or created primary DB instance, or use the reader endpoint to reconnect to one of the other Aurora Replicas in the DB cluster\. 

  If you don't take this approach, you can still make sure that you're connecting to the right DB instance in the DB cluster for the intended operation\. To do so, you can manually or programmatically discover the resulting set of available DB instances in the DB cluster and confirm their instance types after failover, before using the instance endpoint of a specific DB instance\.

  For more information about failovers, see [Fault Tolerance for an Aurora DB Cluster](Aurora.Managing.Backups.md#Aurora.Managing.FaultTolerance)\.
+ The reader endpoint only load\-balances connections to available Aurora Replicas in an Aurora DB cluster\. It does not load\-balance specific queries\. If you want to load\-balance queries to distribute the read workload for a DB cluster, you need to manage that in your application and use instance endpoints to connect directly to Aurora Replicas to balance the load\.
+ During a failover, the reader endpoint might direct connections to the new primary DB instance of a DB cluster for a short time, when an Aurora Replica is promoted to the new primary DB instance\.