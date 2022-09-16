# Supported features in Amazon Aurora by AWS Region and Aurora DB engine<a name="Concepts.AuroraFeaturesRegionsDBEngines.grids"></a>

Aurora MySQL\- and PostgreSQL\-compatible database engines support several Amazon Aurora and Amazon RDS features and options\. The support varies across specific versions of each database engine, and across AWS Regions\. To identify Aurora database engine version support and availability for a feature in a given AWS Region, you can use the following sections\.

**Topics**
+ [Backtracking in Aurora](Concepts.Aurora_Fea_Regions_DB-eng.Feature.Backtrack.md)
+ [Aurora global databases](Concepts.Aurora_Fea_Regions_DB-eng.Feature.GlobalDatabase.md)
+ [Aurora Kerberos authentication](Concepts.Aurora_Fea_Regions_DB-eng.Feature.KerberosAuthentication.md)
+ [Aurora machine learning](Concepts.Aurora_Fea_Regions_DB-eng.Feature.Aurora_ML.md)
+ [Aurora parallel queries](Concepts.Aurora_Fea_Regions_DB-eng.Feature.ParallelQuery.md)
+ [Amazon RDS Proxy](Concepts.Aurora_Fea_Regions_DB-eng.Feature.RDS_Proxy.md)
+ [Aurora Serverless v2](Concepts.Aurora_Fea_Regions_DB-eng.Feature.ServerlessV2.md)
+ [Aurora Serverless v1](Concepts.Aurora_Fea_Regions_DB-eng.Feature.Serverless.md)
+ [Data API for Aurora Serverless v1](Concepts.Aurora_Fea_Regions_DB-eng.Feature.Data_API.md)

Some of these features are Aurora\-only capabilities\. For example, Aurora Serverless, Aurora global databases, and support for integration with AWS machine learning services aren't supported by Amazon RDS\. Other features, such as Amazon RDS Proxy, are supported by both Amazon Aurora and Amazon RDS\.  

The tables use the following patterns to specify version numbers and level of support: 
+ **Version x\.y** – The specific version alone is supported\.
+ **Version x\.y and higher** – The version and all minor versions are also supported\. For example, "version 10\.11 and higher" means that versions 10\.11, 10\.11\.1, and 10\.12 are also supported\. 
+ **\-** – The feature is not currently available for that particular Aurora feature for the given Aurora database engine, or in that specific AWS Region\.