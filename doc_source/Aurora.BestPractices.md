# Best Practices with Amazon Aurora<a name="Aurora.BestPractices"></a>

This topic includes information on general best practices and options for using or migrating data to an Amazon Aurora DB cluster\.

Some of the best practices for Amazon Aurora are specific to a particular database engine\. For more information about Aurora best practices specific to a database engine, see the following:


| Database Engine | Best Practices | 
| --- | --- | 
|  Amazon Aurora MySQL  |  See [Best Practices with Amazon Aurora MySQL](AuroraMySQL.BestPractices.md)  | 
|  Amazon Aurora PostgreSQL  |  See [Best Practices with Amazon Aurora PostgreSQL](AuroraPostgreSQL.BestPractices.md)  | 

**Note**  
For common recommendations for Aurora, see [Using Amazon Aurora Recommendations](USER_Recommendations.md)\.

**Topics**
+ [Amazon Aurora Basic Operational Guidelines](#Aurora.BestPractices.OperationalGuidelines)
+ [DB Instance RAM Recommendations](#Aurora.BestPractices.Performance.Sizing)
+ [Monitoring Amazon Aurora](#Aurora.BestPractices.Monitoring)
+ [Working with DB Parameter Groups and DB Cluster Parameter Groups](#Aurora.BestPractices.ParameterGroups)
+ [Amazon Aurora Best Practices Presentation Video](#Aurora.BestPractices.Presentation)

## Amazon Aurora Basic Operational Guidelines<a name="Aurora.BestPractices.OperationalGuidelines"></a>

The following are basic operational guidelines that everyone should follow when working with Amazon Aurora\. Note that the Amazon RDS Service Level Agreement requires that you follow these guidelines:
+ Monitor your memory, CPU, and storage usage\. Amazon CloudWatch can be set up to notify you when usage patterns change or when you approach the capacity of your deployment, so that you can maintain system performance and availability\.
+ If your client application is caching the Domain Name Service \(DNS\) data of your DB instances, set a time\-to\-live \(TTL\) value of less than 30 seconds\. Because the underlying IP address of a DB instance can change after a failover, caching the DNS data for an extended time can lead to connection failures if your application tries to connect to an IP address that no longer is in service\. Aurora DB clusters with multiple read replicas can experience connection failures also when connections use the reader endpoint and one of the read replica instances is in maintenance or is deleted\.
+ Test failover for your DB cluster to understand how long the process takes for your use case and to ensure that the application that accesses your DB cluster can automatically connect to the new DB cluster after failover\. 

## DB Instance RAM Recommendations<a name="Aurora.BestPractices.Performance.Sizing"></a>

To optimize performance, allocate enough RAM so that your working set resides almost completely in memory\. To determine whether your working set is almost all in memory, examine the following metrics in Amazon CloudWatch:
+ `VolumeReadIOPS` – This measures the average number of read I/O operations from a cluster volume, reported at 5\-minute intervals\. The value of `VolumeReadIOPS` should be small and stable\. If you find your read IO is spiking or is higher than usual, you should investigate the DB instances in your DB cluster to see which DB instances are causing the increased IO\.
+ `BufferCacheHitRatio` – This metric measures the percentage of requests that are served by the buffer cache of a DB instance in your DB cluster\. This metric gives you an insight into the amount of data that is being served from memory\. If the hit ratio is low, it is a good indication that your queries on this DB instance are going to disk more often than not\. In this case, you should investigate your workload to see which queries are causing this behavior\.

If, after investigating your workload, you find that you need more memory, scaling up the DB instance class to a class with more RAM could be beneficial\. After doing so, you can investigate the metrics above and continue to scale up as necessary\. For more information about monitoring a DB cluster, see [Monitoring Amazon Aurora DB Cluster Metrics](Aurora.Monitoring.md)\.

## Monitoring Amazon Aurora<a name="Aurora.BestPractices.Monitoring"></a>

Amazon Aurora provides a variety of Amazon CloudWatch metrics that you can monitor to determine the health and performance of your Aurora DB cluster\. You can use various tools, such as the Amazon RDS Management Console, AWS CLI, and CloudWatch API, to view Aurora metrics\. For more information, see [Monitoring an Amazon Aurora DB Cluster](MonitoringAurora.md)\.

## Working with DB Parameter Groups and DB Cluster Parameter Groups<a name="Aurora.BestPractices.ParameterGroups"></a>

We recommend that you try out DB parameter group and DB cluster parameter group changes on a test DB cluster before applying parameter group changes to your production DB cluster\. Improperly setting DB engine parameters can have unintended adverse effects, including degraded performance and system instability\.

Always exercise caution when modifying DB engine parameters and back up your DB cluster before modifying a DB parameter group\. For information about backing up your DB cluster, see [Backing Up and Restoring an Amazon Aurora DB Cluster](BackupRestoreAurora.md)\.

## Amazon Aurora Best Practices Presentation Video<a name="Aurora.BestPractices.Presentation"></a>

The 2016 AWS Summit conference in Chicago included a presentation on best practices for creating and configuring a secure, highly available Amazon Aurora DB Cluster\. A video of the presentation is available here:

[![AWS Videos](http://img.youtube.com/vi/https://www.youtube.com/embed/DZFPYzp1JJA/0.jpg)](http://www.youtube.com/watch?v=https://www.youtube.com/embed/DZFPYzp1JJA)