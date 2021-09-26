# Amazon Aurora PostgreSQL reference<a name="AuroraPostgreSQL.Reference"></a>

**Topics**
+ [Amazon Aurora PostgreSQL parameters](#AuroraPostgreSQL.Reference.ParameterGroups)
+ [Amazon Aurora PostgreSQL events](#AuroraPostgreSQL.Reference.Waitevents)
+ [Aurora PostgreSQL functions reference](Appendix.AuroraPostgreSQL.Functions.md)

## Amazon Aurora PostgreSQL parameters<a name="AuroraPostgreSQL.Reference.ParameterGroups"></a>

You manage your Amazon Aurora DB cluster in the same way that you manage other Amazon RDS DB instances, by using parameters in a DB parameter group\. Amazon Aurora differs from other DB engines in that you have a DB cluster that contains multiple DB instances\. As a result, some of the parameters that you use to manage your Amazon Aurora DB cluster apply to the entire cluster, while other parameters apply only to a particular DB instance in the DB cluster\.

Cluster\-level parameters are managed in DB cluster parameter groups\. Instance\-level parameters are managed in DB parameter groups\. Although each DB instance in an Aurora PostgreSQL DB cluster is compatible with the PostgreSQL database engine, some of the PostgreSQL database engine parameters must be applied at the cluster level, and are managed using DB cluster parameter groups\. Cluster\-level parameters are not found in the DB parameter group for a DB instance in an Aurora PostgreSQL DB cluster and are listed later in this topic\.

You can view both cluster\-level and instance\-level parameters in the Amazon RDS console, or by using the AWS CLI or Amazon RDS API\. For example, to view cluster\-level parameters in a DB cluster parameter group, use the [describe\-db\-cluster\-parameters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-cluster-parameters.html) AWS CLI command\. To view instance\-level parameters in a DB parameter group for a DB instance in a DB cluster, use the [describe\-db\-parameters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-parameters.html) AWS CLI command\.

You can manage both cluster\-level and instance\-level parameters using the Amazon RDS console, the AWS CLI, or the Amazon RDS API\. There are separate commands for managing cluster\-level parameters and instance\-level parameters\. For example:
+ To manage cluster\-level parameters in a DB cluster parameter group, use the [modify\-db\-cluster\-parameter\-group](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster-parameter-group.html) AWS CLI command\.
+ To manage instance\-level parameters in a DB parameter group for a DB instance in a DB cluster, use the [modify\-db\-parameter\-group](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-parameter-group.html) AWS CLI command\.

For more information about parameter groups, see [Working with DB parameter groups and DB cluster parameter groups](USER_WorkingWithParamGroups.md)\.

### Aurora PostgreSQL cluster\-level parameters<a name="AuroraPostgreSQL.Reference.Parameters.Cluster"></a>

The following table shows all of the parameters that apply to the entire Aurora PostgreSQL DB cluster\. 


| Parameter name | Modifiable | 
| --- | --- | 
| ansi\_constraint\_trigger\_ordering | Yes | 
| ansi\_force\_foreign\_key\_checks | Yes | 
| ansi\_qualified\_update\_set\_target | Yes | 
|  apg\_ccm\_enabled  |  Yes  | 
| archive\_command | No | 
|  archive\_timeout  |  No  | 
|  array\_nulls  |  Yes  | 
|  autovacuum  |  Yes  | 
|  autovacuum\_analyze\_scale\_factor  |  Yes  | 
|  autovacuum\_analyze\_threshold  |  Yes  | 
|  autovacuum\_freeze\_max\_age  |  Yes  | 
|  autovacuum\_max\_workers  |  Yes  | 
|  autovacuum\_multixact\_freeze\_max\_age  |  Yes  | 
|  autovacuum\_naptime  |  Yes  | 
|  autovacuum\_vacuum\_cost\_delay  |  Yes  | 
|  autovacuum\_vacuum\_cost\_limit  |  Yes  | 
|  autovacuum\_vacuum\_scale\_factor  |  Yes  | 
|  autovacuum\_vacuum\_threshold  |  Yes  | 
|  autovacuum\_work\_mem  |  Yes  | 
|  backslash\_quote  |  Yes  | 
|  client\_encoding  |  Yes  | 
|  data\_directory  |  No  | 
|  datestyle  |  Yes  | 
|  default\_tablespace  |  Yes  | 
|  default\_with\_oids  |  Yes  | 
|  extra\_float\_digits  |  Yes  | 
| huge\_pages | No | 
|  intervalstyle  |  Yes  | 
|  lc\_monetary  |  Yes  | 
|  lc\_numeric  |  Yes  | 
|  lc\_time  |  Yes  | 
|  log\_autovacuum\_min\_duration  |  Yes  | 
| max\_prepared\_transactions | Yes | 
|  password\_encryption  |  No  | 
|  max\_standby\_streaming\_delay  |  Yes for PostgreSQL 11, No for earlier PostgreSQL versions  | 
|  port  |  No  | 
| rds\.enable\_plan\_management  | Yes | 
| rds\.extensions | No | 
|  rds\.force\_autovacuum\_logging\_level  |  Yes  | 
|  rds\.force\_ssl  |  Yes  | 
| rds\.global\_db\_rpo | Yes | 
| rds\.logical\_replication | Yes | 
| rds\.pg\_stat\_ramdisk\_size | Yes | 
| rds\.restrict\_password\_commands | Yes | 
|  server\_encoding  |  No  | 
|  ssl  |  Yes  | 
| ssl\_max\_protocol\_version | Yes | 
| ssl\_min\_protocol\_version | Yes | 
|  synchronous\_commit  |  Yes  | 
|  timezone  |  Yes  | 
|  track\_commit\_timestamp  |  Yes  | 
| vacuum\_cost\_delay | Yes | 
| vacuum\_cost\_limit | Yes | 
| vacuum\_cost\_page\_hit | Yes | 
| vacuum\_cost\_page\_miss | Yes | 
|  vacuum\_defer\_cleanup\_age  |  Yes  | 
|  vacuum\_freeze\_min\_age  |  Yes  | 
|  vacuum\_freeze\_table\_age  |  Yes  | 
|  vacuum\_multixact\_freeze\_min\_age  |  Yes  | 
|  vacuum\_multixact\_freeze\_table\_age  |  Yes  | 
|  wal\_buffers  |  Yes  | 
| wal\_sender\_timeout | Yes | 

### Aurora PostgreSQL instance\-level parameters<a name="AuroraPostgreSQL.Reference.Parameters.Instance"></a>

The following table shows all of the parameters that apply to a specific DB instance in an Aurora PostgreSQL DB cluster\. 


| Parameter name | Modifiable | 
| --- | --- | 
| apg\_enable\_not\_in\_transform | Yes | 
| apg\_enable\_remove\_redundant\_inner\_joins | Yes | 
| apg\_enable\_semijoin\_push\_down  | Yes | 
| apg\_plan\_mgmt\.capture\_plan\_baselines | Yes | 
| apg\_plan\_mgmt\.max\_databases | Yes | 
| apg\_plan\_mgmt\.max\_plans | Yes | 
| apg\_plan\_mgmt\.pgss\_min\_calls \(deprecated\) | Yes | 
| apg\_plan\_mgmt\.pgss\_min\_mean\_time\_ms \(deprecated\) | Yes | 
| apg\_plan\_mgmt\.pgss\_min\_stddev\_time\_ms \(deprecated\) | Yes | 
| apg\_plan\_mgmt\.pgss\_min\_total\_time\_ms \(deprecated\) | Yes | 
| apg\_plan\_mgmt\.plan\_retention\_period | Yes | 
| apg\_plan\_mgmt\.unapproved\_plan\_execution\_threshold | Yes | 
| apg\_plan\_mgmt\.use\_plan\_baselines | Yes | 
|  application\_name  |  Yes  | 
| authentication\_timeout | Yes | 
| auto\_explain\.log\_analyze | Yes | 
| auto\_explain\.log\_buffers | Yes | 
| auto\_explain\.log\_format | Yes | 
| auto\_explain\.log\_min\_duration | Yes | 
| auto\_explain\.log\_nested\_statements | Yes | 
| auto\_explain\.log\_timing | Yes | 
| auto\_explain\.log\_triggers | Yes | 
| auto\_explain\.log\_verbose | Yes | 
| auto\_explain\.sample\_rate | Yes | 
| backend\_flush\_after | Yes | 
| bgwriter\_flush\_after | Yes | 
| bytea\_output | Yes | 
| check\_function\_bodies | Yes | 
| checkpoint\_flush\_after | Yes | 
| checkpoint\_timeout | No | 
| client\_min\_messages | Yes | 
| config\_file | No | 
| constraint\_exclusion | Yes | 
| cpu\_index\_tuple\_cost | Yes | 
| cpu\_operator\_cost | Yes | 
| cpu\_tuple\_cost | Yes | 
| cursor\_tuple\_fraction | Yes | 
| db\_user\_namespace | No | 
| deadlock\_timeout | Yes | 
| debug\_pretty\_print | Yes | 
| debug\_print\_parse | Yes | 
| debug\_print\_plan | Yes | 
| debug\_print\_rewritten | Yes | 
| default\_statistics\_target | Yes | 
| default\_transaction\_deferrable | Yes | 
| default\_transaction\_isolation | Yes | 
| default\_transaction\_read\_only | Yes | 
| effective\_cache\_size | Yes | 
| effective\_io\_concurrency | No | 
| enable\_bitmapscan | Yes | 
| enable\_hashagg | Yes | 
| enable\_hashjoin | Yes | 
| enable\_indexonlyscan | Yes | 
| enable\_indexscan | Yes | 
| enable\_material | Yes | 
| enable\_mergejoin | Yes | 
| enable\_nestloop | Yes | 
| enable\_seqscan | Yes | 
| enable\_sort | Yes | 
| enable\_tidscan | Yes | 
| escape\_string\_warning | Yes | 
| exit\_on\_error | No | 
| force\_parallel\_mode | Yes | 
| from\_collapse\_limit | Yes | 
| geqo | Yes | 
| geqo\_effort | Yes | 
| geqo\_generations | Yes | 
| geqo\_pool\_size | Yes | 
| geqo\_seed | Yes | 
| geqo\_selection\_bias | Yes | 
| geqo\_threshold | Yes | 
| gin\_fuzzy\_search\_limit | Yes | 
| gin\_pending\_list\_limit | Yes | 
| hba\_file | No | 
| hot\_standby\_feedback | No | 
| ident\_file | No | 
| idle\_in\_transaction\_session\_timeout | Yes | 
| join\_collapse\_limit | Yes | 
| lc\_messages | Yes | 
| listen\_addresses | No | 
| lo\_compat\_privileges | No | 
| log\_connections | Yes | 
| log\_destination | Yes | 
| log\_directory | No | 
| log\_disconnections | Yes | 
| log\_duration | Yes | 
| log\_error\_verbosity | Yes | 
| log\_executor\_stats | Yes | 
| log\_file\_mode | No | 
| log\_filename | Yes | 
| log\_hostname | Yes | 
| log\_line\_prefix | No | 
| log\_lock\_waits | Yes | 
| log\_min\_duration\_statement | Yes | 
| log\_min\_error\_statement | Yes | 
| log\_min\_messages | Yes | 
| log\_parser\_stats | Yes | 
| log\_planner\_stats | Yes | 
| log\_replication\_commands | Yes | 
| log\_rotation\_age | Yes | 
| log\_rotation\_size | Yes | 
| log\_statement | Yes | 
| log\_statement\_stats | Yes | 
| log\_temp\_files | Yes | 
| log\_timezone | No | 
| log\_truncate\_on\_rotation | No | 
| logging\_collector | No | 
| maintenance\_work\_mem | Yes | 
| max\_connections | Yes | 
| max\_files\_per\_process | Yes | 
| max\_locks\_per\_transaction | Yes | 
| max\_replication\_slots | Yes | 
| max\_stack\_depth | Yes | 
| max\_standby\_archive\_delay | No | 
| max\_standby\_streaming\_delay | Yes for PostgreSQL 11, No for earlier PostgreSQL versions | 
| max\_wal\_senders | Yes | 
| max\_worker\_processes | Yes | 
| min\_parallel\_relation\_size | Yes | 
| old\_snapshot\_threshold | Yes | 
| operator\_precedence\_warning | Yes | 
| parallel\_setup\_cost | Yes | 
| parallel\_tuple\_cost | Yes | 
| pg\_hint\_plan\.debug\_print | Yes | 
| pg\_hint\_plan\.enable\_hint | Yes | 
| pg\_hint\_plan\.enable\_hint\_table | Yes | 
| pg\_hint\_plan\.message\_level | Yes | 
| pg\_hint\_plan\.parse\_messages | Yes | 
| pg\_stat\_statements\.max | Yes | 
| pg\_stat\_statements\.save | Yes | 
| pg\_stat\_statements\.track | Yes | 
| pg\_stat\_statements\.track\_utility | Yes | 
| pgaudit\.log | Yes | 
| pgaudit\.log\_catalog | Yes | 
| pgaudit\.log\_level | Yes | 
| pgaudit\.log\_parameter | Yes | 
| pgaudit\.log\_relation | Yes | 
| pgaudit\.log\_statement\_once | Yes | 
| pgaudit\.role | Yes | 
| postgis\.gdal\_enabled\_drivers | Yes | 
| quote\_all\_identifiers | Yes | 
| random\_page\_cost | Yes | 
| rds\.force\_admin\_logging\_level | Yes | 
| rds\.log\_retention\_period | Yes | 
| rds\.rds\_superuser\_reserved\_connections | Yes | 
| rds\.superuser\_variables | No | 
| replacement\_sort\_tuples | Yes | 
| restart\_after\_crash | No | 
| row\_security | Yes | 
| search\_path | Yes | 
| seq\_page\_cost | Yes | 
| session\_replication\_role | Yes | 
| shared\_buffers | Yes | 
| shared\_preload\_libraries | Yes | 
| sql\_inheritance | Yes | 
| ssl\_ca\_file | No | 
| ssl\_cert\_file | No | 
| ssl\_ciphers | No | 
| ssl\_key\_file | No | 
| standard\_conforming\_strings | Yes | 
| statement\_timeout | Yes | 
| stats\_temp\_directory | No | 
| superuser\_reserved\_connections | No | 
| synchronize\_seqscans | Yes | 
| syslog\_facility | No | 
| tcp\_keepalives\_count | Yes | 
| tcp\_keepalives\_idle | Yes | 
| tcp\_keepalives\_interval | Yes | 
| temp\_buffers | Yes | 
| temp\_tablespaces | Yes | 
| track\_activities | Yes | 
| track\_activity\_query\_size | Yes | 
| track\_counts | Yes | 
| track\_functions | Yes | 
| track\_io\_timing | Yes | 
| transaction\_deferrable | Yes | 
| transaction\_read\_only | Yes | 
| transform\_null\_equals | Yes | 
| unix\_socket\_directories | No | 
| unix\_socket\_group | No | 
| unix\_socket\_permissions | No | 
| update\_process\_title | Yes | 
| wal\_receiver\_status\_interval | Yes | 
| wal\_receiver\_timeout | Yes | 
| work\_mem | Yes | 
| xmlbinary | Yes | 
| xmloption | Yes | 

## Amazon Aurora PostgreSQL events<a name="AuroraPostgreSQL.Reference.Waitevents"></a>

The following are some common wait events for Aurora PostgreSQL\.

**BufferPin:BufferPin**  
In this wait event, a session is waiting to access a data buffer during a period when no other session can examine that buffer\. Buffer pin waits can be protracted if another process holds an open cursor which last read data from the buffer in question\.

**Client:ClientRead**  
In this wait event, a session is receiving data from an application client\. This wait might be prevalent during bulk data loads using the COPY statement, or for applications that pass data to Aurora using many round trips between the client and the database\. A high number of client read waits per transaction may indicate excessive round trips, such as parameter passing\. You should compare this against the expected number of statements per transaction\. 

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
In this wait event, a session is waiting to read or write a data page in memory while another session has that page locked for writing\. Heavy write contention for a single page \(hot page\), due to frequent updates of the same piece of data by many sessions, could lead to prevalence of this wait event\. Excessive use of foreign key constraints could increase lock duration, leading to increased contention\. You should investigate workloads experiencing high `buffer_content` waits for usage of foreign key constraints to determine if the constraints are necessary\. Alternatively, decreasing the fillfactor on the parent table will spread the keys across more of the block and can reduce contention on the page\.

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