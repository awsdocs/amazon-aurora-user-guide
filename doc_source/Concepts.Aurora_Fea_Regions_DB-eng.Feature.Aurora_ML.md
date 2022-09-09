# Aurora machine learning<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.Aurora_ML"></a>

Aurora machine learning provides simple, optimized, and secure integration between Aurora and AWS machine learning services without having to build custom integrations or move data around\. Aurora exposes ML models as SQL functions, so you don't need to learn new programming languages or tools\. Instead, you use standard SQL to build applications that call ML models, pass data to them, and return predictions as query results\. For more information, see [Using machine learning \(ML\) capabilities with Amazon Aurora](aurora-ml.md)\.

**Topics**
+ [Aurora machine learning with Aurora MySQL](#Concepts.Aurora_Fea_Regions_DB-eng.Feature.Aurora_ML.ams)
+ [Aurora machine learning with Aurora PostgreSQL](#Concepts.Aurora_Fea_Regions_DB-eng.Feature.Aurora_ML.apg)

## Aurora machine learning with Aurora MySQL<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.Aurora_ML.ams"></a>

Aurora machine learning is supported for SageMaker and Amazon Comprehend in the AWS Regions where they are available\. For a list of AWS Regions where Amazon SageMaker is available, see [Amazon SageMaker endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/sagemaker.html) in the *Amazon Web Services General Reference*\. For a list of AWS Regions where Amazon Comprehend is available, see [Amazon Comprehend endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/comprehend.html) in the *Amazon Web Services General Reference*\. Following are the supported engines and Region availability for Aurora machine learning with Aurora MySQL\. 


| Region | Aurora MySQL 5\.6 | Aurora MySQL 5\.7 | Aurora MySQL 8\.0 | 
| --- | --- | --- | --- | 
| US East \(Ohio\) | – | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| US East \(N\. Virginia\) | – | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| US West \(N\. California\) | – | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| US West \(Oregon\) | – | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Africa \(Cape Town\) | – | – | – | 
| Asia Pacific \(Hong Kong\) | – | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Asia Pacific \(Jakarta\) | – | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Asia Pacific \(Mumbai\) | – | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Asia Pacific \(Osaka\) | – | Version 2\.07\.3 and higher | Version 3\.01\.0 and higher | 
| Asia Pacific \(Seoul\) | – | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Asia Pacific \(Singapore\) | – | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Asia Pacific \(Sydney\) | – | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Asia Pacific \(Tokyo\) | – | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Canada \(Central\) | – | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| China \(Beijing\) | – | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| China \(Ningxia\) | – | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Europe \(Frankfurt\) | – | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Europe \(Ireland\) | – | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Europe \(London\) | – | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Europe \(Milan\) | – | – | – | 
| Europe \(Paris\) | – | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Europe \(Stockholm\) | – | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Middle East \(Bahrain\) | – | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| Middle East \(UAE\) | – | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| South America \(São Paulo\) | – | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| AWS GovCloud \(US\-East\) | – | Version 2\.07 and higher | Version 3\.01\.0 and higher | 
| AWS GovCloud \(US\-West\) | – | Version 2\.07 and higher | Version 3\.01\.0 and higher | 

## Aurora machine learning with Aurora PostgreSQL<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.Aurora_ML.apg"></a>

Aurora machine learning is supported for SageMaker and Amazon Comprehend in the AWS Regions where they are available\. For a list of AWS Regions where Amazon SageMaker is available, see [Amazon SageMaker endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/sagemaker.html) in the *Amazon Web Services General Reference*\. For a list of AWS Regions where Amazon Comprehend is available, see [Amazon Comprehend endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/comprehend.html) in the *Amazon Web Services General Reference*\. Following are the supported engines and Region availability for Aurora machine learning with Aurora PostgreSQL\.


| Region | Aurora PostgreSQL 10 | Aurora PostgreSQL 11 | Aurora PostgreSQL 12 | Aurora PostgreSQL 13 | Aurora PostgreSQL 14 | 
| --- | --- | --- | --- | --- | --- | 
| US East \(Ohio\) | Version 10\.14 | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| US East \(N\. Virginia\) | Version 10\.14 | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| US West \(N\. California\) | Version 10\.14 | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| US West \(Oregon\) | Version 10\.14 | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Africa \(Cape Town\) | – | – | – | – | – | 
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
| Europe \(Milan\) | – | – | – | – | – | 
| Europe \(Paris\) | Version 10\.14 | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Europe \(Stockholm\) | Version 10\.14 | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Middle East \(Bahrain\) | Version 10\.14 | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Middle East \(UAE\) | Version 10\.14 | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| South America \(São Paulo\) | Version 10\.14 | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| AWS GovCloud \(US\-East\) | Version 10\.14 | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| AWS GovCloud \(US\-West\) | Version 10\.14 | Version 11\.9 and higher | Version 12\.4 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 