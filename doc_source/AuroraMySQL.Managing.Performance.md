# Managing Performance and Scaling for Amazon Aurora MySQL<a name="AuroraMySQL.Managing.Performance"></a>

## Scaling Aurora MySQL DB Instances<a name="AuroraMySQL.Managing.Performance.InstanceScaling"></a>

You can scale Aurora MySQL DB instances in two ways, instance scaling and read scaling\. For more information about read scaling, see [Read Scaling](Aurora.Managing.Performance.md#Aurora.Managing.Performance.ReadScaling)\.

You can scale your Aurora MySQL DB cluster by modifying the DB instance class for each DB instance in the DB cluster\. Aurora MySQL supports several DB instance classes optimized for Aurora\. The following table describes the specifications of the DB instance classes supported by Aurora MySQL\.


| Instance Class | vCPU | ECU | Memory \(GiB\) | 
| --- | --- | --- | --- | 
|  db\.t2\.small  |  1  | 1 | 2 | 
|  db\.t2\.medium  |  2  | 2 | 4 | 
|  db\.r3\.large  |  2  | 6\.5 | 15\.25 | 
|  db\.r3\.xlarge  |  4  | 13 | 30\.5 | 
|  db\.r3\.2xlarge  |  8  | 26 | 61 | 
|  db\.r3\.4xlarge  |  16  | 52 | 122 | 
|  db\.r3\.8xlarge  |  32  | 104 | 244 | 
|  db\.r4\.large  |  2  | 7 | 15\.25 | 
|  db\.r4\.xlarge  |  4  | 13\.5 | 30\.5 | 
|  db\.r4\.2xlarge  |  8  | 27 | 61 | 
|  db\.r4\.4xlarge  |  16  | 53 | 122 | 
|  db\.r4\.8xlarge  |  32  | 99 | 244 | 
|  db\.r4\.16xlarge  |  64  | 195 | 488 | 
|  db\.r5\.large  |  2  | 10 | 16 | 
|  db\.r5\.xlarge  |  4  | 19 | 32 | 
|  db\.r5\.2xlarge  |  8  | 38 | 64 | 
|  db\.r5\.4xlarge  |  16  | 71 | 128 | 
|  db\.r5\.12xlarge  |  48  | 173 | 384 | 

## Maximum Connections to an Aurora MySQL DB Instance<a name="AuroraMySQL.Managing.MaxConnections"></a>

The maximum number of connections allowed to an Aurora MySQL DB instance is determined by the `max_connections` parameter in the instance\-level parameter group for the DB instance\.

The following table lists the resulting default value of `max_connections` for each DB instance class available to Aurora MySQL\. You can increase the maximum number of connections to your Aurora MySQL DB instance by scaling the instance up to a DB instance class with more memory, or by setting a larger value for the `max_connections` parameter, up to 16,000\.


| Instance Class | max\_connections Default Value | 
| --- | --- | 
|  db\.t2\.small  |  45  | 
|  db\.t2\.medium  |  90  | 
|  db\.r3\.large  |  1000  | 
|  db\.r3\.xlarge  |  2000  | 
|  db\.r3\.2xlarge  |  3000  | 
|  db\.r3\.4xlarge  |  4000  | 
|  db\.r3\.8xlarge  |  5000  | 
|  db\.r4\.large  |  1000  | 
|  db\.r4\.xlarge  |  2000  | 
|  db\.r4\.2xlarge  |  3000  | 
|  db\.r4\.4xlarge  |  4000  | 
|  db\.r4\.8xlarge  |  5000  | 
|  db\.r4\.16xlarge  |  6000  | 
|  db\.r5\.large  |  1000  | 
|  db\.r5\.xlarge  |  2000  | 
|  db\.r5\.2xlarge  |  3000  | 
|  db\.r5\.4xlarge  |  4000  | 
|  db\.r5\.12xlarge  |  6000  | 

If you create a new parameter group to customize your own default for the connection limit, you'll see that the default connection limit is derived using a formula based on the `DBInstanceClassMemory` value\. As shown in the preceding table, the formula produces connection limits that increase by 1000 as the memory doubles between progressively larger R3, R4, and R5 instances, and by 45 for different memory sizes of T2 instances\. The much lower connectivity limits for T2 instances are because T2 instances are intended only for development and test scenarios, not for production workloads\. The default connection limits are tuned for systems that use the default values for other major memory consumers, such as the buffer pool and query cache\. If you change those other settings for your cluster, consider adjusting the connection limit to account for the increase or decrease in available memory on the DB instances\.