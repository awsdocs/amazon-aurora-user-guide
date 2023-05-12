# Aurora DB instance classes<a name="Concepts.DBInstanceClass"></a>

The DB instance class determines the computation and memory capacity of an Amazon Aurora DB instance\. The DB instance class that you need depends on your processing power and memory requirements\.

A DB instance class consists of both the DB instance class type and the size\. For example, db\.r6g is a memory\-optimized DB instance class type powered by AWS Graviton2 processors\. Within the db\.r6g instance class type, db\.r6g\.2xlarge is a DB instance class\. The size of this class is 2xlarge\.

For more information about instance class pricing, see [Amazon RDS pricing](https://aws.amazon.com/rds/pricing/)\.

**Topics**
+ [DB instance class types](#Concepts.DBInstanceClass.Types)
+ [Supported DB engines for DB instance classes](#Concepts.DBInstanceClass.SupportAurora)
+ [Determining DB instance class support in AWS Regions](#Concepts.DBInstanceClass.RegionSupportAurora)
+ [Hardware specifications for DB instance classes for Aurora](#Concepts.DBInstanceClass.Summary)

## DB instance class types<a name="Concepts.DBInstanceClass.Types"></a>

Amazon Aurora supports DB instance classes for the following use cases:
+ [Aurora Serverless v2](#Concepts.DBInstanceClass.Types.serverless-v2)
+ [Memory\-optimized](#Concepts.DBInstanceClass.Types.memory)
+ [Burstable\-performance](#Concepts.DBInstanceClass.Types.burstable)

 For more information about Amazon EC2 instance types, see [Instance types](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html) in the Amazon EC2 documentation\. 

### Aurora Serverless v2 instance class type<a name="Concepts.DBInstanceClass.Types.serverless-v2"></a>

The following Aurora Serverless v2 type is available:
+  **db\.serverless** – A special DB instance class type used by Aurora Serverless v2\. Aurora adjusts the compute, memory, and network resources dynamically as the workload changes\. For usage details, see [Using Aurora Serverless v2](aurora-serverless-v2.md)\. 

### Memory\-optimized instance class types<a name="Concepts.DBInstanceClass.Types.memory"></a>

The memory\-optimized X family supports the following instance class types:
+ **db\.x2g** – Instance classes optimized for memory\-intensive applications and powered by AWS Graviton2 processors\. These instance classes offer low cost per GiB of memory\.

  You can modify a DB instance to use one of the DB instance classes powered by AWS Graviton2 processors\. To do so, complete the same steps as with any other DB instance modification\.

The memory\-optimized R family supports the following instance class types:
+ **db\.r7g** – Instance classes powered by AWS Graviton3 processors\. These instance classes are ideal for running memory\-intensive workloads\.

  You can modify a DB instance to use one of the DB instance classes powered by AWS Graviton3 processors\. To do so, complete the same steps as with any other DB instance modification\.
+ **db\.r6g** – Instance classes powered by AWS Graviton2 processors\. These instance classes are ideal for running memory\-intensive workloads\.

  You can modify a DB instance to use one of the DB instance classes powered by AWS Graviton2 processors\. To do so, complete the same steps as with any other DB instance modification\.
+ **db\.r6i** – Instance classes that are ideal for running memory\-intensive workloads\.
+ **db\.r4** – These instance classes are supported only for Aurora PostgreSQL 11 and 12 versions\. For all Aurora PostgreSQL DB clusters that use db\.r4 DB instance classes, We recommend that you upgrade to a db\.r5 or newer instance class as soon as possible\.
+ **db\.r3** – Instance classes that provide memory optimization\.

  Amazon Aurora has started the end\-of\-life process for db\.r3 DB instance classes using the following schedule, which includes upgrade recommendations\. For all Aurora MySQL DB clusters that use db\.r3 DB instance classes, we recommend that you upgrade to a db\.r5 or higher DB instance class as soon as possible\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Concepts.DBInstanceClass.html)

### Burstable\-performance instance class types<a name="Concepts.DBInstanceClass.Types.burstable"></a>

The following burstable\-performance DB instance class types are available:
+ **db\.t4g** – General\-purpose instance classes powered by Arm\-based AWS Graviton2 processors\. These instance classes deliver better price performance than previous burstable\-performance DB instance classes for a broad set of burstable general\-purpose workloads\. Amazon RDS T4g instances are configured for Unlimited mode\. This means that they can burst beyond the baseline over a 24\-hour window for an additional charge\.

  You can modify a DB instance to use one of the DB instance classes powered by AWS Graviton2 processors\. To do so, complete the same steps as with any other DB instance modification\.
+ **db\.t3** – Instance classes that provide a baseline performance level, with the ability to burst to full CPU usage\. T3 instances are configured for Unlimited mode\. These instance classes provide more computing capacity than the previous db\.t2 instance classes\. They are powered by the AWS Nitro System, a combination of dedicated hardware and lightweight hypervisor\. We recommend using these instance classes only for development and test servers, or other non\-production servers\. 
+ **db\.t2** – Instance classes that provide a baseline performance level, with the ability to burst to full CPU usage\. We recommend using these instance classes only for development and test servers, or other non\-production servers\.

**Note**  
We recommend using the T DB instance classes only for development, test, or other nonproduction servers\. For more detailed recommendations for the T instance classes, see [Using T instance classes for development and testing](AuroraMySQL.BestPractices.md#AuroraMySQL.BestPractices.T2Medium)\.

For DB instance class hardware specifications, see [Hardware specifications for DB instance classes for Aurora](#Concepts.DBInstanceClass.Summary)\.

## Supported DB engines for DB instance classes<a name="Concepts.DBInstanceClass.SupportAurora"></a><a name="instance_classes"></a>

In the following table, you can find details about supported Amazon Aurora DB instance classes for the Aurora DB engines\. 


****  

| Instance class | Aurora MySQL | Aurora PostgreSQL | 
| --- | --- | --- | 
| db\.serverless – Aurora Serverless v2 instance class with automatic capacity scaling | 
| db\.serverless | See [Aurora Serverless v2](Concepts.Aurora_Fea_Regions_DB-eng.Feature.ServerlessV2.md) | See [Aurora Serverless v2](Concepts.Aurora_Fea_Regions_DB-eng.Feature.ServerlessV2.md) | 
| db\.x2g – memory\-optimized instance classes powered by AWS Graviton2 processors | 
| db\.x2g\.16xlarge | 2\.09\.2 and higher, 2\.10\.0 and higher, 3\.01\.0 and higher | 14\.3 and higher, 13\.3 and higher, 12\.8 and higher, 11\.9, 11\.12 and higher | 
| db\.x2g\.12xlarge | 2\.09\.2 and higher, 2\.10\.0 and higher, 3\.01\.0 and higher | 14\.3 and higher, 13\.3 and higher, 12\.8 and higher, 11\.9, 11\.12 and higher | 
| db\.x2g\.8xlarge | 2\.09\.2 and higher, 2\.10\.0 and higher, 3\.01\.0 and higher | 14\.3 and higher, 13\.3 and higher, 12\.8 and higher, 11\.9, 11\.12 and higher | 
| db\.x2g\.4xlarge | 2\.09\.2 and higher, 2\.10\.0 and higher, 3\.01\.0 and higher | 14\.3 and higher, 13\.3 and higher, 12\.8 and higher, 11\.9, 11\.12 and higher | 
| db\.x2g\.2xlarge | 2\.09\.2 and higher, 2\.10\.0 and higher, 3\.01\.0 and higher | 14\.3 and higher, 13\.3 and higher, 12\.8 and higher, 11\.9, 11\.12 and higher | 
| db\.x2g\.xlarge | 2\.09\.2 and higher, 2\.10\.0 and higher, 3\.01\.0 and higher | 14\.3 and higher, 13\.3 and higher, 12\.8 and higher, 11\.9, 11\.12 and higher | 
| db\.x2g\.large | 2\.09\.2 and higher, 2\.10\.0 and higher, 3\.01\.0 and higher | 14\.3 and higher, 13\.3 and higher, 12\.8 and higher, 11\.9, 11\.12 and higher | 
| db\.r7g – memory\-optimized instance classes powered by AWS Graviton3 processors | 
| db\.r7g\.16xlarge | 3\.03\.1 and higher | 15\.2 and higher, 14\.7 and higher, 13\.10 and higher | 
| db\.r7g\.12xlarge | 3\.03\.1 and higher | 15\.2 and higher, 14\.7 and higher, 13\.10 and higher | 
| db\.r7g\.8xlarge | 3\.03\.1 and higher | 15\.2 and higher, 14\.7 and higher, 13\.10 and higher | 
| db\.r7g\.4xlarge | 3\.03\.1 and higher | 15\.2 and higher, 14\.7 and higher, 13\.10 and higher | 
| db\.r7g\.2xlarge | 3\.03\.1 and higher | 15\.2 and higher, 14\.7 and higher, 13\.10 and higher | 
| db\.r7g\.xlarge | 3\.03\.1 and higher | 15\.2 and higher, 14\.7 and higher, 13\.10 and higher | 
| db\.r7g\.large | 3\.03\.1 and higher | 15\.2 and higher, 14\.7 and higher, 13\.10 and higher | 
| db\.r6g – memory\-optimized instance classes powered by AWS Graviton2 processors | 
| db\.r6g\.16xlarge | 2\.09\.2 and higher, 2\.10\.0 and higher, 3\.01\.0 and higher | 14\.3 and higher, 13\.3 and higher, 12\.8 and higher, 11\.9, 11\.12 and higher | 
| db\.r6g\.12xlarge | 2\.09\.2 and higher, 2\.10\.0 and higher, 3\.01\.0 and higher | 14\.3 and higher, 13\.3 and higher, 12\.8 and higher, 11\.9, 11\.12 and higher | 
| db\.r6g\.8xlarge | 2\.09\.2 and higher, 2\.10\.0 and higher, 3\.01\.0 and higher | 14\.3 and higher, 13\.3 and higher, 12\.8 and higher, 11\.9, 11\.12 and higher | 
| db\.r6g\.4xlarge | 2\.09\.2 and higher, 2\.10\.0 and higher, 3\.01\.0 and higher | 14\.3 and higher, 13\.3 and higher, 12\.8 and higher, 11\.9, 11\.12 and higher | 
| db\.r6g\.2xlarge | 2\.09\.2 and higher, 2\.10\.0 and higher, 3\.01\.0 and higher | 14\.3 and higher, 13\.3 and higher, 12\.8 and higher, 11\.9, 11\.12 and higher | 
| db\.r6g\.xlarge | 2\.09\.2 and higher, 2\.10\.0 and higher, 3\.01\.0 and higher | 14\.3 and higher, 13\.3 and higher, 12\.8 and higher, 11\.9, 11\.12 and higher | 
| db\.r6g\.large | 2\.09\.2 and higher, 2\.10\.0 and higher, 3\.01\.0 and higher | 14\.3 and higher, 13\.3 and higher, 12\.8 and higher, 11\.9, 11\.12 and higher | 
| db\.r6i – memory\-optimized instance classes | 
| db\.r6i\.32xlarge | 2\.11\.0 and higher, 3\.02\.1 and higher | 14\.3 and higher, 13\.5 and higher, 12\.9 and higher | 
| db\.r6i\.24xlarge | 2\.11\.0 and higher, 3\.02\.1 and higher | 14\.3 and higher, 13\.5 and higher, 12\.9 and higher | 
| db\.r6i\.16xlarge | 2\.11\.0 and higher, 3\.02\.1 and higher | 14\.3 and higher, 13\.5 and higher, 12\.9 and higher | 
| db\.r6i\.12xlarge | 2\.11\.0 and higher, 3\.02\.1 and higher | 14\.3 and higher, 13\.5 and higher, 12\.9 and higher | 
| db\.r6i\.8xlarge | 2\.11\.0 and higher, 3\.02\.1 and higher | 14\.3 and higher, 13\.5 and higher, 12\.9 and higher | 
| db\.r6i\.4xlarge | 2\.11\.0 and higher, 3\.02\.1 and higher | 14\.3 and higher, 13\.5 and higher, 12\.9 and higher | 
| db\.r6i\.2xlarge | 2\.11\.0 and higher, 3\.02\.1 and higher | 14\.3 and higher, 13\.5 and higher, 12\.9 and higher | 
| db\.r6i\.xlarge | 2\.11\.0 and higher, 3\.02\.1 and higher | 14\.3 and higher, 13\.5 and higher, 12\.9 and higher | 
| db\.r6i\.large | 2\.11\.0 and higher, 3\.02\.1 and higher | 14\.3 and higher, 13\.5 and higher, 12\.9 and higher | 
| db\.r5 – memory\-optimized instance classes | 
| db\.r5\.24xlarge | 1\.22 and higher, 2\.06 and higher, 3\.01\.0 and higher | [All currently available versions](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html) | 
| db\.r5\.16xlarge | 1\.22 and higher, 2\.06 and higher, 3\.01\.0 and higher | [All currently available versions](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html) | 
| db\.r5\.12xlarge | 1\.14\.4 and higher, 2\.06 and higher, 3\.01\.0 and higher | [All currently available versions](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html) | 
| db\.r5\.8xlarge | 1\.22 and higher, 2\.06 and higher, 3\.01\.0 and higher | [All currently available versions](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html) | 
| db\.r5\.4xlarge | 1\.14\.4 and higher, 2\.06 and higher, 3\.01\.0 and higher | [All currently available versions](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html) | 
| db\.r5\.2xlarge | 1\.14\.4 and higher, 2\.06 and higher, 3\.01\.0 and higher | [All currently available versions](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html) | 
| db\.r5\.xlarge | 1\.14\.4 and higher, 2\.06 and higher, 3\.01\.0 and higher | [All currently available versions](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html) | 
| db\.r5\.large | 1\.14\.4 and higher, 2\.06 and higher, 3\.01\.0 and higher | [All currently available versions](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html) | 
| db\.r4 – memory\-optimized instance classes | 
| db\.r4\.16xlarge | 1\.14\.4 and higher; not supported in 3\.01\.0 and higher | No | 
| db\.r4\.8xlarge | 1\.14\.4 and higher; not supported in 3\.01\.0 and higher | No | 
| db\.r4\.4xlarge | 1\.14\.4 and higher; not supported in 3\.01\.0 and higher | No | 
| db\.r4\.2xlarge | 1\.14\.4 and higher; not supported in 3\.01\.0 and higher | No | 
| db\.r4\.xlarge | 1\.14\.4 and higher; not supported in 3\.01\.0 and higher | No | 
| db\.r4\.large | 1\.14\.4 and higher; not supported in 3\.01\.0 and higher | No | 
| db\.r3 – memory\-optimized instance classes | 
| db\.r3\.8xlarge | All version 1\.x; not supported in version 2\.11\.0 and higher; not supported in version 3\.01\.0 and higher | No | 
| db\.r3\.4xlarge | All version 1\.x; not supported in version 2\.11\.0 and higher; not supported in version 3\.01\.0 and higher | No | 
| db\.r3\.2xlarge | All version 1\.x; not supported in version 2\.11\.0 and higher; not supported in version 3\.01\.0 and higher | No | 
| db\.r3\.xlarge | All version 1\.x; not supported in version 2\.11\.0 and higher; not supported in version 3\.01\.0 and higher | No | 
| db\.r3\.large | All version 1\.x; not supported in version 2\.11\.0 and higher; not supported in version 3\.01\.0 and higher | No | 
| db\.t4g – burstable\-performance instance classes powered by AWS Graviton2 processors | 
| db\.t4g\.2xlarge | No | No | 
| db\.t4g\.xlarge | No | No | 
| db\.t4g\.large | 2\.09\.2 and higher, 2\.10\.0 and higher, 3\.01\.0 and higher | 14\.3 and higher, 13\.3 and higher, 12\.7 and higher, 11\.12 and higher | 
| db\.t4g\.medium | 2\.09\.2 and higher, 2\.10\.0 and higher, 3\.01\.0 and higher | 14\.3 and higher, 13\.3 and higher, 12\.7 and higher, 11\.12 and higher | 
| db\.t4g\.small | No | No | 
| db\.t3 – burstable\-performance instance classes | 
| db\.t3\.2xlarge | No | No | 
| db\.t3\.xlarge | No | No | 
| db\.t3\.large | 2\.10 and higher, 3\.01\.0 and higher | 14\.3 and higher, 13\.3 and higher, 12\.7 and higher, 11\.12 and higher | 
| db\.t3\.medium | 1\.14\.4 and higher, 3\.01\.0 and higher | 14\.3 and higher, 13\.3 and higher, 12\.7 and higher, 11\.12 and higher | 
| db\.t3\.small | 1\.14\.4 and higher; not supported in 3\.01\.0 and higher | No | 
| db\.t3\.micro | No | No | 
| db\.t2 – burstable\-performance instance classes | 
| db\.t2\.medium | All 1\.x and 2\.x versions; not supported in 3\.01\.0 and higher | No | 
| db\.t2\.small | All 1\.x and 2\.x versions; not supported in 3\.01\.0 and higher | No | 

## Determining DB instance class support in AWS Regions<a name="Concepts.DBInstanceClass.RegionSupportAurora"></a>

To determine the DB instance classes supported by each DB engine in a specific AWS Region, you can take one of several approaches\. You can use the AWS Management Console, the [Amazon RDS Pricing](http://aws.amazon.com/rds/pricing/) page, or the [describe\-orderable\-db\-instance\-options](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-orderable-db-instance-options.html) AWS CLI command\.

**Note**  
When you perform operations with the AWS CLI, it automatically shows the supported DB instance classes for a specific DB engine, DB engine version, and AWS Region\. Examples of the operations that you can perform include creating and modifying a DB instance\.

**Contents**
+ [Using the Amazon RDS pricing page to determine DB instance class support in AWS Regions](#Concepts.DBInstanceClass.RegionSupportAurora.PricingPage)
+ [Using the AWS CLI to determine DB instance class support in AWS Regions](#Concepts.DBInstanceClass.RegionSupportAurora.CLI)
  + [Listing the DB instance classes that are supported by a specific DB engine version in an AWS Region](#Concepts.DBInstanceClass.RegionSupportAurora.CLI.Example1)
  + [Listing the DB engine versions that support a specific DB instance class in an AWS Region](#Concepts.DBInstanceClass.RegionSupportAurora.CLI.Example2)

### Using the Amazon RDS pricing page to determine DB instance class support in AWS Regions<a name="Concepts.DBInstanceClass.RegionSupportAurora.PricingPage"></a>

You can use the [Amazon RDS Pricing](http://aws.amazon.com/rds/pricing/) page to determine the DB instance classes supported by each DB engine in a specific AWS Region\. 

**To use the pricing page to determine the DB instance classes supported by each engine in a Region**

1. Go to [Amazon RDS Pricing](http://aws.amazon.com/rds/pricing/)\.

1. Choose **Amazon Aurora**\.

1. In **DB Instances**, open **MySQL\-Compatible Edition** or **PostgreSQL\-Compatible Edition**\.

1. To see the DB instance classes available in an AWS Region, choose the AWS Region in **Region** in the appropriate subsection\.

### Using the AWS CLI to determine DB instance class support in AWS Regions<a name="Concepts.DBInstanceClass.RegionSupportAurora.CLI"></a>

You can use the AWS CLI to determine which DB instance classes are supported for specific DB engines and DB engine versions in an AWS Region\.

To use the AWS CLI examples following, enter valid values for the DB engine, DB engine version, DB instance class, and AWS Region\. The following table shows the valid DB engine values\.


****  

| Engine name | Engine value in CLI commands | More information about versions | 
| --- | --- | --- | 
|  MySQL 5\.7\-compatible and 8\.0\-compatible Aurora  |  `aurora-mysql`  |  [ Database engine updates for Amazon Aurora MySQL version 2](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraMySQLReleaseNotes/AuroraMySQL.Updates.20Updates.html) and [ Database engine updates for Amazon Aurora MySQL version 3](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraMySQLReleaseNotes/AuroraMySQL.Updates.30Updates.html) in the *Release Notes for Aurora MySQL*  | 
|  Aurora PostgreSQL  |  `aurora-postgresql`  |  [https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/Welcome.html](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/Welcome.html)  | 

For information about AWS Region names, see [AWS RegionsAvailability Zones](Concepts.RegionsAndAvailabilityZones.md#Concepts.RegionsAndAvailabilityZones.Regions)\.

The following examples demonstrate how to determine DB instance class support in an AWS Region using the [describe\-orderable\-db\-instance\-options](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-orderable-db-instance-options.html) AWS CLI command\.

**Topics**
+ [Listing the DB instance classes that are supported by a specific DB engine version in an AWS Region](#Concepts.DBInstanceClass.RegionSupportAurora.CLI.Example1)
+ [Listing the DB engine versions that support a specific DB instance class in an AWS Region](#Concepts.DBInstanceClass.RegionSupportAurora.CLI.Example2)

#### Listing the DB instance classes that are supported by a specific DB engine version in an AWS Region<a name="Concepts.DBInstanceClass.RegionSupportAurora.CLI.Example1"></a>

To list the DB instance classes that are supported by a specific DB engine version in an AWS Region, run the following command\.

For Linux, macOS, or Unix:

```
aws rds describe-orderable-db-instance-options --engine engine --engine-version version \
    --query "OrderableDBInstanceOptions[].{DBInstanceClass:DBInstanceClass,SupportedEngineModes:SupportedEngineModes[0]}" \
    --output table \
    --region region
```

For Windows:

```
aws rds describe-orderable-db-instance-options --engine engine --engine-version version ^
    --query "OrderableDBInstanceOptions[].{DBInstanceClass:DBInstanceClass,SupportedEngineModes:SupportedEngineModes[0]}" ^
    --output table ^
    --region region
```

The output also shows the engine modes that are supported for each DB instance class\.

For example, the following command lists the supported DB instance classes for version 13\.6 of the Aurora PostgreSQL DB engine in US East \(N\. Virginia\)\.

For Linux, macOS, or Unix:

```
aws rds describe-orderable-db-instance-options --engine aurora-postgresql --engine-version 13.6 \
    --query "OrderableDBInstanceOptions[].{DBInstanceClass:DBInstanceClass,SupportedEngineModes:SupportedEngineModes[0]}" \
    --output table \
    --region us-east-1
```

For Windows:

```
aws rds describe-orderable-db-instance-options --engine aurora-postgresql --engine-version 13.6 ^
    --query "OrderableDBInstanceOptions[].{DBInstanceClass:DBInstanceClass,SupportedEngineModes:SupportedEngineModes[0]}"  ^
    --output table ^
    --region us-east-1
```

#### Listing the DB engine versions that support a specific DB instance class in an AWS Region<a name="Concepts.DBInstanceClass.RegionSupportAurora.CLI.Example2"></a>

To list the DB engine versions that support a specific DB instance class in an AWS Region, run the following command\.

For Linux, macOS, or Unix:

```
aws rds describe-orderable-db-instance-options --engine engine --db-instance-class DB_instance_class \
    --query "OrderableDBInstanceOptions[].{EngineVersion:EngineVersion,SupportedEngineModes:SupportedEngineModes[0]}" \
    --output table \
    --region region
```

For Windows:

```
aws rds describe-orderable-db-instance-options --engine engine --db-instance-class DB_instance_class ^
    --query "OrderableDBInstanceOptions[].{EngineVersion:EngineVersion,SupportedEngineModes:SupportedEngineModes[0]}" ^
    --output table ^
    --region region
```

The output also shows the engine modes that are supported for each DB engine version\.

For example, the following command lists the DB engine versions of the Aurora PostgreSQL DB engine that support the db\.r5\.large DB instance class in US East \(N\. Virginia\)\.

For Linux, macOS, or Unix:

```
aws rds describe-orderable-db-instance-options --engine aurora-postgresql --db-instance-class db.r5.large \
    --query "OrderableDBInstanceOptions[].{EngineVersion:EngineVersion,SupportedEngineModes:SupportedEngineModes[0]}" \
    --output table \
    --region us-east-1
```

For Windows:

```
aws rds describe-orderable-db-instance-options --engine aurora-postgresql --db-instance-class db.r5.large ^
    --query "OrderableDBInstanceOptions[].{EngineVersion:EngineVersion,SupportedEngineModes:SupportedEngineModes[0]}" ^
    --output table ^
    --region us-east-1
```

## Hardware specifications for DB instance classes for Aurora<a name="Concepts.DBInstanceClass.Summary"></a>

The following terminology is used to describe hardware specifications for DB instance classes:

**vCPU**  
The number of virtual central processing units \(CPUs\)\. A *virtual CPU *is a unit of capacity that you can use to compare DB instance classes\. Instead of purchasing or leasing a particular processor to use for several months or years, you are renting capacity by the hour\. Our goal is to make a consistent and specific amount of CPU capacity available, within the limits of the actual underlying hardware\.

**ECU**  
The relative measure of the integer processing power of an Amazon EC2 instance\. To make it easy for developers to compare CPU capacity between different instance classes, we have defined an Amazon EC2 Compute Unit\. The amount of CPU that is allocated to a particular instance is expressed in terms of these EC2 Compute Units\. One ECU currently provides CPU capacity equivalent to a 1\.0–1\.2 GHz 2007 Opteron or 2007 Xeon processor\.

**Memory \(GiB\)**  
The RAM, in gibibytes, allocated to the DB instance\. There is often a consistent ratio between memory and vCPU\. As an example, take the db\.r4 instance class, which has a memory to vCPU ratio similar to the db\.r5 instance class\. However, for most use cases the db\.r5 instance class provides better, more consistent performance than the db\.r4 instance class\. 

**Max\. EBS bandwidth \(Mbps\)**  
The maximum EBS bandwidth in megabits per second\. Divide by 8 to get the expected throughput in megabytes per second\.   
This figure refers to I/O bandwidth for local storage within the DB instance\. It doesn't apply to communication with the Aurora cluster volume\.

**Network bandwidth**  
The network speed relative to other DB instance classes\.

In the following table, you can find hardware details about the Amazon RDS DB instance classes for Aurora\. 

For information about Aurora DB engine support for each DB instance class, see [Supported DB engines for DB instance classes](#Concepts.DBInstanceClass.SupportAurora)\. 


****  

| Instance class | vCPU | ECU | Memory \(GiB\) | Max\. bandwidth \(Mbps\) of local storage | Network performance \(Gbps\) | 
| --- | --- | --- | --- | --- | --- | 
| db\.x2g – memory\-optimized instance classes | 
| db\.x2g\.16xlarge | 64 | — | 1024 | 19,000 | 25 | 
| db\.x2g\.12xlarge | 48 | — | 768 | 14,250 | 20 | 
| db\.x2g\.8xlarge | 32 | — | 512 | 9,500 | 12 | 
| db\.x2g\.4xlarge | 16 | — | 256 | 4,750 | Up to 10 | 
| db\.x2g\.2xlarge | 8 | — | 128 | Up to 4,750 | Up to 10 | 
| db\.x2g\.xlarge | 4 | — | 64 | Up to 4,750 | Up to 10 | 
| db\.x2g\.large | 2 | — | 32 | Up to 4,750 | Up to 10 | 
| db\.r7g – memory\-optimized instance classes powered by AWS Graviton3 processors | 
| db\.r7g\.16xlarge | 64 | — | 512 | 20,000 | 30 | 
| db\.r7g\.12xlarge | 48 | — | 384 | 15,000 | 22\.5 | 
| db\.r7g\.8xlarge | 32 | — | 256 | 10,000 | 15 | 
| db\.r7g\.4xlarge | 16 | — | 128 | Up to 10,000 | Up to 15 | 
| db\.r7g\.2xlarge | 8 | — | 64 | Up to 10,000 | Up to 15 | 
| db\.r7g\.xlarge | 4 | — | 32 | Up to 10,000 | Up to 12\.5 | 
| db\.r7g\.large | 2 | — | 16 | Up to 10,000 | Up to 12\.5 | 
| db\.r6g – memory\-optimized instance classes powered by AWS Graviton2 processors | 
| db\.r6g\.16xlarge | 64 | — | 512 | 19,000 | 25 | 
| db\.r6g\.12xlarge | 48 | — | 384 | 13,500 | 20 | 
| db\.r6g\.8xlarge | 32 | — | 256 | 9,000 | 12 | 
| db\.r6g\.4xlarge | 16 | — | 128 | 4,750 | Up to 10  | 
| db\.r6g\.2xlarge | 8 | — | 64 | Up to 4,750 | Up to 10  | 
| db\.r6g\.xlarge | 4 | — | 32 | Up to 4,750 | Up to 10  | 
| db\.r6g\.large | 2 | — | 16 | Up to 4,750 | Up to 10  | 
| db\.r6i – memory\-optimized instance classes | 
| db\.r6i\.32xlarge | 128 | — | 1,024 | 40,000 | 50 | 
| db\.r6i\.24xlarge | 96 | — | 768 | 30,000 | 37\.5 | 
| db\.r6i\.16xlarge | 64 | — | 512 | 20,000 | 25 | 
| db\.r6i\.12xlarge | 48 | — | 384 | 15,000 | 18\.75 | 
| db\.r6i\.8xlarge | 32 | — | 256 | 10,000 | 12\.5 | 
| db\.r6i\.4xlarge\* | 16 | — | 128 | Up to 10,000 | Up to 12\.5 | 
| db\.r6i\.2xlarge\* | 8 | — | 64 | Up to 10,000 | Up to 12\.5 | 
| db\.r6i\.xlarge\* | 4 | — | 32 | Up to 10,000 | Up to 12\.5 | 
| db\.r6i\.large\* | 2 | — | 16 | Up to 10,000 | Up to 12\.5 | 
| db\.r5 – memory\-optimized instance classes | 
| db\.r5\.24xlarge | 96 | 347 | 768 | 19,000 | 25 | 
| db\.r5\.16xlarge | 64 | 264 | 512 | 13,600 | 20 | 
| db\.r5\.12xlarge | 48 | 173 | 384 | 9,500 | 10 | 
| db\.r5\.8xlarge | 32 | 132 | 256 | 6,800 | 10 | 
| db\.r5\.4xlarge | 16 | 71 | 128 | 4,750 | Up to 10 | 
| db\.r5\.2xlarge | 8 | 38 | 64 | Up to 4,750 | Up to 10 | 
| db\.r5\.xlarge | 4 | 19 | 32 | Up to 4,750 | Up to 10 | 
| db\.r5\.large | 2 | 10 | 16 | Up to 4,750 | Up to 10 | 
| db\.r4 – memory\-optimized instance classes | 
| db\.r4\.16xlarge | 64 | 195 | 488 | 14,000 | 25 | 
| db\.r4\.8xlarge | 32 | 99 | 244 | 7,000 | 10 | 
| db\.r4\.4xlarge | 16 | 53 | 122 | 3,500 | Up to 10 | 
| db\.r4\.2xlarge | 8 | 27 | 61 | 1,700 | Up to 10 | 
| db\.r4\.xlarge | 4 | 13\.5 | 30\.5 | 850 | Up to 10 | 
| db\.r4\.large | 2 | 7 | 15\.25 | 425 | Up to 10 | 
| db\.r3 – memory\-optimized instance classes | 
| db\.r3\.8xlarge | 32 | 104 | 244 | — | 10 | 
| db\.r3\.4xlarge | 16 | 52 | 122 | 2,000 | High | 
| db\.r3\.2xlarge | 8 | 26 | 61 | 1,000 | High | 
| db\.r3\.xlarge | 4 | 13 | 30\.5 | 500 | Moderate | 
| db\.r3\.large | 2 | 6\.5 | 15\.25 | — | Moderate | 
| db\.t4g – burstable\-performance instance classes | 
| db\.t4g\.large | 2 | — | 8 | Up to 2,780 | Up to 5 | 
| db\.t4g\.medium | 2 | — | 4 | Up to 2,085 | Up to 5 | 
| db\.t3 – burstable\-performance instance classes | 
| db\.t3\.large | 2 | Variable | 8 | Up to 2,048 | Up to 5 | 
| db\.t3\.medium | 2 | Variable | 4 | Up to 1,536 | Up to 5 | 
| db\.t3\.small | 2 | Variable | 2 | Up to 1,536 | Up to 5 | 
| db\.t2 – burstable\-performance instance classes | 
| db\.t2\.medium | 2 | Variable | 4 | — | Moderate | 
| db\.t2\.small | 1 | Variable | 2 | — | Low | 

\*\* The r3\.8xlarge DB instance class doesn't have dedicated EBS bandwidth and therefore doesn't offer EBS optimization\. For this instance class, network traffic and Amazon EBS traffic share the same 10\-gigabit network interface\.