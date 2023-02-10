# What is Amazon Aurora?<a name="CHAP_AuroraOverview"></a>

Amazon Aurora \(Aurora\) is a fully managed relational database engine that's compatible with MySQL and PostgreSQL\. You already know how MySQL and PostgreSQL combine the speed and reliability of high\-end commercial databases with the simplicity and cost\-effectiveness of open\-source databases\. The code, tools, and applications you use today with your existing MySQL and PostgreSQL databases can be used with Aurora\. With some workloads, Aurora can deliver up to five times the throughput of MySQL and up to three times the throughput of PostgreSQL without requiring changes to most of your existing applications\.

Aurora includes a high\-performance storage subsystem\. Its MySQL\- and PostgreSQL\-compatible database engines are customized to take advantage of that fast distributed storage\. The underlying storage grows automatically as needed\. An Aurora cluster volume can grow to a maximum size of 128 tebibytes \(TiB\)\. Aurora also automates and standardizes database clustering and replication, which are typically among the most challenging aspects of database configuration and administration\.

Aurora is part of the managed database service Amazon Relational Database Service \(Amazon RDS\)\. Amazon RDS is a web service that makes it easier to set up, operate, and scale a relational database in the cloud\. If you are not already familiar with Amazon RDS, see the [https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Welcome.html](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Welcome.html)\. To learn more about the variety of database options available on Amazon Web Services, see [Choosing the right database for your organization on AWS](http://aws.amazon.com/getting-started/decision-guides/databases-on-aws-how-to-choose/)\.

**Topics**
+ [Amazon RDS shared responsibility model](#aur-shared-resp)
+ [How Amazon Aurora works with Amazon RDS](#aurora-rds-comparison)
+ [Amazon Aurora DB clusters](Aurora.Overview.md)
+ [Amazon Aurora versions](Aurora.VersionPolicy.md)
+ [Regions and Availability Zones](Concepts.RegionsAndAvailabilityZones.md)
+ [Supported features in Amazon Aurora by AWS Region and Aurora DB engine](Concepts.AuroraFeaturesRegionsDBEngines.grids.md)
+ [Amazon Aurora connection management](Aurora.Overview.Endpoints.md)
+ [Aurora DB instance classes](Concepts.DBInstanceClass.md)
+ [Amazon Aurora storage and reliability](Aurora.Overview.StorageReliability.md)
+ [Amazon Aurora security](Aurora.Overview.Security.md)
+ [High availability for Amazon Aurora](Concepts.AuroraHighAvailability.md)
+ [Replication with Amazon Aurora](Aurora.Replication.md)
+ [DB instance billing for Aurora](User_DBInstanceBilling.md)

## Amazon RDS shared responsibility model<a name="aur-shared-resp"></a>

Amazon RDS is responsible for hosting the software components and infrastructure of DB instances and DB clusters\. You are responsible for query tuning, which is the process of adjusting SQL queries to improve performance\. Query performance is highly dependent on database design, data size, data distribution, application workload, and query patterns, which can vary greatly\. Monitoring and tuning are highly individualized processes that you own for your RDS databases\. You can use Amazon RDS Performance Insights and other tools to identify problematic queries\.

## How Amazon Aurora works with Amazon RDS<a name="aurora-rds-comparison"></a>

The following points illustrate how Amazon Aurora relates to the standard MySQL and PostgreSQL engines available in Amazon RDS:
+ You choose Aurora MySQL or Aurora PostgreSQL as the DB engine option when setting up new database servers through Amazon RDS\.
+ Aurora takes advantage of the familiar Amazon Relational Database Service \(Amazon RDS\) features for management and administration\. Aurora uses the Amazon RDS AWS Management Console interface, AWS CLI commands, and API operations to handle routine database tasks such as provisioning, patching, backup, recovery, failure detection, and repair\.
+ Aurora management operations typically involve entire clusters of database servers that are synchronized through replication, instead of individual database instances\. The automatic clustering, replication, and storage allocation make it simple and cost\-effective to set up, operate, and scale your largest MySQL and PostgreSQL deployments\.
+ You can bring data from Amazon RDS for MySQL and Amazon RDS for PostgreSQL into Aurora by creating and restoring snapshots, or by setting up one\-way replication\. You can use push\-button migration tools to convert your existing RDS for MySQL and RDS for PostgreSQL applications to Aurora\.

Before using Amazon Aurora, complete the steps in [Setting up your environment for Amazon Aurora](CHAP_SettingUp_Aurora.md), and then review the concepts and features of Aurora in [Amazon Aurora DB clusters](Aurora.Overview.md)\.