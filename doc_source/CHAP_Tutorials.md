# Amazon Aurora tutorials and sample code<a name="CHAP_Tutorials"></a>

The AWS documentation includes several tutorials that guide you through common Amazon Aurora use cases\. Many of these tutorials show you how to use Amazon Aurora with other AWS services\. In addition, you can access sample code in GitHub\. 

**Note**  
You can find more tutorials at the [AWS Database Blog](http://aws.amazon.com/blogs/database/)\. For information about training, see [AWS Training and Certification](https://www.aws.training/)\.

**Topics**
+ [Tutorials in this guide](#CHAP_Tutorials.ThisGuide)
+ [Tutorials in other AWS guides](#CHAP_Tutorials.OtherGuides)
+ [Tutorials and sample code in GitHub](#CHAP_Tutorials.GitHub)

## Tutorials in this guide<a name="CHAP_Tutorials.ThisGuide"></a>

The following tutorials in this guide show you how to perform common tasks with Amazon Aurora:
+ [Tutorial: Create a VPC for use with a DB cluster \(IPv4 only\)](CHAP_Tutorials.WebServerDB.CreateVPC.md)

  Learn how to include a DB cluster in a virtual private cloud \(VPC\) based on the Amazon VPC service\. In this case, the VPC shares data with a web server that is running on an Amazon EC2 instance in the same VPC\.
+ [Tutorial: Create a VPC for use with a DB cluster \(dual\-stack mode\)](CHAP_Tutorials.CreateVPCDualStack.md)

  Learn how to include a DB cluster in a virtual private cloud \(VPC\) based on the Amazon VPC service\. In this case, the VPC shares data with an Amazon EC2 instance in the same VPC\. In this tutorial, you create the VPC for this scenario that works with a database running in dual\-stack mode\. 
+ [Tutorial: Create a web server and an Amazon Aurora DB cluster](TUT_WebAppWithRDS.md)

  Learn how to install an Apache web server with PHP and create a MySQL database\. The web server runs on an Amazon EC2 instance using Amazon Linux, and the MySQL database is an Aurora MySQL DB cluster\. Both the Amazon EC2 instance and the DB cluster run in an Amazon VPC\.
+ [Tutorial: Restore an Amazon Aurora DB cluster from a DB cluster snapshot](tut-restore-cluster.md)

  Learn how to restore a DB cluster from a DB cluster snapshot\.
+ [Tutorial: Use tags to specify which Aurora DB clusters to stop](USER_Tagging.md#Tagging.Aurora.Autostop)

  Learn how to use tags to specify which Aurora DB clusters to stop\.
+ [Tutorial: Log DB instance state changes using Amazon EventBridge](rds-cloud-watch-events.md#log-rds-instance-state)

  Learn how to log a DB instance state change using Amazon EventBridge and AWS Lambda\.

## Tutorials in other AWS guides<a name="CHAP_Tutorials.OtherGuides"></a>

The following tutorials in other AWS guides show you how to perform common tasks with Amazon Aurora:

**Note**  
Some of the tutorials use Amazon RDS DB instances, but they can be adapted to use Aurora DB clusters\.
+ [ Tutorial: Aurora Serverless](https://docs.aws.amazon.com/appsync/latest/devguide/tutorial-rds-resolvers.html) in the *AWS AppSync Developer Guide*

  Learn how to use AWS AppSync to provide a data source for running SQL commands against Aurora Serverless DB clusters with the Data API enabled\. You can use AWS AppSync resolvers to run SQL statements against the Data API with GraphQL queries, mutations, and subscriptions\.
+ [ Tutorial: Rotating a Secret for an AWS Database](https://docs.aws.amazon.com/secretsmanager/latest/userguide/tutorials_db-rotate.html) in the *AWS Secrets Manager User Guide*

  Learn how to create a secret for an AWS database and configure the secret to rotate on a schedule\. You trigger one rotation manually, and then confirm that the new version of the secret continues to provide access\.
+ [ Tutorial: Configuring a Lambda function to access Amazon RDS in an Amazon VPC](https://docs.aws.amazon.com/lambda/latest/dg/services-rds-tutorial.html) in the *AWS Lambda Developer Guide*

  Learn how to create a Lambda function to access a database, create a table, add a few records, and retrieve the records from the table\. You also learn how to invoke the Lambda function and verify the query results\.
+ [ Tutorials and samples](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/tutorials.html) in the *AWS Elastic Beanstalk Developer Guide*

  Learn how to deploy applications that use Amazon RDS databases with AWS Elastic Beanstalk\.
+ [ Using Data from an Amazon RDS Database to Create an Amazon ML Datasource](https://docs.aws.amazon.com/machine-learning/latest/dg/using-amazon-rds-with-amazon-ml.html) in the *Amazon Machine Learning Developer Guide*

  Learn how to create an Amazon Machine Learning \(Amazon ML\) datasource object from data stored in a MySQL DB instance\.
+ [ Manually Enabling Access to an Amazon RDS Instance in a VPC](https://docs.aws.amazon.com/quicksight/latest/user/rds-vpc-access.html) in the *Amazon QuickSight User Guide*

  Learn how to enable Amazon QuickSight access to an Amazon RDS DB instance in a VPC\.

## Tutorials and sample code in GitHub<a name="CHAP_Tutorials.GitHub"></a>

The following tutorials and sample code in GitHub show you how to perform common tasks with Amazon Aurora:

**Note**  
Some of the tutorials use Amazon RDS DB instances, but they can be adapted to use Aurora DB clusters\.
+ [ Creating a Job Posting Site using Amazon Aurora and Amazon Translation Services](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javav2/usecases/Creating_amazon_aurora_app)

  Learn how to create a web application that stores and queries data by using Amazon Aurora, Elastic Beanstalk, and SDK for Java 2\.x\. The application created in this AWS tutorial is a job posting web application\. This application lets an employer, an administrator, or human resources staff alert employees or the public about a job opening within a company\.
+ [ Creating an Aurora Serverless v1 lending library](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/python/cross_service/aurora_rest_lending_library)

  Learn how to create a lending library application where patrons can borrow and return books\. The example uses Aurora Serverless v1 and AWS SDK for Python \(Boto3\)\.
+ [ Creating an Amazon Aurora item tracker application with a Spring REST API that queries Aurora Serverless v1 data using SDK for Java 2\.x](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javav2/usecases/Creating_Spring_RDS_%20Rest)

  Learn how to create a Spring REST API that queries Aurora Serverless v1 data\. It's for use by a React application using SDK for Java 2\.x\.
+ [ Creating an Amazon Aurora item tracker application that queries Aurora Serverless v1 data using AWS SDK for PHP](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/php/cross_service/aurora_item_tracker)

  Learn how to create an application that uses the `RdsDataClient` of the Data API and Aurora Serverless v1 to track and report on work items\. The example uses AWS SDK for PHP\.
+ [ Creating an Amazon Aurora item tracker application that queries Aurora Serverless v1 data using AWS SDK for Python \(Boto3\)](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/python/cross_service/aurora_item_tracker)

  Learn how to create an application that uses the `RdsDataClient` of the Data API and Aurora Serverless v1 to track and report on work items\. The example uses AWS SDK for Python \(Boto3\)\.
+ [ SDK for Go code examples for Amazon RDS](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/go/rds)

  View a collection of SDK for Go code examples for Amazon RDS and Aurora\.
+ [ SDK for Java 2\.x code examples for Amazon RDS](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javav2/example_code/rds/src/main/java/com/example/rds)

  View a collection of SDK for Java 2\.x code examples for Amazon RDS and Aurora\.
+ [ SDK for PHP code examples for Amazon RDS](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/php/example_code/rds)

  View a collection of SDK for PHP code examples for Amazon RDS and Aurora\.
+ [AWS SDK for Python \(Boto3\) code examples for Aurora](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/python/example_code/aurora)

  View a collection of AWS SDK for Python \(Boto3\) code examples for Aurora\.
+ [ SDK for Ruby code examples for Amazon RDS](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/ruby/example_code/rds)

  View a collection of SDK for Ruby code examples for Amazon RDS and Aurora\.