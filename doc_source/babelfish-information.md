# Querying Babelfish to find Babelfish details<a name="babelfish-information"></a>

You can query Babelfish to find details about the Babelfish version, the Aurora PostgreSQL version, and the compatible Microsoft SQL Server version\.

Run these queries while connected to the TDS port\.

To identify the Babelfish version, run the following query:

```
SELECT CAST(serverproperty('babelfishversion') AS VARCHAR)
```

The query returns results similar to the following:

```
1.0.0
```

To identify the version of the Aurora PostgreSQL DB cluster, run the following query:

```
SELECT aurora_version() AS aurora_version 
```

The query returns results similar to the following:

```
13.4.0
```

To identify the compatible Microsoft SQL Server version, run the following query:

```
SELECT @@VERSION AS version
```

The query returns results similar to the following:

```
Babelfish for Aurora Postgres with SQL Server Compatibility - 12.0.2000.8
Sep 28 2021 14:37:26
Copyright (c) Amazon Web Services
PostgreSQL 13.4 on x86_64-pc-linux-gnu
```

In addition, the following query returns `1` when executed on Babelfish, and `NULL` when executed on Microsoft SQL Server:

```
SELECT CAST(serverproperty('babelfish') AS VARCHAR) AS runs_on_babelfish
```

To query `babelfish_db` the same way using the PostgreSQL port, connect to the `babelfish_db` and run the following\.

```
\x
SELECT
aurora_version() as aurora_version,
version() as postgresql_version,
sys.version() as Babelfish_compatibillity,
sys.SERVERPROPERTY('BabelfishVersion') as Babelfish_Version
```

The query returns the following\.

```
babelfish_db=> \x
Expanded display is on.
babelfish_db=> SELECT
babelfish_db-> aurora_version() as aurora_version,
babelfish_db-> version() as postgresql_version,
babelfish_db-> sys.version() as Babelfish_compatibility,
babelfish_db-> sys.SERVERPROPERTY('BabelfishVersion') as Babelfish_Version ;
-[ RECORD 1 ]
aurora_version          | 13.4.0
postgresql_version      | PostgreSQL 13.4 on aarch64-unknown-linux-gnu, compiled by aarch64-unknown-linux-gnu-gcc (GCC) 7.4.0, 64-bit
babelfish_compatibility | Babelfish for Aurora Postgres with SQL Server Compatibility - 12.0.2000.8                                  +
                        | Oct 13 2021 17:34:47                                                                                       +
                        | Copyright (c) Amazon Web Services                                                                          +
                        | PostgreSQL 13.4 on aarch64-unknown-linux-gnu
babelfish_version       | 1.0.0
```