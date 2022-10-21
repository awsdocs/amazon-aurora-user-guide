# Managing Amazon Aurora PostgreSQL<a name="AuroraPostgreSQL.Managing"></a>

The following section discusses managing performance and scaling for an Amazon Aurora PostgreSQL DB cluster\. It also includes information about other maintenance tasks\.

**Topics**
+ [Scaling Aurora PostgreSQL DB instances](#AuroraPostgreSQL.Managing.Performance.InstanceScaling)
+ [Maximum connections to an Aurora PostgreSQL DB instance](#AuroraPostgreSQL.Managing.MaxConnections)
+ [Temporary storage limits for Aurora PostgreSQL](#AuroraPostgreSQL.Managing.TempStorage)
+ [Testing Amazon Aurora PostgreSQL by using fault injection queries](AuroraPostgreSQL.Managing.FaultInjectionQueries.md)
+ [Displaying volume status for an Aurora PostgreSQL DB cluster](AuroraPostgreSQL.Managing.VolumeStatus.md)
+ [Specifying the RAM disk for the stats\_temp\_directory](AuroraPostgreSQL.Managing.RamDisk.md)

## Scaling Aurora PostgreSQL DB instances<a name="AuroraPostgreSQL.Managing.Performance.InstanceScaling"></a>

You can scale Aurora PostgreSQL DB instances in two ways, instance scaling and read scaling\. For more information about read scaling, see [Read scaling](Aurora.Managing.Performance.md#Aurora.Managing.Performance.ReadScaling)\.

You can scale your Aurora PostgreSQL DB cluster by modifying the DB instance class for each DB instance in the DB cluster\. Aurora PostgreSQL supports several DB instance classes optimized for Aurora\. Don't use db\.t2 or db\.t3 instance classes for larger Aurora clusters of size greater than 40 terabytes \(TB\)\. 

Scaling isn't instantaneous\. It can take 15 minutes or more to complete the change to a different DB instance class\. If you use this approach to modify the DB instance class, you apply the change during the next scheduled maintenance window \(rather than immediately\) to avoid affecting users\. 

As an alternative to modifying the DB instance class directly, you can minimize downtime by using the high availability features of Amazon Aurora\. First, add an Aurora Replica to your cluster\. When creating the replica, choose the DB instance class size that you want to use for your cluster\. When the Aurora Replica is synchronized with the cluster, you then failover to the newly added Replica\. To learn more, see [Aurora Replicas](Aurora.Replication.md#Aurora.Replication.Replicas) and [Fast failover with Amazon Aurora PostgreSQL](AuroraPostgreSQL.BestPractices.FastFailover.md)\. 

For detailed specifications of the DB instance classes supported by Aurora PostgreSQL, see [Supported DB engines for DB instance classes](Concepts.DBInstanceClass.md#Concepts.DBInstanceClass.SupportAurora)\.

## Maximum connections to an Aurora PostgreSQL DB instance<a name="AuroraPostgreSQL.Managing.MaxConnections"></a>

An Aurora PostgreSQL DB cluster allocates resources based on the DB instance class and its available memory\. Each connection to the DB cluster consumes incremental amounts of these resources, such as memory and CPU\. Memory consumed per connection varies based on query type, count, and whether temporary tables are used\. Even an idle connection consumes memory and CPU\. That's because when queries run on a connection, more memory is allocated for each query and it's not released completely, even when processing stops\. Thus, we recommend that you make sure your applications aren't holding on to idle connections: each one of these wastes resources and affects performance negatively\. For more information, see [Resources consumed by idle PostgreSQL connections](http://aws.amazon.com/blogs/database/resources-consumed-by-idle-postgresql-connections/)\. 

The maximum number of connections allowed by an Aurora PostgreSQL DB instance is determined by the `max_connections` parameter value specified in the parameter group for that DB instance\. The ideal setting for the `max_connections` parameter is one that supports all the client connections your application needs, without an excess of unused connections, plus at least 3 more connections to support AWS automation\. Before modifying the `max_connections` parameter setting, we recommend that you consider the following:
+ If the `max_connections` value is too low, the Aurora PostgreSQL DB instance might not have sufficient connections available when clients attempt to connect\. If this happens, attempts to connect using `psql` raise error messages such as the following: 

  ```
  psql: FATAL: remaining connection slots are reserved for non-replication superuser connections
  ```
+ If the `max_connections` value exceeds the number of connections that are actually needed, the unused connections can cause performance to degrade\.

The value of `max_connections` is derived from the following Aurora PostgreSQL `LEAST` function:

`LEAST({DBInstanceClassMemory/9531392},5000)`

If you want to change the value for `max_connections`, you need to create a custom DB cluster parameter group and change its value there\. After applying your custom DB parameter group to your cluster, be sure to reboot the primary instance so the new value takes effect\. For more information, see [Amazon Aurora PostgreSQL parameters](AuroraPostgreSQL.Reference.ParameterGroups.md) and [Creating a DB cluster parameter group](USER_WorkingWithDBClusterParamGroups.md#USER_WorkingWithParamGroups.CreatingCluster)\. 

Following, you can find a table that lists the highest value that should ever be used for `max_connections` for each DB instance class that can be used with Aurora PostgreSQL\. If your application needs more connections than the number listed for your DB instance class, consider choosing a DB instance class that has more memory\. If your connection requirements are too high for the DB instance class supporting your Aurora PostgreSQL DB cluster, you can potentially overload your database and decrease performance\. For list of DB instance classes for Aurora PostgreSQL, see [Supported DB engines for DB instance classes](Concepts.DBInstanceClass.md#Concepts.DBInstanceClass.SupportAurora)\. For the amount of memory for each DB instance class, see [Hardware specifications for DB instance classes for Aurora](Concepts.DBInstanceClass.md#Concepts.DBInstanceClass.Summary)\.

**Tip**  
If your applications frequently open and close connections, or keep a large number of long\-lived connections open, we recommend that you use Amazon RDS Proxy\. RDS Proxy is a fully managed, highly available database proxy that uses connection pooling to share database connections securely and efficiently\. To learn more about RDS Proxy, see [Using Amazon RDS Proxy](rds-proxy.md)\.

 For details about how Aurora Serverless v2 instances handle this parameter, see [Parameters that Aurora computes based on Aurora Serverless v2 maximum capacity](aurora-serverless-v2.setting-capacity.md#aurora-serverless-v2.parameters-based-on-max-capacity)\. 


| Instance class | Default max\_connections value | 
| --- | --- | 
| db\.x2g\.16xlarge | 5000 | 
| db\.x2g\.12xlarge | 5000 | 
| db\.x2g\.8xlarge | 5000 | 
| db\.x2g\.4xlarge | 5000 | 
| db\.x2g\.2xlarge | 5000 | 
| db\.x2g\.xlarge | 5000 | 
| db\.x2g\.large | 3479 | 
| db\.r6g\.16xlarge | 5000 | 
| db\.r6g\.12xlarge | 5000 | 
| db\.r6g\.8xlarge | 5000 | 
| db\.r6g\.4xlarge | 5000 | 
| db\.r6g\.2xlarge | 5000 | 
| db\.r6g\.xlarge | 3479 | 
| db\.r6g\.large | 1722 | 
| db\.r5\.24xlarge | 5000 | 
| db\.r5\.16xlarge | 5000 | 
| db\.r5\.12xlarge | 5000 | 
| db\.r5\.8xlarge | 5000 | 
| db\.r5\.4xlarge | 5000 | 
| db\.r5\.2xlarge | 5000 | 
| db\.r5\.xlarge | 3300 | 
| db\.r5\.large | 1600 | 
| db\.r4\.16xlarge | 5000 | 
| db\.r4\.8xlarge | 5000 | 
| db\.r4\.4xlarge | 5000 | 
| db\.r4\.2xlarge | 5000 | 
| db\.r4\.xlarge | 3200 | 
| db\.r4\.large | 1600 | 
| db\.t4g\.large | 844 | 
| db\.t4g\.medium | 405 | 
| db\.t3\.large | 844 | 
| db\.t3\.medium | 420 | 

## Temporary storage limits for Aurora PostgreSQL<a name="AuroraPostgreSQL.Managing.TempStorage"></a>

Aurora PostgreSQL stores tables and indexes in the Aurora storage subsystem\. Aurora PostgreSQL uses separate temporary storage for non\-persistent temporary files\. This includes files that are used for such purposes as sorting large data sets during query processing or for index build operations\. These local storage volumes are backed by Amazon Elastic Block Store and can be extended by using a larger DB instance class\. For more information about storage, see [Amazon Aurora storage and reliability](Aurora.Overview.StorageReliability.md)\.

The following table shows the maximum amount of temporary storage available for each Aurora PostgreSQL DB instance class\. For more information on DB instance class support for Aurora, see [Aurora DB instance classes](Concepts.DBInstanceClass.md)\.


| DB instance class | Maximum temporary storage available \(GiB\) | 
| --- | --- | 
| db\.x2g\.16xlarge | 1829 | 
| db\.x2g\.12xlarge | 1606 | 
| db\.x2g\.8xlarge | 1071 | 
| db\.x2g\.4xlarge | 535 | 
| db\.x2g\.2xlarge | 268 | 
| db\.x2g\.xlarge | 134 | 
| db\.x2g\.large | 67 | 
| db\.r6g\.16xlarge | 1008 | 
| db\.r6g\.12xlarge | 756 | 
| db\.r6g\.8xlarge | 504 | 
| db\.r6g\.4xlarge | 252 | 
| db\.r6g\.2xlarge | 126 | 
| db\.r6g\.xlarge | 63 | 
| db\.r6g\.large | 32 | 
| db\.r5\.24xlarge | 1500 | 
| db\.r5\.16xlarge | 1008 | 
| db\.r5\.12xlarge | 748 | 
| db\.r5\.8xlarge | 504 | 
| db\.r5\.4xlarge | 249 | 
| db\.r5\.2xlarge | 124 | 
| db\.r5\.xlarge | 62 | 
| db\.r5\.large | 31 | 
| db\.r4\.16xlarge | 960 | 
| db\.r4\.8xlarge | 480 | 
| db\.r4\.4xlarge | 240 | 
| db\.r4\.2xlarge | 120 | 
| db\.r4\.xlarge | 60 | 
| db\.r4\.large | 30 | 
| db\.t4g\.large | 16\.5 | 
| db\.t4g\.medium | 8\.13 | 
| db\.t3\.large | 16 | 
| db\.t3\.medium | 7\.5 | 

You can monitor the temporary storage available for a DB instance with the `FreeLocalStorage` CloudWatch metric, described in [Amazon CloudWatch metrics for Amazon Aurora](Aurora.AuroraMySQL.Monitoring.Metrics.md)\. \(This doesn't apply to Aurora Serverless v2\.\)

For some workloads, you can reduce the amount of temporary storage by allocating more memory to the processes that are performing the operation\. To increase the memory available to an operation, increasing the values of the [work\_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-WORK-MEM) or [maintenance\_work\_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM) PostgreSQL parameters\.