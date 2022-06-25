# Managing collations<a name="collation.managing"></a>

The ICU library provides collation version tracking to ensure that indexes that depend on collations can be reindexed when a new version of ICU becomes available\. To see if your current database has collations that need refreshing, you can use the following query after connecting using `psql` or or `pgAdmin`:

```
SELECT pg_describe_object(refclassid, refobjid,
    refobjsubid) AS "Collation", 
    pg_describe_object(classid, objid, objsubid) AS "Object" 
    FROM pg_depend d JOIN pg_collation c ON refclassid = 'pg_collation'::regclass
    AND refobjid = c.oid WHERE c.collversion <> pg_collation_actual_version(c.oid) 
    ORDER BY 1, 2;
```

This query returns output such as the following:

```
 Collation | Object
-----------+--------
(0 rows)
```

In this example, no collations need to be updated\.

To get a listing of the predefined collations in your Babelfish database, you can use `psql` or `pgAdmin` with the following query:

```
SELECT * FROM pg_collation;
```

Predefined collations are stored in the `sys.fn_helpcollations` table\. You can use the following command to display information about a collation \(such as its lcid, style, and collate flags\)\. To get a listing of all collations by using `sqlcmd`, connect to the T\-SQL port \(1433, by default\) and run the following query: 

```
1> :setvar SQLCMDMAXVARTYPEWIDTH 40
2> :setvar SQLCMDMAXFIXEDTYPEWIDTH 40
3> SELECT * FROM fn_helpcollations()
4> GO
name                                     description
---------------------------------------- ----------------------------------------
arabic_cs_as                             Arabic, case-sensitive, accent-sensitive
arabic_ci_ai                             Arabic, case-insensitive, accent-insensi
arabic_ci_as                             Arabic, case-insensitive, accent-sensiti
bbf_unicode_bin2                         Unicode-General, case-sensitive, accent-
bbf_unicode_cp1250_ci_ai                 Default locale, code page 1250, case-ins
bbf_unicode_cp1250_ci_as                 Default locale, code page 1250, case-ins
bbf_unicode_cp1250_cs_ai                 Default locale, code page 1250, case-sen
bbf_unicode_cp1250_cs_as                 Default locale, code page 1250, case-sen
bbf_unicode_pref_cp1250_cs_as            Default locale, code page 1250, case-sen
bbf_unicode_cp1251_ci_ai                 Default locale, code page 1251, case-ins
bbf_unicode_cp1251_ci_as                 Default locale, code page 1251, case-ins
bbf_unicode_cp1254_ci_ai                 Default locale, code page 1254, case-ins
...
(124 rows affected)
```

Lines 1 and 2 shown in the example narrow the output for documentation readability purposes only\. 

```
1> SELECT SERVERPROPERTY('COLLATION')
2> GO
serverproperty
---------------------------------------------------------------------------
sql_latin1_general_cp1_ci_as

(1 rows affected)
1>
```