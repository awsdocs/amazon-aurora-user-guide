# Supported features in Amazon Aurora by AWS Region and Aurora DB engine<a name="Concepts.AuroraFeaturesRegionsDBEngines.grids"></a>

Aurora MySQL\- and PostgreSQL\-compatible database engines support several Amazon Aurora and Amazon RDS features and options\. The support varies across specific versions of each database engine, and across AWS Regions\. You can use the tables in this section to identify Aurora database engine version support and availability in a given AWS Region for the following features: 

**Topics**
+ [Backtracking in Aurora](#Concepts.Aurora_Fea_Regions_DB-eng.Feature.Backtrack)
+ [Aurora global databases](#Concepts.Aurora_Fea_Regions_DB-eng.Feature.GlobalDatabase)
+ [Aurora machine learning](#Concepts.Aurora_Fea_Regions_DB-eng.Feature.Aurora_ML)
+ [Aurora parallel queries](#Concepts.Aurora_Fea_Regions_DB-eng.Feature.ParallelQuery)
+ [Amazon RDS Proxy](#Concepts.Aurora_Fea_Regions_DB-eng.Feature.RDS_Proxy)
+ [Aurora Serverless v1](#Concepts.Aurora_Fea_Regions_DB-eng.Feature.Serverless)
+ [Data API for Aurora Serverless](#Concepts.Aurora_Fea_Regions_DB-eng.Feature.Data_API)

Some of these features are Aurora\-only capabilities\. For example, Aurora Serverless, Aurora global databases, and support for integration with AWS machine learning services aren't supported by Amazon RDS\. Other features, such as Amazon RDS Proxy, are supported by both Amazon Aurora and Amazon RDS\.  

The tables use the following patterns to specify version numbers and level of support: 
+ **Version x\.y** – The specific version alone is supported\.
+ **Version x\.y and later** – The version and all minor versions are also supported\. For example, "version 10\.11 and later" means that versions 10\.11, 10\.11\.1, and 10\.12 are also supported\. 
+ **\-** – The feature is not currently available for that particular Aurora feature for the given Aurora database engine, or in that specific AWS Region\.

## Backtracking in Aurora<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.Backtrack"></a>

By using backtracking in Aurora, you return the state of an Aurora cluster to a specific point in time, without restoring data from a backup\. It completes within seconds, even for large databases\. For more information, see [Backtracking an Aurora DB cluster](AuroraMySQL.Managing.Backtrack.md)\.

Aurora backtracking is available for Aurora MySQL only\. It's not available for Aurora PostgreSQL\.  


| Region | Aurora MySQL 5\.6 | Aurora MySQL 5\.7 | 
| --- | --- | --- | 
| US East \(Ohio\) | Version 5\.6\.10a | Version 2\.06 and later | 
| US East \(N\. Virginia\) | Version 5\.6\.10a | Version 2\.06 and later | 
| US West \(N\. California\) | Version 5\.6\.10a | Version 2\.06 and later | 
| US West \(Oregon\) | Version 5\.6\.10a | Version 2\.06 and later | 
| Africa \(Cape Town\) | \- | \- | 
| Asia Pacific \(Hong Kong\) | \- | \- | 
| Asia Pacific \(Mumbai\) | Version 5\.6\.10a | Version 2\.06 and later | 
| Asia Pacific \(Osaka\) | Version 5\.6\.10a; version 1\.22 and later | Version 2\.07\.3 and later | 
| Asia Pacific \(Seoul\) | Version 5\.6\.10a | Version 2\.06 and later | 
| Asia Pacific \(Singapore\) | Version 5\.6\.10a | Version 2\.06 and later | 
| Asia Pacific \(Sydney\) | Version 5\.6\.10a | Version 2\.06 and later | 
| Asia Pacific \(Tokyo\) | Version 5\.6\.10a | Version 2\.06 and later | 
| Canada \(Central\) | Version 5\.6\.10a | Version 2\.06 and later | 
| China \(Beijing\) | Version 5\.6\.10a | Version 2\.06 and later | 
| China \(Ningxia\) | \- | \- | 
| Europe \(Frankfurt\) | Version 5\.6\.10a | Version 2\.06 and later | 
| Europe \(Ireland\) | Version 5\.6\.10a | Version 2\.06 and later | 
| Europe \(London\) | Version 5\.6\.10a | Version 2\.06 and later | 
| Europe \(Milan\) | \- | \- | 
| Europe \(Paris\) | Version 5\.6\.10a | Version 2\.06 and later | 
| Europe \(Stockholm\) | \- | \- | 
| Middle East \(Bahrain\) | \- | \- | 
| South America \(São Paulo\) | \- | \- | 
| AWS GovCloud \(US\-East\) | \- | \- | 
| AWS GovCloud \(US\-West\) | \- | \- | 

## Aurora global databases<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.GlobalDatabase"></a>

An *Aurora global database* is a single database that spans multiple AWS Regions, enabling low\-latency global reads and disaster recovery from any Region\-wide outage\. It provides built\-in fault tolerance for your deployment because the DB instance relies not on a single AWS Region, but upon multiple Regions and different Availability Zones\.

Support for this feature varies by Aurora database engine and version\. The following table shows the Regions and Aurora database versions that support this feature\. For more information, see [Using Amazon Aurora global databases](aurora-global-database.md)\.


| Region | Aurora MySQL 5\.6 | Aurora MySQL 5\.7 | Aurora PostgreSQL 10 | Aurora PostgreSQL 11 | Aurora PostgreSQL 12 | 
| --- | --- | --- | --- | --- | --- | 
| US East \(Ohio\) | Version 5\.6\.10a; version 1\.22 and later | Version 2\.07 and later | Version 10\.11 and later | Version 11\.7 and later | Version 12\.4 and later | 
| US East \(N\. Virginia\) | Version 5\.6\.10a; version 1\.22 and later | Version 2\.07 and later | Version 10\.11 and later | Version 11\.7 and later | Version 12\.4 and later | 
| US West \(N\. California\) | Version 5\.6\.10a; version 1\.22 and later | Version 2\.07 and later | Version 10\.11 and later | Version 11\.7 and later | Version 12\.4 and later | 
| US West \(Oregon\) | Version 5\.6\.10a; version 1\.22 and later | Version 2\.07 and later | Version 10\.11 and later | Version 11\.7 and later | Version 12\.4 and later | 
| Africa \(Cape Town\) | \- | \- | \- | \- | \- | 
| Asia Pacific \(Hong Kong\) | \- | \- | \- | \- | \- | 
| Asia Pacific \(Mumbai\) | Version 5\.6\.10a; Version 1\.22 and later | Version 2\.07 and later | Version 10\.11 and later | Version 11\.7 and later | Version 12\.4 and later | 
| Asia Pacific \(Osaka\) | Version 1\.22\.3 and later | Version 2\.07\.3 and later | Version 10\.12 and later | Version 11\.7 and later | Version 12\.4 and later | 
| Asia Pacific \(Seoul\) | Version 5\.6\.10a; Version 1\.22 and later | Version 2\.07 and later | Version 10\.11 and later | Version 11\.7 and later | Version 12\.4 and later | 
| Asia Pacific \(Singapore\) | Version 5\.6\.10a; Version 1\.22 and later | Version 2\.07 and later | Version 10\.11 and later | Version 11\.7 and later | Version 12\.4 and later | 
| Asia Pacific \(Sydney\) | Version 5\.6\.10a; Version 1\.22 and later | Version 2\.07 and later | Version 10\.11 and later | Version 11\.7 and later | Version 12\.4 and later | 
| Asia Pacific \(Tokyo\) | Version 5\.6\.10a; Version 1\.22 and later | Version 2\.07 and later | Version 10\.11 and later | Version 11\.7 and later | Version 12\.4 and later | 
| Canada \(Central\) | Version 5\.6\.10a; Version 1\.22 and later | Version 2\.07 and later | Version 10\.11 and later | Version 11\.7 and later | Version 12\.4 and later | 
| China \(Beijing\) | \- | \- | \- | \- | \- | 
| China \(Ningxia\) | \- | \- | \- | \- | \- | 
| Europe \(Frankfurt\) | Version 5\.6\.10a; Version 1\.22 and later | Version 2\.07 and later | Version 10\.11 and later | Version 11\.7 and later | Version 12\.4 and later | 
| Europe \(Ireland\) | Version 5\.6\.10a; Version 1\.22 and later | Version 2\.07 and later | Version 10\.11 and later | Version 11\.7 and later | Version 12\.4 and later | 
| Europe \(London\) | Version 5\.6\.10a; Version 1\.22 and later | Version 2\.07 and later | Version 10\.11 and later | Version 11\.7 and later | Version 12\.4 and later | 
| Europe \(Milan\) | \- | \- | \- | \- | \- | 
| Europe \(Paris\) | Version 5\.6\.10a; Version 1\.22 and later | Version 2\.07 and later | Version 10\.11 and later | Version 11\.7 and later | Version 12\.4 and later | 
| Europe \(Stockholm\) | Version 1\.22\.2 and later | Version 2\.07\.0 and later | Version 10\.11 and later | Version 11\.7 and later | Version 12\.4 and later | 
| Middle East \(Bahrain\) | \- | \- | \- | \- | \- | 
| South America \(São Paulo\) | Version 1\.22\.2 and later | Version 2\.07\.1 and later | Version 10\.11 and later | Version 11\.7 and later | Version 12\.4 and later | 
| AWS GovCloud \(US\-East\) | \- | \- | \- | \- | \- | 
| AWS GovCloud \(US\-West\) | \- | \- | \- | \- | \- | 

## Aurora machine learning<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.Aurora_ML"></a>

Aurora machine learning provides simple, optimized, and secure integration between Aurora and AWS machine learning services without having to build custom integrations or move data around\. Aurora exposes ML models as SQL functions, so you don't need to learn new programming languages or tools\. Instead, you use standard SQL to build applications that call ML models, pass data to them, and return predictions as query results\. For more information, see [Using machine learning \(ML\) capabilities with Amazon Aurora](aurora-ml.md)\.


| Region | Aurora MySQL 5\.6 | Aurora MySQL 5\.7 | Aurora PostgreSQL 10 | Aurora PostgreSQL 11 | Aurora PostgreSQL 12 | 
| --- | --- | --- | --- | --- | --- | 
| US East \(Ohio\) | \- | Version 2\.07 and later | Version 10\.11 | Version 11\.6 and later | Version 12\.4 and later | 
| US East \(N\. Virginia\) | \- | Version 2\.07 and later | Version 10\.11 | Version 11\.6 and later | Version 12\.4 and later | 
| US West \(N\. California\) | \- | Version 2\.07 and later \(SageMaker only\) | Version 10\.11 \(SageMaker only\) | Version 11\.6 and later \(SageMaker only\) | Version 12\.4 and later \(SageMaker only\) | 
| US West \(Oregon\) | \- | Version 2\.07 and later | Version 10\.11 | Version 11\.6 and later | Version 12\.4 and later | 
| Africa \(Cape Town\) | \- | \- | \- | \- | \- | 
| Asia Pacific \(Hong Kong\) | \- | Version 2\.07 and later \(SageMaker only\) | Version 10\.11 | Version 11\.6 and later | Version 12\.4 and later | 
| Asia Pacific \(Mumbai\) | \- | Version 2\.07 and later | Version 10\.11 | Version 11\.6 and later | Version 12\.4 and later | 
| Asia Pacific \(Osaka\) | \- | Version 2\.07\.3 and later | Version 10\.11 | Version 11\.6 and later | Version 12\.4 and later | 
| Asia Pacific \(Seoul\) | \- | Version 2\.07 and later | Version 10\.11 | Version 11\.6 and later | Version 12\.4 and later | 
| Asia Pacific \(Singapore\) | \- | Version 2\.07 and later | Version 10\.11 | Version 11\.6 and later | Version 12\.4 and later | 
| Asia Pacific \(Sydney\) | \- | Version 2\.07 and later | Version 10\.11 | Version 11\.6 and later | Version 12\.4 and later | 
| Asia Pacific \(Tokyo\) | \- | Version 2\.07 and later | Version 10\.11 | Version 11\.6 and later | Version 12\.4 and later | 
| Canada \(Central\) | \- | Version 2\.07 and later | Version 10\.11 | Version 11\.6 and later | Version 12\.4 and later | 
| China \(Beijing\) | \- | Version 2\.07 and later \(SageMaker only\) | Version 10\.11 \(SageMaker only\) | Version 11\.6 and later \(SageMaker only\) | Version 12\.4 and later | 
| China \(Ningxia\) | \- | Version 2\.07 and later \(SageMaker only\) | Version 10\.11 \(SageMaker only\) | Version 11\.6 and later \(SageMaker only\) | Version 12\.4 and later \(SageMaker only\) | 
| Europe \(Frankfurt\) | \- | Version 2\.07 and later | Version 10\.11 | Version 11\.6 and later | Version 12\.4 and later | 
| Europe \(Ireland\) | \- | Version 2\.07 and later | Version 10\.11 | Version 11\.6 and later | Version 12\.4 and later | 
| Europe \(London\) | \- | Version 2\.07 and later | Version 10\.11 | Version 11\.6 and later | Version 12\.4 and later | 
| Europe \(Milan\) | \- | \- | \- | \- | \- | 
| Europe \(Paris\) | \- | Version 2\.07 and later \(SageMaker only\) | Version 10\.11 \(SageMaker only\) | Version 11\.6 and later \(SageMaker only\) | Version 12\.4 and later | 
| Europe \(Stockholm\) | \- | Version 2\.07 and later \(SageMaker only\) | Version 10\.11 \(SageMaker only\) | Version 11\.6 and later \(SageMaker only\) | Version 12\.4 and later \(SageMaker only\) | 
| Middle East \(Bahrain\) | \- | Version 2\.07 and later \(SageMaker only\) | Version 10\.11 \(SageMaker only\) | Version 11\.6 and later \(SageMaker only\) | Version 12\.4 and later \(SageMaker only\) | 
| South America \(São Paulo\) | \- | Version 2\.07 and later \(SageMaker only\) | Version 10\.11 \(SageMaker only\) | Version 11\.6 and later \(SageMaker only\) | Version 12\.4 and later \(SageMaker only\) | 
| AWS GovCloud \(US\-East\) | \- | Version 2\.07 and later | Version 10\.11 | Version 11\.6 and later | Version 12\.4 and later | 
| AWS GovCloud \(US\-West\) | \- | Version 2\.07 and later \(SageMaker only\) | Version 10\.11 \(SageMaker only\) | Version 11\.6 and later \(SageMaker only\) | Version 12\.4 and later \(SageMaker only\) | 

## Aurora parallel queries<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.ParallelQuery"></a>

Aurora parallel queries can speed up your queries by up to two orders of magnitude, while maintaining high throughput for your core transactional workload\. Using the unique Aurora architecture, parallel queries can push down and parallelize query processing across thousands of CPUs in the Aurora storage layer\. By offloading analytical query processing to the Aurora storage layer, parallel queries reduce network, CPU, and buffer pool contention for transactional workloads\. For more information, see [Working with parallel query for Amazon Aurora MySQL](aurora-mysql-parallel-query.md)\. To learn more about Aurora MySQL versions available for parallel queries and any steps you might need to take based on that version to support parallel queries, see [Planning for a parallel query cluster](aurora-mysql-parallel-query.md#aurora-mysql-parallel-query-planning)\.

Aurora parallel queries are available for Aurora MySQL only\. However, PostgreSQL has its own parallel query feature that is available on Amazon RDS\. The capability is enabled by default when a new PostgreSQL instance is created \(versions 10\.1 and later\)\. For more information, see [PostgreSQL on Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html)\. 


| Region | Aurora MySQL 5\.6 | Aurora MySQL 5\.7 | 
| --- | --- | --- | 
| US East \(Ohio\) | Version 5\.6\.10a; version 1\.19, 1\.20, and 1\.23 | Version 2\.09 | 
| US East \(N\. Virginia\) | Version 5\.6\.10a; version 1\.19, 1\.20, 1\.22, and 1\.23 | Version 2\.09 | 
| US West \(N\. California\) | Version 1\.23 | Version 2\.09 | 
| US West \(Oregon\) | Version 5\.6\.10a; version 1\.19, 1\.20, 1\.22, and 1\.23 | Version 2\.09 | 
| Africa \(Cape Town\) | Version 1\.23 | Version 2\.09 | 
| Asia Pacific \(Hong Kong\) | Version 1\.23 | Version 2\.09 | 
| Asia Pacific \(Mumbai\) | Version 1\.23 | Version 2\.09 | 
| Asia Pacific \(Osaka\) | Version 1\.23\.1 and later | Version 2\.09\.1 and later | 
| Asia Pacific \(Seoul\) | Version 1\.23 | Version 2\.09 | 
| Asia Pacific \(Singapore\) | Version 1\.23 | Version 2\.09 | 
| Asia Pacific \(Sydney\) | Version 1\.23 | Version 2\.09 | 
| Asia Pacific \(Tokyo\) | Version 5\.6\.10a; version 1\.19, 1\.20, 1\.22, and 1\.23 | Version 2\.09 | 
| Canada \(Central\) | Version 5\.6\.10a; version 1\.23 | Version 2\.09 | 
| China \(Beijing\) | \- | \- | 
| China \(Ningxia\) | \- | \- | 
| Europe \(Frankfurt\) | Version 1\.23 | Version 2\.09 | 
| Europe \(Ireland\) | Version 5\.6\.10a; version 1\.19, 1\.20, 1\.22, and 1\.23 | Version 2\.09 | 
| Europe \(London\) | Version 1\.23 | Version 2\.09 | 
| Europe \(Milan\) | Version 1\.23 | Version 2\.09 | 
| Europe \(Paris\) | Version 1\.23 | Version 2\.09 | 
| Europe \(Stockholm\) | Version 1\.23 | Version 2\.09 | 
| Middle East \(Bahrain\) | Version 1\.23 | Version 2\.09 | 
| South America \(São Paulo\) | Version 1\.23 | Version 2\.09 | 
| AWS GovCloud \(US\-East\) | \- | \- | 
| AWS GovCloud \(US\-West\) | \- | \- | 

## Amazon RDS Proxy<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.RDS_Proxy"></a>

Amazon RDS Proxy is a fully managed, highly available database proxy that makes applications more scalable by pooling and sharing established database connections\. With RDS Proxy, failover times for Aurora are reduced by up to 66 percent\. For more information, see [Managing connections with Amazon RDS Proxy](rds-proxy.md)\. 


| Region | Aurora MySQL 5\.6 | Aurora MySQL 5\.7 | Aurora PostgreSQL 10 | Aurora PostgreSQL 11 | 
| --- | --- | --- | --- | --- | 
| US East \(Ohio\) | Version 5\.6\.10a; version 1\.22 and later | Version 2\.07 and later | Version 10\.11 and later | Version 11\.6 and later | 
| US East \(N\. Virginia\) | Version 5\.6\.10a; version 1\.22 and later | Version 2\.07 and later | Version 10\.11 and later | Version 11\.6 and later | 
| US West \(N\. California\) | Version 5\.6\.10a; version 1\.22 and later | Version 2\.07 and later | Version 10\.11 and later | Version 11\.6 and later | 
| US West \(Oregon\) | Version 5\.6\.10a; version 1\.22 and later | Version 2\.07 and later | Version 10\.11 and later | Version 11\.6 and later | 
| Africa \(Cape Town\) | \- | \- | \- | \- | 
| Asia Pacific \(Hong Kong\) | \- | \- | \- | \- | 
| Asia Pacific \(Mumbai\) | Version 5\.6\.10a; version 1\.22 and later | Version 2\.07 and later | Version 10\.11 and later | Version 11\.6 and later | 
| Asia Pacific \(Osaka\) | \- | \- | \- | \- | 
| Asia Pacific \(Seoul\) | Version 5\.6\.10a; version 1\.22 and later | Version 2\.07 and later | Version 10\.11 and later | Version 11\.6 and later | 
| Asia Pacific \(Singapore\) | Version 5\.6\.10a; version 1\.22 and later | Version 2\.07 and later | Version 10\.11 and later | Version 11\.6 and later | 
| Asia Pacific \(Sydney\) | Version 5\.6\.10a; version 1\.22 and later | Version 2\.07 and later | Version 10\.11 and later | Version 11\.6 and later | 
| Asia Pacific \(Tokyo\) | Version 5\.6\.10a; version 1\.22 and later | Version 2\.07 and later | Version 10\.11 and later | Version 11\.6 and later | 
| Canada \(Central\) | Version 5\.6\.10a; version 1\.22 and later | Version 2\.07 and later | Version 10\.11 and later | Version 11\.6 and later | 
| China \(Beijing\) | \- | \- | \- | \- | 
| China \(Ningxia\) | \- | \- | \- | \- | 
| Europe \(Frankfurt\) | Version 5\.6\.10a; version 1\.22 and later | Version 2\.07 and later | Version 10\.11 and later | Version 11\.6 and later | 
| Europe \(Ireland\) | Version 5\.6\.10a; version 1\.22 and later | Version 2\.07 and later | Version 10\.11 and later | Version 11\.6 and later | 
| Europe \(London\) | Version 5\.6\.10a; version 1\.22 and later | Version 2\.07 and later | Version 10\.11 and later | Version 11\.6 and later | 
| Europe \(Milan\) | \- | \- | \- | \- | 
| Europe \(Paris\) | \- | \- | \- | \- | 
| Europe \(Stockholm\) | \- | \- | \- | \- | 
| Middle East \(Bahrain\) | \- | \- | \- | \- | 
| South America \(São Paulo\) | \- | \- | \- | \- | 
| AWS GovCloud \(US\-East\) | \- | \- | \- | \- | 
| AWS GovCloud \(US\-West\) | \- | \- | \- | \- | 

## Aurora Serverless v1<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.Serverless"></a>

Aurora Serverless v1 is an on\-demand, auto\-scaling feature designed to be a cost\-effective approach to running intermittent or unpredictable workloads on Amazon Aurora\. It automatically starts up, shuts down, and scales capacity up or down, as needed by your applications\. For more information, see [Using Amazon Aurora Serverless v1](aurora-serverless.md)\.


| Region | Aurora MySQL 5\.6 | Aurora MySQL 5\.7 | Aurora PostgreSQL 10 | Aurora PostgreSQL 11 | 
| --- | --- | --- | --- | --- | 
| US East \(Ohio\) | Version 5\.6\.10a | Version 2\.07\.1 | Version 10\.12 | \- | 
| US East \(N\. Virginia\) | Version 5\.6\.10a | Version 2\.07\.1 | Version 10\.12 | \- | 
| US West \(N\. California\) | Version 5\.6\.10a | Version 2\.07\.1 | Version 10\.12 | \- | 
| US West \(Oregon\) | Version 5\.6\.10a | Version 2\.07\.1 | Version 10\.12 | \- | 
| Africa \(Cape Town\) | \- | \- | \- | \- | 
| Asia Pacific \(Hong Kong\) | \- | \- | \- | \- | 
| Asia Pacific \(Mumbai\) | Version 5\.6\.10a | Version 2\.07\.1 | Version 10\.12 | \- | 
| Asia Pacific \(Osaka\) | \- | \- | \- | \- | 
| Asia Pacific \(Seoul\) | Version 5\.6\.10a | Version 2\.07\.1 | Version 10\.12 | \- | 
| Asia Pacific \(Singapore\) | Version 5\.6\.10a | Version 2\.07\.1 | Version 10\.12 | \- | 
| Asia Pacific \(Sydney\) | Version 5\.6\.10a | Version 2\.07\.1 | Version 10\.12 | \- | 
| Asia Pacific \(Tokyo\) | Version 5\.6\.10a | Version 2\.07\.1 | Version 10\.12 | \- | 
| Canada \(Central\) | Version 5\.6\.10a | Version 2\.07\.1 | Version 10\.12 | \- | 
| China \(Beijing\) | \- | \- | \- | \- | 
| China \(Ningxia\) | \- | \- | \- | \- | 
| Europe \(Frankfurt\) | Version 5\.6\.10a | Version 2\.07\.1 | Version 10\.12 | \- | 
| Europe \(Ireland\) | Version 5\.6\.10a | Version 2\.07\.1 | Version 10\.12 | \- | 
| Europe \(London\) | Version 5\.6\.10a | Version 2\.07\.1 | Version 10\.12 | \- | 
| Europe \(Milan\) | \- | \- | \- | \- | 
| Europe \(Paris\) | Version 5\.6\.10a | Version 2\.07\.1 | Version 10\.12 | \- | 
| Europe \(Stockholm\) | \- | \- | \- | \- | 
| Middle East \(Bahrain\) | \- | \- | \- | \- | 
| South America \(São Paulo\) | \- | \- | \- | \- | 
| AWS GovCloud \(US\-East\) | \- | \- | \- | \- | 
| AWS GovCloud \(US\-West\) | \- | \- | \- | \- | 

## Data API for Aurora Serverless<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.Data_API"></a>

The Data API for Aurora Serverless provides a web\-services interface to an Aurora Serverless cluster\. Rather than managing database connections from client applications, you can run SQL commands against an HTTPS endpoint\. For more information, see [Using the Data API for Aurora Serverless](data-api.md)\. 


| Region | Aurora MySQL 5\.6 | Aurora MySQL 5\.7 | Aurora PostgreSQL 10 | Aurora PostgreSQL 11 | 
| --- | --- | --- | --- | --- | 
| US East \(Ohio\) | Version 5\.6\.10a | Version 2\.07\.1 | Version 10\.12 | \- | 
| US East \(N\. Virginia\) | Version 5\.6\.10a | Version 2\.07\.1 | Version 10\.12 | \- | 
| US West \(N\. California\) | Version 5\.6\.10a | Version 2\.07\.1 | Version 10\.12 | \- | 
| US West \(Oregon\) | Version 5\.6\.10a | Version 2\.07\.1 | Version 10\.12 | \- | 
| Africa \(Cape Town\) | \- | \- | \- | \- | 
| Asia Pacific \(Hong Kong\) | \- | \- | \- | \- | 
| Asia Pacific \(Mumbai\) | Version 5\.6\.10a | Version 2\.07\.1 | Version 10\.12 | \- | 
| Asia Pacific \(Osaka\) | \- | \- | \- | \- | 
| Asia Pacific \(Seoul\) | Version 5\.6\.10a | Version 2\.07\.1 | Version 10\.12 | \- | 
| Asia Pacific \(Singapore\) | Version 5\.6\.10a | Version 2\.07\.1 | Version 10\.12 | \- | 
| Asia Pacific \(Sydney\) | Version 5\.6\.10a | Version 2\.07\.1 | Version 10\.12 | \- | 
| Asia Pacific \(Tokyo\) | Version 5\.6\.10a | Version 2\.07\.1 | Version 10\.12 | \- | 
| Canada \(Central\) | Version 5\.6\.10a | Version 2\.07\.1 | Version 10\.12 | \- | 
| China \(Beijing\) | \- | \- | \- | \- | 
| China \(Ningxia\) | \- | \- | \- | \- | 
| Europe \(Frankfurt\) | Version 5\.6\.10a | Version 2\.07\.1 | Version 10\.12 | \- | 
| Europe \(Ireland\) | Version 5\.6\.10a | Version 2\.07\.1 | Version 10\.12 | \- | 
| Europe \(London\) | Version 5\.6\.10a | Version 2\.07\.1 | Version 10\.12 | \- | 
| Europe \(Milan\) | \- | \- | \- | \- | 
| Europe \(Paris\) | Version 5\.6\.10a | Version 2\.07\.1 | Version 10\.12 | \- | 
| Europe \(Stockholm\) | \- | \- | \- | \- | 
| Middle East \(Bahrain\) | \- | \- | \- | \- | 
| South America \(São Paulo\) | \- | \- | \- | \- | 
| AWS GovCloud \(US\-East\) | \- | \- | \- | \- | 
| AWS GovCloud \(US\-West\) | \- | \- | \- | \- | 