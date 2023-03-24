# Quotas and constraints for Amazon Aurora<a name="CHAP_Limits"></a>

Following, you can find a description of the resource quotas and naming constraints for Amazon Aurora\.

**Topics**
+ [Quotas in Amazon Aurora](#RDS_Limits.Limits)
+ [Naming constraints in Amazon Aurora](#RDS_Limits.Constraints)
+ [Amazon Aurora size limits](#RDS_Limits.FileSize.Aurora)

## Quotas in Amazon Aurora<a name="RDS_Limits.Limits"></a>

Each AWS account has quotas, for each AWS Region, on the number of Amazon Aurora resources that can be created\. After a quota for a resource has been reached, additional calls to create that resource fail with an exception\.

The following table lists the resources and their quotas per AWS Region\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/CHAP_Limits.html)

**Note**  
By default, you can have up to a total of 40 DB instances\. RDS DB instances, Aurora DB instances, Amazon Neptune instances, and Amazon DocumentDB instances apply to this quota\.  
If your application requires more DB instances, you can request additional DB instances by opening the [Service Quotas console](https://console.aws.amazon.com/servicequotas/home?region=us-east-1#!/dashboard)\. In the navigation pane, choose **AWS services**\. Choose **Amazon Relational Database Service \(Amazon RDS\)**, choose a quota, and follow the directions to request a quota increase\. For more information, see [Requesting a quota increase](https://docs.aws.amazon.com/servicequotas/latest/userguide/request-increase.html) in the *Service Quotas User Guide*\.  
Backups managed by AWS Backup are considered manual DB clustersnapshots, but don't count toward the manual cluster snapshot quota\. For information about AWS Backup, see the [https://docs.aws.amazon.com/aws-backup/latest/devguide](https://docs.aws.amazon.com/aws-backup/latest/devguide)\.

If you use any RDS API operations and exceed the default quota for the number of calls per second, the Amazon RDS API issues an error like the following one\. 

ClientError: An error occurred \(ThrottlingException\) when calling the *API\_name* operation: Rate exceeded\. 

Here, reduce the number of calls per second\. The quota is meant to cover most use cases\. If higher limits are needed, request a quota increase by contacting AWS Support\. Open the [AWS Support Center](https://console.aws.amazon.com/support/home#/) page, sign in if necessary, and choose **Create case**\. Choose **Service limit increase**\. Complete and submit the form\.

**Note**  
This quota can't be changed in the Amazon RDS Service Quotas console\.

## Naming constraints in Amazon Aurora<a name="RDS_Limits.Constraints"></a>

The following table describes naming constraints in Amazon Aurora\. 


****  

| Resource or item | Constraints | 
| --- | --- | 
| DB cluster identifier |  Identifiers have these naming constraints: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/CHAP_Limits.html)  | 
|  Initial database name  |  Database name constraints differ between Aurora MySQL and PostgreSQL\. For more information, see the available settings when creating each DB cluster\.  | 
|  Master user name  |  Master user name constraints differ for each database engine\. For more information, see the available settings when creating each DB cluster\.  | 
|  Master password  |  The password for the database master user can include any printable ASCII character except `/`, `'`, `"`, `@`, or a space\. The password has the following number of printable ASCII characters depending on the DB engine:   [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/CHAP_Limits.html)  | 
| DB parameter group name |  These names have these constraints: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/CHAP_Limits.html)  | 
|  DB subnet group name  |  These names have these constraints: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/CHAP_Limits.html)  | 

## Amazon Aurora size limits<a name="RDS_Limits.FileSize.Aurora"></a>

**Storage size limits**  
An Aurora cluster volume can grow to a maximum size of 128 tebibytes \(TiB\) for the following engine versions:  
+ All available Aurora MySQL version 3 versions; Aurora MySQL version 2, versions 2\.09 and higher
+ All available Aurora PostgreSQL versions
For lower engine versions, the maximum size of an Aurora cluster volume is 64 TiB\. For more information, see [How Aurora storage automatically resizes](Aurora.Overview.StorageReliability.md#aurora-storage-growth)\.  
To monitor the remaining storage space, you can use the `AuroraVolumeBytesLeftTotal` metric\. For more information, see [Cluster\-level metrics for Amazon Aurora](Aurora.AuroraMonitoring.Metrics.md#Aurora.AuroraMySQL.Monitoring.Metrics.clusters)\.

**SQL table size limits**  
For an Aurora MySQL DB cluster, the maximum table size is 64 tebibytes \(TiB\)\. For an Aurora PostgreSQL DB cluster, the maximum table size is 32 tebibytes \(TiB\)\. We recommend that you follow table design best practices, such as partitioning of large tables\.

**Table space ID limits**  
The maximum table space ID for Aurora MySQL is 2147483647\. If you frequently create and drop tables, make sure to be aware of your table space IDs and plan to use logical dumps\. For more information, see [Migrating from MySQL to Amazon Aurora by using mysqldump](AuroraMySQL.Migrating.ExtMySQL.md#AuroraMySQL.Migrating.ExtMySQL.mysqldump)\.