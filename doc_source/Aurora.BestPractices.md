# Best practices with Amazon Aurora<a name="Aurora.BestPractices"></a>

Following, you can find information on general best practices and options for using or migrating data to an Amazon Aurora DB cluster\.

Some of the best practices for Amazon Aurora are specific to a particular database engine\. For more information about Aurora best practices specific to a database engine, see the following\.


| Database engine | Best practices | 
| --- | --- | 
|  Amazon Aurora MySQL  |  See [Best practices with Amazon Aurora MySQL](AuroraMySQL.BestPractices.md)  | 
|  Amazon Aurora PostgreSQL  |  See [Best practices with Amazon Aurora PostgreSQL](AuroraPostgreSQL.BestPractices.md)  | 

**Note**  
For common recommendations for Aurora, see [Viewing Amazon Aurora recommendations](accessing-monitoring.md#USER_Recommendations)\.

**Topics**
+ [Basic operational guidelines for Amazon Aurora](#Aurora.BestPractices.OperationalGuidelines)
+ [DB instance RAM recommendations](#Aurora.BestPractices.Performance.Sizing)
+ [Monitoring Amazon Aurora](#Aurora.BestPractices.Monitoring)
+ [Working with DB parameter groups and DB cluster parameter groups](#Aurora.BestPractices.ParameterGroups)
+ [Amazon Aurora best practices video](#Aurora.BestPractices.Presentation)

## Basic operational guidelines for Amazon Aurora<a name="Aurora.BestPractices.OperationalGuidelines"></a>

The following are basic operational guidelines that everyone should follow when working with Amazon Aurora\. The Amazon RDS Service Level Agreement requires that you follow these guidelines:
+ Monitor your memory, CPU, and storage usage\. You can set up Amazon CloudWatch to notify you when usage patterns change or when you approach the capacity of your deployment\. This way, you can maintain system performance and availability\.
+ If your client application is caching the Domain Name Service \(DNS\) data of your DB instances, set a time\-to\-live \(TTL\) value of less than 30 seconds\. The underlying IP address of a DB instance can change after a failover\. Thus, caching the DNS data for an extended time can lead to connection failures if your application tries to connect to an IP address that no longer is in service\. Aurora DB clusters with multiple read replicas can experience connection failures also when connections use the reader endpoint and one of the read replica instances is in maintenance or is deleted\.
+ Test failover for your DB cluster to understand how long the process takes for your use case\. Testing failover can help you ensure that the application that accesses your DB cluster can automatically connect to the new DB cluster after failover\. 

## DB instance RAM recommendations<a name="Aurora.BestPractices.Performance.Sizing"></a>

To optimize performance, allocate enough RAM so that your working set resides almost completely in memory\. To determine whether your working set is almost all in memory, examine the following metrics in Amazon CloudWatch:
+ `VolumeReadIOPS` – This metric measures the average number of read I/O operations from a cluster volume, reported at 5\-minute intervals\. The value of `VolumeReadIOPS` should be small and stable\. In some cases, you might find your read I/O is spiking or is higher than usual\. If so, investigate the DB instances in your DB cluster to see which DB instances are causing the increased I/O\.
**Tip**  
 If your Aurora MySQL cluster uses parallel query, you might see an increase in `VolumeReadIOPS` values\. Parallel queries don't use the buffer pool\. Thus, although the queries are fast, this optimized processing can result in an increase in read operations and associated charges\. 
+ `BufferCacheHitRatio` – This metric measures the percentage of requests that are served by the buffer cache of a DB instance in your DB cluster\. This metric gives you an insight into the amount of data that is being served from memory\.

  A high hit ratio indicates that your DB instance has enough memory available\. A low hit ratio indicates that your queries on this DB instance are frequently going to disk\. Investigate your workload to see which queries are causing this behavior\.

If, after investigating your workload, you find that you need more memory, consider scaling up the DB instance class to a class with more RAM\. After doing so, you can investigate the metrics discussed preceding and continue to scale up as necessary\. If your Aurora cluster is larger than 40 TB, don't use db\.t2, db\.t3, or db\.t4g instance classes\.

For more information, see [Amazon CloudWatch metrics for Amazon Aurora](Aurora.AuroraMonitoring.Metrics.md)\.

## Monitoring Amazon Aurora<a name="Aurora.BestPractices.Monitoring"></a>

Amazon Aurora provides a variety of Amazon CloudWatch metrics that you can monitor to determine the health and performance of your Aurora DB cluster\. You can use various tools, such as the AWS Management Console, AWS CLI, and CloudWatch API, to view Aurora metrics\. For more information, see [Monitoring metrics in an Amazon Aurora cluster](MonitoringAurora.md)\.

## Working with DB parameter groups and DB cluster parameter groups<a name="Aurora.BestPractices.ParameterGroups"></a>

We recommend that you try out DB parameter group and DB cluster parameter group changes on a test DB cluster before applying parameter group changes to your production DB cluster\. Improperly setting DB engine parameters can have unintended adverse effects, including degraded performance and system instability\.

Always use caution when modifying DB engine parameters, and back up your DB cluster before modifying a DB parameter group\. For information about backing up your DB cluster, see [Backing up and restoring an Amazon Aurora DB cluster](BackupRestoreAurora.md)\.

## Amazon Aurora best practices video<a name="Aurora.BestPractices.Presentation"></a>

The AWS Online Tech Talks channel on YouTube includes a video presentation on best practices for creating and configuring an Amazon Aurora DB cluster to be more secure and highly available\.

[![AWS Videos](http://img.youtube.com/vi/https://www.youtube.com/embed/ydzd95r4_VQ/0.jpg)](http://www.youtube.com/watch?v=https://www.youtube.com/embed/ydzd95r4_VQ)