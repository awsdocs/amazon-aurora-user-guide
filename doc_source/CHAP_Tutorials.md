# Amazon Aurora Tutorials<a name="CHAP_Tutorials"></a>

The AWS documentation includes several tutorials that guide you through common Amazon Aurora use cases\. Many of these tutorials show you how to use Amazon Aurora with other AWS services\. 

**Note**  
You can find more tutorials at the [AWS Database Blog](http://aws.amazon.com/blogs/database/)\. For information about training, see [AWS Training and Certification](https://www.aws.training/)\.

## Tutorials in this guide<a name="CHAP_Tutorials.ThisGuide"></a>

The following tutorials in this guide show you how to perform common tasks with Amazon Aurora:
+ [Tutorial: Create an Amazon VPC for use with a DB instance](CHAP_Tutorials.WebServerDB.CreateVPC.md)

  Learn how to include a DB cluster in an Amazon virtual private cloud \(VPC\) that shares data with a web server that is running on an Amazon EC2 instance in the same VPC\.
+ [Tutorial: Create a web server and an Amazon Aurora DB cluster](TUT_WebAppWithRDS.md)

  Learn how to install an Apache web server with PHP and create a MySQL database\. The web server runs on an Amazon EC2 instance using Amazon Linux, and the MySQL database is an Aurora MySQL DB cluster\. Both the Amazon EC2 instance and the DB cluster run in an Amazon VPC\.
+ [Tutorial: Use tags to specify which Aurora DB clusters to stop](USER_Tagging.md#Tagging.Aurora.Autostop)

  Learn how to use tags to specify which Aurora DB clusters to stop\.

## Tutorials in other AWS guides<a name="CHAP_Tutorials.OtherGuides"></a>

The following tutorials in other AWS guides show you how to perform common tasks with Amazon Aurora:

**Note**  
Some of the tutorials use Amazon RDS DB instances, but they can be adapted to use Aurora DB clusters\.
+ [ Tutorial: Aurora Serverless](https://docs.aws.amazon.com/appsync/latest/devguide/tutorial-rds-resolvers.html) in the *AWS AppSync Developer Guide*

  Learn how to use AWS AppSync to provide a data source for executing SQL commands against Aurora Serverless v1 DB clusters with the Data API enabled\. You can use AWS AppSync resolvers to execute SQL statements against the Data API with GraphQL queries, mutations, and subscriptions\.
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