# Limitations of Aurora Serverless v2 \(preview\)<a name="aurora-serverless-2.limitations"></a>


****  

|  | 
| --- |
| Amazon Aurora Serverless v2 with MySQL compatibility is in preview release and is subject to change\. Aurora Serverless v2 \(preview\) is not covered by the Amazon RDS service level agreement \(SLA\)\. Don't use Aurora Serverless v2 \(preview\) for production databases\. | 

The following limitations apply to Amazon Aurora Serverless v2 \(preview\):
+ You can work with Amazon Aurora Serverless v2 \(preview\) in the preview environment only, and you are limited to working with Aurora Serverless v2 \(preview\) in this environment\. To access any of your other existing AWS services, such as Amazon EC2, you must access them using your regular AWS Management Console in the appropriate Regions and endpoints\.
+ You can work with Amazon Aurora Serverless v2 \(preview\) using the AWS Management Console only\. AWS CLI commands and Amazon RDS API operations for creating and working Aurora Serverless v2 \(preview\) DB clusters aren't currently available\. 
+ You can create only Aurora MySQL 5\.7 \(2\.07\) DB clusters using Aurora Serverless v2 \(preview\)\. Aurora PostgreSQL isn't currently available for Aurora Serverless v2 \(preview\)\. 
+ Your Aurora Serverless v2 \(preview\) DB clusters have a default capacity that ranges from a minimum of 4 Aurora capacity units \(ACUs\) to 32 ACUs\. Each ACU provides the equivalent of approximately 2 GiB \(gibibytes\) of RAM \(memory\) and a corresponding amount of CPU and networking\.
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
  + Integration with AWS Lambda
  + Query editor for Aurora Serverless 
  + Restoring snapshots from Aurora provisioned DB clusters