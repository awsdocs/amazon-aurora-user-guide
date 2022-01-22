# Aurora dimensions<a name="dimensions"></a>

You can filter Aurora metrics data by using any dimension in the following table\.


|  Dimension  |  Filters the requested data for \. \. \.  | 
| --- | --- | 
|  DBInstanceIdentifier  |  A specific DB instance\.  | 
|  DBClusterIdentifier  |  A specific Amazon Aurora DB cluster\.  | 
|  DBClusterIdentifier, Role  |  A specific Aurora DB cluster, aggregating the metric by instance role \(WRITER/READER\)\. For example, you can aggregate metrics for all READER instances that belong to a cluster\.  | 
|  DatabaseClass  |  All instances in a database class\. For example, you can aggregate metrics for all instances that belong to the database class `db.r5.large`\.  | 
|  EngineName  |  The identified engine name only\. For example, you can aggregate metrics for all instances that have the engine name `mysql`\.  | 
|  SourceRegion  |  The specified Region only\. For example, you can aggregate metrics for all DB instances in the `us-east-1` Region\.  | 