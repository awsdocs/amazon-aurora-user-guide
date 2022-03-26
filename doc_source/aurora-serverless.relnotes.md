# Aurora Serverless v1 and Aurora database engine versions<a name="aurora-serverless.relnotes"></a>

Aurora Serverless v1 is available in certain AWS Regions and for specific Aurora MySQL and Aurora PostgreSQL versions only\. For the current list of AWS Regions that support Aurora Serverless v1 and the specific Aurora MySQL and Aurora PostgreSQL versions available in each Region, see [Aurora Serverless v1](Concepts.AuroraFeaturesRegionsDBEngines.grids.md#Concepts.Aurora_Fea_Regions_DB-eng.Feature.Serverless)\.

Aurora Serverless v1 uses its associated Aurora database engine to identify specific supported releases for each database engine supported, as follows:
+ Aurora MySQL Serverless
+ Aurora PostgreSQL Serverless

When minor releases of the database engines become available for Aurora Serverless v1, they are applied automatically in the various AWS Regions where Aurora Serverless v1 is available\. In other words, you don't need to upgrade your Aurora Serverless v1 DB cluster to get a new minor release for your cluster's DB engine when it's available for Aurora Serverless v1\.

## Aurora MySQL Serverless<a name="aurora-serverless.relnotes.aurmysql.serverless"></a>

If you want to use Aurora MySQL\-Compatible Edition for your Aurora Serverless v1 DB cluster, you can choose between Aurora MySQL 5\.6\-compatible or Aurora MySQL 5\.7\-compatible versions\. These two editions of Aurora MySQL differ significantly\. We recommend that you compare their differences before creating your Aurora Serverless v1 DB cluster so that you make the right choice for your use case\. To learn about enhancements and bug fixes for Aurora MySQL Serverless 5\.6 and 5\.7, see [ Database engine updates for Aurora Serverless v1 clusters](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraMySQLReleaseNotes/AuroraMySQL.Updates.ServerlessUpdates.html) in the *Release Notes for Aurora MySQL*\.

## Aurora PostgreSQL Serverless<a name="aurora-serverless.relnotes.aurpostgres.serverless"></a>

If you want to use Aurora PostgreSQL for your Aurora Serverless v1 DB cluster, you have a single version available to use\. Minor releases for Aurora PostgreSQL\-Compatible Edition include only changes that are backward\-compatible\. Your Aurora Serverless v1 DB cluster is transparently upgraded when an Aurora PostgreSQL minor release becomes available for Aurora Serverless v1 in your Region\.

For example, the minor version Aurora PostgreSQL 10\.14 release was transparently applied to all Aurora Serverless v1 DB clusters running the prior Aurora PostgreSQL version\. For more information about the Aurora PostgreSQL version 10\.14 update, see [ PostgreSQL 10\.14, Aurora PostgreSQL release 2\.7 ](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html#AuroraPostgreSQL.Updates.20180305.27) in the *Release Notes for Aurora PostgreSQL*\. 