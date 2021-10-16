# Essential concepts for Aurora PostgreSQL tuning<a name="AuroraPostgreSQL.Tuning.concepts"></a>

Before you tune your Aurora PostgreSQL database, make sure to learn what wait events are and why they occur\. Also review the basic memory and disk architecture of Aurora PostgreSQL\. For a helpful architecture diagram, see the [PostgreSQL](https://en.wikibooks.org/wiki/PostgreSQL/Architecture) wikibook\.

**Topics**
+ [Aurora PostgreSQL wait events](#AuroraPostgreSQL.Tuning.concepts.waits)
+ [Aurora PostgreSQL memory](#AuroraPostgreSQL.Tuning.concepts.memory)
+ [Aurora PostgreSQL processes](#AuroraPostgreSQL.Tuning.concepts.processes)

## Aurora PostgreSQL wait events<a name="AuroraPostgreSQL.Tuning.concepts.waits"></a>

A *wait event* indicates a resource for which a session is waiting\. For example, the wait event `io/socket/sql/client_connection` indicates that a thread is in the process of handling a new connection\. Typical resources that a session waits for include the following:
+ Single\-threaded access to a buffer, for example, when a session is attempting to modify a buffer
+ A row that is currently locked by another session
+ A data file read
+ A log file write

For example, to satisfy a query, the session might perform a full table scan\. If the data isn't already in memory, the session waits for the disk I/O to complete\. When the buffers are read into memory, the session might need to wait because other sessions are accessing the same buffers\. The database records the waits by using a predefined wait event\. These events are grouped into categories\.

A wait event doesn't by itself show a performance problem\. For example, if requested data isn't in memory, reading data from disk is necessary\. If one session locks a row for an update, another session waits for the row to be unlocked so that it can update it\. A commit requires waiting for the write to a log file to complete\. Waits are integral to the normal functioning of a database\. 

Large numbers of wait events typically show a performance problem\. In such cases, you can use wait event data to determine where sessions are spending time\. For example, if a report that typically runs in minutes now runs for hours, you can identify the wait events that contribute the most to total wait time\. If you can determine the causes of the top wait events, you can sometimes make changes that improve performance\. For example, if your session is waiting on a row that has been locked by another session, you can end the locking session\.  

## Aurora PostgreSQL memory<a name="AuroraPostgreSQL.Tuning.concepts.memory"></a>

Aurora PostgreSQL memory is divided into shared and local\.

**Topics**
+ [Shared memory in Aurora PostgreSQL](#AuroraPostgreSQL.Tuning.concepts.shared)
+ [Local memory in Aurora PostgreSQL](#AuroraPostgreSQL.Tuning.concepts.local)

### Shared memory in Aurora PostgreSQL<a name="AuroraPostgreSQL.Tuning.concepts.shared"></a>

Aurora PostgreSQL allocates shared memory when the instance starts\. Shared memory is divided into multiple subareas\. Following, you can find a description of the most important ones\.

**Topics**
+ [Shared buffers](#AuroraPostgreSQL.Tuning.concepts.buffer-pool)
+ [Write ahead log \(WAL\) buffers](#AuroraPostgreSQL.Tuning.concepts.WAL)

#### Shared buffers<a name="AuroraPostgreSQL.Tuning.concepts.buffer-pool"></a>

The *shared buffer pool* is an Aurora PostgreSQL memory area that holds all pages that are or were being used by application connections\. A *page* is the memory version of a disk block\. The shared buffer pool caches the data blocks read from disk\. The pool reduces the need to reread data from disk, making the database operate more efficiently\.

Every table and index is stored as an array of pages of a fixed size\. Each block contains multiple tuples, which correspond to rows\. A tuple can be stored in any page\.

The shared buffer pool has finite memory\. If a new request requires a page that isn't in memory, and no more memory exists, Aurora PostgreSQL evicts a less frequently used page to accommodate the request\. The eviction policy is implemented by a clock sweep algorithm\.

The `shared_buffers` parameter determines how much memory the server dedicates to caching data\.

#### Write ahead log \(WAL\) buffers<a name="AuroraPostgreSQL.Tuning.concepts.WAL"></a>

A *write\-ahead log \(WAL\) buffer* holds transaction data that Aurora PostgreSQL later writes to persistent storage\. Using the WAL mechanism, Aurora PostgreSQL can do the following:
+ Recover data after a failure
+ Reduce disk I/O by avoiding frequent writes to disk

When a client changes data, Aurora PostgreSQL writes the changes to the WAL buffer\. When the client issues a `COMMIT`, the WAL writer process writes transaction data to the WAL file\.

The `wal_level` parameter determines how much information is written to the WAL\.

### Local memory in Aurora PostgreSQL<a name="AuroraPostgreSQL.Tuning.concepts.local"></a>

Every backend process allocates local memory for query processing\.

**Topics**
+ [Work memory area](#AuroraPostgreSQL.Tuning.concepts.local.work_mem)
+ [Maintenance work memory area](#AuroraPostgreSQL.Tuning.concepts.local.maintenance_work_mem)
+ [Temporary buffer area](#AuroraPostgreSQL.Tuning.concepts.temp)

#### Work memory area<a name="AuroraPostgreSQL.Tuning.concepts.local.work_mem"></a>

The *work memory area* holds temporary data for queries that performs sorts and hashes\. For example, a query with an `ORDER BY` clause performs a sort\. Queries use hash tables in hash joins and aggregations\.

The `work_mem` parameter the amount of memory to be used by internal sort operations and hash tables before writing to temporary disk files\. The default value is 4 MB\. Multiple sessions can run simultaneously, and each session can run maintenance operations in parallel\. For this reason, the total work memory used can be multiples of the `work_mem` setting\. 

#### Maintenance work memory area<a name="AuroraPostgreSQL.Tuning.concepts.local.maintenance_work_mem"></a>

The *maintenance work memory area* caches data for maintenance operations\. These operations include vacuuming, creating an index, and adding foreign keys\.

The `maintenance_work_mem` parameter specifies the maximum amount of memory to be used by maintenance operations\. The default value is 64 MB\. A database session can only run one maintenance operation at a time\.

#### Temporary buffer area<a name="AuroraPostgreSQL.Tuning.concepts.temp"></a>

The *temporary buffer area* caches temporary tables for each database session\.

Each session allocates temporary buffers as needed up to the limit you specify\. When the session ends, the server clears the buffers\.

The `temp_buffers` parameter sets the maximum number of temporary buffers used by each session\. Before the first use of temporary tables within a session, you can change the `temp_buffers` value\.

## Aurora PostgreSQL processes<a name="AuroraPostgreSQL.Tuning.concepts.processes"></a>

Aurora PostgreSQL uses multiple processes\.

**Topics**
+ [Postmaster process](#AuroraPostgreSQL.Tuning.concepts.postmaster)
+ [Backend processes](#AuroraPostgreSQL.Tuning.concepts.backend)
+ [Background processes](#AuroraPostgreSQL.Tuning.concepts.vacuum)

### Postmaster process<a name="AuroraPostgreSQL.Tuning.concepts.postmaster"></a>

The *postmaster process* is the first process started when you start Aurora PostgreSQL\. The postmaster process has the following primary responsibilities:
+ Fork and monitor background processes
+ Receive authentication requests from client processes, and authenticate them before allowing the database to service requests

### Backend processes<a name="AuroraPostgreSQL.Tuning.concepts.backend"></a>

If the postmaster authenticates a client request, the postmaster forks a new backend process, also called a postgres process\. One client process connects to exactly one backend process\. The client process and the backend process communicate directly without intervention by the postmaster process\.

### Background processes<a name="AuroraPostgreSQL.Tuning.concepts.vacuum"></a>

The postmaster process forks several processes that perform different backend tasks\. Some of the more important include the following:
+ WAL writer

  Aurora PostgreSQL writes data in the WAL \(write ahead logging\) buffer to the log files\. The principle of write ahead logging is that the database can't write changes to the data files until after the database writes log records describing those changes to disk\. The WAL mechanism reduces disk I/O, and allows Aurora PostgreSQL to use the logs to recover the database after a failure\.
+ Background writer

  This process periodically write dirty \(modified\) pages from the memory buffers to the data files\. A page becomes dirty when a backend process modifies it in memory\.
+ Autovacuum daemon

  The daemon consists of the following:
  + The autovacuum launcher
  + The autovacuum worker processes

  When autovacuum is turned on, it checks for tables that have had a large number of inserted, updated, or deleted tuples\. The daemon has the following responsibilities:
  + Recover or reuse disk space occupied by updated or deleted rows
  + Update statistics used by the planner
  + Protect against loss of old data because of transaction ID wraparound

  The autovacuum feature automates the execution of `VACUUM` and `ANALYZE` commands\. `VACUUM` has the following variants: standard and full\. Standard vacuum runs in parallel with other database operations\. `VACUUM FULL` requires an exclusive lock on the table it is working on\. Thus, it can't run in parallel with operations that access the same table\. `VACUUM` creates a substantial amount of I/O traffic, which can cause poor performance for other active sessions\.