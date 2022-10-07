# Working with extensions and foreign data wrappers<a name="Appendix.PostgreSQL.CommonDBATasks"></a>

To extend the functionality to your Aurora PostgreSQL\-Compatible Edition DB cluster, you can install and use various PostgreSQL *extensions*\. For example, if your use case calls for intensive data entry across very large tables, you can install the `[pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html)` extension to partition your data and thus spread the workload\.

In some cases, rather than installing an extension, you might add a specific *module* to the list of `shared_preload_libraries` in your Aurora PostgreSQL DB cluster's custom DB cluster parameter group\. Typically, the default DB cluster parameter group loads only the `pg_stat_statements`, but several other modules are available to add to the list\. For example, you can add scheduling capability by adding the `pg_cron` module, as detailed in [Scheduling maintenance with the PostgreSQL pg\_cron extension](PostgreSQL_pg_cron.md)\. As another example, you can log query execution plans by loading the `auto_explain` module\. To learn more, see [Logging execution plans of queries](https://aws.amazon.com/premiumsupport/knowledge-center/rds-postgresql-tune-query-performance/#) in the AWS knowledge center\. 

A category of extension that provides access to external data is generally known as a *foreign data wrapper* \(FDW\)\. As one example, the `oracle_fdw` extension allows your Aurora PostgreSQL DB cluster to work with Oracle databases\. 

Following, you can find information about setting up and using some of the extensions, modules, and FDWs available for Aurora PostgreSQL\. For simplicity's sake, these are all referred to as "extensions\." You can find listings of extensions that you can use with the currently available Aurora PostgreSQL versions, see [Extension versions for Aurora PostgreSQL\-Compatible Edition](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Extensions.html) in the *Release Notes for Aurora PostgreSQL*\.
+ [Managing large objects with the lo module](PostgreSQL_large_objects_lo_extension.md)
+ [Managing spatial data with the PostGIS extension](Appendix.PostgreSQL.CommonDBATasks.PostGIS.md)
+ [Scheduling maintenance with the PostgreSQL pg\_cron extension](PostgreSQL_pg_cron.md)
+ [Managing PostgreSQL partitions with the pg\_partman extension](PostgreSQL_Partitions.md)
+ [Working with Oracle databases by using the oracle\_fdw extension](Appendix.PostgreSQL.CommonDBATasks.Extensions.foreign-data-wrappers.md#postgresql-oracle-fdw)
+ [Working with SQL Server databases by using the tds\_fdw extension](Appendix.PostgreSQL.CommonDBATasks.Extensions.foreign-data-wrappers.md#postgresql-tds-fdw)