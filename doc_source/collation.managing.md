# Managing collations<a name="collation.managing"></a>

The ICU library provides collation version tracking to ensure that indexes that depend on collations can be reindexed when a new version of ICU becomes available\. You can use the following query to identify all collations in the current database that need to be refreshed and the objects that depend on them\.

```
SELECT pg_describe_object(refclassid, refobjid, refobjsubid) AS "Collation", pg_describe_object(classid, objid, objsubid) AS "Object" FROM pg_depend d JOIN pg_collation c ON refclassid = 'pg_collation'::regclass AND refobjid = c.oid WHERE c.collversion < > pg_collation_actual_version(c.oid) ORDER BY 1, 2;
```

Predefined collations are stored in the `sys.fn_helpcollations` table\. You can use the following command to display information about a collation \(such as its lcid, style, and collate flags\)\. To retrieve the list, connect a psql client to the Aurora PostgreSQL port \(by default, `5432`\) and enter the following\.

```
postgres=# set search_path = public, pg_temp, sys;
			SET
			postgres=# \dO
```

Connect to the T\-SQL port \(by default `1433`\) and enter the following\.

```
SELECT * FROM fn_helpcollation()
```