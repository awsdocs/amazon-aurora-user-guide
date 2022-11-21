# Aurora PostgreSQL wait events<a name="AuroraPostgreSQL.Tuning.concepts.summary"></a>

The following table lists the wait events for Aurora PostgreSQL that most commonly indicate performance problems, and summarizes the most common causes and corrective actions\. The following wait events are a subset of the list in [Amazon Aurora PostgreSQL wait events](AuroraPostgreSQL.Reference.Waitevents.md)\.


| Wait event | Definition | 
| --- | --- | 
|  [Client:ClientRead](apg-waits.clientread.md)  |  This event occurs when Aurora PostgreSQL is waiting to receive data from the client\.  | 
|  [Client:ClientWrite](apg-waits.clientwrite.md)  |  This event occurs when Aurora PostgreSQL is waiting to write data to the client\.  | 
|  [CPU](apg-waits.cpu.md)  |  This event occurs when a thread is active in CPU or is waiting for CPU\.  | 
|  [IO:BufFileRead and IO:BufFileWrite](apg-waits.iobuffile.md)  |  These events occur when Aurora PostgreSQL creates temporary files\.  | 
|  [IO:DataFileRead](apg-waits.iodatafileread.md)  |  This event occurs when a connection waits on a backend process to read a required page from storage because the page isn't available in shared memory\.   | 
|  [IO:XactSync](apg-waits.xactsync.md)  |  This event occurs when the database is waiting for the Aurora storage subsystem to acknowledge the commit of a regular transaction, or the commit or rollback of a prepared transaction\.   | 
|  [ipc:damrecordtxack](apg-waits.ipcdamrecordtxac.md)  |  This event occurs when Aurora PostgreSQL in a session using database activity streams generates an activity stream event, then waits for that event to become durable\.  | 
|  [Lock:advisory](apg-waits.lockadvisory.md)  |  This event occurs when a PostgreSQL application uses a lock to coordinate activity across multiple sessions\.  | 
|  [Lock:extend](apg-waits.lockextend.md) |  This event occurs when a backend process is waiting to lock a relation to extend it while another process has a lock on that relation for the same purpose\.  | 
|  [Lock:Relation](apg-waits.lockrelation.md)  |  This event occurs when a query is waiting to acquire a lock on a table or view that's currently locked by another transaction\.  | 
|  [Lock:transactionid](apg-waits.locktransactionid.md)  | This event occurs when a transaction is waiting for a row\-level lock\. | 
|  [Lock:tuple](apg-waits.locktuple.md)  |  This event occurs when a backend process is waiting to acquire a lock on a tuple\.  | 
|  [LWLock:buffer\_content \(BufferContent\)](apg-waits.lockbuffercontent.md)  |  This event occurs when a session is waiting to read or write a data page in memory while another session has that page locked for writing\.  | 
|  [LWLock:buffer\_mapping](apg-waits.lwl-buffer-mapping.md)  |  This event occurs when a session is waiting to associate a data block with a buffer in the shared buffer pool\.  | 
|  [LWLock:BufferIO](apg-waits.lwlockbufferio.md)  |  This event occurs when Aurora PostgreSQL or RDS for PostgreSQL is waiting for other processes to finish their input/output \(I/O\) operations when concurrently trying to access a page\.  | 
|  [LWLock:lock\_manager](apg-waits.lw-lock-manager.md)  | This event occurs when the Aurora PostgreSQL engine maintains the shared lock's memory area to allocate, check, and deallocate a lock when a fast path lock isn't possible\. | 
| [LWLock:MultiXact](apg-waits.lwlockmultixact.md)  | This type of event occurs when Aurora PostgreSQL is keeping a session open to complete multiple transactions that involve the same row in a table\. The wait event denotes which aspect of multiple transaction processing is generating the wait event, that is, LWLock:MultiXactOffsetSLRU, LWLock:MultiXactOffsetBuffer, LWLock:MultiXactMemberSLRU, or LWLock:MultiXactMemberBuffer\. | 
|  [Timeout:PgSleep](apg-waits.timeoutpgsleep.md)  |  This event occurs when a server process has called the `pg_sleep` function and is waiting for the sleep timeout to expire\.   | 