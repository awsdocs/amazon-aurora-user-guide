# Essential concepts for Aurora MySQL tuning<a name="AuroraMySQL.Managing.Tuning.concepts"></a>

Before you tune your Aurora MySQL database, make sure to learn what wait events and thread states are and why they occur\. Also review the basic memory and disk architecture of Aurora MySQL when using the InnoDB storage engine\. For a helpful architecture diagram, see the [MySQL Reference Manual](https://dev.mysql.com/doc/refman/8.0/en/images/innodb-architecture.png)\.

**Topics**
+ [Aurora MySQL wait events](#AuroraMySQL.Managing.Tuning.concepts.waits)
+ [Aurora MySQL thread states](#AuroraMySQL.Managing.Tuning.concepts.thread-states)
+ [Aurora MySQL memory](#AuroraMySQL.Managing.Tuning.concepts.memory)
+ [Aurora MySQL processes](#AuroraMySQL.Managing.Tuning.concepts.processes)

## Aurora MySQL wait events<a name="AuroraMySQL.Managing.Tuning.concepts.waits"></a>

A *wait event* indicates a resource for which a session is waiting\. For example, the wait event `io/socket/sql/client_connection` indicates that a thread is in the process of handling a new connection\. Typical resources that a session waits for include the following:
+ Single\-threaded access to a buffer, for example, when a session is attempting to modify a buffer
+ A row that is currently locked by another session
+ A data file read
+ A log file write

For example, to satisfy a query, the session might perform a full table scan\. If the data isn't already in memory, the session waits for the disk I/O to complete\. When the buffers are read into memory, the session might need to wait because other sessions are accessing the same buffers\. The database records the waits by using a predefined wait event\. These events are grouped into categories\.

A wait event doesn't by itself show a performance problem\. For example, if requested data isn't in memory, reading data from disk is necessary\. If one session locks a row for an update, another session waits for the row to be unlocked so that it can update it\. A commit requires waiting for the write to a log file to complete\. Waits are integral to the normal functioning of a database\. 

Large numbers of wait events typically show a performance problem\. In such cases, you can use wait event data to determine where sessions are spending time\. For example, if a report that typically runs in minutes now runs for hours, you can identify the wait events that contribute the most to total wait time\. If you can determine the causes of the top wait events, you can sometimes make changes that improve performance\. For example, if your session is waiting on a row that has been locked by another session, you can end the locking session\.  

## Aurora MySQL thread states<a name="AuroraMySQL.Managing.Tuning.concepts.thread-states"></a>

A *general thread state* is a `State` value that is associated with general query processing\. For example, the thread state `sending data` indicates that a thread is reading and filtering rows for a query to determine the correct result set\. 

You can use thread states to tune Aurora MySQL in a similar fashion to how you use wait events\. For example, frequent occurrences of `sending data` usually indicate that a query isn't using an index\. For more information about thread states, see [General Thread States](https://dev.mysql.com/doc/refman/5.7/en/general-thread-states.html) in the *MySQL Reference Manual*\.

When you use Performance Insights, one of the following conditions is true:
+ Performance Schema is turned on – Aurora MySQL shows wait events rather than the thread state\.
+ Performance Schema isn't turned on – Aurora MySQL shows the thread state\.

We recommend that you configure the Performance Schema for automatic management\. The Performance Schema provides additional insights and better tools to investigate potential performance problems\. For more information, see [Enabling the Performance Schema for Performance Insights on Aurora MySQL](USER_PerfInsights.EnableMySQL.md)\.

## Aurora MySQL memory<a name="AuroraMySQL.Managing.Tuning.concepts.memory"></a>

In Aurora MySQL, the most important memory areas are the buffer pool and log buffer\.

**Topics**
+ [Buffer pool](#AuroraMySQL.Managing.Tuning.concepts.memory.buffer-pool)
+ [Log buffer](#AuroraMySQL.Managing.Tuning.concepts.memory.log-buffer)

### Buffer pool<a name="AuroraMySQL.Managing.Tuning.concepts.memory.buffer-pool"></a>

The *buffer pool* is the shared memory area where Aurora MySQL caches table and index data\. Queries can access frequently used data directly from memory without reading from disk\.

The buffer pool is structured as a linked list of pages\. A *page* can hold multiple rows\. Aurora MySQL uses a least recently used \(LRU\) algorithm to age pages out of the pool\.

In memory, part of the buffer pool is occupied by the change buffer\. The *change buffer* caches changes to secondary index pages when they aren't in the buffer pool\. When subsequent read operations load the pages into the buffer pool, Aurora MySQL merges the buffered changes into the pages\.

For more information, see [Buffer Pool](https://dev.mysql.com/doc/refman/8.0/en/innodb-buffer-pool.html) and [Change Buffer](https://dev.mysql.com/doc/refman/8.0/en/innodb-change-buffer.html) in the *MySQL Reference Manual*\.

### Log buffer<a name="AuroraMySQL.Managing.Tuning.concepts.memory.log-buffer"></a>

The *log buffer* holds redo data that Aurora MySQL periodically writes to disk\. A large log buffer enables large transactions to run without writing to disk\. If transactions use data manipulation language \(DML\) to change large numbers of rows, increasing the size of the log buffer decreases disk I/O\.

## Aurora MySQL processes<a name="AuroraMySQL.Managing.Tuning.concepts.processes"></a>

Aurora MySQL uses a process model that is very different from Aurora PostgreSQL\.

**Topics**
+ [MySQL server \(mysqld\)](#AuroraMySQL.Managing.Tuning.concepts.processes.mysqld)
+ [Threads](#AuroraMySQL.Managing.Tuning.concepts.processes.threads)
+ [Thread pool](#AuroraMySQL.Managing.Tuning.concepts.processes.pool)

### MySQL server \(mysqld\)<a name="AuroraMySQL.Managing.Tuning.concepts.processes.mysqld"></a>

The MySQL server is a single operating\-system process named mysqld\. The MySQL server doesn't spawn additional processes\. Thus, an Aurora MySQL database uses mysqld to perform most of its work\.

When the MySQL server starts, it listens for network connections from MySQL clients\. When a client connects to the database, mysqld opens a thread\.

### Threads<a name="AuroraMySQL.Managing.Tuning.concepts.processes.threads"></a>

Connection manager threads associate each client connection with a dedicated thread\. This thread manages authentication, runs statements, and returns results to the client\. Connection manager creates new threads when necessary\.

The *thread cache* is the set of available threads\. When a connection ends, MySQL returns the thread to the thread cache if the cache isn't full\. The `thread_cache_size` system variable determines the thread cache size\.

### Thread pool<a name="AuroraMySQL.Managing.Tuning.concepts.processes.pool"></a>

The *thread pool* consists of a number of thread groups\. Each group manages a set of client connections\. When a client connects to the database, the thread pool assigns the connections to thread groups in round\-robin fashion\. The thread pool separates connections and threads\. There is no fixed relationship between connections and the threads that run statements received from those connections\.