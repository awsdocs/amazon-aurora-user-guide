# Availability of Aurora metrics in the Amazon RDS console<a name="Aurora.Monitoring.Metrics.RDSAvailability"></a>

Not all of the metrics provided by Amazon Aurora are available to you in the Amazon RDS console\. You can view them using other tools, however, such as the AWS CLI and CloudWatch API\. In addition, some of the metrics that are available in the Amazon RDS console are either shown only for specific instance classes, or with different names and different units of measurement\. 

## Aurora metrics that aren't available<a name="Aurora.Monitoring.Metrics.RDSAvailability.unavailable"></a>

The following Aurora metrics aren't available in the Amazon RDS console:
+ `AuroraBinlogReplicaLag`
+ `DeleteLatency`
+ `DeleteThroughput`
+ `EngineUptime`
+ `InsertLatency`
+ `InsertThroughput`
+ `NetworkThroughput`
+ `Queries`
+ `UpdateLatency`
+ `UpdateThroughput`

## Aurora metrics available in specific cases<a name="Aurora.Monitoring.Metrics.RDSAvailability.specific-cases"></a>

In addition, some Aurora metrics are either shown only for specific instance classes, or only for DB instances, or with different names and different units of measurement:
+ The `CPUCreditBalance` and `CPUCreditUsage` metrics are displayed only for Aurora MySQL `db.t2` instance classes and for Aurora PostgreSQL `db.t3` instance classes\.
+ The following metrics that are displayed with different names, as listed:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Monitoring.Metrics.RDSAvailability.html)
+ The following metrics apply to an entire Aurora DB cluster, but are displayed only when viewing DB instances for an Aurora DB cluster in the Amazon RDS console:
  + `VolumeBytesUsed`
  + `VolumeReadIOPs`
  + `VolumeWriteIOPs`
+ The following metrics are displayed in megabytes, instead of bytes, in the Amazon RDS console:
  + `FreeableMemory`
  + `FreeLocalStorage`
  + `NetworkReceiveThroughput`
  + `NetworkTransmitThroughput`

## Latest Metrics view<a name="Aurora.Monitoring.Metrics.RDSAvailability.LMV"></a>

You can view a subset of categorized Aurora metrics in the Latest Metrics view of the Amazon RDS console\. The following table lists the categories and associated metrics displayed in the Amazon RDS console for an Aurora instance\.


| Category | Metrics | 
| --- | --- | 
| SQL |  `ActiveTransactions` `BlockedTransactions` `BufferCacheHitRatio` `CommitLatency` `CommitThroughput` `DatabaseConnections` `DDLLatency` `DDLThroughput` `Deadlocks` `DMLLatency` `DMLThroughput` `LoginFailures` `ResultSetCacheHitRatio` `SelectLatency` `SelectThroughput`  | 
| System |  `AuroraReplicaLag` `AuroraReplicaLagMaximum` `AuroraReplicaLagMinimum` `CPUCreditBalance` `CPUCreditUsage` `CPUUtilization` `FreeableMemory` `FreeLocalStorage` `NetworkReceiveThroughput`  | 
| Deployment |  `AuroraReplicaLag` `BufferCacheHitRatio` `ResultSetCacheHitRatio` `SelectThroughput`  | 