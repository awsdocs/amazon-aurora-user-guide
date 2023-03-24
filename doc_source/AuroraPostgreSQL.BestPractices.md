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
+ [Using Amazon CloudWatch metrics to analyze resource usage for Aurora PostgreSQL](AuroraPostgreSQL_AnayzeResourceUsage.md)
+ [Diagnosing table and index bloat](AuroraPostgreSQL.diag-table-ind-bloat.md)

## Troubleshooting storage issues<a name="AuroraPostgreSQL.BestPractices.TroubleshootingStorage"></a>

If the amount of working memory needed for sort or index\-creation operations exceeds the amount allocated by the `work_mem` parameter, Aurora PostgreSQL writes the excess data to temporary disk files\. When it writes the data, Aurora PostgreSQL uses the same storage space that it uses for storing error and message logs, that is, *local storage*\. Each instance in your Aurora PostgreSQL DB cluster has an amount of local storage available\. The amount of storage is based on its DB instance class\. To increase the amount of local storage, you need to modify the instance to use a larger DB instance class\. For DB instance class specifications, see [Hardware specifications for DB instance classes for Aurora](Concepts.DBInstanceClass.md#Concepts.DBInstanceClass.Summary)\. 

You can monitor your Aurora PostgreSQL DB cluster's local storage space by watching the Amazon CloudWatch metric for `FreeLocalStorage`\. This metric reports the amount of storage available to each DB instance in the Aurora DB cluster for temporary tables and logs\. For more information, see [Monitoring Amazon Aurora metrics with Amazon CloudWatch](monitoring-cloudwatch.md)\. 

Sorting, indexing, and grouping operations start in working memory but often must be offloaded to local storage\. If your Aurora PostgreSQL DB cluster runs out of local storage because of these types of operations, you can resolve the issue by taking one of the following actions\.
+ Increase the amount of working memory\. This reduces the need to use local storage\. By default, PostgreSQL allocates 4 MB for each sort, group, and index operation\. To check the current working memory value for your Aurora PostgreSQL DB cluster's writer instance, connect to the instance using `psql` and run the following command\.

  ```
  postgres=> SHOW work_mem;
  work_mem
  ----------
   4MB
  (1 row)
  ```

  You can increase the working memory at the session level before sort, group, and other operations, as follows\.

  ```
  SET work_mem TO '1 GB';
  ```

  For more information about working memory, see [Resource Consumption](https://www.postgresql.org/docs/current/runtime-config-resource.html#RUNTIME-CONFIG-RESOURCE-MEMORY) in the PostgreSQL documentation\. 
+ Change the log retention period so that logs are stored for shorter timeframes\. To learn how, see [Aurora PostgreSQL database log files](USER_LogAccess.Concepts.PostgreSQL.md)\.

For Aurora PostgreSQL DB clusters larger than 40 TB, don't use db\.t2, db\.t3, or db\.t4g instance classes\. We recommend using the T DB instance classes only for development and test servers, or other non\-production servers\. For more information, see [DB instance class types](Concepts.DBInstanceClass.md#Concepts.DBInstanceClass.Types)\.

## Avoiding slow performance, automatic restart, and failover for Aurora PostgreSQL DB instances<a name="AuroraPostgreSQL.BestPractices.Avoiding"></a>

If you're running a heavy workload or workloads that spike beyond the allocated resources of your DB instance, you can exhaust the resources on which you're running your application and Aurora database\. To get metrics on your database instance such as CPU utilization, memory usage, and number of database connections used, you can refer to the metrics provided by Amazon CloudWatch, Performance Insights, and Enhanced Monitoring\. For more information on monitoring your DB instance, see [Monitoring metrics in an Amazon Aurora cluster](MonitoringAurora.md)\.

If your workload exhausts the resources you're using, your DB instance might slow down, restart, or even fail over to another DB instance\. To avoid this, monitor your resource utilization, examine the workload running on your DB instance, and make optimizations where necessary\. If optimizations don't improve the instance metrics and mitigate the resource exhaustion, consider scaling up your DB instance before you reach its limits\. For more information on available DB instance classes and their specifications, see [Aurora DB instance classes](Concepts.DBInstanceClass.md)\.