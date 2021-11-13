# Amazon Aurora PostgreSQL wait events<a name="AuroraPostgreSQL.Reference.Waitevents"></a>

The following are common wait events for Aurora PostgreSQL\.

**BufferPin:BufferPin**  
In this wait event, a session is waiting to access a data buffer during a period when no other session can examine that buffer\. Buffer pin waits can be protracted if another process holds an open cursor which last read data from the buffer in question\.

**Client:ClientRead**  
In this wait event, a session is receiving data from an application client\. This wait might be prevalent during bulk data loads using the COPY statement, or for applications that pass data to Aurora using many round trips between the client and the database\. A high number of client read waits per transaction may indicate excessive round trips, such as parameter passing\. You should compare this against the expected number of statements per transaction\. To learn how to tune your database when this wait is prominent, see [Client:ClientRead](apg-waits.clientread.md)\.

**IO:DataFilePrefetch**  
In this wait event, a session is waiting for an asynchronous prefetch from Aurora Storage\. 

**IO:DataFileRead**  
In this wait event, a session is reading data from Aurora Storage\. This may be a typical wait event for I/O intensive workloads\. SQL statements showing a comparatively large proportion of this wait event compared to other SQL statements may be using an inefficient query plan that requires reading large amounts of data\.

**IO:XactSync**  
In this wait event, a session is issuing a COMMIT or ROLLBACK, requiring the current transaction's changes to be persisted\. Aurora is waiting for Aurora storage to acknowledge persistence\.   
This wait most often arises when there is a very high rate of commit activity on the system\. You can sometimes alleviate this wait by modifying applications to commit transactions in batches\. You might see this wait at the same time as CPU waits in a case where the DB load exceeds the number of virtual CPUs \(vCPUs\) for the DB instance\. In this case, the storage persistence might be competing for CPU with CPU\-intensive database workloads\. To alleviate this scenario, you can try reducing those workloads, or scaling up to a DB instance with more vCPUs\. 

**Lock:transactionid**  
In this wait event, a session is trying to modify data that has been modified by another session, and is waiting for the other session's transaction to be committed or rolled back\. You can investigate blocking and waiting sessions in the pg\_locks view\. 

**LWLock:buffer\_content**  
In this wait event, a session is waiting to read or write a data page in memory while another session has that page locked for writing\. Heavy write contention for a single page \(hot page\), due to frequent updates of the same piece of data by many sessions, could lead to prevalence of this wait event\. Excessive use of foreign key constraints could increase lock duration, leading to increased contention\. You should investigate workloads experiencing high `buffer_content` waits for usage of foreign key constraints to determine if the constraints are necessary\. Alternatively, decreasing the fillfactor on the parent table will spread the keys across more of the block and can reduce contention on the page\. To learn how to tune your database when this wait is prominent, see [lwlock:buffer\_content \(BufferContent\)](apg-waits.lockbuffercontent.md)\.

**LWLock:MultiXactOffsetSLRU, MultiXactOffsetBuffer, MultiXactMemberSLRU, MultiXactMemberBuffer**  
In these wait events, a session is retrieving the list of all transactions that have an interest in a single row in a table\. When only one transaction has an interest in the row, that transaction's ID is stored directly in the row\. Normally, only the transaction that inserted the row is relevant\. However, in some cases, more transactions have an interest in the row\. In these cases, the database uses a secondary data structure called a *multixact* \(or *multitransaction*\) to store the list of transaction IDs\.  
Three common causes of multixact use are the following:  
+ Row locks combined with explicit savepoints, for example `SELECT FOR UPDATE`, then `SAVEPOINT`, then `UPDATE`
+ Row locks combined with PL/pgSQL `EXCEPTION` clauses, which use savepoints internally
+ Foreign keys, for example when multiple transactions acquire a share lock on the parent row
Some drivers, object\-relational mappings \(ORMs\), and abstraction layers have configuration options to automatically wrap all operations with savepoints\. This approach can lead to heavy multixact use under some workloads\. The pgjdbc `autosave` option and the psqlodbc `protocol` option are examples of this\.
If multixacts are in use for a particular row, then whenever the database needs to process that row, it must go to the secondary data structure for interested transaction IDs\. With enough multixact use, lookups in those data structures begin to experience memory cache misses\. They then require reading the data structure from storage\. When these lookups require storage I/O, SQL queries can take longer\. Then these wait events can become prominent\. Multixacts are eventually removed by the vacuum process\. As a result, these wait events can also become more pronounced when vacuum isn't aggressive\. They can also become more pronounced when long\-running transactions with row locks cause vacuum to retain transaction information for a longer time\.  
To alleviate spikes on these wait events, reduce the use of multixacts and ensure that autovacuum is configured aggressively on the problem table\. Also, monitor and manage long\-running transactions aggressively\. During an incident, sometimes a `VACUUM (VERBOSE ON, DISABLE_PAGE_SKIPPING ON, INDEX_CLEANUP OFF, TRUNCATE OFF)` command on the problem table can provide temporary relief immediately\.

**LWLock:SubtransControlLock**  
In this wait event, a session is looking up or manipulating the parent/child relationship between a transaction and a subtransaction\. The two most common causes of subtransaction use are savepoints and PL/pgSQL exception blocks\. The wait event might occur if there is heavy concurrent querying of data that's simultaneously being updated from within subtransactions\. You should investigate whether it is possible to reduce the use of savepoints and exception blocks, or to decrease concurrent queries on the rows being updated\.

For a complete list of PostgreSQL wait events, see [PostgreSQL wait\-event table](https://www.postgresql.org/docs/10/static/monitoring-stats.html#WAIT-EVENT-TABLE)\.