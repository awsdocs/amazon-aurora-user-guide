# aurora\_ccm\_status<a name="aurora_ccm_status"></a>

Displays the status of cluster cache manager\. 

## Syntax<a name="aurora_ccm_status-syntax"></a>

 

```
aurora_ccm_status()
```

## Arguments<a name="aurora_ccm_status-arguments"></a>

None\.

## Return type<a name="aurora_ccm_status-return-type"></a>

SETOF record with following columns:
+ `buffers_sent_last_minute` – The number of buffers sent to the designated reader in the past minute\. 
+ `buffers_found_last_minute` – The number of frequently access buffers during the past minute\. 
+ `buffers_sent_last_scan` – The number of buffers sent to the designated reader during the last complete scan of the buffer cache\. 
+ `buffers_found_last_scan` – The number of frequently accessed buffers sent during the last complete scan of the buffer cache\. Buffers that are already cached on the designated reader aren't sent\. 
+ `buffers_sent_current_scan` – The number of buffers sent during the current scan\. 
+ `buffers_found_current_scan` – The number of frequently accessed buffers that were identified in the current scan\. 
+ `current_scan_progress` – The number of buffers visited so far during the current scan\.

## Usage notes<a name="aurora_ccm_status-usage-notes"></a>

You can use this function to check and monitor the cluster cache management \(CCM\) feature\. This function works only if CCM is active on your Aurora PostgreSQL DB cluster\. To use this function you connect to the Write DB instance on your Aurora PostgreSQL DB cluster\.

You turn on CCM for an Aurora PostgreSQL DB cluster by setting the `apg_ccm_enabled` to 1 in the cluster's custom DB cluster parameter group\. To learn how, see [Configuring cluster cache management](AuroraPostgreSQL.cluster-cache-mgmt.md#AuroraPostgreSQL.cluster-cache-mgmt.Configure)\. 

Cluster cache management is active on an Aurora PostgreSQL DB cluster when the cluster has an Aurora Reader instance configured as follows:
+ The Aurora Reader instance uses same DB instance class type and size as the cluster's Writer instance\. 
+ The Aurora Reader instance is configured as Tier\-0 for the cluster\. If the cluster has more than one Reader, this is its only Tier\-0 Reader\. 

Setting more than one Reader to Tier\-0 disables CCM\. When CCM is disabled, calling this function returns the following error message:

```
ERROR: Cluster Cache Manager is disabled
```

You can also the PostgreSQL pg\_buffercache extension to analyze the buffer cache\. For more information, see [pg\_buffercache](https://www.postgresql.org/docs/current/pgbuffercache.html) in the PostgreSQL documentation\. 

For more information, see [Introduction to Aurora PostgreSQL cluster cache management](http://aws.amazon.com/blogs/database/introduction-to-aurora-postgresql-cluster-cache-management/)\.

## Examples<a name="aurora_ccm_status-examples"></a>

The following example shows the results of calling the `aurora_ccm_status` function\. This first example shows CCM statistics\.

```
=> SELECT * FROM aurora_ccm_status();
 buffers_sent_last_minute | buffers_found_last_minute | buffers_sent_last_scan | buffers_found_last_scan | buffers_sent_current_scan | buffers_found_current_scan | current_scan_progress
--------------------------+---------------------------+------------------------+-------------------------+---------------------------+----------------------------+-----------------------
                  2242000 |                   2242003 |               17920442 |                17923410 |                  14098000 |                   14100964 |              15877443
```

For more complete detail, you can use expanded display, as shown following:

```
\x
Expanded display is on.
SELECT *  FROM aurora_ccm_status();
[ RECORD 1 ]-----------------------+---------
buffers_sent_last_minute           | 2242000
buffers_found_last_minute          | 2242003
buffers_sent_last_scan             | 17920442
buffers_found_last_scan            | 17923410
buffers_sent_current_scan          | 14098000
buffers_found_current_scan         | 14100964
current_scan_progress              | 15877443
```

This example shows how to check warm rate and warm percentage\.

```
=> SELECT buffers_sent_last_minute * 8/60 AS warm_rate_kbps,
100 * (1.0-buffers_sent_last_scan/buffers_found_last_scan) AS warm_percent 
FROM aurora_ccm_status ();
 warm_rate_kbps | warm_percent
----------------+--------------
 16523 |        100.0
```