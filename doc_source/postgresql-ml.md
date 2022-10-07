# Using Amazon Aurora machine learning with Aurora PostgreSQL<a name="postgresql-ml"></a>

By using Amazon Aurora machine learning with your Aurora PostgreSQL DB cluster, you can Amazon Comprehend or Amazon SageMaker or both, depending on your needs\. Amazon Comprehend and SageMaker each support specific machine learning use cases, as follows\.

**Amazon Comprehend**  
Amazon Comprehend is a managed natural language processing \(NLP\) service that's used to extract insights from documents\. With Amazon Comprehend, you can deduce sentiment based on the content of documents, by analyzing entities, key phrases, language, and other features\. To learn more, see [What is Amazon Comprehend?](https://docs.aws.amazon.com/comprehend/latest/dg/what-is.html) in the *Amazon Comprehend Developer Guide*\. 

**SageMaker**  
Amazon SageMaker is a fully managed machine learning service\. Data scientists and developers use Amazon SageMaker to build, train, and test machine learning models for a variety of inference tasks, such as fraud detection and product recommendation\. When a machine learning model is ready for use in production, it can be deployed to the Amazon SageMaker hosted environment\. For more information, see [What Is Amazon SageMaker?](https://docs.aws.amazon.com/sagemaker/latest/dg/whatis.html) in the *Amazon SageMaker Developer Guide*\.  

Using Amazon Comprehend with your Aurora DB cluster has less preliminary setup than using SageMaker\. If you're new to both AWS machine learning and to Aurora machine learning, we recommend that you start by exploring Amazon Comprehend\. 

Aurora machine learning is supported in certain AWS Regions and for specific versions of Aurora PostgreSQL only\. Before trying to set up Aurora machine learning, check availability for your Aurora PostgreSQL version and your Region\. For details, see [Aurora machine learning with Aurora PostgreSQL](Concepts.Aurora_Fea_Regions_DB-eng.Feature.Aurora_ML.md#Concepts.Aurora_Fea_Regions_DB-eng.Feature.Aurora_ML.apg)\. 

**Topics**
+ [Requirements for using Aurora machine learning with Aurora PostgreSQL](#postgresql-ml-prereqs)
+ [Supported features and limitations of Aurora machine learning with Aurora PostgreSQL](#postgresql-ml-supported-fea-limitations)
+ [Setting up your Aurora PostgreSQL DB cluster to use Aurora machine learning](#postgresql-ml-setting-up)
+ [Using Amazon Comprehend with your Aurora PostgreSQL DB cluster](#postgresql-using-comprehend)
+ [Using SageMaker with your Aurora PostgreSQL DB cluster](#postgresql-using-sagemaker)
+ [Exporting data to Amazon S3 for SageMaker model training \(Advanced\)](#postgresql-export-to-s3)
+ [Performance considerations for using Aurora machine learning with Aurora PostgreSQL](#postgresql-ml-best-practice)
+ [Monitoring Aurora machine learning](#postgresql-ml-monitoring)

## Requirements for using Aurora machine learning with Aurora PostgreSQL<a name="postgresql-ml-prereqs"></a>

AWS machine learning services are managed services that are set up and run in their own production environments\. Aurora machine learning supports integration with Amazon Comprehend and SageMaker\. Before trying to set up your Aurora PostgreSQL DB cluster to use Aurora machine learning, be sure you understand the following requirements and prerequisites\. 
+ The Amazon Comprehend and SageMaker services must be running in the same AWS Region as your Aurora PostgreSQL DB cluster\. You can't use Amazon Comprehend or SageMaker services from an Aurora PostgreSQL DB cluster in a different Region\.
+ If your Aurora PostgreSQL DB cluster is in a different virtual public cloud \(VPC\) based on the Amazon VPC service than your Amazon Comprehend and SageMaker services, the VPC's Security group needs to allow outbound connections to the target Aurora machine learning service\. For more information, see [Enabling network communication from Amazon Aurora MySQL to other AWS services](AuroraMySQL.Integrating.Authorizing.Network.md)\. 
+ For SageMaker, the machine learning components that you want to use for inferences must be setup and ready to use\. During the configuration process for your Aurora PostgreSQL DB cluster, you need to have the Amazon Resource Name \(ARN\) of the SageMaker endpoint available\. The data scientists on your team are likely best able to handle working with SageMaker to prepare the models and handle the other such tasks\. To get started with Amazon SageMaker, see [Get Started with Amazon SageMaker](https://docs.aws.amazon.com/sagemaker/latest/dg/gs.html)\. For more information about inferences and endpoints, see [Real\-time inference](https://docs.aws.amazon.com/sagemaker/latest/dg/realtime-endpoints.html)\. 

## Supported features and limitations of Aurora machine learning with Aurora PostgreSQL<a name="postgresql-ml-supported-fea-limitations"></a>

Aurora machine learning supports any SageMaker endpoint that can read and write the comma\-separated value \(CSV\) format through a `ContentType` value of `text/csv`\. The built\-in SageMaker algorithms that currently accept this format are the following\.
+ Linear Learner
+ Random Cut Forest
+ XGBoost

  To learn more about these algorithms, see [Choose an Algorithm](https://docs.aws.amazon.com/sagemaker/latest/dg/algorithms-choose.html) in the *Amazon SageMaker Developer Guide*\.

## Setting up your Aurora PostgreSQL DB cluster to use Aurora machine learning<a name="postgresql-ml-setting-up"></a>

For Aurora machine learning to work with your Aurora PostgreSQL DB cluster, you need to create an AWS Identity and Access Management \(IAM\) role for each of the services that you want to use\. The IAM role allows your Aurora PostgreSQL DB cluster to use the Aurora machine learning service on the cluster's behalf\. You also need to install the Aurora machine learning extension\. In the following topics, you can find setup procedures for each of these Aurora machine learning services\. 

**Contents**
+ [Setting up Aurora PostgreSQL to use Amazon Comprehend](#postgresql-ml-setting-up-apg-cmp)
+ [Setting up Aurora PostgreSQL to use Amazon SageMaker](#postgresql-ml-setting-up-apg-sagemaker)
  + [Setting up Aurora PostgreSQL to use Amazon S3 for SageMaker \(Advanced\)](#postgresql-ml-setting-up-apg-sagemaker-advanced-option-s3)
+ [Installing the Aurora machine learning extension](#postgresql-ml-aws_ml-install)

### Setting up Aurora PostgreSQL to use Amazon Comprehend<a name="postgresql-ml-setting-up-apg-cmp"></a>

In the procedure following, you first create the IAM role and policy that gives your Aurora PostgreSQL permission to use Amazon Comprehend on the cluster's behalf\. You then attach the policy to an IAM role that your Aurora PostgreSQL DB cluster uses to work with Amazon Comprehend For simplicity's sake, this procedure uses the AWS Management Console to complete all tasks\.

**To set up your Aurora PostgreSQL DB cluster to use Amazon Comprehend**

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. Choose **Policies** \(under Access management\) on the AWS Identity and Access Management \(IAM\) Console menu\.   
![\[Creating the IAM policy to use for Amazon Comprehend.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/apg-ml-create-iam-role-policy-1.png)

1. Choose **Create policy**\. In the Visual editor page, choose **Service** and then enter **Comprehend** in the Select a service field\. Expand the Read access level\. Choose **BatchDetectSentiment** and **DetectSentiment** from the Amazon Comprehend read settings\.

1. Choose **Next: Tags** and define any tags \(this is optional\)\. Choose **Next: Review**\. Enter a Name for the policy and description, as shown in the image\.   
![\[Creating the policy for the IAM role to use for Amazon Comprehend.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/apg-ml-create-iam-policy-3.png)

1. Choose **Create policy**\. The Console displays an alert when the policy has been saved\. You can find it in the list of Policies\.

1. Choose **Roles** \(under Access management\) on the IAM Console\. 

1. Choose **Create role**\. 

1. On the Select trusted entity page, choose the **AWS service** tile, and then choose **RDS** to open the selector\. 

1. Choose **RDS – Add Role to Database**\.   
![\[Creating the role for Aurora PostgreSQL to work with Amazon Comprehend.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/apg-ml-create-iam-role-policy-4.png)

1. Choose **Next**\. On the Add permissions page, find the policy that you created in the previous step and choose it from among those listed\. Choose **Next**

1. **Next: Review**\. Enter a name for the IAM role and a description\.

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Navigate to the AWS Region where your Aurora PostgreSQL DB cluster is located\.

1. In the navigation pane, choose **Databases**, and then choose the Aurora PostgreSQL DB cluster that you want to use with Amazon Comprehend\.

1. Choose the **Connectivity & security** tab and scroll to find the **Manage IAM roles** section of the page\. From the **Add IAM roles to this cluster** selector, choose the role that you created in the previous steps\. In the **Feature** selector, choose Comrehend, and then choose **Add role**\. 

The role \(with its policy\) are associated with the Aurora PostgreSQL DB cluster\. When the process completes, the role is listed in the Current IAM roles for this cluster listing, as shown following\.

![\[The role for Amazon Comprehend has been added to the Aurora PostgreSQL DB cluster and is now Active.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/apg-ml-completed-iam-setup-both.png)

The IAM setup for Amazon Comprehend is complete\. Continue setting up your Aurora PostgreSQL to work with Aurora machine learning by installing the extension as detailed in [Installing the Aurora machine learning extension](#postgresql-ml-aws_ml-install) 

### Setting up Aurora PostgreSQL to use Amazon SageMaker<a name="postgresql-ml-setting-up-apg-sagemaker"></a>

Before you can create the IAM policy and role for your Aurora PostgreSQL DB cluster, you need to have your SageMaker model setup and your endpoint available\.

**To set up your Aurora PostgreSQL DB cluster to use SageMaker**

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. Choose **Policies** \(under Access management\) on the AWS Identity and Access Management \(IAM\) Console menu, and then choose **Create policy**\. In the Visual editor, choose **SageMaker** for the Service\. For Actions, open the Read selector \(under Access level\) and choose **InvokeEndpoint**\. When you this, a warning icon displays\. 

1. Open the Resources selector and choose the **Add ARN to restrict access** link under the Specify endpoint resource ARN for the InvokeEndpoint action\. 

1. Enter the AWS Region of your SageMaker resources and the name of your endpoint\. Your AWS account is prefilled\.   
![\[Specifying the ARN for the endpoint for the IAM Policy.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/apg-ml-create-policy-sm-endpoint.png)

1. Choose **Add** to save\. Choose **Next: Tags** and **Next: Review** to get to the last page of the policy creation process\. 

1. Enter a Name and Description for this policy, and then choose **Create policy**\. The policy is created and is added to the Policies list\. You see an alert in the Console as this occurs\.

1. On the IAM Console, choose **Roles**\.

1. Choose **Create role**\. 

1. On the Select trusted entity page, choose the **AWS service** tile, and then choose **RDS** to open the selector\. 

1. Choose **RDS – Add Role to Database**\. 

1. Choose **Next**\. On the Add permissions page, find the policy that you created in the previous step and choose it from among those listed\. Choose **Next**

1. **Next: Review**\. Enter a name for the IAM role and a description\.

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Navigate to the AWS Region where your Aurora PostgreSQL DB cluster is located\.

1. In the navigation pane, choose **Databases**, and then choose the Aurora PostgreSQL DB cluster that you want to use with SageMaker\.

1. Choose the **Connectivity & security** tab and scroll to find the **Manage IAM roles** section of the page\. From the **Add IAM roles to this cluster** selector, choose the role that you created in the previous steps\. In the **Feature** selector, choose SageMaker, and then choose **Add role**\. 

The role \(with its policy\) are associated with the Aurora PostgreSQL DB cluster\. When the process completes, the role is listed in the Current IAM roles for this cluster listing\.

The IAM setup for SageMaker is complete\. Continue setting up your Aurora PostgreSQL to work with Aurora machine learning by installing the extension as detailed in [Installing the Aurora machine learning extension](#postgresql-ml-aws_ml-install)\.

#### Setting up Aurora PostgreSQL to use Amazon S3 for SageMaker \(Advanced\)<a name="postgresql-ml-setting-up-apg-sagemaker-advanced-option-s3"></a>

To use SageMaker with your own models rather than using the pre\-built components provided by SageMaker, you need to set up an Amazon Simple Storage Service \(Amazon S3\) bucket for Aurora PostgreSQL DB cluster to use\. This is an advanced topic, and isn't fully documented in this *Amazon Aurora User Guide*\. The general process is the same as for integrating support for SageMaker, as follows\. 

1. Create the the IAM policy and role for Amazon S3\. 

1. Add the IAM role and the Amazon S3 import or export as a feature on the Connectivity & security tab of your Aurora PostgreSQL DB cluster\.

1. Add the ARN of the role to your custom DB cluster parameter group for your Aurora DB cluster\.

For basic usage information, see [Exporting data to Amazon S3 for SageMaker model training \(Advanced\)](#postgresql-export-to-s3)\. 

### Installing the Aurora machine learning extension<a name="postgresql-ml-aws_ml-install"></a>

The Aurora machine learning extension, `aws_ml`, provides two functions that you can use to invoke Amazon Comprehend and SageMaker services\. Installing the extension on your Aurora PostgreSQL DB cluster also creates an administrative role for the feature\. 

**Note**  
Using these functions depends on having the IAM setup for the Aurora machine learning service \(Amazon Comprehend, SageMaker\) complete, as detailed in [Setting up your Aurora PostgreSQL DB cluster to use Aurora machine learning](#postgresql-ml-setting-up)\. 
+ **aws\_comprehend\.detect\_sentiment** – You use this function to apply sentiment analysis to text stored in in the database on your Aurora PostgreSQL DB cluster\.
+ **aws\_sagemaker\.invoke\_endpoint** – You use this function in your SQL code to communicate with the SageMaker endpoint from your cluster\. 

**To install the Aurora machine learning extension in your Aurora PostgreSQL DB cluster**
+ Use `psql` to connect to the writer instance of your Aurora PostgreSQL DB cluster\. Connect to the specific database in which to install the `aws_ml` extension\. 

  ```
  psql --host=cluster-instance-1.111122223333.aws-region.rds.amazonaws.com --port=5432 --username=postgres --password --dbname=labdb
  ```

```
labdb=> CREATE EXTENSION IF NOT EXISTS aws_ml CASCADE;
NOTICE:  installing required extension "aws_commons"
CREATE EXTENSION
labdb=>
```

Installing the `aws_ml` extension also creates the `aws_ml` administrative role and two new schemas, as follows\. 
+ `aws_comprehend` – Schema for the Amazon Comprehend service and source of the `detect_sentiment` function \(`aws_comprehend.detect_sentiment`\)\.
+ `aws_sagemaker` – Schema for the SageMaker service and source of the `invoke_endpoint` function \(`aws_sagemaker.invoke_endpoint`\)\.

The `rds_superuser` role is granted the `aws_ml` administrative role and is made the `OWNER` of these two Aurora machine learning schemas\. To allow other database users to access the Aurora machine learning functions, the `rds_superuser` needs to grant `EXECUTE` privileges on the Aurora machine learning functions\. By default, `EXECUTE` privileges are revoked from `PUBLIC` on the functions in the two Aurora machine learning schemas\. 

In a multi\-tenant database configuration, you can prevent tenants from accessing Aurora machine learning functions by using `REVOKE USAGE` on the specific Aurora machine learning schema that you want to protect\.

## Using Amazon Comprehend with your Aurora PostgreSQL DB cluster<a name="postgresql-using-comprehend"></a>

For Aurora PostgreSQL, Aurora machine learning provides the following Amazon Comprehend function for working with your text data\. This function is available only after you install the `aws_ml` extension and complete all setup procedures\. For more information, see [Setting up your Aurora PostgreSQL DB cluster to use Aurora machine learning](#postgresql-ml-setting-up)\. 

**aws\_comprehend\.detect\_sentiment**  
This function takes text as input and evaluates whether the text has a a positive, negative, neutral, or mixed emotional posture\. It outputs this sentiment along with a confidence level for its evaluation\. A summary of the function's documentation is as follows\.  

```
aws_comprehend.detect_sentiment(
   IN input_text varchar,      
   IN language_code varchar,  
   IN max_rows_per_batch int, 
   OUT sentiment varchar, 
   OUT confidence real)
```

The inputs and outputs of this function are as follows\.
+ `input_text` – The text to evaluate and to assign sentiment \(negative, positive, neutral, mixed\)\. 
+ `language_code` – The language of the `input_text` identified using the 2\-letter ISO 639\-1 identifier with regional subtag \(as needed\) or the ISO 639\-2 three\-letter code, as appropriate\. For example, `en` is the code for English, `zh` is the code for simplified Chinese\. For more information, see [Supported languages](https://docs.aws.amazon.com/comprehend/latest/dg/supported-languages.html#supported-languages-1) in the *Amazon Comprehend Developer Guide*\.
+ `max_rows_per_batch` – The maximum number of rows per batch for batch\-mode processing\. For more information, see [Understanding batch mode and Aurora machine learning functions](#postgresql-ml-batch-mode)\.
+ `sentiment` – The sentiment of the input text, identified as POSITIVE, NEGATIVE, NEUTRAL, or MIXED\. 
+ `confidence` – The degree of confidence in the accuracy of the specified `sentiment`\. Values range from 0\.0 to 1\.0\. 

In the following, you can find examples of how to use this function\. 

**Example: A simple query using Amazon Comprehend functions**  
Here's an example of a simple query that invokes this function to assess customer satisfaction with your support team\. Suppose you have a database table \(`support`\) that stores customer feedback after each request for help\. This example query applies the `aws_comprehend.detect_sentiment` function to the text in the `feedback` column of the table and outputs the sentiment and the confidence level for that sentiment\. This query also outputs results in descending order\.   

```
SELECT feedback, s.sentiment,s.confidence 
    FROM support,aws_comprehend.detect_sentiment(feedback, 'en') s 
    ORDER BY s.confidence DESC;
 feedback                         | sentiment | confidence
 ----------------------------------------------------------+-----------+------------
 Thank you for the excellent customer support!            | POSITIVE  |   0.999771
 The latest version of this product stinks!               | NEGATIVE  |   0.999184
 Your support team is just awesome! I am blown away.      | POSITIVE  |   0.997774
 Your product is too complex, but your support is great.  | MIXED     |   0.957958
 Your support tech helped me in fifteen minutes.          | POSITIVE  |   0.949491
 My problem was never resolved!                           | NEGATIVE  |   0.920644
 When will the new version of this product be released?   | NEUTRAL   |   0.902706
 I cannot stand that chatbot.                             | NEGATIVE  |   0.895219
 Your support tech talked down to me.                     | NEGATIVE  |   0.868598
 It took me way too long to get a real person.            | NEGATIVE  |   0.481805
 
 (10 rows)
```

To avoid being charged for sentiment detection more than once per table row, you can materialize the results\. Do this on the rows of interest\. For example, the clinician's notes are being updated so that only those in French \(`fr`\) use the sentiment detection function\.

```
UPDATE clinician_notes
SET sentiment = (aws_comprehend.detect_sentiment (french_notes, 'fr')).sentiment,
    confidence = (aws_comprehend.detect_sentiment (french_notes, 'fr')).confidence
WHERE
    clinician_notes.french_notes IS NOT NULL AND
    LENGTH(TRIM(clinician_notes.french_notes)) > 0 AND
    clinician_notes.sentiment IS NULL;
```

For more information on optimizing your function calls, see [Performance considerations for using Aurora machine learning with Aurora PostgreSQL](#postgresql-ml-best-practice)\. 

## Using SageMaker with your Aurora PostgreSQL DB cluster<a name="postgresql-using-sagemaker"></a>

After setting up your SageMaker environment and integrating with Aurora PostgreSQL as outlined in [Setting up Aurora PostgreSQL to use Amazon SageMaker ](#postgresql-ml-setting-up-apg-sagemaker), you can invoke operations by using the `aws_sagemaker.invoke_endpoint` function\. The `aws_sagemaker.invoke_endpoint` function connects only to a model endpoint in the same AWS Region\. If your database instance has replicas in multiple AWS Regions be sure that you setup and deploy each SageMaker model to every AWS Region\. 

Calls to `aws_sagemaker.invoke_endpoint` are authenticated using the IAM role that you set up to associated your Aurora PostgreSQL DB cluster with the SageMaker service and the endpoint that you provided during the setup process\. SageMaker model endpoints are scoped to an individual account and are not public\. The `endpoint_name` URL doesn't contain the account ID\. SageMaker determines the account ID from the authentication token that is supplied by the SageMaker IAM role of the database instance\. 

**aws\_sagemaker\.invoke\_endpoint**  
This function takes the SageMaker endpoint as input and the number of rows that should be processed as a batch\. It also takes as input the various parameters expected by the SageMaker model endpoint\. This function's reference documentation is as follows\.  

```
aws_sagemaker.invoke_endpoint(
  IN endpoint_name varchar,   
  IN max_rows_per_batch int,
  VARIADIC model_input "any",
  OUT model_output varchar  
  )
```

The inputs and outputs of this function are as follows\.
+ `endpoint_name` – An endpoint URL that is AWS Region–independent\. 
+ `max_rows_per_batch` – The maximum number of rows per batch for batch\-mode processing\. For more information, see [Understanding batch mode and Aurora machine learning functions](#postgresql-ml-batch-mode)\. 
+ `model_input` – One or more input parameters for the model\. These can be any data type needed by the SageMaker model\. PostgreSQL allows you to specify up to 100 input parameters for a function\. Array data types must be one\-dimensional, but can contain as many elements as are expected by the SageMaker model\. The number of inputs to a SageMaker model is limited only by the SageMaker 6 MB message size limit\.
+ `model_output` – The SageMaker model's output as text\.

### Creating a user\-defined function to invoke a SageMaker model<a name="postgresql-using-sagemaker-example1"></a>

Create a separate user\-defined function to call `aws_sagemaker.invoke_endpoint` for each of your SageMaker models\. Your user\-defined function represents the SageMaker endpoint hosting the model\. The `aws_sagemaker.invoke_endpoint` function runs within the user\-defined function\. User\-defined functions provide many advantages:
+ You can give your SageMaker model its own name instead of only calling `aws_sagemaker.invoke_endpoint` for all of your SageMaker models\. 
+ You can specify the model endpoint URL in just one place in your SQL application code\.
+ You can control `EXECUTE` privileges to each Aurora machine learning function independently\.
+ You can declare the model input and output types using SQL types\. SQL enforces the number and type of arguments passed to your SageMaker model and performs type conversion if necessary\. Using SQL types will also translate `SQL NULL` to the appropriate default value expected by your SageMaker model\.
+ You can reduce the maximum batch size if you want to return the first few rows a little faster\.

To specify a user\-defined function, use the SQL data definition language \(DDL\) statement `CREATE FUNCTION`\. When you define the function, you specify the following:
+ The input parameters to the model\.
+ The specific SageMaker endpoint to invoke\.
+ The return type\. 

The user\-defined function returns the inference computed by the SageMaker endpoint after running the model on the input parameters\. The following example creates a user\-defined function for an SageMaker model with two input parameters\.

```
CREATE FUNCTION classify_event (IN arg1 INT, IN arg2 DATE, OUT category INT)
AS $$
    SELECT aws_sagemaker.invoke_endpoint (
        'sagemaker_model_endpoint_name', NULL,
        arg1, arg2                        -- model inputs are separate arguments
        )::INT                            -- cast the output to INT
$$ LANGUAGE SQL PARALLEL SAFE COST 5000;
```

Note the following:
+ The `aws_sagemaker.invoke_endpoint` function input can be one or more parameters of any data type\. 
+ This example uses an INT output type\. If you cast the output from a `varchar` type to a different type, then it must be cast to a PostgreSQL builtin scalar type such as `INTEGER`, `REAL`, `FLOAT`, or `NUMERIC`\. For more information about these types, see [Data types](https://www.postgresql.org/docs/current/datatype.html) in the PostgreSQL documentation\.
+ Specify `PARALLEL SAFE` to enable parallel query processing\. For more information, see [Improving response times with parallel query processing](#postgresql-using-sagemaker-example-parallel)\.
+ Specify `COST 5000` to estimate the cost of running the function\. Use a positive number giving the estimated run cost for the function, in units of `cpu_operator_cost`\.

### Passing an array as input to a SageMaker model<a name="postgresql-using-sagemaker-example-array"></a>

The `aws_sagemaker.invoke_endpoint` function can have up to 100 input parameters, which is the limit for PostgreSQL functions\. If the SageMaker model requires more than 100 parameters of the same type, pass the model parameters as an array\. 

The following example defines a function that passes an array as input to the SageMaker regression model\. The output is cast to a `REAL` value\.

```
CREATE FUNCTION regression_model (params REAL[], OUT estimate REAL)
AS $$
    SELECT aws_sagemaker.invoke_endpoint (
      'sagemaker_model_endpoint_name',
      NULL,
      params                            
      )::REAL
$$ LANGUAGE SQL PARALLEL SAFE COST 5000;
```

### Specifying batch size when invoking a SageMaker model<a name="postgresql-using-sagemaker-example3"></a>

The following example creates a user\-defined function for a SageMaker model that sets the batch size default to NULL\. The function also allows you to provide a different batch size when you invoke it\.

```
CREATE FUNCTION classify_event (
    IN event_type INT, IN event_day DATE, IN amount REAL, -- model inputs
    max_rows_per_batch INT DEFAULT NULL,  -- optional batch size limit
    OUT category INT)                     -- model output
AS $$
    SELECT aws_sagemaker.invoke_endpoint (
        'sagemaker_model_endpoint_name', max_rows_per_batch,
        event_type, event_day, COALESCE(amount, 0.0)
        )::INT              -- casts output to type INT
$$ LANGUAGE SQL PARALLEL SAFE COST 5000;
```

Note the following:
+ Use the optional `max_rows_per_batch` parameter to provide control of the number of rows for a batch\-mode function invocation\. If you use a value of NULL, then the query optimizer automatically chooses the maximum batch size\. For more information, see [Understanding batch mode and Aurora machine learning functions](#postgresql-ml-batch-mode)\.
+ By default, passing NULL as a parameter's value is translated to an empty string before passing to SageMaker\. For this example the inputs have different types\.
+ If you have a non\-text input, or text input that needs to default to some value other than an empty string, use the `COALESCE` statement\. Use `COALESCE` to translate NULL to the desired null replacement value in the call to `aws_sagemaker.invoke_endpoint`\. For the `amount` parameter in this example, a NULL value is converted to 0\.0\. 

### Invoking a SageMaker model that has multiple outputs<a name="postgresql-using-sagemaker-example4"></a>

The following example creates a user\-defined function for a SageMaker model that returns multiple outputs\. Your function needs to cast the output of the `aws_sagemaker.invoke_endpoint` function to a corresponding data type\. For example, you could use the built\-in PostgreSQL point type for \(x,y\) pairs or a user\-defined composite type\. 

This user\-defined function returns values from a model that returns multiple outputs by using a composite type for the outputs\.

```
CREATE TYPE company_forecasts AS ( 
    six_month_estimated_return real,
    one_year_bankruptcy_probability float);
CREATE FUNCTION analyze_company (
    IN free_cash_flow NUMERIC(18, 6),
    IN debt NUMERIC(18,6),
    IN max_rows_per_batch INT DEFAULT NULL,
    OUT prediction company_forecasts) 
AS $$
    SELECT (aws_sagemaker.invoke_endpoint('endpt_name',
       max_rows_per_batch,free_cash_flow, debt))::company_forecasts;                                                                                                                  
$$ LANGUAGE SQL PARALLEL SAFE COST 5000;
```

For the composite type, use fields in the same order as they appear in the model output and cast the output of `aws_sagemaker.invoke_endpoint` to your composite type\. The caller can extract the individual fields either by name or with PostgreSQL "\.\*" notation\.

## Exporting data to Amazon S3 for SageMaker model training \(Advanced\)<a name="postgresql-export-to-s3"></a>

We recommend that you become familiar with Aurora machine learning and SageMaker by using the provided algorithms and examples rather than trying to train your own models\. For more information, see [Get Started with Amazon SageMaker](https://docs.aws.amazon.com/sagemaker/latest/dg/gs.html) 

To train SageMaker models, you export data to an Amazon S3 bucket\. The Amazon S3 bucket is used by SageMaker to train your model before it is deployed\. You can query data from an Aurora PostgreSQL DB cluster and save it directly into text files stored in an Amazon S3 bucket\. Then SageMaker consumes the data from the Amazon S3 bucket for training\. For more about SageMaker model training, see [ Train a model with Amazon SageMaker](https://docs.aws.amazon.com/sagemaker/latest/dg/how-it-works-training.html)\.

**Note**  
When you create an Amazon S3 bucket for SageMaker model training or batch scoring, use `sagemaker` in the Amazon S3 bucket name\. For more information, see [Specify a Amazon S3 Bucket to Upload Training Datasets and Store Output Data](https://docs.aws.amazon.com/sagemaker/latest/dg/automatic-model-tuning-ex-bucket.html) in the *Amazon SageMaker Developer Guide*\.

For more information about exporting your data, see [Exporting data from an Aurora PostgreSQL DB cluster to Amazon S3](postgresql-s3-export.md)\.

## Performance considerations for using Aurora machine learning with Aurora PostgreSQL<a name="postgresql-ml-best-practice"></a>

The Amazon Comprehend and SageMaker services do most of the work when invoked by an Aurora machine learning function\. That means that you can scale those resources as needed, independently\. For your Aurora PostgreSQL DB cluster, you can make your function calls as efficient as possible\. Following, you can find some performance considerations to note when working with Aurora machine learning from Aurora PostgreSQL\.

**Topics**
+ [Understanding batch mode and Aurora machine learning functions](#postgresql-ml-batch-mode)
+ [Improving response times with parallel query processing](#postgresql-using-sagemaker-example-parallel)
+ [Using materialized views and materialized columns](#postgresql-using-sagemaker-example-materialized)

### Understanding batch mode and Aurora machine learning functions<a name="postgresql-ml-batch-mode"></a>

Typically, PostgreSQL runs functions one row at a time\. Aurora machine learning can reduce this overhead by combining the calls to the external Aurora machine learning service for many rows into batches with an approach called *batch\-mode execution*\. In batch mode, Aurora machine learning receives the responses for a batch of input rows, and then delivers the responses back to the running query one row at a time\. This optimization improves the throughput of your Aurora queries without limiting the PostgreSQL query optimizer\. 

Aurora automatically uses batch mode if the function is referenced from the `SELECT` list, a `WHERE` clause, or a `HAVING` clause\. Note that top\-level simple `CASE` expressions are eligible for batch\-mode execution\. Top\-level searched `CASE` expressions are also eligible for batch\-mode execution provided that the first `WHEN` clause is a simple predicate with a batch\-mode function call\. 

Your user\-defined function must be a `LANGUAGE SQL` function and should specify `PARALLEL SAFE` and `COST 5000`\. 

#### Function migration from the SELECT statement to the FROM clause<a name="postgresql-ml-batch-mode-function-migration"></a>

Usually, an `aws_ml` function that is eligible for batch\-mode execution is automatically migrated by Aurora to the `FROM` clause\. 

The migration of eligible batch\-mode functions to the FROM clause can be examined manually on a per\-query level\. To do this, you use EXPLAIN statements \(and ANALYZE and VERBOSE\) and find the "Batch Processing" information below each batch\-mode `Function Scan`\. You can also use EXPLAIN \(with VERBOSE\) without running the query\. You then observe whether the calls to the function appear as a `Function Scan` under a nested loop join that was not specified in the original statement\. 

In the following example, the nested loop join operator in the plan shows that Aurora migrated the `anomaly_score` function\. It migrated this function from the SELECT list to the FROM clause, where it's eligible for batch\-mode execution\.

```
EXPLAIN (VERBOSE, COSTS false) 
SELECT anomaly_score(ts.R.description) from ts.R;
                         QUERY PLAN                          
-------------------------------------------------------------
 Nested Loop
   Output: anomaly_score((r.description)::text)
   ->  Seq Scan on ts.r
         Output: r.id, r.description, r.score
   ->  Function Scan on public.anomaly_score
         Output: anomaly_score.anomaly_score
         Function Call: anomaly_score((r.description)::text)
```

To disable batch\-mode execution, set the `apg_enable_function_migration` parameter to `false`\. This prevents the migration of `aws_ml` functions from the SELECT to the `FROM` clause\. The following shows how\.

```
SET apg_enable_function_migration = false;
```

The `apg_enable_function_migration` parameter is a Grand Unified Configuration \(GUC\) parameter that is recognized by the Aurora PostgreSQL `apg_plan_mgmt` extension for query plan management\. To disable function migration in a session, use query plan management to save the resulting plan as an `approved` plan\. At runtime, query plan management enforces the `approved` plan with its `apg_enable_function_migration` setting\. This enforcement occurs regardless of the `apg_enable_function_migration` GUC parameter setting\. For more information, see [Managing query execution plans for Aurora PostgreSQL](AuroraPostgreSQL.Optimize.md)\.

#### Using the max\_rows\_per\_batch parameter<a name="postgresql-ml-batch-mode-max_rows_per_batch"></a>

Both the `aws_comprehend.detect_sentiment` and the `aws_sagemaker.invoke_endpoint` functions have a `max_rows_per_batch` parameter\. This parameter specifies the number of rows that can be sent to the Aurora machine learning service\. The larger the dataset processed by your function, the larger you can make the batch size\.

Batch\-mode functions improve efficiency by building batches of rows that spread the cost of the Aurora machine learning function calls over a large number of rows\. However, if a `SELECT` statement finishes early due to a `LIMIT` clause, then the batch can be constructed over more rows than the query uses\. This approach can result in additional charges to your AWS account\. To gain the benefits of batch\-mode execution but avoid building batches that are too large, use a smaller value for the `max_rows_per_batch` parameter in your function calls\.

If you do an `EXPLAIN` \(`VERBOSE`, `ANALYZE`\) of a query that uses batch\-mode execution, you see a `FunctionScan` operator that is below a nested loop join\. The number of loops reported by `EXPLAIN` equals the number of times a row was fetched from the `FunctionScan` operator\. If a statement uses a LIMIT clause, the number of fetches is consistent\. To optimize the size of the batch, set the `max_rows_per_batch` parameter to this value\. However, if the batch\-mode function is referenced in a predicate in the `WHERE` clause or `HAVING` clause, then you probably can't know the number of fetches in advance\. In this case, use the loops as a guideline and experiment with `max_rows_per_batch` to find a setting that optimizes performance\.

#### Verifying batch\-mode execution<a name="postgresql-ml-batch-mode-verify"></a>

To see if a function ran in batch mode, use `EXPLAIN ANALYZE`\. If batch\-mode execution was used, then the query plan will include the information in a "Batch Processing" section\.

```
EXPLAIN ANALYZE SELECT user-defined-function();
 Batch Processing: num batches=1 avg/min/max batch size=3333.000/3333.000/3333.000
                                avg/min/max batch call time=146.273/146.273/146.273
```

In this example, there was 1 batch that contained 3,333 rows, which took 146\.273 ms to process\. The "Batch Processing" section shows the following: 
+ How many batches there were for this function scan operation
+ The batch size average, minimum, and maximum
+ The batch execution time average, minimum, and maximum

Typically the final batch is smaller than the rest, which often results in a minimum batch size that is much smaller than the average\. 

To return the first few rows more quickly, set the `max_rows_per_batch` parameter to a smaller value\. 

To reduce the number of batch mode calls to the ML service when you use a `LIMIT` in your user\-defined function, set the `max_rows_per_batch` parameter to a smaller value\. 

### Improving response times with parallel query processing<a name="postgresql-using-sagemaker-example-parallel"></a>

To get results as fast as possible from a large number of rows, you can combine parallel query processing with batch mode processing\. You can use parallel query processing for `SELECT`, `CREATE TABLE AS SELECT`, and `CREATE MATERIALIZED VIEW` statements\.

**Note**  
PostgreSQL doesn't yet support parallel query for data manipulation language \(DML\) statements\.

Parallel query processing occurs both within the database and within the ML service\. The number of cores in the instance class of the database limits the degree of parallelism that can be used when running a query\. The database server can construct a parallel query execution plan that partitions the task among a set of parallel workers\. Then each of these workers can build batched requests containing tens of thousands of rows \(or as many as are allowed by each service\)\. 

The batched requests from all of the parallel workers are sent to the SageMaker endpoint\. The degree of parallelism that the endpoint can support is constrained by the number and type of instances that support it\. For *K* degrees of parallelism, you need a database instance class that has at least *K* cores\. You also need to configure the SageMaker endpoint for your model to have *K* initial instances of a sufficiently high\-performing instance class\.

To use parallel query processing, you can set the `parallel_workers` storage parameter of the table that contains the data that you plan to pass\. You set `parallel_workers` to a batch\-mode function such as `aws_comprehend.detect_sentiment`\. If the optimizer chooses a parallel query plan, the AWS ML services can be called both in batch and in parallel\. 

You can use the following parameters with the `aws_comprehend.detect_sentiment` function to get a plan with four\-way parallelism\. If you change either of the following two parameters, you must restart the database instance for the changes to take effect

```
-- SET max_worker_processes to 8;  -- default value is 8
-- SET max_parallel_workers to 8;  -- not greater than max_worker_processes
SET max_parallel_workers_per_gather to 4;  -- not greater than max_parallel_workers

-- You can set the parallel_workers storage parameter on the table that the data
-- for the Aurora machine learning function is coming from in order to manually override the degree of
-- parallelism that would otherwise be chosen by the query optimizer
--
ALTER TABLE yourTable SET (parallel_workers = 4);

-- Example query to exploit both batch-mode execution and parallel query
EXPLAIN (verbose, analyze, buffers, hashes) 
SELECT aws_comprehend.detect_sentiment(description, 'en')).*
FROM yourTable
WHERE id < 100;
```

For more information about controlling parallel query, see [Parallel plans](https://www.postgresql.org/docs/current/parallel-plans.html) in the PostgreSQL documentation\.

### Using materialized views and materialized columns<a name="postgresql-using-sagemaker-example-materialized"></a>

When you invoke an AWS service such as SageMaker or Amazon Comprehend from your database, your account is charged according to the pricing policy of that service\. To minimize charges to your account, you can materialize the result of calling the AWS service into a materialized column so that the AWS service is not called more than once per input row\. If desired, you can add a `materializedAt` timestamp column to record the time at which the columns were materialized\. 

The latency of an ordinary single\-row `INSERT` statement is typically much less than the latency of calling a batch\-mode function\. Thus, you might not be able to meet the latency requirements of your application if you invoke the batch\-mode function for every single\-row `INSERT` that your application performs\. To materialize the result of calling an AWS service into a materialized column, high\-performance applications generally need to populate the materialized columns\. To do this, they periodically issue an `UPDATE` statement that operates on a large batch of rows at the same time\.

`UPDATE` takes a row\-level lock that can impact a running application\. So you might need to use `SELECT ... FOR UPDATE SKIP LOCKED`, or use `MATERIALIZED VIEW`\. 

Analytic queries that operate on a large number of rows in real time can combine batch\-mode materialization with real\-time processing\. To do this, these queries assemble a `UNION ALL` of the pre\-materialized results with a query over the rows that don't yet have materialized results\. In some cases, such a `UNION ALL` is needed in multiple places, or the query is generated by a third\-party application\. If so, you can create a `VIEW` to encapsulate the `UNION ALL` operation so this detail isn't exposed to the rest of the SQL application\.

You can use a materialized view to materialize the results of an arbitrary `SELECT` statement at a snapshot in time\. You can also use it to refresh the materialized view at any time in the future\. Currently PostgreSQL doesn't support incremental refresh, so each time the materialized view is refreshed the materialized view is fully recomputed\.

You can refresh materialized views with the `CONCURRENTLY` option, which updates the contents of the materialized view without taking an exclusive lock\. Doing this allows a SQL application to read from the materialized view while it's being refreshed\.

## Monitoring Aurora machine learning<a name="postgresql-ml-monitoring"></a>

You can monitor the `aws_ml` functions by setting the `track_functions` parameter in your custom DB cluster parameter group to `all`\. By default, this parameter is set to `pl` which means that only procedure\-language functions are tracked\. By changing this to `all`, the `aws_ml` functions are also tracked\. For more information, see [Run\-time Statistics](https://www.postgresql.org/docs/current/runtime-config-statistics.html) in the PostgreSQL documentation\. 

For information about monitoring the performance of the SageMaker operations called from Aurora machine learning functions, see [Monitor Amazon SageMaker](https://docs.aws.amazon.com/sagemaker/latest/dg/monitoring-overview.html) in the *Amazon SageMaker Developer Guide*\.

With `track_functions` set to `all`, you can query the `pg_stat_user_functions` view to get statistics about the functions that you define and use to invoke Aurora machine learning services\. For each function, the view provides the number of `calls`, `total_time`, and `self_time`\. 

To view the statistics for the `aws_sagemaker.invoke_endpoint` and the `aws_comprehend.detect_sentiment` functions, you can filter results by schema name using the following query\.

```
SELECT * FROM pg_stat_user_functions 
    WHERE schemaname 
    LIKE 'aws_%';
```

To clear the statistics, do as follows\.

```
SELECT pg_stat_reset();
```

You can get the names of your SQL functions that call the `aws_sagemaker.invoke_endpoint` function by querying the PostgreSQL `pg_proc` system catalog\. This catalog stores information about functions, procedures, and more\. For more information, see [pg\_proc](https://www.postgresql.org/docs/current/catalog-pg-proc.html) in the PostgreSQL documentation\. Following is an example of querying the table to get the names of functions \(`proname`\) whose source \(`prosrc`\) includes the text *invoke\_endpoint*\. 

```
SELECT proname FROM pg_proc WHERE prosrc LIKE '%invoke_endpoint%';
```