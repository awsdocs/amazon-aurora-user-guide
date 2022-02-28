# Tuning Aurora MySQL with wait events<a name="AuroraMySQL.Managing.Tuning.wait-events"></a>

The following table summarizes the Aurora MySQL wait events that most commonly indicate performance problems\. The following wait events are a subset of the list in [Aurora MySQL wait events](AuroraMySQL.Reference.md#AuroraMySQL.Reference.Waitevents)\.


| Wait event | Description | 
| --- | --- | 
|  [cpu](ams-waits.cpu.md)  |  This event occurs when a thread is active in CPU or is waiting for CPU\.  | 
|  [io/aurora\_redo\_log\_flush](ams-waits.io-auredologflush.md)  |  This event occurs when a session is writing persistent data to Aurora storage\.  | 
|  [io/aurora\_respond\_to\_client](ams-waits.resond-to-client.md)  |  This event occurs when a thread is waiting to return a result set to a client\.  | 
|  [io/file/innodb/innodb\_data\_file](ams-waits.innodb-data-file.md)  |  This event occurs when there are threads waiting on I/O operations from storage\.  | 
|  [io/socket/sql/client\_connection](ams-waits.client-connection.md)  |  This event occurs when a thread is in the process of handling a new connection\.  | 
|  [io/table/sql/handler](ams-waits.waitio.md)  |  This event occurs when work has been delegated to a storage engine\.   | 
|  [synch/cond/mysys/my\_thread\_var::suspend](ams-waits.suspend.md)  |  This event occurs when threads are suspended because they are waiting on a condition\.  | 
|  [synch/cond/sql/MDL\_context::COND\_wait\_status](ams-waits.cond-wait-status.md)  |  This event occurs when there are threads waiting on a table metadata lock\.  | 
|  [synch/mutex/innodb/aurora\_lock\_thread\_slot\_futex](ams-waits.waitsynch.md)  |  This event occurs when one session has locked a row for an update, and another session tries to update the same row\.  | 
|  [synch/mutex/innodb/buf\_pool\_mutex](ams-waits.bufpoolmutex.md)  |  This event occurs when a thread has acquired a lock on the InnoDB buffer pool to access a page in memory\.  | 
|  [synch/mutex/innodb/fil\_system\_mutex](ams-waits.innodb-fil-system-mutex.md)  |  This event occurs when when a session is waiting to access the tablespace memory cache\.  | 
|  [synch/mutex/innodb/trx\_sys\_mutex](ams-waits.trxsysmutex.md)  |  This event occurs when there is high database activity with a large number of transactions\.  | 
|  [synch/rwlock/innodb/hash\_table\_locks](ams-waits.rw-lock-hash-table-locks.md)  |  This event occurs when there is contention on modifying the hash table that maps the buffer cache\.  | 
|  [synch/sxlock/innodb/hash\_table\_locks](ams-waits.sx-lock-hash-table-locks.md)  |  This event occurs when pages not found in the buffer pool must be read from a file\.  | 