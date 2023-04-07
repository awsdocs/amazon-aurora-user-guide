# Identifying your version of Babelfish<a name="babelfish-information-identify-version"></a>

You can query Babelfish to find details about the Babelfish version, the Aurora PostgreSQL version, and the compatible Microsoft SQL Server version\. You can use the TDS port or the PostgreSQL port\. 
+ [To use the TDS port to query for version information](#apg-version-info-tds)
+ [To use the PostgreSQL port to query for version information](#apg-version-info-psql)

**To use the TDS port to query for version information**

1. Use `sqlcmd` or `ssms` to connect to the endpoint for your Babelfish DB cluster\.

   ```
   sqlcmd -S bfish_db.cluster-123456789012.aws-region.rds.amazonaws.com,1433 -U
       login-id -P password -d db_name
   ```

1. To identify the Babelfish version, run the following query:

   ```
   1> SELECT CAST(serverproperty('babelfishversion') AS VARCHAR)
   2> GO
   ```

   The query returns results similar to the following:

   ```
   serverproperty
   ------------------------------
   3.1.0
   
   (1 rows affected)
   ```

1. To identify the version of the Aurora PostgreSQL DB cluster, run the following query:

   ```
   1> SELECT aurora_version() AS aurora_version
   2> GO
   ```

   The query returns results similar to the following:

   ```
   aurora_version                                                                                                                                                                                                                                  
   -------------------------------------------------
   15.2.1
   
   (1 rows affected)
   ```

1. To identify the compatible Microsoft SQL Server version, run the following query:

   ```
   1> SELECT @@VERSION AS version
   2> GO
   ```

   The query returns results similar to the following:

   ```
   Babelfish for Aurora PostgreSQL with SQL Server Compatibility - 12.0.2000.8
   Mar 23 2023 07:55:24
   Copyright (c) Amazon Web Services
   PostgreSQL 15.2 on aarch64-unknown-linux-gnu (Babelfish 3.1.0)
   
   (1 rows affected)
   ```

As an example that shows one minor difference between Babelfish and Microsoft SQL Server, you can run the following query\. On Babelfish, the query returns `1`, while on Microsoft SQL Server, the query returns `NULL`\. 

```
SELECT CAST(serverproperty('babelfish') AS VARCHAR) AS runs_on_babelfish
```

You can also use the PostgreSQL port to obtain version information, as shown in the following procedure\.

**To use the PostgreSQL port to query for version information**

1. Use `psql` or `pgAdmin` to connect to the endpoint for your Babelfish DB cluster\. 

   ```
   psql host=bfish_db.cluster-123456789012.aws-region.rds.amazonaws.com
        port=5432 dbname=babelfish_db user=sa
   ```

1. Turn on the extended feature \(`\x`\) of `psql` for more readable output\.

   ```
   babelfish_db=> \x
   babelfish_db=> SELECT
   babelfish_db=> aurora_version() AS aurora_version,
   babelfish_db=> version() AS postgresql_version,
   babelfish_db=> sys.version() AS Babelfish_compatibility,
   babelfish_db=> sys.SERVERPROPERTY('BabelfishVersion') AS Babelfish_Version;
   ```

   The query returns output similar to the following:

   ```
   -[ RECORD 1 ]-----------+-----------------------------------------------------------------------------------------------
   aurora_version          | 15.2.1
   postgresql_version      | PostgreSQL 15.2 on aarch64-unknown-linux-gnu, compiled by x86_64-pc-linux-gnu-gcc (GCC) 9.5.0, 64-bit
   babelfish_compatibility | Babelfish for Aurora Postgres with SQL Server Compatibility - 12.0.2000.8                     +
                           | Mar 23 2023 07:55:24                                                                          +
                           | Copyright (c) Amazon Web Services                                                             +
                           | PostgreSQL 15.2 on aarch64-unknown-linux-gnu (Babelfish 3.1.0)
   babelfish_version       | 3.1.0
   ```