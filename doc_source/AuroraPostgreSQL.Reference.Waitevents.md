# Amazon Aurora PostgreSQL wait events<a name="AuroraPostgreSQL.Reference.Waitevents"></a>

The following are common wait events for Aurora PostgreSQL\. To learn more about wait events and tuning your Aurora PostgreSQL DB cluster, see [Tuning with wait events for Aurora PostgreSQL](AuroraPostgreSQL.Tuning.md)\. 

**Activity:ArchiverMain**  
The archiver process is waiting for activity\.

**Activity:AutoVacuumMain**  
The autovacuum launcher process is waiting for activity\.

**Activity:BgWriterHibernate**  
The background writer process is hibernating while waiting for activity\.

**Activity:BgWriterMain**  
The background writer process is waiting for activity\.

**Activity:CheckpointerMain**  
The checkpointer process is waiting for activity\.

**Activity:LogicalApplyMain**  
The logical replication apply process is waiting for activity\.

**Activity:LogicalLauncherMain**  
The logical replication launcher process is waiting for activity\.

**Activity:PgStatMain**  
The statistics collector process is waiting for activity\.

**Activity:RecoveryWalAll**  
A process is waiting for the write\-ahead log \(WAL\) from a stream at recovery\.

**Activity:RecoveryWalStream**  
The startup process is waiting for the write\-ahead log \(WAL\) to arrive during streaming recovery\.

**Activity:SysLoggerMain**  
The syslogger process is waiting for activity\.

**Activity:WalReceiverMain**  
The write\-ahead log \(WAL\) receiver process is waiting for activity\.

**Activity:WalSenderMain**  
The write\-ahead log \(WAL\) sender process is waiting for activity\.

**Activity:WalWriterMain**  
The write\-ahead log \(WAL\) writer process is waiting for activity\.

**BufferPin:BufferPin**  
A process is waiting to acquire an exclusive pin on a buffer\.

**Client:GSSOpenServer**  
A process is waiting to read data from the client while establishing a Generic Security Service Application Program Interface \(GSSAPI\) session\.

**Client:ClientRead**  
A backend process is waiting to receive data from a PostgreSQL client\. For more information, see [Client:ClientRead](apg-waits.clientread.md)\.

**Client:ClientWrite**  
A backend process is waiting to send more data to a PostgreSQL client\. For more information, see [Client:ClientWrite](apg-waits.clientwrite.md)\.

**Client:LibPQWalReceiverConnect**  
A process is waiting in the write\-ahead log \(WAL\) receiver to establish connection to remote server\.

**Client:LibPQWalReceiverReceive**  
A process is waiting in the write\-ahead log \(WAL\) receiver to receive data from remote server\.

**Client:SSLOpenServer**  
A process is waiting for Secure Sockets Layer \(SSL\) while attempting connection\.

**Client:WalReceiverWaitStart**  
A process is waiting for startup process to send initial data for streaming replication\.

**Client:WalSenderWaitForWAL**  
A process is waiting for the write\-ahead log \(WAL\) to be flushed in the WAL sender process\.

**Client:WalSenderWriteData**  
A process is waiting for any activity when processing replies from the write\-ahead log \(WAL\) receiver in the WAL sender process\.

**CPU**  
A backend process is active in or is waiting for CPU\. For more information, see [CPU](apg-waits.cpu.md)\.

**Extension:extension**  
A backend process is waiting for a condition defined by an extension or module\.

**IO:AuroraStorageLogAllocate**  
A session is allocating metadata and preparing for a transaction log write\.

**IO:BufFileRead**  
When operations require more memory than the amount defined by working memory parameters, the engine creates temporary files on disk\. This wait event occurs when operations read from the temporary files\. For more information, see [IO:BufFileRead and IO:BufFileWrite](apg-waits.iobuffile.md)\.

**IO:BufFileWrite**  
When operations require more memory than the amount defined by working memory parameters, the engine creates temporary files on disk\. This wait event occurs when operations write to the temporary files\. For more information, see [IO:BufFileRead and IO:BufFileWrite](apg-waits.iobuffile.md)\.

**IO:ControlFileRead**  
A process is waiting for a read from the `pg_control` file\.

**IO:ControlFileSync**  
A process is waiting for the `pg_control` file to reach durable storage\.

**IO:ControlFileSyncUpdate**  
A process is waiting for an update to the `pg_control` file to reach durable storage\.

**IO:ControlFileWrite**  
A process is waiting for a write to the `pg_control` file\.

**IO:ControlFileWriteUpdate**  
A process is waiting for a write to update the `pg_control` file\.

**IO:CopyFileRead**  
A process is waiting for a read during a file copy operation\.

**IO:CopyFileWrite**  
A process is waiting for a write during a file copy operation\.

**IO:DataFileExtend**  
A process is waiting for a relation data file to be extended\.

**IO:DataFileFlush**  
A process is waiting for a relation data file to reach durable storage\.

**IO:DataFileImmediateSync**  
A process is waiting for an immediate synchronization of a relation data file to durable storage\.

**IO:DataFilePrefetch**  
A process is waiting for an asynchronous prefetch from a relation data file\.

**IO:DataFileSync**  
A process is waiting for changes to a relation data file to reach durable storage\.

**IO:DataFileRead**  
A backend process tried to find a page in the shared buffers, didn't find it, and so read it from storage\. For more information, see [IO:DataFileRead](apg-waits.iodatafileread.md)\.

**IO:DataFileTruncate**  
A process is waiting for a relation data file to be truncated\.

**IO:DataFileWrite**  
A process is waiting for a write to a relation data file\.

**IO:DSMFillZeroWrite**  
A process is waiting to write zero bytes to a dynamic shared memory backing file\.

**IO:LockFileAddToDataDirRead**  
A process is waiting for a read while adding a line to the data directory lock file\.

**IO:LockFileAddToDataDirSync**  
A process is waiting for data to reach durable storage while adding a line to the data directory lock file\.

**IO:LockFileAddToDataDirWrite**  
A process is waiting for a write while adding a line to the data directory lock file\.

**IO:LockFileCreateRead**  
A process is waiting to read while creating the data directory lock file\.

**IO:LockFileCreateSync**  
A process is waiting for data to reach durable storage while creating the data directory lock file\.

**IO:LockFileCreateWrite**  
A process is waiting for a write while creating the data directory lock file\.

**IO:LockFileReCheckDataDirRead**  
A process is waiting for a read during recheck of the data directory lock file\.

**IO:LogicalRewriteCheckpointSync**  
A process is waiting for logical rewrite mappings to reach durable storage during a checkpoint\.

**IO:LogicalRewriteMappingSync**  
A process is waiting for mapping data to reach durable storage during a logical rewrite\.

**IO:LogicalRewriteMappingWrite**  
A process is waiting for a write of mapping data during a logical rewrite\.

**IO:LogicalRewriteSync**  
A process is waiting for logical rewrite mappings to reach durable storage\.

**IO:LogicalRewriteTruncate**  
A process is waiting for the truncation of mapping data during a logical rewrite\.

**IO:LogicalRewriteWrite**  
A process is waiting for a write of logical rewrite mappings\.

**IO:RelationMapRead**  
A process is waiting for a read of the relation map file\.

**IO:RelationMapSync**  
A process is waiting for the relation map file to reach durable storage\.

**IO:RelationMapWrite**  
A process is waiting for a write to the relation map file\.

**IO:ReorderBufferRead**  
A process is waiting for a read during reorder buffer management\.

**IO:ReorderBufferWrite**  
A process is waiting for a write during reorder buffer management\.

**IO:ReorderLogicalMappingRead**  
A process is waiting for a read of a logical mapping during reorder buffer management\.

**IO:ReplicationSlotRead**  
A process is waiting for a read from a replication slot control file\.

**IO:ReplicationSlotRestoreSync**  
A process is waiting for a replication slot control file to reach durable storage while restoring it to memory\.

**IO:ReplicationSlotSync**  
A process is waiting for a replication slot control file to reach durable storage\.

**IO:ReplicationSlotWrite**  
A process is waiting for a write to a replication slot control file\.

**IO:SLRUFlushSync**  
A process is waiting for simple least\-recently used \(SLRU\) data to reach durable storage during a checkpoint or database shutdown\.

**IO:SLRURead**  
A process is waiting for a read of a simple least\-recently used \(SLRU\) page\.

**IO:SLRUSync**  
A process is waiting for simple least\-recently used \(SLRU\) data to reach durable storage following a page write\.

**IO:SLRUWrite**  
A process is waiting for a write of a simple least\-recently used \(SLRU\) page\.

**IO:SnapbuildRead**  
A process is waiting for a read of a serialized historical catalog snapshot\.

**IO:SnapbuildSync**  
A process is waiting for a serialized historical catalog snapshot to reach durable storage\.

**IO:SnapbuildWrite**  
A process is waiting for a write of a serialized historical catalog snapshot\.

**IO:TimelineHistoryFileSync**  
A process is waiting for a timeline history file received through streaming replication to reach durable storage\.

**IO:TimelineHistoryFileWrite**  
A process is waiting for a write of a timeline history file received through streaming replication\.

**IO:TimelineHistoryRead**  
A process is waiting for a read of a timeline history file\.

**IO:TimelineHistorySync**  
A process is waiting for a newly created timeline history file to reach durable storage\.

**IO:TimelineHistoryWrite**  
A process is waiting for a write of a newly created timeline history file\.

**IO:TwophaseFileRead**  
A process is waiting for a read of a two phase state file\.

**IO:TwophaseFileSync**  
A process is waiting for a two phase state file to reach durable storage\.

**IO:TwophaseFileWrite**  
A process is waiting for a write of a two phase state file\.

**IO:WALBootstrapSync**  
A process is waiting for the write\-ahead log \(WAL\) to reach durable storage during bootstrapping\.

**IO:WALBootstrapWrite**  
A process is waiting for a write of a write\-ahead log \(WAL\) page during bootstrapping\.

**IO:WALCopyRead**  
A process is waiting for a read when creating a new write\-ahead log \(WAL\) segment by copying an existing one\.

**IO:WALCopySync**  
A process is waiting for a new write\-ahead log \(WAL\) segment created by copying an existing one to reach durable storage\. 

**IO:WALCopyWrite**  
A process is waiting for a write when creating a new write\-ahead log \(WAL\) segment by copying an existing one\.

**IO:WALInitSync**  
A process is waiting for a newly initialized write\-ahead log \(WAL\) file to reach durable storage\.

**IO:WALInitWrite**  
A process is waiting for a write while initializing a new write\-ahead log \(WAL\) file\.

**IO:WALRead**  
A process is waiting for a read from a write\-ahead log \(WAL\) file\.

**IO:WALSenderTimelineHistoryRead**  
A process is waiting for a read from a timeline history file during a WAL sender timeline command\.

**IO:WALSync**  
A process is waiting for a write\-ahead log \(WAL\) file to reach durable storage\.

**IO:WALSyncMethodAssign**  
A process is waiting for data to reach durable storage while assigning a new write\-ahead log \(WAL\) sync method\.

**IO:WALWrite**  
A process is waiting for a write to a write\-ahead log \(WAL\) file\.

**IO:XactSync**  
A backend process is waiting for the Aurora storage subsystem to acknowledge the commit of a regular transaction, or the commit or rollback of a prepared transaction\. For more information, see [IO:XactSync](apg-waits.xactsync.md)\.

**IPC:BackupWaitWalArchive**  
A process is waiting for write\-ahead log \(WAL\) files required for a backup to be successfully archived\.

**IPC:BgWorkerShutdown**  
A process is waiting for a background worker to shut down\.

**IPC:BgWorkerStartup**  
A process is waiting for a background worker to start\. 

**IPC:BtreePage**  
A process is waiting for the page number needed to continue a parallel B\-tree scan to become available\. 

**IPC:CheckpointDone**  
A process is waiting for a checkpoint to complete\. 

**IPC:CheckpointStart**  
A process is waiting for a checkpoint to start\. 

**IPC:ClogGroupUpdate**  
A process is waiting for the group leader to update the transaction status at a transaction's end\.

**pc:damrecordtxack**  
A backend process has generated a database activity streams event and is waiting for the event to become durable\. For more information, see [ipc:damrecordtxack](apg-waits.ipcdamrecordtxac.md)\.

**IPC:ExecuteGather**  
A process is waiting for activity from a child process while executing a Gather plan node\. 

**IPC:Hash/Batch/Allocating**  
A process is waiting for an elected parallel hash participant to allocate a hash table\.

**IPC:Hash/Batch/Electing**  
A process is electing a parallel hash participant to allocate a hash table\.

**IPC:Hash/Batch/Loading**  
A process is waiting for other parallel hash participants to finish loading a hash table\.

**IPC:Hash/Build/Allocating**  
A process is waiting for an elected parallel hash participant to allocate the initial hash table\.

**IPC:Hash/Build/Electing**  
A process is electing a parallel hash participant to allocate the initial hash table\.

**IPC:Hash/Build/HashingInner**  
A process is waiting for other parallel hash participants to finish hashing the inner relation\.

**IPC:Hash/Build/HashingOuter**  
A process is waiting for other parallel hash participants to finish partitioning the outer relation\.

**IPC:Hash/GrowBatches/Allocating**  
A process is waiting for an elected parallel hash participant to allocate more batches\.

**IPC:Hash/GrowBatches/Deciding**  
A process is electing a parallel hash participant to decide on future batch growth\.

**IPC:Hash/GrowBatches/Electing**  
A process is electing a parallel hash participant to allocate more batches\.

**IPC:Hash/GrowBatches/Finishing**  
A process is waiting for an elected parallel hash participant to decide on future batch growth\.

**IPC:Hash/GrowBatches/Repartitioning**  
A process is waiting for other parallel hash participants to finishing repartitioning\.

**IPC:Hash/GrowBuckets/Allocating**  
A process is waiting for an elected parallel hash participant to finish allocating more buckets\.

**IPC:Hash/GrowBuckets/Electing**  
A process is electing a parallel hash participant to allocate more buckets\.

**IPC:Hash/GrowBuckets/Reinserting**  
A process is waiting for other parallel hash participants to finish inserting tuples into new buckets\.

**IPC:HashBatchAllocate**  
A process is waiting for an elected parallel hash participant to allocate a hash table\. 

**IPC:HashBatchElect**  
A process is waiting to elect a parallel hash participant to allocate a hash table\. 

**IPC:HashBatchLoad**  
A process is waiting for other parallel hash participants to finish loading a hash table\. 

**IPC:HashBuildAllocate**  
A process is waiting for an elected parallel hash participant to allocate the initial hash table\. 

**IPC:HashBuildElect**  
A process is waiting to elect a parallel hash participant to allocate the initial hash table\. 

**IPC:HashBuildHashInner**  
A process is waiting for other parallel hash participants to finish hashing the inner relation\. 

**IPC:'HashBuildHashOuter**  
A process is waiting for other parallel hash participants to finish partitioning the outer relation\. 

**IPC:HashGrowBatchesAllocate**  
A process is waiting for an elected parallel hash participant to allocate more batches\. 

**IPC:'HashGrowBatchesDecide**  
A process is waiting to elect a parallel hash participant to decide on future batch growth\. 

**IPC:HashGrowBatchesElect**  
A process is waiting to elect a parallel hash participant to allocate more batches\. 

**IPC:HashGrowBatchesFinish**  
A process is waiting for an elected parallel hash participant to decide on future batch growth\. 

**IPC:HashGrowBatchesRepartition**  
A process is waiting for other parallel hash participants to finish repartitioning\. 

**IPC:HashGrowBucketsAllocate**  
A process is waiting for an elected parallel hash participant to finish allocating more buckets\. 

**IPC:HashGrowBucketsElect**  
A process is waiting to elect a parallel hash participant to allocate more buckets\. 

**IPC:HashGrowBucketsReinsert**  
A process is waiting for other parallel hash participants to finish inserting tuples into new buckets\. 

**IPC:LogicalSyncData**  
A process is waiting for a logical replication remote server to send data for initial table synchronization\. 

**IPC:LogicalSyncStateChange**  
A process is waiting for a logical replication remote server to change state\. 

**IPC:MessageQueueInternal**  
A process is waiting for another process to be attached to a shared message queue\. 

**IPC:MessageQueuePutMessage**  
A process is waiting to write a protocol message to a shared message queue\. 

**IPC:MessageQueueReceive**  
A process is waiting to receive bytes from a shared message queue\. 

**IPC:MessageQueueSend**  
A process is waiting to send bytes to a shared message queue\. 

**IPC:ParallelBitmapScan**  
A process is waiting for a parallel bitmap scan to become initialized\. 

**IPC:ParallelCreateIndexScan**  
A process is waiting for parallel CREATE INDEX workers to finish a heap scan\. 

**IPC:ParallelFinish**  
A process is waiting for parallel workers to finish computing\. 

**IPC:ProcArrayGroupUpdate**  
A process is waiting for the group leader to clear the transaction ID at the end of a parallel operation\. 

**IPC:ProcSignalBarrier**  
A process is waiting for a barrier event to be processed by all backends\. 

**IPC:Promote**  
A process is waiting for standby promotion\. 

**IPC:RecoveryConflictSnapshot**  
A process is waiting for recovery conflict resolution for a vacuum cleanup\. 

**IPC:RecoveryConflictTablespace**  
A process is waiting for recovery conflict resolution for dropping a tablespace\. 

**IPC:RecoveryPause**  
A process is waiting for recovery to be resumed\. 

**IPC:ReplicationOriginDrop**  
A process is waiting for a replication origin to become inactive so it can be dropped\. 

**IPC:ReplicationSlotDrop**  
A process is waiting for a replication slot to become inactive so it can be dropped\. 

**IPC:SafeSnapshot**  
A process is waiting to obtain a valid snapshot for a READ ONLY DEFERRABLE transaction\. 

**IPC:SyncRep**  
A process is waiting for confirmation from a remote server during synchronous replication\. 

**IPC:XactGroupUpdate**  
A process is waiting for the group leader to update the transaction status at the end of a parallel operation\.

**Lock:advisory**  
A backend process requested an advisory lock and is waiting for it\. For more information, see [Lock:advisory](apg-waits.lockadvisory.md)\.

**Lock:extend**  
A backend process is waiting for a lock to be released so that it can extend a relation\. This lock is needed because only one backend process can extend a relation at a time\. For more information, see [Lock:extend](apg-waits.lockextend.md)\.

**Lock:frozenid**  
A process is waiting to update `pg_database.datfrozenxid` and `pg_database.datminmxid`\. 

**Lock:object**  
A process is waiting to get a lock on a nonrelation database object\.

**Lock:page**  
A process is waiting to get a lock on a page of a relation\.

**Lock:Relation**  
A backend process is waiting to acquire a lock on a relation that is locked by another transaction\. For more information, see [Lock:Relation](apg-waits.lockrelation.md)\.

**Lock:spectoken**  
A process is waiting to get a speculative insertion lock\.

**Lock:speculative token**  
A process is waiting to acquire a speculative insertion lock\.

**Lock:transactionid**  
A transaction is waiting for a row\-level lock\. For more information, see [Lock:transactionid](apg-waits.locktransactionid.md)\.

**Lock:tuple**  
A backend process is waiting to acquire a lock on a tuple while another backend process holds a conflicting lock on the same tuple\. For more information, see [Lock:tuple](apg-waits.locktuple.md)\.

**Lock:userlock**  
A process is waiting to get a user lock\.

**Lock:virtualxid**  
A process is waiting to get a virtual transaction ID lock\.

**LWLock:AddinShmemInit**  
A process is waiting to manage an extension's space allocation in shared memory\.

**LWLock:AddinShmemInitLock**  
A process is waiting to manage space allocation in shared memory\.

**LWLock:async**  
A process is waiting for I/O on an async \(notify\) buffer\.

**LWLock:AsyncCtlLock**  
A process is waiting to read or update a shared notification state\.

**LWLock:AsyncQueueLock**  
A process is waiting to read or update notification messages\. 

**LWLock:AutoFile**  
A process is waiting to update the `postgresql.auto.conf` file\.

**LWLock:AutoFileLock**  
A process is waiting to update the `postgresql.auto.conf` file\.

**LWLock:Autovacuum**  
A process is waiting to read or update the current state of autovacuum workers\.

**LWLock:AutovacuumLock**  
An autovacuum worker or launcher is waiting to update or read the current state of autovacuum workers\.

**LWLock:AutovacuumSchedule**  
A process is waiting to ensure that a table selected for autovacuum still needs vacuuming\.

**LWLock:AutovacuumScheduleLock**  
A process is waiting to ensure that the table it has selected for a vacuum still needs vacuuming\. 

**LWLock:BackendRandomLock**  
A process is waiting to generate a random number\. 

**LWLock:BackgroundWorker**  
A process is waiting to read or update background worker state\.

**LWLock:BackgroundWorkerLock**  
A process is waiting to read or update the background worker state\.

**LWLock:BtreeVacuum**  
A process is waiting to read or update vacuum\-related information for a B\-tree index\.

**LWLock:BtreeVacuumLock**  
A process is waiting to read or update vacuum\-related information for a B\-tree index\.

**LWLock:buffer\_content**  
A backend process is waiting to acquire a lightweight lock on the contents of a shared memory buffer\. For more information, see [LWLock:buffer\_content \(BufferContent\)](apg-waits.lockbuffercontent.md)\.

**LWLock:buffer\_mapping**  
A backend process is waiting to associate a data block with a buffer in the shared buffer pool\. For more information, see [LWLock:buffer\_mapping](apg-waits.lwl-buffer-mapping.md)\.

**LWLock:BufferIO**  
A backend process wants to read a page into shared memory\. The process is waiting for other processes to finish their I/O for the page\. For more information, see [LWLock:BufferIO](apg-waits.lwlockbufferio.md)\.

**LWLock:Checkpoint**  
A process is waiting to begin a checkpoint\. 

**LWLock:CheckpointLock**  
A process is waiting to perform checkpoint\. 

**LWLock:CheckpointerComm**  
A process is waiting to manage `fsync` requests\. 

**LWLock:CheckpointerCommLock**  
A process is waiting to manage `fsync` requests\. 

**LWLock:clog**  
A process is waiting for I/O on a clog \(transaction status\) buffer\. 

**LWLock:CLogControlLock**  
A process is waiting to read or update transaction status\. 

**LWLock:CLogTruncationLock**  
A process is waiting to run `txid_status` or update the oldest transaction ID available to it\. 

**LWLock:commit\_timestamp**  
A process is waiting for I/O on a commit timestamp buffer\. 

**LWLock:CommitTs**  
A process is waiting to read or update the last value set for a transaction commit timestamp\. 

**LWLock:CommitTsBuffer**  
A process is waiting for I/O on a simple least\-recently used \(SLRU\) buffer for a commit timestamp\. 

**LWLock:CommitTsControlLock**  
A process is waiting to read or update transaction commit timestamps\. 

**LWLock:CommitTsLock**  
A process is waiting to read or update the last value set for the transaction timestamp\. 

**LWLock:CommitTsSLRU**  
A process is waiting to access the simple least\-recently used \(SLRU\) cache for a commit timestamp\. 

**LWLock:ControlFile**  
A process is waiting to read or update the `pg_control` file or create a new write\-ahead log \(WAL\) file\. 

**LWLock:ControlFileLock**  
A process is waiting to read or update the control file or creation of a new write\-ahead log \(WAL\) file\. 

**LWLock:DynamicSharedMemoryControl**  
A process is waiting to read or update dynamic shared memory allocation information\. 

**LWLock:DynamicSharedMemoryControlLock**  
A process is waiting to read or update the dynamic shared memory state\. 

**LWLock:lock\_manager**  
A backend process is waiting to add or examine locks for backend processes\. Or it's waiting to join or exit a locking group that is used by parallel query\. For more information, see [LWLock:lock\_manager](apg-waits.lw-lock-manager.md)\.

**LWLock:LockFastPath**  
A process is waiting to read or update a process's fast\-path lock information\. 

**LWLock:LogicalRepWorker**  
A process is waiting to read or update the state of logical replication workers\. 

**LWLock:LogicalRepWorkerLock**  
A process is waiting for an action on a logical replication worker to finish\. 

**LWLock:multixact\_member**  
A process is waiting for I/O on a multixact\_member buffer\. 

**LWLock:multixact\_offset**  
A process is waiting for I/O on a multixact offset buffer\. 

**LWLock:MultiXactGen**  
A process is waiting to read or update shared multixact state\. 

**LWLock:MultiXactGenLock**  
A process is waiting to read or update a shared multixact state\. 

**LWLock:MultiXactMemberBuffer**  
A process is waiting for I/O on a simple least\-recently used \(SLRU\) buffer for a multixact member\. For more information, see [LWLock:MultiXact](apg-waits.lwlockmultixact.md)\. 

**LWLock:MultiXactMemberControlLock**  
A process is waiting to read or update multixact member mappings\. 

**LWLock:MultiXactMemberSLRU**  
A process is waiting to access the simple least\-recently used \(SLRU\) cache for a multixact member\. For more information, see [LWLock:MultiXact](apg-waits.lwlockmultixact.md)\. 

**LWLock:MultiXactOffsetBuffer**  
A process is waiting for I/O on a simple least\-recently used \(SLRU\) buffer for a multixact offset\. For more information, see [LWLock:MultiXact](apg-waits.lwlockmultixact.md)\. 

**LWLock:MultiXactOffsetControlLock**  
A process is waiting to read or update multixact offset mappings\. 

**LWLock:MultiXactOffsetSLRU**  
A process is waiting to access the simple least\-recently used \(SLRU\) cache for a multixact offset\. For more information, see [LWLock:MultiXact](apg-waits.lwlockmultixact.md)\. 

**LWLock:MultiXactTruncation**  
A process is waiting to read or truncate multixact information\. 

**LWLock:MultiXactTruncationLock**  
A process is waiting to read or truncate multixact information\. 

**LWLock:NotifyBuffer**  
A process is waiting for I/O on the simple least\-recently used \(SLRU\) buffer for a NOTIFY message\. 

**LWLock:NotifyQueue**  
A process is waiting to read or update NOTIFY messages\.

**LWLock:NotifyQueueTail**  
A process is waiting to update a limit on NOTIFY message storage\.

**LWLock:NotifyQueueTailLock**  
A process is waiting to update limit on notification message storage\.

**LWLock:NotifySLRU**  
A process is waiting to access the simple least\-recently used \(SLRU\) cache for a NOTIFY message\.

**LWLock:OidGen**  
A process is waiting to allocate a new object ID \(OID\)\. 

**LWLock:OidGenLock**  
A process is waiting to allocate or assign an object ID \(OID\)\. 

**LWLock:oldserxid**  
A process is waiting for I/O on an oldserxid buffer\. 

**LWLock:OldSerXidLock**  
A process is waiting to read or record conflicting serializable transactions\.

**LWLock:OldSnapshotTimeMap**  
A process is waiting to read or update old snapshot control information\.

**LWLock:OldSnapshotTimeMapLock**  
A process is waiting to read or update old snapshot control information\.

**LWLock:parallel\_append**  
A process is waiting to choose the next subplan during parallel append plan execution\. 

**LWLock:parallel\_hash\_join**  
A process is waiting to allocate or exchange a chunk of memory or update counters during a parallel hash plan execution\.

**LWLock:parallel\_query\_dsa**  
A process is waiting for a lock on dynamic shared memory allocation for a parallel query\. 

**LWLock:ParallelAppend**  
A process is waiting to choose the next subplan during parallel append plan execution\. 

**LWLock:ParallelHashJoin**  
A process is waiting to synchronize workers during plan execution for a parallel hash join\. 

**Lwlock:ParallelQueryDSA**  
A process is waiting for dynamic shared memory allocation for a parallel query\. 

**Lwlock:PerSessionDSA**  
A process is waiting for dynamic shared memory allocation for a parallel query\. 

**Lwlock:PerSessionRecordType**  
A process is waiting to access a parallel query's information about composite types\. 

**Lwlock:PerSessionRecordTypmod**  
A process is waiting to access a parallel query's information about type modifiers that identify anonymous record types\. 

**Lwlock:PerXactPredicateList**  
A process is waiting to access the list of predicate locks held by the current serializable transaction during a parallel query\. 

**Lwlock:predicate\_lock\_manager**  
A process is waiting to add or examine predicate lock information\.

**Lwlock:PredicateLockManager**  
A process is waiting to access predicate lock information used by serializable transactions\.

**Lwlock:proc**  
A process is waiting to read or update the fast\-path lock information\. 

**LWLock:ProcArray**  
A process is waiting to access the shared per\-process data structures \(typically, to get a snapshot or report a session's transaction ID\)\. 

**LWLock:ProcArrayLock**  
A process is waiting to get a snapshot or clearing a transaction Id at a transaction's end\. 

**LWLock:RelationMapping**  
A process is waiting to read or update a `pg_filenode.map` file \(used to track the file\-node assignments of certain system catalogs\)\. 

**LWLock:RelationMappingLock**  
A process is waiting to update the relation map file used to store catalog\-to\-file\-node mapping\. 

**LWLock:RelCacheInit**  
A process is waiting to read or update a `pg_internal.init` file \(a relation cache initialization file\)\. 

**LWLock:RelCacheInitLock**  
A process is waiting to read or write a relation cache initialization file\. 

**LWLock:replication\_origin**  
A process is waiting to read or update the replication progress\. 

**LWLock:replication\_slot\_io**  
A process is waiting for I/O on a replication slot\. 

**LWLock:ReplicationOrigin**  
A process is waiting to create, drop, or use a replication origin\.

**LWLock:ReplicationOriginLock**  
A process is waiting to set up, drop, or use a replication origin\.

**LWLock:ReplicationOriginState**  
A process is waiting to read or update the progress of one replication origin\. 

**LWLock:ReplicationSlotAllocation**  
A process is waiting to allocate or free a replication slot\.

**LWLock:ReplicationSlotAllocationLock**  
A process is waiting to allocate or free a replication slot\.

**LWLock:ReplicationSlotControl**  
A process is waiting to read or update a replication slot state\.

**LWLock:ReplicationSlotControlLock**  
A process is waiting to read or update the replication slot state\. 

**LWLock:ReplicationSlotIO**  
A process is waiting for I/O on a replication slot\. 

**LWLock:SerialBuffer**  
A process is waiting for I/O on a simple least\-recently used \(SLRU\) buffer for a serializable transaction conflict\. 

**LWLock:SerializableFinishedList**  
A process is waiting to access the list of finished serializable transactions\.

**LWLock:SerializableFinishedListLock**  
A process is waiting to access the list of finished serializable transactions\.

**LWLock:SerializablePredicateList**  
A process is waiting to access the list of predicate locks held by serializable transactions\.

**LWLock:SerializablePredicateLockListLock**  
A process is waiting to perform an operation on a list of locks held by serializable transactions\. 

**LWLock:SerializableXactHash**  
A process is waiting to read or update information about serializable transactions\. 

**LWLock:SerializableXactHashLock**  
A process is waiting to retrieve or store information about serializable transactions\. 

**LWLock:SerialSLRU**  
A process is waiting to access the simple least\-recently used \(SLRU\) cache for a serializable transaction conflict\. 

**LWLock:SharedTidBitmap**  
A process is waiting to access a shared tuple identifier \(TID\) bitmap during a parallel bitmap index scan\. 

**LWLock:SharedTupleStore**  
A process is waiting to access a shared tuple store during a parallel query\. 

**LWLock:ShmemIndex**  
A process is waiting to find or allocate space in shared memory\. 

**LWLock:ShmemIndexLock**  
A process is waiting to find or allocate space in shared memory\. 

**LWLock:SInvalRead**  
A process is waiting to retrieve messages from the shared catalog invalidation queue\. 

**LWLock:SInvalReadLock**  
A process is waiting to retrieve or remove messages from a shared invalidation queue\. 

**LWLock:SInvalWrite**  
A process is waiting to add a message to the shared catalog invalidation queue\. 

**LWLock:SInvalWriteLock**  
A process is waiting to add a message in a shared invalidation queue\. 

**LWLock:subtrans**  
A process is waiting for I/O on a subtransaction buffer\. 

**LWLock:SubtransBuffer**  
A process is waiting for I/O on a simple least\-recently used \(SLRU\) buffer for a subtransaction\. 

**LWLock:SubtransControlLock**  
A process is waiting to read or update subtransaction information\. 

**LWLock:SubtransSLRU**  
A process is waiting to access the simple least\-recently used \(SLRU\) cache for a subtransaction\. 

**LWLock:SyncRep**  
A process is waiting to read or update information about the state of synchronous replication\. 

**LWLock:SyncRepLock**  
A process is waiting to read or update information about synchronous replicas\. 

**LWLock:SyncScan**  
A process is waiting to select the starting location of a synchronized table scan\.

**LWLock:SyncScanLock**  
A process is waiting to get the start location of a scan on a table for synchronized scans\.

**LWLock:TablespaceCreate**  
A process is waiting to create or drop a tablespace\. 

**LWLock:TablespaceCreateLock**  
A process is waiting to create or drop the tablespace\. 

**LWLock:tbm**  
A process is waiting for a shared iterator lock on a tree bitmap \(TBM\)\. 

**LWLock:TwoPhaseState**  
A process is waiting to read or update the state of prepared transactions\. 

**LWLock:TwoPhaseStateLock**  
A process is waiting to read or update the state of prepared transactions\. 

**LWLock:wal\_insert**  
A process is waiting to insert the write\-ahead log \(WAL\) into a memory buffer\. 

**LWLock:WALBufMapping**  
A process is waiting to replace a page in write\-ahead log \(WAL\) buffers\. 

**LWLock:WALBufMappingLock**  
A process is waiting to replace a page in write\-ahead log \(WAL\) buffers\. 

**LWLock:WALInsert**  
A process is waiting to insert write\-ahead log \(WAL\) data into a memory buffer\. 

**LWLock:WALWrite**  
A process is waiting for write\-ahead log \(WAL\) buffers to be written to disk\. 

**LWLock:WALWriteLock**  
A process is waiting for write\-ahead log \(WAL\) buffers to be written to disk\. 

**LWLock:WrapLimitsVacuum**  
A process is waiting to update limits on transaction ID and multixact consumption\. 

**LWLock:WrapLimitsVacuumLock**  
A process is waiting to update limits on transaction ID and multixact consumption\. 

**LWLock:XactBuffer**  
A process is waiting for I/O on a simple least\-recently used \(SLRU\) buffer for a transaction status\. 

**LWLock:XactSLRU**  
A process is waiting to access the simple least\-recently used \(SLRU\) cache for a transaction status\. 

**LWLock:XactTruncation**  
A process is waiting to run pg\_xact\_status or update the oldest transaction ID available to it\. 

**LWLock:XidGen**  
A process is waiting to allocate a new transaction ID\.

**LWLock:XidGenLock**  
A process is waiting to allocate or assign a transaction ID\. 

**Timeout:BaseBackupThrottle**  
A process is waiting during base backup when throttling activity\. 

**Timeout:PgSleep**  
A backend process has called the pg\_sleep function and is waiting for the sleep timeout to expire\. For more information, see [Timeout:PgSleep](apg-waits.timeoutpgsleep.md)\.

**Timeout:RecoveryApplyDelay**  
A process is waiting to apply write\-ahead log \(WAL\) during recovery because of a delay setting\. 

**Timeout:RecoveryRetrieveRetryInterval**  
A process is waiting during recovery when write\-ahead log \(WAL\) data is not available from any source \(pg\_wal, archive, or stream\)\. 

**Timeout:VacuumDelay**  
A process is waiting in a cost\-based vacuum delay point\. 

For a complete list of PostgreSQL wait events, see [The Statistics Collector > Wait Event tables](https://www.postgresql.org/docs/current/monitoring-stats.html#WAIT-EVENT-TABLE) in the PostgreSQL documentation\.