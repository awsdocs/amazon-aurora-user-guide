# Managing performance and scaling for Aurora DB clusters<a name="Aurora.Managing.Performance"></a>

You can use the following options to manage performance and scaling for Aurora DB clusters and DB instances:

**Topics**
+ [Storage scaling](#Aurora.Managing.Performance.StorageScaling)
+ [Instance scaling](#Aurora.Managing.Performance.InstanceScaling)
+ [Read scaling](#Aurora.Managing.Performance.ReadScaling)
+ [Managing connections](#Aurora.Managing.MaxConnections)
+ [Managing query execution plans](#Aurora.Managing.Optimizing)

## Storage scaling<a name="Aurora.Managing.Performance.StorageScaling"></a>

Aurora storage automatically scales with the data in your cluster volume\. As your data grows, your cluster volume storage expands up to a maximum of 128 tebibytes \(TiB\) or 64 TiB\. The maximum size depends on the DB engine version\. To learn what kinds of data are included in the cluster volume, see [Amazon Aurora storage and reliability](Aurora.Overview.StorageReliability.md)\. For details about the maximum size for a specific version, see [Amazon Aurora size limits](CHAP_Limits.md#RDS_Limits.FileSize.Aurora)\.

The size of your cluster volume is evaluated on an hourly basis to determine your storage costs\. For pricing information, see the [Aurora pricing page](https://aws.amazon.com/rds/aurora/pricing)\.

 Even though an Aurora cluster volume can scale up in size to many tebibytes, you are only charged for the space that you use in the volume\. The mechanism for determining billed storage space depends on the version of your Aurora cluster\. 
+  When Aurora data is removed from the cluster volume, the overall billed space decreases by a comparable amount\. This dynamic resizing behavior happens when underlying database files are deleted or reorganized to require less space\. Thus, you can reduce storage charges by deleting tables, indexes, databases, and so on that you no longer need\. Dynamic resizing applies to certain Aurora versions\. The following are the Aurora versions where the cluster volume dynamically resizes as you delete data: 
  +  Aurora MySQL 3 \(compatible with MySQL 8\.0\), all minor versions
  +  Aurora MySQL 2\.09 \(compatible with MySQL 5\.7\) and higher
  +  Aurora MySQL version 1\.23 \(compatible with MySQL 5\.6\) and higher
  +  All Aurora PostgreSQL 14 and 13 versions
  +  Aurora PostgreSQL version 12\.4 and higher
  +  Aurora PostgreSQL version 11\.8 and higher
  +  Aurora PostgreSQL version 10\.13 and higher
+  In Aurora versions lower than those in the preceding list, the cluster volume can reuse space that was freed up when you deleted data, but the volume itself never decreases in size\. 
+  This feature is being deployed in phases to the AWS Regions where Aurora is available\. Depending on the Region where your cluster is, this feature might not be available yet\. 

 Dynamic resizing applies to operations that physically remove or resize data files within the cluster volume\. Thus, it applies to SQL statements such as `DROP TABLE`, `DROP DATABASE`, `TRUNCATE TABLE`, and `ALTER TABLE ... DROP PARTITION`\. It doesn't apply to deleting rows using the `DELETE` statement\. If you delete a large number of rows from a table, you can run the Aurora MySQL `OPTIMIZE TABLE` statement or use the Aurora PostgreSQL `pg_repack` extension afterward to reorganize the table and dynamically resize the cluster volume\. 

**Note**  
For Aurora MySQL, the `innodb_file_per_table` parameter affects how table storage is organized\. When tables are part of the system tablespace, dropping the table doesn't reduce the size of the system tablespace\. Thus, make sure to set `innodb_file_per_table` to 1 for Aurora MySQL DB clusters to take full advantage of dynamic resizing\.  
The data resizing feature reclaims space from deleted tablespaces over time, not immediately, when tables in those tablespaces are dropped\. Space in the system tablespace isn't reclaimed because the system tablespace is never deleted\. Unreclaimed free space in a tablespace is reused when an operation needs space in that tablespace\.

 These Aurora versions also have a higher storage limit for the cluster volume than lower versions do\. Thus, you can consider upgrading to one of these versions if you are close to exceeding the original 64 TiB volume size\. 

 You can check how much storage space a cluster is using by monitoring the `VolumeBytesUsed` metric in CloudWatch\. 
+  In the AWS Management Console, you can see this figure in a chart by viewing the `Monitoring` tab on the details page for the cluster\. 
+  With the AWS CLI, you can run a command similar to the following Linux example\. Substitute your own values for the start and end times and the name of the cluster\. 

  ```
  aws cloudwatch get-metric-statistics --metric-name "VolumeBytesUsed" \
    --start-time "$(date -d '6 hours ago')" --end-time "$(date -d 'now')" --period 60 \
    --namespace "AWS/RDS" \
    --statistics Average Maximum Minimum \
    --dimensions Name=DBClusterIdentifier,Value=my_cluster_identifier
  ```

   That command produces output similar to the following\. 

  ```
  {
      "Label": "VolumeBytesUsed",
      "Datapoints": [
          {
              "Timestamp": "2020-08-04T21:25:00+00:00",
              "Average": 182871982080.0,
              "Minimum": 182871982080.0,
              "Maximum": 182871982080.0,
              "Unit": "Bytes"
          }
      ]
  }
  ```

 The following examples show how you might track storage usage for an Aurora cluster over time using AWS CLI commands on a Linux system\. The `--start-time` and `--end-time` parameters define the overall time interval as one day\. The `--period` parameter requests the measurements at one hour intervals\. It doesn't make sense to choose a `--period` value that's small, because the metrics are collected at intervals, not continuously\. Also, Aurora storage operations sometimes continue for some time in the background after the relevant SQL statement finishes\. 

 The first example returns output in the default JSON format\. The data points are returned in arbitrary order, not sorted by timestamp\. You might import this JSON data into a charting tool to do sorting and visualization\. 

```
$ aws cloudwatch get-metric-statistics --metric-name "VolumeBytesUsed" \
  --start-time "$(date -d '1 day ago')" --end-time "$(date -d 'now')" --period 3600
  --namespace "AWS/RDS" --statistics Maximum --dimensions Name=DBClusterIdentifier,Value=my_cluster_id
{
    "Label": "VolumeBytesUsed",
    "Datapoints": [
        {
            "Timestamp": "2020-08-04T19:40:00+00:00",
            "Maximum": 182872522752.0,
            "Unit": "Bytes"
        },
        {
            "Timestamp": "2020-08-05T00:40:00+00:00",
            "Maximum": 198573719552.0,
            "Unit": "Bytes"
        },
        {
            "Timestamp": "2020-08-05T05:40:00+00:00",
            "Maximum": 206827454464.0,
            "Unit": "Bytes"
        },
        {
            "Timestamp": "2020-08-04T17:40:00+00:00",
            "Maximum": 182872522752.0,
            "Unit": "Bytes"
        },
... output omitted ...
```

 This example returns the same data as the previous one\. The `--output` parameter represents the data in compact plain text format\. The `aws cloudwatch ` command pipes its output to the `sort` command\. The `-k` parameter of the `sort` command sorts the output by the third field, which is the timestamp in Universal Coordinated Time \(UTC\) format\. 

```
$ aws cloudwatch get-metric-statistics --metric-name "VolumeBytesUsed" \
  --start-time "$(date -d '1 day ago')" --end-time "$(date -d 'now')" --period 3600 \
  --namespace "AWS/RDS" --statistics Maximum --dimensions Name=DBClusterIdentifier,Value=my_cluster_id \
  --output text | sort -k 3
VolumeBytesUsed
DATAPOINTS  182872522752.0  2020-08-04T17:41:00+00:00 Bytes
DATAPOINTS  182872522752.0  2020-08-04T18:41:00+00:00 Bytes
DATAPOINTS  182872522752.0  2020-08-04T19:41:00+00:00 Bytes
DATAPOINTS  182872522752.0  2020-08-04T20:41:00+00:00 Bytes
DATAPOINTS  187667791872.0  2020-08-04T21:41:00+00:00 Bytes
DATAPOINTS  190981029888.0  2020-08-04T22:41:00+00:00 Bytes
DATAPOINTS  195587244032.0  2020-08-04T23:41:00+00:00 Bytes
DATAPOINTS  201048915968.0  2020-08-05T00:41:00+00:00 Bytes
DATAPOINTS  205368492032.0  2020-08-05T01:41:00+00:00 Bytes
DATAPOINTS  206827454464.0  2020-08-05T02:41:00+00:00 Bytes
DATAPOINTS  206827454464.0  2020-08-05T03:41:00+00:00 Bytes
DATAPOINTS  206827454464.0  2020-08-05T04:41:00+00:00 Bytes
DATAPOINTS  206827454464.0  2020-08-05T05:41:00+00:00 Bytes
DATAPOINTS  206827454464.0  2020-08-05T06:41:00+00:00 Bytes
DATAPOINTS  206827454464.0  2020-08-05T07:41:00+00:00 Bytes
DATAPOINTS  206827454464.0  2020-08-05T08:41:00+00:00 Bytes
DATAPOINTS  206827454464.0  2020-08-05T09:41:00+00:00 Bytes
DATAPOINTS  206827454464.0  2020-08-05T10:41:00+00:00 Bytes
DATAPOINTS  206827454464.0  2020-08-05T11:41:00+00:00 Bytes
DATAPOINTS  206827454464.0  2020-08-05T12:41:00+00:00 Bytes
DATAPOINTS  206827454464.0  2020-08-05T13:41:00+00:00 Bytes
DATAPOINTS  206827454464.0  2020-08-05T14:41:00+00:00 Bytes
DATAPOINTS  206833664000.0  2020-08-05T15:41:00+00:00 Bytes
DATAPOINTS  206833664000.0  2020-08-05T16:41:00+00:00 Bytes
```

 The sorted output shows how much storage was used at the start and end of the monitoring period\. You can also find the points during that period when Aurora allocated more storage for the cluster\. The following example uses Linux commands to reformat the starting and ending `VolumeBytesUsed` values as gigabytes \(GB\) and as gibibytes \(GiB\)\. Gigabytes represent units measured in powers of 10 and are commonly used in discussions of storage for rotational hard drives\. Gibibytes represent units measured in powers of 2\. Aurora storage measurements and limits are typically stated in the power\-of\-2 units, such as gibibytes and tebibytes\. 

```
$ GiB=$((1024*1024*1024))
$ GB=$((1000*1000*1000))
$ echo "Start: $((182872522752/$GiB)) GiB, End: $((206833664000/$GiB)) GiB"
Start: 170 GiB, End: 192 GiB
$ echo "Start: $((182872522752/$GB)) GB, End: $((206833664000/$GB)) GB"
Start: 182 GB, End: 206 GB
```

 The `VolumeBytesUsed` metric tells you how much storage in the cluster is incurring charges\. Thus, it's best to minimize this number when practical\. However, this metric doesn't include some storage that Aurora uses internally in the cluster and doesn't charge for\. If your cluster is approaching the storage limit and might run out of space, it's more helpful to monitor the `AuroraVolumeBytesLeftTotal` metric and try to maximize that number\. The following example runs a similar calculation as the previous one, but for `AuroraVolumeBytesLeftTotal` instead of `VolumeBytesUsed`\. You can see that the free size for this cluster reflects the original 64 TiB limit, because the cluster is running Aurora MySQL version 1\.22\. 

```
$ aws cloudwatch get-metric-statistics --metric-name "AuroraVolumeBytesLeftTotal" \
  --start-time "$(date -d '1 hour ago')" --end-time "$(date -d 'now')" --period 3600 \
  --namespace "AWS/RDS" --statistics Maximum --dimensions Name=DBClusterIdentifier,Value=my_old_cluster_id \
  --output text | sort -k 3
AuroraVolumeBytesLeftTotal
DATAPOINTS      69797193744384.0        2020-08-05T17:52:00+00:00       Count
DATAPOINTS      69797193744384.0        2020-08-05T18:52:00+00:00       Count
$ TiB=$((1024*1024*1024*1024))
$ TB=$((1000*1000*1000*1000))
$ echo "$((69797067915264 / $TB)) TB remaining for this cluster"
69 TB remaining for this cluster
$ echo "$((69797067915264 / $TiB)) TiB remaining for this cluster"
63 TiB remaining for this cluster
```

 For a cluster running Aurora MySQL version 1\.23 or 2\.09 and higher, or Aurora PostgreSQL 3\.3\.0 or 2\.6\.0 and higher, the free size reported by `VolumeBytesUsed` increases when data is added and decreases when data is removed\. The following example shows how\. This report shows the maximum and minimum storage size for a cluster at 15\-minute intervals as tables with temporary data are created and dropped\. The report lists the maximum value before the minimum value\. Thus, to understand how storage usage changed within the 15\-minute interval, interpret the numbers from right to left\. 

```
$ aws cloudwatch get-metric-statistics --metric-name "VolumeBytesUsed" \
  --start-time "$(date -d '4 hours ago')" --end-time "$(date -d 'now')" --period 1800 \
  --namespace "AWS/RDS" --statistics Maximum Minimum --dimensions Name=DBClusterIdentifier,Value=my_new_cluster_id
  --output text | sort -k 4
VolumeBytesUsed
DATAPOINTS	14545305600.0	14545305600.0	2020-08-05T20:49:00+00:00	Bytes
DATAPOINTS	14545305600.0	14545305600.0	2020-08-05T21:19:00+00:00	Bytes
DATAPOINTS	22022176768.0 14545305600.0	2020-08-05T21:49:00+00:00	Bytes
DATAPOINTS	22022176768.0	22022176768.0	2020-08-05T22:19:00+00:00	Bytes
DATAPOINTS	22022176768.0	22022176768.0	2020-08-05T22:49:00+00:00	Bytes
DATAPOINTS	22022176768.0 15614263296.0	2020-08-05T23:19:00+00:00	Bytes
DATAPOINTS	15614263296.0	15614263296.0	2020-08-05T23:49:00+00:00	Bytes
DATAPOINTS	15614263296.0	15614263296.0	2020-08-06T00:19:00+00:00	Bytes
```

 The following example shows how with a cluster running Aurora MySQL version 1\.23 or 2\.09 and higher, or Aurora PostgreSQL 3\.3\.0 or 2\.6\.0 and higher, the free size reported by `AuroraVolumeBytesLeftTotal` reflects the higher 128 TiB size limit\. 

```
$ aws cloudwatch get-metric-statistics --region us-east-1 --metric-name "AuroraVolumeBytesLeftTotal" \
  --start-time "$(date -d '4 hours ago')" --end-time "$(date -d 'now')" --period 1800 \
  --namespace "AWS/RDS" --statistics Minimum --dimensions Name=DBClusterIdentifier,Value=pq-57 \
  --output text | sort -k 3
AuroraVolumeBytesLeftTotal
DATAPOINTS	140515818864640.0	2020-08-05T20:56:00+00:00	Count
DATAPOINTS	140515818864640.0	2020-08-05T21:26:00+00:00	Count
DATAPOINTS	140515818864640.0	2020-08-05T21:56:00+00:00	Count
DATAPOINTS	140514866757632.0	2020-08-05T22:26:00+00:00	Count
DATAPOINTS	140511020580864.0	2020-08-05T22:56:00+00:00	Count
DATAPOINTS	140503168843776.0	2020-08-05T23:26:00+00:00	Count
DATAPOINTS	140503168843776.0	2020-08-05T23:56:00+00:00	Count
DATAPOINTS	140515818864640.0	2020-08-06T00:26:00+00:00	Count
$ TiB=$((1024*1024*1024*1024))
$ TB=$((1000*1000*1000*1000))
$ echo "$((140515818864640 / $TB)) TB remaining for this cluster"
140 TB remaining for this cluster
$ echo "$((140515818864640 / $TiB)) TiB remaining for this cluster"
127 TiB remaining for this cluster
```

## Instance scaling<a name="Aurora.Managing.Performance.InstanceScaling"></a>

You can scale your Aurora DB cluster as needed by modifying the DB instance class for each DB instance in the DB cluster\. Aurora supports several DB instance classes optimized for Aurora, depending on database engine compatibility\.


| Database engine | Instance scaling | 
| --- | --- | 
|  Amazon Aurora MySQL  |  See [Scaling Aurora MySQL DB instances](AuroraMySQL.Managing.Performance.md#AuroraMySQL.Managing.Performance.InstanceScaling)  | 
|  Amazon Aurora PostgreSQL  |  See [Scaling Aurora PostgreSQL DB instances](AuroraPostgreSQL.Managing.md#AuroraPostgreSQL.Managing.Performance.InstanceScaling)  | 

## Read scaling<a name="Aurora.Managing.Performance.ReadScaling"></a>

You can achieve read scaling for your Aurora DB cluster by creating up to 15 Aurora Replicas in a DB cluster that uses single\-master replication\. Each Aurora Replica returns the same data from the cluster volume with minimal replica lag—usually considerably less than 100 milliseconds after the primary instance has written an update\. As your read traffic increases, you can create additional Aurora Replicas and connect to them directly to distribute the read load for your DB cluster\. Aurora Replicas don't have to be of the same DB instance class as the primary instance\.

For information about adding Aurora Replicas to a DB cluster, see [Adding Aurora Replicas to a DB cluster](aurora-replicas-adding.md)\.

## Managing connections<a name="Aurora.Managing.MaxConnections"></a>

The maximum number of connections allowed to an Aurora DB instance is determined by the `max_connections` parameter in the instance\-level parameter group for the DB instance\. The default value of that parameter varies depends on the DB instance class used for the DB instance and database engine compatibility\.


| Database engine | max\_connections default value | 
| --- | --- | 
|  Amazon Aurora MySQL  |  See [Maximum connections to an Aurora MySQL DB instance](AuroraMySQL.Managing.Performance.md#AuroraMySQL.Managing.MaxConnections)  | 
|  Amazon Aurora PostgreSQL  |  See [Maximum connections to an Aurora PostgreSQL DB instance](AuroraPostgreSQL.Managing.md#AuroraPostgreSQL.Managing.MaxConnections)  | 

**Tip**  
If your applications frequently open and close connections, or keep a large number of long\-lived connections open, we recommend that you use Amazon RDS Proxy\. RDS Proxy is a fully managed, highly available database proxy that uses connection pooling to share database connections securely and efficiently\. To learn more about RDS Proxy, see [Using Amazon RDS Proxy](rds-proxy.md)\.

## Managing query execution plans<a name="Aurora.Managing.Optimizing"></a>

If you use query plan management for Aurora PostgreSQL, you gain control over which plans the optimizer runs\. For more information, see [Managing query execution plans for Aurora PostgreSQL](AuroraPostgreSQL.Optimize.md)\.