# Performance Insights with Aurora<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.PerfInsights"></a>

Performance Insights expands on existing Amazon RDS monitoring features to illustrate and help you analyze your database performance\. With the Performance Insights dashboard, you can visualize the database load on your Amazon RDS DB instance load and filter the load by waits, SQL statements, hosts, or users\. For more information, see [Overview of Performance Insights on Amazon Aurora](USER_PerfInsights.Overview.md)\. 

**Topics**
+ [Performance Insights with Aurora MySQL](#Concepts.Aurora_Fea_Regions_DB-eng.Feature.PerfInsights.amy)
+ [Performance Insights with Aurora PostgreSQL](#Concepts.Aurora_Fea_Regions_DB-eng.Feature.PerfInsights.apg)
+ [Performance Insights with Aurora Serverless](#Concepts.Aurora_Fea_Regions_DB-eng.Feature.PerfInsights.serverless)

## Performance Insights with Aurora MySQL<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.PerfInsights.amy"></a>

**Note**  
Engine version support is different for Performance Insights with Aurora MySQL if you have parallel query turned on\. For more information on parallel query, see [Working with parallel query for Amazon Aurora MySQL](aurora-mysql-parallel-query.md)\.

**Topics**
+ [Performance Insights with Aurora MySQL and parallel query turned off](#Feature.PerfInsights.regions.amy.pq)
+ [Performance Insights with Aurora MySQL and parallel query turned on](#Feature.PerfInsights.regions.amy.pqoff)

### Performance Insights with Aurora MySQL and parallel query turned off<a name="Feature.PerfInsights.regions.amy.pq"></a>

Following are the supported engines and Region availability for Performance Insights with Aurora MySQL and parallel query turned off\.


| Region | Aurora MySQL version 3 | Aurora MySQL version 2 | 
| --- | --- | --- | 
| US East \(Ohio\) | All versions | All versions | 
| US East \(N\. Virginia\) | All versions | All versions | 
| US West \(N\. California\) | All versions | All versions | 
| US West \(Oregon\) | All versions | All versions | 
| Africa \(Cape Town\) | All versions | All versions | 
| Asia Pacific \(Hong Kong\) | All versions | All versions | 
| Asia Pacific \(Hyderabad\) | All versions | All versions | 
| Asia Pacific \(Jakarta\) | All versions | All versions | 
| Asia Pacific \(Melbourne\) | All versions | All versions | 
| Asia Pacific \(Mumbai\) | All versions | All versions | 
| Asia Pacific \(Osaka\) | All versions | All versions | 
| Asia Pacific \(Seoul\) | All versions | All versions | 
| Asia Pacific \(Singapore\) | All versions | All versions | 
| Asia Pacific \(Sydney\) | All versions | All versions | 
| Asia Pacific \(Tokyo\) | All versions | All versions | 
| Canada \(Central\) | All versions | All versions | 
| China \(Beijing\) | All versions | All versions | 
| China \(Ningxia\) | All versions | All versions | 
| Europe \(Frankfurt\) | All versions | All versions | 
| Europe \(Ireland\) | All versions | All versions | 
| Europe \(London\) | All versions | All versions | 
| Europe \(Milan\) | All versions | All versions | 
| Europe \(Paris\) | All versions | All versions | 
| Europe \(Spain\) | All versions | All versions | 
| Europe \(Stockholm\) | All versions | All versions | 
| Europe \(Zurich\) | All versions | All versions | 
| Middle East \(Bahrain\) | All versions | All versions | 
| Middle East \(UAE\) | All versions | All versions | 
| South America \(São Paulo\) | All versions | All versions | 
| AWS GovCloud \(US\-East\) | – | – | 
| AWS GovCloud \(US\-West\) | – | – | 

### Performance Insights with Aurora MySQL and parallel query turned on<a name="Feature.PerfInsights.regions.amy.pqoff"></a>

Following are the supported engines and Region availability for Performance Insights with Aurora MySQL and parallel query turned on\.


| Region | Aurora MySQL version 3 | Aurora MySQL version 2 | 
| --- | --- | --- | 
| US East \(Ohio\) | – | Version 2\.09\.0 and higher | 
| US East \(N\. Virginia\) | – | Version 2\.09\.0 and higher | 
| US West \(N\. California\) | – | Version 2\.09\.0 and higher | 
| US West \(Oregon\) | – | Version 2\.09\.0 and higher | 
| Africa \(Cape Town\) | – | Version 2\.09\.0 and higher | 
| Asia Pacific \(Hong Kong\) | – | Version 2\.09\.0 and higher | 
| Asia Pacific \(Hyderabad\) | – | All versions | 
| Asia Pacific \(Jakarta\) | – | Version 2\.09\.0 and higher | 
| Asia Pacific \(Melbourne\) | – | Version 2\.09\.0 and higher | 
| Asia Pacific \(Mumbai\) | – | Version 2\.09\.0 and higher | 
| Asia Pacific \(Osaka\) | – | Version 2\.09\.0 and higher | 
| Asia Pacific \(Seoul\) | – | Version 2\.09\.0 and higher | 
| Asia Pacific \(Singapore\) | – | Version 2\.09\.0 and higher | 
| Asia Pacific \(Sydney\) | – | Version 2\.09\.0 and higher | 
| Asia Pacific \(Tokyo\) | – | Version 2\.09\.0 and higher | 
| Canada \(Central\) | – | Version 2\.09\.0 and higher | 
| China \(Beijing\) | – | Version 2\.09\.0 and higher | 
| China \(Ningxia\) | – | Version 2\.09\.0 and higher | 
| Europe \(Frankfurt\) | – | Version 2\.09\.0 and higher | 
| Europe \(Ireland\) | – | Version 2\.09\.0 and higher | 
| Europe \(London\) | – | Version 2\.09\.0 and higher | 
| Europe \(Milan\) | – | Version 2\.09\.0 and higher | 
| Europe \(Paris\) | – | Version 2\.09\.0 and higher | 
| Europe \(Spain\) | – | Version 2\.09\.0 and higher | 
| Europe \(Stockholm\) | – | Version 2\.09\.0 and higher | 
| Europe \(Zurich\) | – | Version 2\.09\.0 and higher | 
| Middle East \(Bahrain\) | – | Version 2\.09\.0 and higher | 
| Middle East \(UAE\) | – | Version 2\.09\.0 and higher | 
| South America \(São Paulo\) | – | Version 2\.09\.0 and higher | 
| AWS GovCloud \(US\-East\) | – | – | 
| AWS GovCloud \(US\-West\) | – | – | 

## Performance Insights with Aurora PostgreSQL<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.PerfInsights.apg"></a>

Following are the supported engines and Region availability for Performance Insights with Aurora PostgreSQL\.


| Region | Aurora PostgreSQL 14 | Aurora PostgreSQL 13 | Aurora PostgreSQL 12 | Aurora PostgreSQL 11 | Aurora PostgreSQL 10 | 
| --- | --- | --- | --- | --- | --- | 
| US East \(Ohio\) | All versions | All versions | All versions | All versions | All versions | 
| US East \(N\. Virginia\) | All versions | All versions | All versions | All versions | All versions | 
| US West \(N\. California\) | All versions | All versions | All versions | All versions | All versions | 
| US West \(Oregon\) | All versions | All versions | All versions | All versions | All versions | 
| Africa \(Cape Town\) | All versions | All versions | All versions | All versions | All versions | 
| Asia Pacific \(Hong Kong\) | All versions | All versions | All versions | All versions | All versions | 
| Asia Pacific \(Hyderabad\) | All versions | All versions | All versions | All versions | All versions | 
| Asia Pacific \(Jakarta\) | All versions | All versions | All versions | All versions | All versions | 
| Asia Pacific \(Melbourne\) | All versions | All versions | All versions | All versions | All versions | 
| Asia Pacific \(Mumbai\) | All versions | All versions | All versions | All versions | All versions | 
| Asia Pacific \(Osaka\) | All versions | All versions | All versions | All versions | All versions | 
| Asia Pacific \(Seoul\) | All versions | All versions | All versions | All versions | All versions | 
| Asia Pacific \(Singapore\) | All versions | All versions | All versions | All versions | All versions | 
| Asia Pacific \(Sydney\) | All versions | All versions | All versions | All versions | All versions | 
| Asia Pacific \(Tokyo\) | All versions | All versions | All versions | All versions | All versions | 
| Canada \(Central\) | All versions | All versions | All versions | All versions | All versions | 
| China \(Beijing\) | All versions | All versions | All versions | All versions | All versions | 
| China \(Ningxia\) | All versions | All versions | All versions | All versions | All versions | 
| Europe \(Frankfurt\) | All versions | All versions | All versions | All versions | All versions | 
| Europe \(Ireland\) | All versions | All versions | All versions | All versions | All versions | 
| Europe \(London\) | All versions | All versions | All versions | All versions | All versions | 
| Europe \(Milan\) | All versions | All versions | All versions | All versions | All versions | 
| Europe \(Paris\) | All versions | All versions | All versions | All versions | All versions | 
| Europe \(Spain\) | All versions | All versions | All versions | All versions | All versions | 
| Europe \(Stockholm\) | All versions | All versions | All versions | All versions | All versions | 
| Europe \(Zurich\) | All versions | All versions | All versions | All versions | All versions | 
| Middle East \(Bahrain\) | All versions | All versions | All versions | All versions | All versions | 
| Middle East \(UAE\) | All versions | All versions | All versions | All versions | All versions | 
| South America \(São Paulo\) | All versions | All versions | All versions | All versions | All versions | 
| AWS GovCloud \(US\-East\) | – | – | – | – | – | 
| AWS GovCloud \(US\-West\) | – | – | – | – | – | 

## Performance Insights with Aurora Serverless<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.PerfInsights.serverless"></a>

Aurora Serverless v2 supports Performance Insights for all MySQL\-compatible and PostgreSQL\-compatible versions\. We recommend that you set the minimum capacity to at least 2 Aurora capacity units \(ACUs\)\.

Aurora Serverless v1 doesn't support Performance Insights\.