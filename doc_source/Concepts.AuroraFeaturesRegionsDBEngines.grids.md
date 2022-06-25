# Supported features in Amazon Aurora by AWS Region and Aurora DB engine<a name="Concepts.AuroraFeaturesRegionsDBEngines.grids"></a>

Aurora MySQL\- and PostgreSQL\-compatible database engines support several Amazon Aurora and Amazon RDS features and options\. The support varies across specific versions of each database engine, and across AWS Regions\. You can use the tables in this section to identify Aurora database engine version support and availability in a given AWS Region for the following features: 

**Topics**
+ [Backtracking in Aurora](#Concepts.Aurora_Fea_Regions_DB-eng.Feature.Backtrack)
+ [Aurora global databases](#Concepts.Aurora_Fea_Regions_DB-eng.Feature.GlobalDatabase)
+ [Aurora machine learning](#Concepts.Aurora_Fea_Regions_DB-eng.Feature.Aurora_ML)
+ [Aurora parallel queries](#Concepts.Aurora_Fea_Regions_DB-eng.Feature.ParallelQuery)
+ [Amazon RDS Proxy](#Concepts.Aurora_Fea_Regions_DB-eng.Feature.RDS_Proxy)
+ [Aurora Serverless v2](#Concepts.Aurora_Fea_Regions_DB-eng.Feature.ServerlessV2)
+ [Aurora Serverless v1](#Concepts.Aurora_Fea_Regions_DB-eng.Feature.Serverless)
+ [Data API for Aurora Serverless v1](#Concepts.Aurora_Fea_Regions_DB-eng.Feature.Data_API)

Some of these features are Aurora\-only capabilities\. For example, Aurora Serverless, Aurora global databases, and support for integration with AWS machine learning services aren't supported by Amazon RDS\. Other features, such as Amazon RDS Proxy, are supported by both Amazon Aurora and Amazon RDS\.  

The tables use the following patterns to specify version numbers and level of support: 
+ **Version x\.y** – The specific version alone is supported\.
+ **Version x\.y and higher** – The version and all minor versions are also supported\. For example, "version 10\.11 and higher" means that versions 10\.11, 10\.11\.1, and 10\.12 are also supported\. 
+ **\-** – The feature is not currently available for that particular Aurora feature for the given Aurora database engine, or in that specific AWS Region\.

## Backtracking in Aurora<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.Backtrack"></a>

By using backtracking in Aurora, you return the state of an Aurora cluster to a specific point in time, without restoring data from a backup\. It completes within seconds, even for large databases\. For more information, see [Backtracking an Aurora DB cluster](AuroraMySQL.Managing.Backtrack.md)\.

Aurora backtracking is available for Aurora MySQL only\. It's not available for Aurora PostgreSQL\.  


| Region | Aurora MySQL 5\.6 | Aurora MySQL 5\.7 | Aurora MySQL 8\.0 | 
| --- | --- | --- | --- | 
| US East \(Ohio\) | Version 5\.6\.10a | Version 2\.06 and higher | \- | 
| US East \(N\. Virginia\) | Version 5\.6\.10a | Version 2\.06 and higher | \- | 
| US West \(N\. California\) | Version 5\.6\.10a | Version 2\.06 and higher | \- | 
| US West \(Oregon\) | Version 5\.6\.10a | Version 2\.06 and higher | \- | 
| Africa \(Cape Town\) | \- | \- | \- | 
| Asia Pacific \(Hong Kong\) | \- | \- | \- | 
| Asia Pacific \(Jakarta\) | \- | \- | \- | 
| Asia Pacific \(Mumbai\) | Version 5\.6\.10a | Version 2\.06 and higher | \- | 
| Asia Pacific \(Osaka\) | Version 1\.22 and higher | Version 2\.07\.3 and higher | \- | 
| Asia Pacific \(Seoul\) | Version 5\.6\.10a | Version 2\.06 and higher | \- | 
| Asia Pacific \(Singapore\) | Version 5\.6\.10a | Version 2\.06 and higher | \- | 
| Asia Pacific \(Sydney\) | Version 5\.6\.10a | Version 2\.06 and higher | \- | 
| Asia Pacific \(Tokyo\) | Version 5\.6\.10a | Version 2\.06 and higher | \- | 
| Canada \(Central\) | Version 5\.6\.10a | Version 2\.06 and higher | \- | 
| China \(Beijing\) | \- | \- | \- | 
| China \(Ningxia\) | \- | \- | \- | 
| Europe \(Frankfurt\) | Version 5\.6\.10a | Version 2\.06 and higher | \- | 
| Europe \(Ireland\) | Version 5\.6\.10a | Version 2\.06 and higher | \- | 
| Europe \(London\) | Version 5\.6\.10a | Version 2\.06 and higher | \- | 
| Europe \(Milan\) | \- | \- | \- | 
| Europe \(Paris\) | Version 5\.6\.10a | Version 2\.06 and higher | \- | 
| Europe \(Stockholm\) | \- | \- | \- | 
| Middle East \(Bahrain\) | \- | \- | \- | 
| South America \(São Paulo\) | \- | \- | \- | 
| AWS GovCloud \(US\-East\) | \- | \- | \- | 
| AWS GovCloud \(US\-West\) | \- | \- | \- | 

## Aurora global databases<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.GlobalDatabase"></a>

An *Aurora global database* is a single database that spans multiple AWS Regions, enabling low\-latency global reads and disaster recovery from any Region\-wide outage\. It provides built\-in fault tolerance for your deployment because the DB instance relies not on a single AWS Region, but upon multiple Regions and different Availability Zones\.  For more information, see [Using Amazon Aurora global databases](aurora-global-database.md)\.

**Topics**
+ [Aurora global databases with Aurora MySQL](#Concepts.Aurora_Fea_Regions_DB-eng.Feature.GlobalDatabase.ams)
+ [Aurora global databases with Aurora PostgreSQL](#Concepts.Aurora_Fea_Regions_DB-eng.Feature.GlobalDatabase.apg)

### Aurora global databases with Aurora MySQL<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.GlobalDatabase.ams"></a>

Following are the supported engines and Region availability for Aurora global databases with Aurora MySQL\.


| Region | Aurora MySQL 5\.6 | Aurora MySQL 5\.7 | Aurora MySQL 8\.0 | 
| --- | --- | --- | --- | 
| US East \(Ohio\) | Version 5\.6\.10a; version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| US East \(N\. Virginia\) | Version 5\.6\.10a; version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| US West \(N\. California\) | Version 5\.6\.10a; version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| US West \(Oregon\) | Version 5\.6\.10a; version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Africa \(Cape Town\) | \- | \- | \- | 
| Asia Pacific \(Hong Kong\) | \- | \- | \- | 
| Asia Pacific \(Jakarta\) | \- | \- | \- | 
| Asia Pacific \(Mumbai\) | Version 5\.6\.10a; Version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Asia Pacific \(Osaka\) | Version 1\.22\.3 and higher | Version 2\.07\.3 and higher | Version 3\.01\.0 and higher | 
| Asia Pacific \(Seoul\) | Version 5\.6\.10a; Version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Asia Pacific \(Singapore\) | Version 5\.6\.10a; Version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Asia Pacific \(Sydney\) | Version 5\.6\.10a; Version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Asia Pacific \(Tokyo\) | Version 5\.6\.10a; Version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Canada \(Central\) | Version 5\.6\.10a; Version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| China \(Beijing\) | Version 1\.22\.2 and higher | Version 2\.07\.2 and higher | Version 3\.01\.0 and higher | 
| China \(Ningxia\) | Version 1\.22\.2 and higher | Version 2\.07\.2 and higher | Version 3\.01\.0 and higher | 
| Europe \(Frankfurt\) | Version 5\.6\.10a; Version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Europe \(Ireland\) | Version 5\.6\.10a; Version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Europe \(London\) | Version 5\.6\.10a; Version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Europe \(Milan\) | \- | \- | \- | 
| Europe \(Paris\) | Version 5\.6\.10a; Version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Europe \(Stockholm\) | Version 1\.22\.2 and higher | Version 2\.07\.0 and higher | Version 3\.01\.0 and higher | 
| Middle East \(Bahrain\) | \- | \- | \- | 
| South America \(São Paulo\) | Version 1\.22\.2 and higher | Version 2\.07\.1 and higher | Version 3\.01\.0 and higher | 
| AWS GovCloud \(US\-East\) | Version 5\.6\.10a; version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| AWS GovCloud \(US\-West\) | Version 5\.6\.10a; version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 

### Aurora global databases with Aurora PostgreSQL<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.GlobalDatabase.apg"></a>

Following are the supported engines and Region availability for Aurora global databases with Aurora PostgreSQL\.


| Region | Aurora PostgreSQL 10 | Aurora PostgreSQL 11 | Aurora PostgreSQL 12 | Aurora PostgreSQL 13 | Aurora PostgreSQL 14 | 
| --- | --- | --- | --- | --- | --- | 
| US East \(Ohio\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| US East \(N\. Virginia\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| US West \(N\. California\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| US West \(Oregon\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Africa \(Cape Town\) | \- | \- | \- | \- | \- | 
| Asia Pacific \(Hong Kong\) | \- | \- | \- | \- | \- | 
| Asia Pacific \(Jakarta\) | \- | \- | \- | \- | \- | 
| Asia Pacific \(Mumbai\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Asia Pacific \(Osaka\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Asia Pacific \(Seoul\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Asia Pacific \(Singapore\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Asia Pacific \(Sydney\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Asia Pacific \(Tokyo\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Canada \(Central\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| China \(Beijing\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| China \(Ningxia\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Europe \(Frankfurt\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Europe \(Ireland\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Europe \(London\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Europe \(Milan\) | \- | \- | \- | \- | \- | 
| Europe \(Paris\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Europe \(Stockholm\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Middle East \(Bahrain\) | \- | \- | \- | \- | \- | 
| South America \(São Paulo\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| AWS GovCloud \(US\-East\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| AWS GovCloud \(US\-West\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 

## Aurora machine learning<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.Aurora_ML"></a>

Aurora machine learning provides simple, optimized, and secure integration between Aurora and AWS machine learning services without having to build custom integrations or move data around\. Aurora exposes ML models as SQL functions, so you don't need to learn new programming languages or tools\. Instead, you use standard SQL to build applications that call ML models, pass data to them, and return predictions as query results\. For more information, see [Using machine learning \(ML\) capabilities with Amazon Aurora](aurora-ml.md)\.

**Topics**
+ [Aurora machine learning with Aurora MySQL](#Concepts.Aurora_Fea_Regions_DB-eng.Feature.Aurora_ML.ams)
+ [Aurora machine learning with Aurora PostgreSQL](#Concepts.Aurora_Fea_Regions_DB-eng.Feature.Aurora_ML.apg)

### Aurora machine learning with Aurora MySQL<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.Aurora_ML.ams"></a>

Aurora machine learning is supported for SageMaker and Amazon Comprehend in the AWS Regions where they are available\. For a list of AWS Regions where Amazon SageMaker is available, see [Amazon SageMaker endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/sagemaker.html) in the *Amazon Web Services General Reference*\. For a list of AWS Regions where Amazon Comprehend is available, see [Amazon Comprehend endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/comprehend.html) in the *Amazon Web Services General Reference*\. Following are the supported engines and Region availability for Aurora machine learning with Aurora MySQL\. 


| Region | Aurora MySQL 5\.6 | Aurora MySQL 5\.7 | Aurora MySQL 8\.0 | 
| --- | --- | --- | --- | 
| US East \(Ohio\) | \- | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| US East \(N\. Virginia\) | \- | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| US West \(N\. California\) | \- | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| US West \(Oregon\) | \- | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Africa \(Cape Town\) | \- | \- | \- | 
| Asia Pacific \(Hong Kong\) | \- | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Asia Pacific \(Jakarta\) | \- | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Asia Pacific \(Mumbai\) | \- | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Asia Pacific \(Osaka\) | \- | Version 2\.07\.3 and higher | Version 3\.01\.0 and higher | 
| Asia Pacific \(Seoul\) | \- | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Asia Pacific \(Singapore\) | \- | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Asia Pacific \(Sydney\) | \- | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Asia Pacific \(Tokyo\) | \- | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Canada \(Central\) | \- | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| China \(Beijing\) | \- | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| China \(Ningxia\) | \- | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Europe \(Frankfurt\) | \- | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Europe \(Ireland\) | \- | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Europe \(London\) | \- | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Europe \(Milan\) | \- | \- | \- | 
| Europe \(Paris\) | \- | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Europe \(Stockholm\) | \- | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Middle East \(Bahrain\) | \- | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| South America \(São Paulo\) | \- | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| AWS GovCloud \(US\-East\) | \- | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| AWS GovCloud \(US\-West\) | \- | Version 2\.07 and higher | Version 3\.01\.0 and higher | 

### Aurora machine learning with Aurora PostgreSQL<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.Aurora_ML.apg"></a>

Aurora machine learning is supported for SageMaker and Amazon Comprehend in the AWS Regions where they are available\. For a list of AWS Regions where Amazon SageMaker is available, see [Amazon SageMaker endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/sagemaker.html) in the *Amazon Web Services General Reference*\. For a list of AWS Regions where Amazon Comprehend is available, see [Amazon Comprehend endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/comprehend.html) in the *Amazon Web Services General Reference*\. Following are the supported engines and Region availability for Aurora machine learning with Aurora PostgreSQL\.


| Region | Aurora PostgreSQL 10 | Aurora PostgreSQL 11 | Aurora PostgreSQL 12 | Aurora PostgreSQL 13 | Aurora PostgreSQL 14 | 
| --- | --- | --- | --- | --- | --- | 
| US East \(Ohio\) | Version 10\.14 | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| US East \(N\. Virginia\) | Version 10\.14 | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| US West \(N\. California\) | Version 10\.14 | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| US West \(Oregon\) | Version 10\.14 | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Africa \(Cape Town\) | \- | \- | \- | \- | \- | 
| Asia Pacific \(Hong Kong\) | Version 10\.14 | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Asia Pacific \(Jakarta\) | Version 10\.14 | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Asia Pacific \(Mumbai\) | Version 10\.14 | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Asia Pacific \(Osaka\) | Version 10\.14 | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Asia Pacific \(Seoul\) | Version 10\.14 | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Asia Pacific \(Singapore\) | Version 10\.14 | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Asia Pacific \(Sydney\) | Version 10\.14 | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Asia Pacific \(Tokyo\) | Version 10\.14 | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Canada \(Central\) | Version 10\.14 | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| China \(Beijing\) | Version 10\.14 | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| China \(Ningxia\) | Version 10\.14 | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Europe \(Frankfurt\) | Version 10\.14 | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Europe \(Ireland\) | Version 10\.14 | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Europe \(London\) | Version 10\.14 | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Europe \(Milan\) | \- | \- | \- | \- | \- | 
| Europe \(Paris\) | Version 10\.14 | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Europe \(Stockholm\) | Version 10\.14 | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Middle East \(Bahrain\) | Version 10\.14 | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| South America \(São Paulo\) | Version 10\.14 | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| AWS GovCloud \(US\-East\) | Version 10\.14 | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| AWS GovCloud \(US\-West\) | Version 10\.14 | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 

## Aurora parallel queries<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.ParallelQuery"></a>

Aurora parallel queries can speed up your queries by up to two orders of magnitude, while maintaining high throughput for your core transactional workload\. Using the unique Aurora architecture, parallel queries can push down and parallelize query processing across thousands of CPUs in the Aurora storage layer\. By offloading analytical query processing to the Aurora storage layer, parallel queries reduce network, CPU, and buffer pool contention for transactional workloads\. For more information, see [Working with parallel query for Amazon Aurora MySQL](aurora-mysql-parallel-query.md)\. To learn more about Aurora MySQL versions available for parallel queries and any steps you might need to take based on that version to support parallel queries, see [Planning for a parallel query cluster](aurora-mysql-parallel-query.md#aurora-mysql-parallel-query-planning)\.

Aurora parallel queries are available for Aurora MySQL only\. However, PostgreSQL has its own parallel query feature that is available on Amazon RDS\. The capability is enabled by default when a new PostgreSQL instance is created \(versions 10\.1 and higher\)\. For more information, see [PostgreSQL on Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html)\. 


| Region | Aurora MySQL 5\.6 | Aurora MySQL 5\.7 | Aurora MySQL 8\.0 | 
| --- | --- | --- | --- | 
| US East \(Ohio\) | Version 1\.23 | Version 2\.09 and higher | Version 3\.01\.0 and higher | 
| US East \(N\. Virginia\) | Version 1\.23 | Version 2\.09 and higher | Version 3\.01\.0 and higher | 
| US West \(N\. California\) | Version 1\.23 | Version 2\.09 and higher | Version 3\.01\.0 and higher | 
| US West \(Oregon\) | Version 1\.23 | Version 2\.09 and higher | Version 3\.01\.0 and higher | 
| Africa \(Cape Town\) | Version 1\.23 | Version 2\.09 and higher | Version 3\.01\.0 and higher | 
| Asia Pacific \(Hong Kong\) | Version 1\.23 | Version 2\.09 and higher | Version 3\.01\.0 and higher | 
| Asia Pacific \(Jakarta\) | \- | Version 2\.10 and higher | Version 3\.01\.0 and higher | 
| Asia Pacific \(Mumbai\) | Version 1\.23 | Version 2\.09 and higher | Version 3\.01\.0 and higher | 
| Asia Pacific \(Osaka\) | Version 1\.23 | Version 2\.09 and higher | Version 3\.01\.0 and higher | 
| Asia Pacific \(Seoul\) | Version 1\.23 | Version 2\.09 and higher | Version 3\.01\.0 and higher | 
| Asia Pacific \(Singapore\) | Version 1\.23 | Version 2\.09 and higher | Version 3\.01\.0 and higher | 
| Asia Pacific \(Sydney\) | Version 1\.23 | Version 2\.09 and higher | Version 3\.01\.0 and higher | 
| Asia Pacific \(Tokyo\) | Version 1\.23 | Version 2\.09 and higher | Version 3\.01\.0 and higher | 
| Canada \(Central\) | Version 1\.23 | Version 2\.09 and higher | Version 3\.01\.0 and higher | 
| China \(Beijing\) | Version 1\.23 | Version 2\.09 and higher | Version 3\.01\.0 and higher | 
| China \(Ningxia\) | Version 1\.23 | Version 2\.09 and higher | Version 3\.01\.0 and higher | 
| Europe \(Frankfurt\) | Version 1\.23 | Version 2\.09 and higher | Version 3\.01\.0 and higher | 
| Europe \(Ireland\) | Version 1\.23 | Version 2\.09 and higher | Version 3\.01\.0 and higher | 
| Europe \(London\) | Version 1\.23 | Version 2\.09 and higher | Version 3\.01\.0 and higher | 
| Europe \(Milan\) | Version 1\.23 | Version 2\.09 and higher | Version 3\.01\.0 and higher | 
| Europe \(Paris\) | Version 1\.23 | Version 2\.09 and higher | Version 3\.01\.0 and higher | 
| Europe \(Stockholm\) | Version 1\.23 | Version 2\.09 and higher | Version 3\.01\.0 and higher | 
| Middle East \(Bahrain\) | Version 1\.23 | Version 2\.09 and higher | Version 3\.01\.0 and higher | 
| South America \(São Paulo\) | Version 1\.23 | Version 2\.09 and higher | Version 3\.01\.0 and higher | 
| AWS GovCloud \(US\-East\) | Version 1\.23 | Version 2\.09 and higher | Version 3\.01\.0 and higher | 
| AWS GovCloud \(US\-West\) | Version 1\.23 | Version 2\.09 and higher | Version 3\.01\.0 and higher | 

## Amazon RDS Proxy<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.RDS_Proxy"></a>

Amazon RDS Proxy is a fully managed, highly available database proxy that makes applications more scalable by pooling and sharing established database connections\. For more information about RDS Proxy, see [Using Amazon RDS Proxy](rds-proxy.md)\.

**Topics**
+ [Amazon RDS Proxy with Aurora MySQL](#Concepts.Aurora_Fea_Regions_DB-eng.Feature.RDS_Proxy.ams)
+ [Amazon RDS Proxy with Aurora PostgreSQL](#Concepts.Aurora_Fea_Regions_DB-eng.Feature.RDS_Proxy.apg)

### Amazon RDS Proxy with Aurora MySQL<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.RDS_Proxy.ams"></a>

Following are the supported engines and Region availability for RDS Proxy with Aurora MySQL\.


| Region | Aurora MySQL 5\.6 | Aurora MySQL 5\.7 | Aurora MySQL 8\.0 | 
| --- | --- | --- | --- | 
| US East \(Ohio\) | Version 5\.6\.10a; version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| US East \(N\. Virginia\) | Version 5\.6\.10a; version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| US West \(N\. California\) | Version 5\.6\.10a; version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| US West \(Oregon\) | Version 5\.6\.10a; version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Africa \(Cape Town\) | Version 5\.6\.10a; version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Asia Pacific \(Hong Kong\) | Version 5\.6\.10a; version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Asia Pacific \(Jakarta\) | \- | \- | \- | 
| Asia Pacific \(Mumbai\) | Version 5\.6\.10a; version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Asia Pacific \(Osaka\) | Version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Asia Pacific \(Seoul\) | Version 5\.6\.10a; version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Asia Pacific \(Singapore\) | Version 5\.6\.10a; version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Asia Pacific \(Sydney\) | Version 5\.6\.10a; version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Asia Pacific \(Tokyo\) | Version 5\.6\.10a; version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Canada \(Central\) | Version 5\.6\.10a; version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| China \(Beijing\) | \- | \- | \- | 
| China \(Ningxia\) | \- | \- | \- | 
| Europe \(Frankfurt\) | Version 5\.6\.10a; version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Europe \(Ireland\) | Version 5\.6\.10a; version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Europe \(London\) | Version 5\.6\.10a; version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Europe \(Milan\) | Version 5\.6\.10a; version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Europe \(Paris\) | Version 5\.6\.10a; version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Europe \(Stockholm\) | Version 5\.6\.10a; version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Middle East \(Bahrain\) | Version 5\.6\.10a; version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| South America \(São Paulo\) | Version 5\.6\.10a; version 1\.22 and higher | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| AWS GovCloud \(US\-East\) | \- | \- | \- | 
| AWS GovCloud \(US\-West\) | \- | \- | \- | 

### Amazon RDS Proxy with Aurora PostgreSQL<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.RDS_Proxy.apg"></a>

Following are the supported engines and Region availability for RDS Proxy with Aurora PostgreSQL\.


| Region | Aurora PostgreSQL 10 | Aurora PostgreSQL 11 | Aurora PostgreSQL 12 | Aurora PostgreSQL 13 | 
| --- | --- | --- | --- | --- | 
| US East \(Ohio\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | 
| US East \(N\. Virginia\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | 
| US West \(N\. California\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | 
| US West \(Oregon\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | 
| Africa \(Cape Town\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | 
| Asia Pacific \(Hong Kong\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | 
| Asia Pacific \(Jakarta\) | \- | \- | \- | \- | 
| Asia Pacific \(Mumbai\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | 
| Asia Pacific \(Osaka\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | 
| Asia Pacific \(Seoul\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | 
| Asia Pacific \(Singapore\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | 
| Asia Pacific \(Sydney\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | 
| Asia Pacific \(Tokyo\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | 
| Canada \(Central\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | 
| China \(Beijing\) | \- | \- | \- | \- | 
| China \(Ningxia\) | \- | \- | \- | \- | 
| Europe \(Frankfurt\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | 
| Europe \(Ireland\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | 
| Europe \(London\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | 
| Europe \(Milan\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | 
| Europe \(Paris\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | 
| Europe \(Stockholm\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | 
| Middle East \(Bahrain\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | 
| South America \(São Paulo\) | Version 10\.14 and higher | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | 
| AWS GovCloud \(US\-East\) | \- | \- | \- | \- | 
| AWS GovCloud \(US\-West\) | \- | \- | \- | \- | 

## Aurora Serverless v2<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.ServerlessV2"></a>

Aurora Serverless v2 is an on\-demand, auto\-scaling feature designed to be a cost\-effective approach to running intermittent or unpredictable workloads on Amazon Aurora\. It automatically starts up, shuts down, and scales capacity up or down, as needed by your applications\. The scaling is faster and more granular than with Aurora Serverless v1\. With Aurora Serverless v2, each cluster can contain a writer DB instance and multiple reader DB instances\. You can combine Aurora Serverless v2 and traditional provisioned DB instances within the same cluster\. For more information, see [Using Aurora Serverless v2](aurora-serverless-v2.md)\.


| Region | Aurora MySQL version 3 | Aurora PostgreSQL 13 | 
| --- | --- | --- | 
| US East \(Ohio\) | Version 3\.02\.0 | Version 13\.6 | 
| US East \(N\. Virginia\) | Version 3\.02\.0 | Version 13\.6 | 
| US West \(N\. California\) | Version 3\.02\.0 | Version 13\.6 | 
| US West \(Oregon\) | Version 3\.02\.0 | Version 13\.6 | 
| Africa \(Cape Town\) | \- | \- | 
| Asia Pacific \(Hong Kong\) | Version 3\.02\.0 | Version 13\.6 | 
| Asia Pacific \(Mumbai\) | Version 3\.02\.0 | Version 13\.6 | 
| Asia Pacific \(Mumbai\) | Version 3\.02\.0 | Version 13\.6 | 
| Asia Pacific \(Osaka\) | \- | \- | 
| Asia Pacific \(Seoul\) | Version 3\.02\.0 | Version 13\.6 | 
| Asia Pacific \(Singapore\) | Version 3\.02\.0 | Version 13\.6 | 
| Asia Pacific \(Sydney\) | Version 3\.02\.0 | Version 13\.6 | 
| Asia Pacific \(Tokyo\) | Version 3\.02\.0 | Version 13\.6 | 
| Canada \(Central\) | Version 3\.02\.0 | Version 13\.6 | 
| China \(Beijing\) | \- | \- | 
| China \(Ningxia\) | \- | \- | 
| Europe \(Frankfurt\) | Version 3\.02\.0 | Version 13\.6 | 
| Europe \(Ireland\) | Version 3\.02\.0 | Version 13\.6 | 
| Europe \(London\) | Version 3\.02\.0 | Version 13\.6 | 
| Europe \(Milan\) | \- | \- | 
| Europe \(Paris\) | Version 3\.02\.0 | Version 13\.6 | 
| Europe \(Stockholm\) | Version 3\.02\.0 | Version 13\.6 | 
| Middle East \(Bahrain\) | \- | \- | 
| South America \(São Paulo\) | Version 3\.02\.0 | Version 13\.6 | 
| AWS GovCloud \(US\-East\) | \- | \- | 
| AWS GovCloud \(US\-West\) | \- | \- | 

## Aurora Serverless v1<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.Serverless"></a>

Aurora Serverless is an on\-demand, auto\-scaling feature designed to be a cost\-effective approach to running intermittent or unpredictable workloads on Amazon Aurora\. It automatically starts up, shuts down, and scales capacity up or down, as needed by your applications, using a single DB instance in each cluster\. For more information, see [Using Amazon Aurora Serverless v1](aurora-serverless.md)\.


| Region | Aurora MySQL 5\.6 | Aurora MySQL 5\.7 | Aurora MySQL 8\.0 | Aurora PostgreSQL 10 | Aurora PostgreSQL 11 | 
| --- | --- | --- | --- | --- | --- | 
| US East \(Ohio\) | Version 5\.6\.10a | Version 2\.07\.1 | \- | Version 10\.18 | \- | 
| US East \(N\. Virginia\) | Version 5\.6\.10a | Version 2\.07\.1 | \- | Version 10\.18 | \- | 
| US West \(N\. California\) | Version 5\.6\.10a | Version 2\.07\.1 | \- | Version 10\.18 | \- | 
| US West \(Oregon\) | Version 5\.6\.10a | Version 2\.07\.1 | \- | Version 10\.18 | \- | 
| Africa \(Cape Town\) | \- | \- | \- | \- | \- | 
| Asia Pacific \(Hong Kong\) | \- | \- | \- | \- | \- | 
| Asia Pacific \(Jakarta\) | \- | \- | \- | \- | \- | 
| Asia Pacific \(Mumbai\) | Version 5\.6\.10a | Version 2\.07\.1 | \- | Version 10\.18 | \- | 
| Asia Pacific \(Osaka\) | \- | \- | \- | \- | \- | 
| Asia Pacific \(Seoul\) | Version 5\.6\.10a | Version 2\.07\.1 | \- | Version 10\.18 | \- | 
| Asia Pacific \(Singapore\) | Version 5\.6\.10a | Version 2\.07\.1 | \- | Version 10\.18 | \- | 
| Asia Pacific \(Sydney\) | Version 5\.6\.10a | Version 2\.07\.1 | \- | Version 10\.18 | \- | 
| Asia Pacific \(Tokyo\) | Version 5\.6\.10a | Version 2\.07\.1 | \- | Version 10\.18 | \- | 
| Canada \(Central\) | Version 5\.6\.10a | Version 2\.07\.1 | \- | Version 10\.18 | \- | 
| China \(Beijing\) | \- | \- | \- | \- | \- | 
| China \(Ningxia\) | \- | \- | \- | \- | \- | 
| Europe \(Frankfurt\) | Version 5\.6\.10a | Version 2\.07\.1 | \- | Version 10\.18 | \- | 
| Europe \(Ireland\) | Version 5\.6\.10a | Version 2\.07\.1 | \- | Version 10\.18 | \- | 
| Europe \(London\) | Version 5\.6\.10a | Version 2\.07\.1 | \- | Version 10\.18 | \- | 
| Europe \(Milan\) | \- | \- | \- | \- | \- | 
| Europe \(Paris\) | Version 5\.6\.10a | Version 2\.07\.1 | \- | Version 10\.18 | \- | 
| Europe \(Stockholm\) | \- | \- | \- | \- | \- | 
| Middle East \(Bahrain\) | \- | \- | \- | \- | \- | 
| South America \(São Paulo\) | \- | \- | \- | \- | \- | 
| AWS GovCloud \(US\-East\) | \- | \- | \- | \- | \- | 
| AWS GovCloud \(US\-West\) | \- | \- | \- | \- | \- | 

## Data API for Aurora Serverless v1<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.Data_API"></a>

The Data API for Aurora Serverless provides a web\-services interface to an Aurora Serverless v1 cluster\. Instead of managing database connections from client applications, you can run SQL commands against an HTTPS endpoint\. For more information, see [Using the Data API for Aurora Serverless v1](data-api.md)\. 


| Region | Aurora MySQL 5\.6 | Aurora MySQL 5\.7 | Aurora MySQL 8\.0 | Aurora PostgreSQL 10 | Aurora PostgreSQL 11 | 
| --- | --- | --- | --- | --- | --- | 
| US East \(Ohio\) | Version 5\.6\.10a | Version 2\.07\.1 | \- | Version 10\.18 | \- | 
| US East \(N\. Virginia\) | Version 5\.6\.10a | Version 2\.07\.1 | \- | Version 10\.18 | \- | 
| US West \(N\. California\) | Version 5\.6\.10a | Version 2\.07\.1 | \- | Version 10\.18 | \- | 
| US West \(Oregon\) | Version 5\.6\.10a | Version 2\.07\.1 | \- | Version 10\.18 | \- | 
| Africa \(Cape Town\) | \- | \- | \- | \- | \- | 
| Asia Pacific \(Hong Kong\) | \- | \- | \- | \- | \- | 
| Asia Pacific \(Jakarta\) | \- | \- | \- | \- | \- | 
| Asia Pacific \(Mumbai\) | Version 5\.6\.10a | Version 2\.07\.1 | \- | Version 10\.18 | \- | 
| Asia Pacific \(Osaka\) | \- | \- | \- | \- | \- | 
| Asia Pacific \(Seoul\) | Version 5\.6\.10a | Version 2\.07\.1 | \- | Version 10\.18 | \- | 
| Asia Pacific \(Singapore\) | Version 5\.6\.10a | Version 2\.07\.1 | \- | Version 10\.18 | \- | 
| Asia Pacific \(Sydney\) | Version 5\.6\.10a | Version 2\.07\.1 | \- | Version 10\.18 | \- | 
| Asia Pacific \(Tokyo\) | Version 5\.6\.10a | Version 2\.07\.1 | \- | Version 10\.18 | \- | 
| Canada \(Central\) | Version 5\.6\.10a | Version 2\.07\.1 | \- | Version 10\.18 | \- | 
| China \(Beijing\) | \- | \- | \- | \- | \- | 
| China \(Ningxia\) | \- | \- | \- | \- | \- | 
| Europe \(Frankfurt\) | Version 5\.6\.10a | Version 2\.07\.1 | \- | Version 10\.18 | \- | 
| Europe \(Ireland\) | Version 5\.6\.10a | Version 2\.07\.1 | \- | Version 10\.18 | \- | 
| Europe \(London\) | Version 5\.6\.10a | Version 2\.07\.1 | \- | Version 10\.18 | \- | 
| Europe \(Milan\) | \- | \- | \- | \- | \- | 
| Europe \(Paris\) | Version 5\.6\.10a | Version 2\.07\.1 | \- | Version 10\.18 | \- | 
| Europe \(Stockholm\) | \- | \- | \- | \- | \- | 
| Middle East \(Bahrain\) | \- | \- | \- | \- | \- | 
| South America \(São Paulo\) | \- | \- | \- | \- | \- | 
| AWS GovCloud \(US\-East\) | \- | \- | \- | \- | \- | 
| AWS GovCloud \(US\-West\) | \- | \- | \- | \- | \- | 