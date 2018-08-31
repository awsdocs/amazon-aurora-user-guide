# Choosing the DB Instance Class<a name="Concepts.DBInstanceClass"></a>

The DB instance class determines the computation and memory capacity of an Amazon RDS DB instance\. The DB instance class you need depends on your processing power and memory requirements\. 

For more information about instance class pricing, see [Amazon RDS Pricing](https://aws.amazon.com/rds/pricing/)\. 

## DB Instance Class Types<a name="Concepts.DBInstanceClass.Types"></a>

Amazon Aurora supports two types of instance classes: Memory Optimized and Burstable Performance\. For more information about Amazon EC2 instance types, see [Instance Type](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html) in the Amazon EC2 documentation\. 

The following are the Memory Optimized DB instance classes available:
+ **db\.r4** – Current\-generation instance classes optimized for memory\-intensive applications\. These offer a better price per GiB of RAM than the db\.r3 instance classes\. 
+ **db\.r3** – Previous\-generation instance classes that provide memory optimization and more computing capacity than the db\.m2 instance classes\. The db\.r3 instances classes are not available in the EU \(Paris\) region\. 

The following are the Burstable Performance DB instance classes available:
+ **db\.t2** – Instance classes that provide a baseline performance level, with the ability to burst to full CPU usage\. We recommend only using these instance classes for development and test servers, or other non\-production servers\. 

## Specifications for All Available DB Instance Classes for Aurora<a name="Concepts.DBInstanceClass.SummaryAurora"></a>

The following table provides details of the Amazon RDS DB instance classes available for Amazon Aurora\. The table columns are explained after the table\. 


****  

| Instance Class | vCPU1 | ECU2 | Memory3 \(GiB\) | VPC Only4 | EBS Optimized5 | Max\. Bandwidth6 \(Mbps\) | Network Performance7 | Aurora MySQL | Aurora PostgreSQL | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| db\.r4 – Latest Generation Memory Optimized Instance Classes | 
| db\.r4\.16xlarge | 64 | 195 | 488 | Yes | Yes | 14,000 | 25 Gbps | 1\.15 and later | Yes | 
| db\.r4\.8xlarge | 32 | 99 | 244 | Yes | Yes | 7,000 | 10 Gbps | 1\.15 and later | Yes | 
| db\.r4\.4xlarge | 16 | 53 | 122 | Yes | Yes | 3,500 | Up to 10 Gbps | 1\.15 and later | Yes | 
| db\.r4\.2xlarge | 8 | 27 | 61 | Yes | Yes | 1,750 | Up to 10 Gbps | 1\.15 and later | Yes | 
| db\.r4\.xlarge | 4 | 13\.5 | 30\.5 | Yes | Yes | 875 | Up to 10 Gbps | 1\.15 and later | Yes | 
| db\.r4\.large | 2 | 7 | 15\.25 | Yes | Yes | 437 | Up to 10 Gbps | 1\.15 and later | Yes | 
| db\.r3 – Current Generation Memory Optimized Instance Classes | 
| db\.r3\.8xlarge | 32 | 104 | 244 | No | No | — | 10 Gbps | Yes | No | 
| db\.r3\.4xlarge | 16 | 52 | 122 | No | Yes | 2,000 | High | Yes | No | 
| db\.r3\.2xlarge | 8 | 26 | 61 | No | Yes | 1,000 | High | Yes | No | 
| db\.r3\.xlarge | 4 | 13 | 30\.5 | No | Yes | 500 | Moderate | Yes | No | 
| db\.r3\.large | 2 | 6\.5 | 15\.25 | No | No | — | Moderate | Yes | No | 
| db\.t2 – Current Generation Burstable Performance Instance Classes | 
| db\.t2\.2xlarge | 8 | 8 | 32 | Yes | No | — | Moderate | No | No | 
| db\.t2\.xlarge | 4 | 4 | 16 | Yes | No | — | Moderate | No | No | 
| db\.t2\.large | 2 | 2 | 8 | Yes | No | — | Moderate | No | No | 
| db\.t2\.medium | 2 | 2 | 4 | Yes | No | — | Moderate | Yes | No | 
| db\.t2\.small | 1 | 1 | 2 | Yes | No | — | Low | Yes | No | 
| db\.t2\.micro | 1 | 1 | 1 | Yes | No | — | Low | No | No | 

1. **vCPU** – The number of virtual central processing units \(CPUs\)\. A virtual CPU is a unit of capacity that you can use to compare DB instance classes\. Instead of purchasing or leasing a particular processor to use for several months or years, you are renting capacity by the hour\. Our goal is to provide a consistent amount of CPU capacity no matter what the actual underlying hardware\. 

1. **ECU** – The relative measure of the integer processing power of an Amazon EC2 instance\. To make it easy for developers to compare CPU capacity between different instance classes, we have defined an Amazon EC2 Compute Unit\. The amount of CPU that is allocated to a particular instance is expressed in terms of these EC2 Compute Units\. One ECU currently provides CPU capacity equivalent to a 1\.0–1\.2 GHz 2007 Opteron or 2007 Xeon processor\. 

1. **Memory \(GiB\)** – The RAM memory, in gibibytes, allocated to the DB instance\. There is often a consistent ratio between memory and vCPU\. For example, the db\.m1 instance class has the same memory to vCPU ratio as the db\.m3 instance class, but for most use cases the db\.m3 instance class provides better, more consistent performance, than the db\.m1 instance class\. 

1. **VPC Only** – The instance class is supported only for DB instances that are in a Virtual Private Cloud \(VPC\)\. 

1. **EBS\-Optimized** – The DB instance uses an optimized configuration stack and provides additional, dedicated capacity for I/O\. This optimization provides the best performance by minimizing contention between I/O and other traffic from your instance\. For more information about Amazon EBS–optimized instances, see [Amazon EBS–Optimized Instances](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSOptimized.html) in the Amazon EC2 documentation\. 

1. **Max\. Bandwidth \(Mbps\)** – The maximum bandwidth in megabits per second\. Divide by 8 to get the expected throughput in megabytes per second\. 
**Important**  
For general purpose \(gp2\) storage, the maximum throughput is 1,280 Mbps \(160 MB/s\)\. 

1. **Network Performance** – The network speed relative to other DB instance classes\. 