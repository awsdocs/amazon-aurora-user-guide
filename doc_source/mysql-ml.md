# Using Machine Learning \(ML\) with Aurora MySQL<a name="mysql-ml"></a>

 Aurora machine learning enables you to add machine learning\-based predictions to database applications using the SQL language\. Aurora machine learning makes use of a highly optimized integration between the Aurora database and the AWS machine learning \(ML\) services SageMaker and Amazon Comprehend\. 

 Benefits of Aurora Machine Learning include the following: 
+  You can add ML\-based predictions to your existing database applications\. You don't need to build custom integrations or learn separate tools\. You can embed machine learning processing directly into your SQL query as calls to stored functions\. 
+  The ML integration is a fast way to enable ML services to work with transactional data\. You don't have to move the data out of the database to perform the machine learning operations\. You don't have to convert or reimport the results of the machine learning operations to use them in your database application\. 
+  You can use your existing governance policies to control who has access to the underlying data and to the generated insights\. 

 AWS ML Services are managed services that are set up and run in their own production environments\. Currently, Aurora Machine Learning integrates with Amazon Comprehend for sentiment analysis and SageMaker for a wide variety of ML algorithms\. 

 For general information about Amazon Comprehend, see [Amazon Comprehend](https://aws.amazon.com/comprehend)\. For details about using Aurora and Amazon Comprehend together, see [Using Amazon Comprehend for Sentiment Detection](#using-amazon-comprehend-for-sentiment-detection)\. 

 For general information about SageMaker, see [SageMaker](https://aws.amazon.com/sagemaker)\. For details about using Aurora and SageMaker together, see [Using SageMaker to Run Your Own ML Models](#using-amazon-sagemaker-to-run-your-own-ml-models)\. 

**Topics**
+ [Prerequisites for Aurora Machine Learning](#aurora-ml-prereqs)
+ [Enabling Aurora Machine Learning](#aurora-ml-enabling)
+ [Exporting Data to Amazon S3 for SageMaker Model Training](#exporting-data-to-s3-for-model-training)
+ [Using SageMaker to Run Your Own ML Models](#using-amazon-sagemaker-to-run-your-own-ml-models)
+ [Using Amazon Comprehend for Sentiment Detection](#using-amazon-comprehend-for-sentiment-detection)
+ [Performance Considerations for Aurora Machine Learning](#aurora-ml-performance)
+ [Monitoring Aurora Machine Learning](#aurora-ml-monitoring)
+ [Limitations of Aurora Machine Learning](#aurora-ml-limitations)

## Prerequisites for Aurora Machine Learning<a name="aurora-ml-prereqs"></a>

 Currently, Aurora machine learning requires that the cluster use the Aurora MySQL database engine\. This feature is available on any Aurora cluster running Aurora MySQL 2\.07\.0 and higher\. You can upgrade an older Aurora cluster to one of these releases and use this feature with that cluster\. 

## Enabling Aurora Machine Learning<a name="aurora-ml-enabling"></a>

 Enabling the ML capabilities involves the following steps: 
+  You enable the Aurora cluster to access the Amazon machine learning services SageMaker or Amazon Comprehend, depending the kinds of ML algorithms you want for your application\. 
+  For SageMaker, then you use the Aurora `CREATE FUNCTION` statement to set up stored functions that access inference features\. 
**Note**  
 Aurora machine learning includes built\-in functions that call Amazon Comprehend for sentiment analysis\. You don't need to run any `CREATE FUNCTION` statements if you only use Amazon Comprehend\. 

**Topics**
+ [Setting Up IAM Access to Amazon Comprehend and SageMaker](#setting-up-access-to-amazon-comprehend-and-amazon-sagemaker)
+ [Granting SQL Privileges for Invoking Aurora Machine Learning Services](#aurora-ml-sql-privileges)
+ [Enabling Network Communication from Aurora MySQL to Other AWS Services](#aurora-ml-enabling-interop)

### Setting Up IAM Access to Amazon Comprehend and SageMaker<a name="setting-up-access-to-amazon-comprehend-and-amazon-sagemaker"></a>

 Before you can access SageMaker and Amazon Comprehend services, enable the Aurora MySQL cluster to access the AWS ML services\. For your Aurora MySQL DB cluster to access AWS ML services on your behalf, create and configure AWS Identity and Access Management \(IAM\) roles\. These roles authorize the users of your Aurora MySQL database to access AWS ML services\. 

 When you use the AWS Management Console, AWS does the IAM setup for you automatically\. You can skip the following information and follow the procedure in [Connecting an Aurora DB Cluster to Amazon S3, SageMaker, or Amazon Comprehend Using the Console](#aurora-ml-console)\. 

 Setting up the IAM roles for SageMaker or Amazon Comprehend using the AWS CLI or the RDS API consists of the following steps: 

1.  Create an IAM policy to specify which SageMaker endpoints tan be invoked by your Aurora MySQL cluster or to enable access to Amazon Comprehend\. 

1.  Create an IAM role to permit your Aurora MySQL database cluster to access AWS ML services\. The IAM Policy created above is attached to the IAM role\. 

1.  To permit the Aurora MySQL database cluster to access AWS ML services, you associate the IAM role that you created above to the database cluster\. 

1.  To permit database applications to invoke AWS ML services, you must also grant privileges to specific database users\. For SageMaker, because the calls to the endpoints are wrapped inside a stored function, you also grant `EXECUTE` privileges on the stored functions to any database users that call them\. 

 For general information about how to permit your Aurora MySQL DB cluster to access other AWS services on your behalf, see [Authorizing Amazon Aurora MySQL to Access Other AWS Services on Your Behalf](AuroraMySQL.Integrating.Authorizing.md)\. 

#### Connecting an Aurora DB Cluster to Amazon S3, SageMaker, or Amazon Comprehend Using the Console<a name="aurora-ml-console"></a>

 Aurora machine learning requires that your DB cluster use some combination of Amazon S3, SageMaker, and Amazon Comprehend\. Amazon Comprehend is for sentiment analysis\. SageMaker is for a wide variety of machine learning algorithms\. For Aurora Machine Learning, Amazon S3 is only for training SageMaker models\. You only need to use Amazon S3 with Aurora machine learning if you don't already have a trained model available and the training is your responsibility\. To connect a DB cluster to these services requires that you set up an AWS Identity and Access Management \(IAM\) role for each Amazon service\. The IAM role enables users of your DB cluster to authenticate with the corresponding service\. 

 To generate the IAM roles for Amazon S3, SageMaker, or Amazon Comprehend, repeat the following steps for each service that you need\. 

**To connect a DB cluster to an Amazon service**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1.  In the navigation pane, choose **Databases**, and then choose the Aurora MySQL DB cluster that you want to use\. 

1.  Choose the **Connectivity & security** tab\. 

1.  Choose **Select a service to connect to this cluster** in the **Manage IAM roles** section\. 

1.  Choose the service that you want to connect to from the dropdown list\. 
   +  **Amazon S3** 
   +  **Amazon Comprehend** 
   +  **SageMaker** 

1.  Choose **Connect service**\. 

1.  Enter the required information for the specific service on the **Connect cluster** window: 
   +  For SageMaker, enter the Amazon Resource Name \(ARN\) of an SageMaker endpoint\. For details about what the endpoint represents, see [Deploy a Model on Amazon SageMaker Hosting Services](https://docs.aws.amazon.com/sagemaker/latest/dg/how-it-works-hosting.html)\. 

      In the navigation pane of the [SageMaker console](https://console.aws.amazon.com/sagemaker/home), choose **Endpoints** and copy the ARN of the endpoint you want to use\. 
   +  For Amazon Comprehend, you don't specify any additional parameter\. 
   +  For Amazon S3, enter the ARN of an Amazon S3 bucket to use\. 

      The format of an Amazon S3 bucket ARN is `arn:aws:s3:::bucket_name`\. Ensure that the Amazon S3 bucket you use is set up with the requirements for training SageMaker models\. When you train a model, your Aurora DB cluster requires permission to export data to the Amazon S3 bucket, and also to import data from the bucket\. 

      For more about an Amazon S3 bucket ARN, see [Specifying Resources in a Policy](https://docs.aws.amazon.com/AmazonS3/latest/dev/s3-arn-format.html) in the Amazon Simple Storage Service Developer Guide\. For more about using an Amazon S3 bucket with SageMaker, see [Step 1: Create an Amazon Amazon S3 Bucket](https://docs.aws.amazon.com/sagemaker/latest/dg/gs-config-permissions.html) in the Amazon SageMaker Developer Guide\. 

1.  Choose **Connect service**\. 

1.  Aurora creates a new IAM role and adds it to the DB cluster's list of **Current IAM roles for this cluster**\. The IAM role's status is initially **In progress**\. The IAM role name is autogenerated with the following pattern for each connected service: 
   +  The Amazon S3 IAM role name pattern is `rds-cluster_ID-S3-policy-timestamp`\. 
   +  The SageMaker IAM role name pattern is `rds-cluster_ID-SageMaker-policy-timestamp`\. 
   +  The Amazon Comprehend IAM role name pattern is `rds-cluster_ID-Comprehend-policy-timestamp`\. 

    Aurora also creates a new IAM policy and attaches it to the role\. The policy name follows a similar naming convention and also has a timestamp\. 

#### Creating an IAM Policy to Access SageMaker \(AWS CLI Only\)<a name="aurora-ml-creating-iam-policy-sagemaker"></a>

**Note**  
 When you use the AWS Management Console, Aurora creates the IAM policy automatically\. In that case, you can skip this section\. 

 The following policy adds the permissions required by Aurora MySQL to invoke an SageMaker function on your behalf\. You can specify all of your SageMaker endpoints that you need your database applications to access from your Aurora MySQL cluster in a single policy\. The policy allows you to specify the AWS Region for an SageMaker endpoint\. However, an Aurora MySQL cluster can only invoke SageMaker models deployed in the same AWS Region as the cluster\. 

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowAuroraToInvokeRCFEndPoint",
      "Effect": "Allow",
      "Action": "sagemaker:InvokeEndpoint",
      "Resource": "arn:aws:sagemaker:region:123456789012:endpoint/endpointName"
    }
  ]
}
```

 The following command performs the same operation through the AWS CLI\. 

```
aws iam put-role-policy --role-name role_name --policy-name policy_name --policy-document '{"Version": "2012-10-17", "Statement": [ { "Sid": "AllowAuroraToInvokeRCFEndPoint", "Effect": "Allow", "Action": "sagemaker:InvokeEndpoint", "Resource": "arn:aws:sagemaker:region:123456789012:endpoint/endpointName" }]}'
```

#### Creating an IAM Policy to Access Amazon Comprehend \(AWS CLI Only\)<a name="aurora-ml-creating-iam-policy-comprehend"></a>

**Note**  
 When you use the AWS Management Console, Aurora creates the IAM policy automatically\. In that case, you can skip this section\. 

 The following policy adds the permissions required by Aurora MySQL to invoke AWS Amazon Comprehend on your behalf\. 

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

 The following command performs the same operation through the AWS CLI\. 

```
aws iam put-role-policy --role-name role_name --policy-name policy_name --policy-document '{ "Version": "2012-10-17", "Statement": [ { "Sid": "AllowAuroraToInvokeComprehendDetectSentiment", "Effect": "Allow", "Action": [ "comprehend:DetectSentiment", "comprehend:BatchDetectSentiment" ], "Resource": "*" }]}'
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

1.  Complete the procedure in [Creating an IAM Role to Allow Amazon Aurora to Access AWS Services](AuroraMySQL.Integrating.Authorizing.IAM.CreateRole.md)\. 

#### Creating an IAM Role to Access SageMaker and Amazon Comprehend<a name="aurora-ml-creating-iam-role"></a>

 After you create the IAM policies, create an IAM role that the Aurora MySQL cluster can assume on behalf of your database users to access ML services\. To create an IAM role, you can use the AWS Management Console or the AWS CLI\. To create an IAM role and attach the preceding policies to the role, follow the steps described in [Creating an IAM Role to Allow Amazon Aurora to Access AWS Services](AuroraMySQL.Integrating.Authorizing.IAM.CreateRole.md)\. For more information about IAM roles, see [IAM Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) in the *AWS Identity and Access Management User Guide*\. 

 You can only use a global IAM role for authentication\. You can't use an IAM role associated with a database user or a session\. This requirement is the same as for Aurora integration with the Lambda and Amazon S3 services\. 

#### Associating an IAM Role with an Aurora MySQL DB Cluster \(AWS CLI Only\)<a name="aurora-ml-associating-iam-role"></a>

**Note**  
 When you use the AWS Management Console, Aurora creates the IAM policy automatically\. In that case, you can skip this section\. 

 The last step is to associate the IAM role with the attached IAM policy with your Aurora MySQL DB cluster\. To associate an IAM role with an Aurora DB cluster, you do two things: 

1.  Add the role to the list of associated roles for a DB cluster by using the AWS Management Console, the [add\-role\-to\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/add-role-to-db-cluster.html) AWS CLI command, or the [AddRoleToDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_AddRoleToDBCluster.html) RDS API operation\. 

1.  Set the cluster\-level parameter for the related AWS ML service to the ARN for the associated IAM role\. Use the `aws_default_sagemaker_role`, `aws_default_comprehend_role`, or both parameters depending on which AWS ML services you intend to use with your Aurora cluster\. 

 Cluster\-level parameters are grouped into DB cluster parameter groups\. To set the preceding cluster parameters, use an existing custom DB cluster group or create an new one\. To create a new DB cluster parameter group, call the `create-db-cluster-parameter-group` command from the AWS CLI, as shown following\. 

```
PROMPT> aws rds create-db-cluster-parameter-group  --db-cluster-parameter-group-name AllowAWSAccessToExternalServices \
     --db-parameter-group-family aurora-mysql5.7 --description "Allow access to Amazon S3, AWS Lambda, AWS SageMaker, and AWS Comprehend"
```

 Set the appropriate cluster\-level parameter or parameters and the related IAM role ARN values in your DB cluster parameter group, as shown in the following\. 

```
PROMPT> aws rds modify-db-cluster-parameter-group \
  --db-cluster-parameter-group-name AllowAWSAccessToExternalServices \
  --parameters "ParameterName=aws_default_s3_role,ParameterValue=arn:aws:iam::123456789012:role/AllowAuroraS3Role,ApplyMethod=pending-reboot" \
  --parameters "ParameterName=aws_default_sagemaker_role,ParameterValue=arn:aws:iam::123456789012:role/AllowAuroraSageMakerRole,ApplyMethod=pending-reboot" \
  --parameters "ParameterName=aws_default_comprehend_role,ParameterValue=arn:aws:iam::123456789012:role/AllowAuroraComprehendRole,ApplyMethod=pending-reboot"
```

 Modify the DB cluster to use the new DB cluster parameter group\. Then, reboot the cluster\. The following shows how\. 

```
PROMPT> aws rds modify-db-cluster --db-cluster-identifier your_cluster_id --db-cluster-parameter-group-nameAllowAWSAccessToExternalServices 
PROMPT> aws rds failover-db-cluster --db-cluster-identifier your_cluster_id
```

 When the instance has rebooted, your IAM roles are associated with your DB cluster\. 

### Granting SQL Privileges for Invoking Aurora Machine Learning Services<a name="aurora-ml-sql-privileges"></a>

 After you create the required IAM policies and roles and associating the role to the Aurora MySQL DB cluster, you authorize individual database users to invoke the Aurora Machine Learning stored functions for SageMaker and built\-in functions for Amazon Comprehend\. 

 The database user invoking a native function must be granted the `INVOKE SAGEMAKER` or `INVOKE COMPREHEND` privilege\. To grant this privilege to a user, connect to the DB instance as the master user, and run the following statements\. Substitute the appropriate details for the database user\. 

```
GRANT INVOKE SAGEMAKER ON *.* TO user@domain-or-ip-address
GRANT INVOKE COMPREHEND ON *.* TO user@domain-or-ip-address
```

 For SageMaker, user\-defined functions define the parameters to be sent to the model for producing the inference and to configure the endpoint name to be invoked\. You grant `EXECUTE` permission to the stored functions configured for SageMaker for each of the database users who intend to invoke the endpoint\. 

```
GRANT EXECUTE ON FUNCTION db1.anomaly_score TO user1@domain-or-ip-address1
GRANT EXECUTE ON FUNCTION db2.company_forecasts TO user2@domain-or-ip-address2
```

### Enabling Network Communication from Aurora MySQL to Other AWS Services<a name="aurora-ml-enabling-interop"></a>

 Since SageMaker and Amazon Comprehend are external AWS services, you must also configure your Aurora DB cluster to allow outbound connections to the target AWS service\. For more information, see [Enabling Network Communication from Amazon Aurora MySQL to Other AWS Services](AuroraMySQL.Integrating.Authorizing.Network.md)\. 

 You can use VPC endpoints to connect to Amazon S3\. AWS PrivateLink can't be used to connect Aurora to AWS machine learning services or Amazon S3 at this time\. 

## Exporting Data to Amazon S3 for SageMaker Model Training<a name="exporting-data-to-s3-for-model-training"></a>

 Depending on how your team divides the machine learning tasks, you might not perform this task\. If someone else provides the SageMaker model for you, you can skip this section\. 

  To train SageMaker models, you export data to an Amazon S3 bucket\. The Amazon S3 bucket is used by a Jupyter SageMaker notebook instance to train your model before it is deployed\. You can use the `SELECT INTO OUTFILE S3` statement to query data from an Aurora MySQL DB cluster and save it directly into text files stored in an Amazon S3 bucket\. Then the notebook instance consumes the data from the Amazon S3 bucket for training\. 

 Aurora Machine Learning extends the existing `SELECT INTO OUTFILE` syntax in Aurora MySQL to export data to CSV format\. The generated CSV file can be directly consumed by models that need this format for training purposes\. 

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

## Using SageMaker to Run Your Own ML Models<a name="using-amazon-sagemaker-to-run-your-own-ml-models"></a>

 SageMaker is a fully managed machine learning service\. With SageMaker, data scientists and developers can quickly and easily build and train machine learning models\. Then they can directly deploy the models into a production\-ready hosted environment\. SageMaker provides an integrated Jupyter authoring notebook instance for easy access to your data sources\. That way, you can perform exploration and analysis without managing the hardware infrastructure for servers\. It also provides common machine learning algorithms that are optimized to run efficiently against extremely large datasets in a distributed environment\. With native support for bring\-your\-own\-algorithms and frameworks, SageMaker offers flexible distributed training options that adjust to your specific workflows\. 

 Currently, Aurora Machine Learning supports any SageMaker endpoint that can read and write comma\-separated value format, through a `ContentType` of `text/csv`\. The built\-in SageMaker algorithms that currently accept this format are Random Cut Forest, Linear Learner, 1P, XGBoost, and 3P\. If the algorithms return multiple outputs per item, the Aurora Machine Learning function returns only the first item\. This first item is expected to be a representative result\. 

 Aurora machine learning always invokes SageMaker endpoints in the same AWS Region as your Aurora cluster\. Therefore, for a single\-region Aurora cluster, always deploy the model in the same AWS Region as your Aurora MySQL cluster\. 

 If you are using an Aurora global database, you set up the same integration between the services for each AWS Region that's part of the global database\. In particular, make sure the following conditions are satisfied for all AWS Regions in the global database: 
+  Configure the appropriate IAM roles for accessing external services such as SageMaker, Amazon Comprehend, or Lambda for the global database cluster in each AWS Region\. 
+  Ensure that all AWS Regions have the same trained SageMaker models deployed with the same endpoint names\. Do so before running the `CREATE FUNCTION` statement for your Aurora Machine Learning function in the primary AWS Region\. In a global database, all `CREATE FUNCTION` statements you run in the primary AWS Region are immediately run in all the secondary regions also\. 

 To use models deployed in SageMaker for inference, you create user\-defined functions using the familiar MySQL data definition language \(DDL\) statements for stored functions\. Each stored function represents the SageMaker endpoint hosting the model\. When you define such a function, you specify the input parameters to the model, the specific SageMaker endpoint to invoke, and the return type\. The function returns the inference computed by the SageMaker endpoint after applying the model to the input parameters\. All Aurora Machine Learning stored functions return numeric types or `VARCHAR`\. You can use any numeric type except `BIT`\. Other types, such as `JSON`, `BLOB`, `TEXT`, and `DATE` are not allowed\. Use model input parameters that are the same as the input parameters that you exported to Amazon S3 for model training\. 

```
CREATE FUNCTION function_name (arg1 type1, arg2 type2, ...) -- variable number of arguments
  [DEFINER = user]                                             -- same as existing MySQL CREATE FUNCTION
  RETURNS mysql_type       -- For example, INTEGER, REAL, ...
  [SQL SECURITY { DEFINER | INVOKER } ]                         -- same as existing MySQL CREATE FUNCTION
  ALIAS AWS_SAGEMAKER_INVOKE_ENDPOINT   -- ALIAS replaces the stored function body. Only AWS_SAGEMAKER_INVOKE_ENDPOINT is supported for now.
  ENDPOINT NAME 'endpoint_name'
  [MAX_BATCH_SIZE max_batch_size];     -- default is 10,000
```

 This is a variation of the existing `CREATE FUNCTION` DDL statement\. In the `CREATE FUNCTION` statement that defines the SageMaker function, you don't specify a function body\. Instead, you specify the new keyword `ALIAS` where the function body usually goes\. Currently, Aurora Machine Learning only supports `aws_sagemaker_invoke_endpoint` for this extended syntax\. You must specify the `endpoint_name` parameter\. The optional parameter `max_batch_size` restricts the maximum number of inputs processed in an actual batched request to SageMaker\. An SageMaker endpoint can have different characteristics for each model\. The `max_batch_size` parameter can help to avoid an error caused by inputs that are too large, or to make SageMaker return a response more quickly\. The max\_batch\_size parameter affects the size of an internal buffer used for ML request processing\. Specifying too large a value for `max_batch_size` might cause substantial memory overhead on your DB instance\. 

 We recommend leaving the `MANIFEST` setting at its default value of `OFF`\. Although you can use the `MANIFEST ON` option, some SageMaker features can't directly use the CSV exported with this option\. The manifest format is not compatible with the expected manifest format from SageMaker\. 

 You create a separate stored function for each of your SageMaker models\. This mapping of functions to models is required because an endpoint is associated with a specific model, and each model accepts different parameters\. Using SQL types for the model inputs and the model output type helps to avoid type conversion errors passing data back and forth between the AWS services\. You can control who can apply the model\. You can also control the runtime characteristics by specifying a parameter representing the maximum batch size\. 

 Currently, all Aurora Machine Learning functions have the `NOT DETERMINISTIC` property\. If you don't specify that property explicitly, Aurora sets `NOT DETERMINISTIC` automatically\. This requirement is because the ML model can be changed without any notification to the database\. If that happens, calls to an Aurora Machine Learning function might return different results for the same input within a single transaction\. 

 You can't use the characteristics `CONTAINS SQL`, `NO SQL`, `READS SQL DATA`, or `MODIFIES SQL DATA` in your `CREATE FUNCTION` statement\. 

 Following is an example usage of invoking an SageMaker endpoint to detect anomalies\. There is an SageMaker endpoint `random-cut-forest-model`\. The corresponding model is already trained by the `random-cut-forest` algorithm\. For each input, the model returns an anomaly score\. This example shows the data points whose score is greater than 3 standard deviations \(approximately the 99\.9th percentile\) from the mean score\. 

```
create function anomaly_score(value real) returns real
  alias aws_sagemaker_invoke_endpoint endpoint name 'random-cut-forest-model-demo';
  
set @score_cutoff = (select avg(anomaly_score(value)) + 3 * std(anomaly_score(value)) from nyc_taxi);

select *, anomaly_detection(value) score from nyc_taxi
  where anomaly_detection(value) > @score_cutoff;
```

### Character Set Requirement for SageMaker Functions that Return Strings<a name="note-character-set-of-ml-functions-return-type"></a>

 We recommend specifying a character set of `utf8mb4` as the return type type for your SageMaker functions that return string values\. If that isn't practical, use a large enough string length for the return type to hold a value represented in the `utf8mb4` character set\. The following example shows how to declare the `utf8mb4` character set for your function\. 

```
CREATE FUNCTION my_ml_func(...) RETURNS VARCHAR(5) CHARSET utf8mb4 ALIAS ...
```

 Currently, each SageMaker function that returns a string uses the character set `utf8mb4` for the return value\. The return value uses this character set even if your ML function declares a different character set for its return type implicitly or explicitly\. If your ML function declares a different character set for the return value, the returned data might be silently truncated if you store it in a table column that isn't long enough\. For example, a query with a `DISTINCT` clause creates a temporary table\. Thus, the ML function result might be truncated due to the way strings are handled internally during a query\. 

## Using Amazon Comprehend for Sentiment Detection<a name="using-amazon-comprehend-for-sentiment-detection"></a>

 Amazon Comprehend uses machine learning to find insights and relationships in textual data\. You can use this AWS machine learning service even if you don't have any machine learning experience or expertise\. Aurora machine learning uses Amazon Comprehend for sentiment analysis of text that is stored in your database\. For example, using Amazon Comprehend you can analyze contact center call\-in documents to detect sentiment and better understand caller\-agent dynamics\. You can find a further description in the post [Analyzing contact center calls](http://aws.amazon.com/blogs/machine-learning/analyzing-contact-center-calls-part-1-use-amazon-transcribe-and-amazon-comprehend-to-analyze-customer-sentiment/) on the AWS Machine Learning blog\. 

 You can also combine sentiment analysis with analysis of other information in your database using a single query\. For example, you can detect the average sentiment of call\-in center documents for issues that combine the following: 
+  Open for more than 30 days\. 
+  About a specific product or feature\. 
+  Made by the customers who have the greatest social media influence\. 

 Using Amazon Comprehend from Aurora Machine Learning is as easy as calling a SQL function\. Aurora machine learning provides two built\-in Amazon Comprehend functions, `aws_comprehend_detect_sentiment()` and `aws_comprehend_detect_sentiment_confidence()` to perform sentiment analysis through Amazon Comprehend\. For each text fragment that you analyze, these functions help you to determine the sentiment and the confidence level\. 

```
-- Returns one of 'POSITIVE', 'NEGATIVE', 'NEUTRAL', 'MIXED'
aws_comprehend_detect_sentiment(
  input_text 
  ,language_code
  [,max_batch_size]  -- default is 25. should be greater than 0
)

-- Returns a double value that indicates confidence of the result of aws_comprehend_detect_sentiment.
aws_comprehend_detect_sentiment_confidence(
  input_text
  ,language_code
  [,max_batch_size]  -- default is 25. should be greater than 0.
)
```

 The `max_batch_size` helps you to tune the performance of the Amazon Comprehend function calls\. A large batch size trades off faster performance for greater memory usage on the Aurora cluster\. For more information, see [Performance Considerations for Aurora Machine Learning](#aurora-ml-performance)\. 

 For information about parameters and return types for the sentiment detection functions in Amazon Comprehend, see [DetectSentiment](https://docs.aws.amazon.com/comprehend/latest/dg/API_DetectSentiment.html) 

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

**Note**  
 Amazon Comprehend is currently available only in some AWS Regions\. To check in which AWS Regions you can use Amazon Comprehend, see [the AWS Region Table page](https://aws.amazon.com/about-aws/global-infrastructure/regional-product-services/)\. 

## Performance Considerations for Aurora Machine Learning<a name="aurora-ml-performance"></a>

 Most of the work in an Aurora Machine Learning function call happens within the external ML service\. This separation enables you to scale the resources for the machine learning service independent of your Aurora cluster\. Within Aurora, you mostly focus on making the function calls themselves as efficient as possible\. 

### Query Cache<a name="query-cache"></a>

 The Aurora MySQL query cache doesn't work for ML functions\. Aurora MySQL doesn't store query results in the query cache for any SQL statements that call ML functions\. 

### Batch Optimization for Aurora Machine Learning Function Calls<a name="ml-batch-optimization"></a>

 The main Aurora Machine Learning performance aspect that you can influence from your Aurora cluster is the batch mode setting for calls to the Aurora Machine Learning stored functions\. Machine learning functions typically require substantial overhead, making it impractical to call an external service separately for each row\. Aurora Machine Learning can minimize this overhead by combining the calls to the external Aurora Machine Learning service for many rows into a single batch\. Aurora Machine Learning receives the responses for all the input rows, and delivers the responses, one row at a time, to the query as it runs\. This optimization improves the throughput and latency of your Aurora queries without changing the results\. 

 When you create an Aurora stored function that's connected to an SageMaker endpoint, you define the batch size parameter\. This parameter influences how many rows are transferred for every underlying call to SageMaker\. For queries that process large numbers of rows, the overhead to make a separate SageMaker call for each row can be substantial\. The larger the data set processed by the stored procedure, the larger you can make the batch size\. 

 If the batch mode optimization can be applied to an SageMaker function, you can tell by checking the query plan produced by the `EXPLAIN PLAN` statement\. In this case, the `extra` column in the execution plan includes `Batched machine learning`\. The following example shows a call to an SageMaker function that uses batch mode\. 

```
mysql> create function anomaly_score(val real) returns real alias aws_sagemaker_invoke_endpoint endpoint name 'my-rcf-model-20191126';
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

 The batch optimization for evaluating Aurora Machine Learning functions applies in the following cases: 
+  Function calls within the select list or the `WHERE` clause of `SELECT` statements\. There are some exceptions, as described following\. 
+  Function calls in the `VALUES` list of `INSERT` and `REPLACE` statements\. 
+  ML functions in `SET` values in `UPDATE` statements\. 

  ```
  INSERT INTO MY_TABLE (col1, col2, col3) VALUES
    (ML_FUNC(1), ML_FUNC(2), ML_FUNC(3)),
    (ML_FUNC(4), ML_FUNC(5), ML_FUNC(6));
  UPDATE MY_TABLE SET col1 = ML_FUNC(col2), SET col3 = ML_FUNC(col4) WHERE ...;
  ```

## Monitoring Aurora Machine Learning<a name="aurora-ml-monitoring"></a>

 To monitor the performance of Aurora Machine Learning batch operations, Aurora MySQL includes several global variables that you can query as follows\. 

```
show status like 'Aurora_ml%';
```

 You can reset these status variables by using a `FLUSH STATUS` statement\. Thus, all of the figures represent totals, averages, and so on, since the last time the variable was reset\. 

`Aurora_ml_logical_response_cnt`  
 The aggregate response count that Aurora MySQL receives from the ML services across all queries run by users of the DB instance\. 

`Aurora_ml_actual_request_cnt`  
 The aggregate request count that Aurora MySQL receives from the ML services across all queries run by users of the DB instance\. 

`Aurora_ml_actual_response_cnt`  
 The aggregate response count that Aurora MySQL receives from the ML services across all queries run by users of the DB instance\. 

`Aurora_ml_cache_hit_cnt`  
 The aggregate internal cache hit count that Aurora MySQL receives from the ML services across all queries run by users of the DB instance\. 

`Aurora_ml_single_request_cnt`  
 The aggregate count of ML functions that are evaluated by non\-batch mode across all queries run by users of the DB instance\. 

 For information about monitoring the performance of the SageMaker operations called from Aurora Machine Learning functions, see [Monitor Amazon SageMaker](https://docs.aws.amazon.com/sagemaker/latest/dg/monitoring-overview.html)\. 

## Limitations of Aurora Machine Learning<a name="aurora-ml-limitations"></a>

 The following limitations apply to Aurora machine learning\. 

 You can't use an Aurora machine learning function for a generated\-always column\. The same limitation applies to any Aurora MySQL stored function\. functions aren't compatible with this binary log \(binlog\) format\. for information about generated columns, see [the MySQL documentation](https://dev.mysql.com/doc/refman/5.7/en/create-table-generated-columns.html)\. 

 The setting `—binlog-format=STATEMENT` throws an exception for calls to Aurora machine learning functions\. The reason for the error is that Aurora Machine Learning considers all ML functions to be nondeterministic, and nondeterministic stored functions aren't compatible with this binlog format\. For information about this binlog format, see [the MySQL documentation](https://dev.mysql.com/doc/refman/5.7/en/binary-log-formats.html)\. 