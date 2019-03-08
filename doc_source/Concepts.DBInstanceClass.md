# Choosing the DB Instance Class<a name="Concepts.DBInstanceClass"></a>

The DB instance class determines the computation and memory capacity of an Amazon RDS DB instance\. The DB instance class you need depends on your processing power and memory requirements\. 

For more information about instance class pricing, see [Amazon RDS Pricing](https://aws.amazon.com/rds/pricing/)\. 

## DB Instance Class Types<a name="Concepts.DBInstanceClass.Types"></a>

Amazon Aurora supports two types of instance classes: Memory Optimized and Burstable Performance\. For more information about Amazon EC2 instance types, see [Instance Type](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html) in the Amazon EC2 documentation\. 

The following are the Memory Optimized DB instance classes available:
+ **db\.r5** – Latest\-generation instance classes optimized for memory\-intensive applications\. These offer improved networking performance\. They are powered by the AWS Nitro System, a combination of dedicated hardware and lightweight hypervisor\.
+ **db\.r4** – Current\-generation instance classes optimized for memory\-intensive applications\. These offer improved networking performance\.
+ **db\.r3** – Previous\-generation instance classes that provide memory optimization and more computing capacity than the db\.m2 instance classes\. The db\.r3 instances classes are not available in the EU \(Paris\) region\. 

The following are the Burstable Performance DB instance classes available:
+ **db\.t3** – Latest\-generation instance classes that provide a baseline performance level, with the ability to burst to full CPU usage\. These instance classes provide more computing capacity than the previous db\.t2 instance classes\. 
+ **db\.t2** – Current\-generation instance classes that provide a baseline performance level, with the ability to burst to full CPU usage\. We recommend using these instance classes only for development and test servers, or other nonproduction servers\. 

## Specifications for All Available DB Instance Classes for Aurora<a name="Concepts.DBInstanceClass.SummaryAurora"></a>

The following table provides details of the Amazon RDS DB instance classes available for Amazon Aurora\. The table columns are explained after the table\. 


****  

| Instance Class | vCPU1 | ECU2 | Memory3 \(GiB\) | VPC Only4 | Max\. Bandwidth5 \(Mbps\) | Network Performance6 | Aurora MySQL | Aurora PostgreSQL | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| db\.r5 – Latest Generation Memory Optimized Instance Classes7 | 
| db\.r5\.24xlarge | 96 | 347 | 768 | Yes | 14,000 | 25 Gbps | No | No | 
| db\.r5\.12xlarge | 48 | 173 | 384 | Yes | 7,000 | 10 Gbps | Yes | No | 
| db\.r5\.4xlarge | 16 | 71 | 128 | Yes | 3,500 | Up to 10 Gbps | Yes | No | 
| db\.r5\.2xlarge | 8 | 38 | 64 | Yes | Up to 3,500 | Up to 10 Gbps | Yes | No | 
| db\.r5\.xlarge | 4 | 19 | 32 | Yes | Up to 3,500 | Up to 10 Gbps | Yes | No | 
| db\.r5\.large | 2 | 10 | 16 | Yes | Up to 3,500 | Up to 10 Gbps | Yes | No | 
| db\.r4 – Current Generation Memory Optimized Instance Classes | 
| db\.r4\.16xlarge | 64 | 195 | 488 | Yes | 14,000 | 25 Gbps | 1\.14\.4 and later | Yes | 
| db\.r4\.8xlarge | 32 | 99 | 244 | Yes | 7,000 | 10 Gbps | 1\.14\.4 and later | Yes | 
| db\.r4\.4xlarge | 16 | 53 | 122 | Yes | 3,500 | Up to 10 Gbps | 1\.14\.4 and later | Yes | 
| db\.r4\.2xlarge | 8 | 27 | 61 | Yes | 1,750 | Up to 10 Gbps | 1\.14\.4 and later | Yes | 
| db\.r4\.xlarge | 4 | 13\.5 | 30\.5 | Yes | 875 | Up to 10 Gbps | 1\.14\.4 and later | Yes | 
| db\.r4\.large | 2 | 7 | 15\.25 | Yes | 437 | Up to 10 Gbps | 1\.14\.4 and later | Yes | 
| db\.r3 – Previous Generation Memory Optimized Instance Classes | 
| db\.r3\.8xlarge | 32 | 104 | 244 | No | — | 10 Gbps | Yes | No | 
| db\.r3\.4xlarge | 16 | 52 | 122 | No | 2,000 | High | Yes | No | 
| db\.r3\.2xlarge | 8 | 26 | 61 | No | 1,000 | High | Yes | No | 
| db\.r3\.xlarge | 4 | 13 | 30\.5 | No | 500 | Moderate | Yes | No | 
| db\.r3\.large | 2 | 6\.5 | 15\.25 | No | — | Moderate | Yes | No | 
| db\.t3 – Latest Generation Burstable Performance Instance Classes8 | 
| db\.t3\.2xlarge | 8 | Variable | 32 | Yes | 2,050 | Up to 5 Gigabit | Yes | No | 
| db\.t3\.xlarge | 4 | Variable | 16 | Yes | 2,050 | Up to 5 Gigabit | Yes | No | 
| db\.t3\.large | 2 | Variable | 8 | Yes | 2,050 | Up to 5 Gigabit | Yes | No | 
| db\.t3\.medium | 2 | Variable | 4 | Yes | 1,500 | Up to 5 Gigabit | Yes | No | 
| db\.t3\.small | 2 | Variable | 2 | Yes | 1,500 | Up to 5 Gigabit | Yes | No | 
| db\.t3\.micro | 2 | Variable | 1 | Yes | 1,500 | Up to 5 Gigabit | Yes | No | 
| db\.t2 – Current Generation Burstable Performance Instance Classes | 
| db\.t2\.medium | 2 | Variable | 4 | Yes | — | Moderate | Yes | No | 
| db\.t2\.small | 1 | Variable | 2 | Yes | — | Low | Yes | No | 

1. **vCPU** – The number of virtual central processing units \(CPUs\)\. A virtual CPU is a unit of capacity that you can use to compare DB instance classes\. Instead of purchasing or leasing a particular processor to use for several months or years, you are renting capacity by the hour\. Our goal is to provide a consistent amount of CPU capacity no matter what the actual underlying hardware\. 

1. **ECU** – The relative measure of the integer processing power of an Amazon EC2 instance\. To make it easy for developers to compare CPU capacity between different instance classes, we have defined an Amazon EC2 Compute Unit\. The amount of CPU that is allocated to a particular instance is expressed in terms of these EC2 Compute Units\. One ECU currently provides CPU capacity equivalent to a 1\.0–1\.2 GHz 2007 Opteron or 2007 Xeon processor\. 

1. **Memory \(GiB\)** – The RAM memory, in gibibytes, allocated to the DB instance\. There is often a consistent ratio between memory and vCPU\. For example, the db\.m1 instance class has the same memory to vCPU ratio as the db\.m3 instance class, but for most use cases the db\.m3 instance class provides better, more consistent performance, than the db\.m1 instance class\. 

1. **VPC Only** – The instance class is supported only for DB instances that are in a Virtual Private Cloud \(VPC\)\. 

1. **Max\. Bandwidth \(Mbps\)** – The maximum bandwidth in megabits per second\. Divide by 8 to get the expected throughput in megabytes per second\. 
**Important**  
For general purpose \(gp2\) storage, the maximum throughput is 1,280 Mbps \(160 MB/s\)\. 

1. **Network Performance** – The network speed relative to other DB instance classes\. 

1. **Aurora Support for db\.r5** – Aurora MySQL supports the db\.r5 instance classes for all Aurora MySQL versions except 2\.03\.4\. The maximum available instance size is db\.r5\.12xlarge\. These instance classes are available for Aurora MySQL in all Aurora regions except AWS GovCloud \(US\-West\), AWS GovCloud \(US\-East\), and China \(Beijing\)\. 

1. **Aurora Support for db\.t3** – Aurora MySQL supports the db\.t3 instance classes for all Aurora MySQL versions except 2\.03\.4\. These instance classes are available for Aurora MySQL in all Aurora regions except AWS GovCloud \(US\-West\), AWS GovCloud \(US\-East\), and China \(Beijing\)\. 