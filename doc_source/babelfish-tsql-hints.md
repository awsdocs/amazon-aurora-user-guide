# Using T\-SQL query hints to improve Babelfish query performance<a name="babelfish-tsql-hints"></a>

Starting with version 2\.3\.0, Babelfish supports the use of query hints using `pg_hint_plan`\. In Aurora PostgreSQL, `pg_hint_plan` is installed by default\. For more information about the PostgreSQL extension `pg_hint_plan`, see [https://github.com/ossc-db/pg_hint_plan](https://github.com/ossc-db/pg_hint_plan)\. For details about the version of this extension supported by Aurora PostgreSQL, see [Extension versions for Amazon Aurora PostgreSQL](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Extensions.html) in *Release Notes for Aurora PostgreSQL*\. 

 The query optimizer is well\-designed to find the optimal execution plan for a SQL statement\. When selecting a plan, the query optimizer considers both the engine’s cost model, and column and table statistics\. However, the suggested plan might not meet the needs of your datasets\. Thus, query hints addresses the performance issues to improve execution plans\. A `query hint` is syntax added to the SQL standard that instructs the database engine about how to execute the query\. For example, a hint may instruct the engine to follow a sequential scan and override any plan that the query optimizer had selected\. 

## Turning on T\-SQL query hints in Babelfish<a name="babelfish-tsql-hints-turningon"></a>

Currently, Babelfish ignores all T\-SQL hints by default\. To apply T\-SQL hints, run the command `sp_babelfish_configure` with the enable\_pg\_hint value as ON\.

```
EXECUTE sp_babelfish_configure 'enable_pg_hint', 'on' [, 'server']
```

You can make the settings permanent on a cluster\-wide level by including the *server* keyword\. To configure the setting for the current session only, don't use server\. 

After `enable_pg_hint` is ON, Babelfish applies the following T\-SQL hints\.
+ INDEX hints
+ JOIN hints
+ FORCE ORDER hint
+ MAXDOP hint

For example, the following command sequence turns on `pg_hint_plan`\.

```
1> CREATE TABLE t1 (a1 INT PRIMARY KEY, b1 INT);
2> CREATE TABLE t2 (a2 INT PRIMARY KEY, b2 INT);
3> GO    
1> EXECUTE sp_babelfish_configure 'enable_pg_hint', 'on';
2> GO
1> SET BABELFISH_SHOWPLAN_ALL ON;
2> GO
1> SELECT * FROM t1 JOIN t2 ON t1.a1 = t2.a2; --NO HINTS (HASH JOIN)
2> GO
```

No hint is applied to the SELECT statement\. The query plan with no hint is returned\.

```
QUERY PLAN                                                                                                                                                                                                                                
---------------------------------------------------------------------------
Query Text: SELECT * FROM t1 JOIN t2 ON t1.a1 = t2.a2
Hash Join (cost=60.85..99.39 rows=2260 width=16)
 Hash Cond: (t1.a1 = t2.a2)
 -> Seq Scan on t1 (cost=0.00..32.60 rows=2260 width=8)
 -> Hash (cost=32.60..32.60 rows=2260 width=8)
 -> Seq Scan on t2 (cost=0.00..32.60 rows=2260 width=8)
```

```
1> SELECT * FROM t1 INNER MERGE JOIN t2 ON t1.a1 = t2.a2;
2> GO
```

The query hint is applied to the SELECT statement\. The following output shows that the query plan with merge join is returned\.

```
QUERY PLAN                                                                                                                                                                                                                                
---------------------------------------------------------------------------
Query Text: SELECT/*+ MergeJoin(t1 t2) Leading(t1 t2)*/ * FROM t1 INNER JOIN t2 ON t1.a1 = t2.a2
Merge Join (cost=0.31..190.01 rows=2260 width=16)
 Merge Cond: (t1.a1 = t2.a2)
 -> Index Scan using t1_pkey on t1 (cost=0.15..78.06 rows=2260 width=8)
 -> Index Scan using t2_pkey on t2 (cost=0.15..78.06 rows=2260 width=8)
```

```
1> SET BABELFISH_SHOWPLAN_ALL OFF;
2> GO
```

## Limitations<a name="babelfish-tsql-hints-limitations"></a>

While using the query hints, consider the following limitations:
+ If a query plan is cached before `enable_pg_hint` is turned on, hints won't be applied in the same session\. It will be applied in the new session \.
+ If schema names are explicitly given, then hints can't be applied\. You can use table aliases as a workaround\.
+ A query hint can't be applied to views and sub\-queries\.
+ Hints don't work for UPDATE/DELETE statements with JOINs\.
+ An index hint for a non\-existing index or table is ignored\.
+ The FORCE ORDER hint doesn't work for HASH JOINs and non\-ANSI JOINs\.