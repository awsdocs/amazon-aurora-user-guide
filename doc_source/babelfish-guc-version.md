# Using Babelfish product version GUC<a name="babelfish-guc-version"></a>

 A new Grand Unified Configuration \(GUC\) parameter called `babelfishpg_tds.product_version` is introduced from Babelfish 2\.4\.0 and 3\.1\.0 versions\. This parameter allows you to set the SQL Server product version number as the output of Babelfish\. 

The GUC is a 4\-part version ID string, and each part should be separated by “\.”\. 

Syntax  

```
Major.Minor.Build.Revision
```
+ Major Version: A number between 11 and 16\.
+ Minor Version: A number between 0 and 255\. 
+ Build Version: A number between 0 and 65535\.
+ Revision: 0 and any positive number\.

## Configuring Babelfish GUC parameter<a name="babelfish-guc-version-setvalues"></a>

You must use the cluster parameter group to set the babelfishpg\_tds\.product\_version parameter in the console\. For more information on how to modify the DB cluster parameter, see [ Modifying parameters in a DB cluster parameter group](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithDBClusterParamGroups.html#USER_WorkingWithParamGroups.ModifyingCluster)

When you set the GUC to an invalid value, the change will not take effect\. Though the console might show you the new value, the GUC parameter retains the previous value\. Check the engine log file for details on the error messages\. 

For Linux, macOS, or Unix:

```
aws rds modify-db-cluster-parameter-group \
--db-cluster-parameter-group-name mydbparametergroup \
--parameters "ParameterName=babelfishpg_tds.product_version,ParameterValue=15.2.4000.1,ApplyMethod=immediate"
```

For Windows:

```
aws rds modify-db-cluster-parameter-group ^
--db-cluster-parameter-group-name mydbparametergroup ^
--parameters "ParameterName=babelfishpg_tds.product_version,ParameterValue=15.2.4000.1,ApplyMethod=immediate"
```

## Affected queries and parameter<a name="babelfish-guc-version-affects"></a>


|  Query/Parameter  |  Result  |  Affective time  | 
| --- | --- | --- | 
|   SELECT @@VERSION   |   Returns user defined SQL Server version \(babelfishpg\_tsql\.version value = Default\)   |   Immediately   | 
|   SELECT SERVERPROPERTY\('ProductVersion'\)   |   Returns user defined SQL Server version   |   Immediately   | 
|   SELECT SERVERPROPERTY\('ProductMajorVersion'\)   |   Returns Major Version of the user defined SQL Server version   |   Immediately   | 
|   VERSION tokens in PRELOGIN Response Message   |   Server returns PRELOGIN messages with user defined SQL Server version   |   Take effect when user creating a new session   | 
|   SQLServerVersion in LoginAck when using JDBC   |   DatabaseMetaData\.getDatabaseProductVersion\(\) returns user defined SQL Server version   |   Take effect when user creating a new session   | 

## Interface with babelfishpg\_tsql\.version GUC<a name="babelfish-guc-version-tsql"></a>

You can set the output of the @@VERSION using the parameters babelfishpg\_tsql\.version and babelfishpg\_tds\.product\_version\. The following examples show how these two GUC parameters interface\.
+ When babelfishpg\_tsql\.version GUC is "default" and babelfishpg\_tds\.product\_version is 15\.0\.2000\.8\.
  +  Output of @@version – 15\.0\.2000\.8\.
+ When babelfishpg\_tsql\.version GUC is set to 13\.0\.2000\.8 and babelfishpg\_tds\.product\_version GUC is 15\.0\.2000\.8\.
  + Output of @@version – 13\.0\.2000\.8\.