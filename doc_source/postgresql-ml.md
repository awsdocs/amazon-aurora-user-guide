# Using Machine Learning \(ML\) with Aurora PostgreSQL<a name="postgresql-ml"></a>

Amazon Aurora machine learning enables you to add machine learning–based predictions to database applications using the SQL language\. Aurora machine learning uses a highly optimized integration between the Aurora database and the AWS machine learning \(ML\) services SageMaker and Amazon Comprehend\. 

 Benefits of Aurora machine learning include the following: 
+  You can add ML\-based predictions to your existing database applications\. You don't need to build custom integrations or learn separate tools\. You can embed machine learning processing directly into your SQL query as calls to functions\. 
+  The ML integration is a fast way to enable ML services to work with transactional data\. You don't have to move the data out of the database to perform the machine learning operations\. You don't have to convert or reimport the results of the machine learning operations to use them in your database application\. 
+  You can use your existing governance policies to control who has access to the underlying data and to the generated insights\. 

AWS machine learning services are managed services that you set up and run in their own production environments\. Currently, Aurora Machine Learning integrates with Amazon Comprehend for sentiment analysis and SageMaker for a wide variety of ML algorithms\. 

 For general information about Amazon Comprehend, see [Amazon Comprehend](https://aws.amazon.com/comprehend)\. For details about using Aurora and Amazon Comprehend together, see [Using Amazon Comprehend for Natural Language Processing](#postgresql-using-comprehend)\. 

For general information about SageMaker, see [SageMaker](https://aws.amazon.com/sagemaker)\. For details about using Aurora and SageMaker together, see [Using SageMaker to Run Your Own ML Models](#postgresql-using-sagemaker)\. 

**Note**  
Aurora machine learning for PostgreSQL connects an Aurora cluster to SageMaker or Amazon Comprehend services only within the same AWS Region\. 

**Topics**
+ [Enabling Aurora Machine Learning](#postgresql-ml-enabling)
+ [Using Amazon Comprehend for Natural Language Processing](#postgresql-using-comprehend)
+ [Exporting Data to Amazon S3 for SageMaker Model Training](#postgresql-export-to-s3)
+ [Using SageMaker to Run Your Own ML Models](#postgresql-using-sagemaker)
+ [Best Practices with Aurora Machine Learning](#postgresql-ml-best-practice)
+ [Monitoring Aurora Machine Learning](#postgresql-ml-monitoring)
+ [PostgreSQL Function Reference for Aurora Machine Learning](#postgresql-ml-functions)
+ [Manually Setting up IAM Roles for SageMaker and Amazon Comprehend Using the AWS CLI](#postgresql-ml-connect-cli)

## Enabling Aurora Machine Learning<a name="postgresql-ml-enabling"></a>

Aurora machine learning is available for the following Aurora PostgreSQL versions:
+ Version 2\.4 of Aurora with PostgreSQL compatibility running the PostgreSQL engine version 10\.11\.
+ Version 3\.1 and higher of Aurora with PostgreSQL compatibility running the PostgreSQL engine version 11\.6 and higher

For information about upgrading an older Aurora cluster, see [Upgrading the PostgreSQL DB Engine for Aurora PostgreSQL](USER_UpgradeDBInstance.PostgreSQL.md)\.

Enabling the ML capabilities involves the following steps\. 

**Topics**
+ [Setting Up IAM Access to AWS Machine Learning Services](#postgresql-iam-access-ml)
+ [Installing the aws\_ml Extension for Model Inference](#postgresql-ml-aws_ml-install)

### Setting Up IAM Access to AWS Machine Learning Services<a name="postgresql-iam-access-ml"></a>

 Before you can access SageMaker and Amazon Comprehend services, you set up AWS Identity and Access Management \(IAM\) roles\. You then add the IAM roles to the Aurora PostgreSQL cluster\. These roles authorize the users of your Aurora PostgreSQL database to access AWS ML services\. 

You can do IAM setup automatically by using the AWS Management Console as shown here\. To use the AWS CLI to set up IAM access, see [Manually Setting up IAM Roles for SageMaker and Amazon Comprehend Using the AWS CLI](#postgresql-ml-connect-cli)\.

#### Automatically Connecting an Aurora DB Cluster to AWS Services Using the Console<a name="postgresql-ml-connect-console"></a>

 Aurora machine learning requires that your DB cluster use some combination of Amazon S3, SageMaker, and Amazon Comprehend\. Amazon Comprehend is for sentiment analysis\. SageMaker is for a wide variety of machine learning algorithms\. 

For Aurora machine learning, you use Amazon S3 only for training SageMaker models\. You only need to use Amazon S3 with Aurora machine learning if you don't already have a trained model available and the training is your responsibility\. 

To connect a DB cluster to these services requires that you set up an AWS Identity and Access Management \(IAM\) role for each Amazon service\. The IAM role enables users of your DB cluster to authenticate with the corresponding service\. 

 To generate the IAM roles for SageMaker, Amazon Comprehend, or Amazon S3, repeat the following steps for each service that you need\. 

**To connect a DB cluster to an Amazon service**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1.  In the navigation pane, choose **Databases**, and then choose the Aurora PostgreSQL DB cluster that you want to use\. 

1.  Choose the **Connectivity & security** tab\. 

1.  Choose **Select a service to connect to this cluster** in the **Manage IAM roles** section\. 

1.  Choose the service that you want to connect to from the list: 
   +  **Amazon S3** 
   +  **Amazon Comprehend** 
   +  **SageMaker** 

1.  Choose **Connect service**\. 

1.  Enter the required information for the specific service on the **Connect cluster** window: 
   +  For SageMaker, enter the Amazon Resource Name \(ARN\) of a SageMaker endpoint\. 

      From the navigation pane of the [SageMaker console](https://console.aws.amazon.com/sagemaker/home), choose **Endpoints** and copy the ARN of the endpoint you want to use\. For details about what the endpoint represents, see [Deploy a Model on Amazon SageMaker Hosting Services](https://docs.aws.amazon.com/sagemaker/latest/dg/how-it-works-hosting.html)\. 
   +  For Amazon Comprehend, you don't specify any additional parameters\. 
   +  For Amazon S3, enter the ARN of an Amazon S3 bucket to use\. 

      The format of an Amazon S3 bucket ARN is `arn:aws:s3:::bucket_name`\. Ensure that the Amazon S3 bucket you use is set up with the requirements for training SageMaker models\. When you train a model, your Aurora DB cluster requires permission to export data to the Amazon S3 bucket, and also to import data from the bucket\. 

      For more about an Amazon S3 bucket ARN, see [Specifying Resources in a Policy](https://docs.aws.amazon.com/AmazonS3/latest/dev/s3-arn-format.html) in the *Amazon Simple Storage Service Developer Guide\.* For more about using an Amazon S3 bucket with SageMaker, see [Step 1: Create an Amazon S3 Bucket](https://docs.aws.amazon.com/sagemaker/latest/dg/gs-config-permissions.html) in the *Amazon SageMaker Developer Guide\. *

1.  Choose **Connect service**\. 

1.  Aurora creates a new IAM role and adds it to the DB cluster's list of **Current IAM roles for this cluster**\. The IAM role's status is initially **In progress**\. The IAM role name is autogenerated with the following pattern for each connected service: 
   +  The Amazon S3 IAM role name pattern is `rds-cluster_ID-S3-role-timestamp`\. 
   +  The SageMaker IAM role name pattern is `rds-cluster_ID-SageMaker-role-timestamp`\. 
   +  The Amazon Comprehend IAM role name pattern is `rds-cluster_ID-Comprehend-role-timestamp`\. 

    Aurora also creates a new IAM policy and attaches it to the role\. The policy name follows a similar naming convention and also has a timestamp\. 

### Installing the aws\_ml Extension for Model Inference<a name="postgresql-ml-aws_ml-install"></a>

After you create the required IAM roles and associate them with the Aurora PostgreSQL DB cluster, install the functions that use the SageMaker and Amazon Comprehend functionality\. The `aws_ml` Aurora PostgreSQL extension provides the `aws_sagemaker.invoke_endpoint` function that communicates directly with SageMaker\. The `aws_ml` extension also provides the `aws_comprehend.detect_sentiment` function that communicates directly with Amazon Comprehend\.

To install these functions in a specific database, enter the following SQL command at a psql prompt\. 

```
psql>CREATE EXTENSION IF NOT EXISTS aws_ml CASCADE;
```

If you create the `aws_ml` extension in the `template1` default database, then the functions are available in each new database that you create\.

To verify the installation, enter the following at a psql prompt\. 

```
psql>\dx
```

If you set up an IAM role for Amazon Comprehend, you can verify the setup as follows\.

```
SELECT sentiment FROM aws_comprehend.detect_sentiment(null, 'I like it!', 'en'); 
```

When you install the `aws_ml` extension, the `aws_ml` administrative role is created and granted to the `rds_superuser` role\. Separate schemas are also created for the `aws_sagemaker` service and for the `aws_comprehend` service\. The `rds_superuser` role is made the `OWNER` of both of these schemas\. 

For users or roles to obtain access to the functions in the `aws_ml` extension, grant `EXECUTE` privilege on those functions\. You can subsequently REVOKE the privileges, if needed\. `EXECUTE` privileges are revoked from PUBLIC on the functions of these schemas by default\. In a multi\-tenant database configuration, to prevent tenants from accessing the functions use `REVOKE USAGE` on one or more of the ML service schemas\.

For a reference to the installed functions of the `aws_ml` extension, see [PostgreSQL Function Reference for Aurora Machine Learning](#postgresql-ml-functions)\. 

## Using Amazon Comprehend for Natural Language Processing<a name="postgresql-using-comprehend"></a>

Amazon Comprehend uses machine learning to find insights and relationships in text\. Amazon Comprehend uses natural language processing to extract insights about the content of documents\. It develops insights by recognizing the entities, key phrases, language, sentiments, and other common elements in a document\. You can use this Aurora machine learning service with very little machine learning experience\.

Aurora machine learning uses Amazon Comprehend for sentiment analysis of text that is stored in your database\. A *sentiment* is an opinion expressed in text\. Sentiment analysis identifies and categorizes sentiments to determine if the attitude towards something \(such as a topic or product\) is positive, negative, or neutral\.

**Note**  
 Amazon Comprehend is currently available only in some AWS Regions\. To check in which AWS Regions you can use Amazon Comprehend, see [the AWS Region Table](https://aws.amazon.com/about-aws/global-infrastructure/regional-product-services/) page on the AWS site\. 

For example, using Amazon Comprehend you can analyze contact center call\-in documents to detect caller sentiment and better understand caller\-agent dynamics\. You can find a further description in the post [Analyzing contact center calls](http://aws.amazon.com/blogs/machine-learning/analyzing-contact-center-calls-part-1-use-amazon-transcribe-and-amazon-comprehend-to-analyze-customer-sentiment/) on the AWS Machine Learning blog\. 

You can also combine sentiment analysis with the analysis of other information in your database using a single query\. For example, you can detect the average sentiment of call\-in center documents for issues that combine the following: 
+  Open for more than 30 days\. 
+  About a specific product or feature\. 
+  Made by the customers who have the greatest social media influence\. 

Using Amazon Comprehend from Aurora Machine Learning is as easy as calling a SQL function\. When you installed the `aws_ml` extension \([Installing the aws\_ml Extension for Model Inference](#postgresql-ml-aws_ml-install)\), it provides the [aws\_comprehend\.detect\_sentiment](#aws_comprehend.detect_sentiment) function to perform sentiment analysis through Amazon Comprehend\. 

For each text fragment that you analyze, this function helps you determine the sentiment and the confidence level\. A typical Amazon Comprehend query looks for table rows where the sentiment has a certain value \(POSITIVE or NEGATIVE\), with a confidence level greater than a certain percent\. 

For example, the following query shows how to determine the average sentiment of documents in a database table, `myTable.document`\. The query considers only documents where the confidence of the assessment is at least 80 percent\. In the following example, English \(`en`\) is the language of the sentiment text\.

```
SELECT AVG(CASE s.sentiment 
    WHEN 'POSITIVE' then 1 
    WHEN 'NEGATIVE' then -1 
    ELSE 0 END) as avg_sentiment, COUNT(*) AS total
FROM myTable, aws_comprehend.detect_sentiment (myTable.document, 'en') s
WHERE s.confidence >= 0.80;
```

To avoid your being charged for sentiment analysis more than once per table row, you can materialize the results of the analysis once per row\. Do this on the rows of interest\. In the following example, French \(`fr`\) is the language of the sentiment text\.

```
-- *Example:* Update the sentiment and confidence of French text.
--
UPDATE clinician_notes
SET sentiment = (aws_comprehend.detect_sentiment (french_notes, 'fr')).sentiment,
    confidence = (aws_comprehend.detect_sentiment (french_notes, 'fr')).confidence
WHERE
    clinician_notes.french_notes IS NOT NULL AND
    LENGTH(TRIM(clinician_notes.french_nodes)) > 0 AND
    clinician_notes.sentiment IS NULL;
```

For more information on optimizing your function calls, see [Best Practices with Aurora Machine Learning](#postgresql-ml-best-practice)\.

For information about parameters and return types for the sentiment detection function, see [aws\_comprehend\.detect\_sentiment](#aws_comprehend.detect_sentiment)\.

## Exporting Data to Amazon S3 for SageMaker Model Training<a name="postgresql-export-to-s3"></a>

Depending on how your team divides the machine learning tasks, you might not perform model training\. If someone else provides the SageMaker model for you, you can skip this section\.

To train SageMaker models, you export data to an Amazon S3 bucket\. The Amazon S3 bucket is used by SageMaker to train your model before it is deployed\. You can query data from an Aurora PostgreSQL DB cluster and save it directly into text files stored in an Amazon S3 bucket\. Then SageMaker consumes the data from the Amazon S3 bucket for training\. For more about SageMaker model training, see [ Train a Model with Amazon SageMaker](https://docs.aws.amazon.com/sagemaker/latest/dg/how-it-works-training.html)\.

**Note**  
When you create an S3 bucket for SageMaker model training or batch scoring, always include the text `sagemaker` in the S3 bucket name\. For more information about creating an S3 bucket for SageMaker, see [Step 1: Create an Amazon S3 Bucket](https://docs.aws.amazon.com/sagemaker/latest/dg/gs-config-permissions.html)\.

For more information about exporting your data, see [Exporting Data from an Aurora PostgreSQL DB Cluster to Amazon S3](postgresql-s3-export.md)\.

## Using SageMaker to Run Your Own ML Models<a name="postgresql-using-sagemaker"></a>

SageMaker is a fully managed machine learning service\. With SageMaker, data scientists and developers build and train machine learning models\. Then they can directly deploy the models into a production\-ready hosted environment\. 

 SageMaker provides access to your data sources so that you can perform exploration and analysis without managing the hardware infrastructure for servers\. SageMaker also provides common machine learning algorithms that are optimized to run efficiently against extremely large datasets in a distributed environment\. With native support for bring\-your\-own\-algorithms and frameworks, SageMaker offers flexible distributed training options that adjust to your specific workflows\. 

**Note**  
Currently, Aurora machine learning supports any SageMaker endpoint that can read and write the comma\-separated value \(CSV\) format, through a `ContentType` value of `text/csv`\. The built\-in SageMaker algorithms that currently accept this format are Random Cut Forest, Linear Learner, and XGBoost\. 

Be sure to deploy the model you are using in the same AWS Region as your Aurora PostgreSQL cluster\. Aurora machine learning always invokes SageMaker endpoints in the same AWS Region as your Aurora cluster\.

When you install the `aws_ml` extension \(as described in [Installing the aws\_ml Extension for Model Inference](#postgresql-ml-aws_ml-install)\), it provides the [aws\_sagemaker\.invoke\_endpoint](#aws_sagemaker.invoke_endpoint) function\. You use this function to invoke your SageMaker model and perform model inference directly from within your SQL database application\. 

**Topics**
+ [Creating a User\-Defined Function to Invoke a SageMaker Model](#postgresql-using-sagemaker-example1)
+ [Passing an Array as Input to a SageMaker Model](#postgresql-using-sagemaker-example-array)
+ [Specifying Batch Size When Invoking a SageMaker Model](#postgresql-using-sagemaker-example3)
+ [Invoking a SageMaker Model that has Multiple Outputs](#postgresql-using-sagemaker-example4)

### Creating a User\-Defined Function to Invoke a SageMaker Model<a name="postgresql-using-sagemaker-example1"></a>

Create a separate user\-defined function to call `aws_sagemaker.invoke_endpoint` for each of your SageMaker models\. Your user\-defined function represents the SageMaker endpoint hosting the model\. The `aws_sagemaker.invoke_endpoint` function runs within the user\-defined function\. User\-defined functions provide many advantages:
+ You can give your ML model its own name instead of only calling `aws_sagemaker.invoke_endpoint` for all of your ML models\. 
+ You can specify the model endpoint URL in just one place in your SQL application code\.
+ You can control `EXECUTE` privileges to each ML function independently\.
+ You can declare the model input and output types using SQL types\. SQL enforces the number and type of arguments passed to your ML model and performs type conversion if necessary\. Using SQL types will also translate `SQL NULL` to the appropriate default value expected by your ML model\.
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

  For more details about parameters, see the [aws\_sagemaker\.invoke\_endpoint](#aws_sagemaker.invoke_endpoint) function reference\.
+ This example uses an INT output type\. If you cast the output from a `varchar` type to a different type, then it must be cast to a PostgreSQL builtin scalar type such as `INTEGER`, `REAL`, `FLOAT`, or `NUMERIC`\. For more information about these types, see [Data Types](https://www.postgresql.org/docs/current/datatype.html) in the PostgreSQL documentation\.
+ Specify `PARALLEL SAFE` to enable parallel query processing\. For more information, see [Exploiting Parallel Query Processing](#postgresql-using-sagemaker-example-parallel)\.
+ Specify `COST 5000` to estimate the cost of running the function\. Use a positive number giving the estimated run cost for the function, in units of `cpu_operator_cost`\.

### Passing an Array as Input to a SageMaker Model<a name="postgresql-using-sagemaker-example-array"></a>

The [aws\_sagemaker\.invoke\_endpoint](#aws_sagemaker.invoke_endpoint) function can have up to 100 input parameters, which is the limit for PostgreSQL functions\. If the SageMaker model requires more than 100 parameters of the same type, pass the model parameters as an array\. 

The following example creates a user\-defined function that passes an array as input to the SageMaker regression model\. 

```
CREATE FUNCTION regression_model (params REAL[], OUT estimate REAL)
AS $$
    SELECT aws_sagemaker.invoke_endpoint (
        'sagemaker_model_endpoint_name', NULL,
        params                            -- model input parameters as an array
        )::REAL                           -- cast output to REAL
$$ LANGUAGE SQL PARALLEL SAFE COST 5000;
```

### Specifying Batch Size When Invoking a SageMaker Model<a name="postgresql-using-sagemaker-example3"></a>

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
+ Use the optional `max_rows_per_batch` parameter to provide control of the number of rows for a batch\-mode function invocation\. If you use a value of NULL, then the query optimizer automatically chooses the maximum batch size\. For more information, see [Optimizing Batch\-Mode Execution for Aurora Machine Learning Function Calls](#postgresql-ml-batch-mode)\.
+ By default, passing NULL as a parameter's value is translated to an empty string before passing to SageMaker\. For this example the inputs have different types\.
+ If you have a non\-text input, or text input that needs to default to some value other than an empty string, use the `COALESCE` statement\. Use `COALESCE` to translate NULL to the desired null replacement value in the call to `aws_sagemaker.invoke_endpoint`\. For the `amount` parameter in this example, a NULL value is converted to 0\.0\. 

### Invoking a SageMaker Model that has Multiple Outputs<a name="postgresql-using-sagemaker-example4"></a>

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
    SELECT (aws_sagemaker.invoke_endpoint(
              'endpt_name', max_rows_per_batch,
              free_cash_flow, debt))::company_forecasts;                                                                                                                  
$$ LANGUAGE SQL PARALLEL SAFE COST 5000;
```

For the composite type, use fields in the same order as they appear in the model output and cast the output of `aws_sagemaker.invoke_endpoint` to your composite type\. The caller can extract the individual fields either by name or with PostgreSQL "\.\*" notation\.

## Best Practices with Aurora Machine Learning<a name="postgresql-ml-best-practice"></a>

 Most of the work in an `aws_ml` function call happens within the external Aurora Machine Learning service\. This separation allows you to scale the resources for the machine learning service independent of your Aurora cluster\. Within Aurora, you mostly focus on making the user\-defined function calls themselves as efficient as possible\. Some aspects that you can influence from your Aurora cluster include:
+ The `max_rows_per_batch` setting for calls to the `aws_ml` functions\.
+ The number of virtual CPUs of the database instance, which determines the maximum degree of parallelism that the database might use when running your ML functions\.
+ the PostgreSQL parameters that control parallel query processing\.

**Topics**
+ [Optimizing Batch\-Mode Execution for Aurora Machine Learning Function Calls](#postgresql-ml-batch-mode)
+ [Exploiting Parallel Query Processing](#postgresql-using-sagemaker-example-parallel)
+ [Using Materialized Views and Materialized Columns](#postgresql-using-sagemaker-example-materialized)

### Optimizing Batch\-Mode Execution for Aurora Machine Learning Function Calls<a name="postgresql-ml-batch-mode"></a>

Typically PostgreSQL runs functions one row at a time\. Aurora Machine Learning can minimize this overhead by combining the calls to the external Aurora Machine Learning service for many rows into batches with an approach called *batch\-mode execution*\. In batch mode, Aurora Machine Learning receives the responses for a batch of input rows, and then delivers the responses back to the running query one row at a time\. This optimization improves the throughput of your Aurora queries without limiting the PostgreSQL query optimizer\. 

Aurora automatically uses batch mode if the function is referenced from the `SELECT` list, a `WHERE` clause, or a `HAVING` clause\. Note that top\-level simple `CASE` expressions are eligible for batch\-mode execution\. Top\-level searched `CASE` expressions are also eligible for batch\-mode execution provided that the first `WHEN` clause is a simple predicate with a batch\-mode function call\. 

Your user\-defined function must be a `LANGUAGE SQL` function and should specify `PARALLEL SAFE` and `COST 5000`\. 

**Topics**
+ [Function Migration from the SELECT Statement to the FROM Clause](#postgresql-ml-batch-mode-function-migration)
+ [Using the max\_rows\_per\_batch Parameter](#postgresql-ml-batch-mode-max_rows_per_batch)
+ [Verifying Batch\-Mode Execution](#postgresql-ml-batch-mode-verify)

#### Function Migration from the SELECT Statement to the FROM Clause<a name="postgresql-ml-batch-mode-function-migration"></a>

Usually, an `aws_ml` function that is eligible for batch\-mode execution is automatically migrated by Aurora to the `FROM` clause\. 

The migration of eligible batch\-mode functions to the FROM clause can be examined manually on a per\-query level\. To do this, you use EXPLAIN statements \(and ANALYZE and VERBOSE\) and find the "Batch Processing" information below each batch\-mode `Function Scan`\. You can also use EXPLAIN \(with VERBOSE\) without running the query\. You then observe whether the calls to the function appear as a `Function Scan` under a nested loop join that was not specified in the original statement\. 

In the following example, the presence of the nested loop join operator in the plan shows that Aurora migrated the `anomaly_score` function\. It migrated this function from the SELECT list to the FROM clause, where it's eligible for batch\-mode execution\.

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

The `apg_enable_function_migration` parameter is a Grand Unified Configuration \(GUC\) parameter that is recognized by the Aurora PostgreSQL `apg_plan_mgmt` extension for query plan management\. To disable function migration in a session, use query plan management to save the resulting plan as an `approved` plan\. At runtime, query plan management enforces the `approved` plan with its `apg_enable_function_migration` setting\. This enforcement occurs regardless of the `apg_enable_function_migration` GUC parameter setting\. For more information, see [Managing Query Execution Plans for Aurora PostgreSQL](AuroraPostgreSQL.Optimize.md)\.

#### Using the max\_rows\_per\_batch Parameter<a name="postgresql-ml-batch-mode-max_rows_per_batch"></a>

The `max_rows_per_batch` parameter of the [aws\_sagemaker\.invoke\_endpoint](#aws_sagemaker.invoke_endpoint) and [aws\_comprehend\.detect\_sentiment](#aws_comprehend.detect_sentiment) functions influences how many rows are transferred to the Aurora Machine Learning service\. The larger the dataset processed by the user\-defined function, the larger you can make the batch size\.

Batch\-mode functions improve efficiency by building batches of rows that spread the cost of the Aurora Machine Learning function calls over a large number of rows\. However, if a `SELECT` statement finishes early due to a `LIMIT` clause, then the batch can be constructed over more rows than the query uses\. This approach can result in additional charges to your AWS account\. To gain the benefits of batch\-mode execution but avoid building batches that are too large, use a smaller value for the `max_rows_per_batch` parameter in your function calls\.

If you do an `EXPLAIN` \(`VERBOSE`, `ANALYZE`\) of a query that uses batch\-mode execution, you see a `FunctionScan` operator that is below a nested loop join\. The number of loops reported by `EXPLAIN` tells you the number of times a row was fetched from the `FunctionScan` operator\. If a statement uses a LIMIT clause, the number of fetches is consistent\. To optimize the size of the batch, set the `max_rows_per_batch` parameter to this value\. However, if the batch\-mode function is referenced in a predicate in the `WHERE` clause or `HAVING` clause, then you probably can't know the number of fetches in advance\. In this case, use the loops as a guideline and experiment with `max_rows_per_batch` to find a setting that optimizes performance\.

#### Verifying Batch\-Mode Execution<a name="postgresql-ml-batch-mode-verify"></a>

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

### Exploiting Parallel Query Processing<a name="postgresql-using-sagemaker-example-parallel"></a>

To dramatically increase performance when processing a large number of rows, you can combine parallel query processing with batch mode processing\. You can use parallel query processing for `SELECT`, `CREATE TABLE AS SELECT`, and `CREATE MATERIALIZED VIEW` statements\.

**Note**  
PostgreSQL doesn't yet support parallel query for data manipulation language \(DML\) statements\.

Parallel query processing occurs both within the database and within the ML service\. The number of cores in the instance class of the database limits the degree of parallelism that can be used when running a query\. The database server can construct a parallel query execution plan that partitions the task among a set of parallel workers\. Then each of these workers can build batched requests containing tens of thousands of rows \(or as many as are allowed by each service\)\. 

The batched requests from all of the parallel workers are sent to the endpoint for the AWS service \(SageMaker, for example\)\. Thus, the number and type of instances behind the AWS service endpoint also limits the degree of parallelism that can be usefully exploited\. Even a two\-core instance class can benefit significantly from parallel query processing\. However, to fully exploit parallelism at higher K degrees, you need a database instance class that has at least K cores\. You also need to configure the AWS service so that it can process K batched requests in parallel\. For SageMaker, you need to configure the SageMaker endpoint for your ML model to have K initial instances of a sufficiently high\-performing instance class\.

To exploit parallel query processing, you can set the `parallel_workers` storage parameter of the table that contains the data that you plan to pass\. You set `parallel_workers` to a batch\-mode function such as `aws_comprehend.detect_sentiment`\. If the optimizer chooses a parallel query plan, the AWS ML services can be called both in batch and in parallel\. You can use the following parameters with the `aws_comprehend.detect_sentiment` function to get a plan with four\-way parallelism\.

```
-- If you change either of the following two parameters, you must restart
-- the database instance for the changes to take effect.
--
-- SET max_worker_processes to 8;  -- default value is 8
-- SET max_parallel_workers to 8;  -- not greater than max_worker_processes

--
SET max_parallel_workers_per_gather to 4;  -- not greater than max_parallel_workers

-- You can set the parallel_workers storage parameter on the table that the data
-- for the ML function is coming from in order to manually override the degree of
-- parallelism that would otherwise be chosen by the query optimizer
--
ALTER TABLE yourTable SET (parallel_workers = 4);

-- Example query to exploit both batch-mode execution and parallel query
--
EXPLAIN (verbose, analyze, buffers, hashes) 
SELECT aws_comprehend.detect_sentiment(description, 'en')).*
FROM yourTable
WHERE id < 100;
```

For more about controlling parallel query, see [Parallel Plans](https://www.postgresql.org/docs/current/parallel-plans.html) in the PostgreSQL documentation\.

### Using Materialized Views and Materialized Columns<a name="postgresql-using-sagemaker-example-materialized"></a>

When you invoke an AWS service such as SageMaker or Amazon Comprehend from your database, your account is charged according to the pricing policy of that service\. To minimize charges to your account, you can materialize the result of calling the AWS service into a materialized column so that the AWS service is not called more than once per input row\. If desired, you can add a `materializedAt` timestamp column to record the time at which the columns were materialized\. 

The latency of an ordinary single\-row `INSERT` statement is typically much less than the latency of calling a batch\-mode function\. Thus, you might not be able to meet the latency requirements of your application if you invoke the batch\-mode function for every single\-row `INSERT` that your application performs\. To materialize the result of calling an AWS service into a materialized column, high\-performance applications generally need to populate the materialized columns\. To do this, they periodically issue an `UPDATE` statement that operates on a large batch of rows at the same time\.

`UPDATE` takes a row\-level lock that can impact a running application\. So you might need to use `SELECT ... FOR UPDATE SKIP LOCKED`, or use `MATERIALIZED VIEW`\. 

Analytics queries that operate on a large number of rows in real time can combine batch\-mode materialization with real\-time processing\. To do this, these queries assemble a `UNION ALL` of the pre\-materialized results with a query over the rows that don't yet have materialized results\. In some cases, such a `UNION ALL` is needed in multiple places, or the query is generated by a third\-party application\. If so, you can create a `VIEW` to encapsulate the `UNION ALL` operation so this detail isn't exposed to the rest of the SQL application\.

You can use a materialized view to materialize the results of an arbitrary `SELECT` statement at a snapshot in time\. You can also use it to refresh the materialized view at any time in the future\. Currently PostgreSQL doesn't support incremental refresh, so each time the materialized view is refreshed the materialized view is fully recomputed\.

You can refresh materialized views with the `CONCURRENTLY` option, which updates the contents of the materialized view without taking an exclusive lock\. Doing this allows a SQL application to read from the materialized view while it's being refreshed\.

## Monitoring Aurora Machine Learning<a name="postgresql-ml-monitoring"></a>

To monitor the functions in the `aws_ml` package, set the `track_functions` parameter and then query the [PostgreSQL pg\_stat\_user\_functions view](https://www.postgresql.org/docs/current/monitoring-stats.html#PG-STAT-USER-FUNCTIONS-VIEW)\. 

 For information about monitoring the performance of the SageMaker operations called from Aurora Machine Learning functions, see [Monitor Amazon SageMaker](https://docs.aws.amazon.com/sagemaker/latest/dg/monitoring-overview.html)\. 

To set `track_functions` at the session level, run the following\.

```
SET track_functions = 'all';
```

Use one of the following values:
+ `all` – Track C language functions and SQL language functions that aren't placed inline\. To track the `aws_ml` functions, use `all` because these functions are implemented in C\.
+ `pl` – Track only procedural\-language functions\.
+ `none` – Disable function statistics tracking\.

 After enabling `track_functions` and running your user\-defined ML function, query the `pg_stat_user_functions` view to get information\. The view includes the number of `calls`, `total_time` and `self_time` for each function\. To view the statistics for the `aws_sagemaker.invoke_endpoint` and `aws_comprehend.detect_sentiment` functions, filter the results by schema names starting with `aws_`\.

```
run your statement
SELECT * FROM pg_stat_user_functions WHERE schemaname LIKE 'aws_%';
SELECT pg_stat_reset();  -- To clear statistics
```

To find the names of your SQL functions that call the `aws_sagemaker.invoke_endpoint` function, query the source code of the functions in the [ PostgreSQL pg\_proc catalog](https://www.postgresql.org/docs/current/catalog-pg-proc.html) table\.

```
SELECT proname FROM pg_proc WHERE prosrc LIKE '%invoke_endpoint%';
```

### Using Query Plan Management to Monitor ML Functions<a name="postgresql-ml-monitoring-query-plan"></a>

If you captured plans using the `apg_plan_mgmt` extension of query plan management, you can then search through all the statements in your workload that refer to these function names\. In your search, you can check `plan_outline` to see if batch\-mode execution was used\. You can also list statement statistics such as execution time and plan cost\. Plans that use batch\-mode function scans contain a `FuncScan` operator in the plan outline\. Functions that aren't run as a join don't contain a `FuncScan` operator\. 

For more about query plan management, see [Managing Query Execution Plans for Aurora PostgreSQL](AuroraPostgreSQL.Optimize.md)\.

To find calls to the `aws_sagemaker.invoke_endpoint` function that don't use batch mode, use the following statement\.

```
\dx apg_plan_mgmt

SELECT sql_hash, plan_hash, status, environment_variables,
       sql_text::varchar(50), plan_outline 
FROM pg_proc, apg_plan_mgmt.dba_plans
WHERE
    prosrc LIKE '%invoke_endpoint%' AND
    sql_text LIKE '%' || proname || '%' AND
    plan_outline NOT LIKE '%"FuncScan"%';
```

The example preceding searches all statements in your workload that call SQL functions that in turn call the `aws_sagemaker.invoke_endpoint` function\.

To obtain detailed runtime statistics for each of these statements, call the `apg_plan_mgmt.get_explain_stmt` function\.

```
SELECT apg_plan_mgmt.get_explain_stmt(sql_hash, plan_hash, 'analyze,verbose,buffers')
FROM pg_proc, apg_plan_mgmt.dba_plans
WHERE
    prosrc LIKE '%invoke_endpoint%' AND
    sql_text LIKE '%' || proname || '%' AND
    plan_outline NOT LIKE '%"FuncScan"%';
```

## PostgreSQL Function Reference for Aurora Machine Learning<a name="postgresql-ml-functions"></a>

**Topics**
+ [aws\_comprehend\.detect\_sentiment](#aws_comprehend.detect_sentiment)
+ [aws\_sagemaker\.invoke\_endpoint](#aws_sagemaker.invoke_endpoint)

### aws\_comprehend\.detect\_sentiment<a name="aws_comprehend.detect_sentiment"></a>

 Performs sentiment analysis using Amazon Comprehend\. For more about usage, see [Using Amazon Comprehend for Natural Language Processing](#postgresql-using-comprehend)\.

**Syntax**

```
aws_comprehend.detect_sentiment (
    IN input_text varchar,      
    IN language_code varchar,  
    IN max_rows_per_batch int, 
    OUT sentiment varchar, 
    OUT confidence real)   
)
```Input Parameters

**input\_text**  
The text to detect sentiment on\.

**language\_code**  
The language of the `input_text`\. For valid values, see [ Languages Supported in Amazon Comprehend](https://docs.aws.amazon.com/comprehend/latest/dg/supported-languages.html#supported-languages-1)\. 

**max\_rows\_per\_batch**  
The maximum number of rows per batch for batch\-mode processing\. For more information, see [Optimizing Batch\-Mode Execution for Aurora Machine Learning Function Calls](#postgresql-ml-batch-mode)\. Output Parameters

**sentiment**  
The sentiment of the text\. Valid values are POSITIVE, NEGATIVE, NEUTRAL, or MIXED\.

**confidence**  
The degree of confidence in the `sentiment` value\. Values range between 1\.0 for 100% to 0\.0 for 0%\.

### aws\_sagemaker\.invoke\_endpoint<a name="aws_sagemaker.invoke_endpoint"></a>

After you train a model and deploy it into production using SageMaker services, your client applications use the `aws_sagemaker.invoke_endpoint` function to get inferences from the model\. The model must be hosted at the specified endpoint and must be in the same AWS Region as the database instance\. For more about usage, see [Using SageMaker to Run Your Own ML Models](#postgresql-using-sagemaker)\.

**Syntax**

```
aws_sagemaker.invoke_endpoint(
    IN endpoint_name varchar,   
    IN max_rows_per_batch int,
    VARIADIC model_input "any",
    OUT model_output varchar  
)
```Input Parameters

**endpoint\_name**  
An endpoint URL that is AWS Region–independent\. 

**max\_rows\_per\_batch**  
The maximum number of rows per batch for batch\-mode processing\. For more information, see [Optimizing Batch\-Mode Execution for Aurora Machine Learning Function Calls](#postgresql-ml-batch-mode)\. 

**model\_input**  
One or more input parameters for the ML model\. These can be any data type\.  
PostgreSQL allows you to specify up to 100 input parameters for a function\. Array data types must be one\-dimensional, but can contain as many elements as are expected by the SageMaker model\. The number of inputs to a SageMaker model is bounded only by the SageMaker 5 MB message size limit\.Output Parameters

**model\_output**  
The SageMaker model's output parameter, as text\.

**Usage Notes**

The `aws_sagemaker.invoke_endpoint` function connects only to a model endpoint in the same AWS Region\. If your database instance has replicas in multiple AWS Regions, always deploy each Amazon SageMaker model to all of those AWS Regions\. 

Calls to `aws_sagemaker.invoke_endpoint` are authenticated using the SageMaker IAM role for the database instance\. 

SageMaker model endpoints are scoped to an individual account and are not public\. The `endpoint_name` URL doesn't contain the account ID\. SageMaker determines the account ID from the authentication token that is supplied by the SageMaker IAM role of the database instance\.

## Manually Setting up IAM Roles for SageMaker and Amazon Comprehend Using the AWS CLI<a name="postgresql-ml-connect-cli"></a>

**Note**  
If you use the AWS Management Console, AWS does the IAM setup for you automatically\. In this case, you can skip the following information and follow the procedure in [Automatically Connecting an Aurora DB Cluster to AWS Services Using the Console](#postgresql-ml-connect-console)\. 

Setting up the IAM roles for SageMaker or Amazon Comprehend using the AWS CLI or the RDS API consists of the following steps: 

1. Create an IAM policy to specify which SageMaker endpoints can be invoked by your Aurora PostgreSQL cluster or to enable access to Amazon Comprehend\. 

1. Create an IAM role to permit your Aurora PostgreSQL database cluster to access AWS ML services\. Also attach the IAM policy created preceding to the IAM role created here\. 

1. Associate the IAM role that you created preceding to the Aurora PostgreSQL database cluster to permit access to AWS ML services\.

**Topics**
+ [Creating an IAM Policy to Access SageMaker using the AWS CLI](#postgresql-ml-sagemaker-policy)
+ [Creating an IAM Policy to Access Amazon Comprehend using the AWS CLI](#postgresql-ml-comprehend-policy)
+ [Creating an IAM Role to Access SageMaker and Amazon Comprehend](#postgresql-ml-creating-iam-role)
+ [Associating an IAM Role with an Aurora PostgreSQL DB Cluster Using the AWS CLI](#postgresql-ml-associating-iam-role)

### Creating an IAM Policy to Access SageMaker using the AWS CLI<a name="postgresql-ml-sagemaker-policy"></a>

**Note**  
 Aurora can create the IAM policy for you automatically\. You can skip the following information and use the procedure in [Automatically Connecting an Aurora DB Cluster to AWS Services Using the Console](#postgresql-ml-connect-console)\. 

 The following policy adds the permissions required by Aurora PostgreSQL to invoke a SageMaker function on your behalf\. You can specify all of your SageMaker endpoints that you need your database applications to access from your Aurora PostgreSQL cluster in a single policy\. 

**Note**  
This policy enables you to specify the AWS Region for a SageMaker endpoint\. However, an Aurora PostgreSQL cluster can only invoke SageMaker models deployed in the same AWS Region as the cluster\.

`{ "Version": "2012-10-17", "Statement": [ { "Sid": "AllowAuroraToInvokeRCFEndPoint", "Effect": "Allow", "Action": "sagemaker:InvokeEndpoint", "Resource": "arn:aws:sagemaker:region:123456789012:endpoint/endpointName" } ] }`

The following AWS CLI command creates an IAM policy with these options\. 

```
aws iam create-policy  --policy-name policy_name --policy-document '{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowAuroraToInvokeRCFEndPoint",
      "Effect": "Allow",
      "Action": "sagemaker:InvokeEndpoint",
      "Resource": "arn:aws:sagemaker:region:123456789012:endpoint/endpointName"
    }
  ]
}'
```

For the next step, see [Creating an IAM Role to Access SageMaker and Amazon Comprehend](#postgresql-ml-creating-iam-role)\. 

### Creating an IAM Policy to Access Amazon Comprehend using the AWS CLI<a name="postgresql-ml-comprehend-policy"></a>

**Note**  
 Aurora can create the IAM policy for you automatically\. You can skip the following information and use the procedure in [Automatically Connecting an Aurora DB Cluster to AWS Services Using the Console](#postgresql-ml-connect-console)\. 

 The following policy adds the permissions required by Aurora PostgreSQL to invoke AWS Amazon Comprehend on your behalf\. 

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowAuroraToInvokeComprehendDetectSentiment",
      "Effect": "Allow",
      "Action": [
        "comprehend:DetectSentiment",
        "comprehend:BatchDetectSentiment"
      ],
      "Resource": "*"
    }
  ]
}
```

**To create an IAM policy to grant access to Amazon Comprehend**

1.  Open the [IAM Management Console](https://console.aws.amazon.com/iam/home?#home)\. 

1.  In the navigation pane, choose **Policies**\. 

1.  Choose **Create policy**\. 

1.  On the **Visual editor** tab, choose **Choose a service**, and then choose **Comprehend**\. 

1.  For **Actions**, choose **Detect Sentiment** and **BatchDetectSentiment**\. 

1.  Choose **Review policy**\. 

1.  For **Name**, enter a name for your IAM policy\. You use this name when you create an IAM role to associate with your Aurora DB cluster\. You can also add an optional **Description** value\. 

1.  Choose **Create policy**\. 

For the net step, see [Creating an IAM Role to Access SageMaker and Amazon Comprehend](#postgresql-ml-creating-iam-role)\. 

### Creating an IAM Role to Access SageMaker and Amazon Comprehend<a name="postgresql-ml-creating-iam-role"></a>

**Note**  
 Aurora can create the IAM role for you automatically\. You can skip the following information and use the procedure in [Automatically Connecting an Aurora DB Cluster to AWS Services Using the Console](#postgresql-ml-connect-console)\. 

 After you create the IAM policies, create an IAM role that the Aurora PostgreSQL DB cluster can assume for your database users to access ML services\. To create an IAM role, follow the steps described in [ Creating a Role to Delegate Permissions to an IAM User](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-user.html)\.

Attach the preceding policies to the IAM role you create\. For more information, see [Attaching an IAM Policy to an IAM User or Role](UsingWithRDS.IAMDBAuth.IAMPolicy.md#UsingWithRDS.IAMDBAuth.IAMPolicy.Attaching)\. 

For more information about IAM roles, see [IAM Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) in the *AWS Identity and Access Management User Guide*\. 

For the next step, see [Associating an IAM Role with an Aurora PostgreSQL DB Cluster Using the AWS CLI](#postgresql-ml-associating-iam-role)\. 

### Associating an IAM Role with an Aurora PostgreSQL DB Cluster Using the AWS CLI<a name="postgresql-ml-associating-iam-role"></a>

**Note**  
 Aurora can associate an IAM role with your DB cluster for you automatically\. You can skip the following information and use the procedure in [Automatically Connecting an Aurora DB Cluster to AWS Services Using the Console](#postgresql-ml-connect-console)\. 

The last process in setting up IAM access is to associate the IAM role and its IAM policy with your Aurora PostgreSQL DB cluster\. Do the following: 

1.  Add the role to the list of associated roles for a DB cluster\.

   To associate the role with your DB cluster, use the AWS Management Console or the [add\-role\-to\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/add-role-to-db-cluster.html) AWS CLI command\. 
   + 

**To add an IAM role for a PostgreSQL DB cluster using the console**

     1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console.aws.amazon.com/rds/](https://console.aws.amazon.com/rds/)\. 

     1. Choose the PostgreSQL DB cluster name to display its details\.

     1. On the **Connectivity & security** tab, in the **Manage IAM roles **section, choose the role to add under **Add IAM roles to this cluster **\. 

     1. Under **Feature**, choose **SageMaker** or **Comprehend**\.

     1. Choose **Add role**\. 
   + 

**To add an IAM role for a PostgreSQL DB cluster using the CLI**  
Use the following command to add the role to the PostgreSQL DB cluster named `my-db-cluster`\. Replace *`your-role-arn`* with the role ARN that you noted in a previous step\. For the value of the `--feature-name` option, use `SageMaker`, `Comprehend`, or `s3Export` depending on which service you want to use\.   
**Example**  

     For Linux, macOS, or Unix:

     ```
     aws rds add-role-to-db-cluster \
        --db-cluster-identifier my-db-cluster \
        --feature-name external-service \
        --role-arn your-role-arn   \
        --region your-region
     ```

     For Windows:

     ```
     aws rds add-role-to-db-cluster ^
        --db-cluster-identifier my-db-cluster ^
        --feature-name external-service ^
        --role-arn your-role-arn ^
        --region your-region
     ```

1.  Set the cluster\-level parameter for each AWS ML service to the ARN for the associated IAM role\. 

   Use the `electroencephalographic`, `miscomprehended`, or both parameters depending on which AWS ML services you intend to use with your Aurora cluster\. 

    Cluster\-level parameters are grouped into DB cluster parameter groups\. To set the preceding cluster parameters, use an existing custom DB cluster group or create a new one\. To create a new DB cluster parameter group, call the `create-db-cluster-parameter-group` command from the AWS CLI, for example: 

   ```
   aws rds create-db-cluster-parameter-group  --db-cluster-parameter-group-name AllowAWSAccessToExternalServices \
        --db-parameter-group-family aurora-postgresql-group --description "Allow access to Amazon S3, Amazon SageMaker, and Amazon Comprehend"
   ```

   Set the appropriate cluster\-level parameter or parameters and the related IAM role ARN values in your DB cluster parameter group\. Do the following\. 

   ```
   aws rds modify-db-cluster-parameter-group \
     --db-cluster-parameter-group-name AllowAWSAccessToExternalServices \
     --parameters "ParameterName=aws_default_s3_role,ParameterValue=arn:aws:iam::123456789012:role/AllowAuroraS3Role,ApplyMethod=pending-reboot" \
     --parameters "ParameterName=aws_default_sagemaker_role,ParameterValue=arn:aws:iam::123456789012:role/AllowAuroraSageMakerRole,ApplyMethod=pending-reboot" \
     --parameters "ParameterName=aws_default_comprehend_role,ParameterValue=arn:aws:iam::123456789012:role/AllowAuroraComprehendRole,ApplyMethod=pending-reboot"
   ```

   Modify the DB cluster to use the new DB cluster parameter group\. Then, reboot the cluster\. The following shows how\. 

   ```
   aws rds modify-db-cluster --db-cluster-identifier your_cluster_id --db-cluster-parameter-group-nameAllowAWSAccessToExternalServices 
   aws rds failover-db-cluster --db-cluster-identifier your_cluster_id
   ```

 When the instance has rebooted, your IAM roles are associated with your DB cluster\. 