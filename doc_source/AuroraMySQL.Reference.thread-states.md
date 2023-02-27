# Aurora MySQL thread states<a name="AuroraMySQL.Reference.thread-states"></a>

The following are some common thread states for Aurora MySQL\.

**checking permissions**  
The thread is checking whether the server has the required privileges to run the statement\.

**checking query cache for query**  
The server is checking whether the current query is present in the query cache\.

**cleaned up**  
This is the final state of a connection whose work is complete but which hasn't been closed by the client\. The best solution is to explicitly close the connection in code\. Or you can set a lower value for `wait_timeout` in your parameter group\.

**closing tables**  
The thread is flushing the changed table data to disk and closing the used tables\. If this isn't a fast operation, verify the network bandwidth consumption metrics against the instance class network bandwidth\. Also, check that the parameter values for `table_open_cache` and `table_definition_cache` parameter allow for enough tables to be simultaneously open so that the engine doesn't need to open and close tables frequently\. These parameters influence the memory consumption on the instance\.

**converting HEAP to MyISAM**  
The query is converting a temporary table from in\-memory to on\-disk\. This conversion is necessary because the temporary tables created by MySQL in the intermediate steps of query processing grew too big for memory\. Check the values of `tmp_table_size` and `max_heap_table_size`\. In later versions, this thread state name is `converting HEAP to ondisk`\.

**converting HEAP to ondisk**  
The thread is converting an internal temporary table from an in\-memory table to an on\-disk table\.

**copy to tmp table**  
The thread is processing an `ALTER TABLE` statement\. This state occurs after the table with the new structure has been created but before rows are copied into it\. For a thread in this state, you can use the Performance Schema to obtain information about the progress of the copy operation\.

**creating sort index**  
Aurora MySQL is performing a sort because it can't use an existing index to satisfy the `ORDER BY` or `GROUP BY` clause of a query\. For more information, see [creating sort index](ams-states.sort-index.md)\.

**creating table**  
The thread is creating a permanent or temporary table\.

**delayed commit ok done**  
An asynchronous commit in Aurora MySQL has received an acknowledgement and is complete\.

**delayed commit ok initiated**  
The Aurora MySQL thread has started the async commit process but is waiting for acknowledgement\. This is usually the genuine commit time of a transaction\.

**delayed send ok done**  
An Aurora MySQL worker thread that is tied to a connection can be freed while a response is sent to the client\. The thread can begin other work\. The state `delayed send ok` means that the asynchronous acknowledgement to the client completed\.

**delayed send ok initiated**  
An Aurora MySQL worker thread has sent a response asynchronously to a client and is now free to do work for other connections\. The transaction has started an async commit process that hasn't yet been acknowledged\.

**executing**  
The thread has begun running a statement\.

**freeing items**  
The thread has run a command\. Some freeing of items done during this state involves the query cache\. This state is usually followed by cleaning up\.

**init**  
This state occurs before the initialization of `ALTER TABLE`, `DELETE`, `INSERT`, `SELECT`, or `UPDATE` statements\. Actions in this state include flushing the binary log or InnoDB log, and some cleanup of the query cache\.

**master has sent all binlog to slave**  
The primary node has finished its part of the replication\. The thread is waiting for more queries to run so that it can write to the binary log \(binlog\)\.

**opening tables**  
The thread is trying to open a table\. This operation is fast unless an `ALTER TABLE` or a `LOCK TABLE` statement needs to finish, or it exceeds the value of `table_open_cache`\.

**optimizing**  
The server is performing initial optimizations for a query\.

**preparing**  
This state occurs during query optimization\.

**query end**  
This state occurs after processing a query but before the freeing items state\.

**removing duplicates**  
Aurora MySQL couldn't optimize a `DISTINCT` operation in the early stage of a query\. Aurora MySQL must remove all duplicated rows before sending the result to the client\.

**searching rows for update**  
The thread is finding all matching rows before updating them\. This stage is necessary if the `UPDATE` is changing the index that the engine uses to find the rows\.

**sending binlog event to slave**  
The thread read an event from the binary log and is sending it to the replica\.

**sending cached result to client**  
The server is taking the result of a query from the query cache and sending it to the client\.

**sending data**  
The thread is reading and processing rows for a `SELECT` statement but hasn't yet started sending data to the client\. The process is identifying which pages contain the results necessary to satisfy the query\. For more information, see [sending data](ams-states.sending-data.md)\.

**sending to client**  
The server is writing a packet to the client\. In earlier MySQL versions, this wait event was labeled `writing to net`\.

**starting**  
This is the first stage at the beginning of statement execution\.

**statistics**  
The server is calculating statistics to develop a query execution plan\. If a thread is in this state for a long time, the server is probably disk\-bound while performing other work\.

**storing result in query cache**  
The server is storing the result of a query in the query cache\.

**system lock**  
The thread has called `mysql_lock_tables`, but the thread state hasn't been updated since the call\. This general state occurs for many reasons\.

**update**  
The thread is preparing to start updating the table\. 

**updating**  
The thread is searching for rows and is updating them\.

**user lock**  
The thread issued a `GET_LOCK` call\. The thread either requested an advisory lock and is waiting for it, or is planning to request it\.

**waiting for more updates**  
The primary node has finished its part of the replication\. The thread is waiting for more queries to run so that it can write to the binary log \(binlog\)\.

**waiting for schema metadata lock**  
This is a wait for a metadata lock\.

**waiting for stored function metadata lock**  
This is a wait for a metadata lock\.

**waiting for stored procedure metadata lock**  
This is a wait for a metadata lock\.

**waiting for table flush**  
The thread is executing `FLUSH TABLES` and is waiting for all threads to close their tables\. Or the thread received notification that the underlying structure for a table changed, so it must reopen the table to get the new structure\. To reopen the table, the thread must wait until all other threads have closed the table\. This notification takes place if another thread has used one of the following statements on the table: `FLUSH TABLES`, `ALTER TABLE`, `RENAME TABLE`, `REPAIR TABLE`, `ANALYZE TABLE`, or `OPTIMIZE TABLE`\.

**waiting for table level lock**  
One session is holding a lock on a table while another session tries to acquire the same lock on the same table\.

**waiting for table metadata lock**  
Aurora MySQL uses metadata locking to manage concurrent access to database objects and to ensure data consistency\. In this wait event, one session is holding a metadata lock on a table while another session tries to acquire the same lock on the same table\. When the Performance Schema is enabled, this thread state is reported as the wait event `synch/cond/sql/MDL_context::COND_wait_status`\.

**writing to net**  
The server is writing a packet to the network\. In later MySQL versions, this wait event is labeled `Sending to client`\. 