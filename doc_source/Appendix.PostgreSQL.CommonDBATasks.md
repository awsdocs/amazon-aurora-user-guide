# Working with extensions and foreign data wrappers<a name="Appendix.PostgreSQL.CommonDBATasks"></a>

To add some types of functionality to your Aurora PostgreSQL\-Compatible Edition DB cluster, you can install and use various PostgreSQL extensions\. For example, if your use case calls for intensive data entry across very large tables, you can install the `[pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html)` extension to partition your data and thus spread the workload\. An extension that provides access to external data is generally known as a *foreign data wrapper* \(FDW\)\. As one example, the `oracle_fdw` extension allows your Aurora PostgreSQL DB cluster to work with Oracle databases\. 

Following, you can find information about setting up and using some of the extensions and FDWs available for Aurora PostgreSQL\. For listings of the extensions and FDWs that you can use with currently available Aurora PostgreSQL versions, see [Extension versions for Aurora PostgreSQL\-Compatible Edition](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Extensions.html) in the *Release Notes for Aurora PostgreSQL*\.
+ [Managing large objects with the lo module](PostgreSQL_large_objects_lo_extension.md)
+ [Managing spatial data with the PostGIS extension](Appendix.PostgreSQL.CommonDBATasks.PostGIS.md)
+ [Scheduling maintenance with the PostgreSQL pg\_cron extension](PostgreSQL_pg_cron.md)
+ [Managing PostgreSQL partitions with the pg\_partman extension](PostgreSQL_Partitions.md)
+ [Working with Oracle databases by using the oracle\_fdw extension](Appendix.PostgreSQL.CommonDBATasks.Extensions.foreign-data-wrappers.md#postgresql-oracle-fdw)
+ [Working with SQL Server databases by using the tds\_fdw extension](Appendix.PostgreSQL.CommonDBATasks.Extensions.foreign-data-wrappers.md#postgresql-tds-fdw)