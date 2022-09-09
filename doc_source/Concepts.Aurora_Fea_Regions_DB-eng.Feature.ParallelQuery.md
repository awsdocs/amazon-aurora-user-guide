# Aurora parallel queries<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.ParallelQuery"></a>

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
| Asia Pacific \(Jakarta\) | – | Version 2\.10 and higher | Version 3\.01\.0 and higher | 
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
| Middle East \(UAE\) | Version 1\.23 | Version 2\.09 and higher | Version 3\.01\.0 and higher | 
| South America \(São Paulo\) | Version 1\.23 | Version 2\.09 and higher | Version 3\.01\.0 and higher | 
| AWS GovCloud \(US\-East\) | Version 1\.23 | Version 2\.09 and higher | Version 3\.01\.0 and higher | 
| AWS GovCloud \(US\-West\) | Version 1\.23 | Version 2\.09 and higher | Version 3\.01\.0 and higher | 