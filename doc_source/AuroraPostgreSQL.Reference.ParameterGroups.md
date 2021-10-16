# Amazon Aurora PostgreSQL parameters<a name="AuroraPostgreSQL.Reference.ParameterGroups"></a>

You manage your Amazon Aurora DB cluster in the same way that you manage other Amazon RDS DB instances, by using parameters in a DB parameter group\. Amazon Aurora differs from other DB engines in that you have a DB cluster that contains multiple DB instances\. As a result, some of the parameters that you use to manage your Amazon Aurora DB cluster apply to the entire cluster, while other parameters apply only to a particular DB instance in the DB cluster\.

You manage cluster\-level parameters in DB cluster parameter groups\. You manage instance\-level parameters in DB parameter groups\. Each DB instance in an Aurora PostgreSQL DB cluster is compatible with the PostgreSQL database engine\. However, you apply some of the PostgreSQL database engine parameters at the cluster level and manage them using DB cluster parameter groups\. Cluster\-level parameters aren't found in the DB parameter group for a DB instance in an Aurora PostgreSQL DB cluster and are listed later in this topic\.

You can view both cluster\-level and instance\-level parameters in the Amazon RDS console, or by using the AWS CLI or Amazon RDS API\. For example, to view cluster\-level parameters in a DB cluster parameter group, use the [describe\-db\-cluster\-parameters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-cluster-parameters.html) AWS CLI command\. To view instance\-level parameters in a DB parameter group for a DB instance in a DB cluster, use the [describe\-db\-parameters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-parameters.html) AWS CLI command\.

You can manage both cluster\-level and instance\-level parameters using the Amazon RDS console, the AWS CLI, or the Amazon RDS API\. There are separate commands for managing cluster\-level parameters and instance\-level parameters\. For example:
+ To manage cluster\-level parameters in a DB cluster parameter group, use the [modify\-db\-cluster\-parameter\-group](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster-parameter-group.html) AWS CLI command\.
+ To manage instance\-level parameters in a DB parameter group for a DB instance in a DB cluster, use the [modify\-db\-parameter\-group](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-parameter-group.html) AWS CLI command\.

For more information about parameter groups, see [Working with DB parameter groups and DB cluster parameter groups](USER_WorkingWithParamGroups.md)\.

## Aurora PostgreSQL cluster\-level parameters<a name="AuroraPostgreSQL.Reference.Parameters.Cluster"></a>

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
| huge\_pages |  No  | 
|  intervalstyle  |  Yes  | 
|  lc\_monetary  |  Yes  | 
|  lc\_numeric  |  Yes  | 
|  lc\_time  |  Yes  | 
|  log\_autovacuum\_min\_duration  |  Yes  | 
| max\_prepared\_transactions |  Yes  | 
|  password\_encryption  |  No  | 
|  max\_standby\_streaming\_delay  |  Yes for PostgreSQL 11, No for earlier PostgreSQL versions  | 
|  port  |  No  | 
| random\_page\_cost  | Yes | 
| rds\.enable\_plan\_management  | Yes | 
| rds\.extensions |  No  | 
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
| vacuum\_cost\_delay |  Yes  | 
| vacuum\_cost\_limit |  Yes  | 
| vacuum\_cost\_page\_hit |  Yes  | 
| vacuum\_cost\_page\_miss |  Yes  | 
|  vacuum\_defer\_cleanup\_age  |  Yes  | 
|  vacuum\_freeze\_min\_age  |  Yes  | 
|  vacuum\_freeze\_table\_age  |  Yes  | 
|  vacuum\_multixact\_freeze\_min\_age  |  Yes  | 
|  vacuum\_multixact\_freeze\_table\_age  |  Yes  | 
|  wal\_buffers  |  Yes  | 
| wal\_sender\_timeout |  Yes  | 

## Aurora PostgreSQL instance\-level parameters<a name="AuroraPostgreSQL.Reference.Parameters.Instance"></a>

The following table shows all of the parameters that apply to a specific DB instance in an Aurora PostgreSQL DB cluster\. 


| Parameter name | Modifiable | 
| --- | --- | 
| apg\_enable\_not\_in\_transform | Yes | 
| apg\_enable\_remove\_redundant\_inner\_joins | Yes | 
| apg\_enable\_semijoin\_push\_down  | Yes | 
| apg\_plan\_mgmt\.capture\_plan\_baselines | Yes | 
| apg\_plan\_mgmt\.max\_databases | Yes | 
| apg\_plan\_mgmt\.max\_plans | Yes | 
| apg\_plan\_mgmt\.plan\_retention\_period | Yes | 
| apg\_plan\_mgmt\.unapproved\_plan\_execution\_threshold | Yes | 
| apg\_plan\_mgmt\.use\_plan\_baselines | Yes | 
|  application\_name  |  Yes  | 
| authentication\_timeout |  Yes  | 
| auto\_explain\.log\_analyze |  Yes  | 
| auto\_explain\.log\_buffers |  Yes  | 
| auto\_explain\.log\_format |  Yes  | 
| auto\_explain\.log\_min\_duration |  Yes  | 
| auto\_explain\.log\_nested\_statements |  Yes  | 
| auto\_explain\.log\_timing |  Yes  | 
| auto\_explain\.log\_triggers |  Yes  | 
| auto\_explain\.log\_verbose |  Yes  | 
| auto\_explain\.sample\_rate |  Yes  | 
| backend\_flush\_after |  Yes  | 
| bgwriter\_flush\_after |  Yes  | 
| bytea\_output |  Yes  | 
| check\_function\_bodies |  Yes  | 
| checkpoint\_flush\_after |  Yes  | 
| checkpoint\_timeout | No | 
| client\_min\_messages |  Yes  | 
| config\_file |  No  | 
| constraint\_exclusion |  Yes  | 
| cpu\_index\_tuple\_cost |  Yes  | 
| cpu\_operator\_cost |  Yes  | 
| cpu\_tuple\_cost |  Yes  | 
| cursor\_tuple\_fraction |  Yes  | 
| db\_user\_namespace |  No  | 
| deadlock\_timeout |  Yes  | 
| debug\_pretty\_print |  Yes  | 
| debug\_print\_parse |  Yes  | 
| debug\_print\_plan |  Yes  | 
| debug\_print\_rewritten |  Yes  | 
| default\_statistics\_target |  Yes  | 
| default\_transaction\_deferrable |  Yes  | 
| default\_transaction\_isolation |  Yes  | 
| default\_transaction\_read\_only |  Yes  | 
| effective\_cache\_size |  Yes  | 
| effective\_io\_concurrency |  No  | 
| enable\_bitmapscan |  Yes  | 
| enable\_hashagg |  Yes  | 
| enable\_hashjoin |  Yes  | 
| enable\_indexonlyscan |  Yes  | 
| enable\_indexscan |  Yes  | 
| enable\_material |  Yes  | 
| enable\_mergejoin |  Yes  | 
| enable\_nestloop |  Yes  | 
| enable\_seqscan |  Yes  | 
| enable\_sort |  Yes  | 
| enable\_tidscan |  Yes  | 
| escape\_string\_warning |  Yes  | 
| exit\_on\_error |  No  | 
| force\_parallel\_mode |  Yes  | 
| from\_collapse\_limit |  Yes  | 
| geqo |  Yes  | 
| geqo\_effort |  Yes  | 
| geqo\_generations |  Yes  | 
| geqo\_pool\_size |  Yes  | 
| geqo\_seed |  Yes  | 
| geqo\_selection\_bias |  Yes  | 
| geqo\_threshold |  Yes  | 
| gin\_fuzzy\_search\_limit |  Yes  | 
| gin\_pending\_list\_limit |  Yes  | 
| hba\_file |  No  | 
| hot\_standby\_feedback |  No  | 
| ident\_file |  No  | 
| idle\_in\_transaction\_session\_timeout |  Yes  | 
| join\_collapse\_limit |  Yes  | 
| lc\_messages |  Yes  | 
| listen\_addresses |  No  | 
| lo\_compat\_privileges |  No  | 
| log\_connections |  Yes  | 
| log\_destination |  Yes  | 
| log\_directory |  No  | 
| log\_disconnections |  Yes  | 
| log\_duration |  Yes  | 
| log\_error\_verbosity |  Yes  | 
| log\_executor\_stats |  Yes  | 
| log\_file\_mode |  No  | 
| log\_filename |  Yes  | 
| log\_hostname |  Yes  | 
| log\_line\_prefix |  No  | 
| log\_lock\_waits |  Yes  | 
| log\_min\_duration\_statement |  Yes  | 
| log\_min\_error\_statement |  Yes  | 
| log\_min\_messages |  Yes  | 
| log\_parser\_stats |  Yes  | 
| log\_planner\_stats |  Yes  | 
| log\_replication\_commands |  Yes  | 
| log\_rotation\_age |  Yes  | 
| log\_rotation\_size |  Yes  | 
| log\_statement |  Yes  | 
| log\_statement\_stats |  Yes  | 
| log\_temp\_files |  Yes  | 
| log\_timezone |  No  | 
| log\_truncate\_on\_rotation |  No  | 
| logging\_collector |  No  | 
| maintenance\_work\_mem |  Yes  | 
| max\_connections |  Yes  | 
| max\_files\_per\_process |  Yes  | 
| max\_locks\_per\_transaction |  Yes  | 
| max\_replication\_slots |  Yes  | 
| max\_stack\_depth |  Yes  | 
| max\_standby\_archive\_delay |  No  | 
| max\_standby\_streaming\_delay |  Yes for PostgreSQL 11, No for earlier PostgreSQL versions  | 
| max\_wal\_senders |  Yes  | 
| max\_worker\_processes |  Yes  | 
| min\_parallel\_relation\_size |  Yes  | 
| old\_snapshot\_threshold |  Yes  | 
| operator\_precedence\_warning |  Yes  | 
| parallel\_setup\_cost |  Yes  | 
| parallel\_tuple\_cost |  Yes  | 
| pg\_hint\_plan\.debug\_print |  Yes  | 
| pg\_hint\_plan\.enable\_hint |  Yes  | 
| pg\_hint\_plan\.enable\_hint\_table |  Yes  | 
| pg\_hint\_plan\.message\_level |  Yes  | 
| pg\_hint\_plan\.parse\_messages |  Yes  | 
| pg\_stat\_statements\.max |  Yes  | 
| pg\_stat\_statements\.save |  Yes  | 
| pg\_stat\_statements\.track |  Yes  | 
| pg\_stat\_statements\.track\_utility |  Yes  | 
| pgaudit\.log |  Yes  | 
| pgaudit\.log\_catalog |  Yes  | 
| pgaudit\.log\_level |  Yes  | 
| pgaudit\.log\_parameter |  Yes  | 
| pgaudit\.log\_relation |  Yes  | 
| pgaudit\.log\_statement\_once |  Yes  | 
| pgaudit\.role |  Yes  | 
| postgis\.gdal\_enabled\_drivers |  Yes  | 
| quote\_all\_identifiers |  Yes  | 
| random\_page\_cost |  Yes  | 
| rds\.force\_admin\_logging\_level |  Yes  | 
| rds\.log\_retention\_period |  Yes  | 
| rds\.rds\_superuser\_reserved\_connections |  Yes  | 
| rds\.superuser\_variables |  No  | 
| replacement\_sort\_tuples |  Yes  | 
| restart\_after\_crash |  No  | 
| row\_security |  Yes  | 
| search\_path |  Yes  | 
| seq\_page\_cost |  Yes  | 
| session\_replication\_role |  Yes  | 
| shared\_buffers |  Yes  | 
| shared\_preload\_libraries |  Yes  | 
| sql\_inheritance |  Yes  | 
| ssl\_ca\_file |  No  | 
| ssl\_cert\_file |  No  | 
| ssl\_ciphers |  No  | 
| ssl\_key\_file |  No  | 
| standard\_conforming\_strings |  Yes  | 
| statement\_timeout |  Yes  | 
| stats\_temp\_directory |  No  | 
| superuser\_reserved\_connections |  No  | 
| synchronize\_seqscans |  Yes  | 
| syslog\_facility |  No  | 
| tcp\_keepalives\_count |  Yes  | 
| tcp\_keepalives\_idle |  Yes  | 
| tcp\_keepalives\_interval |  Yes  | 
| temp\_buffers |  Yes  | 
| temp\_tablespaces |  Yes  | 
| track\_activities |  Yes  | 
| track\_activity\_query\_size |  Yes  | 
| track\_counts |  Yes  | 
| track\_functions |  Yes  | 
| track\_io\_timing |  Yes  | 
| transaction\_deferrable |  Yes  | 
| transaction\_read\_only |  Yes  | 
| transform\_null\_equals |  Yes  | 
| unix\_socket\_directories |  No  | 
| unix\_socket\_group |  No  | 
| unix\_socket\_permissions |  No  | 
| update\_process\_title |  Yes  | 
| wal\_receiver\_status\_interval |  Yes  | 
| wal\_receiver\_timeout |  Yes  | 
| work\_mem |  Yes  | 
| xmlbinary |  Yes  | 
| xmloption |  Yes  | 