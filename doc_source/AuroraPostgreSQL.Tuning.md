# Tuning with wait events for Aurora PostgreSQL<a name="AuroraPostgreSQL.Tuning"></a>

Wait events are an important tuning tool for Aurora PostgreSQL\. If you can find out why sessions are waiting for resources and what they are doing, you are better able to reduce bottlenecks\. You can use the information in this section to find possible causes and corrective actions\.

**Important**  
The wait events in this chapter are specific to Aurora PostgreSQL\. Use the information in this chapter to tune only Amazon Aurora, not RDS for PostgreSQL\.   
Some wait events in this chapter have no analogs in the open source versions of these database engines\. Other wait events have the same names as events in open source engines, but behave differently\. For example, Amazon Aurora storage works differently from open source storage, so storage\-related wait events indicate different resource conditions\.

**Topics**
+ [Essential concepts for Aurora PostgreSQL tuning](AuroraPostgreSQL.Tuning.concepts.md)
+ [Aurora PostgreSQL wait events](AuroraPostgreSQL.Tuning.concepts.summary.md)
+ [Client:ClientRead](apg-waits.clientread.md)
+ [Client:ClientWrite](apg-waits.clientwrite.md)
+ [CPU](apg-waits.cpu.md)
+ [IO:BufFileRead and IO:BufFileWrite](apg-waits.iobuffile.md)
+ [IO:DataFileRead](apg-waits.iodatafileread.md)
+ [IO:XactSync](apg-waits.xactsync.md)
+ [ipc:damrecordtxack](apg-waits.ipcdamrecordtxac.md)
+ [Lock:advisory](apg-waits.lockadvisory.md)
+ [Lock:extend](apg-waits.lockextend.md)
+ [Lock:Relation](apg-waits.lockrelation.md)
+ [Lock:transactionid](apg-waits.locktransactionid.md)
+ [Lock:tuple](apg-waits.locktuple.md)
+ [lwlock:buffer\_content \(BufferContent\)](apg-waits.lockbuffercontent.md)
+ [LWLock:buffer\_mapping](apg-waits.lwl-buffer-mapping.md)
+ [LWLock:BufferIO](apg-waits.lwlockbufferio.md)
+ [LWLock:lock\_manager](apg-waits.lw-lock-manager.md)
+ [Timeout:PgSleep](apg-waits.timeoutpgsleep.md)
