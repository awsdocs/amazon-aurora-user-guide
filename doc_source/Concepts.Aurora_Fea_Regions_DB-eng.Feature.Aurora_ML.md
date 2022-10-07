# Aurora machine learning<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.Aurora_ML"></a>

By using Amazon Aurora machine learning, you can integrate your Aurora DB cluster with Amazon Comprehend or Amazon SageMaker, depending on your needs\. Amazon Comprehend and SageMaker each support different machine learning use cases\. Amazon Comprehend is a *natural language processing* \(NLP\) service that's used to extract insights from documents\. By using Aurora machine learning with Amazon Comprehend, you can determine the sentiment of text in your database tables\. SageMaker is a full\-featured *machine learning* service\. Data scientists use Amazon SageMaker to build, train, and test machine learning models for a variety of inference tasks, such as fraud detection\. By using Aurora machine learning with SageMaker, database developers can invoke the SageMaker functionality in SQL code\.

Not all AWS Regions support both Amazon Comprehend and SageMaker, and only certain AWS Regions support Aurora machine learning and thus provide access to these services from an Aurora DB cluster\. The integration process for Aurora machine learning also differs by database engine\. For more information, see [Using machine learning capabilities with Amazon Aurora](aurora-ml.md)\.

**Topics**
+ [Aurora machine learning with Aurora MySQL](#Concepts.Aurora_Fea_Regions_DB-eng.Feature.Aurora_ML.ams)
+ [Aurora machine learning with Aurora PostgreSQL](#Concepts.Aurora_Fea_Regions_DB-eng.Feature.Aurora_ML.apg)

## Aurora machine learning with Aurora MySQL<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.Aurora_ML.ams"></a>

Aurora machine learning is supported for Aurora MySQL in the AWS Regions listed in the table\. In addition to having your version of Aurora MySQL available, the AWS Region must also support the service that you want to use\. For a list of AWS Regions where Amazon SageMaker is available, see [Amazon SageMaker endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/sagemaker.html) in the *Amazon Web Services General Reference*\. For a list of AWS Regions where Amazon Comprehend is available, see [Amazon Comprehend endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/comprehend.html) in the *Amazon Web Services General Reference*\. 


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

Aurora machine learning is supported for Aurora PostgreSQL in the AWS Regions listed in the table\. In addition to having your version of Aurora PostgreSQL available, the AWS Region must also support the service that you want to use\. For a list of AWS Regions where Amazon SageMaker is available, see [Amazon SageMaker endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/sagemaker.html) in the *Amazon Web Services General Reference*\. For a list of AWS Regions where Amazon Comprehend is available, see [Amazon Comprehend endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/comprehend.html) in the *Amazon Web Services General Reference*\.


| Region | Aurora PostgreSQL 11 | Aurora PostgreSQL 12 | Aurora PostgreSQL 13 | Aurora PostgreSQL 14 | 
| --- | --- | --- | --- | --- | 
| US East \(Ohio\) | Version 11\.9, 11\.12 and higher | Version 12\.7 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| US East \(N\. Virginia\) | Version 11\.9, 11\.12 and higher | Version 12\.7 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| US West \(N\. California\) | Version 11\.9, 11\.12 and higher | Version 12\.7 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| US West \(Oregon\) | Version 11\.9, 11\.12 and higher | Version 12\.7 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Africa \(Cape Town\) | – | – | – | – | 
| Asia Pacific \(Hong Kong\) | Version 11\.9, 11\.12 and higher | Version 12\.7 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Asia Pacific \(Jakarta\) | Version 11\.9, 11\.12 and higher | Version 12\.7 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Asia Pacific \(Mumbai\) | Version 11\.9, 11\.12 and higher | Version 12\.7 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Asia Pacific \(Osaka\) | Version 11\.9, 11\.12 and higher | Version 12\.7 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Asia Pacific \(Seoul\) | Version 11\.9, 11\.12 and higher | Version 12\.7 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Asia Pacific \(Singapore\) | Version 11\.9, 11\.12 and higher | Version 12\.7 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Asia Pacific \(Sydney\) | Version 11\.9, 11\.12 and higher | Version 12\.7 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Asia Pacific \(Tokyo\) | Version 11\.9, 11\.12 and higher | Version 12\.7 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Canada \(Central\) | Version 11\.9, 11\.12 and higher | Version 12\.7 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| China \(Beijing\) | Version 11\.9, 11\.12 and higher | Version 12\.7 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| China \(Ningxia\) | Version 11\.9, 11\.12 and higher | Version 12\.7 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Europe \(Frankfurt\) | Version 11\.9, 11\.12 and higher | Version 12\.7 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Europe \(Ireland\) | Version 11\.9, 11\.12 and higher | Version 12\.7 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Europe \(London\) | Version 11\.9, 11\.12 and higher | Version 12\.7 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Europe \(Milan\) | – | – | – | – | 
| Europe \(Paris\) | Version 11\.9, 11\.12 and higher | Version 12\.7 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Europe \(Stockholm\) | Version 11\.9, 11\.12 and higher | Version 12\.7 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Middle East \(Bahrain\) | Version 11\.9, 11\.12 and higher | Version 12\.7 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| Middle East \(UAE\) | Version 11\.9, 11\.12 and higher | Version 12\.7 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| South America \(São Paulo\) | Version 11\.9, 11\.12 and higher | Version 12\.7 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| AWS GovCloud \(US\-East\) | Version 11\.9, 11\.12 and higher | Version 12\.7 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 
| AWS GovCloud \(US\-West\) | Version 11\.9, 11\.12 and higher | Version 12\.7 and higher | Version 13\.3 and higher | Version 14\.3 and higher | 