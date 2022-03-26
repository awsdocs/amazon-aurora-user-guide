# Connecting to an Amazon Aurora DB cluster<a name="Aurora.Connecting"></a>

You can connect to an Aurora DB cluster using the same tools that you use to connect to a MySQL or PostgreSQL database\. You specify a connection string with any script, utility, or application that connects to a MySQL or PostgreSQL DB instance\. You use the same public key for Secure Sockets Layer \(SSL\) connections\. 

In the connection string, you typically use the host and port information from special endpoints associated with the DB cluster\. With these endpoints, you can use the same connection parameters regardless of how many DB instances are in the cluster\. You also use the host and port information from a specific DB instance in your Aurora DB cluster for specialized tasks, such as troubleshooting\. 

**Note**  
For Aurora Serverless v1 DB clusters, you connect to the database endpoint rather than to the DB instance\. You can find the database endpoint for an Aurora Serverless v1 DB cluster on the **Connectivity & security** tab of the AWS Management Console\. For more information, see [Using Amazon Aurora Serverless v1](aurora-serverless.md)\. 

Regardless of the Aurora DB engine and specific tools you use to work with the DB cluster or instance, the endpoint must be accessible\. An Amazon Aurora DB cluster can be created only in a virtual private cloud \(VPC\) based on the Amazon VPC service\. That means that you access the endpoint from either inside the VPC or outside the VPC using one of the following approaches\.
+ **Access the Amazon Aurora DB cluster inside the VPC** – Enable access to the Amazon Aurora DB cluster through the VPC\. You do so by editing the Inbound rules on the Security group for the VPC to allow access to your specific Aurora DB cluster\. To learn more, including how to configure your VPC for different Aurora DB cluster scenarios, see [Amazon Virtual Private Cloud VPCs and Amazon Aurora](https://docs.aws.amazon.com/en_us/AmazonRDS/latest/AuroraUserGuide/USER_VPC.html)\. 
+ **Access the Amazon Aurora DB cluster outside the VPC** – To access an Amazon Aurora DB cluster from outside the VPC, use the public endpoint address of the Amazon Aurora DB cluster\. You can also connect to an Amazon Aurora DB cluster that's inside a VPC from an Amazon EC2 instance that's not in the VPC by using ClassicLink\. For more information, see [A DB instance in a VPC accessed by an EC2 instance not in a VPC](USER_VPC.Scenarios.md#USER_VPC.ClassicLink)\. 

For more information, see [Troubleshooting Aurora connection failures](#Aurora.Connecting.Troubleshooting)\. 

**Topics**
+ [Connecting to an Amazon Aurora MySQL DB cluster](#Aurora.Connecting.AuroraMySQL)
+ [Connecting to an Amazon Aurora PostgreSQL DB cluster](#Aurora.Connecting.AuroraPostgreSQL)
+ [Troubleshooting Aurora connection failures](#Aurora.Connecting.Troubleshooting)

## Connecting to an Amazon Aurora MySQL DB cluster<a name="Aurora.Connecting.AuroraMySQL"></a>

To authenticate to your Aurora MySQL DB cluster, you can use either MySQL user name and password authentication or AWS Identity and Access Management \(IAM\) database authentication\. For more information on using MySQL user name and password authentication, see [ Access control and account management](https://dev.mysql.com/doc/refman/5.7/en/access-control.html) in the MySQL documentation\. For more information on using IAM database authentication, see [IAM database authentication](UsingWithRDS.IAMDBAuth.md)\.

When you have a connection to your Amazon Aurora DB cluster with MySQL 8\.0 compatibility, you can run SQL commands that are compatible with MySQL version 8\.0\. The minimum compatible version is MySQL 8\.0\.23\. For more information about MySQL 8\.0 SQL syntax, see the [MySQL 8\.0 reference manual](http://dev.mysql.com/doc/refman/8.0/en/index.html)\. For information about limitations that apply to Aurora MySQL version 3, see [Comparison of Aurora MySQL version 3 and community MySQL 8\.0](AuroraMySQL.MySQL80.md#Aurora.AuroraMySQL.Compare-80-v3)\. 

When you have a connection to your Amazon Aurora DB cluster with MySQL 5\.7 compatibility, you can run SQL commands that are compatible with MySQL version 5\.7\. For more information about MySQL 5\.7 SQL syntax, see the [MySQL 5\.7 reference manual](http://dev.mysql.com/doc/refman/5.7/en/index.html)\. For information about limitations that apply to Aurora MySQL 5\.7, see [Aurora MySQL version 2 compatible with MySQL 5\.7](Aurora.AuroraMySQL.CompareMySQL57.md)\.

When you have a connection to your Amazon Aurora DB cluster with MySQL 5\.6 compatibility, you can run SQL commands that are compatible with MySQL version 5\.6\. For more information about MySQL 5\.6 SQL syntax, see the [MySQL 5\.6 reference manual](http://dev.mysql.com/doc/refman/5.6/en/index.html)\.

**Note**  
For a helpful and detailed guide on connecting to an Amazon Aurora MySQL DB cluster, you can see the [Aurora connection management](https://d1.awsstatic.com/whitepapers/RDS/amazon-aurora-connection-management-handbook.pdf) handbook\.

In the details view for your DB cluster, you can find the cluster endpoint, which you can use in your MySQL connection string\. The endpoint is made up of the domain name and port for your DB cluster\. For example, if an endpoint value is `mycluster.cluster-123456789012.us-east-1.rds.amazonaws.com:3306`, then you specify the following values in a MySQL connection string:
+ For host or host name, specify `mycluster.cluster-123456789012.us-east-1.rds.amazonaws.com`
+ For port, specify `3306` or the port value you used when you created the DB cluster

The cluster endpoint connects you to the primary instance for the DB cluster\. You can perform both read and write operations using the cluster endpoint\. Your DB cluster can also have up to 15 Aurora Replicas that support read\-only access to the data in your DB cluster\. The primary instance and each Aurora Replica has a unique endpoint that is independent of the cluster endpoint and allows you to connect to a specific DB instance in the cluster directly\. The cluster endpoint always points to the primary instance\. If the primary instance fails and is replaced, then the cluster endpoint points to the new primary instance\.

To view the cluster endpoint \(writer endpoint\), choose **Databases** on the Amazon RDS console and choose the name of the DB cluster to show the DB cluster details\.

![\[Amazon Aurora details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/AuroraMySQLConnect.png)

### Connection utilities for Aurora MySQL<a name="Aurora.Connecting.AuroraMySQL.Utilities"></a>

Some connection utilities you can use are the following:
+ **Command line** – You can connect to an Amazon Aurora DB cluster by using tools like the MySQL command line utility\. For more information on using the MySQL utility, see [mysql \- the MySQL command line tool](http://dev.mysql.com/doc/refman/5.6/en/mysql.html) in the MySQL documentation\.
+ **GUI** – You can use the MySQL Workbench utility to connect by using a UI interface\. For more information, see the [Download MySQL workbench](http://dev.mysql.com/downloads/workbench/) page\.
+ **Applications** – You can use the AWS JDBC Driver for MySQL to connect your client applications to an Aurora MySQL DB cluster\. For more information about the AWS JDBC Driver for MySQL and complete instructions for using it, see the [AWS JDBC Driver for MySQL GitHub repository](https://awslabs.github.io/aws-mysql-jdbc/)\.
**Note**  
Version 3\.0\.3 of the MariaDB Connector/J utility drops support for Aurora DB clusters, so we highly recommend moving to the AWS JDBC Driver for MySQL\. The AWS JDBC Driver for MySQL offers improved failover speed for Aurora MySQL DB clusters by caching DNS connections for quick use\. When using the MariaDB Connector/J utility, use the `jdbc:mariadb:aurora//` in your connection string\.   
If you are using an Aurora Serverless v1 DB cluster, the failover benefits don't apply, but you can disable the feature by setting the `failureDetectionEnabled` parameter to `false`\. To review a complete list of configuration options, see the [AWS JDBC Driver for MySQL GitHub repository](https://awslabs.github.io/aws-mysql-jdbc/)\.

You can use SSL encryption on connections to an Aurora MySQL DB instance\. For information, see [Using SSL/TLS with Aurora MySQL DB clusters](AuroraMySQL.Security.md#AuroraMySQL.Security.SSL)\.

### Connecting with SSL for Aurora MySQL<a name="Aurora.Connecting.SSL"></a>

To connect using SSL, use the MySQL utility as described in the following procedure\. If you are using IAM database authentication, you must use an SSL connection\. For information, see [IAM database authentication](UsingWithRDS.IAMDBAuth.md)\. 

**Note**  
To connect to the cluster endpoint using SSL, your client connection utility must support Subject Alternative Names \(SAN\)\. If your client connection utility doesn't support SAN, you can connect directly to the instances in your Aurora DB cluster\. For more information on Aurora endpoints, see [Amazon Aurora connection management](Aurora.Overview.Endpoints.md)\.

**To connect to a DB cluster with SSL using the MySQL utility**

1. Download the public key for the Amazon RDS signing certificate\.

   For information about downloading certificates, see [Using SSL/TLS to encrypt a connection to a DB cluster](UsingWithRDS.SSL.md)\.

1. Type the following command at a command prompt to connect to the primary instance of a DB cluster with SSL using the MySQL utility\. For the `-h` parameter, substitute the endpoint DNS name for your primary instance\. For the `-u` parameter, substitute the user ID of a database user account\. For the `--ssl-ca` parameter, substitute the SSL certificate file name as appropriate\. Type the master user password when prompted\.

   `mysql -h mycluster-primary.123456789012.us-east-1.rds.amazonaws.com -u admin_user -p --ssl-ca=[full path]global-bundle.pem --ssl-verify-server-cert`

You should see output similar to the following\.

```
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 350
Server version: 5.6.10-log MySQL Community Server (GPL)

Type 'help;' or '\h' for help. Type '\c' to clear the buffer.

mysql>
```

For general instructions on constructing RDS for MySQL connection strings and finding the public key for SSL connections, see [ Connecting to a DB instance running the MySQL database engine](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ConnectToInstance.html)\.

### Connecting with the Amazon Web Services JDBC Driver for MySQL<a name="Aurora.Connecting.JDBCDriverMySQL"></a>

The AWS JDBC Driver for MySQL is a client driver designed for the high availability of Aurora MySQL\. The AWS JDBC Driver for MySQL is drop\-in compatible with the MySQL Connector/J driver\. To install or upgrade your connector, replace the MySQL connector \.jar file \(located in the application CLASSPATH\) with the AWS JDBC Driver for MySQL \.jar file, and update the connection URL prefix from `jdbc:mysql://` to `jdbc:mysql:aws://`\.

The AWS JDBC Driver for MySQL takes full advantage of the failover capabilities of Aurora MySQL\. The AWS JDBC Driver for MySQL fully maintains a cache of the DB cluster topology and each DB instance's role, either primary DB instance or Aurora Replica\. It uses this topology to bypass the delays caused by DNS resolution so that a connection to the new primary DB instance is established as fast as possible\.

For more information about the AWS JDBC Driver for MySQL and complete instructions for using it, see [the AWS JDBC Driver for MySQL GitHub repository](https://awslabs.github.io/aws-mysql-jdbc/)\.

## Connecting to an Amazon Aurora PostgreSQL DB cluster<a name="Aurora.Connecting.AuroraPostgreSQL"></a>

You can connect to a DB instance in your Amazon Aurora PostgreSQL DB cluster using the same tools that you use to connect to a PostgreSQL database\. As part of this, you use the same public key for Secure Sockets Layer \(SSL\) connections\. You can use the endpoint and port information from the primary instance or Aurora Replicas in your Aurora PostgreSQL DB cluster in the connection string of any script, utility, or application that connects to a PostgreSQL DB instance\. In the connection string, specify the DNS address from the primary instance or Aurora Replica endpoint as the host parameter\. Specify the port number from the endpoint as the port parameter\.

When you have a connection to a DB instance in your Amazon Aurora PostgreSQL DB cluster, you can run any SQL command that is compatible with PostgreSQL\. 

In the details view for your Aurora PostgreSQL DB cluster you can find the cluster endpoint name, status, type, and port number\. You use the endpoint and port number in your PostgreSQL connection string\. For example, if an endpoint value is `mycluster.cluster-123456789012.us-east-1.rds.amazonaws.com`, then you specify the following values in a PostgreSQL connection string:
+ For host or host name, specify `mycluster.cluster-123456789012.us-east-1.rds.amazonaws.com`
+ For port, specify `5432` or the port value you used when you created the DB cluster

The cluster endpoint connects you to the primary instance for the DB cluster\. You can perform both read and write operations using the cluster endpoint\. Your DB cluster can also have up to 15 Aurora Replicas that support read\-only access to the data in your DB cluster\. Each DB instance in the Aurora cluster \(that is, the primary instance and each Aurora Replica\) has a unique endpoint that is independent of the cluster endpoint\. This unique endpoint allows you to connect to a specific DB instance in the cluster directly\. The cluster endpoint always points to the primary instance\. If the primary instance fails and is replaced, the cluster endpoint points to the new primary instance\.

To view the cluster endpoint \(writer endpoint\), choose **Databases** on the Amazon RDS console and choose the name of the DB cluster to show the DB cluster details\.

![\[Amazon Aurora details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/AuroraPostgreSQLConnect.png)

### Connection utilities for Aurora PostgreSQL<a name="Aurora.Connecting.AuroraPostgreSQL.Utilities"></a>

Some connection utilities you can use are the following:
+ **Command line** – You can connect to an Amazon Aurora PostgreSQL DB instance by using tools like psql, the PostgreSQL interactive terminal\. For more information on using the PostgreSQL interactive terminal, see [psql](https://www.postgresql.org/docs/current/app-psql.html) in the PostgreSQL documentation\.
+ **GUI** – You can use the pgAdmin utility to connect to a PostgreSQL DB instance by using a UI interface\. For more information, see the [Download](https://www.pgadmin.org/download/) page from the pgAdmin website\.
+ **Applications** – You can use the PostgreSQL JDBC driver to connect your applications to your PostgreSQL DB instance\. For more information, see the [Download](https://jdbc.postgresql.org/download.html) page from the PostgreSQL JDBC driver website\.

### Connecting with the Amazon Web Services JDBC Driver for PostgreSQL \(preview\)<a name="Aurora.Connecting.AuroraPostgreSQL.JDBCDriverPostgreSQL"></a>


|  | 
| --- |
| This is preview documentation for Amazon Web Services JDBC Driver for PostgreSQL\. It is subject to change\. | 

The AWS JDBC Driver for PostgreSQL \(preview\) is a client driver designed for the high availability of Aurora PostgreSQL\. The AWS JDBC Driver for PostgreSQL is drop\-in compatible with the PostgreSQL JDBC Driver\.

The AWS JDBC Driver for PostgreSQL takes full advantage of the failover capabilities of Aurora PostgreSQL\. The AWS JDBC Driver for PostgreSQL fully maintains a cache of the DB cluster topology and each DB instance's role, either primary DB instance or Aurora Replica\. It uses this topology to bypass the delays caused by DNS resolution so that a connection to the new primary DB instance is established as fast as possible\.

For more information about the AWS JDBC Driver for PostgreSQL and complete instructions for using it, see the [AWS JDBC Driver for PostgreSQL GitHub repository](https://awslabs.github.io/aws-postgresql-jdbc/)\.

## Troubleshooting Aurora connection failures<a name="Aurora.Connecting.Troubleshooting"></a>

Common causes of connection failures to a new Aurora DB cluster include the following:
+ **Security group in the VPC doesn't allow access** – Your VPC needs to allow connections from your device or from an Amazon EC2 instance by proper configuration of the Security group in the VPC\. To resolve, modify your VPC's Security group Inbound rules to allow connections\. For an example, see [Create a VPC and subnets](Aurora.CreateVPC.md#CHAP_Aurora.CreateVPC)\.
+ **Port blocked by firewall rules** – Check the value of the port configured for your Aurora DB cluster\. If a firewall rule blocks that port, you can re\-create the instance using a different port\. 
+ **Incomplete or incorrect IAM configuration** – If you created your Aurora DB instance to use IAM–based authentication, make sure that it's properly configured\. For more information, see [IAM database authentication](UsingWithRDS.IAMDBAuth.md)\.

For more information about troubleshooting Aurora DB connection issues, see [Can't connect to Amazon RDS DB instance](CHAP_Troubleshooting.md#CHAP_Troubleshooting.Connecting)\. 