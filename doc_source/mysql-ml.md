# Using Amazon Aurora machine learning with Aurora MySQL<a name="mysql-ml"></a>

By using Amazon Aurora machine learning with your Aurora MySQL DB cluster, you can use Amazon Comprehend or Amazon SageMaker or both, depending on your needs\. Amazon Comprehend and SageMaker each support different machine learning use cases, as follows\.

**Amazon Comprehend**  
Amazon Comprehend is a managed natural language processing \(NLP\) service that's used to extract insights from documents\. With Amazon Comprehend, you can deduce sentiment based on the content of documents, by analyzing entities, key phrases, language, and other features\. To learn more, see [What is Amazon Comprehend?](https://docs.aws.amazon.com/comprehend/latest/dg/what-is.html) in the *Amazon Comprehend Developer Guide*\. 

**SageMaker**  
Amazon SageMaker is a fully managed machine learning service\. Data scientists and developers use Amazon SageMaker to build, train, and test machine learning models for a variety of inference tasks, such as fraud detection and product recommendation\. When a machine learning model is ready for use in production, it can be deployed to the Amazon SageMaker hosted environment\. For more information, see [What Is Amazon SageMaker?](https://docs.aws.amazon.com/sagemaker/latest/dg/whatis.html) in the *Amazon SageMaker Developer Guide*\.  

Using Amazon Comprehend with your Aurora DB cluster has less preliminary setup than using SageMaker\. If you're new to both AWS machine learning and to Aurora machine learning, we recommend that you start by exploring Amazon Comprehend\. 

Aurora machine learning is supported in certain AWS Regions and for specific versions of Aurora MySQL only\. Before trying to set up Aurora machine learning, check availability for your Aurora MySQL version and your Region\. For details, see [Aurora machine learning with Aurora MySQL](Concepts.Aurora_Fea_Regions_DB-eng.Feature.Aurora_ML.md#Concepts.Aurora_Fea_Regions_DB-eng.Feature.Aurora_ML.ams)\. 

**Topics**
+ [Requirements for using Aurora machine learning with Aurora MySQL](#aurora-ml-prereqs)
+ [Supported features and limitations of Aurora machine learning with Aurora MySQL](#aurora-ml-limitations)
+ [Setting up your Aurora MySQL DB cluster to use Aurora machine learning](#aurora-ml-setting-up-access)
+ [Using Amazon Comprehend with your Aurora MySQL DB cluster](#using-amazon-comprehend-for-sentiment-detection)
+ [Using SageMaker with your Aurora MySQL DB cluster](#using-amazon-sagemaker-to-run-your-own-ml-models)
+ [Performance considerations for using Aurora machine learning with Aurora MySQL](#aurora-ml-performance)
+ [Monitoring Aurora machine learning](#aurora-ml-monitoring)

## Requirements for using Aurora machine learning with Aurora MySQL<a name="aurora-ml-prereqs"></a>

AWS machine learning services are managed services that are set up and run in their own production environments\. Aurora machine learning supports integration with Amazon Comprehend and SageMaker\. Before trying to set up your Aurora MySQL DB cluster to use Aurora machine learning, be sure you understand the following requirements and prerequisites\. 
+ The Amazon Comprehend and SageMaker services must be running in the same AWS Region as your Aurora MySQL DB cluster\. You can't use Amazon Comprehend or SageMaker services from an Aurora MySQL DB cluster in a different Region\.
+ If your Aurora MySQL DB cluster is in a different virtual public cloud \(VPC\) based on the Amazon VPC service than your Amazon Comprehend and SageMaker services, the VPC's Security group needs to allow outbound connections to the target Aurora machine learning service\. For more information, see [Enabling network communication from Amazon Aurora MySQL to other AWS services](AuroraMySQL.Integrating.Authorizing.Network.md)\. 
+ Your Aurora MySQL DB cluster needs to use a custom DB cluster parameter group\. At the end of the setup process for each Aurora machine learning service that you want to use, you add the Amazon Resource Name \(ARN\) of the associated IAM role that was created for the service\. We recommend that you create a custom DB cluster parameter group for your Aurora MySQL in advance and configure your Aurora MySQL DB cluster to use it so that it's ready for you to modify at the end of the setup process\.
+ For SageMaker, the machine learning components that you want to use for inferences must be setup and ready to use\. During the configuration process for your Aurora MySQL DB cluster, you need to have the Amazon Resource Name \(ARN\) of the SageMaker endpoint available\. The data scientists on your team are likely best able to handle working with SageMaker to prepare the models and handle the other such tasks\. To get started with Amazon SageMaker, see [Get Started with Amazon SageMaker](https://docs.aws.amazon.com/sagemaker/latest/dg/gs.html)\. For more information about inferences and endpoints, see [Real\-time inference](https://docs.aws.amazon.com/sagemaker/latest/dg/realtime-endpoints.html)\. 
+ To use SageMaker with your own training data, you need to set up an Amazon Simple Storage Service \(Amazon S3\) bucket as part of your Aurora MySQL configuration for Aurora machine learning\. To do so, you follow the same general process as for setting up the SageMaker integration\. For a summary of this optional setup process, see [Setting up your Aurora MySQL DB cluster to use Amazon S3 for SageMaker \(Optional\)](#aurora-ml-setting-up-access-sagemaker-advanced-s3)\. 
+ For Aurora global databases, you set up the Aurora machine learning services that you want to use in all AWS Regions that make up your Aurora global database\. For example, if you want to use Aurora machine learning with SageMaker for your Aurora global database, you do as follows for every Aurora MySQL DB cluster in every AWS Region\. 
  + Set up the Amazon SageMaker services with the same SageMaker training models and endpoints\. These must also use the same names\. 
  + Create the IAM roles as detailed in [Setting up your Aurora MySQL DB cluster to use Aurora machine learning](#aurora-ml-setting-up-access)\.
  + Add the ARN of the IAM role to the custom DB cluster parameter group for each Aurora MySQL DB cluster in every AWS Region\. 

  These tasks require that Aurora machine learning is available for your version of Aurora MySQL in all AWS Regions that make up your Aurora global database\. For the list of versions and Regions, see [Aurora machine learning with Aurora MySQL](Concepts.Aurora_Fea_Regions_DB-eng.Feature.Aurora_ML.md#Concepts.Aurora_Fea_Regions_DB-eng.Feature.Aurora_ML.ams)\.

## Supported features and limitations of Aurora machine learning with Aurora MySQL<a name="aurora-ml-limitations"></a>

When using Aurora MySQL with Aurora machine learning, the following limitations apply\. 
+ **Supported SageMaker algorithm formats** – Aurora MySQL supports any SageMaker endpoint that read and write comma\-separated value \(CSV\) format, through a `ContentType` of `text/csv`\. Currently, this format is accepted by the following built\-in SageMaker algorithms\. 
  + Linear Learner
  + Random Cut Forest
  + XGBoost 

  To learn more about these algorithms, see [Choose an Algorithm](https://docs.aws.amazon.com/sagemaker/latest/dg/algorithms-choose.html) in the *Amazon SageMaker Developer Guide*\.
+ **Aurora MySQL stored function limitations** – Aurora machine learning functions aren't compatible with the binary log \(binlog\) format\. This affects your Aurora machine learning integration as follows\. 
  + Stored functions that call tables with generated\-always columns aren't supported\. This applies to any Aurora MySQL stored function\. To learn more about this column type, see [CREATE TABLE and Generated Columns](https://dev.mysql.com/doc/refman/5.7/en/create-table-generated-columns.html) in the MySQL documentation\.
  + The setting `--binlog-format=STATEMENT` throws an exception for calls to Aurora machine learning functions\. Aurora machine learning functions are nondeterministic, and nondeterministic stored functions aren't compatible with the binlog format\. 

  For more information about the binlog format, see [Binary Logging Formats](https://dev.mysql.com/doc/refman/5.7/en/binary-log-formats.html) in the MySQL documentation\.

## Setting up your Aurora MySQL DB cluster to use Aurora machine learning<a name="aurora-ml-setting-up-access"></a>

As mentioned in [Using Amazon Aurora machine learning with Aurora MySQL](#mysql-ml), Amazon Comprehend and SageMaker support different machine learning use cases\. In the following topics, you can find separate setup procedures for each of these Aurora machine learning services\. 

**Contents**
+ [Setting up your Aurora MySQL DB cluster to use Amazon Comprehend](#aurora-ml-setting-up-access-comprehend)
+ [Setting up your Aurora MySQL DB cluster to use SageMaker](#aurora-ml-setting-up-access-sagemaker)
  + [Setting up your Aurora MySQL DB cluster to use Amazon S3 for SageMaker \(Optional\)](#aurora-ml-setting-up-access-sagemaker-advanced-s3)
+ [Granting database users access to Aurora machine learning](#aurora-ml-sql-privileges)
  + [Granting access to Amazon Comprehend functions](#aurora-ml-sql-privileges.cmp)
  + [Granting access to SageMaker functions](#aurora-ml-sql-privileges.sm)

### Setting up your Aurora MySQL DB cluster to use Amazon Comprehend<a name="aurora-ml-setting-up-access-comprehend"></a>

Aurora machine learning relies on AWS Identity and Access Management roles and policies to allow your Aurora MySQL DB cluster to access and use the Amazon Comprehend services\. The following procedure automatically creates an IAM role and policy for your cluster so that it can use Amazon Comprehend\.

**To set up your Aurora MySQL DB cluster to use Amazon Comprehend**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose **Databases** from the Amazon RDS navigation menu and then choose the Aurora MySQL DB cluster that you want to connect to Amazon Comprehend services\. 

1. Choose the **Connectivity & security** tab\. 

1. Scroll to the **Manage IAM roles** section, and then choose **Select a service to connect to this cluster**\. Choose **Amazon Comprehend** from the selector, and then choose **Connect service**\.   
![\[Image showing Amazon Comprehend chosen for the Aurora MySQL DB cluster.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/ams-ml-cmp-iam-role.png)

1. The **Connect cluster to Amazon Comprehend** dialog doesn't require any additional information\. However, you might see a message notifying you that the integration between Aurora and Amazon Comprehend is currently in preview\. Be sure to read the message before you continue\. You can choose Cancel if you prefer not to proceed\.

1. Choose **Connect service** to complete the integration process\. 

   Aurora creates the IAM role\. It also creates the policy that allows the Aurora MySQL to use Amazon Comprehend services and attaches the policy to the role\. When the process completes, you can find the role in the **Current IAM roles for this cluster** list as shown in the following image\.   
![\[Completed IAM integration for using Amazon Comprehend with Aurora MySQL showing the Active role.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/ams-ml-cmp-iam-role-4.png)

   You need to add the ARN of this IAM role to the `aws_default_comprehend_role` parameter of your Aurora MySQL DB cluster\. If your Aurora MySQL DB cluster doesn't use a custom DB cluster parameter group, you need to create one to use with your Aurora MySQL DB cluster to complete the integration\. For more information, see [Working with DB cluster parameter groups](USER_WorkingWithDBClusterParamGroups.md)\. 

   After creating your custom DB cluster parameter group and associating it with your Aurora MySQL DB cluster, you can continue following these steps\.

   If your cluster uses a custom DB cluster parameter group, do as follows\.

   1. In the Amazon RDS Console, open the **Configuration** tab of your Aurora MySQL DB cluster\.

   1. Locate the DB cluster parameter group configured for your cluster, and choose the link to open your custom DB cluster parameter group\. Choose **Edit parameters**\. 

   1. Find the `aws_default_comprehend_role` parameter in your custom DB cluster parameter group\.

   1. In the Value field, enter the ARN of the IAM role\. 

   1. Choose **Save changes** to save the setting\. In the following image, you can find an example\.   
![\[Adding the ARN of the IAM role to the Aurora MySQL's custom DB cluster parameter group.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/ams-cmp-add-arn-to-custom-db-cluster-params.png)

   Reboot the primary instance of your Aurora MySQL DB cluster so that this parameter setting takes effect\. 

The IAM integration for Amazon Comprehend is complete\. Continue setting up your Aurora MySQL DB cluster to work with Amazon Comprehend by granting access to the appropriate database users\. 

### Setting up your Aurora MySQL DB cluster to use SageMaker<a name="aurora-ml-setting-up-access-sagemaker"></a>

The following procedure automatically creates the IAM role and policy for your Aurora MySQL DB cluster so that it can use SageMaker\. Before trying to follow this procedure, be sure that you have the SageMaker endpoint available so that you can enter it when needed\. Typically, data scientists on your team would do the work to produce an endpoint that you can use from your Aurora MySQL DB cluster\. You can find such endpoints in the [SageMaker console](https://console.aws.amazon.com/sagemaker/home)\. In the navigation pane, open the **Inference** menu and choose **Endpoints**\. In the following image, you can find an example\.

![\[Image showing SageMaker chosen for the Aurora MySQL DB cluster.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/ams-sm-endpoint-arn.png)

**To set up your Aurora MySQL DB cluster to use SageMaker**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose **Databases** from the Amazon RDS navigation menu and then choose the Aurora MySQL DB cluster that you want to connect to SageMaker services\. 

1. Choose the **Connectivity & security** tab 

1. Scroll to the **Manage IAM roles** section, and then choose **Select a service to connect to this cluster**\. Choose **SageMaker** from the selector\.  
![\[Image showing SageMaker chosen for the Aurora MySQL DB cluster.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/ams-sm-iam-role-1.png)

1. Choose **Connect service**\. 

1. In the **Connect cluster to SageMaker** dialog, enter the Amazon Resource Name \(ARN\) of the SageMaker endpoint\.   
![\[Image showing the Amazon Resource Name (ARN) for SageMaker endpoint entered during the configuration process.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/ams-sm-iam-role-3.png)

1. Aurora creates the IAM role\. It also creates the policy that allows the Aurora MySQL to use SageMaker services and attaches the policy to the role\. When the process completes, you can find the role in the **Current IAM roles for this cluster** list\. 

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. Choose **Roles** from the Access management section of the AWS Identity and Access Management navigation menu\.

1. Find the role from among those listed\. Its name uses the following pattern\.

   ```
   rds-sagemaker-your-cluster-name-role-auto-generated-digits
   ```

1. Open the role's Summary page and locate the ARN\. Note the ARN or copy it using the copy widget\.

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose your Aurora MySQL DB cluster, and then choose its **Configuration** tab\. 

1. Locate the DB cluster parameter group, and choose the link to open your custom DB cluster parameter group\. Find the `aws_default_sagemaker_role` parameter and enter the ARN of the IAM role in the Value field and Save the setting\. 

1. Reboot the primary instance of your Aurora MySQL DB cluster so that this parameter setting takes effect\. 

The IAM setup is now complete\. Continue setting up your Aurora MySQL DB cluster to work with SageMaker by granting access to the appropriate database users\.

If you want to use your SageMaker models for training rather than using pre\-built SageMaker components, you also need to add the Amazon S3 bucket to your Aurora MySQL DB cluster, as outlined in the [Setting up your Aurora MySQL DB cluster to use Amazon S3 for SageMaker \(Optional\)](#aurora-ml-setting-up-access-sagemaker-advanced-s3) that follows\. 

#### Setting up your Aurora MySQL DB cluster to use Amazon S3 for SageMaker \(Optional\)<a name="aurora-ml-setting-up-access-sagemaker-advanced-s3"></a>

To use SageMaker with your own models rather than using the pre\-built components provided by SageMaker, you need to set up an Amazon Simple Storage Service \(Amazon S3\) bucket for Aurora MySQL DB cluster to use\. For more information about creating an Amazon S3 bucket, see [Creating a bucket](https://docs.aws.amazon.com/AmazonS3/latest/userguide/create-bucket-overview.html) in the *Amazon Simple Storage Service User Guide*\. 

**To set up your Aurora MySQL DB cluster to use an Amazon S3 bucket for SageMaker**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose **Databases** from the Amazon RDS navigation menu and then choose the Aurora MySQL DB cluster that you want to connect to SageMaker services\. 

1. Choose the **Connectivity & security** tab\. 

1. Scroll to the **Manage IAM roles** section, and then choose **Select a service to connect to this cluster**\. Choose **Amazon S3** from the selector\.  
![\[Choosing Amazon S3 as the service to integrate with the Aurora MySQL DB cluster.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/ams-ml-s3-1.png)

1. Choose **Connect service**\. 

1. In the **Connect cluster to Amazon S3** dialog, enter the Amazon Resource Name \(ARN\) of the Amazon S3 bucket, as shown in the following image\.  
![\[Image of ARN for Amazon S3 bucket specified for the Aurora MySQL DB cluster.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/ams-ml-s3-2.png)

1. Choose **Connect service** to complete this process\.

For more information about using Amazon S3 buckets with SageMaker, see [Specify an Amazon S3 Bucket to Upload Training Datasets and Store Output Data](https://docs.aws.amazon.com/sagemaker/latest/dg/automatic-model-tuning-ex-bucket.html) in the *Amazon SageMaker Developer Guide*\. To learn more about working with SageMaker, see [Get Started with Amazon SageMaker Notebook Instances](https://docs.aws.amazon.com/sagemaker/latest/dg/gs-console.html)in the in the *Amazon SageMaker Developer Guide*\. 

### Granting database users access to Aurora machine learning<a name="aurora-ml-sql-privileges"></a>

Aurora machine learning includes built\-in functions for working with Amazon Comprehend and SageMaker\. Database users must be granted permission to invoke these built\-in functions\. How you grant permission depends on the version of MySQL that you use for your Aurora MySQL DB cluster, as outlined in the following\.  How you do so depends on the version of MySQL that your Aurora MySQL DB cluster uses\.
+ For Aurora MySQL 3 \(MySQL 8\.0 compatible\), database users must be granted the appropriate *database role*\. For more information about MySQL 3 database roles, see [Using Roles](https://dev.mysql.com/doc/refman/8.0/en/roles.html) in the MySQL 8\.0 Reference Manual\. 
+ For Aurora MySQL 2 \(MySQL 5\.7 compatible\), database users are granted *privileges*\. For more information, see [Access Control and Account Management](https://dev.mysql.com/doc/refman/5.7/en/access-control.html) in the MySQL 5\.7 Reference Manual\.

In the following table you can find the role or privilege that database users need to work with Amazon Comprehend and SageMaker functions\. 


| Aurora MySQL 3 \(MySQL 8\.0\) \(role\) | Aurora MySQL 2 \(MySQL 5\.7\) \(privilege\) | 
| --- | --- | 
|  AWS\_COMPREHEND\_ACCESS  |  INVOKE COMPREHEND  | 
|  AWS\_SAGEMAKER\_ACCESS  |  INVOKE SAGEMAKER  | 

#### Granting access to Amazon Comprehend functions<a name="aurora-ml-sql-privileges.cmp"></a>

To give database users access to Amazon Comprehend functions, use the appropriate statement for your Aurora MySQL version\.

****
+ Aurora MySQL version 3 \(MySQL 8\.0 compatible\)

  ```
  GRANT AWS_COMPREHEND_ACCESS TO user@domain-or-ip-address
  ```
+ Aurora MySQL version 2 \(MySQL 5\.7 compatible\)

  ```
  GRANT INVOKE COMPREHEND ON *.* TO user@domain-or-ip-address
  ```

The Amazon Comprehend functions are now available for use\. For usage examples, see [Using Amazon Comprehend with your Aurora MySQL DB cluster](#using-amazon-comprehend-for-sentiment-detection)\. 

#### Granting access to SageMaker functions<a name="aurora-ml-sql-privileges.sm"></a>

To give database users access to SageMaker functions, use the appropriate statement for your Aurora MySQL version\.
+ Aurora MySQL version 3 \(MySQL 8\.0 compatible\)

  ```
  GRANT AWS_SAGEMAKER_ACCESS TO user@domain-or-ip-address
  ```
+ Aurora MySQL version 2 \(MySQL 5\.7 compatible\)

  ```
  GRANT INVOKE SAGEMAKER ON *.* TO user@domain-or-ip-address
  ```

Database users also need to be granted `EXECUTE` permissions for the functions that you create for working with SageMaker\. Suppose that you created two functions, `db1.anomoly_score` and `db2.company_forecasts`, to invoke the services of your SageMaker endpoint\. You grant execute privileges as shown following\.

```
GRANT EXECUTE ON FUNCTION db1.anomaly_score TO user1@domain-or-ip-address1
GRANT EXECUTE ON FUNCTION db2.company_forecasts TO user2@domain-or-ip-address2
```

The SageMaker functions are now available for use\. For usage examples, see [Using SageMaker with your Aurora MySQL DB cluster](#using-amazon-sagemaker-to-run-your-own-ml-models)\. 

## Using Amazon Comprehend with your Aurora MySQL DB cluster<a name="using-amazon-comprehend-for-sentiment-detection"></a>

For Aurora MySQL, Aurora machine learning provides the following two built\-in functions for working with Amazon Comprehend and your text data\. You provide the text to analyze \(`input_data`\) and specify the language \(`language_code`\)\. 

**aws\_comprehend\_detect\_sentiment**  
This function identifies the text as having a positive, negative, neutral, or mixed emotional posture\. This function's reference documentation is as follows\.  

```
aws_comprehend_detect_sentiment(
  input_text,
  language_code
  [,max_batch_size]
)
```
To learn more, see [Sentiment](https://docs.aws.amazon.com/comprehend/latest/dg/how-sentiment.html) in the *Amazon Comprehend Developer Guide*\.

**aws\_comprehend\_detect\_sentiment\_confidence**  
This function measures the confidence level of the sentiment detected for a given text\. It returns a value \(type, `double`\) that indicates the confidence of the sentiment assigned by the aws\_comprehend\_detect\_sentiment function to the text\. Confidence is a statistical metric between 0 and 1\. The higher the confidence level, the more weight you can give the result\. A summary of the function's documentation is as follows\.  

```
aws_comprehend_detect_sentiment_confidence(
  input_text,
  language_code
  [,max_batch_size]
)
```

In both functions \(aws\_comprehend\_detect\_sentiment\_confidence, aws\_comprehend\_detect\_sentiment\) the `max_batch_size` uses a default value of 25 if none is specified\. Batch size should always be greater than 0\. You can use `max_batch_size` to tune the performance of the Amazon Comprehend function calls\. A large batch size trades off faster performance for greater memory usage on the Aurora MySQL DB cluster\. For more information, see [Performance considerations for using Aurora machine learning with Aurora MySQL](#aurora-ml-performance)\. 

 For more information about parameters and return types for the sentiment detection functions in Amazon Comprehend, see [DetectSentiment](https://docs.aws.amazon.com/comprehend/latest/dg/API_DetectSentiment.html) 

**Example: A simple query using Amazon Comprehend functions**  
Here's an example of a simple query that invokes these two functions to see how happy your customers are with your support team\. Suppose you have a database table \(`support`\) that stores customer feedback after each request for help\. This example query applies both built\-in functions to the text in the `feedback` column of the table and outputs the results\. The confidence values returned by the function are doubles between 0\.0 and 1\.0\. For more readable output, this query rounds the results to 6 decimal points\. For easier comparisons, this query also sorts the results in descending order, from the result having the highest degree of confidence, first\.   

```
SELECT feedback AS 'Customer feedback',
    aws_comprehend_detect_sentiment(feedback, 'en') AS Sentiment,
    ROUND(aws_comprehend_detect_sentiment_confidence(feedback, 'en'), 6)
    AS Confidence FROM support
    ORDER BY Confidence DESC;
+----------------------------------------------------------+-----------+------------+
| Customer feedback                                        | Sentiment | Confidence |
+----------------------------------------------------------+-----------+------------+
| Thank you for the excellent customer support!            | POSITIVE  |   0.999771 |
| The latest version of this product stinks!               | NEGATIVE  |   0.999184 |
| Your support team is just awesome! I am blown away.      | POSITIVE  |   0.997774 |
| Your product is too complex, but your support is great.  | MIXED     |   0.957958 |
| Your support tech helped me in fifteen minutes.          | POSITIVE  |   0.949491 |
| My problem was never resolved!                           | NEGATIVE  |   0.920644 |
| When will the new version of this product be released?   | NEUTRAL   |   0.902706 |
| I cannot stand that chatbot.                             | NEGATIVE  |   0.895219 |
| Your support tech talked down to me.                     | NEGATIVE  |   0.868598 |
| It took me way too long to get a real person.            | NEGATIVE  |   0.481805 |
+----------------------------------------------------------+-----------+------------+
 10 rows in set (0.1898 sec)
```

**Example: Determining the average sentiment for text above a specific confidence level**  
A typical Amazon Comprehend query looks for rows where the sentiment is a certain value, with a confidence level greater than a certain number\. For example, the following query shows how you can determine the average sentiment of documents in your database\. The query considers only documents where the confidence of the assessment is at least 80%\.   

```
SELECT AVG(CASE aws_comprehend_detect_sentiment(productTable.document, 'en')
  WHEN 'POSITIVE' THEN 1.0
  WHEN 'NEGATIVE' THEN -1.0
  ELSE 0.0 END) AS avg_sentiment, COUNT(*) AS total
FROM productTable
WHERE productTable.productCode = 1302 AND
    aws_comprehend_detect_sentiment_confidence(productTable.document, 'en') >= 0.80;
```

## Using SageMaker with your Aurora MySQL DB cluster<a name="using-amazon-sagemaker-to-run-your-own-ml-models"></a>

To use SageMaker functionality from your Aurora MySQL DB cluster, you need to create stored functions that embed your calls to the SageMaker endpoint and its inference features\. You do so by using MySQL's `CREATE FUNCTION` in generally the same way that you do for other processing tasks on your Aurora MySQL DB cluster\. In other words, you create user\-defined functions using the familiar MySQL data definition language \(DDL\) statements for stored functions\. 

To use models deployed in SageMaker for inference, you create user\-defined functions using the familiar MySQL data definition language \(DDL\) statements for stored functions\. Each stored function represents the SageMaker endpoint hosting the model\. When you define such a function, you specify the input parameters to the model, the specific SageMaker endpoint to invoke, and the return type\. The function returns the inference computed by the SageMaker endpoint after applying the model to the input parameters\.

 All Aurora machine learning stored functions return numeric types or `VARCHAR`\. You can use any numeric type except `BIT`\. Other types, such as `JSON`, `BLOB`, `TEXT`, and `DATE` aren't allowed\. 

In the following example, you can find syntax pattern for the `CREATE FUNCTION` for working with SageMaker\. 

```
CREATE FUNCTION function_name (
     arg1 type1, 
     arg2 type2, ...)
     [DEFINER = user]
  RETURNS mysql_type
  [SQL SECURITY { DEFINER | INVOKER } ]
  ALIAS AWS_SAGEMAKER_INVOKE_ENDPOINT
  ENDPOINT NAME 'endpoint_name'
  [MAX_BATCH_SIZE max_batch_size];
```

This is an extension of the regular `CREATE FUNCTION` DDL statement\. In the `CREATE FUNCTION` statement that defines the SageMaker function, you don't specify a function body\. Instead, you specify the keyword `ALIAS` where the function body usually goes\. Currently, Aurora machine learning only supports `aws_sagemaker_invoke_endpoint` for this extended syntax\. You must specify the `endpoint_name` parameter\. An SageMaker endpoint can have different characteristics for each model\. 

**Note**  
For more information about `CREATE FUNCTION`, see [CREATE PROCEDURE and CREATE FUNCTION Statements](https://dev.mysql.com/doc/refman/8.0/en/create-procedure.html) in the MySQL 8\.0 Reference Manual\.

The `max_batch_size` parameter is optional\. By default, maximum batch size is 10,000\. You can use this parameter in your function to restrict the maximum number of inputs processed in a batched request to SageMaker\. The `max_batch_size` parameter can help to avoid an error caused by inputs that are too large, or to make SageMaker return a response more quickly\. This parameter affects the size of an internal buffer used for SageMaker request processing\. Specifying too large a value for `max_batch_size` might cause substantial memory overhead on your DB instance\.

We recommend that you leave the `MANIFEST` setting at its default value of `OFF`\. Although you can use the `MANIFEST ON` option, some SageMaker features can't directly use the CSV exported with this option\. The manifest format is not compatible with the expected manifest format from SageMaker\. 

 You create a separate stored function for each of your SageMaker models\. This mapping of functions to models is required because an endpoint is associated with a specific model, and each model accepts different parameters\. Using SQL types for the model inputs and the model output type helps to avoid type conversion errors passing data back and forth between the AWS services\. You can control who can apply the model\. You can also control the runtime characteristics by specifying a parameter representing the maximum batch size\. 

Currently, all Aurora machine learning functions have the `NOT DETERMINISTIC` property\. If you don't specify that property explicitly, Aurora sets `NOT DETERMINISTIC` automatically\. This requirement is because the SageMaker model can be changed without any notification to the database\. If that happens, calls to an Aurora machine learning function might return different results for the same input within a single transaction\. 

You can't use the characteristics `CONTAINS SQL`, `NO SQL`, `READS SQL DATA`, or `MODIFIES SQL DATA` in your `CREATE FUNCTION` statement\. 

Following is an example usage of invoking an SageMaker endpoint to detect anomalies\. There is an SageMaker endpoint `random-cut-forest-model`\. The corresponding model is already trained by the `random-cut-forest` algorithm\. For each input, the model returns an anomaly score\. This example shows the data points whose score is greater than 3 standard deviations \(approximately the 99\.9th percentile\) from the mean score\. 

```
CREATE FUNCTION anomaly_score(value real) returns real
  alias aws_sagemaker_invoke_endpoint endpoint name 'random-cut-forest-model-demo';

set @score_cutoff = (select avg(anomaly_score(value)) + 3 * std(anomaly_score(value)) from nyc_taxi);

select *, anomaly_detection(value) score from nyc_taxi
  where anomaly_detection(value) > @score_cutoff;
```

### Character set requirement for SageMaker functions that return strings<a name="note-character-set-of-ml-functions-return-type"></a>

 We recommend specifying a character set of `utf8mb4` as the return type for your SageMaker functions that return string values\. If that isn't practical, use a large enough string length for the return type to hold a value represented in the `utf8mb4` character set\. The following example shows how to declare the `utf8mb4` character set for your function\. 

```
CREATE FUNCTION my_ml_func(...) RETURNS VARCHAR(5) CHARSET utf8mb4 ALIAS ...
```

Currently, each SageMaker function that returns a string uses the character set `utf8mb4` for the return value\. The return value uses this character set even if your SageMaker function declares a different character set for its return type implicitly or explicitly\. If your SageMaker function declares a different character set for the return value, the returned data might be silently truncated if you store it in a table column that isn't long enough\. For example, a query with a `DISTINCT` clause creates a temporary table\. Thus, the SageMaker function result might be truncated due to the way strings are handled internally during a query\. 

### Exporting data to Amazon S3 for SageMaker model training \(Advanced\)<a name="exporting-data-to-s3-for-model-training"></a>

We recommend that you get started with Aurora machine learning and SageMaker by using some of the provided algorithms, and that the data scientists on your team provide you with the SageMaker endpoints that you can use with your SQL code\. In the following, you can find minimal information about using your own Amazon S3 bucket with your your own SageMaker models and your Aurora MySQL DB cluster\. 

Machine learning consists of two major steps: training, and inference\. To train SageMaker models, you export data to an Amazon S3 bucket\. The Amazon S3 bucket is used by a Jupyter SageMaker notebook instance to train your model before it is deployed\. You can use the `SELECT INTO OUTFILE S3` statement to query data from an Aurora MySQL DB cluster and save it directly into text files stored in an Amazon S3 bucket\. Then the notebook instance consumes the data from the Amazon S3 bucket for training\. 

 Aurora machine learning extends the existing `SELECT INTO OUTFILE` syntax in Aurora MySQL to export data to CSV format\. The generated CSV file can be directly consumed by models that need this format for training purposes\. 

```
SELECT * INTO OUTFILE S3 's3_uri' [FORMAT {CSV|TEXT} [HEADER]] FROM table_name;
```

 The extension supports the standard CSV format\. 
+  Format `TEXT` is the same as the existing MySQL export format\. This is the default format\. 
+  Format `CSV` is a newly introduced format that follows the specification in [RFC\-4180](https://tools.ietf.org/html/rfc4180)\. 
+  If you specify the optional keyword `HEADER`, the output file contains one header line\. The labels in the header line correspond to the column names from the `SELECT` statement\. 
+  You can still use the keywords `CSV` and `HEADER` as identifiers\. 

 The extended syntax and grammar of `SELECT INTO` is now as follows: 

```
INTO OUTFILE S3 's3_uri'
[CHARACTER SET charset_name]
[FORMAT {CSV|TEXT} [HEADER]]
[{FIELDS | COLUMNS}
  [TERMINATED BY 'string']
  [[OPTIONALLY] ENCLOSED BY 'char']
  [ESCAPED BY 'char']
]
[LINES
  [STARTING BY 'string']
  [TERMINATED BY 'string']
]
```

## Performance considerations for using Aurora machine learning with Aurora MySQL<a name="aurora-ml-performance"></a>

The Amazon Comprehend and SageMaker services do most of the work when invoked by an Aurora machine learning function\. That means that you can scale those resources as needed, independently\. For your Aurora MySQL DB cluster, you can make your function calls as efficient as possible\. Following, you can find some performance considerations to note when working with Aurora machine learning\. 

### Query cache<a name="query-cache"></a>

 The Aurora MySQL query cache doesn't work for Aurora machine learning functions\. Aurora MySQL doesn't store query results in the query cache for any SQL statements that call Aurora machine learning functions\. 

### Batch optimization for Aurora machine learning function calls<a name="ml-batch-optimization"></a>

 The main Aurora machine learning performance aspect that you can influence from your Aurora cluster is the batch mode setting for calls to the Aurora machine learning stored functions\. Machine learning functions typically require substantial overhead, making it impractical to call an external service separately for each row\. Aurora machine learning can minimize this overhead by combining the calls to the external Aurora machine learning service for many rows into a single batch\. Aurora machine learning receives the responses for all the input rows, and delivers the responses, one row at a time, to the query as it runs\. This optimization improves the throughput and latency of your Aurora queries without changing the results\. 

 When you create an Aurora stored function that's connected to an SageMaker endpoint, you define the batch size parameter\. This parameter influences how many rows are transferred for every underlying call to SageMaker\. For queries that process large numbers of rows, the overhead to make a separate SageMaker call for each row can be substantial\. The larger the data set processed by the stored procedure, the larger you can make the batch size\. 

 If the batch mode optimization can be applied to an SageMaker function, you can tell by checking the query plan produced by the `EXPLAIN PLAN` statement\. In this case, the `extra` column in the execution plan includes `Batched machine learning`\. The following example shows a call to an SageMaker function that uses batch mode\. 

```
mysql> CREATE FUNCTION anomaly_score(val real) returns real alias aws_sagemaker_invoke_endpoint endpoint name 'my-rcf-model-20191126';
Query OK, 0 rows affected (0.01 sec)

mysql> explain select timestamp, value, anomaly_score(value) from nyc_taxi;
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+--------------------------+
| id | select_type | table    | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra                    |
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+--------------------------+
|  1 | SIMPLE      | nyc_taxi | NULL       | ALL  | NULL          | NULL | NULL    | NULL |   48 |   100.00 | Batched machine learning |
+----+-------------+----------+------------+------+---------------+------+---------+------+------+----------+--------------------------+
1 row in set, 1 warning (0.01 sec)
```

 When you call one of the built\-in Amazon Comprehend functions, you can control the batch size by specifying the optional `max_batch_size` parameter\. his parameter restricts the maximum number of `input_text` values processed in each batch\. By sending multiple items at once, it reduces the number of round trips between Aurora and Amazon Comprehend\. Limiting the batch size is useful in situations such as a query with a `LIMIT` clause\. By using a small value for `max_batch_size`, you can avoid invoking Amazon Comprehend more times than you have input texts\. 

 The batch optimization for evaluating Aurora machine learning functions applies in the following cases: 
+  Function calls within the select list or the `WHERE` clause of `SELECT` statements\. There are some exceptions, as described following\. 
+  Function calls in the `VALUES` list of `INSERT` and `REPLACE` statements\. 
+  SageMaker functions in `SET` values in `UPDATE` statements\. 

  ```
  INSERT INTO MY_TABLE (col1, col2, col3) VALUES
    (ML_FUNC(1), ML_FUNC(2), ML_FUNC(3)),
    (ML_FUNC(4), ML_FUNC(5), ML_FUNC(6));
  UPDATE MY_TABLE SET col1 = ML_FUNC(col2), SET col3 = ML_FUNC(col4) WHERE ...;
  ```

## Monitoring Aurora machine learning<a name="aurora-ml-monitoring"></a>

You can monitor Aurora machine learning batch operations by querying several global variables, as shown following\. 

```
show status like 'Aurora_ml%';
```

 You can reset the status variables by using a `FLUSH STATUS` statement\. Thus, all of the figures represent totals, averages, and so on, since the last time the variable was reset\. 

`Aurora_ml_logical_response_cnt`  
 The aggregate response count that Aurora MySQL receives from the Aurora machine learning services across all queries run by users of the DB instance\. 

`Aurora_ml_actual_request_cnt`  
 The aggregate request count that Aurora MySQL receives from the Aurora machine learning services across all queries run by users of the DB instance\. 

`Aurora_ml_actual_response_cnt`  
 The aggregate response count that Aurora MySQL receives from the Aurora machine learning services across all queries run by users of the DB instance\. 

`Aurora_ml_cache_hit_cnt`  
 The aggregate internal cache hit count that Aurora MySQL receives from the Aurora machine learning services across all queries run by users of the DB instance\. 

`Aurora_ml_single_request_cnt`  
 The aggregate count of Aurora machine learning functions that are evaluated by non\-batch mode across all queries run by users of the DB instance\. 

 For information about monitoring the performance of the SageMaker operations called from Aurora machine learning functions, see [Monitor Amazon SageMaker ](https://docs.aws.amazon.com/sagemaker/latest/dg/monitoring-overview.html)\. 