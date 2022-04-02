# Working with extensions and foreign data wrappers<a name="Appendix.PostgreSQL.CommonDBATasks"></a>

To add some types of functionality to your Aurora PostgreSQL\-Compatible Edition DB cluster, you can install and use various PostgreSQL extensions\. For example, if your use case calls for intensive data entry across very large tables, you can install the `[pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html)` extension to partition your data and thus spread the workload\. PostgreSQL extensions supported by Aurora PostgreSQL include `pg_partman` and `pg_cron`\. For more information, see the following topics:
+ [Managing PostgreSQL partitions with the pg\_partman extension](PostgreSQL_Partitions.md)
+ [Scheduling maintenance with the PostgreSQL pg\_cron extension](PostgreSQL_pg_cron.md)

An extension that provides access to external data is generally known as a *foreign data wrapper* \(FDW\)\. For example, the `oracle_fdw` extension allows your Aurora PostgreSQL DB cluster to work with Oracle databases\. Aurora PostgreSQL also supports the `tds_fdw` extension\. For more information, see the following:
+ [Working with Oracle databases by using the oracle\_fdw extension](Appendix.PostgreSQL.CommonDBATasks.Extensions.foreign-data-wrappers.md#postgresql-oracle-fdw)
+ [Working with SQL Server databases by using the tds\_fdw extension](Appendix.PostgreSQL.CommonDBATasks.Extensions.foreign-data-wrappers.md#postgresql-tds-fdw)