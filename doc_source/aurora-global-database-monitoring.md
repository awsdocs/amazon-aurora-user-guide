# Monitoring an Amazon Aurora global database<a name="aurora-global-database-monitoring"></a>

When you create the Aurora DB clusters that make up your Aurora global database, you can choose many options that let you monitor your DB cluster's performance\. These options include the following:
+ Amazon RDS Performance Insights – Enables performance schema in the underlying Aurora database engine\. To learn more about Performance Insights and Aurora global databases, see [Monitoring an Amazon Aurora global database with Amazon RDS Performance Insights](#aurora-global-database-pi)\. 
+ Enhanced monitoring – Generates metrics for process or thread utilization on the CPU\.
+ Amazon CloudWatch Logs – Publishes specified log types to CloudWatch Logs\. Error logs are published by default, but you can choose other logs specific to your Aurora database engine\.
  + For Aurora MySQL–based Aurora DB clusters, you can export the audit log, general log, and slow query log\.
  + For Aurora PostgreSQL–based Aurora DB clusters, you can export the Postgresql log\.
+ For Aurora PostgreSQL–based global databases, you can use certain functions to check status of your Aurora global database and its instances\. To learn how, see [Monitoring Aurora PostgreSQL\-based Aurora global databases](#aurora-global-database-monitoring.postgres)\. 

The following screenshot shows some of the options available on the Monitoring tab of a primary Aurora DB cluster in an Aurora global database\.

![\[Screenshot showing the Monitoring page for a selected Aurora DB cluster that's part of an Aurora global database\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-db-monitoring-options.png)

For more information, see [Monitoring an Amazon Aurora DB cluster](MonitoringAurora.md)\.

## Monitoring an Amazon Aurora global database with Amazon RDS Performance Insights<a name="aurora-global-database-pi"></a>

You can use Amazon RDS Performance Insights for your Aurora global databases\. You enable this feature individually, for each Aurora DB cluster in your Aurora global database\. To do so, you choose **Enable Performance Insights** in the **Additional configuration** section of the Create database page\. Or you can modify your Aurora DB clusters to use this feature after they are up and running\. You can enable or turn off Performance Insights for each cluster that's part of your Aurora global database\. 

The reports created by Performance Insights apply to each cluster in the global database\. When you add a new secondary AWS Region to an Aurora global database that's already using Performance Insights, be sure that you enable Performance Insights in the newly added cluster\. It doesn't inherit the Performance Insights setting from the existing global database\. 

You can switch AWS Regions while viewing the Performance Insights page for a DB instance that's attached to a global database\. However, you might not see performance information immediately after switching AWS Regions\. Although the DB instances might have identical names in each AWS Region, the associated Performance Insights URL is different for each DB instance\. After switching AWS Regions, choose the name of the DB instance again in the Performance Insights navigation pane\. 

For DB instances associated with a global database, the factors affecting performance might be different in each AWS Region\. For example, the DB instances in each AWS Region might have different capacity\.

To learn more about using Performance Insights, see [Using Performance Insights on Amazon Aurora](USER_PerfInsights.md)\. 

## Monitoring Aurora PostgreSQL\-based Aurora global databases<a name="aurora-global-database-monitoring.postgres"></a>

To view the status of a global database, use the `aurora_global_db_status` and `aurora_global_db_instance_status` functions\. 

**Note**  
Only Aurora PostgreSQL supports the `aurora_global_db_status` and `aurora_global_db_instance_status` functions\.

**To monitor an Aurora PostgreSQL\-based global database**

1. Connect to the global database primary cluster endpoint using a PostgreSQL utility such as psql\. For more information about how to connect, see [Connecting to an Amazon Aurora global database](aurora-global-database-connecting.md)\.

1. Use the `aurora_global_db_status` function in a psql command to list the primary and secondary volumes\. This shows the lag times of the global database secondary DB clusters\.

   ```
   postgres=> select * from aurora_global_db_status();
   ```

   ```
   aws_region | highest_lsn_written | durability_lag_in_msec | rpo_lag_in_msec | last_lag_calculation_time  | feedback_epoch | feedback_xmin
   ------------+---------------------+------------------------+-----------------+----------------------------+----------------+---------------
   us-east-1  |         93763984222 |                     -1 |              -1 | 1970-01-01 00:00:00+00     |              0 |             0
   us-west-2  |         93763984222 |                    900 |            1090 | 2020-05-12 22:49:14.328+00 |              2 |    3315479243
   (2 rows)
   ```

   The output includes a row for each DB cluster of the global database containing the following columns:
   + **aws\_region** – The AWS Region that this DB cluster is in\. For tables listing AWS Regions by engine, see [ Regions and Availability Zones](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Concepts.RegionsAndAvailabilityZones.html#Aurora.Overview.Availability)\.
   + **highest\_lsn\_written** – The highest log sequence number \(LSN\) currently written on this DB cluster\. 

     A *log sequence number \(LSN\)* is a unique sequential number that identifies a record in the database transaction log\. LSNs are ordered such that a larger LSN represents a later transaction\.
   + **durability\_lag\_in\_msec** – The timestamp difference between the highest log sequence number written on a secondary DB cluster \(`highest_lsn_written`\) and the `highest_lsn_written` on the primary DB cluster\.
   + **rpo\_lag\_in\_msec** – The recovery point objective \(RPO\) lag\. This lag is the time difference between the most recent user transaction commit stored on a secondary DB cluster and the most recent user transaction commit stored on the primary DB cluster\.
   + **last\_lag\_calculation\_time** – The timestamp when values were last calculated for `replication_lag_in_msec` and `rpo_lag_in_msec`\.
   + **feedback\_epoch** – The epoch the secondary DB cluster uses when it generates hot standby information\.

     *Hot standby *is when a DB cluster can connect and query while the server is in recovery or standby mode\. Hot standby feedback is information about the DB cluster when it's in hot standby\. For more information, see [Hot standby](https://www.postgresql.org/docs/current/hot-standby.html) in the PostgreSQL documentation\.
   + **feedback\_xmin** – The minimum \(oldest\) active transaction ID used by the secondary DB cluster\.

1. Use the `aurora_global_db_instance_status` function to list all secondary DB instances for both the primary DB cluster and secondary DB clusters\.

   ```
   postgres=> select * from aurora_global_db_instance_status();
   ```

   ```
   server_id                                   |              session_id              | aws_region | durable_lsn | highest_lsn_rcvd | feedback_epoch | feedback_xmin | oldest_read_view_lsn | visibility_lag_in_msec
   --------------------------------------------+--------------------------------------+------------+-------------+------------------+----------------+---------------+----------------------+------------------------
   apg-global-db-rpo-mammothrw-elephantro-1-n1 | MASTER_SESSION_ID                    | us-east-1  | 93763985102 |                  |                |               |                      |
   apg-global-db-rpo-mammothrw-elephantro-1-n2 | f38430cf-6576-479a-b296-dc06b1b1964a | us-east-1  | 93763985099 |      93763985102 |              2 |    3315479243 |          93763985095 |                     10
   apg-global-db-rpo-elephantro-mammothrw-n1   | 0d9f1d98-04ad-4aa4-8fdd-e08674cbbbfe | us-west-2  | 93763985095 |      93763985099 |              2 |    3315479243 |          93763985089 |                   1017
   (3 rows)
   ```

   The output includes a row for each DB instance of the global database containing the following columns:
   + **server\_id** – The server identifier for the DB instance\.
   + **session\_id** – A unique identifier for the current session\.
   + **aws\_region** – The AWS Region that this DB instance is in\. For tables listing AWS Regions by engine, see [ Regions and Availability Zones](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Concepts.RegionsAndAvailabilityZones.html#Aurora.Overview.Availability)\.
   + **durable\_lsn** – The LSN made durable in storage\.
   + **highest\_lsn\_rcvd** – The highest LSN received by the DB Instance from the writer DB Instance\.
   + **feedback\_epoch** – The epoch the DB instance uses when it generates hot standby information\.

     Hot standby is when a DB instance can connect and query while the server is in recovery or standby mode\. Hot standby feedback is information about the DB instance when it's in hot standby\. For more information, see the PostgreSQL documentation on [Hot standby](https://www.postgresql.org/docs/current/hot-standby.html)\.
   + **feedback\_xmin** – The minimum \(oldest\) active transaction ID used by the DB instance\.
   + **oldest\_read\_view\_lsn** – The oldest LSN used by the DB instance to read from storage\.
   + **visibility\_lag\_in\_msec** – How far this DB instance is lagging behind the writer DB instance\.

To see how these values change over time, consider the following transaction block where a table insert takes an hour\.

```
psql> BEGIN;
psql> INSERT INTO table1 SELECT Large_Data_That_Takes_1_Hr_To_Insert;
psql> COMMIT;
```

In some cases, there might be a network disconnect between the primary DB cluster and the secondary DB cluster after the `BEGIN` statement\. If so, the secondary DB cluster's `replication_lag_in_msec` value starts increasing\. At the end of the `INSERT` statement, the `replication_lag_in_msec` value is 1 hour\. However, the `rpo_lag_in_msec` value is 0 because all the user data committed between the primary DB cluster and secondary DB cluster are still the same\. As soon as the `COMMIT` statement completes, the `rpo_lag_in_msec` value increases\. 