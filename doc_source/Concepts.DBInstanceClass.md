# DB Instance Classes<a name="Concepts.DBInstanceClass"></a>

The DB instance class determines the computation and memory capacity of an Amazon RDS DB instance\. The DB instance class you need depends on your processing power and memory requirements\. 

For more information about instance class pricing, see [Amazon RDS Pricing](https://aws.amazon.com/rds/pricing/)\. 

**Topics**
+ [DB Instance Class Types](#Concepts.DBInstanceClass.Types)
+ [Supported DB Engines for DB Instance Classes](#Concepts.DBInstanceClass.SupportAurora)
+ [Hardware Specifications for DB Instance Classes for Aurora](#Concepts.DBInstanceClass.Summary)

## DB Instance Class Types<a name="Concepts.DBInstanceClass.Types"></a>

Amazon Aurora supports two types of instance classes: Memory Optimized and Burstable Performance\. For more information about Amazon EC2 instance types, see [Instance Type](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html) in the Amazon EC2 documentation\. 

The following are the Memory Optimized DB instance classes available:
+ **db\.r5** – Latest\-generation instance classes optimized for memory\-intensive applications\. These offer improved networking performance\. They are powered by the AWS Nitro System, a combination of dedicated hardware and lightweight hypervisor\.
+ **db\.r4** – Current\-generation instance classes optimized for memory\-intensive applications\. These offer improved networking performance\.
+ **db\.r3** – Previous\-generation instance classes that provide memory optimization\. The db\.r3 instances classes are not available in the Europe \(Paris\) region\. 

The following are the Burstable Performance DB instance classes available:
+ **db\.t3** – Latest\-generation instance classes that provide a baseline performance level, with the ability to burst to full CPU usage\. These instance classes provide more computing capacity than the previous db\.t2 instance classes\. They are powered by the AWS Nitro System, a combination of dedicated hardware and lightweight hypervisor\. 
+ **db\.t2** – Current\-generation instance classes that provide a baseline performance level, with the ability to burst to full CPU usage\. We recommend using these instance classes only for development and test servers, or other nonproduction servers\. 

**Note**  
The DB instance classes that use the AWS Nitro System \(db\.r5, db\.t3\) are throttled on combined read plus write workload\.

For DB instance class hardware specifications, see [Hardware Specifications for DB Instance Classes for Aurora ](#Concepts.DBInstanceClass.Summary)\.

## Supported DB Engines for DB Instance Classes<a name="Concepts.DBInstanceClass.SupportAurora"></a>

The following are DB engine considerations for DB instance classes:
+ **Aurora Support for db\.r5**
  + Aurora MySQL versions support the db\.r5 instance classes as specified in the following table\. These instance classes are available in all Aurora regions except AWS GovCloud \(US\-West\), AWS GovCloud \(US\-East\), and China \(Beijing\)\. 
  + For Aurora PostgreSQL, the versions that support db\.r5 instance classes depend on the AWS Region that your DB cluster is in\. To determine which Aurora PostgreSQL versions support db\.r5 instance classes for a specific AWS Region, use the CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-orderable-db-instance-options.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-orderable-db-instance-options.html)\. Enter the AWS Region of your cluster for the `--region` parameter in the following command\.

    For Linux, macOS, or Unix:

    ```
    aws rds describe-orderable-db-instance-options --engine aurora-postgresql --db-instance-class db.r5.large \
        --query 'OrderableDBInstanceOptions[].[DBInstanceClass,StorageType,Engine,EngineVersion]' --output text \
        --region your-cluster-AWS-Region
    ```

    For Windows:

    ```
    aws rds describe-orderable-db-instance-options --engine aurora-postgresql --db-instance-class db.r5.large ^
        --query 'OrderableDBInstanceOptions[].[DBInstanceClass,StorageType,Engine,EngineVersion]' --output text ^
        --region your-cluster-AWS-Region
    ```
+ **Aurora Support for db\.t3** 
  + Aurora MySQL supports the db\.t3\.medium and db\.t3\.small instance classes for Aurora MySQL 1\.15 and higher, and all Aurora MySQL 2\.x versions\. These instance classes are available for Aurora MySQL in all Aurora regions except AWS GovCloud \(US\-West\), AWS GovCloud \(US\-East\), and China \(Beijing\)\. 
  + For Aurora MySQL db\.r5, db\.r4, and db\.t3 DB instance classes, no instances in the cluster can have pending instance\-level system updates\. To see pending system updates, use the following AWS CLI command\.

    ```
    aws rds describe-pending-maintenance-actions
    ```
  + Aurora PostgreSQL supports the following db\.t3 instance classes:
    + The db\.t3\.medium instance class is supported for versions compatible with PostgreSQL 10\.7 or later\.
    + The db\.t3\.large instance class is supported for versions compatible with PostgreSQL versions 10\.11 or later and 11\.6 or later\.

In the following table, you can find details about supported Amazon Aurora DB instance classes for the Aurora DB engines\. 


****  

| Instance Class | Aurora MySQL | Aurora PostgreSQL | 
| --- | --- | --- | 
| db\.r5 – Latest Generation Memory Optimized Instance Classes | 
| db\.r5\.24xlarge | 1\.22 and later, 2\.06 and later | Yes | 
| db\.r5\.16xlarge | 1\.22 and later, 2\.06 and later | Yes | 
| db\.r5\.12xlarge | 1\.14\.4 and later | Yes | 
| db\.r5\.8xlarge | 1\.22 and later, 2\.06 and later | Yes | 
| db\.r5\.4xlarge | 1\.14\.4 and later | Yes | 
| db\.r5\.2xlarge | 1\.14\.4 and later | Yes | 
| db\.r5\.xlarge | 1\.14\.4 and later | Yes | 
| db\.r5\.large | 1\.14\.4 and later | Yes | 
| db\.r4 – Current Generation Memory Optimized Instance Classes | 
| db\.r4\.16xlarge | 1\.14\.4 and later | Yes | 
| db\.r4\.8xlarge | 1\.14\.4 and later | Yes | 
| db\.r4\.4xlarge | 1\.14\.4 and later | Yes | 
| db\.r4\.2xlarge | 1\.14\.4 and later | Yes | 
| db\.r4\.xlarge | 1\.14\.4 and later | Yes | 
| db\.r4\.large | 1\.14\.4 and later | Yes | 
| db\.r3 – Previous Generation Memory Optimized Instance Classes | 
| db\.r3\.8xlarge | Yes | No | 
| db\.r3\.4xlarge | Yes | No | 
| db\.r3\.2xlarge | Yes | No | 
| db\.r3\.xlarge | Yes | No | 
| db\.r3\.large | Yes | No | 
| db\.t3 – Latest Generation Burstable Performance Instance Classes | 
| db\.t3\.2xlarge | No | No | 
| db\.t3\.xlarge | No | No | 
| db\.t3\.large | No | 10\.11 or later and 11\.6 or later | 
| db\.t3\.medium | 1\.14\.4 and later | 10\.7 or later | 
| db\.t3\.small | 1\.14\.4 and later | No | 
| db\.t3\.micro | No | No | 
| db\.t2 – Current Generation Burstable Performance Instance Classes | 
| db\.t2\.medium | Yes | No | 
| db\.t2\.small | Yes | No | 

## Hardware Specifications for DB Instance Classes for Aurora<a name="Concepts.DBInstanceClass.Summary"></a>

The following terminology is used to describe hardware specifications for DB instance classes:

**vCPU**  
The number of virtual central processing units \(CPUs\)\. A *virtual CPU *is a unit of capacity that you can use to compare DB instance classes\. Instead of purchasing or leasing a particular processor to use for several months or years, you are renting capacity by the hour\. Our goal is to make a consistent and specific amount of CPU capacity available, within the limits of the actual underlying hardware\.

**ECU**  
The relative measure of the integer processing power of an Amazon EC2 instance\. To make it easy for developers to compare CPU capacity between different instance classes, we have defined an Amazon EC2 Compute Unit\. The amount of CPU that is allocated to a particular instance is expressed in terms of these EC2 Compute Units\. One ECU currently provides CPU capacity equivalent to a 1\.0–1\.2 GHz 2007 Opteron or 2007 Xeon processor\.

**Memory \(GiB\)**  
The RAM, in gibibytes, allocated to the DB instance\. There is often a consistent ratio between memory and vCPU\. As an example, take the db\.r4 instance class, which has a memory to vCPU ratio similar to the db\.r5 instance class\. However, for most use cases the db\.r5 instance class provides better, more consistent performance than the db\.r4 instance class\. 

**Max\. Bandwidth \(Mbps\)**  
The maximum bandwidth in megabits per second\. Divide by 8 to get the expected throughput in megabytes per second\.   
This figure refers to I/O bandwidth for local storage within the DB instance\. It doesn't apply to communication with the Aurora cluster volume\.

**Network Performance**  
The network speed relative to other DB instance classes\.

In the following table, you can find hardware details about the Amazon RDS DB instance classes for Aurora\. 

For information about Aurora DB engine support for each DB instance class, see [Supported DB Engines for DB Instance Classes](#Concepts.DBInstanceClass.SupportAurora)\. 


****  

| Instance Class | vCPU | ECU | Memory \(GiB\) | Max\. Bandwidth \(Mbps\) of Local Storage | Network Performance | 
| --- | --- | --- | --- | --- | --- | 
| db\.r5 – Latest Generation Memory Optimized Instance Classes | 
| db\.r5\.24xlarge | 96 | 347 | 768 | 19,000 | 25 Gbps | 
| db\.r5\.16xlarge | 64 | 264 | 512 | 13,600 | 20 Gbps | 
| db\.r5\.12xlarge | 48 | 173 | 384 | 9,500 | 10 Gbps | 
| db\.r5\.8xlarge | 32 | 132 | 256 | 6,800 | 10 Gbps | 
| db\.r5\.4xlarge | 16 | 71 | 128 | 4,750 | Up to 10 Gbps | 
| db\.r5\.2xlarge | 8 | 38 | 64 | Up to 4,750 | Up to 10 Gbps | 
| db\.r5\.xlarge | 4 | 19 | 32 | Up to 4,750 | Up to 10 Gbps | 
| db\.r5\.large | 2 | 10 | 16 | Up to 4,750 | Up to 10 Gbps | 
| db\.r4 – Current Generation Memory Optimized Instance Classes | 
| db\.r4\.16xlarge | 64 | 195 | 488 | 14,000 | 25 Gbps | 
| db\.r4\.8xlarge | 32 | 99 | 244 | 7,000 | 10 Gbps | 
| db\.r4\.4xlarge | 16 | 53 | 122 | 3,500 | Up to 10 Gbps | 
| db\.r4\.2xlarge | 8 | 27 | 61 | 1,700 | Up to 10 Gbps | 
| db\.r4\.xlarge | 4 | 13\.5 | 30\.5 | 850 | Up to 10 Gbps | 
| db\.r4\.large | 2 | 7 | 15\.25 | 425 | Up to 10 Gbps | 
| db\.r3 – Previous Generation Memory Optimized Instance Classes | 
| db\.r3\.8xlarge | 32 | 104 | 244 | — | 10 Gbps | 
| db\.r3\.4xlarge | 16 | 52 | 122 | 2,000 | High | 
| db\.r3\.2xlarge | 8 | 26 | 61 | 1,000 | High | 
| db\.r3\.xlarge | 4 | 13 | 30\.5 | 500 | Moderate | 
| db\.r3\.large | 2 | 6\.5 | 15\.25 | — | Moderate | 
| db\.t3 – Latest Generation Burstable Performance Instance Classes | 
| db\.t3\.2xlarge | 8 | Variable | 32 | Up to 2,048 | Up to 5 Gbps | 
| db\.t3\.xlarge | 4 | Variable | 16 | Up to 2,048 | Up to 5 Gbps | 
| db\.t3\.large | 2 | Variable | 8 | Up to 2,048 | Up to 5 Gbps | 
| db\.t3\.medium | 2 | Variable | 4 | Up to 1,536 | Up to 5 Gbps | 
| db\.t3\.small | 2 | Variable | 2 | Up to 1,536 | Up to 5 Gbps | 
| db\.t3\.micro | 2 | Variable | 1 | Up to 1,536 | Up to 5 Gbps | 
| db\.t2 – Current Generation Burstable Performance Instance Classes | 
| db\.t2\.medium | 2 | Variable | 4 | — | Moderate | 
| db\.t2\.small | 1 | Variable | 2 | — | Low | 