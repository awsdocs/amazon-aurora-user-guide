# Best practices with Amazon Aurora PostgreSQL<a name="AuroraPostgreSQL.BestPractices"></a>

Following, you can find several best practices for managing your Amazon Aurora PostgreSQL DB cluster\. Be sure to also review basic maintenance tasks\. For more information, see [Managing Amazon Aurora PostgreSQL](AuroraPostgreSQL.Managing.md)\. 

**Topics**
+ [Troubleshooting storage issues](#AuroraPostgreSQL.BestPractices.TroubleshootingStorage)
+ [Avoiding slow performance, automatic restart, and failover for Aurora PostgreSQL DB instances](#AuroraPostgreSQL.BestPractices.Avoiding)
+ [Fast failover with Amazon Aurora PostgreSQL](AuroraPostgreSQL.BestPractices.FastFailover.md)
+ [Fast recovery after failover with cluster cache management for Aurora PostgreSQL](AuroraPostgreSQL.cluster-cache-mgmt.md)
+ [Managing Aurora PostgreSQL connection churn with pooling](AuroraPostgreSQL.BestPractices.connection_pooling.md)
+ [Using logical replication to perform a major version upgrade for Aurora PostgreSQL](AuroraPostgreSQL.MajorVersionUpgrade.md)
+ [Tuning memory parameters for Aurora PostgreSQL](AuroraPostgreSQL.BestPractices.Tuning-memory-parameters.md)

## Troubleshooting storage issues<a name="AuroraPostgreSQL.BestPractices.TroubleshootingStorage"></a>

If the amount of memory required by a sort or index creation operation exceeds the amount of memory available, Aurora PostgreSQL writes the excess data to storage\. When it writes the data, it uses the same storage space that it uses for storing error and message logs\.

If your sorts or index creation functions exceed the memory available, you can run out of local storage\. If you experience issues with Aurora PostgreSQL running out of storage space, you have a couple of options\. You can either reconfigure your data sorts to use more memory, or reduce the data retention period for your PostgreSQL log files\. For more information about changing the log retention period, see [Aurora PostgreSQL database log files](USER_LogAccess.Concepts.PostgreSQL.md)\. 

If your Aurora cluster is larger than 40 TB, don't use db\.t2, db\.t3, or db\.t4g instance classes\.

## Avoiding slow performance, automatic restart, and failover for Aurora PostgreSQL DB instances<a name="AuroraPostgreSQL.BestPractices.Avoiding"></a>

If you're running a heavy workload or workloads that spike beyond the allocated resources of your DB instance, you can exhaust the resources on which you're running your application and Aurora database\. To get metrics on your database instance such as CPU utilization, memory usage, and number of database connections used, you can refer to the metrics provided by Amazon CloudWatch, Performance Insights, and Enhanced Monitoring\. For more information on monitoring your DB instance, see [Monitoring metrics in an Amazon Aurora cluster](MonitoringAurora.md)\.

If your workload exhausts the resources you're using, your DB instance might slow down, restart, or even fail over to another DB instance\. To avoid this, monitor your resource utilization, examine the workload running on your DB instance, and make optimizations where necessary\. If optimizations don't improve the instance metrics and mitigate the resource exhaustion, consider scaling up your DB instance before you reach its limits\. For more information on available DB instance classes and their specifications, see [Aurora DB instance classes](Concepts.DBInstanceClass.md)\.