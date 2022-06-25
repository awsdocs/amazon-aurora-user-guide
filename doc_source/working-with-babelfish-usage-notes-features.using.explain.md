# Using explain plan to improve Babelfish query performance<a name="working-with-babelfish-usage-notes-features.using.explain"></a>

Starting with version 2\.1\.0, Babelfish includes two functions that transparently use the PostgreSQL optimizer to generate estimated and actual query plans for T\-SQL queries on the TDS port\. These functions are similar to using SET STATISTICS PROFILE or SET SHOWPLAN\_ALL with SQL Server databases to identify and improve slow running queries\.

**Note**  
Getting query plans from functions, control flows, and cursors isn't currently supported\. 

In the table you can find a comparison of query plan explain functions across SQL Server, Babelfish, and PostgreSQL\. 


|  SQL Server  | Babelfish  | PostgreSQL  | 
| --- | --- | --- | 
| SHOWPLAN\_ALL  | BABELFISH\_SHOWPLAN\_ALL  | EXPLAIN  | 
| STATISTICS PROFILE  | BABELFISH\_STATISTICS PROFILE  | EXPLAIN ANALYZE  | 
| Uses the SQL Server optimizer  | Uses the PostgreSQL optimizer  | Uses the PostgreSQL optimizer  | 
| SQL Server input and output format  | SQL Server input and PostgreSQL output format  | PostgreSQL input and output format  | 
| Set for the session  | Set for the session  | Apply to a specific statement  | 
| Supports the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/working-with-babelfish-usage-notes-features.using.explain.html)  | Supports the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/working-with-babelfish-usage-notes-features.using.explain.html)  | Supports the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/working-with-babelfish-usage-notes-features.using.explain.html)  | 

Use the Babelfish functions as follows:
+ SET BABELFISH\_SHOWPLAN\_ALL \[ON\|OFF\] – Set to ON to generate an estimated query execution plan\. This function implements the behavior of the PostgreSQL `EXPLAIN` command\. Use this command to obtain the explain plan for given query\.
+ SET BABELFISH\_STATISTICS PROFILE \[ON\|OFF\] – Set to ON for actual query execution plans\. This function implements the behavior of PostgreSQL's `EXPLAIN ANALYZE` command\. 

For more information about PostgreSQL `EXPLAIN` and `EXPLAIN ANALYZE` see [EXPLAIN](https://www.postgresql.org/docs/current/sql-explain.html) in the PostgreSQL documentation\.

For example, the following command sequence turns on query planning and then returns an estimated query execution plan for the SELECT statement without running the query\. This example uses the SQL Server sample `northwind` database using the `sqlcmd` command\-line tool to query the TDS port: 

```
1> SET BABELFISH_SHOWPLAN_ALL ON
2> GO
1> SELECT t.territoryid, e.employeeid FROM
2> dbo.employeeterritories e, dbo.territories t
3> WHERE e.territoryid=e.territoryid ORDER BY t.territoryid;
4> GO

QUERY PLAN                                                                                                                                                                                                                                      
------------------------------------------------------------------------------------                                                                                                              
Query Text: SELECT t.territoryid, e.employeeid FROM
dbo.employeeterritories e, dbo.territories t
WHERE e.territoryid=e.territoryid ORDER BY t.territoryid
Sort  (cost=6231.74..6399.22 rows=66992 width=10)
  Sort Key: t.territoryid NULLS FIRST
  ->  Nested Loop  (cost=0.00..861.76 rows=66992 width=10)  
        ->  Seq Scan on employeeterritories e  (cost=0.00..22.70 rows=1264 width=4)
              Filter: ((territoryid)::"varchar" IS NOT NULL)
        ->  Materialize  (cost=0.00..1.79 rows=53 width=6)
              ->  Seq Scan on territories t  (cost=0.00..1.53 rows=53 width=6)
```

When you finish reviewing and adjusting your query, you turn off the function as shown following:

```
1> SET BABELFISH_SHOWPLAN ALL OFF
```

With BABELFISH\_STATISTICS PROFILE set to ON, each executed query returns its regular result set followed by an additional result set that shows actual query execution plans\. Babelfish generates the query plan that provides the fastest result set when it invokes the SELECT statement\. 

```
1> SET BABELFISH_STATISTICS PROFILE ON
1>
2> GO
1> SELECT e.employeeid, t.territoryid FROM
2> dbo.employeeterritories e, dbo.territories t
3> WHERE t.territoryid=e.territoryid ORDER BY t.territoryid;
4> GO
```

The result set and the query plan are returned \(this example shows only the query plan\)\. 

```
QUERY PLAN                                                                                                                                                                                                                                
---------------------------------------------------------------------------
Query Text: SELECT e.employeeid, t.territoryid FROM
dbo.employeeterritories e, dbo.territories t
WHERE t.territoryid=e.territoryid ORDER BY t.territoryid
Sort  (cost=42.44..43.28 rows=337 width=10)
  Sort Key: t.territoryid NULLS FIRST                                                                                                                                               
  ->  Hash Join  (cost=2.19..28.29 rows=337 width=10)
       Hash Cond: ((e.territoryid)::"varchar" = (t.territoryid)::"varchar")
        ->  Seq Scan on employeeterritories e  (cost=0.00..22.70 rows=1270 width=36)
        ->  Hash  (cost=1.53..1.53 rows=53 width=6)
             ->  Seq Scan on territories t  (cost=0.00..1.53 rows=53 width=6)
```

To learn more about how to analyze your queries and the results returned by the PostgreSQL optimizer, see [explain\.depesz\.com](https://www.depesz.com/2013/04/16/explaining-the-unexplainable/)\. For more information about PostgreSQL EXPLAIN and EXPLAIN ANALYZE, see [EXPLAIN](https://www.postgresql.org/docs/current/sql-explain.html) in the PostgreSQL documentation\. 

## Parameters that control Babelfish explain options<a name="working-with-babelfish-usage-notes-features.using.explain.parameters"></a>

You can use the parameters shown in the following table to control the type of information that's displayed by your query plan\. 


| Parameter | Description | 
| --- | --- | 
| babelfishpg\_tsql\.explain\_buffers | A boolean that turns on \(and off\) buffer usage information for the optimizer\. \(Default: off\) \(Allowable: off, on\)  | 
| babelfishpg\_tsql\.explain\_costs | A boolean that turns on \(and off\) estimated startup and total cost information for the optimizer\. \(Default: on\) \(Allowable: off, on\)  | 
| babelfishpg\_tsql\.explain\_format | Specifies the output format for the `EXPLAIN` plan\. \(Default: text\) \(Allowable: text, xml, json, yaml\)  | 
| babelfishpg\_tsql\.explain\_settings | A boolean that turns on \(or off\) the inclusion of information about configuration parameters in the EXPLAIN plan output\. \(Default: off\) \(Allowable: off, on\)  | 
| babelfishpg\_tsql\.explain\_summary | A boolean that turns on \(or off\) summary information such as total time after the query plan\. \(Default: off\) \(Allowable: off, on\)  | 
| babelfishpg\_tsql\.explain\_timing | A boolean that turns on \(or off\) actual startup time and time spent in each node in the output\. \(Default: off\) \(Allowable: off, on\)  | 
| babelfishpg\_tsql\.explain\_verbose | A boolean that turns on \(or off\) the most detailed version of an explain plan\. \(Default: off\) \(Allowable: off, on\)  | 
| babelfishpg\_tsql\.explain\_wal | A boolean that turns on \(or off\) generation of WAL record information as part of an explain plan\. \(Default: off\) \(Allowable: off, on\)  | 

You can check the values of any Babelfish\-related parameters on your system by using either PostgreSQL client or SQL Server client\. You can get your current parameter values as follows: 

```
1> :setvar SQLCMDMAXVARTYPEWIDTH 35
2> :setvar SQLCMDMAXFIXEDTYPEWIDTH 10
3> SELECT name,
4> setting,
5> short_desc,
6> pending_restart
7> FROM pg_settings
8> WHERE name LIKE '%babelfishpg_tsql.explain%';
9> GO
```

In the following output, you can see that all settings on this particular Babelfish DB cluster are at their default values\. Not all output is shown in this example\.

```
name             setting         short_desc
---------------------------------- ----- -----------------------------------
babelfishpg_tsql.explain_buffers   off   Include information on buffer usage
babelfishpg_tsql.explain_costs     on    Include information on estimated st
babelfishpg_tsql.explain_format    text  Specify the output format, which ca
babelfishpg_tsql.explain_settings  off   Include information on configuratio
babelfishpg_tsql.explain_summary   off   Include summary information (e.g., 
babelfishpg_tsql.explain_timing    off   Include actual startup time and tim
babelfishpg_tsql.explain_verbose   off   Display additional information rega
babelfishpg_tsql.explain_wal       off   Include information on WAL record g

(8 rows affected)
```

You can change the setting for these parameters using a SELECT statement with the PostgreSQL set\_config function, as shown in the following example\. 

```
SELECT set_config('babelfish_tsql.explain_verbose', 'on', false);
```

For more information about set\_config, see [Configuration Settings Functions](https://www.postgresql.org/docs/current/functions-admin.html#FUNCTIONS-ADMIN-SET) in the PostgreSQL documentation\. 