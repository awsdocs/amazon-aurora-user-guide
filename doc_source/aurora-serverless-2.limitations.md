# Limitations of Aurora Serverless v2 \(preview\)<a name="aurora-serverless-2.limitations"></a>


****  

|  | 
| --- |
| Amazon Aurora Serverless v2 with MySQL compatibility is in preview release and is subject to change\. Aurora Serverless v2 \(preview\) is not covered by the Amazon RDS service level agreement \(SLA\)\. Don't use Aurora Serverless v2 \(preview\) for production databases\. All resources and data will be deleted when the preview ends\.  | 

The following limitations apply to Amazon Aurora Serverless v2 \(preview\):
+ You can work with Amazon Aurora Serverless v2 \(preview\) in the preview environment only\. You are limited to working with Aurora Serverless v2 \(preview\) in this environment\. To use any of your other existing AWS services, such as Amazon EC2 and Amazon CloudWatch, access them through the AWS Management Console in the US East \(N\. Virginia\) Region\.
+ You can work with Amazon Aurora Serverless v2 \(preview\) using the console only\. AWS CLI commands and Amazon RDS API operations for creating and working Aurora Serverless v2 \(preview\) DB clusters aren't currently available\. 
+ You can create only Aurora MySQL 5\.7 \(2\.07\) DB clusters using Aurora Serverless v2 \(preview\)\. Aurora PostgreSQL isn't currently available for Aurora Serverless v2 \(preview\)\. 
+ Your Aurora Serverless v2 \(preview\) DB clusters have a default capacity that ranges from a minimum of 4 Aurora capacity units \(ACUs\) to 32 ACUs\. Each ACU provides the equivalent of approximately 2 gibibytes \(GiB\) of RAM and associated CPU and networking\.
+ You can't give an Aurora Serverless v2 \(preview\) DB cluster a public IP address\. 
+ You must create your Aurora Serverless v2 \(preview\) DB cluster in a virtual private cloud \(VPC\) based on the Amazon VPC service\. You can access your Aurora Serverless v2 \(preview\) DB cluster only from within a VPC based on Amazon VPC\. 
+ Aurora Serverless v2 \(preview\) databases are accessible only through port 3306\. Aurora Serverless v2 \(preview\) assigns port 3306 to any Aurora MySQL DB instances that you create\. You can't change the port number\. 
+ Aurora Serverless v2 \(preview\) doesn't support the following Aurora features:
  + Amazon RDS Performance Insights
  + Amazon RDS Proxy
  + Aurora backtracking
  + Aurora cloning
  + Aurora global databases
  + Aurora multi\-master
  + Aurora Replicas
  + AWS Identity and Access Management \(IAM\) database authentication
  + Data API for Aurora Serverless
  + Exporting snapshots created from Aurora Serverless v2 \(preview\) DB clusters to Amazon S3 buckets
  + Importing data from Amazon S3 into Aurora Serverless v2 \(preview\) DB cluster tables
  + Invoking AWS Lambda functions from within your Aurora Serverless v2 \(preview\) database
  + Query editor for Aurora Serverless 
  + Restoring snapshots from Aurora provisioned DB clusters