# What Is Amazon Aurora?<a name="CHAP_AuroraOverview"></a>

 Amazon Aurora \(Aurora\) is a fully managed relational database engine that's compatible with MySQL and PostgreSQL\. You already know how MySQL and PostgreSQL combine the speed and reliability of high\-end commercial databases with the simplicity and cost\-effectiveness of open\-source databases\. The code, tools, and applications you use today with your existing MySQL and PostgreSQL databases can be used with Aurora\. With some workloads, Aurora can deliver up to five times the throughput of MySQL and up to three times the throughput of PostgreSQL without requiring changes to most of your existing applications\. 

 Aurora includes a high\-performance storage subsystem\. Its MySQL\- and PostgreSQL\-compatible database engines are customized to take advantage of that fast distributed storage\. The underlying storage grows automatically as needed, up to 64 terabytes\. Aurora also automates and standardizes database clustering and replication, which are typically among the most challenging aspects of database configuration and administration\. 

 Aurora is part of the managed database service Amazon Relational Database Service \(Amazon RDS\)\. Amazon RDS is a web service that makes it easier to set up, operate, and scale a relational database in the cloud\. If you are not already familiar with Amazon RDS, see the [ *Amazon Relational Database Service User Guide*](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Welcome.html)\. 

 The following points illustrate how Aurora relates to the standard MySQL and PostgreSQL engines available in Amazon RDS: 
+  You choose Aurora as a DB engine option when setting up new database servers through Amazon RDS\. 
+  Aurora takes advantage of the familiar Amazon Relational Database Service \(Amazon RDS\) features for management and administration\. Aurora uses the Amazon RDS AWS Management Console interface, AWS CLI commands, and API operations to handle routine database tasks such as provisioning, patching, backup, recovery, failure detection, and repair\. 
+  Aurora management operations typically involve entire clusters of database servers that are synchronized through replication, instead of individual database instances\. The automatic clustering, replication, and storage allocation make it simple and cost\-effective to set up, operate, and scale your largest MySQL and PostgreSQL deployments\. 
+  You can bring data from Amazon RDS for MySQL and Amazon RDS for PostgreSQL into Aurora by creating and restoring snapshots, or by setting up one\-way replication\. You can use push\-button migration tools to convert your existing Amazon RDS for MySQL and Amazon RDS for PostgreSQL applications to Aurora\. 

 Before using Amazon Aurora, you should complete the steps in [Setting Up Your Environment for Amazon Aurora](CHAP_SettingUp_Aurora.md), and then review the concepts and features of Aurora in [Amazon Aurora DB Clusters](Aurora.Overview.md)\. 

**Topics**
+ [Amazon Aurora DB Clusters](Aurora.Overview.md)
+ [Amazon Aurora Connection Management](Aurora.Overview.Endpoints.md)
+ [Using the Instance Endpoints](#Aurora.Endpoints.Instance)
+ [How Aurora Endpoints Work with High Availability](#Aurora.Overview.Endpoints.HA)
+ [Amazon Aurora Storage and Reliability](Aurora.Overview.StorageReliability.md)
+ [Amazon Aurora Security](Aurora.Overview.Security.md)
+ [High Availability for Aurora](Concepts.AuroraHighAvailability.md)
+ [Working with Amazon Aurora Global Database](aurora-global-database.md)
+ [Replication with Amazon Aurora](Aurora.Replication.md)

## Using the Instance Endpoints<a name="Aurora.Endpoints.Instance"></a>

 In day\-to\-day operations, the main way that you use instance endpoints is to diagnose capacity or performance issues that affect one specific instance in an Aurora cluster\. While connected to a specific instance, you can examine its status variables, metrics, and so on\. Doing this can help you determine what's happening for that instance that's different from what's happening for other instances in the cluster\. 

 In advanced use cases, you might configure some DB instances differently than others\. In this case, use the instance endpoint to connect directly to an instance that is smaller, larger, or otherwise has different characteristics than the others\. Also, set up failover priority so that this special DB instance is the last choice to take over as the primary instance\. We recommend that you use custom endpoints instead of the instance endpoint in such cases\. Doing so simplifies connection management and high availability as you add more DB instances to your cluster\. 

 Each DB instance in an Aurora cluster has its own built\-in instance endpoint, whose name and other attributes are managed by Aurora\. You can't create, delete, or modify this kind of endpoint\. 

## How Aurora Endpoints Work with High Availability<a name="Aurora.Overview.Endpoints.HA"></a>

 For clusters where high availability is important, use the writer endpoint for read\-write connections and the reader endpoint for read\-only connections\. These kinds of connections manage DB instance failover better than instance endpoints do\. The instance endpoints connect to a specific DB instance in a DB cluster, requiring logic in your application to choose a different endpoint if the DB instance becomes unavailable\. 

 If the primary DB instance of a DB cluster fails, Aurora automatically fails over to a new primary DB instance\. It does so by either promoting an existing Aurora Replica to a new primary DB instance or creating a new primary DB instance\. If a failover occurs, you can use the cluster endpoint to reconnect to the newly promoted or created primary DB instance, or use the reader endpoint to reconnect to one of the Aurora Replicas in the DB cluster\. During a failover, the reader endpoint might direct connections to the new primary DB instance of a DB cluster for a short time after an Aurora Replica is promoted to the new primary DB instance\. 

 If you design your own application logic to manage connections to instance endpoints, you can manually or programmatically discover the resulting set of available DB instances in the DB cluster\. You can then confirm their instance classes after failover and connect to an appropriate instance endpoint\. 

 For more information about failovers, see [Fault Tolerance for an Aurora DB Cluster](Aurora.Managing.Backups.md#Aurora.Managing.FaultTolerance)\. 