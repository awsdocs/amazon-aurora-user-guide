# Working with DB Parameter Groups and DB Cluster Parameter Groups<a name="USER_WorkingWithParamGroups"></a>

You manage your DB engine configuration through the use of parameters in a DB parameter group  and DB cluster parameter Groups\. DB parameter groups act as a container for engine configuration values that are applied to one or more DB instances\. DB cluster parameter groups act as a container for engine configuration values that are applied to every DB instance in a DB cluster\.

A default DB parameter group is created if you create a DB instance without specifying a customer\-created DB parameter group\. A default DB cluster parameter group is created if you create a DB cluster without specifying a customer\-created DB cluster parameter group\. Each default parameter group contains database engine defaults and Amazon RDS system defaults based on the engine, compute class, and allocated storage of the instance\. You cannot modify the parameter settings of a default parameter group; you must create your own parameter group to change parameter settings from their default value\. Note that not all DB engine parameters can be changed in a customer\-created parameter group\.

 If you want to use your own parameter group, you simply create a new parameter group, modify the desired parameters, and modify your DB instance or DB cluster  to use the new parameter group\. All DB instances that are associated with a particular DB parameter group get all parameter updates to that DB parameter group\. All DB clusters that are associated with a particular DB cluster parameter group get all parameter updates to that DB cluster parameter group\.

You can copy an existing DB parameter group with the AWS CLI [copy\-db\-parameter\-group](http://docs.aws.amazon.com/cli/latest/reference/rds/copy-db-parameter-group.html) command\. You can copy an existing DB cluster parameter group with the AWS CLI [copy\-db\-cluster\-parameter\-group](http://docs.aws.amazon.com/cli/latest/reference/rds/copy-db-cluster-parameter-group.html) command\. Copying a parameter group is a convenient solution when you have already created a parameter group and you want to include most of the custom parameters and values from that group in a new parameter group\.

Here are some important points you should know about working with parameters in a parameter group:
+ When you change a dynamic parameter and save the parameter group, the change is applied immediately regardless of the **Apply Immediately** setting\. When you change a static parameter and save the DB parameter group, the parameter change will take effect after you manually reboot the DB instance\. You can reboot a DB instance using the RDS console or explicitly calling the `RebootDbInstance` API action \(without failover, if the DB instance is in a Multi\-AZ deployment\)\. The requirement to reboot the associated DB instance after a static parameter change helps mitigate the risk of a parameter misconfiguration affecting an API call, such as calling `ModifyDBInstance` to change DB instance class or scale storage\.
+ When you change the DB parameter group associated with a DB instance, you must manually reboot the instance before the new DB parameter group is used by the DB instance\. 
+ The value for a parameter can be specified as an integer or as an integer expression built from formulas, variables, functions, and operators\. Functions can include a mathematical log expression\. For more information, see [DB Parameter Values](#USER_ParamValuesRef)\.
+ Set any parameters that relate to the character set or collation of your database in your parameter group prior to creating the DB instance and before you create a database in your DB instance\. This ensures that the default database and new databases in your DB instance use the character set and collation values that you specify\. If you change character set or collation parameters for your DB instance, the parameter changes are not applied to existing databases\.

  You can change character set or collation values for an existing database using the `ALTER DATABASE` command, for example:

  ```
  ALTER DATABASE database_name CHARACTER SET character_set_name COLLATE collation;
  ```
+ Improperly setting parameters in a parameter group can have unintended adverse effects, including degraded performance and system instability\. Always exercise caution when modifying database parameters and back up your data before modifying a parameter group\. You should try out parameter group setting changes on a test DB instance before applying those parameter group changes to a production DB instance\.

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

Amazon Aurora uses both DB parameter groups and DB cluster parameter groups\. Parameters in a DB parameter group apply to a single DB instance in an Aurora DB cluster\. Parameters in a DB cluster parameter group apply to every DB instance in a DB cluster\.

Instance\-level parameters are managed in DB parameter groups\. Cluster\-level parameters are managed in DB cluster parameter groups\. 

Although each DB instance in an Aurora DB cluster is compatible with a specific database engine, some of the database engine parameters must be applied at the cluster level\. You manage these using DB cluster parameter groups\. Cluster\-level parameters are not found in the DB parameter group for an instance in an Aurora DB cluster and are listed later in this topic\.

The DB cluster and DB instance parameters available to you in Aurora vary depending on database engine compatibility\.


| Database Engine | Parameters | 
| --- | --- | 
|  Amazon Aurora MySQL  |  See [Amazon Aurora MySQL Parameters](AuroraMySQL.Reference.md#AuroraMySQL.Reference.ParameterGroups)  | 
|  Amazon Aurora PostgreSQL  |  See [Amazon Aurora PostgreSQL Parameters](AuroraPostgreSQL.Reference.md#AuroraPostgreSQL.Reference.ParameterGroups)  | 

## Creating a DB Parameter Group<a name="USER_WorkingWithParamGroups.Creating"></a>

You can create a new DB parameter group using the AWS Management Console, the AWS CLI, or the RDS API\.

### AWS Management Console<a name="USER_WorkingWithParamGroups.Creating.CON"></a>

**To create a DB parameter group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

1. Choose **Create parameter group**\.

   The **Create parameter group** window appears\.

1. In the **Parameter group family** list, select a DB parameter group family\.

1. In the **Type** list, select **DB Parameter Group**\.

1. In the **Group name** box, type the name of the new DB parameter group\.

1. In the **Description** box, type a description for the new DB parameter group\. 

1. Choose **Create**\.

### CLI<a name="USER_WorkingWithParamGroups.Creating.CLI"></a>

To create a DB parameter group, use the AWS CLI [http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-parameter-group.html](http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-parameter-group.html) command\. The following example creates a DB parameter group named *mydbparametergroup* for MySQL version 5\.6 with a description of "*My new parameter group*\."

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
For Linux, OS X, or Unix:  

```
1. aws rds create-db-parameter-group \
2.     --db-parameter-group-name mydbparametergroup \
3.     --db-parameter-group-family aurora5.6 \
4.     --description "My new parameter group"
```
For Windows:  

```
1. aws rds create-db-parameter-group ^
2.     --db-parameter-group-name mydbparametergroup ^
3.     --db-parameter-group-family aurora5.6 ^
4.     --description "My new parameter group"
```
This command produces output similar to the following:  

```
1. DBPARAMETERGROUP  mydbparametergroup  aurora5.6  My new parameter group					
```

### API<a name="USER_WorkingWithParamGroups.Creating.API"></a>

To create a DB parameter group, use the Amazon RDS API [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBParameterGroup.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBParameterGroup.html) action\.

Include the following required parameters:
+ `DBParameterGroupName`
+ `DBParameterGroupFamily`
+ `Description`

## Creating a DB Cluster Parameter Group<a name="USER_WorkingWithParamGroups.CreatingCluster"></a>

You can create a new DB cluster parameter group using the AWS Management Console, the AWS CLI, or the RDS API\.

### AWS Management Console<a name="USER_WorkingWithParamGroups.CreatingCluster.CON"></a>

**To create a DB cluster parameter group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

1. Choose **Create parameter group**\.

   The **Create parameter group** window appears\.

1. In the **Parameter group family** list, select a DB parameter group family 

1. In the **Type** list, select **DB Cluster Parameter Group**\.

1. In the **Group name** box, type the name of the new DB cluster parameter group\.

1. In the **Description** box, type a description for the new DB cluster parameter group\.

1. Choose **Create**\.

### CLI<a name="USER_WorkingWithParamGroups.CreatingCluster.CLI"></a>

To create a DB cluster parameter group, use the AWS CLI [ `create-db-cluster-parameter-group`](http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster-parameter-group.html) command\. The following example creates a DB cluster parameter group named *mydbclusterparametergroup* for MySQL version 5\.6 with a description of "*My new cluster parameter group*\."

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
For Linux, OS X, or Unix:  

```
1. aws rds create-db-cluster-parameter-group \
2.     --db-cluster-parameter-group-name mydbclusterparametergroup \
3.     --db-parameter-group-family aurora5.6 \
4.     --description "My new cluster parameter group"
```
For Windows:  

```
1. aws rds create-db-cluster-parameter-group ^
2.     --db-cluster-parameter-group-name mydbclusterparametergroup ^
3.     --db-parameter-group-family aurora5.6 ^
4.     --description "My new cluster parameter group"
```
This command produces output similar to the following:  

```
1. DBCLUSTERPARAMETERGROUP  mydbclusterparametergroup  mysql5.6  My cluster new parameter group	
```

### API<a name="USER_WorkingWithParamGroups.CreatingCluster.API"></a>

To create a DB cluster parameter group, use the Amazon RDS API [ `CreateDBClusterParameterGroup`](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBClusterParameterGroup.html) action\.

Include the following required parameters:
+ `DBClusterParameterGroupName`
+ `DBParameterGroupFamily`
+ `Description`

## Modifying Parameters in a DB Parameter Group<a name="USER_WorkingWithParamGroups.Modifying"></a>

You can modify parameter values in a customer\-created DB parameter group; you cannot change the parameter values in a default DB parameter group\. Changes to parameters in a customer\-created DB parameter group are applied to all DB instances that are associated with the DB parameter group\. 

If you change a parameter value, when the change is applied is determined by the type of parameter\. Changes to dynamic parameters are applied immediately\. Changes to static parameters require that the DB instance associated with DB parameter group be rebooted before the change takes effect\. To determine the type of a parameter, list the parameters in a parameter group using one of the procedures shown in the section [Listing DB Parameter Groups](#USER_WorkingWithParamGroups.Listing)\.

The RDS console shows the status of the DB parameter group associated with a DB instance\. For example, if the DB instance is not using the latest changes to its associated DB parameter group, the RDS console shows the DB parameter group with a status of **pending\-reboot**\. You would need to manually reboot the DB instance for the latest parameter changes to take effect for that DB instance\.

![\[Parameter change pending reboot scenario\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/param-reboot.png)

### AWS Management Console<a name="USER_WorkingWithParamGroups.Modifying.CON"></a>

**To modify a DB parameter group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

1. In the list, select the parameter group you want to modify\.

1. Choose **Parameter group actions**, and then choose **Edit**\.

1. Change the values of the parameters you want to modify\. You can scroll through the parameters using the arrow keys at the top right of the dialog box\. 

   Note that you cannot change values in a default parameter group\.

1. Choose **Save changes**\.

### CLI<a name="USER_WorkingWithParamGroups.Modifying.CLI"></a>

To modify a DB parameter group, use the AWS CLI [http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-parameter-group.html](http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-parameter-group.html) command with the following required parameters:
+ `--db-parameter-group-name`
+ `--parameters`

The following example modifies the` max_connections` and `max_allowed_packet` values in the DB parameter group named *mydbparametergroup*\.

**Note**  
Amazon RDS does not support passing multiple comma\-delimited parameter values for a single parameter\. 

**Example**  
For Linux, OS X, or Unix:  

```
1. aws rds modify-db-parameter-group \
2.     --db-parameter-group-name mydbparametergroup \
3.     --parameters "ParameterName=max_connections,ParameterValue=250,ApplyMethod=immediate" \
4.                  "ParameterName=max_allowed_packet,ParameterValue=1024,ApplyMethod=immediate"
```
For Windows:  

```
1. aws rds modify-db-parameter-group ^
2.     --db-parameter-group-name mydbparametergroup ^
3.     --parameters "ParameterName=max_connections,ParameterValue=250,ApplyMethod=immediate" ^
4.                  "ParameterName=max_allowed_packet,ParameterValue=1024,ApplyMethod=immediate"
```
The command produces output like the following:  

```
1. DBPARAMETERGROUP  mydbparametergroup
```

### API<a name="USER_WorkingWithParamGroups.Modifying.API"></a>

To modify a DB parameter group, use the Amazon RDS API [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBParameterGroup.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBParameterGroup.html) command with the following required parameters:
+ `DBParameterGroupName`
+ `Parameters`

## Modifying Parameters in a DB Cluster Parameter Group<a name="USER_WorkingWithParamGroups.ModifyingCluster"></a>

You can modify parameter values in a customer\-created DB cluster parameter group; you cannot change the parameter values in a default DB cluster parameter group\. Changes to parameters in a customer\-created DB cluster parameter group are applied to all DB clusters that are associated with the DB cluster parameter group\. 

If you change a parameter value, when the change is applied is determined by the type of parameter\. Changes to dynamic parameters are applied immediately\. Changes to static parameters require that the DB instances associated with DB cluster that uses the DB cluster parameter group be rebooted before the change takes effect\. To determine the type of a parameter, list the parameters in a parameter group using one of the procedures shown in the section [Listing DB Parameter Groups](#USER_WorkingWithParamGroups.Listing)\.

The RDS console shows the status of the DB cluster parameter group associated with a DB instance\. For example, if the DB instance is not using the latest changes to the associated DB cluster parameter group, the RDS console shows the DB cluster parameter group with a status of **pending\-reboot**\. You would need to manually reboot the DB instance for the latest parameter changes to take effect for that DB instance\.

![\[Cluster parameter change pending reboot scenario\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/param-cluster-reboot.png)

### AWS Management Console<a name="USER_WorkingWithParamGroups.ModifyingCluster.CON"></a>

**To modify a DB cluster parameter group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

1. In the list, select the parameter group you want to modify\.

1. Choose **Parameter group actions**, and then choose **Edit**\.

1. Change the values of the parameters you want to modify\. You can scroll through the parameters using the arrow keys at the top right of the dialog box\. 

   Note that you cannot change values in a default parameter group\.

1. Choose **Save changes**\.

### CLI<a name="USER_WorkingWithParamGroups.ModifyingCluster.CLI"></a>

To modify a DB cluster parameter group, use the AWS CLI [ `modify-db-cluster-parameter-group`](http://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster-parameter-group.html) command with the following required parameters:
+ `--db-cluster-parameter-group-name`
+ `--parameters`

The following example modifies the `server_audit_logging` and `server_audit_logs_upload` values in the DB cluster parameter group named *mydbclusterparametergroup*\.

**Note**  
Amazon RDS does not support passing multiple comma\-delimited parameter values for a single parameter\. 

**Example**  
For Linux, OS X, or Unix:  

```
1. aws rds modify-db-cluster-parameter-group \
2.     --db-cluster-parameter-group-name mydbclusterparametergroup \
3.     --parameters "ParameterName=server_audit_logging,ParameterValue=1,ApplyMethod=immediate" \
4.                  "ParameterName=server_audit_logs_upload,ParameterValue=1,ApplyMethod=immediate"
```
For Windows:  

```
1. aws rds modify-db-cluster-parameter-group ^
2.     --db-cluster-parameter-group-name mydbclusterparametergroup ^
3.     --parameters "ParameterName=server_audit_logging,ParameterValue=1,ApplyMethod=immediate" ^
4.                  "ParameterName=server_audit_logs_upload,ParameterValue=1,ApplyMethod=immediate"
```
The command produces output like the following:  

```
1. DBCLUSTERPARAMETERGROUP  mydbclusterparametergroup
```

### API<a name="USER_WorkingWithParamGroups.ModifyingCluster.API"></a>

To modify a DB cluster parameter group, use the Amazon RDS API [ `ModifyDBClusterParameterGroup`](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBClusterParameterGroup.html) command with the following required parameters:
+ `DBClusterParameterGroupName`
+ `Parameters`

## Copying a DB Parameter Group<a name="USER_WorkingWithParamGroups.Copying"></a>

You can copy custom DB parameter groups that you create\. Copying a parameter group is a convenient solution when you have already created a DB parameter group and you want to include most of the custom parameters and values from that group in a new DB parameter group\. You can copy a DB parameter group by using the AWS CLI [copy\-db\-parameter\-group](http://docs.aws.amazon.com/cli/latest/reference/rds/copy-db-parameter-group.html) command or the Amazon RDS API [CopyDBParameterGroup](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_CopyDBParameterGroup.html) action\.

After you copy a DB parameter group, you should wait at least 5 minutes before creating your first DB instance that uses that DB parameter group as the default parameter group\. This allows Amazon RDS to fully complete the copy action before the parameter group is used as the default for a new DB instance\. This is especially important for parameters that are critical when creating the default database for a DB instance, such as the character set for the default database defined by the `character_set_database` parameter\. You can use the **Parameter Groups** option of the [Amazon RDS console](https://console.aws.amazon.com/rds/) or the [describe\-db\-parameters](http://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-parameters.html) command to verify that your DB parameter group has been created\.

**Note**  
You can't copy a default parameter group\. However, you can create a new parameter group that is based on a default parameter group\.

### AWS Management Console<a name="USER_WorkingWithParamGroups.Copying.CON"></a>

**To copy a DB parameter group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

1. In the list, select the custom parameter group you want to copy\.

1. Choose **Parameter group actions**, and then choose **Copy**\.

1. In **New DB parameter group identifier**, type a name for the new parameter group\.

1. In **Description**, type a description for the new parameter group\.

1. Choose **Copy**\.

### CLI<a name="USER_WorkingWithParamGroups.Copying.CLI"></a>

To copy a DB parameter group, use the AWS CLI [ `copy-db-parameter-group`](http://docs.aws.amazon.com/cli/latest/reference/rds/copy-db-parameter-group.html) command with the following required parameters:
+ `--source-db-parameter-group-identifier`
+ `--target-db-parameter-group-identifier`
+ `--target-db-parameter-group-description`

The following example creates a new DB parameter group named `mygroup2` that is a copy of the DB parameter group `mygroup1`\.

**Example**  
For Linux, OS X, or Unix:  

```
1. aws rds copy-db-parameter-group \
2.     --source-db-parameter-group-identifier mygroup1 \
3.     --target-db-parameter-group-identifier mygroup2 \
4.     --target-db-parameter-group-description "DB parameter group 2"
```
For Windows:  

```
1. aws rds copy-db-parameter-group ^
2.     --source-db-parameter-group-identifier mygroup1 ^
3.     --target-db-parameter-group-identifier mygroup2 ^
4.     --target-db-parameter-group-description "DB parameter group 2"
```

### API<a name="USER_WorkingWithParamGroups.Copying.API"></a>

To copy a DB parameter group, use the RDS API [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CopyDBParameterGroup.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CopyDBParameterGroup.html) action with the following required parameters:
+ `SourceDBParameterGroupIdentifier`
+ `TargetDBParameterGroupIdentifier`
+ `TargetDBParameterGroupDescription`

## Copying a DB Cluster Parameter Group<a name="USER_WorkingWithParamGroups.CopyingCluster"></a>

You can copy custom DB cluster parameter groups that you create\. Copying a parameter group is a convenient solution when you have already created a DB cluster parameter group and you want to include most of the custom parameters and values from that group in a new DB cluster parameter group\. You can copy a DB cluster parameter group by using the AWS CLI [copy\-db\-cluster\-parameter\-group](http://docs.aws.amazon.com/cli/latest/reference/rds/copy-db-cluster-parameter-group.html) command or the Amazon RDS API [CopyDBClusterParameterGroup](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference//API_CopyDBParameterGroup.html) action\.

After you copy a DB cluster parameter group, you should wait at least 5 minutes before creating your first DB cluster that uses that DB cluster parameter group as the default parameter group\. This allows Amazon RDS to fully complete the copy action before the parameter group is used as the default for a new DB cluster\. You can use the **Parameter Groups** option of the [Amazon RDS console](https://console.aws.amazon.com/rds/) or the [describe\-db\-cluster\-parameters](http://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-cluster-parameters.html) command to verify that your DB cluster parameter group has been created\.

**Note**  
You can't copy a default parameter group\. However, you can create a new parameter group that is based on a default parameter group\.

### AWS Management Console<a name="USER_WorkingWithParamGroups.CopyingCluster.CON"></a>

**To copy a DB cluster parameter group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

1. In the list, select the custom parameter group you want to copy\.

1. Choose **Parameter group actions**, and then choose **Copy**\.

1. In **New DB parameter group identifier**, type a name for the new parameter group\.

1. In **Description**, type a description for the new parameter group\.

1. Choose **Copy**\.

### CLI<a name="USER_WorkingWithParamGroups.CopyingCluster.CLI"></a>

To copy a DB cluster parameter group, use the AWS CLI [ `copy-db-cluster-parameter-group`](http://docs.aws.amazon.com/cli/latest/reference/rds/copy-db-cluster-parameter-group.html) command with the following required parameters:
+ `--source-db-cluster-parameter-group-identifier`
+ `--target-db-cluster-parameter-group-identifier`
+ `--target-db-cluster-parameter-group-description`

The following example creates a new DB cluster parameter group named `mygroup2` that is a copy of the DB cluster parameter group `mygroup1`\.

**Example**  
For Linux, OS X, or Unix:  

```
1. aws rds copy-db-cluster-parameter-group \
2.     --source-db-cluster-parameter-group-identifier mygroup1 \
3.     --target-db-cluster-parameter-group-identifier mygroup2 \
4.     --target-db-cluster-parameter-group-description "DB parameter group 2"
```
For Windows:  

```
1. aws rds copy-db-cluster-parameter-group ^
2.     --source-db-cluster-parameter-group-identifier mygroup1 ^
3.     --target-db-cluster-parameter-group-identifier mygroup2 ^
4.     --target-db-cluster-parameter-group-description "DB parameter group 2"
```

### API<a name="USER_WorkingWithParamGroups.Copying.API"></a>

To copy a DB cluster parameter group, use the RDS API [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CopyDBClusterParameterGroup.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CopyDBClusterParameterGroup.html) action with the following required parameters:
+ `SourceDBClusterParameterGroupIdentifier`
+ `TargetDBClusterParameterGroupIdentifier`
+ `TargetDBClusterParameterGroupDescription`

## Listing DB Parameter Groups<a name="USER_WorkingWithParamGroups.Listing"></a>

You can list the DB parameter groups you've created for your AWS account\.

**Note**  
Default parameter groups are automatically created from a default parameter template when you create a DB instance for a particular DB engine and version\. These default parameter groups contain preferred parameter settings and cannot be modified\. When you create a custom parameter group, you can modify parameter settings\. 

### AWS Management Console<a name="USER_WorkingWithParamGroups.Listing.CON"></a>

**To list all DB parameter groups for an AWS account**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

   The DB parameter groups appear in a list\.

### CLI<a name="USER_WorkingWithParamGroups.Listing.CLI"></a>

To list all DB parameter groups for an AWS account, use the AWS CLI [http://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-parameter-groups.html](http://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-parameter-groups.html) command\.

**Example**  
The following example lists all available DB parameter groups for an AWS account\.  

```
1. aws rds describe-db-parameter-groups
```
The command returns a response like the following:  

```
1. DBPARAMETERGROUP  default.mysql5.5     mysql5.5  Default parameter group for MySQL5.5
2. DBPARAMETERGROUP  default.mysql5.6     mysql5.6  Default parameter group for MySQL5.6
3. DBPARAMETERGROUP  mydbparametergroup   mysql5.6  My new parameter group
```
The following example describes the *mydbparamgroup1* parameter group\.  
For Linux, OS X, or Unix:  

```
1. aws rds describe-db-parameter-groups \
2.     --db-parameter-group-name mydbparamgroup1
```
For Windows:  

```
1. aws rds describe-db-parameter-groups ^
2.     --db-parameter-group-name mydbparamgroup1
```
The command returns a response like the following:  

```
1. DBPARAMETERGROUP  mydbparametergroup1  mysql5.5  My new parameter group
```

### API<a name="USER_WorkingWithParamGroups.Listing.API"></a>

To list all DB parameter groups for an AWS account, use the RDS API [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBParameterGroups.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBParameterGroups.html) action\.

## Listing DB Cluster Parameter Groups<a name="USER_WorkingWithParamGroups.ListingCluster"></a>

You can list the DB cluster parameter groups you've created for your AWS account\.

**Note**  
Default parameter groups are automatically created from a default parameter template when you create a DB cluster for a particular DB engine and version\. These default parameter groups contain preferred parameter settings and cannot be modified\. When you create a custom parameter group, you can modify parameter settings\. 

### AWS Management Console<a name="USER_WorkingWithParamGroups.ListingCluster.CON"></a>

**To list all DB cluster parameter groups for an AWS account**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

   The DB cluster parameter groups appear in the list with **DB cluster parameter group** for **Type**\.

### CLI<a name="USER_WorkingWithParamGroups.ListingCluster.CLI"></a>

To list all DB cluster parameter groups for an AWS account, use the AWS CLI [ `describe-db-cluster-parameter-groups`](http://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusterparameter-groups.html) command\.

**Example**  
The following example lists all available DB cluster parameter groups for an AWS account\.  

```
1. aws rds describe-db-cluster-parameter-groups
```
The command returns a response like the following:  

```
1. DBCLUSTERPARAMETERGROUPS        arn:aws:rds:us-west-2:1234567890:cluster-pg:default.aurora5.6 default.aurora5.6       aurora5.6       Default cluster parameter group for aurora5.6
2. DBCLUSTERPARAMETERGROUPS        arn:aws:rds:us-west-2:1234567890:cluster-pg:mydbclusterparametergroup mydbclusterparametergroup       aurora5.6       My new cluster parameter group
```
The following example describes the *mydbclusterparametergroup* parameter group\.  
For Linux, OS X, or Unix:  

```
1. aws rds describe-db-cluster-parameter-groups \
2.     --db-cluster-parameter-group-name mydbclusterparametergroup
```
For Windows:  

```
1. aws rds describe-db-cluster-parameter-groups ^
2.     --db-cluster-parameter-group-name mydbclusterparametergroup
```
The command returns a response like the following:  

```
1. DBCLUSTERPARAMETERGROUPS        arn:aws:rds:us-west-2:1234567890:cluster-pg:mydbclusterparametergroup mydbclusterparametergroup       aurora5.6       My new cluster parameter group
```

### API<a name="USER_WorkingWithParamGroups.ListingCluster.API"></a>

To list all DB cluster parameter groups for an AWS account, use the RDS API [ `DescribeDBClusterParameterGroups`](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBClusterParameterGroups.html) action\.

## Viewing Parameter Values for a DB Parameter Group<a name="USER_WorkingWithParamGroups.Viewing"></a>

You can get a list of all parameters in a DB parameter group and their values\.

### AWS Management Console<a name="USER_WorkingWithParamGroups.Viewing.CON"></a>

**To view the parameter values for a DB parameter group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

   The DB parameter groups appear in a list\.

1. Click the name of the parameter group to see the its list of parameters\.

### CLI<a name="USER_WorkingWithParamGroups.Viewing.CLI"></a>

To view the parameter values for a DB parameter group, use the AWS CLI [http://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-parameters.html](http://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-parameters.html) command with the following required parameter\.
+ `--db-parameter-group-name`

**Example**  
The following example lists the parameters and parameter values for a DB parameter group named *mydbparametergroup\.*  

```
1. aws rds describe-db-parameters --db-parameter-group-name mydbparametergroup
```
The command returns a response like the following:  

```
1. DBPARAMETER  Parameter Name            Parameter Value  Source           Data Type  Apply Type  Is Modifiable
2. DBPARAMETER  allow-suspicious-udfs                      engine-default   boolean    static      false
3. DBPARAMETER  auto_increment_increment                   engine-default   integer    dynamic     true
4. DBPARAMETER  auto_increment_offset                      engine-default   integer    dynamic     true
5. DBPARAMETER  binlog_cache_size         32768            system           integer    dynamic     true
6. DBPARAMETER  socket                    /tmp/mysql.sock  system           string     static      false
```

### API<a name="USER_WorkingWithParamGroups.Viewing.API"></a>

To view the parameter values for a DB parameter group, use the Amazon RDS API [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBParameters.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBParameters.html) command with the following required parameter\.
+ `DBParameterGroupName`

## Viewing Parameter Values for a DB Cluster Parameter Group<a name="USER_WorkingWithParamGroups.ViewingCluster"></a>

You can get a list of all parameters in a DB cluster parameter group and their values\.

### AWS Management Console<a name="USER_WorkingWithParamGroups.ViewingCluster.CON"></a>

**To view the parameter values for a DB cluster parameter group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

   The DB cluster parameter groups appear in the list with **DB cluster parameter group** for **Type**\.

1. Click the name of the DB cluster parameter group to see the its list of parameters\.

### CLI<a name="USER_WorkingWithParamGroups.ViewingCluster.CLI"></a>

To view the parameter values for a DB cluster parameter group, use the AWS CLI [ `describe-db-cluster-parameters`](http://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-cluster-parameters.html) command with the following required parameter\.
+ `--db-cluster-parameter-group-name`

**Example**  
The following example lists the parameters and parameter values for a DB cluster parameter group named *mydbclusterparametergroup\.*  

```
1. aws rds describe-db-cluster-parameters --db-cluster-parameter-group-name mydbclusterparametergroup
```
The command returns a response like the following:  

```
1. PARAMETERS              pending-reboot  dynamic string  IAM role ARN used to load data from AWS S3  True        aurora_load_from_s3_role                engine-default
2. PARAMETERS              pending-reboot  dynamic string  IAM role ARN used to select data into AWS S3    True    aurora_select_into_s3_role              engine-default
3. PARAMETERS              pending-reboot  dynamic string  Default IAM role ARN used to access AWS Lambda Service  True    aws_default_lambda_role         engine-default
4. PARAMETERS              pending-reboot  dynamic string  Arn for the role used to upload data to CloudWatch Logs True    aws_default_logs_role           engine-default
```

### API<a name="USER_WorkingWithParamGroups.ViewingCluster.API"></a>

To view the parameter values for a DB cluster parameter group, use the Amazon RDS API [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBParameters.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBParameters.html) command with the following required parameter\.
+ `DBClusterParameterGroupName`

## Comparing Parameter Groups<a name="USER_WorkingWithParamGroups.Comparing"></a>

You can use the AWS Management Console to view the differences between two parameter groups for the same DB engine and version\.

**To compare two parameter groups**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

1. In the list, select the two parameter groups you want to compare\.

1. Choose **Parameter group actions**, and then choose **Compare**\.

## DB Parameter Values<a name="USER_ParamValuesRef"></a>

The value for a DB parameter can be specified as:
+ An integer constant
+ A DB parameter formula
+ A DB parameter function
+ A character string constant
+ A log expression \(the log function represents log base 2\), such as value=**\{log\(DBInstanceClassMemory/8187281418\)\*1000\}** 

### DB Parameter Formulas<a name="USER_ParamFormulas"></a>

A DB parameter formula is an expression that resolves to an integer value or a Boolean value, and is enclosed in braces: \{\}\. Formulas can be specified for either a DB parameter value or as an argument to a DB parameter function\.

#### Syntax<a name="w4aac19c22c63b7b4"></a>

```
{FormulaVariable}
```

```
{FormulaVariable*Integer}
```

```
{FormulaVariable*Integer/Integer}
```

```
{FormulaVariable/Integer}
```

### DB Parameter Formula Variables<a name="USER_FormulaVariables"></a>

Each formula variable returns integer or a Boolean value\. The names of the variables are case sensitive\.

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

The parameter arguments can be specified as either integers or formulas\. Each function must have at least one argument\. Multiple arguments can be specified as a comma\-separated list\. The list cannot have any empty members, such as *argument1*,,*argument3*\. Function names are case insensitive\.

**Note**  
DB Parameter functions are not currently supported in CLI\.

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
Improperly setting parameters in a DB parameter group can have unintended adverse effects, including degraded performance and system instability\. Always exercise caution when modifying database parameters and back up your data before modifying your DB parameter group\. You should try out parameter group changes on a test DB instances, created using point\-in\-time\-restores, before applying those parameter group changes to your production DB instances\. 

You can specify the LEAST\(\) function in an Aurora MySQL table\_definition\_cache parameter value to set the number of table definitions that can be stored in the definition cache to the lesser of DBInstanceClassMemory/393040 or 20,000:

```
LEAST({DBInstanceClassMemory/393040}, 20000)
```