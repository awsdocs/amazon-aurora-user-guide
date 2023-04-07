# Saving data from an Amazon Aurora MySQL DB cluster into text files in an Amazon S3 bucket<a name="AuroraMySQL.Integrating.SaveIntoS3"></a><a name="save_into_s3"></a><a name="select_into_outfile"></a>

You can use the `SELECT INTO OUTFILE S3` statement to query data from an Amazon Aurora MySQL DB cluster and save it directly into text files stored in an Amazon S3 bucket\. You can use this functionality to skip bringing the data down to the client first, and then copying it from the client to Amazon S3\. The `LOAD DATA FROM S3` statement can use the files created by this statement to load data into an Aurora DB cluster\. For more information, see [Loading data into an Amazon Aurora MySQL DB cluster from text files in an Amazon S3 bucket](AuroraMySQL.Integrating.LoadFromS3.md)\.

If you are using encryption, the Amazon S3 bucket must be encrypted with an AWS managed key\. Currently, you can't save data to a bucket that is encrypted with a customer managed key\.

 This feature isn't supported for Aurora Serverless v1 DB clusters\. It is supported for Aurora Serverless v2 DB clusters\.

**Note**  
You can save DB cluster snapshot data to Amazon S3 using the AWS Management Console, AWS CLI, or Amazon RDS API\. For more information, see [Exporting DB cluster snapshot data to Amazon S3](aurora-export-snapshot.md)\.

## Giving Aurora MySQL access to Amazon S3<a name="AuroraMySQL.Integrating.SaveIntoS3.Authorize"></a>

Before you can save data into an Amazon S3 bucket, you must first give your Aurora MySQL DB cluster permission to access Amazon S3\.

**To give Aurora MySQL access to Amazon S3**

1. Create an AWS Identity and Access Management \(IAM\) policy that provides the bucket and object permissions that allow your Aurora MySQL DB cluster to access Amazon S3\. For instructions, see [Creating an IAM policy to access Amazon S3 resources](AuroraMySQL.Integrating.Authorizing.IAM.S3CreatePolicy.md)\.

1. Create an IAM role, and attach the IAM policy you created in [Creating an IAM policy to access Amazon S3 resources](AuroraMySQL.Integrating.Authorizing.IAM.S3CreatePolicy.md) to the new IAM role\. For instructions, see [Creating an IAM role to allow Amazon Aurora to access AWS services](AuroraMySQL.Integrating.Authorizing.IAM.CreateRole.md)\.

1. For Aurora MySQL version 2, set either the `aurora_select_into_s3_role` or `aws_default_s3_role` DB cluster parameter to the Amazon Resource Name \(ARN\) of the new IAM role\. If an IAM role isn't specified for `aurora_select_into_s3_role`, Aurora uses the IAM role specified in `aws_default_s3_role`\.

   For Aurora MySQL version 3, use `aws_default_s3_role`\.

   If the cluster is part of an Aurora global database, set this parameter for each Aurora cluster in the global database\.

   For more information about DB cluster parameters, see [Amazon Aurora DB cluster and DB instance parameters](USER_WorkingWithDBClusterParamGroups.md#Aurora.Managing.ParameterGroups)\.

1. To permit database users in an Aurora MySQL DB cluster to access Amazon S3, associate the role that you created in [Creating an IAM role to allow Amazon Aurora to access AWS services](AuroraMySQL.Integrating.Authorizing.IAM.CreateRole.md) with the DB cluster\.

   For an Aurora global database, associate the role with each Aurora cluster in the global database\.

   For information about associating an IAM role with a DB cluster, see [Associating an IAM role with an Amazon Aurora MySQL DB cluster](AuroraMySQL.Integrating.Authorizing.IAM.AddRoleToDBCluster.md)\.

1. Configure your Aurora MySQL DB cluster to allow outbound connections to Amazon S3\. For instructions, see [Enabling network communication from Amazon Aurora MySQL to other AWS services](AuroraMySQL.Integrating.Authorizing.Network.md)\. 

    For an Aurora global database, enable outbound connections for each Aurora cluster in the global database\. 

## Granting privileges to save data in Aurora MySQL<a name="AuroraMySQL.Integrating.SaveIntoS3.Grant"></a>

The database user that issues the `SELECT INTO OUTFILE S3` statement must have a specific role or privilege\. In Aurora MySQL version 3, you grant the `AWS_SELECT_S3_ACCESS` role\. In Aurora MySQL version 2, you grant the `SELECT INTO S3` privilege\. The administrative user for a DB cluster is granted the appropriate role or privilege by default\. You can grant the privilege to another user by using one of the following statements\.

 Use the following statement for Aurora MySQL version 3: 

```
GRANT AWS_SELECT_S3_ACCESS TO 'user'@'domain-or-ip-address'
```

**Tip**  
When you use the role technique in Aurora MySQL version 3, you also activate the role by using the `SET ROLE role_name` or `SET ROLE ALL` statement\. If you aren't familiar with the MySQL 8\.0 role system, you can learn more in [Role\-based privilege model](Aurora.AuroraMySQL.Compare-80-v3.md#AuroraMySQL.privilege-model)\. You can also find more details in [Using roles](https://dev.mysql.com/doc/refman/8.0/en/roles.html) in the *MySQL Reference Manual*\.  
This only applies to the current active session\. When you reconnect, you have to run the `SET ROLE` statement again to grant privileges\. For more information, see [SET ROLE statement](https://dev.mysql.com/doc/refman/8.0/en/set-role.html) in the *MySQL Reference Manual*\.  
You can also use the `activate_all_roles_on_login` DB cluster parameter to automatically activate all roles when a user connects to a DB instance\. When this parameter is set, you don't have to call the SET ROLE statement explicitly to activate a role\. For more information, see [activate\_all\_roles\_on\_login](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_activate_all_roles_on_login) in the *MySQL Reference Manual*\.

Use the following statement for Aurora MySQL version 2:

```
GRANT SELECT INTO S3 ON *.* TO 'user'@'domain-or-ip-address'
```

The `AWS_SELECT_S3_ACCESS` role and `SELECT INTO S3` privilege are specific to Amazon Aurora MySQL and are not available for MySQL databases or RDS for MySQL DB instances\. If you have set up replication between an Aurora MySQL DB cluster as the replication master and a MySQL database as the replication client, then the `GRANT` statement for the role or privilege causes replication to stop with an error\. You can safely skip the error to resume replication\. To skip the error on an RDS for MySQL DB instance, use the [mysql\_rds\_skip\_repl\_error](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/mysql_rds_skip_repl_error.html) procedure\. To skip the error on an external MySQL database, use the [slave\_skip\_errors](https://dev.mysql.com/doc/refman/5.7/en/replication-options-replica.html#sysvar_slave_skip_errors) system variable \(Aurora MySQL version 2\) or [replica\_skip\_errors](https://dev.mysql.com/doc/refman/8.0/en/replication-options-replica.html#sysvar_replica_skip_errors) system variable \(Aurora MySQL version 3\)\.

## Specifying a path to an Amazon S3 bucket<a name="AuroraMySQL.Integrating.SaveIntoS3.URI"></a>

The syntax for specifying a path to store the data and manifest files on an Amazon S3 bucket is similar to that used in the `LOAD DATA FROM S3 PREFIX` statement, as shown following\.

```
s3-region://bucket-name/file-prefix
```

The path includes the following values:
+ `region` \(optional\) – The AWS Region that contains the Amazon S3 bucket to save the data into\. This value is optional\. If you don't specify a `region` value, then Aurora saves your files into Amazon S3 in the same region as your DB cluster\.
+ `bucket-name` – The name of the Amazon S3 bucket to save the data into\. Object prefixes that identify a virtual folder path are supported\.
+ `file-prefix` – The Amazon S3 object prefix that identifies the files to be saved in Amazon S3\. 

The data files created by the `SELECT INTO OUTFILE S3` statement use the following path, in which *00000* represents a 5\-digit, zero\-based integer number\.

```
s3-region://bucket-name/file-prefix.part_00000
```

For example, suppose that a `SELECT INTO OUTFILE S3` statement specifies `s3-us-west-2://bucket/prefix` as the path in which to store data files and creates three data files\. The specified Amazon S3 bucket contains the following data files\.
+ s3\-us\-west\-2://bucket/prefix\.part\_00000
+ s3\-us\-west\-2://bucket/prefix\.part\_00001
+ s3\-us\-west\-2://bucket/prefix\.part\_00002

## Creating a manifest to list data files<a name="AuroraMySQL.Integrating.SaveIntoS3.Manifest"></a>

You can use the `SELECT INTO OUTFILE S3` statement with the `MANIFEST ON` option to create a manifest file in JSON format that lists the text files created by the statement\. The `LOAD DATA FROM S3` statement can use the manifest file to load the data files back into an Aurora MySQL DB cluster\. For more information about using a manifest to load data files from Amazon S3 into an Aurora MySQL DB cluster, see [Using a manifest to specify data files to load](AuroraMySQL.Integrating.LoadFromS3.md#AuroraMySQL.Integrating.LoadFromS3.Manifest)\. 

The data files included in the manifest created by the `SELECT INTO OUTFILE S3` statement are listed in the order that they're created by the statement\. For example, suppose that a `SELECT INTO OUTFILE S3` statement specified `s3-us-west-2://bucket/prefix` as the path in which to store data files and creates three data files and a manifest file\. The specified Amazon S3 bucket contains a manifest file named `s3-us-west-2://bucket/prefix.manifest`, that contains the following information\.

```
{
  "entries": [
    {
      "url":"s3-us-west-2://bucket/prefix.part_00000"
    },
    {
      "url":"s3-us-west-2://bucket/prefix.part_00001"
    },
    {
      "url":"s3-us-west-2://bucket/prefix.part_00002"
    }
  ]
}
```

## SELECT INTO OUTFILE S3<a name="AuroraMySQL.Integrating.SaveIntoS3.Statement"></a>

You can use the `SELECT INTO OUTFILE S3` statement to query data from a DB cluster and save it directly into delimited text files stored in an Amazon S3 bucket\. Compressed files are not supported\. Encrypted files are supported starting in Aurora MySQL 2\.09\.0\.

### Syntax<a name="AuroraMySQL.Integrating.SaveIntoS3.Statement.Syntax"></a>

```
SELECT
    [ALL | DISTINCT | DISTINCTROW ]
        [HIGH_PRIORITY]
        [STRAIGHT_JOIN]
        [SQL_SMALL_RESULT] [SQL_BIG_RESULT] [SQL_BUFFER_RESULT]
        [SQL_CACHE | SQL_NO_CACHE] [SQL_CALC_FOUND_ROWS]
    select_expr [, select_expr ...]
    [FROM table_references
        [PARTITION partition_list]
    [WHERE where_condition]
    [GROUP BY {col_name | expr | position}
        [ASC | DESC], ... [WITH ROLLUP]]
    [HAVING where_condition]
    [ORDER BY {col_name | expr | position}
         [ASC | DESC], ...]
    [LIMIT {[offset,] row_count | row_count OFFSET offset}]
INTO OUTFILE S3 's3_uri'
[CHARACTER SET charset_name]
    [export_options]
    [MANIFEST {ON | OFF}]
    [OVERWRITE {ON | OFF}]

export_options:
    [FORMAT {CSV|TEXT} [HEADER]]
    [{FIELDS | COLUMNS}
        [TERMINATED BY 'string']
        [[OPTIONALLY] ENCLOSED BY 'char']
        [ESCAPED BY 'char']
    ]
    [LINES
        [STARTING BY 'string']
        [TERMINATED BY 'string']
]
```

### Parameters<a name="AuroraMySQL.Integrating.SaveIntoS3.Statement.Parameters"></a>

Following, you can find a list of the required and optional parameters used by the `SELECT INTO OUTFILE S3` statement that are specific to Aurora\. 
+ **s3\-uri** – Specifies the URI for an Amazon S3 prefix to use\. Specify the URI using the syntax described in [Specifying a path to an Amazon S3 bucket](#AuroraMySQL.Integrating.SaveIntoS3.URI)\.
+  **FORMAT \{CSV\|TEXT\} \[HEADER\]** – Optionally saves the data in CSV format\. This syntax is available in Aurora MySQL version 2\.07\.0 and later\. 

   The `TEXT` option is the default and produces the existing MySQL export format\. 

   The `CSV` option produces comma\-separated data values\. The CSV format follows the specification in [RFC\-4180](https://tools.ietf.org/html/rfc4180)\. If you specify the optional keyword `HEADER`, the output file contains one header line\. The labels in the header line correspond to the column names from the `SELECT` statement\. You can use the CSV files for training data models for use with AWS ML services\. For more information about using exported Aurora data with AWS ML services, see [Exporting data to Amazon S3 for SageMaker model training \(Advanced\)](mysql-ml.md#exporting-data-to-s3-for-model-training)\. 
+ **MANIFEST \{ON \| OFF\}** – Indicates whether a manifest file is created in Amazon S3\. The manifest file is a JavaScript Object Notation \(JSON\) file that can be used to load data into an Aurora DB cluster with the `LOAD DATA FROM S3 MANIFEST` statement\. For more information about `LOAD DATA FROM S3 MANIFEST`, see [Loading data into an Amazon Aurora MySQL DB cluster from text files in an Amazon S3 bucket](AuroraMySQL.Integrating.LoadFromS3.md)\.

  If `MANIFEST ON` is specified in the query, the manifest file is created in Amazon S3 after all data files have been created and uploaded\. The manifest file is created using the following path: 

  ```
  s3-region://bucket-name/file-prefix.manifest
  ```

  For more information about the format of the manifest file's contents, see [Creating a manifest to list data files](#AuroraMySQL.Integrating.SaveIntoS3.Manifest)\.
+ **OVERWRITE \{ON \| OFF\}** – Indicates whether existing files in the specified Amazon S3 bucket are overwritten\. If `OVERWRITE ON` is specified, existing files that match the file prefix in the URI specified in `s3-uri`are overwritten\. Otherwise, an error occurs\.

You can find more details about other parameters in [SELECT statement](https://dev.mysql.com/doc/refman/8.0/en/select.html) and [LOAD DATA statement](https://dev.mysql.com/doc/refman/8.0/en/load-data.html), in the MySQL documentation\.

### Considerations<a name="AuroraMySQL.Integrating.SaveIntoS3.Considerations"></a>

The number of files written to the Amazon S3 bucket depends on the amount of data selected by the `SELECT INTO OUTFILE S3` statement and the file size threshold for Aurora MySQL\. The default file size threshold is 6 gigabytes \(GB\)\. If the data selected by the statement is less than the file size threshold, a single file is created; otherwise, multiple files are created\. Other considerations for files created by this statement include the following:
+ Aurora MySQL guarantees that rows in data files are not split across file boundaries\. For multiple files, the size of every data file except the last is typically close to the file size threshold\. However, occasionally staying under the file size threshold results in a row being split across two data files\. In this case, Aurora MySQL creates a data file that keeps the row intact, but might be larger than the file size threshold\. 
+ Because each `SELECT` statement in Aurora MySQL runs as an atomic transaction, a `SELECT INTO OUTFILE S3` statement that selects a large data set might run for some time\. If the statement fails for any reason, you might need to start over and issue the statement again\. If the statement fails, however, files already uploaded to Amazon S3 remain in the specified Amazon S3 bucket\. You can use another statement to upload the remaining data instead of starting over again\.
+ If the amount of data to be selected is large \(more than 25 GB\), we recommend that you use multiple `SELECT INTO OUTFILE S3` statements to save the data to Amazon S3\. Each statement should select a different portion of the data to be saved, and also specify a different `file_prefix` in the `s3-uri` parameter to use when saving the data files\. Partitioning the data to be selected with multiple statements makes it easier to recover from an error in one statement\. If an error occurs for one statement, only a portion of data needs to be re\-selected and uploaded to Amazon S3\. Using multiple statements also helps to avoid a single long\-running transaction, which can improve performance\. 
+ If multiple `SELECT INTO OUTFILE S3` statements that use the same `file_prefix` in the `s3-uri` parameter run in parallel to select data into Amazon S3, the behavior is undefined\.
+ Metadata, such as table schema or file metadata, is not uploaded by Aurora MySQL to Amazon S3\.
+ In some cases, you might re\-run a `SELECT INTO OUTFILE S3` query, such as to recover from a failure\. In these cases, you must either remove any existing data files in the Amazon S3 bucket with the same file prefix specified in `s3-uri`, or include `OVERWRITE ON` in the `SELECT INTO OUTFILE S3` query\.

The `SELECT INTO OUTFILE S3` statement returns a typical MySQL error number and response on success or failure\. If you don't have access to the MySQL error number and response, the easiest way to determine when it's done is by specifying `MANIFEST ON` in the statement\. The manifest file is the last file written by the statement\. In other words, if you have a manifest file, the statement has completed\.

Currently, there's no way to directly monitor the progress of the `SELECT INTO OUTFILE S3` statement while it runs\. However, suppose that you're writing a large amount of data from Aurora MySQL to Amazon S3 using this statement, and you know the size of the data selected by the statement\. In this case, you can estimate progress by monitoring the creation of data files in Amazon S3\.

To do so, you can use the fact that a data file is created in the specified Amazon S3 bucket for about every 6 GB of data selected by the statement\. Divide the size of the data selected by 6 GB to get the estimated number of data files to create\. You can then estimate the progress of the statement by monitoring the number of files uploaded to Amazon S3 while the statement runs\.

### Examples<a name="AuroraMySQL.Integrating.SaveIntoS3.Examples"></a>

The following statement selects all of the data in the `employees` table and saves the data into an Amazon S3 bucket that is in a different region from the Aurora MySQL DB cluster\. The statement creates data files in which each field is terminated by a comma \(`,`\) character and each row is terminated by a newline \(`\n`\) character\. The statement returns an error if files that match the `sample_employee_data` file prefix exist in the specified Amazon S3 bucket\.

```
SELECT * FROM employees INTO OUTFILE S3 's3-us-west-2://aurora-select-into-s3-pdx/sample_employee_data'
    FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n';
```

The following statement selects all of the data in the `employees` table and saves the data into an Amazon S3 bucket that is in the same region as the Aurora MySQL DB cluster\. The statement creates data files in which each field is terminated by a comma \(`,`\) character and each row is terminated by a newline \(`\n`\) character, and also a manifest file\. The statement returns an error if files that match the `sample_employee_data` file prefix exist in the specified Amazon S3 bucket\.

```
SELECT * FROM employees INTO OUTFILE S3 's3://aurora-select-into-s3-pdx/sample_employee_data'
    FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    MANIFEST ON;
```

The following statement selects all of the data in the `employees` table and saves the data into an Amazon S3 bucket that is in a different region from the Aurora DB cluster\. The statement creates data files in which each field is terminated by a comma \(`,`\) character and each row is terminated by a newline \(`\n`\) character\. The statement overwrites any existing files that match the `sample_employee_data` file prefix in the specified Amazon S3 bucket\.

```
SELECT * FROM employees INTO OUTFILE S3 's3-us-west-2://aurora-select-into-s3-pdx/sample_employee_data'
    FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    OVERWRITE ON;
```

The following statement selects all of the data in the `employees` table and saves the data into an Amazon S3 bucket that is in the same region as the Aurora MySQL DB cluster\. The statement creates data files in which each field is terminated by a comma \(`,`\) character and each row is terminated by a newline \(`\n`\) character, and also a manifest file\. The statement overwrites any existing files that match the `sample_employee_data` file prefix in the specified Amazon S3 bucket\.

```
SELECT * FROM employees INTO OUTFILE S3 's3://aurora-select-into-s3-pdx/sample_employee_data'
    FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    MANIFEST ON
    OVERWRITE ON;
```

## Related topics<a name="AuroraMySQL.Integrating.SaveIntoS3.RelatedTopics"></a>
+ [Integrating Aurora with other AWS services](Aurora.Integrating.md)
+ [Loading data into an Amazon Aurora MySQL DB cluster from text files in an Amazon S3 bucket](AuroraMySQL.Integrating.LoadFromS3.md)
+ [Managing an Amazon Aurora DB cluster](CHAP_Aurora.md)
+ [Migrating data to an Amazon Aurora DB cluster](Aurora.Migrate.md)