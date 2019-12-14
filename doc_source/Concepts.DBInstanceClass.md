# Choosing the DB Instance Class<a name="Concepts.DBInstanceClass"></a>

The DB instance class determines the computation and memory capacity of an Amazon RDS DB instance\. The DB instance class you need depends on your processing power and memory requirements\. 

For more information about instance class pricing, see [Amazon RDS Pricing](https://aws.amazon.com/rds/pricing/)\. 

**Topics**
+ [DB Instance Class Types](#Concepts.DBInstanceClass.Types)
+ [Terminology for DB Instance Class Hardware Specifications](#Concepts.DBInstanceClass.Terminology)
+ [Hardware Specifications for All Available DB Instance Classes for Aurora](#Concepts.DBInstanceClass.SummaryAurora)

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

## Terminology for DB Instance Class Hardware Specifications<a name="Concepts.DBInstanceClass.Terminology"></a>

The following terminology is used to describe hardware specifications for DB instance classes:
+ **vCPU** – The number of virtual central processing units \(CPUs\)\. A *virtual CPU *is a unit of capacity that you can use to compare DB instance classes\. Instead of purchasing or leasing a particular processor to use for several months or years, you are renting capacity by the hour\. Our goal is to make a consistent and specific amount of CPU capacity available, within the limits of the actual underlying hardware\. 
+ **ECU** – The relative measure of the integer processing power of an Amazon EC2 instance\. To make it easy for developers to compare CPU capacity between different instance classes, we have defined an Amazon EC2 Compute Unit\. The amount of CPU that is allocated to a particular instance is expressed in terms of these EC2 Compute Units\. One ECU currently provides CPU capacity equivalent to a 1\.0–1\.2 GHz 2007 Opteron or 2007 Xeon processor\. 
+ **Memory \(GiB\)** – The RAM, in gibibytes, allocated to the DB instance\. There is often a consistent ratio between memory and vCPU\. As an example, take the db\.r4 instance class, which has a memory to vCPU ratio similar to the db\.r5 instance class\. However, for most use cases the db\.r5 instance class provides better, more consistent performance than the db\.r4 instance class\. 
+ **Max\. Bandwidth \(Mbps\)** – The maximum bandwidth in megabits per second\. Divide by 8 to get the expected throughput in megabytes per second\. 
+ **Network Performance** – The network speed relative to other DB instance classes\. 

## Hardware Specifications for All Available DB Instance Classes for Aurora<a name="Concepts.DBInstanceClass.SummaryAurora"></a>

In the following table, you can find details about the Amazon RDS DB instance classes available for Amazon Aurora\. For a more detailed explanation of the table column terminology, see [Terminology for DB Instance Class Hardware Specifications](#Concepts.DBInstanceClass.Terminology)\. 

The following are DB engine considerations for DB instance classes:
+ **Aurora Support for db\.r5** – These instance classes are available in all Aurora regions except AWS GovCloud \(US\-West\), AWS GovCloud \(US\-East\), and China \(Beijing\)\. 
  + Aurora MySQL versions support the db\.r5 instance classes as specified in the following table\.
  + For Aurora PostgreSQL, only versions compatible with PostgreSQL 10\.6 or later support the db\.r5 instance classes\. 
+ **Aurora Support for db\.t3** 
  + Aurora MySQL supports the db\.t3\.medium and db\.t3\.small instance classes for Aurora MySQL 1\.15 and higher, and all Aurora MySQL 2\.x versions\. These instance classes are available for Aurora MySQL in all Aurora regions except AWS GovCloud \(US\-West\), AWS GovCloud \(US\-East\), and China \(Beijing\)\. 
  + For Aurora MySQL db\.r5, db\.r4, and db\.t3 DB instance classes, no instances in the cluster can have pending instance\-level system updates\. To see pending system updates, use the following AWS CLI command\.

    ```
    aws rds describe-pending-maintenance-actions
    ```
  + Aurora PostgreSQL supports only the db\.t3\.medium instance class for versions compatible with PostgreSQL 10\.7 or later\. These instance classes are available for Aurora PostgreSQL in all Aurora regions except China \(Ningxia\)\. 


****  

|  |  |  |  |  |  |  |  | 
| --- |--- |--- |--- |--- |--- |--- |--- |
| **Instance Class** | **vCPU** | **ECU** | **Memory \(GiB\)** | **Max\. Bandwidth \(Mbps\)** | **Network Performance** | **Aurora MySQL** | **Aurora PostgreSQL** | 
| db\.r5 – Latest Generation Memory Optimized Instance Classes | 
| db\.r5\.24xlarge | 96 | 347 | 768 | 19,000 | 25 Gbps | 1\.22 and later, 2\.06 and later | Yes | 
| db\.r5\.16xlarge | 64 | 264 | 512 | 13,600 | 20 Gbps | 1\.22 and later, 2\.06 and later | No | 
| db\.r5\.12xlarge | 48 | 173 | 384 | 9,500 | 10 Gbps | 1\.14\.4 and later | Yes | 
| db\.r5\.8xlarge | 32 | 132 | 256 | 6,800 | 10 Gbps | 1\.22 and later, 2\.06 and later | No | 
| db\.r5\.4xlarge | 16 | 71 | 128 | 4,750 | Up to 10 Gbps | 1\.14\.4 and later | Yes | 
| db\.r5\.2xlarge\* | 8 | 38 | 64 | Up to 4,750 | Up to 10 Gbps | 1\.14\.4 and later | Yes | 
| db\.r5\.xlarge\* | 4 | 19 | 32 | Up to 4,750 | Up to 10 Gbps | 1\.14\.4 and later | Yes | 
| db\.r5\.large\* | 2 | 10 | 16 | Up to 4,750 | Up to 10 Gbps | 1\.14\.4 and later | Yes | 
| db\.r4 – Current Generation Memory Optimized Instance Classes | 
| db\.r4\.16xlarge | 64 | 195 | 488 | 14,000 | 25 Gbps | 1\.14\.4 and later | Yes | 
| db\.r4\.8xlarge | 32 | 99 | 244 | 7,000 | 10 Gbps | 1\.14\.4 and later | Yes | 
| db\.r4\.4xlarge | 16 | 53 | 122 | 3,500 | Up to 10 Gbps | 1\.14\.4 and later | Yes | 
| db\.r4\.2xlarge | 8 | 27 | 61 | 1,750 | Up to 10 Gbps | 1\.14\.4 and later | Yes | 
| db\.r4\.xlarge | 4 | 13\.5 | 30\.5 | 875 | Up to 10 Gbps | 1\.14\.4 and later | Yes | 
| db\.r4\.large | 2 | 7 | 15\.25 | 437 | Up to 10 Gbps | 1\.14\.4 and later | Yes | 
| db\.r3 – Previous Generation Memory Optimized Instance Classes | 
| db\.r3\.8xlarge | 32 | 104 | 244 | — | 10 Gbps | Yes | No | 
| db\.r3\.4xlarge | 16 | 52 | 122 | 2,000 | High | Yes | No | 
| db\.r3\.2xlarge | 8 | 26 | 61 | 1,000 | High | Yes | No | 
| db\.r3\.xlarge | 4 | 13 | 30\.5 | 500 | Moderate | Yes | No | 
| db\.r3\.large | 2 | 6\.5 | 15\.25 | — | Moderate | Yes | No | 
| **Instance Class** | **vCPU** | **ECU** | **Memory \(GiB\)** | **Max\. Bandwidth \(Mbps\)** | **Network Performance** | **Aurora MySQL** | **Aurora PostgreSQL** | 
| db\.t3 – Latest Generation Burstable Performance Instance Classes | 
| db\.t3\.2xlarge\* | 8 | Variable | 32 | Up to 2,048 | Up to 5 Gbps | No | No | 
| db\.t3\.xlarge\* | 4 | Variable | 16 | Up to 2,048 | Up to 5 Gbps | No | No | 
| db\.t3\.large\* | 2 | Variable | 8 | Up to 2,048 | Up to 5 Gbps | No | No | 
| db\.t3\.medium\* | 2 | Variable | 4 | Up to 1,536 | Up to 5 Gbps | 1\.14\.4 and later | Yes | 
| db\.t3\.small\* | 2 | Variable | 2 | Up to 1,536 | Up to 5 Gbps | 1\.14\.4 and later | No | 
| db\.t3\.micro\* | 2 | Variable | 1 | Up to 1,536 | Up to 5 Gbps | No | No | 
| db\.t2 – Current Generation Burstable Performance Instance Classes | 
| db\.t2\.medium | 2 | Variable | 4 | — | Moderate | Yes | No | 
| db\.t2\.small | 1 | Variable | 2 | — | Low | Yes | No | 

\* These DB instance classes can support maximum performance for 30 minutes at least once every 24 hours\. For more information on baseline performance of these instance types, see [Amazon EBS–Optimized Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSOptimized.html) in the *Amazon EC2 User Guide for Linux Instances\.*