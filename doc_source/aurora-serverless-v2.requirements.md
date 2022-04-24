# Requirements for Aurora Serverless v2<a name="aurora-serverless-v2.requirements"></a>

 When you create a cluster where you intend to use Aurora Serverless v2 DB instances, pay attention to the following requirements\. 

**Topics**
+ [Aurora Serverless v2 is available in certain AWS Regions](#aurora-serverless-v2.requirements.regions)
+ [Aurora Serverless v2 requires minimum engine versions](#aurora-serverless-v2.requirements.versions)
+ [Clusters that use Aurora Serverless v2 must have a capacity range specified](#aurora-serverless-v2.requirements.capacity-range)
+ [Some provisioned features aren't supported in Aurora Serverless v2](#aurora-serverless-v2.limitations)
+ [Some Aurora Serverless v2 aspects are different from Aurora Serverless v1](#aurora-serverless-v2.requirements.v1-v2-differences)

## Aurora Serverless v2 is available in certain AWS Regions<a name="aurora-serverless-v2.requirements.regions"></a>

 For the AWS Regions where Aurora Serverless v2 DB instances are currently available, see [Aurora Serverless v2](Concepts.AuroraFeaturesRegionsDBEngines.grids.md#Concepts.Aurora_Fea_Regions_DB-eng.Feature.ServerlessV2)\. 

## Aurora Serverless v2 requires minimum engine versions<a name="aurora-serverless-v2.requirements.versions"></a>

 Aurora clusters that use Aurora Serverless v2 DB instances must be running one of the following DB engine versions: 
+  MySQL\-compatible DB instances for Aurora Serverless v2 require Aurora MySQL 3\.02\.0 or higher\. This Aurora MySQL version is compatible with MySQL 8\.0\. 
+  PostgreSQL\-compatible DB instances for Aurora Serverless v2 require Aurora PostgreSQL 13\.6 or higher\. 

 The following example shows the AWS CLI commands to confirm the exact DB engine values you can use with Aurora Serverless v2 for a specific AWS Region\. The `--db-instance-class` parameter for Aurora Serverless v2 is always `db.serverless`\. The `--engine` parameter can be `aurora-mysql` or `aurora-postgresql`\. Substitute the appropriate `--region` and `--engine` values to confirm the `--engine-version` values that you can use\. If the command doesn't produce any output, Aurora Serverless v2 isn't available for that combination of Region and DB engine\. 

```
aws rds describe-orderable-db-instance-options --engine aurora-mysql --db-instance-class db.serverless \
  --region my_region --query 'OrderableDBInstanceOptions[].[EngineVersion]' --output text

aws rds describe-orderable-db-instance-options --engine aurora-postgresql --db-instance-class db.serverless \
  --region my_region --query 'OrderableDBInstanceOptions[].[EngineVersion]' --output text
```

## Clusters that use Aurora Serverless v2 must have a capacity range specified<a name="aurora-serverless-v2.requirements.capacity-range"></a>

 An Aurora cluster must have a `ScalingConfigurationInfo` attribute before you can add any DB instances that use the `db.serverless` DB instance class\. This attribute specifies the capacity range\. Aurora Serverless v2 capacity ranges from a minimum of 0\.5 Aurora capacity units \(ACU\) through 128 ACUs, in increments of 0\.5 ACU\. Each ACU provides the equivalent of approximately 2 gibibytes \(GiB\) of RAM and associated CPU and networking\. For details about how Aurora Serverless v2 uses the capacity range settings, see [How Aurora Serverless v2 works](aurora-serverless-v2.how-it-works.md)\. 

 You can specify the minimum and maximum ACU values in the AWS Management Console when you create a cluster and associated Aurora Serverless v2 DB instance\. You can also specify the `--serverless-v2-scaling-configuration` option in the AWS CLI\. Or you can specify the `ServerlessV2ScalingConfiguration` parameter with the Amazon RDS API\. You can specify this attribute when you create a cluster or modify an existing cluster\. For the procedures to set the capacity range, see [Setting the Aurora Serverless v2 capacity range for a cluster](aurora-serverless-v2-administration.md#aurora-serverless-v2-setting-acus)\. For a detailed discussion of how to pick minimum and maximum capacity values and how those settings affect some database parameters, see [Choosing the Aurora Serverless v2 capacity range for an Aurora cluster](aurora-serverless-v2.setting-capacity.md#aurora-serverless-v2-examples-setting-capacity-range-for-cluster)\. 

## Some provisioned features aren't supported in Aurora Serverless v2<a name="aurora-serverless-v2.limitations"></a>

 The following features from Aurora provisioned DB instances currently aren't available for Amazon Aurora Serverless v2: 
+  Database activity streams \(DAS\)\. 
+  Backtracking for Aurora MySQL\. This feature currently isn't available for Aurora MySQL version 3\. For details of Aurora MySQL version 3 feature support, see [Aurora MySQL version 3 compatible with MySQL 8\.0](AuroraMySQL.MySQL80.md)\. 
+  Cluster cache management for Aurora PostgreSQL\. The `apg_ccm_enabled` configuration parameter doesn't apply to Aurora Serverless v2 DB instances\. 
+  In the AWS Billing and Cost Management console, filtering by custom tags added to Aurora Serverless v2 shows zero usage\. Use other filtering options, such as AWS Region, engine, and usageType, to get accurate billed values\. 
+  Aurora Auto Scaling\. This is the type of scaling that adds entire new readers to handle additional read\-intensive workload\. As an alternative, you can create Aurora Serverless v2 reader DB instances in advance and leave them scaled down to low capacity\. That's a faster and less disruptive way to scale a cluster's read capacity than adding new DB instances dynamically\. 

 Some Aurora features work with Aurora Serverless v2, but might cause issues if your capacity range is lower than needed for the memory requirements for those features with your specific workload\. In that case, your database might not perform as well as usual, or might encounter out\-of\-memory errors\. For recommendations about setting the appropriate capacity range, see [Choosing the Aurora Serverless v2 capacity range for an Aurora cluster](aurora-serverless-v2.setting-capacity.md#aurora-serverless-v2-examples-setting-capacity-range-for-cluster)\. For troubleshooting information if your database encounters out\-of\-memory errors due to a misconfigured capacity range, see [Avoiding out\-of\-memory errors](aurora-serverless-v2.setting-capacity.md#aurora-serverless-v2.setting-capacity.incompatible_parameters)\. 

## Some Aurora Serverless v2 aspects are different from Aurora Serverless v1<a name="aurora-serverless-v2.requirements.v1-v2-differences"></a>

 If you are an Aurora Serverless v1 user and this is your first time using Aurora Serverless v2, consult [differences between Aurora Serverless v2 and Aurora Serverless v1 requirements](aurora-serverless-v2.upgrade.md#Serverless.v1-v2-requirements) to understand how requirements are different between Aurora Serverless v1 and Aurora Serverless v2\. 