# Amazon Aurora PostgreSQL wait events<a name="AuroraPostgreSQL.Reference.Waitevents"></a>

The following are common wait events for Aurora PostgreSQL\.

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
A process is waiting for segmented least\-recently used \(SLRU\) data to reach durable storage during a checkpoint or database shutdown\.

**IO:SLRURead**  
A process is waiting for a read of a segmented least\-recently used \(SLRU\) page\.

**IO:SLRUSync**  
A process is waiting for segmented least\-recently used \(SLRU\) data to reach durable storage following a page write\.

**IO:SLRUWrite**  
A process is waiting for a write of a segmented least\-recently used \(SLRU\) page\.

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

**Lwlock:AddinShmemInit**  
A process is waiting to manage an extension's space allocation in shared memory\.

**Lwlock:AddinShmemInitLock**  
A process is waiting to manage space allocation in shared memory\.

**Lwlock:async**  
A process is waiting for I/O on an async \(notify\) buffer\.

**Lwlock:AsyncCtlLock**  
A process is waiting to read or update a shared notification state\.

**Lwlock:AsyncQueueLock**  
A process is waiting to read or update notification messages\. 

**Lwlock:AutoFile**  
A process is waiting to update the `postgresql.auto.conf` file\.

**Lwlock:AutoFileLock**  
A process is waiting to update the `postgresql.auto.conf` file\.

**Lwlock:Autovacuum**  
A process is waiting to read or update the current state of autovacuum workers\.

**Lwlock:AutovacuumLock**  
An autovacuum worker or launcher is waiting to update or read the current state of autovacuum workers\.

**Lwlock:AutovacuumSchedule**  
A process is waiting to ensure that a table selected for autovacuum still needs vacuuming\.

**Lwlock:AutovacuumScheduleLock**  
A process is waiting to ensure that the table it has selected for a vacuum still needs vacuuming\. 

**Lwlock:BackendRandomLock**  
A process is waiting to generate a random number\. 

**Lwlock:BackgroundWorker**  
A process is waiting to read or update background worker state\.

**Lwlock:BackgroundWorkerLock**  
A process is waiting to read or update the background worker state\.

**Lwlock:BtreeVacuum**  
A process is waiting to read or update vacuum\-related information for a B\-tree index\.

**Lwlock:BtreeVacuumLock**  
A process is waiting to read or update vacuum\-related information for a B\-tree index\.

**LWLock:buffer\_content**  
A backend process is waiting to acquire a lightweight lock on the contents of a shared memory buffer\. For more information, see [lwlock:buffer\_content \(BufferContent\)](apg-waits.lockbuffercontent.md)\.

**LWLock:buffer\_mapping**  
A backend process is waiting to associate a data block with a buffer in the shared buffer pool\. For more information, see [LWLock:buffer\_mapping](apg-waits.lwl-buffer-mapping.md)\.

**LWLock:BufferIO**  
A backend process wants to read a page into shared memory\. The process is waiting for other processes to finish their I/O for the page\. For more information, see [LWLock:BufferIO](apg-waits.lwlockbufferio.md)\.

**Lwlock:Checkpoint**  
A process is waiting to begin a checkpoint\. 

**Lwlock:CheckpointLock**  
A process is waiting to perform checkpoint\. 

**Lwlock:CheckpointerComm**  
A process is waiting to manage `fsync` requests\. 

**Lwlock:CheckpointerCommLock**  
A process is waiting to manage `fsync` requests\. 

**Lwlock:clog**  
A process is waiting for I/O on a clog \(transaction status\) buffer\. 

**Lwlock:CLogControlLock**  
A process is waiting to read or update transaction status\. 

**Lwlock:CLogTruncationLock**  
A process is waiting to run `txid_status` or update the oldest transaction ID available to it\. 

**Lwlock:commit\_timestamp**  
A process is waiting for I/O on a commit timestamp buffer\. 

**Lwlock:CommitTs**  
A process is waiting to read or update the last value set for a transaction commit timestamp\. 

**Lwlock:CommitTsBuffer**  
A process is waiting for I/O on a segmented least\-recently used \(SLRU\) buffer for a commit timestamp\. 

**Lwlock:CommitTsControlLock**  
A process is waiting to read or update transaction commit timestamps\. 

**Lwlock:CommitTsLock**  
A process is waiting to read or update the last value set for the transaction timestamp\. 

**Lwlock:CommitTsSLRU**  
A process is waiting to access the segmented least\-recently used \(SLRU\) cache for a commit timestamp\. 

**Lwlock:ControlFile**  
A process is waiting to read or update the `pg_control` file or create a new write\-ahead log \(WAL\) file\. 

**Lwlock:ControlFileLock**  
A process is waiting to read or update the control file or creation of a new write\-ahead log \(WAL\) file\. 

**Lwlock:DynamicSharedMemoryControl**  
A process is waiting to read or update dynamic shared memory allocation information\. 

**Lwlock:DynamicSharedMemoryControlLock**  
A process is waiting to read or update the dynamic shared memory state\. 

**LWLock:lock\_manager**  
A backend process is waiting to add or examine locks for backend processes\. Or it's waiting to join or exit a locking group that is used by parallel query\. For more information, see [LWLock:lock\_manager](apg-waits.lw-lock-manager.md)\.

**Lwlock:LockFastPath**  
A process is waiting to read or update a process's fast\-path lock information\. 

**Lwlock:LogicalRepWorker**  
A process is waiting to read or update the state of logical replication workers\. 

**Lwlock:LogicalRepWorkerLock**  
A process is waiting for an action on a logical replication worker to finish\. 

**Lwlock:multixact\_member**  
A process is waiting for I/O on a multixact\_member buffer\. 

**Lwlock:multixact\_offset**  
A process is waiting for I/O on a multixact offset buffer\. 

**Lwlock:MultiXactGen**  
A process is waiting to read or update shared multixact state\. 

**Lwlock:MultiXactGenLock**  
A process is waiting to read or update a shared multixact state\. 

**Lwlock:MultiXactMemberBuffer**  
A process is waiting for I/O on a segmented least\-recently used \(SLRU\) buffer for a multixact member\. 

**Lwlock:MultiXactMemberControlLock**  
A process is waiting to read or update multixact member mappings\. 

**Lwlock:MultiXactMemberSLRU**  
A process is waiting to access the segmented least\-recently used \(SLRU\) cache for a multixact member\. 

**Lwlock:MultiXactOffsetBuffer**  
A process is waiting for I/O on a segmented least\-recently used \(SLRU\) buffer for a multixact offset\. 

**Lwlock:MultiXactOffsetControlLock**  
A process is waiting to read or update multixact offset mappings\. 

**Lwlock:MultiXactOffsetSLRU**  
A process is waiting to access the segmented least\-recently used \(SLRU\) cache for a multixact offset\. 

**Lwlock:MultiXactTruncation**  
A process is waiting to read or truncate multixact information\. 

**Lwlock:MultiXactTruncationLock**  
A process is waiting to read or truncate multixact information\. 

**Lwlock:NotifyBuffer**  
A process is waiting for I/O on the segmented least\-recently used \(SLRU\) buffer for a NOTIFY message\. 

**Lwlock:NotifyQueue**  
A process is waiting to read or update NOTIFY messages\.

**Lwlock:NotifyQueueTail**  
A process is waiting to update a limit on NOTIFY message storage\.

**Lwlock:NotifyQueueTailLock**  
A process is waiting to update limit on notification message storage\.

**Lwlock:NotifySLRU**  
A process is waiting to access the segmented least\-recently used \(SLRU\) cache for a NOTIFY message\.

**Lwlock:OidGen**  
A process is waiting to allocate a new object ID \(OID\)\. 

**Lwlock:OidGenLock**  
A process is waiting to allocate or assign an object ID \(OID\)\. 

**Lwlock:oldserxid**  
A process is waiting for I/O on an oldserxid buffer\. 

**Lwlock:OldSerXidLock**  
A process is waiting to read or record conflicting serializable transactions\.

**Lwlock:OldSnapshotTimeMap**  
A process is waiting to read or update old snapshot control information\.

**Lwlock:OldSnapshotTimeMapLock**  
A process is waiting to read or update old snapshot control information\.

**Lwlock:parallel\_append**  
A process is waiting to choose the next subplan during parallel append plan execution\. 

**Lwlock:parallel\_hash\_join**  
A process is waiting to allocate or exchange a chunk of memory or update counters during a parallel hash plan execution\.

**Lwlock:parallel\_query\_dsa**  
A process is waiting for a lock on dynamic shared memory allocation for a parallel query\. 

**Lwlock:ParallelAppend**  
A process is waiting to choose the next subplan during parallel append plan execution\. 

**Lwlock:ParallelHashJoin**  
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

**Lwlock:ProcArray**  
A process is waiting to access the shared per\-process data structures \(typically, to get a snapshot or report a session's transaction ID\)\. 

**Lwlock:ProcArrayLock**  
A process is waiting to get a snapshot or clearing a transaction Id at a transaction's end\. 

**Lwlock:RelationMapping**  
A process is waiting to read or update a `pg_filenode.map` file \(used to track the file\-node assignments of certain system catalogs\)\. 

**Lwlock:RelationMappingLock**  
A process is waiting to update the relation map file used to store catalog\-to\-file\-node mapping\. 

**Lwlock:RelCacheInit**  
A process is waiting to read or update a `pg_internal.init` file \(a relation cache initialization file\)\. 

**Lwlock:RelCacheInitLock**  
A process is waiting to read or write a relation cache initialization file\. 

**Lwlock:replication\_origin**  
A process is waiting to read or update the replication progress\. 

**Lwlock:replication\_slot\_io**  
A process is waiting for I/O on a replication slot\. 

**Lwlock:ReplicationOrigin**  
A process is waiting to create, drop, or use a replication origin\.

**Lwlock:ReplicationOriginLock**  
A process is waiting to set up, drop, or use a replication origin\.

**Lwlock:ReplicationOriginState**  
A process is waiting to read or update the progress of one replication origin\. 

**Lwlock:ReplicationSlotAllocation**  
A process is waiting to allocate or free a replication slot\.

**Lwlock:ReplicationSlotAllocationLock**  
A process is waiting to allocate or free a replication slot\.

**Lwlock:ReplicationSlotControl**  
A process is waiting to read or update a replication slot state\.

**Lwlock:ReplicationSlotControlLock**  
A process is waiting to read or update the replication slot state\. 

**Lwlock:ReplicationSlotIO**  
A process is waiting for I/O on a replication slot\. 

**Lwlock:SerialBuffer**  
A process is waiting for I/O on a segmented least\-recently used \(SLRU\) buffer for a serializable transaction conflict\. 

**Lwlock:SerializableFinishedList**  
A process is waiting to access the list of finished serializable transactions\.

**Lwlock:SerializableFinishedListLock**  
A process is waiting to access the list of finished serializable transactions\.

**Lwlock:SerializablePredicateList**  
A process is waiting to access the list of predicate locks held by serializable transactions\.

**Lwlock:SerializablePredicateLockListLock**  
A process is waiting to perform an operation on a list of locks held by serializable transactions\. 

**Lwlock:SerializableXactHash**  
A process is waiting to read or update information about serializable transactions\. 

**Lwlock:SerializableXactHashLock**  
A process is waiting to retrieve or store information about serializable transactions\. 

**Lwlock:SerialSLRU**  
A process is waiting to access the segmented least\-recently used \(SLRU\) cache for a serializable transaction conflict\. 

**Lwlock:SharedTidBitmap**  
A process is waiting to access a shared tuple identifier \(TID\) bitmap during a parallel bitmap index scan\. 

**Lwlock:SharedTupleStore**  
A process is waiting to access a shared tuple store during a parallel query\. 

**Lwlock:ShmemIndex**  
A process is waiting to find or allocate space in shared memory\. 

**Lwlock:ShmemIndexLock**  
A process is waiting to find or allocate space in shared memory\. 

**Lwlock:SInvalRead**  
A process is waiting to retrieve messages from the shared catalog invalidation queue\. 

**Lwlock:SInvalReadLock**  
A process is waiting to retrieve or remove messages from a shared invalidation queue\. 

**Lwlock:SInvalWrite**  
A process is waiting to add a message to the shared catalog invalidation queue\. 

**Lwlock:SInvalWriteLock**  
A process is waiting to add a message in a shared invalidation queue\. 

**Lwlock:subtrans**  
A process is waiting for I/O on a subtransaction buffer\. 

**Lwlock:SubtransBuffer**  
A process is waiting for I/O on a segmented least\-recently used \(SLRU\) buffer for a subtransaction\. 

**Lwlock:SubtransControlLock**  
A process is waiting to read or update subtransaction information\. 

**Lwlock:SubtransSLRU**  
A process is waiting to access the segmented least\-recently used \(SLRU\) cache for a subtransaction\. 

**Lwlock:SyncRep**  
A process is waiting to read or update information about the state of synchronous replication\. 

**Lwlock:SyncRepLock**  
A process is waiting to read or update information about synchronous replicas\. 

**Lwlock:SyncScan**  
A process is waiting to select the starting location of a synchronized table scan\.

**Lwlock:SyncScanLock**  
A process is waiting to get the start location of a scan on a table for synchronized scans\.

**Lwlock:TablespaceCreate**  
A process is waiting to create or drop a tablespace\. 

**Lwlock:TablespaceCreateLock**  
A process is waiting to create or drop the tablespace\. 

**Lwlock:tbm**  
A process is waiting for a shared iterator lock on a tree bitmap \(TBM\)\. 

**Lwlock:TwoPhaseState**  
A process is waiting to read or update the state of prepared transactions\. 

**Lwlock:TwoPhaseStateLock**  
A process is waiting to read or update the state of prepared transactions\. 

**Lwlock:wal\_insert**  
A process is waiting to insert the write\-ahead log \(WAL\) into a memory buffer\. 

**Lwlock:WALBufMapping**  
A process is waiting to replace a page in write\-ahead log \(WAL\) buffers\. 

**Lwlock:WALBufMappingLock**  
A process is waiting to replace a page in write\-ahead log \(WAL\) buffers\. 

**Lwlock:WALInsert**  
A process is waiting to insert write\-ahead log \(WAL\) data into a memory buffer\. 

**Lwlock:WALWrite**  
A process is waiting for write\-ahead log \(WAL\) buffers to be written to disk\. 

**Lwlock:WALWriteLock**  
A process is waiting for write\-ahead log \(WAL\) buffers to be written to disk\. 

**Lwlock:WrapLimitsVacuum**  
A process is waiting to update limits on transaction ID and multixact consumption\. 

**Lwlock:WrapLimitsVacuumLock**  
A process is waiting to update limits on transaction ID and multixact consumption\. 

**Lwlock:XactBuffer**  
A process is waiting for I/O on a segmented least\-recently used \(SLRU\) buffer for a transaction status\. 

**Lwlock:XactSLRU**  
A process is waiting to access the segmented least\-recently used \(SLRU\) cache for a transaction status\. 

**Lwlock:XactTruncation**  
A process is waiting to run pg\_xact\_status or update the oldest transaction ID available to it\. 

**Lwlock:XidGen**  
A process is waiting to allocate a new transaction ID\.

**Lwlock:XidGenLock**  
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