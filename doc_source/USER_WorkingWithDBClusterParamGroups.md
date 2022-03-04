# Working with DB cluster parameter groups<a name="USER_WorkingWithDBClusterParamGroups"></a>

Amazon Aurora DB clusters use DB cluster parameter groups\. The following sections describe configuring and managing DB cluster parameter groups\.

**Topics**
+ [Amazon Aurora DB cluster and DB instance parameters](#Aurora.Managing.ParameterGroups)
+ [Creating a DB cluster parameter group](#USER_WorkingWithParamGroups.CreatingCluster)
+ [Associating a DB cluster parameter group with a DB cluster](#USER_WorkingWithParamGroups.AssociatingCluster)
+ [Modifying parameters in a DB cluster parameter group](#USER_WorkingWithParamGroups.ModifyingCluster)
+ [Resetting parameters in a DB cluster parameter group](#USER_WorkingWithParamGroups.ResettingCluster)
+ [Copying a DB cluster parameter group](#USER_WorkingWithParamGroups.CopyingCluster)
+ [Listing DB cluster parameter groups](#USER_WorkingWithParamGroups.ListingCluster)
+ [Viewing parameter values for a DB cluster parameter group](#USER_WorkingWithParamGroups.ViewingCluster)

## Amazon Aurora DB cluster and DB instance parameters<a name="Aurora.Managing.ParameterGroups"></a>

 Aurora uses a two\-level system of configuration settings, as follows: 
+  Parameters in a *DB cluster parameter group* apply to every DB instance in a DB cluster\. Your data is stored in the Aurora shared storage subsystem\. Because of this, all parameters related to physical layout of table data must be the same for all DB instances in an Aurora cluster\. Likewise, because Aurora DB instances are connected by replication, all the parameters for replication settings must be identical throughout an Aurora cluster\. 
+  Parameters in a *DB parameter group* apply to a single DB instance in an Aurora DB cluster\. These parameters are related to aspects such as memory usage that you can vary across DB instances in the same Aurora cluster\. For example, a cluster often contains DB instances with different AWS instance classes\. 

 Every Aurora cluster is associated with a DB cluster parameter group\. Each DB instance within the cluster inherits the settings from that DB cluster parameter group, and is associated with a DB parameter group\. Aurora assigns default parameter groups when you create a cluster or a new DB instance, based on the specified database engine and version\. You can change the parameter groups later to ones that you create, where you can edit the parameter values\. 

 The DB cluster parameter groups also include default values for all the instance\-level parameters from the DB parameter group\. These defaults are mainly intended for configuring Aurora Serverless clusters, which are only associated with DB cluster parameter groups, not DB parameter groups\. You can modify the instance\-level parameter settings in the DB cluster parameter group\. Then, Aurora applies those settings to each new DB instance that's added to a Serverless cluster\. To learn more about configuration settings for Aurora Serverless clusters and which settings you can modify, see [Parameter groups and Aurora Serverless v1](aurora-serverless.how-it-works.md#aurora-serverless.parameter-groups)\. 

 For non\-Serverless clusters, any configuration values that you modify in the DB cluster parameter group override default values in the DB parameter group\. If you edit the corresponding values in the DB parameter group, those values override the settings from the DB cluster parameter group\. 

 Any DB parameter settings that you modify take precedence over the DB cluster parameter group values, even if you change the configuration parameters back to their default values\. You can see which parameters are overridden by using the [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-parameters.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-parameters.html) AWS CLI command or the [DescribeDBParameters](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBParameters.html) RDS API\. The `Source` field contains the value `user` if you modified that parameter\. To reset one or more parameters so that the value from the DB cluster parameter group takes precedence, use the [https://docs.aws.amazon.com/cli/latest/reference/rds/reset-db-parameter-group.html](https://docs.aws.amazon.com/cli/latest/reference/rds/reset-db-parameter-group.html) AWS CLI command or the [ResetDBParameterGroup](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ResetDBParameterGroup.html) RDS API operation\. 

The DB cluster and DB instance parameters available to you in Aurora vary depending on database engine compatibility\.


| Database engine | Parameters | 
| --- | --- | 
|  Aurora MySQL  |  See [Aurora MySQL configuration parameters](AuroraMySQL.Reference.md#AuroraMySQL.Reference.ParameterGroups)\.  For Aurora Serverless clusters, see additional details in [Parameter groups and Aurora Serverless v1](aurora-serverless.how-it-works.md#aurora-serverless.parameter-groups)\.   | 
|  Aurora PostgreSQL  |  See [Amazon Aurora PostgreSQL parameters](AuroraPostgreSQL.Reference.ParameterGroups.md)\.  | 

## Creating a DB cluster parameter group<a name="USER_WorkingWithParamGroups.CreatingCluster"></a>

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

To create a DB cluster parameter group, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster-parameter-group.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster-parameter-group.html) command\.

The following example creates a DB cluster parameter group named *mydbclusterparametergroup* for Aurora MySQL version 5\.7 with a description of "*My new cluster parameter group*\."

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
    --db-parameter-group-family aurora-mysql5.7 \
    --description "My new cluster parameter group"
```
For Windows:  

```
aws rds create-db-cluster-parameter-group ^
    --db-cluster-parameter-group-name mydbclusterparametergroup ^
    --db-parameter-group-family aurora-mysql5.7 ^
    --description "My new cluster parameter group"
```
This command produces output similar to the following:  

```
{
    "DBClusterParameterGroup": {
        "DBClusterParameterGroupName": "mydbclusterparametergroup",
        "DBParameterGroupFamily": "aurora-mysql5.7",
        "Description": "My new cluster parameter group",
        "DBClusterParameterGroupArn": "arn:aws:rds:us-east-1:123456789012:cluster-pg:mydbclusterparametergroup"
    }
}
```

### RDS API<a name="USER_WorkingWithParamGroups.CreatingCluster.API"></a>

To create a DB cluster parameter group, use the RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBClusterParameterGroup.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBClusterParameterGroup.html) action\.

Include the following required parameters:
+ `DBClusterParameterGroupName`
+ `DBParameterGroupFamily`
+ `Description`

## Associating a DB cluster parameter group with a DB cluster<a name="USER_WorkingWithParamGroups.AssociatingCluster"></a>

You can create your own DB cluster parameter groups with customized settings\. You can associate a DB cluster parameter group with a DB cluster using the AWS Management Console, the AWS CLI, or the RDS API\. You can do so when you create or modify a DB cluster\.

For information about creating a DB cluster parameter group, see [Creating a DB cluster parameter group](#USER_WorkingWithParamGroups.CreatingCluster)\. For information about creating a DB cluster, see [Creating an Amazon Aurora DB cluster](Aurora.CreateInstance.md)\. For information about modifying a DB cluster, see [Modifying an Amazon Aurora DB cluster](Aurora.Modifying.md)\.

**Note**  
After you change the DB cluster parameter group associated with a DB cluster, reboot the primary DB instance in the cluster to apply the changes to all of the DB instances in the cluster\.  
To determine whether the primary DB instance of a DB cluster must be rebooted to apply changes, run the following AWS CLI command:  
`aws rds describe-db-clusters --db-cluster-identifier db_cluster_identifier`  
Check the `DBClusterParameterGroupStatus` value for the primary DB instance in the output\. If the value is `pending-reboot`, then reboot the primary DB instance of the DB cluster\.

### Console<a name="USER_WorkingWithParamGroups.AssociatingCluster.CON"></a>

**To associate a DB cluster parameter group with a DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then select the DB cluster that you want to modify\. 

1. Choose **Modify**\. The **Modify DB cluster** page appears\.

1. Change the **DB cluster parameter group** setting\. 

1. Choose **Continue** and check the summary of modifications\. 

   The change is applied immediately regardless of the **Scheduling of modifications** setting\.

1. On the confirmation page, review your changes\. If they are correct, choose **Modify cluster** to save your changes\. 

   Alternatively, choose **Back** to edit your changes, or choose **Cancel** to cancel your changes\. 

### AWS CLI<a name="USER_WorkingWithParamGroups.AssociatingCluster.CLI"></a>

To associate a DB cluster parameter group with a DB cluster, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) command with the following options:
+ `--db-cluster-name`
+ `--db-cluster-parameter-group-name`

The following example associates the `mydbclpg` DB parameter group with the `mydbcluster` DB cluster\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds modify-db-cluster \
    --db-cluster-identifier mydbcluster \
    --db-cluster-parameter-group-name mydbclpg
```
For Windows:  

```
aws rds modify-db-cluster ^
    --db-cluster-identifier mydbcluster ^
    --db-cluster-parameter-group-name mydbclpg
```

### RDS API<a name="USER_WorkingWithParamGroups.AssociatingCluster.API"></a>

To associate a DB cluster parameter group with a DB cluster, use the RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) operation with the following parameters:
+ `DBClusterIdentifier`
+ `DBClusterParameterGroupName`

## Modifying parameters in a DB cluster parameter group<a name="USER_WorkingWithParamGroups.ModifyingCluster"></a>

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

To modify a DB cluster parameter group, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster-parameter-group.html](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster-parameter-group.html) command with the following required parameters:
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

To modify a DB cluster parameter group, use the RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBClusterParameterGroup.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBClusterParameterGroup.html) command with the following required parameters:
+ `DBClusterParameterGroupName`
+ `Parameters`

## Resetting parameters in a DB cluster parameter group<a name="USER_WorkingWithParamGroups.ResettingCluster"></a>

You can reset parameters to their default values in a customer\-created DB cluster parameter group\. Changes to parameters in a customer\-created DB cluster parameter group are applied to all DB clusters that are associated with the DB cluster parameter group\.

**Note**  
In a default DB cluster parameter group, parameters are always set to their default values\.

### Console<a name="USER_WorkingWithParamGroups.ResettingCluster.CON"></a>

**To reset parameters in a DB cluster parameter group to their default values**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

1. In the list, choose the parameter group\.

1. For **Parameter group actions**, choose **Edit**\.

1. Choose the parameters that you want to reset to their default values\. You can scroll through the parameters using the arrow keys at the top right of the dialog box\. 

   You can't reset values in a default parameter group\.

1. Choose **Reset** and then confirm by choosing **Reset parameters**\.

1. Reboot the primary DB instance in the DB cluster to apply the changes to all of the DB instances in the DB cluster\.

### AWS CLI<a name="USER_WorkingWithParamGroups.ResettingCluster.CLI"></a>

To reset parameters in a DB cluster parameter group to their default values, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/reset-db-cluster-parameter-group.html](https://docs.aws.amazon.com/cli/latest/reference/rds/reset-db-cluster-parameter-group.html) command with the following required option: `--db-cluster-parameter-group-name`\.

To reset all of the parameters in the DB cluster parameter group, specify the `--reset-all-parameters` option\. To reset specific parameters, specify the `--parameters` option\.

The following example resets all of the parameters in the DB parameter group named *mydbparametergroup* to their default values\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds reset-db-cluster-parameter-group \
    --db-cluster-parameter-group-name mydbparametergroup \
    --reset-all-parameters
```
For Windows:  

```
aws rds reset-db-cluster-parameter-group ^
    --db-cluster-parameter-group-name mydbparametergroup ^
    --reset-all-parameters
```

The following example resets the `server_audit_logging` and `server_audit_logs_upload` to their default values in the DB cluster parameter group named *mydbclusterparametergroup*\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds reset-db-cluster-parameter-group \
    --db-cluster-parameter-group-name mydbclusterparametergroup \
    --parameters "ParameterName=server_audit_logging,ApplyMethod=immediate" \
                 "ParameterName=server_audit_logs_upload,ApplyMethod=immediate"
```
For Windows:  

```
aws rds reset-db-cluster-parameter-group ^
    --db-cluster-parameter-group-name mydbclusterparametergroup ^
    --parameters "ParameterName=server_audit_logging,ParameterValue=1,ApplyMethod=immediate" ^
                 "ParameterName=server_audit_logs_upload,ParameterValue=1,ApplyMethod=immediate"
```
The command produces output like the following:  

```
DBClusterParameterGroupName  mydbclusterparametergroup
```

### RDS API<a name="USER_WorkingWithParamGroups.ResettingCluster.API"></a>

To reset parameters in a DB cluster parameter group to their default values, use the RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ResetDBClusterParameterGroup.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ResetDBClusterParameterGroup.html) command with the following required parameter: `DBClusterParameterGroupName`\.

To reset all of the parameters in the DB cluster parameter group, set the `ResetAllParameters` parameter to `true`\. To reset specific parameters, specify the `Parameters` parameter\.

## Copying a DB cluster parameter group<a name="USER_WorkingWithParamGroups.CopyingCluster"></a>

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

To copy a DB cluster parameter group, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/copy-db-cluster-parameter-group.html](https://docs.aws.amazon.com/cli/latest/reference/rds/copy-db-cluster-parameter-group.html) command with the following required parameters:
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

## Listing DB cluster parameter groups<a name="USER_WorkingWithParamGroups.ListingCluster"></a>

You can list the DB cluster parameter groups you've created for your AWS account\.

**Note**  
Default parameter groups are automatically created from a default parameter template when you create a DB cluster for a particular DB engine and version\. These default parameter groups contain preferred parameter settings and can't be modified\. When you create a custom parameter group, you can modify parameter settings\. 

### Console<a name="USER_WorkingWithParamGroups.ListingCluster.CON"></a>

**To list all DB cluster parameter groups for an AWS account**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

   The DB cluster parameter groups appear in the list with **DB cluster parameter group** for **Type**\.

### AWS CLI<a name="USER_WorkingWithParamGroups.ListingCluster.CLI"></a>

To list all DB cluster parameter groups for an AWS account, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusterparameter-groups.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusterparameter-groups.html) command\.

**Example**  
The following example lists all available DB cluster parameter groups for an AWS account\.  

```
aws rds describe-db-cluster-parameter-groups
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
{
    "DBClusterParameterGroups": [
        {
            "DBClusterParameterGroupName": "mydbclusterparametergroup",
            "DBParameterGroupFamily": "aurora-mysql5.7",
            "Description": "My new cluster parameter group",
            "DBClusterParameterGroupArn": "arn:aws:rds:us-east-1:123456789012:cluster-pg:mydbclusterparametergroup"
        }
    ]
}
```

### RDS API<a name="USER_WorkingWithParamGroups.ListingCluster.API"></a>

To list all DB cluster parameter groups for an AWS account, use the RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBClusterParameterGroups.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBClusterParameterGroups.html) action\.

## Viewing parameter values for a DB cluster parameter group<a name="USER_WorkingWithParamGroups.ViewingCluster"></a>

You can get a list of all parameters in a DB cluster parameter group and their values\.

### Console<a name="USER_WorkingWithParamGroups.ViewingCluster.CON"></a>

**To view the parameter values for a DB cluster parameter group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

   The DB cluster parameter groups appear in the list with **DB cluster parameter group** for **Type**\.

1. Choose the name of the DB cluster parameter group to see its list of parameters\.

### AWS CLI<a name="USER_WorkingWithParamGroups.ViewingCluster.CLI"></a>

To view the parameter values for a DB cluster parameter group, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-cluster-parameters.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-cluster-parameters.html) command with the following required parameter\.
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
            "ParameterName": "allow-suspicious-udfs",
            "Description": "Controls whether user-defined functions that have only an xxx symbol for the main function can be loaded",
            "Source": "engine-default",
            "ApplyType": "static",
            "DataType": "boolean",
            "AllowedValues": "0,1",
            "IsModifiable": false,
            "ApplyMethod": "pending-reboot",
            "SupportedEngineModes": [
                "provisioned"
            ]
        },
        {
            "ParameterName": "aurora_binlog_read_buffer_size",
            "ParameterValue": "5242880",
            "Description": "Read buffer size used by master dump thread when the switch aurora_binlog_use_large_read_buffer is ON.",
            "Source": "engine-default",
            "ApplyType": "dynamic",
            "DataType": "integer",
            "AllowedValues": "8192-536870912",
            "IsModifiable": true,
            "ApplyMethod": "pending-reboot",
            "SupportedEngineModes": [
                "provisioned"
            ]
        },

...
```

### RDS API<a name="USER_WorkingWithParamGroups.ViewingCluster.API"></a>

To view the parameter values for a DB cluster parameter group, use the RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBParameters.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBParameters.html) command with the following required parameter\.
+ `DBClusterParameterGroupName`