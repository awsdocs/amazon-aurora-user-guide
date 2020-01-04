# Quotas for Amazon Aurora<a name="CHAP_Limits"></a>

Following, you can find a description of the resource quotas and naming constraints for Amazon Aurora\.

**Topics**
+ [Quotas in Amazon Aurora](#RDS_Limits.Limits)
+ [Naming Constraints in Amazon Aurora](#RDS_Limits.Constraints)
+ [Amazon Aurora File Size Limits](#RDS_Limits.FileSize.Aurora)

## Quotas in Amazon Aurora<a name="RDS_Limits.Limits"></a>

Each AWS account has quotas, for each AWS Region, on the number of Amazon Aurora resources that can be created\. After a quota for a resource has been reached, additional calls to create that resource fail with an exception\.

The following table lists the resources and their quotas per AWS Region\.


| Resource | Default Quota | 
| --- | --- | 
| Authorizations per DB security group | 20 | 
| Cross\-region snapshots copy requests | 5 | 
| DB clusters | 40 | 
| DB cluster parameter groups | 50 | 
| DB instances | 40 | 
| DB subnet groups | 50 | 
| Event subscriptions | 20 | 
| IAM roles per DB cluster | 5 | 
| IAM roles per DB instance | 5 | 
| Manual snapshots | 100 | 
| Manual cluster snapshots | 100 | 
| Parameter groups | 50 | 
| Reserved DB instances | 40 | 
| Rules per virtual private cloud \(VPC\) security group | 50 inbound, 50 outbound | 
| Subnets per subnet group | 20 | 
| Tags per resource | 50 | 
| VPC security groups | 5 | 

**Note**  
By default, you can have up to a total of 40 DB instances\. RDS DB instances, Aurora DB instances, Amazon Neptune instances, and Amazon DocumentDB instances apply to this quota\. If your application requires more DB instances, you can request additional DB instances by opening the [Service Quotas console](https://console.aws.amazon.com/servicequotas/home?region=us-east-1#!/dashboard)\. In the navigation pane, choose **AWS services**\. Choose **Amazon Relational Database Service \(Amazon RDS\)**, choose a quota, and follow the directions to request a quota increase\. For more information, see [Requesting a Quota Increase](https://docs.aws.amazon.com/servicequotas/latest/userguide/request-increase.html) in the *Service Quotas User Guide*\.

## Naming Constraints in Amazon Aurora<a name="RDS_Limits.Constraints"></a>

The following table describes naming constraints in Amazon Aurora\. 


****  

|  |  | 
| --- |--- |
| DB instance identifier |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/CHAP_Limits.html)  | 
|  Database name  |  Database name constraints differ for each database engine\.      **Amazon Aurora MySQL**  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/CHAP_Limits.html)  **Amazon Aurora PostgreSQL**  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/CHAP_Limits.html)  | 
|  Master user name  |  Master user name constraints differ for each database engine\.       **Amazon Aurora MySQL**  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/CHAP_Limits.html)  **Amazon Aurora PostgreSQL**  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/CHAP_Limits.html)  | 
|  Master password  |  The password for the master database user can be any printable ASCII character except "/", """, or "@"\. Master password constraints differ for each database engine\.      **Amazon Aurora MySQL**  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/CHAP_Limits.html)  **Amazon Aurora PostgreSQL**  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/CHAP_Limits.html)  | 
| DB parameter group name |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/CHAP_Limits.html)  | 
|  DB subnet group name  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/CHAP_Limits.html)  | 

## Amazon Aurora File Size Limits<a name="RDS_Limits.FileSize.Aurora"></a>

With Aurora, the table size limit is only constrained by the size of the Aurora cluster volume\. As a result, the maximum table size for an Aurora MySQL DB cluster is 64 tebibytes \(TiB\) and for an Aurora PostgreSQL DB cluster it is 32 TiB\. We recommend that you follow table design best practices, such as partitioning of large tables\.