# Aurora MySQL hints<a name="AuroraMySQL.Reference.Hints"></a><a name="hints"></a>

You can use SQL hints with Aurora MySQL queries to fine\-tune performance\. You can also use hints to prevent execution plans for important queries from changing because of unpredictable conditions\.

**Tip**  
To verify the effect that a hint has on a query, examine the query plan produced by the `EXPLAIN` statement\. Compare the query plans with and without the hint\.

In Aurora MySQL version 3, you can use all the hints that are available in MySQL Community Edition 8\.0\. For more information about these hints, see [Optimizer Hints](https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html) in the *MySQL Reference Manual*\.

The following hints are available in Aurora MySQL version 2\. These hints apply to queries that use the hash join feature in Aurora MySQL version 2, especially queries that use parallel query optimization\.

**PQ, NO\_PQ**  
Specifies whether to force the optimizer to use parallel query on a per\-table or per\-query basis\.  
`PQ` forces the optimizer to use parallel query for specified tables or the whole query \(block\)\. `NO_PQ` prevents the optimizer from using parallel query for specified tables or the whole query \(block\)\.  
This hint is available in Aurora MySQL version 2\.11 and higher\. The following examples show you how to use this hint\.  
Specifying a table name forces the optimizer to apply the `PQ/NO_PQ` hint only on those select tables\. Not specifying a table name forces the `PQ/NO_PQ` hint on all tables affected by the query block\.

```
EXPLAIN SELECT /*+ PQ() */ f1, f2
    FROM num1 t1 WHERE f1 > 10 and f2 < 100;

EXPLAIN SELECT /*+ PQ(t1) */ f1, f2
    FROM num1 t1 WHERE f1 > 10 and f2 < 100;

EXPLAIN SELECT /*+ PQ(t1,t2) */ f1, f2
    FROM num1 t1, num1 t2 WHERE t1.f1 = t2.f21;

EXPLAIN SELECT /*+ NO_PQ() */ f1, f2
    FROM num1 t1 WHERE f1 > 10 and f2 < 100;

EXPLAIN SELECT /*+ NO_PQ(t1) */ f1, f2
    FROM num1 t1 WHERE f1 > 10 and f2 < 100;
    
EXPLAIN SELECT /*+ NO_PQ(t1,t2) */ f1, f2
    FROM num1 t1, num1 t2 WHERE t1.f1 = t2.f21;
```

**HASH\_JOIN, NO\_HASH\_JOIN**  
Turns on or off the ability of the parallel query optimizer to choose whether to use the hash join optimization method for a query\. `HASH_JOIN` lets the optimizer use hash join if that mechanism is more efficient\. `NO_HASH_JOIN` prevents the optimizer from using hash join for the query\. This hint is available in Aurora MySQL version 2\.08 and higher\. It has no effect in Aurora MySQL version 3\.  
The following examples show you how to use this hint\.  

```
EXPLAIN SELECT/*+ HASH_JOIN(t2) */ f1, f2
  FROM t1, t2 WHERE t1.f1 = t2.f1;

EXPLAIN SELECT /*+ NO_HASH_JOIN(t2) */ f1, f2
  FROM t1, t2 WHERE t1.f1 = t2.f1;
```

**HASH\_JOIN\_PROBING, NO\_HASH\_JOIN\_PROBING**  
In a hash join query, specifies whether to use the specified table for the probe side of the join\. The query tests if column values from the build table exist in the probe table, instead of reading the entire contents of the probe table\. You can use `HASH_JOIN_PROBING` and `HASH_JOIN_BUILDING` to specify how hash join queries are processed without reordering the tables within the query text\. This hint is available in Aurora MySQL version 2\.08 and higher\. It has no effect in Aurora MySQL version 3\.  
The following examples show how to use this hint\. Specifying the `HASH_JOIN_PROBING` hint for the table `T2` has the same effect as specifying `NO_HASH_JOIN_PROBING` for the table `T1`\.  

```
EXPLAIN SELECT /*+ HASH_JOIN(t2) HASH_JOIN_PROBING(t2) */ f1, f2
  FROM t1, t2 WHERE t1.f1 = t2.f1;

EXPLAIN SELECT /*+ HASH_JOIN(t2) NO_HASH_JOIN_PROBING(t1) */ f1, f2
  FROM t1, t2 WHERE t1.f1 = t2.f1;
```

**HASH\_JOIN\_BUILDING, NO\_HASH\_JOIN\_BUILDING**  
In a hash join query, specifies whether to use the specified table for the build side of the join\. The query processes all the rows from this table to build the list of column values to cross\-reference with the other table\. You can use `HASH_JOIN_PROBING` and `HASH_JOIN_BUILDING` to specify how hash join queries are processed without reordering the tables within the query text\. This hint is available in Aurora MySQL version 2\.08 and higher\. It has no effect in Aurora MySQL version 3\.  
The following examples show you how to use this hint\. Specifying the `HASH_JOIN_BUILDING` hint for the table `T2` has the same effect as specifying `NO_HASH_JOIN_BUILDING` for the table `T1`\.  

```
EXPLAIN SELECT /*+ HASH_JOIN(t2) HASH_JOIN_BUILDING(t2) */ f1, f2
  FROM t1, t2 WHERE t1.f1 = t2.f1;

EXPLAIN SELECT /*+ HASH_JOIN(t2) NO_HASH_JOIN_BUILDING(t1) */ f1, f2
  FROM t1, t2 WHERE t1.f1 = t2.f1;
```

**JOIN\_FIXED\_ORDER**  
Specifies that tables in the query are joined based on the order they are listed in the query\. It is useful with queries involving three or more tables\. It is intended as a replacement for the MySQL `STRAIGHT_JOIN` hint and is equivalent to the MySQL [JOIN\_FIXED\_ORDER](https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html) hint\. This hint is available in Aurora MySQL version 2\.08 and higher\.  
The following examples show you how to use this hint\.  

```
EXPLAIN SELECT /*+ JOIN_FIXED_ORDER */ f1, f2
  FROM t1 JOIN t2 USING (id) JOIN t3 USING (id) JOIN t4 USING (id);
```

**JOIN\_ORDER**  
Specifies the join order for the tables in the query\. It is useful with queries involving three or more tables\. It is equivalent to the MySQL [JOIN\_ORDER](https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html) hint\. This hint is available in Aurora MySQL version 2\.08 and higher\.  
The following examples show you how to use this hint\.  

```
EXPLAIN SELECT /*+ JOIN_ORDER (t4, t2, t1, t3) */ f1, f2
  FROM t1 JOIN t2 USING (id) JOIN t3 USING (id) JOIN t4 USING (id);
```

**JOIN\_PREFIX**  
Specifies the tables to put first in the join order\. It is useful with queries involving three or more tables\. It is equivalent to the MySQL [JOIN\_PREFIX](https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html) hint\. This hint is available in Aurora MySQL version 2\.08 and higher\.  
The following examples show you how to use this hint\.  

```
EXPLAIN SELECT /*+ JOIN_ORDER (t4, t2) */ f1, f2
  FROM t1 JOIN t2 USING (id) JOIN t3 USING (id) JOIN t4 USING (id);
```

**JOIN\_SUFFIX**  
Specifies the tables to put last in the join order\. It is useful with queries involving three or more tables\. It is equivalent to the MySQL [JOIN\_SUFFIX](https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html) hint\. This hint is available in Aurora MySQL version 2\.08 and higher\.  
The following examples show you how to use this hint\.  

```
EXPLAIN SELECT /*+ JOIN_ORDER (t1, t3) */ f1, f2
  FROM t1 JOIN t2 USING (id) JOIN t3 USING (id) JOIN t4 USING (id);
```

For information about using hash join queries, see [Optimizing large Aurora MySQL join queries with hash joins](AuroraMySQL.BestPractices.md#Aurora.BestPractices.HashJoin)\.