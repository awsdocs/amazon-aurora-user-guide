# Working with DB Parameter Groups and DB Cluster Parameter Groups<a name="USER_WorkingWithParamGroups"></a>

 You manage your DB engine configuration by associating your DB instances and Aurora DB clusters with parameter groups\. Amazon RDS defines parameter groups with default settings that apply to newly created DB instances and Aurora DB clusters\. 

**Important**  
You can define your own parameter groups with customized settings\. Then you can modify your DB instances and Aurora clusters to use your own parameter groups\.  
For information about modifying a DB cluster or DB instance, see [Modifying an Amazon Aurora DB Cluster](Aurora.Modifying.md)\.

 A *DB parameter group* acts as a container for engine configuration values that are applied to one or more DB instances\. DB parameter groups apply to DB instances in both Amazon RDS and Aurora\. These configuration settings apply to properties that can vary among the DB instances within an Aurora cluster, such as the sizes for memory buffers\. 

 A *DB cluster parameter group* acts as a container for engine configuration values that are applied to every DB instance in an Aurora DB cluster\. For example, the Aurora shared storage model requires that every DB instance in an Aurora cluster use the same setting for parameters such as `innodb_file_per_table`\. Thus, parameters that affect the physical storage layout are part of the cluster parameter group\. The DB cluster parameter group also includes default values for all the instance\-level parameters 

 If you create a DB instance without specifying a DB parameter group, the DB instance uses a default DB parameter group\. Likewise, if you create an Aurora DB cluster without specifying a DB cluster parameter group, the DB cluster uses a default DB cluster parameter group\. Each default parameter group contains database engine defaults and Amazon RDS system defaults based on the engine, compute class, and allocated storage of the instance\. You can't modify the parameter settings of a default parameter group\. Instead, you create your own parameter group where you choose your own parameter settings\. Not all DB engine parameters can be changed in a parameter group that you create\. 

 If you want to use your own parameter group, you create a new parameter group and modify the parameters that you want to\. You then modify your DB instance or DB cluster to use the new parameter group\. If you update parameters within a DB parameter group, the changes apply to all DB instances that are associated with that parameter group\. Likewise, if you update parameters within a DB cluster parameter group, the changes apply to all Aurora clusters that are associated with that DB cluster parameter group\. 

 You can copy an existing DB parameter group with the AWS CLI [copy\-db\-parameter\-group](https://docs.aws.amazon.com/cli/latest/reference/rds/copy-db-parameter-group.html) command\. You can copy an existing DB cluster parameter group with the AWS CLI [copy\-db\-cluster\-parameter\-group](https://docs.aws.amazon.com/cli/latest/reference/rds/copy-db-cluster-parameter-group.html) command\. Copying a parameter group can be convenient when you want to include most of an existing parameter group's custom parameters and values in a new parameter group\. 

Here are some important points about working with parameters in a parameter group:
+ When you change a dynamic parameter and save the parameter group, the change is applied immediately regardless of the **Apply Immediately** setting\. When you change a static parameter and save the DB parameter group, the parameter change takes effect after you manually reboot the DB instance\. You can reboot a DB instance using the RDS console or by explicitly calling the `RebootDbInstance` API operation \(without failover, if the DB instance is in a Multi\-AZ deployment\)\. The requirement to reboot the associated DB instance after a static parameter change helps mitigate the risk of a parameter misconfiguration affecting an API call, such as calling `ModifyDBInstance` to change DB instance class or scale storage\.

  If a DB instance isn't using the latest changes to its associated DB parameter group, the AWS Management Console shows the DB parameter group with a status of **pending\-reboot**\. The **pending\-reboot** parameter groups status doesn't result in an automatic reboot during the next maintenance window\. To apply the latest parameter changes to that DB instance, manually reboot the DB instance\.
+ When you change the DB parameter group associated with a DB instance, you must manually reboot the instance before the DB instance can use the new DB parameter group\. For more information about changing the DB parameter group, see [Modifying an Amazon Aurora DB Cluster](Aurora.Modifying.md)\.
**Note**  
After you change the DB cluster parameter group associated with a DB cluster, reboot the primary DB instance in the cluster to apply the changes to all of the DB instances in the cluster\.
+ You can specify the value for a parameter as an integer or as an integer expression built from formulas, variables, functions, and operators\. Functions can include a mathematical log expression\. For more information, see [DB Parameter Values](#USER_ParamValuesRef)\.
+ Set any parameters that relate to the character set or collation of your database in your parameter group before creating the DB instance and before you create a database in your DB instance\. This ensures that the default database and new databases in your DB instance use the character set and collation values that you specify\. If you change character set or collation parameters for your DB instance, the parameter changes are not applied to existing databases\.

  You can change character set or collation values for an existing database using the `ALTER DATABASE` command, for example:

  ```
  ALTER DATABASE database_name CHARACTER SET character_set_name COLLATE collation;
  ```
+ Improperly setting parameters in a parameter group can have unintended adverse effects, including degraded performance and system instability\. Always exercise caution when modifying database parameters and back up your data before modifying a parameter group\. Try out parameter group setting changes on a test DB instance before applying those parameter group changes to a production DB instance\.
+  For an Aurora global database, you can specify different configuration settings for the individual Aurora clusters\. Make sure that the settings are similar enough to produce consistent behavior if you promote a secondary cluster to be the primary cluster\. For example, use the same settings for time zones and character sets across all the clusters of an Aurora global database\. 
+ To determine the supported parameters for your DB engine, you can view the parameters in the DB parameter group  and DB cluster parameter group used by the DB cluster\. For more information, see [Viewing Parameter Values for a DB Parameter Group](#USER_WorkingWithParamGroups.Viewing) and [Viewing Parameter Values for a DB Cluster Parameter Group](#USER_WorkingWithParamGroups.ViewingCluster)\.

**Topics**
+ [Amazon Aurora DB Cluster and DB Instance Parameters](#Aurora.Managing.ParameterGroups)
+ [Creating a DB Parameter Group](#USER_WorkingWithParamGroups.Creating)
+ [Creating a DB Cluster Parameter Group](#USER_WorkingWithParamGroups.CreatingCluster)
+ [Modifying Parameters in a DB Parameter Group](#USER_WorkingWithParamGroups.Modifying)
+ [Modifying Parameters in a DB Cluster Parameter Group](#USER_WorkingWithParamGroups.ModifyingCluster)
+ [Copying a DB Parameter Group](#USER_WorkingWithParamGroups.Copying)
+ [Copying a DB Cluster Parameter Group](#USER_WorkingWithParamGroups.CopyingCluster)
+ [Listing DB Parameter Groups](#USER_WorkingWithParamGroups.Listing)
+ [Listing DB Cluster Parameter Groups](#USER_WorkingWithParamGroups.ListingCluster)
+ [Viewing Parameter Values for a DB Parameter Group](#USER_WorkingWithParamGroups.Viewing)
+ [Viewing Parameter Values for a DB Cluster Parameter Group](#USER_WorkingWithParamGroups.ViewingCluster)
+ [Comparing Parameter Groups](#USER_WorkingWithParamGroups.Comparing)
+ [DB Parameter Values](#USER_ParamValuesRef)

## Amazon Aurora DB Cluster and DB Instance Parameters<a name="Aurora.Managing.ParameterGroups"></a>

 Aurora uses a two\-level system of configuration settings, as follows: 
+  Parameters in a *DB cluster parameter group* apply to every DB instance in a DB cluster\. Your data is stored in the Aurora shared storage subsystem\. Because of this, all parameters related to physical layout of table data must be the same for all DB instances in an Aurora cluster\. Likewise, because Aurora DB instances are connected by replication, all the parameters for replication settings must be identical throughout an Aurora cluster\. 
+  Parameters in a *DB parameter group* apply to a single DB instance in an Aurora DB cluster\. These parameters are related to aspects such as memory usage that you can vary across DB instances in the same Aurora cluster\. For example, a cluster often contains DB instances with different AWS instance classes\. 

 Every Aurora cluster is associated with a DB cluster parameter group\. Each DB instance within the cluster inherits the settings from that DB cluster parameter group, and is associated with a DB parameter group\. Aurora assigns default parameter groups when you create a cluster or a new DB instance, based on the specified database engine and version\. You can change the parameter groups later to ones that you create, where you can edit the parameter values\. 

 The DB cluster parameter groups also include default values for all the instance\-level parameters from the DB parameter group\. These defaults are mainly intended for configuring Aurora Serverless clusters, which are only associated with DB cluster parameter groups, not DB parameter groups\. You can modify the instance\-level parameter settings in the DB cluster parameter group\. Then, Aurora applies those settings to each new DB instance that's added to a Serverless cluster\. To learn more about configuration settings for Aurora Serverless clusters and which settings you can modify, see [Aurora Serverless and Parameter Groups](aurora-serverless.how-it-works.md#aurora-serverless.parameter-groups)\. 

 For non\-Serverless clusters, any configuration values that you modify in the DB cluster parameter group override default values in the DB parameter group\. If you edit the corresponding values in the DB parameter group, those values override the settings from the DB cluster parameter group\. 

 Any DB parameter settings that you modify take precedence over the DB cluster parameter group values, even if you change the configuration parameters back to their default values\. You can see which parameters are overridden by using the [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-parameters.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-parameters.html) AWS CLI command or the [DescribeDBParameters](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBParameters.html) RDS API\. The `Source` field contains the value `user` if you modified that parameter\. To reset one or more parameters so that the value from the DB cluster parameter group takes precedence, use the [https://docs.aws.amazon.com/cli/latest/reference/rds/reset-db-parameter-group.html](https://docs.aws.amazon.com/cli/latest/reference/rds/reset-db-parameter-group.html) AWS CLI command or the [ResetDBParameterGroup](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ResetDBParameterGroup.html) RDS API operation\. 

The DB cluster and DB instance parameters available to you in Aurora vary depending on database engine compatibility\.


| Database Engine | Parameters | 
| --- | --- | 
|  Aurora MySQL  |  See [Aurora MySQL Parameters](AuroraMySQL.Reference.md#AuroraMySQL.Reference.ParameterGroups)\.  For Aurora Serverless clusters, see additional details in [Aurora Serverless and Parameter Groups](aurora-serverless.how-it-works.md#aurora-serverless.parameter-groups)\.   | 
|  Aurora PostgreSQL  |  See [Amazon Aurora PostgreSQL Parameters](AuroraPostgreSQL.Reference.md#AuroraPostgreSQL.Reference.ParameterGroups)\.  | 

## Creating a DB Parameter Group<a name="USER_WorkingWithParamGroups.Creating"></a>

You can create a new DB parameter group using the AWS Management Console, the AWS CLI, or the RDS API\.

### Console<a name="USER_WorkingWithParamGroups.Creating.CON"></a>

**To create a DB parameter group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

1. Choose **Create parameter group**\.

   The **Create parameter group** window appears\.

1. In the **Parameter group family** list, select a DB parameter group family\.

1. In the **Type** list, select **DB Parameter Group**\.

1. In the **Group name** box, enter the name of the new DB parameter group\.

1. In the **Description** box, enter a description for the new DB parameter group\. 

1. Choose **Create**\.

### AWS CLI<a name="USER_WorkingWithParamGroups.Creating.CLI"></a>

To create a DB parameter group, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-parameter-group.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-parameter-group.html) command\. The following example creates a DB parameter group named *mydbparametergroup* for MySQL version 5\.6 with a description of "*My new parameter group*\."

Include the following required parameters:
+ `--db-parameter-group-name`
+ `--db-parameter-group-family`
+ `--description`

To list all of the available parameter group families, use the following command:

```
aws rds describe-db-engine-versions --query "DBEngineVersions[].DBParameterGroupFamily"				
```

**Note**  
The output contains duplicates\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds create-db-parameter-group \
    --db-parameter-group-name mydbparametergroup \
    --db-parameter-group-family aurora5.6 \
    --description "My new parameter group"
```
For Windows:  

```
aws rds create-db-parameter-group ^
    --db-parameter-group-name mydbparametergroup ^
    --db-parameter-group-family aurora5.6 ^
    --description "My new parameter group"
```
This command produces output similar to the following:  

```
DBPARAMETERGROUP  mydbparametergroup  aurora5.6  My new parameter group					
```

### RDS API<a name="USER_WorkingWithParamGroups.Creating.API"></a>

To create a DB parameter group, use the RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBParameterGroup.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBParameterGroup.html) operation\.

Include the following required parameters:
+ `DBParameterGroupName`
+ `DBParameterGroupFamily`
+ `Description`

## Creating a DB Cluster Parameter Group<a name="USER_WorkingWithParamGroups.CreatingCluster"></a>

You can create a new DB cluster parameter group using the AWS Management Console, the AWS CLI, or the RDS API\.

### Console<a name="USER_WorkingWithParamGroups.CreatingCluster.CON"></a>

**To create a DB cluster parameter group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

1. Choose **Create parameter group**\.

   The **Create parameter group** window appears\.

1. In the **Parameter group family** list, select a DB parameter group family 

1. In the **Type** list, select **DB Cluster Parameter Group**\.

1. In the **Group name** box, enter the name of the new DB cluster parameter group\.

1. In the **Description** box, enter a description for the new DB cluster parameter group\.

1. Choose **Create**\.

### AWS CLI<a name="USER_WorkingWithParamGroups.CreatingCluster.CLI"></a>

To create a DB cluster parameter group, use the AWS CLI [ `create-db-cluster-parameter-group`](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster-parameter-group.html) command\. The following example creates a DB cluster parameter group named *mydbclusterparametergroup* for MySQL version 5\.6 with a description of "*My new cluster parameter group*\."

Include the following required parameters:
+ `--db-cluster-parameter-group-name`
+ `--db-parameter-group-family`
+ `--description`

To list all of the available parameter group families, use the following command:

```
aws rds describe-db-engine-versions --query "DBEngineVersions[].DBParameterGroupFamily"
```

**Note**  
The output contains duplicates\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds create-db-cluster-parameter-group \
    --db-cluster-parameter-group-name mydbclusterparametergroup \
    --db-parameter-group-family aurora5.6 \
    --description "My new cluster parameter group"
```
For Windows:  

```
aws rds create-db-cluster-parameter-group ^
    --db-cluster-parameter-group-name mydbclusterparametergroup ^
    --db-parameter-group-family aurora5.6 ^
    --description "My new cluster parameter group"
```
This command produces output similar to the following:  

```
DBCLUSTERPARAMETERGROUP  mydbclusterparametergroup  mysql5.6  My cluster new parameter group  
```

### RDS API<a name="USER_WorkingWithParamGroups.CreatingCluster.API"></a>

To create a DB cluster parameter group, use the RDS API [ `CreateDBClusterParameterGroup`](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBClusterParameterGroup.html) action\.

Include the following required parameters:
+ `DBClusterParameterGroupName`
+ `DBParameterGroupFamily`
+ `Description`

## Modifying Parameters in a DB Parameter Group<a name="USER_WorkingWithParamGroups.Modifying"></a>

You can modify parameter values in a customer\-created DB parameter group; you can't change the parameter values in a default DB parameter group\. Changes to parameters in a customer\-created DB parameter group are applied to all DB instances that are associated with the DB parameter group\. 

Changes to some parameters are applied to the DB instance immediately without a reboot\. Changes to other parameters are applied only after the DB instance is rebooted\. The RDS console shows the status of the DB parameter group associated with a DB instance on the **Configuration** tab\. For example, if the DB instance isn't using the latest changes to its associated DB parameter group, the RDS console shows the DB parameter group with a status of **pending\-reboot**\. To apply the latest parameter changes to that DB instance, manually reboot the DB instance\.

![\[Parameter change pending reboot scenario\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/db-cluster-instance-param-group.png)

### Console<a name="USER_WorkingWithParamGroups.Modifying.CON"></a>

**To modify a DB parameter group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

1. In the list, choose the parameter group that you want to modify\.

1. For **Parameter group actions**, choose **Edit**\.

1. Change the values of the parameters that you want to modify\. You can scroll through the parameters using the arrow keys at the top right of the dialog box\. 

   You can't change values in a default parameter group\.

1. Choose **Save changes**\.

### AWS CLI<a name="USER_WorkingWithParamGroups.Modifying.CLI"></a>

To modify a DB parameter group, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-parameter-group.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-parameter-group.html) command with the following required parameters:
+ `--db-parameter-group-name`
+ `--parameters`

The following example modifies the` max_connections` and `max_allowed_packet` values in the DB parameter group named *mydbparametergroup*\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds modify-db-parameter-group \
    --db-parameter-group-name mydbparametergroup \
    --parameters "ParameterName=max_connections,ParameterValue=250,ApplyMethod=immediate" \
                 "ParameterName=max_allowed_packet,ParameterValue=1024,ApplyMethod=immediate"
```
For Windows:  

```
aws rds modify-db-parameter-group ^
    --db-parameter-group-name mydbparametergroup ^
    --parameters "ParameterName=max_connections,ParameterValue=250,ApplyMethod=immediate" ^
                 "ParameterName=max_allowed_packet,ParameterValue=1024,ApplyMethod=immediate"
```
The command produces output like the following:  

```
DBPARAMETERGROUP  mydbparametergroup
```

### RDS API<a name="USER_WorkingWithParamGroups.Modifying.API"></a>

To modify a DB parameter group, use the RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBParameterGroup.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBParameterGroup.html) command with the following required parameters:
+ `DBParameterGroupName`
+ `Parameters`

## Modifying Parameters in a DB Cluster Parameter Group<a name="USER_WorkingWithParamGroups.ModifyingCluster"></a>

You can modify parameter values in a customer\-created DB cluster parameter group\. You can't change the parameter values in a default DB cluster parameter group\. Changes to parameters in a customer\-created DB cluster parameter group are applied to all DB clusters that are associated with the DB cluster parameter group\.

### Console<a name="USER_WorkingWithParamGroups.ModifyingCluster.CON"></a>

**To modify a DB cluster parameter group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

1. In the list, choose the parameter group that you want to modify\.

1. For **Parameter group actions**, choose **Edit**\.

1. Change the values of the parameters you want to modify\. You can scroll through the parameters using the arrow keys at the top right of the dialog box\. 

   You can't change values in a default parameter group\.

1. Choose **Save changes**\.

1. Reboot the primary DB instance in the cluster to apply the changes to all of the DB instances in the cluster\.

### AWS CLI<a name="USER_WorkingWithParamGroups.ModifyingCluster.CLI"></a>

To modify a DB cluster parameter group, use the AWS CLI [ `modify-db-cluster-parameter-group`](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster-parameter-group.html) command with the following required parameters:
+ `--db-cluster-parameter-group-name`
+ `--parameters`

The following example modifies the `server_audit_logging` and `server_audit_logs_upload` values in the DB cluster parameter group named *mydbclusterparametergroup*\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds modify-db-cluster-parameter-group \
    --db-cluster-parameter-group-name mydbclusterparametergroup \
    --parameters "ParameterName=server_audit_logging,ParameterValue=1,ApplyMethod=immediate" \
                 "ParameterName=server_audit_logs_upload,ParameterValue=1,ApplyMethod=immediate"
```
For Windows:  

```
aws rds modify-db-cluster-parameter-group ^
    --db-cluster-parameter-group-name mydbclusterparametergroup ^
    --parameters "ParameterName=server_audit_logging,ParameterValue=1,ApplyMethod=immediate" ^
                 "ParameterName=server_audit_logs_upload,ParameterValue=1,ApplyMethod=immediate"
```
The command produces output like the following:  

```
DBCLUSTERPARAMETERGROUP  mydbclusterparametergroup
```

### RDS API<a name="USER_WorkingWithParamGroups.ModifyingCluster.API"></a>

To modify a DB cluster parameter group, use the RDS API [ `ModifyDBClusterParameterGroup`](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBClusterParameterGroup.html) command with the following required parameters:
+ `DBClusterParameterGroupName`
+ `Parameters`

## Copying a DB Parameter Group<a name="USER_WorkingWithParamGroups.Copying"></a>

You can copy custom DB parameter groups that you create\. Copying a parameter group is a convenient solution when you have already created a DB parameter group and you want to include most of the custom parameters and values from that group in a new DB parameter group\. You can copy a DB parameter group by using the AWS CLI [copy\-db\-parameter\-group](https://docs.aws.amazon.com/cli/latest/reference/rds/copy-db-parameter-group.html) command or the RDS API [CopyDBParameterGroup](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CopyDBParameterGroup.html) operation\.

After you copy a DB parameter group, wait at least 5 minutes before creating your first DB instance that uses that DB parameter group as the default parameter group\. Doing this allows Amazon RDS to fully complete the copy action before the parameter group is used\. This is especially important for parameters that are critical when creating the default database for a DB instance\. An example is the character set for the default database defined by the `character_set_database` parameter\. Use the **Parameter Groups** option of the [Amazon RDS console](https://console.aws.amazon.com/rds/) or the [describe\-db\-parameters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-parameters.html) command to verify that your DB parameter group is created\.

**Note**  
You can't copy a default parameter group\. However, you can create a new parameter group that is based on a default parameter group\.

### Console<a name="USER_WorkingWithParamGroups.Copying.CON"></a>

**To copy a DB parameter group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

1. In the list, choose the custom parameter group that you want to copy\.

1. For **Parameter group actions**, choose **Copy**\.

1. In **New DB parameter group identifier**, enter a name for the new parameter group\.

1. In **Description**, enter a description for the new parameter group\.

1. Choose **Copy**\.

### AWS CLI<a name="USER_WorkingWithParamGroups.Copying.CLI"></a>

To copy a DB parameter group, use the AWS CLI [ `copy-db-parameter-group`](https://docs.aws.amazon.com/cli/latest/reference/rds/copy-db-parameter-group.html) command with the following required parameters:
+ `--source-db-parameter-group-identifier`
+ `--target-db-parameter-group-identifier`
+ `--target-db-parameter-group-description`

The following example creates a new DB parameter group named `mygroup2` that is a copy of the DB parameter group `mygroup1`\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds copy-db-parameter-group \
    --source-db-parameter-group-identifier mygroup1 \
    --target-db-parameter-group-identifier mygroup2 \
    --target-db-parameter-group-description "DB parameter group 2"
```
For Windows:  

```
aws rds copy-db-parameter-group ^
    --source-db-parameter-group-identifier mygroup1 ^
    --target-db-parameter-group-identifier mygroup2 ^
    --target-db-parameter-group-description "DB parameter group 2"
```

### RDS API<a name="USER_WorkingWithParamGroups.Copying.API"></a>

To copy a DB parameter group, use the RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CopyDBParameterGroup.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CopyDBParameterGroup.html) operation with the following required parameters:
+ `SourceDBParameterGroupIdentifier`
+ `TargetDBParameterGroupIdentifier`
+ `TargetDBParameterGroupDescription`

## Copying a DB Cluster Parameter Group<a name="USER_WorkingWithParamGroups.CopyingCluster"></a>

You can copy custom DB cluster parameter groups that you create\. Copying a parameter group is a convenient solution when you have already created a DB cluster parameter group and you want to include most of the custom parameters and values from that group in a new DB cluster parameter group\. You can copy a DB cluster parameter group by using the AWS CLI [copy\-db\-cluster\-parameter\-group](https://docs.aws.amazon.com/cli/latest/reference/rds/copy-db-cluster-parameter-group.html) command or the RDS API [CopyDBClusterParameterGroup](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CopyDBParameterGroup.html) operation\.

After you copy a DB cluster parameter group, wait at least 5 minutes before creating your first DB cluster that uses that DB cluster parameter group as the default parameter group\. Doing this allows Amazon RDS to fully complete the copy action before the parameter group is used as the default for a new DB cluster\. You can use the **Parameter Groups** option of the [Amazon RDS console](https://console.aws.amazon.com/rds/) or the [describe\-db\-cluster\-parameters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-cluster-parameters.html) command to verify that your DB cluster parameter group is created\.

**Note**  
You can't copy a default parameter group\. However, you can create a new parameter group that is based on a default parameter group\.

### Console<a name="USER_WorkingWithParamGroups.CopyingCluster.CON"></a>

**To copy a DB cluster parameter group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

1. In the list, choose the custom parameter group that you want to copy\.

1. For **Parameter group actions**, choose **Copy**\.

1. In **New DB parameter group identifier**, enter a name for the new parameter group\.

1. In **Description**, enter a description for the new parameter group\.

1. Choose **Copy**\.

### AWS CLI<a name="USER_WorkingWithParamGroups.CopyingCluster.CLI"></a>

To copy a DB cluster parameter group, use the AWS CLI [ `copy-db-cluster-parameter-group`](https://docs.aws.amazon.com/cli/latest/reference/rds/copy-db-cluster-parameter-group.html) command with the following required parameters:
+ `--source-db-cluster-parameter-group-identifier`
+ `--target-db-cluster-parameter-group-identifier`
+ `--target-db-cluster-parameter-group-description`

The following example creates a new DB cluster parameter group named `mygroup2` that is a copy of the DB cluster parameter group `mygroup1`\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds copy-db-cluster-parameter-group \
    --source-db-cluster-parameter-group-identifier mygroup1 \
    --target-db-cluster-parameter-group-identifier mygroup2 \
    --target-db-cluster-parameter-group-description "DB parameter group 2"
```
For Windows:  

```
aws rds copy-db-cluster-parameter-group ^
    --source-db-cluster-parameter-group-identifier mygroup1 ^
    --target-db-cluster-parameter-group-identifier mygroup2 ^
    --target-db-cluster-parameter-group-description "DB parameter group 2"
```

### RDS API<a name="USER_WorkingWithParamGroups.Copying.API"></a>

To copy a DB cluster parameter group, use the RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CopyDBClusterParameterGroup.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CopyDBClusterParameterGroup.html) operation with the following required parameters:
+ `SourceDBClusterParameterGroupIdentifier`
+ `TargetDBClusterParameterGroupIdentifier`
+ `TargetDBClusterParameterGroupDescription`

## Listing DB Parameter Groups<a name="USER_WorkingWithParamGroups.Listing"></a>

You can list the DB parameter groups you've created for your AWS account\.

**Note**  
Default parameter groups are automatically created from a default parameter template when you create a DB instance for a particular DB engine and version\. These default parameter groups contain preferred parameter settings and can't be modified\. When you create a custom parameter group, you can modify parameter settings\. 

### Console<a name="USER_WorkingWithParamGroups.Listing.CON"></a>

**To list all DB parameter groups for an AWS account**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

   The DB parameter groups appear in a list\.

### AWS CLI<a name="USER_WorkingWithParamGroups.Listing.CLI"></a>

To list all DB parameter groups for an AWS account, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-parameter-groups.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-parameter-groups.html) command\.

**Example**  
The following example lists all available DB parameter groups for an AWS account\.  

```
aws rds describe-db-parameter-groups
```
The command returns a response like the following:  

```
DBPARAMETERGROUP  default.mysql5.5     mysql5.5  Default parameter group for MySQL5.5
DBPARAMETERGROUP  default.mysql5.6     mysql5.6  Default parameter group for MySQL5.6
DBPARAMETERGROUP  mydbparametergroup   mysql5.6  My new parameter group
```
The following example describes the *mydbparamgroup1* parameter group\.  
For Linux, macOS, or Unix:  

```
aws rds describe-db-parameter-groups \
    --db-parameter-group-name mydbparamgroup1
```
For Windows:  

```
aws rds describe-db-parameter-groups ^
    --db-parameter-group-name mydbparamgroup1
```
The command returns a response like the following:  

```
DBPARAMETERGROUP  mydbparametergroup1  mysql5.5  My new parameter group
```

### RDS API<a name="USER_WorkingWithParamGroups.Listing.API"></a>

To list all DB parameter groups for an AWS account, use the RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBParameterGroups.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBParameterGroups.html) operation\.

## Listing DB Cluster Parameter Groups<a name="USER_WorkingWithParamGroups.ListingCluster"></a>

You can list the DB cluster parameter groups you've created for your AWS account\.

**Note**  
Default parameter groups are automatically created from a default parameter template when you create a DB cluster for a particular DB engine and version\. These default parameter groups contain preferred parameter settings and can't be modified\. When you create a custom parameter group, you can modify parameter settings\. 

### Console<a name="USER_WorkingWithParamGroups.ListingCluster.CON"></a>

**To list all DB cluster parameter groups for an AWS account**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

   The DB cluster parameter groups appear in the list with **DB cluster parameter group** for **Type**\.

### AWS CLI<a name="USER_WorkingWithParamGroups.ListingCluster.CLI"></a>

To list all DB cluster parameter groups for an AWS account, use the AWS CLI [ `describe-db-cluster-parameter-groups`](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusterparameter-groups.html) command\.

**Example**  
The following example lists all available DB cluster parameter groups for an AWS account\.  

```
aws rds describe-db-cluster-parameter-groups
```
The command returns a response like the following:  

```
DBCLUSTERPARAMETERGROUPS        arn:aws:rds:us-west-2:1234567890:cluster-pg:default.aurora5.6 default.aurora5.6       aurora5.6       Default cluster parameter group for aurora5.6
DBCLUSTERPARAMETERGROUPS        arn:aws:rds:us-west-2:1234567890:cluster-pg:mydbclusterparametergroup mydbclusterparametergroup       aurora5.6       My new cluster parameter group
```
The following example describes the *mydbclusterparametergroup* parameter group\.  
For Linux, macOS, or Unix:  

```
aws rds describe-db-cluster-parameter-groups \
    --db-cluster-parameter-group-name mydbclusterparametergroup
```
For Windows:  

```
aws rds describe-db-cluster-parameter-groups ^
    --db-cluster-parameter-group-name mydbclusterparametergroup
```
The command returns a response like the following:  

```
DBCLUSTERPARAMETERGROUPS        arn:aws:rds:us-west-2:1234567890:cluster-pg:mydbclusterparametergroup mydbclusterparametergroup       aurora5.6       My new cluster parameter group
```

### RDS API<a name="USER_WorkingWithParamGroups.ListingCluster.API"></a>

To list all DB cluster parameter groups for an AWS account, use the RDS API [ `DescribeDBClusterParameterGroups`](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBClusterParameterGroups.html) action\.

## Viewing Parameter Values for a DB Parameter Group<a name="USER_WorkingWithParamGroups.Viewing"></a>

You can get a list of all parameters in a DB parameter group and their values\.

### Console<a name="USER_WorkingWithParamGroups.Viewing.CON"></a>

**To view the parameter values for a DB parameter group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

   The DB parameter groups appear in a list\.

1. Choose the name of the parameter group to see its list of parameters\.

### AWS CLI<a name="USER_WorkingWithParamGroups.Viewing.CLI"></a>

To view the parameter values for a DB parameter group, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-parameters.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-parameters.html) command with the following required parameter\.
+ `--db-parameter-group-name`

**Example**  
The following example lists the parameters and parameter values for a DB parameter group named *mydbparametergroup\.*  

```
aws rds describe-db-parameters --db-parameter-group-name mydbparametergroup
```
The command returns a response like the following:  

```
DBPARAMETER  Parameter Name            Parameter Value  Source           Data Type  Apply Type  Is Modifiable
DBPARAMETER  allow-suspicious-udfs                      engine-default   boolean    static      false
DBPARAMETER  auto_increment_increment                   engine-default   integer    dynamic     true
DBPARAMETER  auto_increment_offset                      engine-default   integer    dynamic     true
DBPARAMETER  binlog_cache_size         32768            system           integer    dynamic     true
DBPARAMETER  socket                    /tmp/mysql.sock  system           string     static      false
```

### RDS API<a name="USER_WorkingWithParamGroups.Viewing.API"></a>

To view the parameter values for a DB parameter group, use the RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBParameters.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBParameters.html) command with the following required parameter\.
+ `DBParameterGroupName`

## Viewing Parameter Values for a DB Cluster Parameter Group<a name="USER_WorkingWithParamGroups.ViewingCluster"></a>

You can get a list of all parameters in a DB cluster parameter group and their values\.

### Console<a name="USER_WorkingWithParamGroups.ViewingCluster.CON"></a>

**To view the parameter values for a DB cluster parameter group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

   The DB cluster parameter groups appear in the list with **DB cluster parameter group** for **Type**\.

1. Choose the name of the DB cluster parameter group to see its list of parameters\.

### AWS CLI<a name="USER_WorkingWithParamGroups.ViewingCluster.CLI"></a>

To view the parameter values for a DB cluster parameter group, use the AWS CLI [ `describe-db-cluster-parameters`](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-cluster-parameters.html) command with the following required parameter\.
+ `--db-cluster-parameter-group-name`

**Example**  
The following example lists the parameters and parameter values for a DB cluster parameter group named *mydbclusterparametergroup*, in JSON format\.  
The command returns a response like the following:  

```
aws rds describe-db-cluster-parameters --db-cluster-parameter-group-name mydbclusterparametergroup
```

```
{
    "Parameters": [
        {
            "ApplyMethod": "pending-reboot",
            "Description": "Controls whether user-defined functions that have only an xxx symbol for the main function can be loaded",
            "DataType": "boolean",
            "AllowedValues": "0,1",
            "SupportedEngineModes": [
                "provisioned"
            ],
            "Source": "engine-default",
            "IsModifiable": false,
            "ParameterName": "allow-suspicious-udfs",
            "ApplyType": "static"
        },
        {
            "ApplyMethod": "pending-reboot",
            "Description": "Enables new features in the Aurora engine.",
            "DataType": "boolean",
            "IsModifiable": true,
            "AllowedValues": "0,1",
            "SupportedEngineModes": [
                "provisioned"
            ],
            "Source": "engine-default",
            "ParameterValue": "0",
            "ParameterName": "aurora_lab_mode",
            "ApplyType": "static"
        },
...
```
The following example lists the parameters and parameter values for a DB cluster parameter group named *mydbclusterparametergroup*, in plain text format\.  

```
aws rds describe-db-cluster-parameters --db-cluster-parameter-group-name mydbclusterparametergroup --output text
```
The command returns a response like the following:  

```
PARAMETERS  0,1  pending-reboot  static  boolean  Controls whether user-defined functions that have only an xxx symbol for the main function can be loaded  False  allow-suspicious-udfs    engine-default SUPPORTEDENGINEMODES  provisioned
PARAMETERS  0,1  pending-reboot  static  boolean  Enables new features in the Aurora engine.  True  aurora_lab_mode  0  engine-default SUPPORTEDENGINEMODES  provisioned
...
```

### RDS API<a name="USER_WorkingWithParamGroups.ViewingCluster.API"></a>

To view the parameter values for a DB cluster parameter group, use the RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBParameters.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBParameters.html) command with the following required parameter\.
+ `DBClusterParameterGroupName`

## Comparing Parameter Groups<a name="USER_WorkingWithParamGroups.Comparing"></a>

You can use the AWS Management Console to view the differences between two parameter groups for the same DB engine and version\.

**To compare two parameter groups**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

1. In the list, choose the two parameter groups that you want to compare\.

1. For **Parameter group actions**, choose **Compare**\.
**Note**  
If the items you selected aren't equivalent, you can't choose **Compare**\. For example, you can't compare a MySQL 5\.6 and a MySQL 5\.7 parameter group\. You can't compare a DB parameter group and an Aurora DB cluster parameter group\.

## DB Parameter Values<a name="USER_ParamValuesRef"></a>

You can specify the value for a DB parameter as any of the following:
+ An integer constant
+ A DB parameter formula
+ A DB parameter function
+ A character string constant
+ A log expression \(the log function represents log base 2\), such as `value={log(DBInstanceClassMemory/8187281418)*1000}` 

### DB Parameter Formulas<a name="USER_ParamFormulas"></a>

A DB parameter formula is an expression that resolves to an integer value or a Boolean value, and is enclosed in braces: \{\}\. You can specify formulas for either a DB parameter value or as an argument to a DB parameter function\.

#### Syntax<a name="USER_ParamFormulas.Syntax"></a>

```
{FormulaVariable}
{FormulaVariable*Integer}
{FormulaVariable*Integer/Integer}
{FormulaVariable/Integer}
```

### DB Parameter Formula Variables<a name="USER_FormulaVariables"></a>

Each formula variable returns integer or a Boolean value\. The names of the variables are case\-sensitive\.

*AllocatedStorage*  
Returns the size, in bytes, of the data volume\.

*DBInstanceClassMemory*  
Returns the number of bytes of memory allocated to the DB instance class associated with the current DB instance, less the memory used by the Amazon RDS processes that manage the instance\.

*EndPointPort*  
Returns the number of the port used when connecting to the DB instance\.

### DB Parameter Formula Operators<a name="USER_FormulaOperators"></a>

DB parameter formulas support two operators: division and multiplication\.

*Division Operator: /*  
Divides the dividend by the divisor, returning an integer quotient\. Decimals in the quotient are truncated, not rounded\.  
Syntax  

```
dividend / divisor
```
The dividend and divisor arguments must be integer expressions\.

*Multiplication Operator: \**  
Multiplies the expressions, returning the product of the expressions\. Decimals in the expressions are truncated, not rounded\.  
Syntax  

```
expression * expression
```
Both expressions must be integers\.

### DB Parameter Functions<a name="USER_ParamFunctions"></a>

The parameter arguments can be specified as either integers or formulas\. Each function must have at least one argument\. Multiple arguments can be specified as a comma\-separated list\. The list can't have any empty members, such as *argument1*,,*argument3*\. Function names are case\-insensitive\.

**Note**  
DB Parameter functions are not currently supported in the AWS CLI\.

*IF\(\)*  
Returns an argument\.  
Syntax  

```
IF(argument1, argument2, argument3)
```
Returns the second argument if the first argument evaluates to true\. Returns the third argument otherwise\.

*GREATEST\(\)*  
Returns the largest value from a list of integers or parameter formulas\.  
Syntax  

```
GREATEST(argument1, argument2,...argumentn)
```
Returns an integer\.

*LEAST\(\)*  
Returns the smallest value from a list of integers or parameter formulas\.  
Syntax  

```
LEAST(argument1, argument2,...argumentn)
```
Returns an integer\.

*SUM\(\)*  
Adds the values of the specified integers or parameter formulas\.  
Syntax  

```
SUM(argument1, argument2,...argumentn)
```
Returns an integer\.

### DB Parameter Value Examples<a name="USER_ParamValueExamples"></a>

These examples show using formulas and functions in the values for DB parameters\.

**Warning**  
Improperly setting parameters in a DB parameter group can have unintended adverse effects, including degraded performance and system instability\. Always exercise caution when modifying database parameters and back up your data before modifying your DB parameter group\. Try out parameter group changes on a test DB instances, created using point\-in\-time\-restores, before applying those parameter group changes to your production DB instances\. 

You can specify the `LEAST()` function in an Aurora MySQL `table_definition_cache` parameter value to set the number of table definitions that can be stored in the definition cache to the lesser of `DBInstanceClassMemory`/393040 or 20,000\.

```
LEAST({DBInstanceClassMemory/393040}, 20000)
```