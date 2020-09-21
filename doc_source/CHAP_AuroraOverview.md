# What Is Amazon Aurora?<a name="CHAP_AuroraOverview"></a>

 Amazon Aurora \(Aurora\) is a fully managed relational database engine that's compatible with MySQL and PostgreSQL\. You already know how MySQL and PostgreSQL combine the speed and reliability of high\-end commercial databases with the simplicity and cost\-effectiveness of open\-source databases\. The code, tools, and applications you use today with your existing MySQL and PostgreSQL databases can be used with Aurora\. With some workloads, Aurora can deliver up to five times the throughput of MySQL and up to three times the throughput of PostgreSQL without requiring changes to most of your existing applications\. 

 Aurora includes a high\-performance storage subsystem\. Its MySQL\- and PostgreSQL\-compatible database engines are customized to take advantage of that fast distributed storage\. The underlying storage grows automatically as needed, up to 128 tebibytes \(TiB\) for Aurora MySQL and 64 TiB for Aurora PostgreSQL\. Aurora also automates and standardizes database clustering and replication, which are typically among the most challenging aspects of database configuration and administration\. 

 Aurora is part of the managed database service Amazon Relational Database Service \(Amazon RDS\)\. Amazon RDS is a web service that makes it easier to set up, operate, and scale a relational database in the cloud\. If you are not already familiar with Amazon RDS, see the [ *Amazon Relational Database Service User Guide*](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Welcome.html)\. 

 The following points illustrate how Aurora relates to the standard MySQL and PostgreSQL engines available in Amazon RDS: 
+  You choose Aurora as a DB engine option when setting up new database servers through Amazon RDS\. 
+  Aurora takes advantage of the familiar Amazon Relational Database Service \(Amazon RDS\) features for management and administration\. Aurora uses the Amazon RDS AWS Management Console interface, AWS CLI commands, and API operations to handle routine database tasks such as provisioning, patching, backup, recovery, failure detection, and repair\. 
+  Aurora management operations typically involve entire clusters of database servers that are synchronized through replication, instead of individual database instances\. The automatic clustering, replication, and storage allocation make it simple and cost\-effective to set up, operate, and scale your largest MySQL and PostgreSQL deployments\. 
+  You can bring data from Amazon RDS for MySQL and Amazon RDS for PostgreSQL into Aurora by creating and restoring snapshots, or by setting up one\-way replication\. You can use push\-button migration tools to convert your existing Amazon RDS for MySQL and Amazon RDS for PostgreSQL applications to Aurora\. 

 Before using Amazon Aurora, you should complete the steps in [Setting Up Your Environment for Amazon Aurora](CHAP_SettingUp_Aurora.md), and then review the concepts and features of Aurora in [Amazon Aurora DB Clusters](Aurora.Overview.md)\. 

**Topics**
+ [Amazon Aurora DB Clusters](Aurora.Overview.md)
+ [Regions and Availability Zones](Concepts.RegionsAndAvailabilityZones.md)
+ [Amazon Aurora Connection Management](Aurora.Overview.Endpoints.md)
+ [DB Instance Classes](Concepts.DBInstanceClass.md)
+ [Amazon Aurora Storage and Reliability](Aurora.Overview.StorageReliability.md)
+ [Amazon Aurora Security](Aurora.Overview.Security.md)
+ [High Availability for Amazon Aurora](Concepts.AuroraHighAvailability.md)
+ [Aurora Global Databases](Concepts.Aurora.GlobalDB.md)
+ [Replication with Amazon Aurora](Aurora.Replication.md)
+ [DB Instance Billing for Aurora](User_DBInstanceBilling.md)