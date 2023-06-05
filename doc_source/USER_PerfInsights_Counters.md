# Performance Insights counter metrics<a name="USER_PerfInsights_Counters"></a>

Counter metrics are operating system and database performance metrics in the Performance Insights dashboard\. To help identify and analyze performance problems, you can correlate counter metrics with DB load\.

The counter metrics are collected one time each minute\. The OS metrics collection depends on whether Enhanced Monitoring is turned on or off\. If Enhanced Monitoring is turned off, the OS metrics are collected one time each minute\. If Enhanced Monitoring is turned on, the OS metrics are collected for the selected time period\. For more information about turning Enhanced Monitoring on or off, see [Turning Enhanced Monitoring on and off](USER_Monitoring.OS.Enabling.md#USER_Monitoring.OS.Enabling.Procedure)\.

**Topics**
+ [Performance Insights operating system counters](#USER_PerfInsights_Counters.OS)
+ [Performance Insights counters for Aurora MySQL](#USER_PerfInsights_Counters.Aurora_MySQL)
+ [Performance Insights counters for Aurora PostgreSQL](#USER_PerfInsights_Counters.Aurora_PostgreSQL)

## Performance Insights operating system counters<a name="USER_PerfInsights_Counters.OS"></a>

The following operating system counters, which are prefixed with `os`, are available with Performance Insights for Aurora PostgreSQL\. You can find definitions for these metrics in [Viewing OS metrics using CloudWatch Logs](USER_Monitoring.OS.CloudWatchLogs.md)\. 


| Counter | Type | Metric | 
| --- | --- | --- | 
| active | memory | os\.memory\.active | 
| buffers | memory | os\.memory\.buffers | 
| cached | memory | os\.memory\.cached | 
| dirty | memory | os\.memory\.dirty | 
| free | memory | os\.memory\.free | 
| hugePagesFree | memory | os\.memory\.hugePagesFree | 
| hugePagesRsvd | memory | os\.memory\.hugePagesRsvd | 
| hugePagesSize | memory | os\.memory\.hugePagesSize | 
| hugePagesSurp | memory | os\.memory\.hugePagesSurp | 
| hugePagesTotal | memory | os\.memory\.hugePagesTotal | 
| inactive | memory | os\.memory\.inactive | 
| mapped | memory | os\.memory\.mapped | 
| pageTables | memory | os\.memory\.pageTables | 
| slab | memory | os\.memory\.slab | 
| total | memory | os\.memory\.total | 
| writeback | memory | os\.memory\.writeback | 
| guest | cpuUtilization | os\.cpuUtilization\.guest | 
| idle | cpuUtilization | os\.cpuUtilization\.idle | 
| irq | cpuUtilization | os\.cpuUtilization\.irq | 
| nice | cpuUtilization | os\.cpuUtilization\.nice | 
| steal | cpuUtilization | os\.cpuUtilization\.steal | 
| system | cpuUtilization | os\.cpuUtilization\.system | 
| total | cpuUtilization | os\.cpuUtilization\.total | 
| user | cpuUtilization | os\.cpuUtilization\.user | 
| wait | cpuUtilization | os\.cpuUtilization\.wait | 
| avgQueueLen | diskIO | os\.diskIO\.avgQueueLen | 
| avgReqSz | diskIO | os\.diskIO\.avgReqSz | 
| await | diskIO | os\.diskIO\.await | 
| readIOsPS | diskIO | os\.diskIO\.readIOsPS | 
| readKb | diskIO | os\.diskIO\.readKb | 
| readKbPS | diskIO | os\.diskIO\.readKbPS | 
| rrqmPS | diskIO | os\.diskIO\.rrqmPS | 
| tps | diskIO | os\.diskIO\.tps | 
| util | diskIO | os\.diskIO\.util | 
| writeIOsPS | diskIO | os\.diskIO\.writeIOsPS | 
| writeKb | diskIO | os\.diskIO\.writeKb | 
| writeKbPS | diskIO | os\.diskIO\.writeKbPS | 
| wrqmPS | diskIO | os\.diskIO\.wrqmPS | 
| blocked | tasks | os\.tasks\.blocked | 
| running | tasks | os\.tasks\.running | 
| sleeping | tasks | os\.tasks\.sleeping | 
| stopped | tasks | os\.tasks\.stopped | 
| total | tasks | os\.tasks\.total | 
| zombie | tasks | os\.tasks\.zombie | 
| one | loadAverageMinute | os\.loadAverageMinute\.one | 
| fifteen | loadAverageMinute | os\.loadAverageMinute\.fifteen | 
| five | loadAverageMinute | os\.loadAverageMinute\.five | 
| cached | swap | os\.swap\.cached | 
| free | swap | os\.swap\.free | 
| in | swap | os\.swap\.in | 
| out | swap | os\.swap\.out | 
| total | swap | os\.swap\.total | 
| maxFiles | fileSys | os\.fileSys\.maxFiles | 
| usedFiles | fileSys | os\.fileSys\.usedFiles | 
| usedFilePercent | fileSys | os\.fileSys\.usedFilePercent | 
| usedPercent | fileSys | os\.fileSys\.usedPercent | 
| used | fileSys | os\.fileSys\.used | 
| total | fileSys | os\.fileSys\.total | 
| rx | network | os\.network\.rx | 
| tx | network | os\.network\.tx | 
| acuUtilization | general | os\.general\.acuUtilization | 
| maxConfiguredAcu | general | os\.general\.maxConfiguredAcu | 
| minConfiguredAcu | general | os\.general\.minConfiguredAcu | 
| numVCPUs | general | os\.general\.numVCPUs | 
| serverlessDatabaseCapacity | general | os\.general\.serverlessDatabaseCapacity | 

## Performance Insights counters for Aurora MySQL<a name="USER_PerfInsights_Counters.Aurora_MySQL"></a>

The following database counters are available with Performance Insights for Aurora MySQL\.

**Topics**
+ [Native counters for Aurora MySQL](#USER_PerfInsights_Counters.Aurora_MySQL.Native)
+ [Non\-native counters for Aurora MySQL](#USER_PerfInsights_Counters.Aurora_MySQL.NonNative)

### Native counters for Aurora MySQL<a name="USER_PerfInsights_Counters.Aurora_MySQL.Native"></a>

Native metrics are defined by the database engine and not by Amazon Aurora\. You can find definitions for these native metrics in [Server status variables](https://dev.mysql.com/doc/refman/8.0/en/server-status-variables.html) in the MySQL documentation\.


| Counter | Type | Unit | Metric | 
| --- | --- | --- | --- | 
| Com\_analyze | SQL | Queries per second | db\.SQL\.Com\_analyze | 
| Com\_optimize | SQL | Queries per second | db\.SQL\.Com\_optimize | 
| Com\_select | SQL | Queries per second | db\.SQL\.Com\_select | 
| Innodb\_rows\_deleted | SQL | Rows per second | db\.SQL\.Innodb\_rows\_deleted | 
| Innodb\_rows\_inserted | SQL | Rows per second | db\.SQL\.Innodb\_rows\_inserted | 
| Innodb\_rows\_read | SQL | Rows per second | db\.SQL\.Innodb\_rows\_read | 
| Innodb\_rows\_updated | SQL | Rows per second | db\.SQL\.Innodb\_rows\_updated | 
| Queries | SQL | Queries per second | db\.SQL\.Queries | 
| Questions | SQL | Queries per second | db\.SQL\.Questions | 
| Select\_full\_join | SQL | Queries per second | db\.SQL\.Select\_full\_join | 
| Select\_full\_range\_join | SQL | Queries per second | db\.SQL\.Select\_full\_range\_join | 
| Select\_range | SQL | Queries per second | db\.SQL\.Select\_range | 
| Select\_range\_check | SQL | Queries per second | db\.SQL\.Select\_range\_check | 
| Select\_scan | SQL | Queries per second | db\.SQL\.Select\_scan | 
| Slow\_queries | SQL | Queries per second | db\.SQL\.Slow\_queries | 
| Sort\_merge\_passes | SQL | Queries per second | db\.SQL\.Sort\_merge\_passes | 
| Sort\_range | SQL | Queries per second | db\.SQL\.Sort\_range | 
| Sort\_rows | SQL | Queries per second | db\.SQL\.Sort\_rows | 
| Sort\_scan | SQL | Queries per second | db\.SQL\.Sort\_scan | 
| Total\_query\_time | SQL | Milliseconds | db\.SQL\.Total\_query\_time | 
| Table\_locks\_immediate | Locks | Requests per second | db\.Locks\.Table\_locks\_immediate | 
| Table\_locks\_waited | Locks | Requests per second | db\.Locks\.Table\_locks\_waited | 
| Innodb\_row\_lock\_time | Locks | Milliseconds \(average\) | db\.Locks\.Innodb\_row\_lock\_time | 
| Aborted\_clients | Users | Connections | db\.Users\.Aborted\_clients | 
| Aborted\_connects | Users | Connections | db\.Users\.Aborted\_connects | 
| Connections | Users | Connections | db\.Users\.Connections | 
| External\_threads\_connected | Users | Connections | db\.Users\.External\_threads\_connected | 
| Threads\_connected | Users | Connections | db\.Users\.Threads\_connected | 
| Threads\_created | Users | Connections | db\.Users\.Threads\_created | 
| Threads\_running | Users | Connections | db\.Users\.Threads\_running | 
| Created\_tmp\_disk\_tables | Temp | Tables per second | db\.Temp\.Created\_tmp\_disk\_tables | 
| Created\_tmp\_tables | Temp | Tables per second | db\.Temp\.Created\_tmp\_tables | 
| Innodb\_buffer\_pool\_pages\_data | Cache | Pages | db\.Cache\.Innodb\_buffer\_pool\_pages\_data | 
| Innodb\_buffer\_pool\_pages\_total | Cache | Pages | db\.Cache\.Innodb\_buffer\_pool\_pages\_total | 
| Innodb\_buffer\_pool\_read\_requests | Cache | Pages per second | db\.Cache\.Innodb\_buffer\_pool\_read\_requests | 
| Innodb\_buffer\_pool\_reads | Cache | Pages per second | db\.Cache\.Innodb\_buffer\_pool\_reads | 
| Opened\_tables | Cache | Tables | db\.Cache\.Opened\_tables | 
| Opened\_table\_definitions | Cache | Tables | db\.Cache\.Opened\_table\_definitions | 
| Qcache\_hits | Cache | Queries | db\.Cache\.Qcache\_hits | 

### Non\-native counters for Aurora MySQL<a name="USER_PerfInsights_Counters.Aurora_MySQL.NonNative"></a>

Non\-native counter metrics are counters defined by Amazon RDS\. A non\-native metric can be a metric that you get with a specific query\. A non\-native metric also can be a derived metric, where two or more native counters are used in calculations for ratios, hit rates, or latencies\.


| Counter | Type | Metric | Description | Definition | 
| --- | --- | --- | --- | --- | 
| innodb\_buffer\_pool\_hits | Cache | db\.Cache\.innoDB\_buffer\_pool\_hits | The number of reads that InnoDB could satisfy from the buffer pool\. | innodb\_buffer\_pool\_read\_requests \- innodb\_buffer\_pool\_reads | 
| innodb\_buffer\_pool\_hit\_rate | Cache | db\.Cache\.innoDB\_buffer\_pool\_hit\_rate | The percentage of reads that InnoDB could satisfy from the buffer pool\. | 100 \* innodb\_buffer\_pool\_read\_requests / \(innodb\_buffer\_pool\_read\_requests \+ innodb\_buffer\_pool\_reads\) | 
| innodb\_buffer\_pool\_usage | Cache | db\.Cache\.innoDB\_buffer\_pool\_usage |  The percentage of the InnoDB buffer pool that contains data \(pages\)\.  When using compressed tables, this value can vary\. For more information, see the information about `Innodb_buffer_pool_pages_data` and `Innodb_buffer_pool_pages_total` in [Server status sariables](https://dev.mysql.com/doc/refman/8.0/en/server-status-variables.html) in the MySQL documentation\.   | Innodb\_buffer\_pool\_pages\_data / Innodb\_buffer\_pool\_pages\_total \* 100\.0 | 
| query\_cache\_hit\_rate | Cache | db\.Cache\.query\_cache\_hit\_rate | The hit ratio for the MySQL result set cache \(query cache\)\. | Qcache\_hits / \(QCache\_hits \+ Com\_select\) \* 100 | 
| innodb\_rows\_changed | SQL | db\.SQL\.innodb\_rows\_changed | The total InnoDB row operations\. | db\.SQL\.Innodb\_rows\_inserted \+ db\.SQL\.Innodb\_rows\_deleted \+ db\.SQL\.Innodb\_rows\_updated | 
| active\_transactions | Transactions | db\.Transactions\.active\_transactions | The total active transactions\. | SELECT COUNT\(1\) AS active\_transactions FROM INFORMATION\_SCHEMA\.INNODB\_TRX | 
| trx\_rseg\_history\_len | Transactions | db\.Transactions\.trx\_rseg\_history\_len | A list of the undo log pages for committed transactions that is maintained by the InnoDB transaction system to implement multi\-version concurrency control\. For more information about undo log records details, see [https://dev.mysql.com/doc/refman/8.0/en/innodb-multi-versioning.html](https://dev.mysql.com/doc/refman/8.0/en/innodb-multi-versioning.html) in the MySQL documentation\. | SELECT COUNT AS trx\_rseg\_history\_len FROM INFORMATION\_SCHEMA\.INNODB\_METRICS WHERE NAME='trx\_rseg\_history\_len'  | 
| innodb\_deadlocks | Locks | db\.Locks\.innodb\_deadlocks | The total number of deadlocks\. | SELECT COUNT AS innodb\_deadlocks FROM INFORMATION\_SCHEMA\.INNODB\_METRICS WHERE NAME='lock\_deadlocks' | 
| innodb\_lock\_timeouts | Locks | db\.Locks\.innodb\_lock\_timeouts | The total number of deadlocks that timed out\. | SELECT COUNT AS innodb\_lock\_timeouts FROM INFORMATION\_SCHEMA\.INNODB\_METRICS WHERE NAME='lock\_timeouts' | 
| innodb\_row\_lock\_waits | Locks | db\.Locks\.innodb\_row\_lock\_waits | The total number of row locks that resulted in a wait\. | SELECT COUNT AS innodb\_row\_lock\_waits FROM INFORMATION\_SCHEMA\.INNODB\_METRICS WHERE NAME='lock\_row\_lock\_waits' | 

## Performance Insights counters for Aurora PostgreSQL<a name="USER_PerfInsights_Counters.Aurora_PostgreSQL"></a>

The following database counters are available with Performance Insights for Aurora PostgreSQL\.

**Topics**
+ [Native Counters for Aurora PostgreSQL](#USER_PerfInsights_Counters.Aurora_PostgreSQL.Native)
+ [Non\-native counters for Aurora PostgreSQL](#USER_PerfInsights_Counters.Aurora_PostgreSQL.NonNative)

### Native Counters for Aurora PostgreSQL<a name="USER_PerfInsights_Counters.Aurora_PostgreSQL.Native"></a>

Native metrics are defined by the database engine and not by Amazon Aurora\. You can find definitions for these native metrics in [Viewing Statistics](https://www.postgresql.org/docs/10/monitoring-stats.html#MONITORING-STATS-VIEWS) in the PostgreSQL documentation\.


| Counter | Type | Unit | Metric | 
| --- | --- | --- | --- | 
| queries\_started | SQL | Queries per second | db\.SQL\.queries | 
| total\_query\_time | SQL | Milliseconds | db\.SQL\.total\_query\_time | 
| tup\_deleted | SQL | Tuples per second | db\.SQL\.tup\_deleted | 
| tup\_fetched | SQL | Tuples per second | db\.SQL\.tup\_fetched | 
| tup\_inserted | SQL | Tuples per second | db\.SQL\.tup\_inserted | 
| tup\_returned | SQL | Tuples per second | db\.SQL\.tup\_returned | 
| tup\_updated | SQL | Tuples per second | db\.SQL\.tup\_updated | 
| blks\_hit | Cache | Blocks per second | db\.Cache\.blks\_hit | 
| buffers\_alloc | Cache | Blocks per second | db\.Cache\.buffers\_alloc | 
| buffers\_checkpoint | Checkpoint | Blocks per second | db\.Checkpoint\.buffers\_checkpoint | 
| checkpoints\_req | Checkpoint | Checkpoints per minute | db\.Checkpoint\.checkpoints\_req | 
| checkpoint\_sync\_time | Checkpoint | Milliseconds per checkpoint | db\.Checkpoint\.checkpoint\_sync\_time | 
| checkpoints\_timed | Checkpoint | Checkpoints per minute | db\.Checkpoint\.checkpoints\_timed | 
| checkpoint\_write\_time | Checkpoint | Milliseconds per checkpoint | db\.Checkpoint\.checkpoint\_write\_time | 
| maxwritten\_clean | Checkpoint | Bgwriter clean stops per minute | db\.Checkpoint\.maxwritten\_clean | 
| deadlocks | Concurrency | Deadlocks per minute | db\.Concurrency\.deadlocks | 
| blk\_read\_time | I/O | Milliseconds | db\.IO\.blk\_read\_time | 
| blks\_read | I/O | Blocks per second | db\.IO\.blks\_read | 
| buffers\_backend | I/O | Blocks per second | db\.IO\.buffers\_backend | 
| buffers\_backend\_fsync | I/O | Blocks per second | db\.IO\.buffers\_backend\_fsync | 
| buffers\_clean | I/O | Blocks per second | db\.IO\.buffers\_clean | 
| idle\_in\_transaction\_aborted\_count | State | Sessions | db\.State\.idle\_in\_transaction\_aborted\_count | 
| idle\_in\_transaction\_count | State | Sessions | db\.State\.idle\_in\_transaction\_count | 
| idle\_in\_transaction\_max\_time | State | Seconds | db\.State\.idle\_in\_transaction\_max\_time | 
| temp\_bytes | Temp | Bytes per second | db\.Temp\.temp\_bytes | 
| temp\_files | Temp | Files per minute | db\.Temp\.temp\_files | 
| active\_transactions | Transactions | Transactions | db\.Transactions\.active\_transactions | 
| blocked\_transactions | Transactions | Transactions | db\.Transactions\.blocked\_transactions | 
| duration\_commits | Transactions | Milliseconds | db\.Transactions\.duration\_commits | 
| max\_used\_xact\_ids | Transactions | Transactions | db\.Transactions\.max\_used\_xact\_ids | 
| xact\_commit | Transactions | Commits per second | db\.Transactions\.xact\_commit | 
| xaxt\_rollback | Transactions | Rollbacks per second | db\.Transactions\.xact\_rollback | 
| numbackends | User | Connections | db\.User\.numbackends | 
| total\_auth\_attempts | User | Connections per minute | db\.User\.total\_auth\_attempts | 
| archived\_count | WAL | Files per minute | db\.WAL\.archived\_count | 
| archive\_failed\_count | WAL | Files per minute | db\.WAL\.archive\_failed\_count | 

### Non\-native counters for Aurora PostgreSQL<a name="USER_PerfInsights_Counters.Aurora_PostgreSQL.NonNative"></a>

Non\-native counter metrics are counters defined by Amazon Aurora\. A non\-native metric can be a metric that you get with a specific query\. A non\-native metric also can be a derived metric, where two or more native counters are used in calculations for ratios, hit rates, or latencies\.


| Counter | Type | Metric | Description | Definition | 
| --- | --- | --- | --- | --- | 
| logical\_reads | SQL | db\.SQL\.logical\_reads | The total number of blocks hit and read\. | blks\_hit \+ blks\_read | 
| checkpoint\_sync\_latency | Checkpoint | db\.Checkpoint\.checkpoint\_sync\_latency | The total amount of time that has been spent in the portion of checkpoint processing where files are synchronized to disk\. | checkpoint\_sync\_time / \(checkpoints\_timed \+ checkpoints\_req\) | 
| checkpoint\_write\_latency | Checkpoint | db\.Checkpoint\.checkpoint\_write\_latency | The total amount of time that has been spent in the portion of checkpoint processing where files are written to disk\. | checkpoint\_write\_time / \(checkpoints\_timed \+ checkpoints\_req\) | 
| read\_latency | I/O | db\.IO\.read\_latency | The time spent reading data file blocks by backends in this instance\. | blk\_read\_time / blks\_read | 
| commit\_latency | Transactions | db\.Transactions\.commit\_latency | The average duration of commit operations\. | db\.Transactions\.duration\_commits / db\.Transactions\.xact\_commit | 