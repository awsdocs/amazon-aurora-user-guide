# Aurora MySQL wait events<a name="AuroraMySQL.Reference.Waitevents"></a>

The following are some common wait events for Aurora MySQL\.

**Note**  
For information on tuning Aurora MySQL performance using wait events, see [Tuning Aurora MySQL with wait events](AuroraMySQL.Managing.Tuning.wait-events.md)\.  
For information about the naming conventions used in MySQL wait events, see [ Performance Schema instrument naming conventions](https://dev.mysql.com/doc/refman/8.0/en/performance-schema-instrument-naming.html) in the MySQL documentation\.

**cpu**  
The number of active connections that are ready to run is consistently higher than the number of vCPUs\. For more information, see [cpu](ams-waits.cpu.md)\.

**io/aurora\_redo\_log\_flush**  
A session is persisting data to Aurora storage\. Typically, this wait event is for a write I/O operation in Aurora MySQL\. For more information, see [io/aurora\_redo\_log\_flush](ams-waits.io-auredologflush.md)\.

**io/aurora\_respond\_to\_client**  
Query processing has completed and results are being returned to the application client for the following Aurora MySQL versions: 2\.10\.2 and higher 2\.10 versions, 2\.09\.3 and higher 2\.09 versions, and 2\.07\.7 and higher 2\.07 versions\. Compare the network bandwidth of the DB instance class with the size of the result set being returned\. Also, check client\-side response times\. If the client is unresponsive and can't process the TCP packets, packet drops and TCP retransmissions can occur\. This situation negatively affects network bandwidth\. In versions lower than 2\.10\.2, 2\.09\.3, and 2\.07\.7, the wait event erroneously includes idle time\. To learn how to tune your database when this wait is prominent, see [io/aurora\_respond\_to\_client](ams-waits.respond-to-client.md)\.

**io/file/csv/data**  
Threads are writing to tables in comma\-separated value \(CSV\) format\. Check your CSV table usage\. A typical cause of this event is setting `log_output` on a table\.

**io/file/sql/binlog**  
A thread is waiting on a binary log \(binlog\) file that is being written to disk\.

**io/socket/sql/client\_connection**  
The `mysqld` program is busy creating threads to handle incoming new client connections\. For more information, see [io/socket/sql/client\_connection](ams-waits.client-connection.md)\.

**io/table/sql/handler**  
The engine is waiting for access to a table\. This event occurs regardless of whether the data is cached in the buffer pool or accessed on disk\. For more information, see [io/table/sql/handler](ams-waits.waitio.md)\.

**lock/table/sql/handler**  
This wait event is a table lock wait event handler\. For more information about atom and molecule events in the Performance Schema, see [ Performance Schema atom and molecule events](https://dev.mysql.com/doc/refman/8.0/en/performance-schema-atom-molecule-events.html) in the MySQL documentation\.

**synch/cond/innodb/row\_lock\_wait**  
Multiple data manipulation language \(DML\) statements are accessing the same database rows at the same time\. For more information, see [synch/cond/innodb/row\_lock\_wait](ams-waits.row-lock-wait.md)\.

**synch/cond/innodb/row\_lock\_wait\_cond**  
Multiple DML statements are accessing the same database rows at the same time\. For more information, see [synch/cond/innodb/row\_lock\_wait\_cond](ams-waits.row-lock-wait-cond.md)\.

**synch/cond/mysys/my\_thread\_var::suspend**  
The thread is suspended while waiting on a table\-level lock because another thread issued `LOCK TABLES ... READ`\.

**synch/cond/sql/MDL\_context::COND\_wait\_status**  
Threads are waiting on a table metadata lock\. The engine uses this type of lock to manage concurrent access to a database schema and to ensure data consistency\. For more information, see [Optimizing locking operations](https://dev.mysql.com/doc/refman/8.0/en/locking-issues.html) in the MySQL documentation\. To learn how to tune your database when this event is prominent, see [synch/cond/sql/MDL\_context::COND\_wait\_status](ams-waits.cond-wait-status.md)\.

**synch/cond/sql/MYSQL\_BIN\_LOG::COND\_done**  
You have turned on binary logging\. There might be a high commit throughput, large number transactions committing, or replicas reading binlogs\. Consider using multirow statements or bundling statements into one transaction\. In Aurora, use global databases instead of binary log replication, or use the `aurora_binlog_*` parameters\.

**synch/mutex/innodb/aurora\_lock\_thread\_slot\_futex**  
Multiple DML statements are accessing the same database rows at the same time\. For more information, see [synch/mutex/innodb/aurora\_lock\_thread\_slot\_futex](ams-waits.waitsynch.md)\.

**synch/mutex/innodb/buf\_pool\_mutex**  
The buffer pool isn't large enough to hold the working data set\. Or the workload accesses pages from a specific table, which leads to contention in the buffer pool\. For more information, see [synch/mutex/innodb/buf\_pool\_mutex](ams-waits.bufpoolmutex.md)\.

**synch/mutex/innodb/fil\_system\_mutex**  
The process is waiting for access to the tablespace memory cache\. For more information, see [synch/mutex/innodb/fil\_system\_mutex](ams-waits.innodb-fil-system-mutex.md)\.

**synch/mutex/innodb/trx\_sys\_mutex**  
Operations are checking, updating, deleting, or adding transaction IDs in InnoDB in a consistent or controlled manner\. These operations require a `trx_sys` mutex call, which is tracked by Performance Schema instrumentation\. Operations include management of the transaction system when the database starts or shuts down, rollbacks, undo cleanups, row read access, and buffer pool loads\. High database load with a large number of transactions results in the frequent appearance of this wait event\. For more information, see [synch/mutex/innodb/trx\_sys\_mutex](ams-waits.trxsysmutex.md)\.

**synch/mutex/mysys/KEY\_CACHE::cache\_lock**  <a name="key-cache.cache-lock"></a>
The `keycache->cache_lock` mutex controls access to the key cache for MyISAM tables\. While Aurora MySQL doesn't allow usage of MyISAM tables to store persistent data, they are used to store internal temporary tables\. Consider checking the `created_tmp_tables` or `created_tmp_disk_tables` status counters, because in certain situations, temporary tables are written to disk when they no longer fit in memory\.

**synch/mutex/sql/FILE\_AS\_TABLE::LOCK\_offsets**  
The engine acquires this mutex when opening or creating a table metadata file\. When this wait event occurs with excessive frequency, the number of tables being created or opened has spiked\. 

**synch/mutex/sql/FILE\_AS\_TABLE::LOCK\_shim\_lists**  
The engine acquires this mutex while performing operations such as `reset_size`, `detach_contents`, or `add_contents` on the internal structure that keeps track of opened tables\. The mutex synchronizes access to the list contents\. When this wait event occurs with high frequency, it indicates a sudden change in the set of tables that were previously accessed\. The engine needs to access new tables or let go of the context related to previously accessed tables\.

**synch/mutex/sql/LOCK\_open**  
The number of tables that your sessions are opening exceeds the size of the table definition cache or the table open cache\. Increase the size of these caches\. For more information, see [How MySQL opens and closes tables](https://dev.mysql.com/doc/refman/8.0/en/table-cache.html)\.

**synch/mutex/sql/LOCK\_table\_cache**  
The number of tables that your sessions are opening exceeds the size of the table definition cache or the table open cache\. Increase the size of these caches\. For more information, see [How MySQL opens and closes tables](https://dev.mysql.com/doc/refman/8.0/en/table-cache.html)\.

**synch/mutex/sql/LOG**  
In this wait event, there are threads waiting on a log lock\. For example, a thread might wait for a lock to write to the slow query log file\.

**synch/mutex/sql/MYSQL\_BIN\_LOG::LOCK\_commit**  
In this wait event, there is a thread that is waiting to acquire a lock with the intention of committing to the binary log\. Binary logging contention can occur on databases with a very high change rate\. Depending on your version of MySQL, there are certain locks being used to protect the consistency and durability of the binary log\. In RDS for MySQL, binary logs are used for replication and the automated backup process\. In Aurora MySQL, binary logs are not needed for native replication or backups\. They are disabled by default but can be enabled and used for external replication or change data capture\. For more information, see [The binary log](https://dev.mysql.com/doc/refman/8.0/en/binary-log.html) in the MySQL documentation\.

**sync/mutex/sql/MYSQL\_BIN\_LOG::LOCK\_dump\_thread\_metrics\_collection**  
If binary logging is turned on, the engine acquires this mutex when it prints active dump threads metrics to the engine error log and to the internal operations map\.

**sync/mutex/sql/MYSQL\_BIN\_LOG::LOCK\_inactive\_binlogs\_map**  
If binary logging is turned on, the engine acquires this mutex when it adds to, deletes from, or searches through the list of binlog files behind the latest one\.

**sync/mutex/sql/MYSQL\_BIN\_LOG::LOCK\_io\_cache**  
If binary logging is turned on, the engine acquires this mutex during Aurora binlog IO cache operations: allocate, resize, free, write, read, purge, and access cache info\. If this event occurs frequently, the engine is accessing the cache where binlog events are stored\. To reduce wait times, reduce commits\. Try grouping multiple statements into a single transaction\.

**synch/mutex/sql/MYSQL\_BIN\_LOG::LOCK\_log**  
You have turned on binary logging\. There might be high commit throughput, many transactions committing, or replicas reading binlogs\. Consider using multirow statements or bundling statements into one transaction\. In Aurora, use global databases instead of binary log replication or use the `aurora_binlog_*` parameters\.

**synch/mutex/sql/SERVER\_THREAD::LOCK\_sync**  
The mutex `SERVER_THREAD::LOCK_sync` is acquired during the scheduling, processing, or launching of threads for file writes\. The excessive occurrence of this wait event indicates increased write activity in the database\.

**synch/mutex/sql/TABLESPACES:lock**  
The engine acquires the `TABLESPACES:lock` mutex during the following tablespace operations: create, delete, truncate, and extend\. The excessive occurrence of this wait event indicates a high frequency of tablespace operations\. An example is loading a large amount of data into the database\.

**synch/rwlock/innodb/dict**  
In this wait event, there are threads waiting on an rwlock held on the InnoDB data dictionary\.

**synch/rwlock/innodb/dict\_operation\_lock**  
In this wait event, there are threads holding locks on InnoDB data dictionary operations\.

**synch/rwlock/innodb/dict sys RW lock**  
A high number of concurrent data control language statements \(DCLs\) in data definition language code \(DDLs\) are triggered at the same time\. Reduce the application's dependency on DDLs during regular application activity\.

**synch/rwlock/innodb/index\_tree\_rw\_lock**  
A large number of similar data manipulation language \(DML\) statements are accessing the same database object at the same time\. Try using multirow statements\. Also, spread the workload over different database objects\. For example, implement partitioning\.

**synch/sxlock/innodb/dict\_operation\_lock**  
A high number of concurrent data control language statements \(DCLs\) in data definition language code \(DDLs\) are triggered at the same time\. Reduce the application's dependency on DDLs during regular application activity\.

**synch/sxlock/innodb/dict\_sys\_lock**  
A high number of concurrent data control language statements \(DCLs\) in data definition language code \(DDLs\) are triggered at the same time\. Reduce the application's dependency on DDLs during regular application activity\.

**synch/sxlock/innodb/hash\_table\_locks**  
The session couldn't find pages in the buffer pool\. The engine either needs to read a file or modify the least\-recently used \(LRU\) list for the buffer pool\. Consider increasing the buffer cache size and improving access paths for the relevant queries\.

**synch/sxlock/innodb/index\_tree\_rw\_lock**  
Many similar data manipulation language \(DML\) statements are accessing the same database object at the same time\. Try using multirow statements\. Also, spread the workload over different database objects\. For example, implement partitioning\.

For more information on troubleshooting synch wait events, see [Why is my MySQL DB instance showing a high number of active sessions waiting on SYNCH wait events in Performance Insights?](https://aws.amazon.com/premiumsupport/knowledge-center/aurora-mysql-synch-wait-events/)\.