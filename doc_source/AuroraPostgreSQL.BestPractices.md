# Best practices with Amazon Aurora PostgreSQL<a name="AuroraPostgreSQL.BestPractices"></a>

Following, you can find several best practices for managing your Amazon Aurora PostgreSQL DB cluster\. Be sure to also review basic maintenance tasks\. For more information, see [Managing Amazon Aurora PostgreSQL](AuroraPostgreSQL.Managing.md)\. 

**Topics**
+ [Troubleshooting storage issues](#AuroraPostgreSQL.BestPractices.TroubleshootingStorage)
+ [Fast failover with Amazon Aurora PostgreSQL](AuroraPostgreSQL.BestPractices.FastFailover.md)
+ [Fast recovery after failover with cluster cache management for Aurora PostgreSQL](AuroraPostgreSQL.cluster-cache-mgmt.md)
+ [Managing Aurora PostgreSQL connection churn with pooling](AuroraPostgreSQL.BestPractices.connection_pooling.md)
+ [Tuning memory parameters for Aurora PostgreSQL](AuroraPostgreSQL.BestPractices.Tuning-memory-parameters.md)

## Troubleshooting storage issues<a name="AuroraPostgreSQL.BestPractices.TroubleshootingStorage"></a>

If the amount of memory required by a sort or index creation operation exceeds the amount of memory available, Aurora PostgreSQL writes the excess data to storage\. When it writes the data, it uses the same storage space that it uses for storing error and message logs\.

If your sorts or index creation functions exceed the memory available, you can run out of local storage\. If you experience issues with Aurora PostgreSQL running out of storage space, you have a couple of options\. You can either reconfigure your data sorts to use more memory, or reduce the data retention period for your PostgreSQL log files\. For more information about changing the log retention period, see [PostgreSQL database log files](USER_LogAccess.Concepts.PostgreSQL.md)\. 

If your Aurora cluster is larger than 40 TB, don't use db\.t2, db\.t3, or db\.t4g instance classes\.