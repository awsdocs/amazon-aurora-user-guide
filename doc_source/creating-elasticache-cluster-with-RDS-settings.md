# Creating an Amazon ElastiCache cluster using Aurora DB cluster settings<a name="creating-elasticache-cluster-with-RDS-settings"></a>

You can use the Amazon RDS console for creating ElastiCache clusters\. ElastiCache is a fully managed, in\-memory caching service that provides microsecond read and write latencies that support flexible, real\-time use cases\. ElastiCache can help you accelerate application and database performance\. You can use ElastiCache as a primary data store for use cases that don't require data durability, such as gaming leaderboards, streaming, and data analytics\. ElastiCache helps remove the complexity associated with deploying and managing a distributed computing environment\.

Amazon ElastiCache works with both the Redis and Memcached engines\.  If you're unsure which engine you want to use, see [Comparing Memcached and Redis](https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/SelectEngine.html)\. For more information about Amazon ElastiCache, see the [Amazon ElastiCache User Guide](https://docs.aws.amazon.com/AmazonElastiCache/latest/UserGuide/)\.

**Topics**
+ [Overview of ElastiCache cluster creation with Aurora DB cluster settings](#creating-elasticache-cluster-with-RDS-settings-overview)
+ [Creating an ElastiCache cluster with settings from a new Aurora DB cluster](#creating-elasticache-cluster-with-RDS-settings-new-DB)
+ [Creating an ElastiCache cluster with settings from an existing Aurora DB cluster](#creating-elasticache-cluster-with-RDS-settings-existing-DB)

## Overview of ElastiCache cluster creation with Aurora DB cluster settings<a name="creating-elasticache-cluster-with-RDS-settings-overview"></a>

You can create an ElastiCache cluster from Amazon RDS using the same configuration settings as a newly created or existing Aurora DB cluster\.

When you create an ElastiCache cluster from RDS, you can set the cluster configuration settings according to your requirements\. The newly created ElastiCache cluster inherits the following settings from the associated Aurora DB cluster:
+ ElastiCache connectivity settings
+ ElastiCache security settings

## Creating an ElastiCache cluster with settings from a new Aurora DB cluster<a name="creating-elasticache-cluster-with-RDS-settings-new-DB"></a>

 After creating a new Aurora DB cluster in the RDS console, you can create add\-ons for your Aurora DB cluster from the **Suggested add\-ons** window\.

In the **Suggested add\-ons** window, you can create an ElastiCache cluster from RDS with the same settings as your newly created Aurora DB cluster\.

**Create an ElastiCache cluster with settings from a new DB cluster**

1. To create a DB cluster, follow the instructions in [Creating an Amazon Aurora DB cluster](Aurora.CreateInstance.md)\.

1. After creating a new Aurora DB cluster, the console displays the **Suggested add\-ons** window\. Select **Create an ElastiCache cluster from RDS using your DB settings**\. 

   In the **ElastiCache configuration section**, the **Source DB identifier** displays which DB cluster the ElastiCache cluster inherits settings from\.

1. Choose whether you want to create a Redis or Memcached cluster\. For more information, see [Comparing Memcached and Redis](https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/SelectEngine.html)\.

   If you choose **Redis cluster**, then choose whether you want to keep the cluster mode **Enabled** or **Disabled**\. For more information, see [ Replication: Redis \(Cluster Mode Disabled\) vs\. Redis \(Cluster Mode Enabled\)](https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/Replication.Redis-RedisCluster.html)\.   
![\[Choose cluster type and cluster mode.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/EC-RDS-Config.png)

1. Enter values for **Name**, **Description**, and **Engine version**\. 

   For **Engine version**, the recommended default value is the latest engine version\. You can also choose an **Engine version** for the ElastiCache cluster that best meets your requirements\.

1. Choose the node type in the **Node type** option\. For more information, see [Managing nodes](https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/CacheNodes.html)\.

   If you choose to create a Redis cluster with the **Cluster mode** set to **Enabled**, then enter the number of shards \(partitions/node groups\) in the **Number of shards** option\.

   Enter the number of replicas of each shard in **Number of replicas**\.
**Note**  
The selected node type, the number of shards, and the number of replicas all affect your cluster performance and resource costs\. Be sure these settings match your database needs\. For pricing information, see [Amazon ElastiCache pricing](http://aws.amazon.com/elasticache/pricing/)\.

1. Confirm the ElastiCache connectivity settings\. 

   RDS automatically fills the **Port** and the **Network type**\. ElastiCache creates an equivalent **Subnet group** from the source database\. To customize these settings, select **Customize your connectivity settings**\.  
![\[Confirm or customize your connectivity settings.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/EC-RDS-cnnct-sttngs.png)

1. Confirm the ElastiCache security settings\. 

   ElastiCache provides the default values for **Encryption at rest**, **Encryption key**, **Encryption in transit**, **Access control**, and **Security groups**\. To customize these settings, select **Customize your security settings**\.  
![\[Confirm or customize your security settings.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/EC-RDS-sec-sttngs.png)

1. Verify the default and inherited settings of your ElastiCache cluster\. Some settings can't be changed after creation\.
**Note**  
RDS might adjust the backup window of your ElastiCache cluster to meet the minimum window requirement of 60 minutes\. The backup window of your source database remains the same\. 

1. When you're ready, choose **Create ElastiCache cluster**\.

The console displays a confirmation banner for the ElastiCache cluster creation\. Follow the link in the banner to the ElastiCache console to view the cluster details\. The ElastiCache console displays the newly created ElastiCache cluster\. 

## Creating an ElastiCache cluster with settings from an existing Aurora DB cluster<a name="creating-elasticache-cluster-with-RDS-settings-existing-DB"></a>

You can create an ElastiCache cluster for your existing Aurora DB clusters from the **Actions** dropdown menu in the console\.

**Create an ElastiCache cluster with settings from an existing DB cluster**

1. In the **Databases** page, select the required DB cluster\.

1. In the **Actions** dropdown menu, choose **Create ElastiCache cluster** to create an ElastiCache cluster in RDS that has the same settings as your existing Aurora DB cluster\.

   In the **ElastiCache configuration section**, the **Source DB identifier** shows which DB cluster the ElastiCache cluster inherits settings from\.

1. Choose whether you want to create a Redis or Memcached cluster\. For more information, see [Comparing Memcached and Redis](https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/SelectEngine.html)\.

   If you choose **Redis cluster**, then choose whether you want to keep the cluster mode **Enabled** or **Disabled**\. For more information, see [ Replication: Redis \(Cluster Mode Disabled\) vs\. Redis \(Cluster Mode Enabled\)](https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/Replication.Redis-RedisCluster.html)\.  
![\[Choose cluster type and cluster mode.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/EC-RDS-Config.png)

1. Enter values for **Name**, **Description**, and **Engine version**\. 

   For **Engine version**, the recommended default value is the latest engine version\. You can also choose an **Engine version** for the ElastiCache cluster that best meets your requirements\.

1. Choose the node type in the **Node type** option\. For more information, see [Managing nodes](https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/CacheNodes.html)\.

   If you choose to create a Redis cluster with the **Cluster mode** set to **Enabled**, then enter the number of shards \(partitions/node groups\) in the **Number of shards** option\.

   Enter the number of replicas of each shard in **Number of replicas**\.
**Note**  
The selected node type, the number of shards, and the number of replicas all affect your cluster performance and resource costs\. Be sure these settings match your database needs\. For pricing information, see [Amazon ElastiCache pricing](http://aws.amazon.com/elasticache/pricing/)\.

1. Confirm the ElastiCache connectivity settings\. 

   RDS automatically fills the **Port** and the **Network type**\. ElastiCache creates an equivalent **Subnet group** from the source database\. To customize these settings, select **Customize your connectivity settings**\.  
![\[Confirm or customize your connectivity settings.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/EC-RDS-cnnct-sttngs.png)

    

1. Confirm the ElastiCache security settings\. 

   ElastiCache provides the default values for **Encryption at rest**, **Encryption key**, **Encryption in transit**, **Access control**, and **Security groups**\. To customize these settings, select **Customize your security settings**\.  
![\[Confirm or customize your security settings.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/EC-RDS-sec-sttngs.png)

1. Verify the default and inherited settings of your ElastiCache cluster\. Some settings can't be changed after creation\.
**Note**  
RDS might adjust the backup window of your ElastiCache cluster to meet the minimum window requirement of 60 minutes\. The backup window of your source database remains the same\. 

1. When you're ready, choose **Create ElastiCache cluster**\.

The console displays a confirmation banner for the ElastiCache cluster creation\. Follow the link in the banner to the ElastiCache console to view the cluster details\. The ElastiCache console displays the newly created ElastiCache cluster\. 