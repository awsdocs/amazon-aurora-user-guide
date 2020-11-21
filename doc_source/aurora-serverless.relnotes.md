# Aurora Serverless versions<a name="aurora-serverless.relnotes"></a>

Aurora Serverless is available in certain AWS Regions and for specific Aurora MySQL and Aurora PostgreSQL versions only\. For the current list of AWS Regions that support Aurora Serverless and the specific Aurora MySQL and Aurora PostgreSQL versions available in each Region, see [Aurora Serverless](Concepts.AuroraFeaturesRegionsDBEngines.grids.md#Concepts.Aurora_Fea_Regions_DB-eng.Feature.Serverless)\.

Aurora Serverless doesn't have its own version number\. Instead, Aurora Serverless uses the name and version number of the associated Aurora database engine for naming purposes\. For example:
+ Aurora MySQL Serverless
+ Aurora PostgreSQL Serverless

When minor releases of the database engines become available for Aurora Serverless, they are applied automatically in the various AWS Regions where Aurora Serverless is available, for the respective database engine\. In other words, you don't need to upgrade your Aurora Serverless DB cluster to get a new minor release for your cluster's DB engine when it's available for Aurora Serverless\.

## Aurora MySQL Serverless<a name="aurora-serverless.relnotes.aurmysql.serverless"></a>

If you want to use Aurora with MySQL compatibility for your Aurora Serverless DB cluster, you can choose between Aurora MySQL 5\.6\-compatible or Aurora MySQL 5\.7\-compatible versions\. These two editions of Aurora MySQL differ significantly\. We recommend that you compare their differences before creating your Aurora Serverless DB cluster so that you make the right choice for your use case\. You can review the release notes for Aurora MySQL Serverless 5\.6 and Aurora MySQL Serverless 5\.7 to learn about the respective enhancements, bug fixes, and the like for each of these DB engines\. For more information, see [Database engine updates for Aurora MySQL Serverless clusters](AuroraMySQL.Updates.ServerlessUpdates.md)\.

## Aurora PostgreSQL Serverless<a name="aurora-serverless.relnotes.aurpostgres.serverless"></a>

If you want to use Aurora PostgreSQL for your Aurora Serverless DB cluster, you have a single version available to use\. Minor releases for Aurora with PostgreSQL compatibility include only changes that are backward\-compatible\. Your Aurora Serverless DB cluster is transparently upgraded when a Aurora PostgreSQL minor release becomes available for Aurora Serverless in your Region\.

For example, the minor version Aurora PostgreSQL 10\.12 release was transparently applied to all Aurora Serverless DB clusters running the prior Aurora PostgreSQL version\. For more information about the Aurora PostgreSQL version 10\.12 update, see [Version 2\.5, compatible with PostgreSQL 10\.12](AuroraPostgreSQL.Updates.20180305.md#AuroraPostgreSQL.Updates.20180305.25)\. 