# Managing Aurora Serverless v2<a name="aurora-serverless-v2-administration"></a>

 With Aurora Serverless v2, your clusters are interchangeable with provisioned clusters\. The Aurora Serverless v2 properties apply to one or more DB instances within a cluster\. Thus, the procedures for creating clusters, modifying clusters, creating and restoring snapshots, and so on, are basically the same as for other kinds of Aurora clusters\. For general procedures for managing Aurora clusters and DB instances, see [Managing an Amazon Aurora DB cluster](CHAP_Aurora.md)\. 

 Following, you can learn about management considerations for clusters that contain Aurora Serverless v2 DB instances\. 

**Topics**
+ [Setting the Aurora Serverless v2 capacity range for a cluster](#aurora-serverless-v2-setting-acus)
+ [Checking the capacity range for Aurora Serverless v2](#aurora-serverless-v2-checking-capacity)
+ [Creating an Aurora Serverless v2 writer](#aurora-serverless-v2-adding-writer)
+ [Adding an Aurora Serverless v2 reader](#aurora-serverless-v2-adding-reader)
+ [Converting a provisioned writer or reader to Aurora Serverless v2](#aurora-serverless-v2-converting-from-provisioned)
+ [Converting an Aurora Serverless v2 writer or reader to provisioned](#aurora-serverless-v2-converting-to-provisioned)
+ [Choosing the promotion tier for an Aurora Serverless v2 reader](#aurora-serverless-v2-choosing-promotion-tier)
+ [Using TLS/SSL with Aurora Serverless v2](#aurora-serverless-v2.tls)
+ [Viewing Aurora Serverless v2 writers and readers](#aurora-serverless-v2.viewing)
+ [Logging for Aurora Serverless v2](#aurora-serverless-v2.logging)

## Setting the Aurora Serverless v2 capacity range for a cluster<a name="aurora-serverless-v2-setting-acus"></a>

 To modify configuration parameters or other settings for clusters containing Aurora Serverless v2 DB instances, or the DB instances themselves, follow the same general procedures as for provisioned clusters\. For details, see [Modifying an Amazon Aurora DB cluster](Aurora.Modifying.md)\. 

 The most important setting that's unique to Aurora Serverless v2 is the capacity range\. After you set the minimum and maximum Aurora capacity unit \(ACU\) values for an Aurora cluster, you don't need to actively adjust the capacity of the Aurora Serverless v2 DB instances in the cluster\. Aurora does that for you\. This setting is managed at the cluster level\. The same minimum and maximum ACU values apply to each Aurora Serverless v2 DB instance in the cluster\. 

 You can set the following specific values: 
+  **Minimum ACUs** – The Aurora Serverless v2 DB instance can reduce capacity down to this number of ACUs\. 
+  **Maximum ACUs** – The Aurora Serverless v2 DB instance can increase capacity up to this number of ACUs\. 

 For details about the effects of the capacity range and how to monitor and fine\-tune it, see [Important Amazon CloudWatch metrics for Aurora Serverless v2](aurora-serverless-v2.setting-capacity.md#aurora-serverless-v2.viewing.monitoring) and [Performance and scaling for Aurora Serverless v2](aurora-serverless-v2.setting-capacity.md)\. Your goal is to make sure that the maximum capacity for the cluster is high enough to handle spikes in workload, and the minimum is low enough to minimize costs when the cluster isn't busy\. 

 Suppose that you determine based on your monitoring that the ACU range for the cluster should be higher, lower, wider, or narrower\. You can set the capacity of an Aurora cluster to a specific range of ACUs with the AWS Management Console, the AWS CLI, or the Amazon RDS API\. This capacity range applies to every Aurora Serverless v2 DB instance in the cluster\. 

 For example, suppose that your cluster has a capacity range of 1–16 ACUs and contains two Aurora Serverless v2 DB instances\. Then the cluster as a whole consumes somewhere between 2 ACUs \(when idle\) and 32 ACUs \(when fully utilized\)\. If you change the capacity range from 8 to 20\.5 ACUs, now the cluster consumes 16 ACUs when idle, and up to 41 ACUs when fully utilized\. 

 Aurora automatically sets certain parameters for Aurora Serverless v2 DB instances to values that depend on the maximum ACU value in the capacity range\. For the list of such parameters, see [Maximum connections for Aurora Serverless v2](aurora-serverless-v2.setting-capacity.md#aurora-serverless-v2.max-connections)\. For static parameters that rely on this type of calculation, the value is evaluated again when you reboot the DB instance\. Thus, you can update the value of such parameters by rebooting the DB instance after changing the capacity range\. To check whether you need to reboot your DB instance to pick up such parameter changes, check the `ParameterApplyStatus` attribute of the DB instance\. A value of `pending-reboot` indicates that rebooting will apply changes to some parameter values\. 

### Console<a name="aurora-serverless-v2.setting-capacity.console"></a>

 You can set the capacity range of a cluster that contains Aurora Serverless v2 DB instances with the AWS Management Console\. 

 When you use the console, you set the capacity range for the cluster at the time that you add the first Aurora Serverless v2 DB instance to that cluster\. You might do so when you choose the **Serverless v2** DB instance class for the writer DB instance when you create the cluster\. Or you might do so when you choose the **Serverless** DB instance class when you add an Aurora Serverless v2 reader DB instance to the cluster\. Or you might do so when you convert an existing provisioned DB instance in the cluster to the **Serverless** DB instance class\. For the full versions of those procedures, see [Creating an Aurora Serverless v2 writer](#aurora-serverless-v2-adding-writer), [Adding an Aurora Serverless v2 reader](#aurora-serverless-v2-adding-reader), and [Converting a provisioned writer or reader to Aurora Serverless v2](#aurora-serverless-v2-converting-from-provisioned) 

 Whatever capacity range that you set at the cluster level applies to all Aurora Serverless v2 DB instances in your cluster\. The following image shows a cluster with multiple Aurora Serverless v2 reader DB instances\. Each has an identical capacity range of 2–64 ACUs\. 

![\[Cluster with multiple Aurora Serverless v2 reader DB instances\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/serverless_v2_screencaps/serverless_v2_capacity_settings_identical_all_instances_in_tree_view.png)

**To modify the capacity range of an Aurora Serverless v2 cluster**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1.  In the navigation pane, choose **Databases**\. 

1.  Choose the cluster containing your Aurora Serverless v2 DB instances from the list\. The cluster must already contain at least one Aurora Serverless v2 DB instance\. Otherwise, Aurora doesn't show the **Capacity range** section\. 

1.  For **Actions**, choose **Modify**\. 

1.  In the **Capacity range** section, choose the following: 

   1.  Enter a value for **Minimum ACUs**\. The console shows the allowed range of values\. You can choose from 0\.5 ACUs minimum to 128 ACUs maximum, in increments of 0\.5 ACU\. 

   1.  Enter a value for **Maximum ACUs**\. This value must be greater than or equal to **Minimum ACUs**\. The console shows the allowed range of values\. The following screenshot shows that choice\.   
![\[Screenshot of modifying DB cluster capacity, changing capacity\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/serverless_v2_screencaps/serverless_v2_capacity_settings_modify_for_cluster.png)

1.  Choose **Continue**\. The **Summary of modifications** page appears\. 

1.  Choose whether to apply the capacity change immediately, or during the next scheduled maintenance window\. 

1.  Choose **Modify cluster** to accept the summary of modifications\. You can also choose **Back** to modify your changes or **Cancel** to discard your changes\. 

### AWS CLI<a name="aurora-serverless-v2.setting-capacity.cli"></a>

 To set the capacity of a cluster where you intend to use Aurora Serverless v2 DB instances using the AWS CLI, run the [modify\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) AWS CLI command\. Specify the `--serverless-v2-scaling-configuration` option\. The cluster might already contain one or more Aurora Serverless v2 DB instances, or you can add the DB instances later\. Valid values for the `MinCapacity` and `MaxCapacity` fields include the following: 
+  `0.5`, `1`, `1.5`, `2`, and so on, in steps of 0\.5, up to a maximum of 128\. 

 In this example, you set the ACU range of an Aurora DB cluster named `sample-cluster` to a minimum of `48.5` and a maximum of 64\.  

```
aws rds modify-db-cluster --db-cluster-identifier sample-cluster --serverless-v2-scaling-configuration MinCapacity=48.5,MaxCapacity=64
```

 After doing so, you can add Aurora Serverless v2 DB instances to the cluster and each new DB instance can scale between 48\.5 and 64 ACUs\. The new capacity range also applies to any Aurora Serverless v2 DB instances that were already in the cluster\. The DB instances scale up or down if necessary to fall within the new capacity range\. 

 For additional examples of setting the capacity range using the CLI, see [Choosing the Aurora Serverless v2 capacity range for an Aurora cluster](aurora-serverless-v2.setting-capacity.md#aurora-serverless-v2-examples-setting-capacity-range-for-cluster)\. 

 To modify the scaling configuration of an Aurora Serverless DB cluster using the AWS CLI, run the [modify\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) AWS CLI command\. Specify the `--serverless-v2-scaling-configuration` option to configure the minimum capacity and maximum capacity\. Valid capacity values include the following: 
+  Aurora MySQL: `0.5`, `1`, `1.5`, `2`, and so on, in increments of 0\.5 ACUs up to a maximum of `128`\. 
+  Aurora PostgreSQL: `0.5`, `1`, `1.5`, `2`, and so on, in increments of 0\.5 ACUs up to a maximum of `128`\. 

 In the following example, you modify the scaling configuration of an Aurora Serverless v2 DB instance named `sample-instance` that's part of a cluster named `sample-cluster`\. 

For Linux, macOS, or Unix:

```
aws rds modify-db-cluster --db-cluster-identifier sample-cluster \
--serverless-v2-scaling-configuration MinCapacity=8,MaxCapacity=64
```

For Windows:

```
aws rds modify-db-cluster --db-cluster-identifier sample-cluster ^
--serverless-v2-scaling-configuration MinCapacity=8,MaxCapacity=64
```

### RDS API<a name="aurora-serverless-v2.setting-capacity.api"></a>

 You can set the capacity of an Aurora DB instance with the [ModifyDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) API operation\. Specify the `ServerlessV2ScalingConfiguration` parameter\. Valid values for the `MinCapacity` and `MaxCapacity` fields include the following: 
+  `0.5`, `1`, `1.5`, `2`, and so on, in steps of 0\.5, up to a maximum of 128\. 

 You can modify the scaling configuration of a cluster containing Aurora Serverless v2 DB instances with the [ModifyDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) API operation\. Specify the `ServerlessV2ScalingConfiguration` parameter to configure the minimum capacity and the maximum capacity\. Valid capacity values include the following: 
+  Aurora MySQL: `0.5`, `1`, `1.5`, `2`, and so on, in increments of 0\.5 ACUs up to a maximum of `128`\. 
+  Aurora PostgreSQL: `0.5`, `1`, `1.5`, `2`, and so on, in increments of 0\.5 ACUs up to a maximum of `128`\. 

## Checking the capacity range for Aurora Serverless v2<a name="aurora-serverless-v2-checking-capacity"></a>

 The procedure to check the capacity range for your Aurora Serverless v2 cluster requires that you first set a capacity range\. If you haven't done so, follow the procedure in [Setting the Aurora Serverless v2 capacity range for a cluster](#aurora-serverless-v2-setting-acus)\. 

 Whatever capacity range you set at the cluster level applies to all Aurora Serverless v2 DB instances in your cluster\. The following image shows a cluster with multiple Aurora Serverless v2 DB instances\. Each has an identical capacity range\. 

![\[Cluster details for multiple Aurora Serverless v2 DB instances\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/serverless_v2_screencaps/serverless_v2_capacity_settings_identical_all_instances_in_tree_view.png)

 You can also view the details page for any Aurora Serverless v2 DB instance in the cluster\. DB instances' capacity range appears on the **Configuration** tab\. 

![\[Instance type section, part of DB instance configuration user interface\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/serverless_v2_screencaps/serverless_v2_capacity_settings_shown_for_serverless_instance.png)

 You can also see the current capacity range for the cluster on the **Modify** page for the cluster\. The following image shows how\. At that point, you can change the capacity range\. For all the ways that you can set or change the capacity range, see [Setting the Aurora Serverless v2 capacity range for a cluster](#aurora-serverless-v2-setting-acus)\. 

![\[User interface for Aurora Serverless v2 capacity settings\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/serverless_v2_screencaps/serverless_v2_capacity_settings_modify_for_cluster.png)

### Checking the current capacity range for an Aurora cluster<a name="aurora-serverless-v2-examples-checking-cluster-capacity-range"></a>

 You can check the capacity range that's configured for Aurora Serverless v2 DB instances in a cluster by examining the `ServerlessV2ScalingConfiguration` attribute for the cluster\. The following AWS CLI example shows a cluster with a minimum capacity of 0\.5 Aurora capacity units \(ACUs\) and a maximum capacity of 16 ACUs\. 

```
$ aws rds describe-db-clusters --db-cluster-identifier serverless-v2-64-acu-cluster \
  --query 'DBClusters[*].[ServerlessV2ScalingConfiguration]'
[
    [
        {
            "MinCapacity": 0.5,
            "MaxCapacity": 16.0
        }
    ]
]
```

## Creating an Aurora Serverless v2 writer<a name="aurora-serverless-v2-adding-writer"></a>

### Console<a name="aurora-serverless-v2-adding-writer.CON"></a>

 When you create a cluster using the AWS Management Console, you specify the properties of the writer DB instance at the same time\. To make the writer DB instance use Aurora Serverless v2, choose the **Serverless** DB instance class\. Then you set the capacity range for the cluster by specifying the minimum and maximum Aurora capacity unit \(ACU\) values\. These minimum and maximum values apply to each Aurora Serverless v2 DB instance in the cluster\. 

![\[User interface for instance configuration\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/serverless_v2_screencaps/serverless_v2_capacity_settings_settable_for_add_reader_modify_instance.png)

 If you don't create an Aurora Serverless v2 DB instance when you first create the cluster, you can add one or more Aurora Serverless v2 DB instances later\. To do so, follow the procedures in [Adding an Aurora Serverless v2 reader](#aurora-serverless-v2-adding-reader) and [Converting a provisioned writer or reader to Aurora Serverless v2](#aurora-serverless-v2-converting-from-provisioned)\. You specify the capacity range at the time that you add the first Aurora Serverless v2 DB instance to the cluster\. You can change the capacity range later by following the procedure in [Setting the Aurora Serverless v2 capacity range for a cluster](#aurora-serverless-v2-setting-acus)\. 

### CLI<a name="aurora-serverless-v2-adding-writer.CLI"></a>

When you create a Aurora Serverless v2 DB cluster using the AWS CLI, you explicitly add the writer DB instance using the [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) command\. Include the following parameter:
+ `--db-instance-class db.serverless`

The following example shows the creation of an Aurora Serverless v2 writer DB instance\.

```
aws rds create-db-instance \
    --db-cluster-identifier my-serverless-v2-cluster \
    --db-instance-identifier my-serverless-v2-instance \
    --db-instance-class db.serverless \
    --engine aurora-mysql
```

## Adding an Aurora Serverless v2 reader<a name="aurora-serverless-v2-adding-reader"></a>

 To add an Aurora Serverless v2 reader DB instance to your cluster, you follow the same general procedure as in [Adding Aurora Replicas to a DB cluster](aurora-replicas-adding.md)\. Choose the **Serverless v2** instance class for the new DB instance\. 

 If the reader DB instance is the first Aurora Serverless v2 DB instance in the cluster, you also choose the capacity range\. The following image shows the controls that you use to specify the minimum and maximum Aurora capacity units \(ACUs\)\. This setting applies to this reader DB instance and to any other Aurora Serverless v2 DB instances that you add to the cluster\. Each Aurora Serverless v2 DB instance can scale between the minimum and maximum ACU values\. 

![\[Instance configuration user interface\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/serverless_v2_screencaps/serverless_v2_capacity_settings_settable_for_add_reader_modify_instance.png)

 If you already added any Aurora Serverless v2 DB instances to the cluster, adding another Aurora Serverless v2 reader DB instance shows you the current capacity range\. The following image shows those read\-only controls\. 

![\[\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/serverless_v2_screencaps/serverless_v2_capacity_settings_fixed_for_add_reader_modify_instance.png)

 If you want to change the capacity range for the cluster, follow the procedure in [Setting the Aurora Serverless v2 capacity range for a cluster](#aurora-serverless-v2-setting-acus)\. 

 For clusters containing more than one reader DB instance, the failover priority of each Aurora Serverless v2 reader DB instance plays an important part in how that DB instance scales up and down\. You can't specify the priority when you initially create the cluster\. Keep this property in mind when you add a second reader or later DB instance to your cluster\. For more information, see [Choosing the promotion tier for an Aurora Serverless v2 reader](#aurora-serverless-v2-choosing-promotion-tier)\. 

 For other ways that you can see the current capacity range for a cluster, see [Checking the capacity range for Aurora Serverless v2](#aurora-serverless-v2-checking-capacity)\. 

## Converting a provisioned writer or reader to Aurora Serverless v2<a name="aurora-serverless-v2-converting-from-provisioned"></a>

 You can convert a provisioned DB instance to use Aurora Serverless v2\. To do so, you follow the procedure in [Modifying a DB instance in a DB cluster](Aurora.Modifying.md#Aurora.Modifying.Instance)\. The cluster must meet the requirements in [Requirements for Aurora Serverless v2](aurora-serverless-v2.requirements.md)\. For example, Aurora Serverless v2 DB instances require that the cluster be running certain minimum engine versions\. 

 Suppose that you are converting a running provisioned cluster to take advantage of Aurora Serverless v2\. In that case, you can minimize downtime by converting a DB instance to Aurora Serverless v2 as the first step in the switchover process\. For the full procedure, see [Switching from a provisioned cluster to Aurora Serverless v2](aurora-serverless-v2.upgrade.md#aurora-serverless-v2.switch-from-provisioned)\. 

 If the DB instance that you convert is the first Aurora Serverless v2 DB instance in the cluster, you choose the capacity range for the cluster as part of the **Modify** operation\. This capacity range applies to each Aurora Serverless v2 DB instance that you add to the cluster\. The following image shows the page where you specify the minimum and maximum Aurora capacity units \(ACUs\)\. 

![\[Instance configuration user interface\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/serverless_v2_screencaps/serverless_v2_capacity_settings_settable_for_add_reader_modify_instance.png)

 For details about the significance of the capacity range, see [Aurora Serverless v2 capacity](aurora-serverless-v2.how-it-works.md#aurora-serverless-v2.how-it-works.capacity)\. 

 If the cluster already contains one or more Aurora Serverless v2 DB instances, you see the existing capacity range during the **Modify** operation\. The following image shows an example of that information panel\. 

![\[Capacity range information panel\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/serverless_v2_screencaps/serverless_v2_capacity_settings_fixed_for_add_reader_modify_instance.png)

 If you want to change the capacity range for the cluster after you add more Aurora Serverless v2 DB instances, follow the procedure in [Setting the Aurora Serverless v2 capacity range for a cluster](#aurora-serverless-v2-setting-acus)\. 

## Converting an Aurora Serverless v2 writer or reader to provisioned<a name="aurora-serverless-v2-converting-to-provisioned"></a>

 You can convert an Aurora Serverless v2 DB instance to a provisioned DB instance\. To do so, you follow the procedure in [Modifying a DB instance in a DB cluster](Aurora.Modifying.md#Aurora.Modifying.Instance)\. Choose a DB instance class other than **Serverless**\. 

 You might convert an Aurora Serverless v2 DB instance to provisioned if it needs a larger capacity than is available with the maximum Aurora capacity units \(ACUs\) of an Aurora Serverless v2 DB instance\. For example, the largest db\.r5 and db\.r6g DB instance classes have a larger memory capacity than an Aurora Serverless v2 DB instance can scale to\. 

**Tip**  
 Some of the older DB instance classes such as db\.r3 and db\.t2 aren't available for the Aurora versions that you use with Aurora Serverless v2\. To see which DB instance classes you can use when converting an Aurora Serverless v2 DB instance to a provisioned one, see [Supported DB engines for DB instance classes](Concepts.DBInstanceClass.md#Concepts.DBInstanceClass.SupportAurora)\. 

 If you are converting the writer DB instance of your cluster from Aurora Serverless v2 to provisioned, you can follow the procedure in [Switching from a provisioned cluster to Aurora Serverless v2](aurora-serverless-v2.upgrade.md#aurora-serverless-v2.switch-from-provisioned) but in reverse\. Switch one of the reader DB instances in the cluster from Aurora Serverless v2 to provisioned\. Then perform a failover to make that provisioned DB instance into the writer\. 

 Any capacity range that you previously specified for the cluster remains in place, even if all Aurora Serverless v2 DB instances are removed from the cluster\. If you want to change the capacity range, you can modify the cluster, as explained in [Setting the Aurora Serverless v2 capacity range for a cluster](#aurora-serverless-v2-setting-acus)\. 

## Choosing the promotion tier for an Aurora Serverless v2 reader<a name="aurora-serverless-v2-choosing-promotion-tier"></a>

 For clusters containing multiple Aurora Serverless v2 DB instances or a mixture of provisioned and Aurora Serverless v2 DB instances, pay attention to the promotion tier setting for each Aurora Serverless v2 DB instance\. This setting controls more behavior for Aurora Serverless v2 DB instances than for provisioned DB instances\. 

 In the AWS Management Console, you specify this setting using the **Failover priority** choice under **Additional configuration** for the **Create database**, **Modify instance**, and **Add reader** pages\. You see this property for existing DB instances in the optional **Priority tier** column on the **Databases** page\. You can also see this property on the details page for a DB cluster or DB instance\. 

 For provisioned DB instances, the choice of tier 0–15 determines only the order in which Aurora chooses which reader DB instance to promote to the writer during a failover operation\. For Aurora Serverless v2 reader DB instances, the tier number also determines whether the DB instance scales up to match the capacity of the writer DB instance or scales independently based on its own workload\. Aurora Serverless v2 reader DB instances in tier 0 or 1 are kept at a minimum capacity at least as high as the writer DB instance\. That way, they are ready to take over from the writer DB instance in case of a failover\. If the writer DB instance is a provisioned DB instance, Aurora estimates the equivalent Aurora Serverless v2 capacity\. It uses that estimate as the minimum capacity for the Aurora Serverless v2 reader DB instance\. 

 Aurora Serverless v2 reader DB instances in tiers 2–15 don't have the same constraint on their minimum capacity\. When they are idle, they can scale down to the minimum Aurora capacity unit \(ACU\) value specified in the cluster's capacity range\. 

 The following Linux AWS CLI example shows how to examine the promotion tiers of all the DB instances in your cluster\. The final field includes a value of `True` for the writer DB instance and `False` for all the reader DB instances\. 

```
$ aws rds describe-db-clusters --db-cluster-identifier promotion-tier-demo \
  --query 'DBClusters[*].DBClusterMembers[*].[PromotionTier,DBInstanceIdentifier,IsClusterWriter]' \
  --output text

1   instance-192  True
1   tier-01-4840  False
10  tier-10-7425  False
15  tier-15-6694  False
```

 The following Linux AWS CLI example shows how to change the promotion tier of a specific DB instance in your cluster\. The commands first modify the DB instance with a new promotion tier\. Then they wait for the DB instance to become available again and confirm the new promotion tier for the DB instance\. 

```
$ aws rds modify-db-instance --db-instance-identifier instance-192 --promotion-tier 0
$ aws rds wait db-instance-available --db-instance-identifier instance-192
$ aws rds describe-db-instances --db-instance-identifier instance-192 \
  --query '*[].[PromotionTier]' --output text
0
```

 For more guidance about specifying promotion tiers for different use cases, see [Aurora Serverless v2 scaling](aurora-serverless-v2.how-it-works.md#aurora-serverless-v2.how-it-works.scaling)\.   

## Using TLS/SSL with Aurora Serverless v2<a name="aurora-serverless-v2.tls"></a>

 Aurora Serverless v2 can use the Transport Layer Security/Secure Sockets Layer \(TLS/SSL\) protocol to encrypt communications between clients and your Aurora Serverless v2 DB instances\. It supports TLS/SSL versions 1\.0, 1\.1, and 1\.2\. For general information about using TLS/SSL with Aurora, see [Using SSL/TLS with Aurora MySQL DB clusters](AuroraMySQL.Security.md#AuroraMySQL.Security.SSL)\. 

 To learn more about connecting to Aurora MySQL database with the MySQL client, see [Connecting to a DB instance running the MySQL database engine](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ConnectToInstance.html)\. 

 Aurora Serverless v2 supports all TLS/SSL modes available to the MySQL client \(`mysql`\) and PostgreSQL client \(`psql`\), including those listed in the following table\. 


|  Description of TLS/SSL mode  |  mysql  |  psql  | 
| --- | --- | --- | 
|   Connect without using TLS/SSL\.   |   DISABLED   |   disable   | 
|   Try the connection using TLS/SSL first, but fall back to non\-SSL if necessary\.   |   PREFERRED   |   prefer \(default\)   | 
|   Enforce using TLS/SSL\.   |   REQUIRED   |   require   | 
|   Enforce TLS/SSL and verify the certificate authority \(CA\)\.   |   VERIFY\_CA   |   verify\-ca   | 
|   Enforce TLS/SSL, verify the CA, and verify the CA hostname\.   |   VERIFY\_IDENTITY   |   verify\-full   | 

 Aurora Serverless v2 uses wildcard certificates\. If you specify the "verify CA" or the "verify CA and CA hostname" option when using TLS/SSL, first download the [Amazon root CA 1 trust store](https://www.amazontrust.com/repository/AmazonRootCA1.pem) from Amazon Trust Services\. After doing so, you can identify this PEM\-formatted file in your client command\. To do so using the PostgreSQL client, do the following\. 

For Linux, macOS, or Unix:

```
psql 'host=endpoint user=user sslmode=require sslrootcert=amazon-root-CA-1.pem dbname=db-name'
```

 To learn more about working with the Aurora PostgreSQL database using the Postgres client, see [Connecting to a DB instance running the PostgreSQL database engine](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ConnectToPostgreSQLInstance.html)\. 

 For more information about connecting to Aurora DB clusters in general, see [Connecting to an Amazon Aurora DB cluster](Aurora.Connecting.md)\. 

### Supported cipher suites for connections to Aurora Serverless v2 DB clusters<a name="aurora-serverless-v2.tls.cipher-suites"></a>

By using configurable cipher suites, you can have more control over the security of your database connections\. You can specify a list of cipher suites that you want to allow to secure client TLS/SSL connections to your database\. With configurable cipher suites, you can control the connection encryption that your database server accepts\. Doing this prevents the use of ciphers that aren't secure or that are no longer used\.

Aurora Serverless v2 DB clusters that are based on Aurora MySQL support the same cipher suites as Aurora MySQL provisioned DB clusters\. For information about these cipher suites, see [Configuring cipher suites for connections to Aurora MySQL DB clusters](AuroraMySQL.Security.md#AuroraMySQL.Security.SSL.ConfiguringCipherSuites)\.

Aurora Serverless v2 DB clusters that are based on Aurora PostgreSQL support the same cipher suites as Aurora PostgreSQL provisioned DB clusters\. For information about these cipher suites, see [Configuring cipher suites for connections to Aurora PostgreSQL DB clusters](AuroraPostgreSQL.Security.md#AuroraPostgreSQL.Security.SSL.ConfiguringCipherSuites)\.

## Viewing Aurora Serverless v2 writers and readers<a name="aurora-serverless-v2.viewing"></a>

 You can view the details of Aurora Serverless v2 DB instances in the same way that you do for provisioned DB instances\. To do so, follow the general procedure from [Viewing an Amazon Aurora DB cluster](accessing-monitoring.md#Aurora.Viewing)\. A cluster might contain all Aurora Serverless v2 DB instances, all provisioned DB instances, or some of each\. 

 After you create one or more Aurora Serverless v2 DB instances, you can view which DB instances are type **Serverless** and which are type **Instance**\. You can also view the minimum and maximum Aurora capacity units \(ACUs\) that the Aurora Serverless v2 DB instance can use\. Each ACU is a combination of processing \(CPU\) and memory \(RAM\) capacity\. This capacity range applies to each Aurora Serverless v2 DB instance in the cluster\. For the procedure to check the capacity range of a cluster or any Aurora Serverless v2 DB instance in the cluster, see [Checking the capacity range for Aurora Serverless v2](#aurora-serverless-v2-checking-capacity)\. 

 In the AWS Management Console, Aurora Serverless v2 DB instances are marked under the **Size** column in the **Databases** page\. Provisioned DB instances show the name of a DB instance class such as r6g\.xlarge\. The Aurora Serverless DB instances show **Serverless** for the DB instance class, along with the DB instance's minimum and maximum capacity\. For example, you might see **Serverless v2 \(4–64 ACUs\)** or **Serverless v2 \(1–40 ACUs\)**\. 

 You can find the same information on the **Configuration** tab for each Aurora Serverless v2 DB instance in the console\. For example, you might see an **Instance type** section such as the following\.  Here, the **Instance type** value is **Serverless v2**, the **Minimum capacity** value is **2 ACUs \(4 GiB\)**, and the **Maximum capacity** value is **64 ACUs \(128 GiB\)**\. 

![\[Instance type section, part of DB instance configuration user interface\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/serverless_v2_screencaps/serverless_v2_capacity_settings_shown_for_serverless_instance.png)

 You can monitor the capacity of each Aurora Serverless v2 DB instance over time\. That way, you can check the minimum, maximum, and average ACUs consumed by each DB instance\. You can also check how close the DB instance came to its minimum or maximum capacity\. To see such details in the AWS Management Console, examine the graphs of Amazon CloudWatch metrics on the **Monitoring** tab for the DB instance\. For information about the metrics to watch and how to interpret them, see [Important Amazon CloudWatch metrics for Aurora Serverless v2](aurora-serverless-v2.setting-capacity.md#aurora-serverless-v2.viewing.monitoring)\. 

## Logging for Aurora Serverless v2<a name="aurora-serverless-v2.logging"></a>

To turn on database logging, you specify the logs to enable using configuration parameters in your custom parameter group\.

For Aurora MySQL, you can enable the following logs\.


| Aurora MySQL | Description | 
| --- | --- | 
|  `general_log`  |  Creates the general log\. Set to 1 to turn on\. Default is off \(0\)\.  | 
|  `log_queries_not_using_indexes`  |  Logs any queries to the slow query log that don't use an index\. Default is off \(0\)\. Set to 1 to turn on this log\.  | 
|  `long_query_time`  |  Prevents fast\-running queries from being logged in the slow query log\. Can be set to a float between 0 and 31536000\. Default is 0 \(not active\)\.  | 
|  `server_audit_events`  |  The list of events to capture in the logs\. Supported values are `CONNECT`, `QUERY`, `QUERY_DCL`, `QUERY_DDL`, `QUERY_DML`, and `TABLE`\.  | 
|  `server_audit_logging`  |  Set to 1 to turn on server audit logging\. If you turn this on, you can specify the audit events to send to CloudWatch by listing them in the `server_audit_events` parameter\.  | 
|  `slow_query_log`  |  Creates a slow query log\. Set to 1 to turn on the slow query log\. Default is off \(0\)\.  | 

For more information, see [Using Advanced Auditing with an Amazon Aurora MySQL DB cluster](AuroraMySQL.Auditing.md)\.

For Aurora PostgreSQL, you can enable the following logs on your Aurora Serverless v2 DB instances\.


|  Aurora PostgreSQL  |  Description  | 
| --- | --- | 
|   `log_connections`   |  Logs each successful connection\.  | 
|   `log_disconnections`   |  Logs end of a session including duration\.  | 
|   `log_lock_waits`   |  Default is 0 \(off\)\. Set to 1 to log lock waits\.  | 
|   `log_min_duration_statement`   |  The minimum duration \(in milliseconds\) for a statement to run before it's logged\.  | 
|  `log_min_messages`  | Sets the message levels that are logged\. Supported values are debug5, debug4, debug3, debug2, debug1, info, notice, warning, error, log, fatal, panic\. To log performance data to the postgres log, set the value to debug1\. | 
|  `log_temp_files`  |  Logs the use of temporary files that are above the specified kilobytes \(kB\)\.  | 
|  `log_statement`  |  Controls the specific SQL statements that get logged\. Supported values are `none`, `ddl`, `mod`, and `all`\. Default is `none`\.  | 

**Topics**
+ [Logging with Amazon CloudWatch](#aurora-serverless-v2.how-it-works.logging)
+ [Viewing Aurora Serverless v2 logs in Amazon CloudWatch](#aurora-serverless-v2.logging.monitoring)
+ [Monitoring capacity with Amazon CloudWatch](#aurora-serverless-v2.how-it-works.logging.monitoring)

### Logging with Amazon CloudWatch<a name="aurora-serverless-v2.how-it-works.logging"></a>

 After you use the procedure in [Logging for Aurora Serverless v2](#aurora-serverless-v2.logging) to choose which database logs to turn on, you can choose which logs to upload \("publish"\) to Amazon CloudWatch\. 

You can use Amazon CloudWatch to analyze log data, create alarms, and view metrics\. By default, error logs for Aurora Serverless v2 are enabled and automatically uploaded to CloudWatch\. You can also upload other logs from Aurora Serverless v2 DB instances to CloudWatch\.

Then you choose which of those logs to upload to CloudWatch, by using the **Log exports** settings in the AWS Management Console or the `--enable-cloudwatch-logs-exports` option in the AWS CLI\.

You can choose which of your Aurora Serverless v2 logs to upload to CloudWatch\. For more information, see [Using Advanced Auditing with an Amazon Aurora MySQL DB cluster](AuroraMySQL.Auditing.md)\.

As with any type of Aurora DB cluster, you can't modify the default DB cluster parameter group\. Instead, create your own DB cluster parameter group based on a default parameter for your DB cluster and engine type\. We recommend that you create your custom DB cluster parameter group before creating your Aurora Serverless v2 DB cluster, so that it's available to choose when you create a database on the console\.

**Note**  
For Aurora Serverless v2, you can create both DB cluster and DB parameter groups\. This contrasts with Aurora Serverless v1, where you can only create DB cluster parameter groups\.

### Viewing Aurora Serverless v2 logs in Amazon CloudWatch<a name="aurora-serverless-v2.logging.monitoring"></a>

After you use the procedure in [Logging with Amazon CloudWatch](#aurora-serverless-v2.how-it-works.logging) to choose which database logs to turn on, you can view the contents of the logs\.

For more information on using CloudWatch with Aurora MySQL and Aurora PostgreSQL logs, see [Monitoring log events in Amazon CloudWatch](AuroraMySQL.Integrating.CloudWatch.md#AuroraMySQL.Integrating.CloudWatch.Monitor) and [Publishing Aurora PostgreSQL logs to Amazon CloudWatch Logs](AuroraPostgreSQL.CloudWatch.md)\.

**To view logs for your Aurora Serverless v2 DB cluster**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. Choose your AWS Region\.

1. Choose **Log groups**\.

1. Choose your Aurora Serverless v2 DB cluster log from the list\. The log naming pattern is as follows\.

   ```
   /aws/rds/cluster/cluster-name/log_type
   ```

**Note**  
For Aurora MySQL–compatible Aurora Serverless v2 DB clusters, the error log includes buffer pool scaling events even when there are no errors\.

### Monitoring capacity with Amazon CloudWatch<a name="aurora-serverless-v2.how-it-works.logging.monitoring"></a>

With Aurora Serverless v2, you can use CloudWatch to to monitor the capacity and utilization of all the Aurora Serverless v2 DB instances in your cluster\. You can view instance\-level metrics to check the capacity of each Aurora Serverless v2 DB instance as it scales up and down\. You can also compare the capacity\-related metrics to other metrics to see how changes in workloads affect resource consumption\. For example, you can compare `ServerlessDatabaseCapacity` to `DatabaseUsedMemory`, `DatabaseConnections`, and `DMLThroughput` to assess how your DB cluster is responding during operations\. For details about the capacity\-related metrics that apply to Aurora Serverless v2, see [Important Amazon CloudWatch metrics for Aurora Serverless v2](aurora-serverless-v2.setting-capacity.md#aurora-serverless-v2.viewing.monitoring)\.

**To monitor your Aurora Serverless v2 DB cluster's capacity**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. Choose **Metrics**\. All available metrics appear as cards in the console, grouped by service name\.

1. Choose **RDS**\.

1. \(Optional\) Use the **Search** box to find the metrics that are especially important for Aurora Serverless v2: `ServerlessDatabaseCapacity`, `ACUUtilization`, `CPUUtilization`, and `FreeableMemory`\.

We recommend that you set up a CloudWatch dashboard to monitor your Aurora Serverless v2 DB cluster capacity using the capacity\-related metrics\. To learn how, see [Building dashboards with CloudWatch](https://docs.aws.amazon.com/autoscaling/application/userguide/monitoring-cloudwatch.html)\. 

To learn more about using Amazon CloudWatch with Amazon Aurora, see [Publishing Amazon Aurora MySQL logs to Amazon CloudWatch Logs](AuroraMySQL.Integrating.CloudWatch.md)\.