# Limits for Amazon RDS<a name="CHAP_Limits"></a>

This topic describes the resource limits and naming constraints for Amazon Aurora\.

**Topics**
+ [Limits in Amazon RDS](#RDS_Limits.Limits)
+ [Naming Constraints in Amazon RDS](#RDS_Limits.Constraints)
+ [Amazon Aurora File Size Limits](#RDS_Limits.FileSize.Aurora)

## Limits in Amazon RDS<a name="RDS_Limits.Limits"></a>

Each AWS account has limits, for each AWS Region, on the number of Amazon RDS resources that can be created\. Once a limit for a resource has been reached, additional calls to create that resource fail with an exception\.

The following table lists the resources and their limits per region\.


| Resource | Default Limit | 
| --- | --- | 
| Clusters | 40 | 
| Cluster parameter groups | 50 | 
| Cross\-region snapshots copy requests | 5 | 
| DB Instances 1 | 40 | 
| Event subscriptions | 20 | 
| Manual snapshots | 100 | 
| Manual cluster snapshots | 100 | 
| Option groups | 20 | 
| Parameter groups | 50 | 
| Reserved instances | 40 | 
| Rules per DB security group | 20 | 
| Rules per VPC security group | 50 inbound 50 outbound | 
| DB Security groups | 25 | 
| VPC Security groups | 5 | 
| Subnet groups | 50 | 
| Subnets per subnet group | 20 | 
| Tags per resource | 50 | 

1\. By default, you can have up to a total of 40 Amazon RDS DB instances\. If your application requires more DB instances, you can request additional DB instances via this request form [Request RDS DB instance limit](https://aws.amazon.com/contact-us/request-to-increase-the-amazon-rds-db-instance-limit/)\.

## Naming Constraints in Amazon RDS<a name="RDS_Limits.Constraints"></a>

The following table describes naming constraints in Amazon RDS\. 


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

With Aurora, the table size limit is only constrained by the size of the Aurora cluster volume, which has a maximum of 64 tebibytes \(TiB\)\. As a result, the maximum table size for a table in an Aurora DB cluster is 64 TiB\. We recommend that you follow table design best practices, such as partitioning of large tables\.