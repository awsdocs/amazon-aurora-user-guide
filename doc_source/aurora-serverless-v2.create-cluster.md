# Creating a cluster that uses Aurora Serverless v2<a name="aurora-serverless-v2.create-cluster"></a>

 To create an Aurora cluster where you can add Aurora Serverless v2 DB instances, you follow the same procedure as in [Creating an Amazon Aurora DB cluster](Aurora.CreateInstance.md)\. With Aurora Serverless v2, your clusters are interchangeable with provisioned clusters\. You can have clusters where some DB instances use Aurora Serverless v2 and some DB instances are provisioned\. 

 If you intend to use Aurora Serverless v2 DB instances in a cluster, make sure that the cluster's initial settings meet the requirements that are listed in [Requirements for Aurora Serverless v2](aurora-serverless-v2.requirements.md)\. Specify the following settings to make sure that you can add Aurora Serverless v2 DB instances to the cluster: 

**AWS Region**  
 Create the cluster in an AWS Region where Aurora Serverless v2 DB instances are available\. For details about available Regions, see [Aurora Serverless v2](Concepts.Aurora_Fea_Regions_DB-eng.Feature.ServerlessV2.md)\. 

**DB engine version**  
 Choose an engine version that's compatible with Aurora Serverless v2\. For information about the Aurora Serverless v2 version requirements, see [Requirements for Aurora Serverless v2](aurora-serverless-v2.requirements.md)\. On the **Create database** page in the AWS Management Console, you can choose the filter setting **Show versions that support Serverless v2**\. You can also see the applicable versions in the **Info** panel on the **Create database** page\. 

**DB instance class**  
 If you create a cluster using the AWS Management Console, you choose the DB instance class for the writer DB instance at the same time\. Choose the **Serverless** DB instance class\. When you choose that DB instance class, you also specify the capacity range for the writer DB instance\. That same capacity range applies to all other Aurora Serverless v2 DB instances that you add to that cluster\. If you don't see the **Serverless** choice for the DB instance class, make sure that you chose a DB engine version that's compatible with Aurora Serverless v2\.   
 When you use the AWS CLI or the Amazon RDS API, the parameter that you specify for the DB instance class parameter is `db.serverless`\. 

**capacity range**  
 Fill in the minimum and maximum Aurora capacity unit \(ACU\) values that apply to all the DB instances in the cluster\. This option is available on both the **Create cluster** and **Add reader** console pages when you choose **Serverless** for the DB instance class\.   
 If you don't see the minimum and maximum ACU boxes, make sure that you chose the **Serverless** DB instance class for the writer DB instance\. 

**Tip**  
 A simple way to set up a new cluster where you can use Aurora Serverless v2 is to choose the **Easy create** setting on the console **Create database** page\. With that setting, you only make a single other choice: whether the cluster is intended for development and test use or for production\. Both of those options set up a cluster with an Aurora Serverless v2 writer DB instance\. 

 If you initially create the cluster with a provisioned DB instance, you don't specify the minimum and maximum ACUs\. In that case you can modify the cluster afterward to add that setting\. You can also add an Aurora Serverless v2 reader DB instance to the cluster\. You specify the capacity range as part of that process\. 

 Until you specify the capacity range for your cluster, you can't add any Aurora Serverless v2 DB instances to the cluster using the AWS CLI or RDS API\. If you try to add a Aurora Serverless v2 DB instance, you get an error\. In the AWS CLI or the RDS API procedures, the capacity range is represented by the `ServerlessV2ScalingConfiguration` attribute\. 

 For clusters containing more than one reader DB instance, the failover priority of each Aurora Serverless v2 reader DB instance plays an important part in how that DB instance scales up and down\. You can't specify the priority when you initially create the cluster\. Keep this property in mind when you add a second or later reader DB instance to your cluster\. For more information, see [Choosing the promotion tier for an Aurora Serverless v2 reader](aurora-serverless-v2-administration.md#aurora-serverless-v2-choosing-promotion-tier)\. 

## Console<a name="aurora-serverless-v2-create.console"></a>

**To create a cluster with an Aurora Serverless v2 writer**

1.  Sign in using the AWS Management Console and open the Amazon RDS console\. 

1.  Choose **Create Database**\. On the page that appears, choose the following options: 
   +  For **Engine type**, choose Aurora\. 
   +  For **Edition**, choose Aurora MySQL or Aurora PostgreSQL\. 
   +  For **Version**, choose one of the compatible versions from [Aurora Serverless v2](Concepts.Aurora_Fea_Regions_DB-eng.Feature.ServerlessV2.md)\. To see the available versions, choose the filter **Show versions that support Serverless v2**\. 

1.  For **Capacity settings**, you can accept the default range\. Or you can choose other values for minimum and maximum capacity units\. You can choose from 0\.5 ACUs minimum through 128 ACUs maximum, in increments of 0\.5 ACU\. 

    For more information about Aurora Serverless v2 capacity units, see [Aurora Serverless v2 capacity](aurora-serverless-v2.how-it-works.md#aurora-serverless-v2.how-it-works.capacity) and [Performance and scaling for Aurora Serverless v2](aurora-serverless-v2.setting-capacity.md)\.   
![\[Screenshot of creating DB cluster with console and capacity settings.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/serverless_v2_screencaps/capacity_range.png)

1.  Choose any other cluster settings, as described in [Creating an Amazon Aurora DB cluster](Aurora.CreateInstance.md)\. 

1.  Choose **Create database** to create your Aurora cluster with an Aurora Serverless v2 DB instance as the writer instance, also known as the primary DB instance\. 

## CLI<a name="aurora-serverless-v2-create.cli"></a>

 To create a DB cluster that's compatible with Aurora Serverless v2 DB instances using the AWS CLI, you follow the CLI procedure in [Creating an Amazon Aurora DB cluster](Aurora.CreateInstance.md)\. Include the following parameters in your `create-db-cluster` command:
+ \-\-region *AWS\_Region\_where\_Aurora Serverless v2\_instances\_are\_available*
+ \-\-engine\-version *serverless\_v2\_compatible\_engine\_version*
+ \-\-serverless\-v2\-scaling\-configuration MinCapacity=*minimum\_capacity*,MaxCapacity=*maximum\_capacity* 

The following example shows the creation of an Aurora Serverless v2 DB cluster\.

```
aws rds create-db-cluster \
    --db-cluster-identifier my-serverless-v2-cluster \
    --region eu-central-1 \
    --engine aurora-mysql \
    --engine-version 8.0.mysql_aurora.3.02.0 \
    --serverless-v2-scaling-configuration MinCapacity=1,MaxCapacity=4 \
    --master-username myuser \
    --master-user-password mypassword \
    --backup-retention 1
```

**Note**  
When you create an Aurora Serverless v2 DB cluster using the AWS CLI, the engine mode appears in the output as `provisioned` rather than `serverless`\. The `serverless` engine mode refers to Aurora Serverless v1\.

 For information about the Aurora Serverless v2 version requirements, see [Requirements for Aurora Serverless v2](aurora-serverless-v2.requirements.md)\. For information about the allowed numbers for the capacity range and what those numbers represent, see [Aurora Serverless v2 capacity](aurora-serverless-v2.how-it-works.md#aurora-serverless-v2.how-it-works.capacity) and [Performance and scaling for Aurora Serverless v2](aurora-serverless-v2.setting-capacity.md)\. 

 To verify whether an existing cluster has the capacity settings specified, check the output of the `describe-db-clusters` command for the `ServerlessV2ScalingConfiguration` attribute\. That attribute looks similar to the following\. 

```
"ServerlessV2ScalingConfiguration": {
    "MinCapacity": 1.5,
    "MaxCapacity": 24.0
}
```

**Tip**  
 If you don't specify the minimum and maximum ACUs when you create the cluster, you can use the `modify-db-cluster` command afterward to add that setting\. Until you do, you can't add any Aurora Serverless v2 DB instances to the cluster\. If you try to add a `db.serverless` DB instance, you get an error\. 

## API<a name="aurora-serverless-v2-create.api"></a>

 To create a DB cluster that's compatible with Aurora Serverless v2 DB instances using the RDS API, you follow the API procedure in [Creating an Amazon Aurora DB cluster](Aurora.CreateInstance.md)\. Choose the following settings\. Make sure that your `CreateDBCluster` operation includes the following parameters: 

```
EngineVersion serverless_v2_compatible_engine_version
ServerlessV2ScalingConfiguration with MinCapacity=minimum_capacity and MaxCapacity=maximum_capacity
```

 For information about the Aurora Serverless v2 version requirements, see [Requirements for Aurora Serverless v2](aurora-serverless-v2.requirements.md)\. For information about the allowed numbers for the capacity range and what those numbers represent, see [Aurora Serverless v2 capacity](aurora-serverless-v2.how-it-works.md#aurora-serverless-v2.how-it-works.capacity) and [Performance and scaling for Aurora Serverless v2](aurora-serverless-v2.setting-capacity.md)\. 

 To check if an existing cluster has the capacity settings specified, check the output of the `DescribeDBClusters` operation for the `ServerlessV2ScalingConfiguration` attribute\. That attribute looks similar to the following\. 

```
"ServerlessV2ScalingConfiguration": {
    "MinCapacity": 1.5,
    "MaxCapacity": 24.0
}
```

**Tip**  
 If you don't specify the minimum and maximum ACUs when you create the cluster, you can use the `ModifyDBCluster` operation afterward to add that setting\. Until you do, you can't add any Aurora Serverless v2 DB instances to the cluster\. If you try to add a `db.serverless` DB instance, you get an error\. 