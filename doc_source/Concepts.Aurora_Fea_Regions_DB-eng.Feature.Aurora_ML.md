# Aurora machine learning<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.Aurora_ML"></a>

By using Amazon Aurora machine learning, you can integrate your Aurora DB cluster with Amazon Comprehend or Amazon SageMaker, depending on your needs\. Amazon Comprehend and SageMaker each support different machine learning use cases\. Amazon Comprehend is a *natural language processing* \(NLP\) service that's used to extract insights from documents\. By using Aurora machine learning with Amazon Comprehend, you can determine the sentiment of text in your database tables\. SageMaker is a full\-featured *machine learning* service\. Data scientists use Amazon SageMaker to build, train, and test machine learning models for a variety of inference tasks, such as fraud detection\. By using Aurora machine learning with SageMaker, database developers can invoke the SageMaker functionality in SQL code\.

Not all AWS Regions support both Amazon Comprehend and SageMaker, and only certain AWS Regions support Aurora machine learning and thus provide access to these services from an Aurora DB cluster\. The integration process for Aurora machine learning also differs by database engine\. For more information, see [Using machine learning capabilities with Amazon Aurora](aurora-ml.md)\.

**Topics**
+ [Aurora machine learning with Aurora MySQL](#Concepts.Aurora_Fea_Regions_DB-eng.Feature.Aurora_ML.amy)
+ [Aurora machine learning with Aurora PostgreSQL](#Concepts.Aurora_Fea_Regions_DB-eng.Feature.Aurora_ML.apg)

## Aurora machine learning with Aurora MySQL<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.Aurora_ML.amy"></a>

Aurora machine learning is supported for Aurora MySQL in the AWS Regions listed in the table\. In addition to having your version of Aurora MySQL available, the AWS Region must also support the service that you want to use\. For a list of AWS Regions where Amazon SageMaker is available, see [Amazon SageMaker endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/sagemaker.html) in the *Amazon Web Services General Reference*\. For a list of AWS Regions where Amazon Comprehend is available, see [Amazon Comprehend endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/comprehend.html) in the *Amazon Web Services General Reference*\. 


| Region | Aurora MySQL 8\.0 | Aurora MySQL 5\.7 | Aurora MySQL 5\.6 | 
| --- | --- | --- | --- | 
| US East \(Ohio\) | Version 3\.01\.0 and higher | Version 2\.07 and higher | – | 
| US East \(N\. Virginia\) | Version 3\.01\.0 and higher | Version 2\.07 and higher | – | 
| US West \(N\. California\) | Version 3\.01\.0 and higher | Version 2\.07 and higher | – | 
| US West \(Oregon\) | Version 3\.01\.0 and higher | Version 2\.07 and higher | – | 
| Africa \(Cape Town\) | – | – | – | 
| Asia Pacific \(Hong Kong\) | Version 3\.01\.0 and higher | Version 2\.07 and higher | – | 
| Asia Pacific \(Hyderabad\) | Version 3\.01\.0 and higher | Version 2\.07 and higher | – | 
| Asia Pacific \(Jakarta\) | Version 3\.01\.0 and higher | Version 2\.07 and higher | – | 
| Asia Pacific \(Melbourne\) | Version 3\.01\.0 and higher | Version 2\.07 and higher | – | 
| Asia Pacific \(Mumbai\) | Version 3\.01\.0 and higher | Version 2\.07 and higher | – | 
| Asia Pacific \(Osaka\) | Version 3\.01\.0 and higher | Version 2\.07\.3 and higher | – | 
| Asia Pacific \(Seoul\) | Version 3\.01\.0 and higher | Version 2\.07 and higher | – | 
| Asia Pacific \(Singapore\) | Version 3\.01\.0 and higher | Version 2\.07 and higher | – | 
| Asia Pacific \(Sydney\) | Version 3\.01\.0 and higher | Version 2\.07 and higher | – | 
| Asia Pacific \(Tokyo\) | Version 3\.01\.0 and higher | Version 2\.07 and higher | – | 
| Canada \(Central\) | Version 3\.01\.0 and higher | Version 2\.07 and higher | – | 
| China \(Beijing\) | Version 3\.01\.0 and higher | Version 2\.07 and higher | – | 
| China \(Ningxia\) | Version 3\.01\.0 and higher | Version 2\.07 and higher | – | 
| Europe \(Frankfurt\) | Version 3\.01\.0 and higher | Version 2\.07 and higher | – | 
| Europe \(Ireland\) | Version 3\.01\.0 and higher | Version 2\.07 and higher | – | 
| Europe \(London\) | Version 3\.01\.0 and higher | Version 2\.07 and higher | – | 
| Europe \(Milan\) | – | – | – | 
| Europe \(Paris\) | Version 3\.01\.0 and higher | Version 2\.07 and higher | – | 
| Europe \(Spain\) | Version 3\.01\.0 and higher | Version 2\.07 and higher | – | 
| Europe \(Stockholm\) | Version 3\.01\.0 and higher | Version 2\.07 and higher | – | 
| Europe \(Zurich\) | Version 3\.01\.0 and higher | Version 2\.07 and higher | – | 
| Middle East \(Bahrain\) | Version 3\.01\.0 and higher | Version 2\.07 and higher | – | 
| Middle East \(UAE\) | Version 3\.01\.0 and higher | Version 2\.07 and higher | – | 
| South America \(São Paulo\) | Version 3\.01\.0 and higher | Version 2\.07 and higher | – | 
| AWS GovCloud \(US\-East\) | Version 3\.01\.0 and higher | Version 2\.07 and higher | – | 
| AWS GovCloud \(US\-West\) | Version 3\.01\.0 and higher | Version 2\.07 and higher | – | 

## Aurora machine learning with Aurora PostgreSQL<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.Aurora_ML.apg"></a>

Aurora machine learning is supported for Aurora PostgreSQL in the AWS Regions listed in the table\. In addition to having your version of Aurora PostgreSQL available, the AWS Region must also support the service that you want to use\. For a list of AWS Regions where Amazon SageMaker is available, see [Amazon SageMaker endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/sagemaker.html) in the *Amazon Web Services General Reference*\. For a list of AWS Regions where Amazon Comprehend is available, see [Amazon Comprehend endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/comprehend.html) in the *Amazon Web Services General Reference*\.

Following are the supported engines and Region availability for Aurora machine learning with Aurora PostgreSQL\.


| Region | Aurora PostgreSQL 14 | Aurora PostgreSQL 13 | Aurora PostgreSQL 12 | Aurora PostgreSQL 11 | Aurora PostgreSQL 10 | 
| --- | --- | --- | --- | --- | --- | 
| US East \(Ohio\) | Version 14\.3 | Version 13\.3 and higher | Version 12\.4 and higher | Version 11\.9, 11\.12 and higher | Version 10\.14 and higher | 
| US East \(N\. Virginia\) | Version 14\.3 | Version 13\.3 and higher | Version 12\.4 and higher | Version 11\.9, 11\.12 and higher | Version 10\.14 and higher | 
| US West \(N\. California\) | Version 14\.3 | Version 13\.3 and higher | Version 12\.4 and higher | Version 11\.9, 11\.12 and higher | Version 10\.14 and higher | 
| US West \(Oregon\) | Version 14\.3 | Version 13\.3 and higher | Version 12\.4 and higher | Version 11\.9, 11\.12 and higher | Version 10\.14 and higher | 
| Africa \(Cape Town\) | – | – | – | – | – | 
| Asia Pacific \(Hong Kong\) | Version 14\.3 | Version 13\.3 and higher | Version 12\.4 and higher | Version 11\.9, 11\.12 and higher | Version 10\.14 and higher | 
| Asia Pacific \(Hyderabad\) | Version 14\.3 | Version 13\.3 and higher | Version 12\.4 and higher | Version 11\.9, 11\.12 and higher | Version 10\.14 and higher | 
| Asia Pacific \(Jakarta\) | Version 14\.3 | Version 13\.3 and higher | Version 12\.4 and higher | Version 11\.9, 11\.12 and higher | Version 10\.14 and higher | 
| Asia Pacific \(Melbourne\) | Version 14\.3 | Version 13\.3 and higher | Version 12\.4 and higher | Version 11\.9, 11\.12 and higher | Version 10\.14 and higher | 
| Asia Pacific \(Mumbai\) | Version 14\.3 | Version 13\.3 and higher | Version 12\.4 and higher | Version 11\.9, 11\.12 and higher | Version 10\.14 and higher | 
| Asia Pacific \(Osaka\) | Version 14\.3 | Version 13\.3 and higher | Version 12\.4 and higher | Version 11\.9, 11\.12 and higher | Version 10\.14 and higher | 
| Asia Pacific \(Seoul\) | Version 14\.3 | Version 13\.3 and higher | Version 12\.4 and higher | Version 11\.9, 11\.12 and higher | Version 10\.14 and higher | 
| Asia Pacific \(Singapore\) | Version 14\.3 | Version 13\.3 and higher | Version 12\.4 and higher | Version 11\.9, 11\.12 and higher | Version 10\.14 and higher | 
| Asia Pacific \(Sydney\) | Version 14\.3 | Version 13\.3 and higher | Version 12\.4 and higher | Version 11\.9, 11\.12 and higher | Version 10\.14 and higher | 
| Asia Pacific \(Tokyo\) | Version 14\.3 | Version 13\.3 and higher | Version 12\.4 and higher | Version 11\.9, 11\.12 and higher | Version 10\.14 and higher | 
| Canada \(Central\) | Version 14\.3 | Version 13\.3 and higher | Version 12\.4 and higher | Version 11\.9, 11\.12 and higher | Version 10\.14 and higher | 
| China \(Beijing\) | Version 14\.3 | Version 13\.3 and higher | Version 12\.4 and higher | Version 11\.9, 11\.12 and higher | Version 10\.14 and higher | 
| China \(Ningxia\) | Version 14\.3 | Version 13\.3 and higher | Version 12\.4 and higher | Version 11\.9, 11\.12 and higher | Version 10\.14 and higher | 
| Europe \(Frankfurt\) | Version 14\.3 | Version 13\.3 and higher | Version 12\.4 and higher | Version 11\.9, 11\.12 and higher | Version 10\.14 and higher | 
| Europe \(Ireland\) | Version 14\.3 | Version 13\.3 and higher | Version 12\.4 and higher | Version 11\.9, 11\.12 and higher | Version 10\.14 and higher | 
| Europe \(London\) | Version 14\.3 | Version 13\.3 and higher | Version 12\.4 and higher | Version 11\.9, 11\.12 and higher | Version 10\.14 and higher | 
| Europe \(Milan\) | – | – | – | – | – | 
| Europe \(Paris\) | Version 14\.3 | Version 13\.3 and higher | Version 12\.4 and higher | Version 11\.9, 11\.12 and higher | Version 10\.14 and higher | 
| Europe \(Spain\) | Version 14\.3 | Version 13\.3 and higher | Version 12\.4 and higher | Version 11\.9, 11\.12 and higher | Version 10\.14 and higher | 
| Europe \(Stockholm\) | Version 14\.3 | Version 13\.3 and higher | Version 12\.4 and higher | Version 11\.9, 11\.12 and higher | Version 10\.14 and higher | 
| Europe \(Zurich\) | Version 14\.3 | Version 13\.3 and higher | Version 12\.4 and higher | Version 11\.9, 11\.12 and higher | Version 10\.14 and higher | 
| Middle East \(Bahrain\) | Version 14\.3 | Version 13\.3 and higher | Version 12\.4 and higher | Version 11\.9, 11\.12 and higher | Version 10\.14 and higher | 
| Middle East \(UAE\) | Version 14\.3 | Version 13\.3 and higher | Version 12\.4 and higher | Version 11\.9, 11\.12 and higher | Version 10\.14 and higher | 
| South America \(São Paulo\) | Version 14\.3 | Version 13\.3 and higher | Version 12\.4 and higher | Version 11\.9, 11\.12 and higher | Version 10\.14 and higher | 
| AWS GovCloud \(US\-East\) | Version 14\.3 | Version 13\.3 and higher | Version 12\.4 and higher | Version 11\.9, 11\.12 and higher | Version 10\.14 and higher | 
| AWS GovCloud \(US\-West\) | Version 14\.3 | Version 13\.3 and higher | Version 12\.4 and higher | Version 11\.9, 11\.12 and higher | Version 10\.14 and higher | 