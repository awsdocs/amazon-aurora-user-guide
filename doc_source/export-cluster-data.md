# Exporting DB cluster data to Amazon S3<a name="export-cluster-data"></a>

You can export data from a live Amazon Aurora DB cluster to an Amazon S3 bucket\. The export process runs in the background and doesn't affect the performance of your active DB cluster\.

By default, all data in the DB cluster is exported\. However, you can choose to export specific sets of databases, schemas, or tables\.

Amazon Aurora clones the DB cluster, extracts data from the clone, and stores the data in an Amazon S3 bucket\. The data is stored in an Apache Parquet format that is compressed and consistent\. Individual Parquet files are usually \~20 GB in size\.

The faster performance that you can get with exporting snapshot data for Aurora MySQL version 2 and version 3 doesn't apply to exporting DB cluster data\. For more information, see [Exporting DB cluster snapshot data to Amazon S3](aurora-export-snapshot.md)\.

You're charged for exporting the entire DB cluster, whether you export all or partial data\. For more information, see the [Amazon Aurora pricing page](https://aws.amazon.com/rds/aurora/pricing/)\.

After the data is exported, you can analyze the exported data directly through tools like Amazon Athena or Amazon Redshift Spectrum\. For more information on using Athena to read Parquet data, see [Parquet SerDe](https://docs.aws.amazon.com/athena/latest/ug/parquet-serde.html) in the *Amazon Athena User Guide*\. For more information on using Redshift Spectrum to read Parquet data, see [COPY from columnar data formats](https://docs.aws.amazon.com/redshift/latest/dg/copy-usage_notes-copy-from-columnar.html) in the *Amazon Redshift Database Developer Guide*\.

Feature availability and support varies across specific versions of each database engine and across AWS Regions\. For more information on version and Region availability of exporting DB cluster data to S3, see [Exporting cluster data to Amazon S3](Concepts.Aurora_Fea_Regions_DB-eng.Feature.ExportClusterToS3.md)\.

**Topics**
+ [Limitations](#export-cluster-data.Limits)
+ [Overview of exporting DB cluster data](#export-cluster-data.Overview)
+ [Setting up access to an Amazon S3 bucket](#export-cluster-data.Setup)
+ [Exporting DB cluster data to an Amazon S3 bucket](#export-cluster-data.Exporting)
+ [Monitoring DB cluster export tasks](#export-cluster-data.Monitoring)
+ [Canceling a DB cluster export task](#export-cluster-data.Canceling)
+ [Failure messages for Amazon S3 export tasks](#export-cluster-data.failure-msg)
+ [Troubleshooting PostgreSQL permissions errors](#export-cluster-data.postgres-permissions)
+ [File naming convention](#export-cluster-data.FileNames)
+ [Data conversion and storage format](#export-cluster-data.data-types)

## Limitations<a name="export-cluster-data.Limits"></a>

Exporting DB cluster data to Amazon S3 has the following limitations:
+ You can't run multiple export tasks for the same DB cluster simultaneously\. This applies to both full and partial exports\.
+ Aurora Serverless v1 DB clusters don't support exports to S3\.
+ Aurora MySQL and Aurora PostgreSQL support exports to S3 only for the provisioned engine mode\.
+ DB clusters can have at most 15 clones\. Because of this limit, if your cluster already has 15 clones, the export task copies the DB cluster instead of cloning it\. Copying causes the export task to take longer\. For more information on cloning, see [Cloning a volume for an Amazon Aurora DB cluster](Aurora.Managing.Clone.md)\.
+ The following characters in the S3 file path are converted to underscores \(\_\) during export:

  ```
  \ ` " (space)
  ```
+ If a database, schema, or table has characters in its name other than the following, partial export isn't supported\. However, you can export the entire DB cluster\.
  + Latin letters \(A–Z\)
  + Digits \(0–9\)
  + Dollar symbol \($\)
  + Underscore \(\_\)
+ Spaces \( \) and certain characters aren't supported in database table column names\. Tables with the following characters in column names are skipped during export:

  ```
  , ; { } ( ) \n \t = (space)
  ```
+ Tables with slashes \(/\) in their names are skipped during export\.
+ Aurora PostgreSQL temporary and unlogged tables are skipped during export\.
+ If the data contains a large object, such as a BLOB or CLOB, that is close to or greater than 500 MB, then the export fails\.
+ If a table contains a large row that is close to or greater than 2 GB, then the table is skipped during export\.
+ We strongly recommend that you use a unique name for each export task\. If you don't use a unique task name, you might receive the following error message:

  ExportTaskAlreadyExistsFault: An error occurred \(ExportTaskAlreadyExists\) when calling the StartExportTask operation: The export task with the ID *xxxxx* already exists\.

## Overview of exporting DB cluster data<a name="export-cluster-data.Overview"></a>

You use the following process to export DB cluster data to an Amazon S3 bucket\. For more details, see the following sections\.

1. Identify the DB cluster whose data you want to export\.

1. Set up access to the Amazon S3 bucket\.

   A *bucket* is a container for Amazon S3 objects or files\. To provide the information to access a bucket, take the following steps:

   1. Identify the S3 bucket where the DB cluster data is to be exported\. The S3 bucket must be in the same AWS Region as the DB cluster\. For more information, see [Identifying the Amazon S3 bucket for export](#export-cluster-data.SetupBucket)\.

   1. Create an AWS Identity and Access Management \(IAM\) role that grants the DB cluster export task access to the S3 bucket\. For more information, see [Providing access to an Amazon S3 bucket using an IAM role](#export-cluster-data.SetupIAMRole)\.

1. Create a symmetric encryption AWS KMS key for the server\-side encryption\. The KMS key is used by the cluster export task to set up AWS KMS server\-side encryption when writing the export data to S3\. The KMS key policy must include both the `kms:Encrypt` and `kms:Decrypt` permissions\. For more information on using KMS keys in Amazon Aurora, see [AWS KMS key management](Overview.Encryption.Keys.md)\.

   If you have a deny statement in your KMS key policy, make sure to explicitly exclude the AWS service principal `export.rds.amazonaws.com`\.

   You can use a KMS key within your AWS account, or you can use a cross\-account KMS key\. For more information, see [Using a cross\-account AWS KMS key](aurora-export-snapshot.md#aurora-export-snapshot.CMK)\.

1. Export the DB cluster to Amazon S3 using the console or the `start-export-task` CLI command\. For more information, see [Exporting DB cluster data to an Amazon S3 bucket](#export-cluster-data.Exporting)\. 

1. To access your exported data in the Amazon S3 bucket, see [Uploading, downloading, and managing objects](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/upload-download-objects.html) in the *Amazon Simple Storage Service User Guide*\.

## Setting up access to an Amazon S3 bucket<a name="export-cluster-data.Setup"></a>

You identify the Amazon S3 bucket, then you give the DB cluster export task permission to access it\.

**Topics**
+ [Identifying the Amazon S3 bucket for export](#export-cluster-data.SetupBucket)
+ [Providing access to an Amazon S3 bucket using an IAM role](#export-cluster-data.SetupIAMRole)
+ [Using a cross\-account Amazon S3 bucket](#export-cluster-data.Setup.XAcctBucket)

### Identifying the Amazon S3 bucket for export<a name="export-cluster-data.SetupBucket"></a>

Identify the Amazon S3 bucket to export the DB cluster data to\. Use an existing S3 bucket or create a new S3 bucket\.

**Note**  
The S3 bucket must be in the same AWS Region as the DB cluster\.

For more information about working with Amazon S3 buckets, see the following in the *Amazon Simple Storage Service User Guide*:
+ [ How do I view the properties for an S3 bucket?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/view-bucket-properties.html)
+ [ How do I enable default encryption for an Amazon S3 bucket?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/default-bucket-encryption.html)
+ [ How do I create an S3 bucket?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/create-bucket.html)

### Providing access to an Amazon S3 bucket using an IAM role<a name="export-cluster-data.SetupIAMRole"></a>

Before you export DB cluster data to Amazon S3, give the export tasks write\-access permission to the Amazon S3 bucket\.

To grant this permission, create an IAM policy that provides access to the bucket, then create an IAM role and attach the policy to the role\. Later, you can assign the IAM role to your DB cluster export task\.

**Important**  
If you plan to use the AWS Management Console to export your DB cluster, you can choose to create the IAM policy and the role automatically when you export the DB cluster\. For instructions, see [Exporting DB cluster data to an Amazon S3 bucket](#export-cluster-data.Exporting)\.

**To give tasks access to Amazon S3**

1. Create an IAM policy\. This policy provides the bucket and object permissions that allow your DB cluster export task to access Amazon S3\.

   In the policy, include the following required actions to allow the transfer of files from Amazon Aurora to an S3 bucket:
   + `s3:PutObject*`
   + `s3:GetObject*` 
   + `s3:ListBucket` 
   + `s3:DeleteObject*`
   +  `s3:GetBucketLocation`

   In the policy, include the following resources to identify the S3 bucket and objects in the bucket\. The following list of resources shows the Amazon Resource Name \(ARN\) format for accessing Amazon S3\.
   + `arn:aws:s3:::your-s3-bucket`
   + `arn:aws:s3:::your-s3-bucket/*`

   For more information about creating an IAM policy for Amazon Aurora, see [Creating and using an IAM policy for IAM database access](UsingWithRDS.IAMDBAuth.IAMPolicy.md)\. See also [Tutorial: Create and attach your first customer managed policy](https://docs.aws.amazon.com/IAM/latest/UserGuide/tutorial_managed-policies.html) in the *IAM User Guide*\.

   The following AWS CLI command creates an IAM policy named `ExportPolicy` with these options\. It grants access to a bucket named `your-s3-bucket`\.
**Note**  
After you create the policy, note the ARN of the policy\. You need the ARN for a subsequent step when you attach the policy to an IAM role\.

   ```
   aws iam create-policy  --policy-name ExportPolicy --policy-document '{
       "Version": "2012-10-17",
       "Statement": [
           {
               "Sid": "ExportPolicy",
               "Effect": "Allow",
               "Action": [
                   "s3:PutObject*",
                   "s3:ListBucket",
                   "s3:GetObject*",
                   "s3:DeleteObject*",
                   "s3:GetBucketLocation"
               ],
               "Resource": [
                   "arn:aws:s3:::your-s3-bucket",
                   "arn:aws:s3:::your-s3-bucket/*"
               ]
           }
       ]
   }'
   ```

1. Create an IAM role, so that Aurora can assume this IAM role on your behalf to access your Amazon S3 buckets\. For more information, see [Creating a role to delegate permissions to an IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-user.html) in the *IAM User Guide*\.

   The following example shows using the AWS CLI command to create a role named `rds-s3-export-role`\.

   ```
   aws iam create-role  --role-name rds-s3-export-role  --assume-role-policy-document '{
        "Version": "2012-10-17",
        "Statement": [
          {
            "Effect": "Allow",
            "Principal": {
               "Service": "export.rds.amazonaws.com"
             },
            "Action": "sts:AssumeRole"
          }
        ] 
      }'
   ```

1. Attach the IAM policy that you created to the IAM role that you created\.

   The following AWS CLI command attaches the policy created earlier to the role named `rds-s3-export-role`\. Replace `your-policy-arn` with the policy ARN that you noted in an earlier step\.

   ```
   aws iam attach-role-policy  --policy-arn your-policy-arn  --role-name rds-s3-export-role
   ```

### Using a cross\-account Amazon S3 bucket<a name="export-cluster-data.Setup.XAcctBucket"></a>

You can use S3 buckets across AWS accounts\. For more information, see [Using a cross\-account Amazon S3 bucket](aurora-export-snapshot.md#aurora-export-snapshot.Setup.XAcctBucket)\.

## Exporting DB cluster data to an Amazon S3 bucket<a name="export-cluster-data.Exporting"></a>

You can have up to five concurrent DB cluster export tasks in progress per AWS account\.

**Note**  
Exporting DB cluster data can take a while depending on your database type and size\. The export task first clones and scales the entire database before extracting the data to Amazon S3\. The task's progress during this phase displays as **Starting**\. When the task switches to exporting data to S3, progress displays as **In progress**\.  
The time it takes for the export to complete depends on the data stored in the database\. For example, tables with well\-distributed numeric primary key or index columns export the fastest\. Tables that don't contain a column suitable for partitioning and tables with only one index on a string\-based column take longer because the export uses a slower single\-threaded process\.

You can export DB cluster data to Amazon S3 using the AWS Management Console, the AWS CLI, or the RDS API\.

If you use a Lambda function to export the DB cluster data, add the `kms:DescribeKey` action to the Lambda function policy\. For more information, see [AWS Lambda permissions](https://docs.aws.amazon.com/lambda/latest/dg/lambda-permissions.html)\.

### Console<a name="export-cluster-data.ExportConsole"></a>

The **Export to Amazon S3** console option appears only for DB clusters that can be exported to Amazon S3\. A DB cluster might not be available for export because of the following reasons:
+ The DB engine isn't supported for S3 export\.
+ The DB cluster version isn't supported for S3 export\.
+ S3 export isn't supported in the AWS Region where the DB cluster was created\.

**To export DB cluster data**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the DB cluster whose data you want to export\.

1. For **Actions**, choose **Export to Amazon S3**\.

   The **Export to Amazon S3** window appears\.

1. For **Export identifier**, enter a name to identify the export task\. This value is also used for the name of the file created in the S3 bucket\.

1. Choose the data to be exported:
   + Choose **All** to export all data in the DB cluster\.
   + Choose **Partial** to export specific parts of the DB cluster\. To identify which parts of the cluster to export, enter one or more databases, schemas, or tables for **Identifiers**, separated by spaces\.

     Use the following format:

     ```
     database[.schema][.table] database2[.schema2][.table2] ... databasen[.scheman][.tablen]
     ```

     For example:

     ```
     mydatabase mydatabase2.myschema1 mydatabase2.myschema2.mytable1 mydatabase2.myschema2.mytable2
     ```

1. For **S3 bucket**, choose the bucket to export to\.

   To assign the exported data to a folder path in the S3 bucket, enter the optional path for **S3 prefix**\.

1. For **IAM role**, either choose a role that grants you write access to your chosen S3 bucket, or create a new role\.
   + If you created a role by following the steps in [Providing access to an Amazon S3 bucket using an IAM role](#export-cluster-data.SetupIAMRole), choose that role\.
   + If you didn't create a role that grants you write access to your chosen S3 bucket, then choose **Create a new role** to create the role automatically\. Next, enter a name for the role in **IAM role name**\.

1. For **KMS key**, enter the ARN for the key to use for encrypting the exported data\.

1. Choose **Export to Amazon S3**\.

### AWS CLI<a name="export-cluster-data.ExportCLI"></a>

To export DB cluster data to Amazon S3 using the AWS CLI, use the [start\-export\-task](https://docs.aws.amazon.com/cli/latest/reference/rds/start-export-task.html) command with the following required options:
+ `--export-task-identifier`
+ `--source-arn` – the Amazon Resource Name \(ARN\) of the DB cluster
+ `--s3-bucket-name`
+ `--iam-role-arn`
+ `--kms-key-id`

In the following examples, the export task is named *my\-cluster\-export*, which exports the data to an S3 bucket named *my\-export\-bucket*\.

**Example**  
For Linux, macOS, or Unix:  

```
1. aws rds start-export-task \
2.     --export-task-identifier my-cluster-export \
3.     --source-arn arn:aws:rds:us-west-2:123456789012:cluster:my-cluster \
4.     --s3-bucket-name my-export-bucket \
5.     --iam-role-arn iam-role \
6.     --kms-key-id my-key
```
For Windows:  

```
1. aws rds start-export-task ^
2.     --export-task-identifier my-DB-cluster-export ^
3.     --source-arn arn:aws:rds:us-west-2:123456789012:cluster:my-cluster ^
4.     --s3-bucket-name my-export-bucket ^
5.     --iam-role-arn iam-role ^
6.     --kms-key-id my-key
```
Sample output follows\.  

```
{
    "ExportTaskIdentifier": "my-cluster-export",
    "SourceArn": "arn:aws:rds:us-west-2:123456789012:cluster:my-cluster",
    "S3Bucket": "my-export-bucket",
    "IamRoleArn": "arn:aws:iam:123456789012:role/ExportTest",
    "KmsKeyId": "my-key",
    "Status": "STARTING",
    "PercentProgress": 0,
    "TotalExtractedDataInGB": 0,
}
```
To provide a folder path in the S3 bucket for the DB cluster export, include the `--s3-prefix` option in the [start\-export\-task](https://docs.aws.amazon.com/cli/latest/reference/rds/start-export-task.html) command\.

### RDS API<a name="export-cluster-data.ExportAPI"></a>

To export DB cluster data to Amazon S3 using the Amazon RDS API, use the [StartExportTask](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_StartExportTask.html) operation with the following required parameters:
+ `ExportTaskIdentifier`
+ `SourceArn` – the ARN of the DB cluster
+ `S3BucketName`
+ `IamRoleArn`
+ `KmsKeyId`

## Monitoring DB cluster export tasks<a name="export-cluster-data.Monitoring"></a>

You can monitor DB cluster exports using the AWS Management Console, the AWS CLI, or the RDS API\.

### Console<a name="export-cluster-data.MonitorConsole"></a>

**To monitor DB cluster exports**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Exports in Amazon S3**\.

   DB cluster exports are indicated in the **Source type** column\. Export status is displayed in the **Status** column\.

1. To view detailed information about a specific DB cluster export, choose the export task\.

### AWS CLI<a name="export-cluster-data.MonitorCLI"></a>

To monitor DB cluster export tasks using the AWS CLI, use the [ describe\-export\-tasks](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-export-tasks.html) command\.

The following example shows how to display current information about all of your DB cluster exports\.

**Example**  

```
 1. aws rds describe-export-tasks
 2. 
 3. {
 4.     "ExportTasks": [
 5.         {
 6.             "Status": "CANCELED",
 7.             "TaskEndTime": "2022-11-01T17:36:46.961Z",
 8.             "S3Prefix": "something",
 9.             "S3Bucket": "examplebucket",
10.             "PercentProgress": 0,
11.             "KmsKeyId": "arn:aws:kms:us-west-2:123456789012:key/K7MDENG/bPxRfiCYEXAMPLEKEY",
12.             "ExportTaskIdentifier": "anewtest",
13.             "IamRoleArn": "arn:aws:iam::123456789012:role/export-to-s3",
14.             "TotalExtractedDataInGB": 0,
15.             "SourceArn": "arn:aws:rds:us-west-2:123456789012:cluster:parameter-groups-test"
16.         },
17. {
18.             "Status": "COMPLETE",
19.             "TaskStartTime": "2022-10-31T20:58:06.998Z",
20.             "TaskEndTime": "2022-10-31T21:37:28.312Z",
21.             "WarningMessage": "{\"skippedTables\":[],\"skippedObjectives\":[],\"general\":[{\"reason\":\"FAILED_TO_EXTRACT_TABLES_LIST_FOR_DATABASE\"}]}",
22.             "S3Prefix": "",
23.             "S3Bucket": "examplebucket1",
24.             "PercentProgress": 100,
25.             "KmsKeyId": "arn:aws:kms:us-west-2:123456789012:key/2Zp9Utk/h3yCo8nvbEXAMPLEKEY",
26.             "ExportTaskIdentifier": "thursday-events-test", 
27.             "IamRoleArn": "arn:aws:iam::123456789012:role/export-to-s3",
28.             "TotalExtractedDataInGB": 263,
29.             "SourceArn": "arn:aws:rds:us-west-2:123456789012:cluster:example-1-2019-10-31-06-44"
30.         },
31.         {
32.             "Status": "FAILED",
33.             "TaskEndTime": "2022-10-31T02:12:36.409Z",
34.             "FailureCause": "The S3 bucket examplebucket2 isn't located in the current AWS Region. Please, review your S3 bucket name and retry the export.",
35.             "S3Prefix": "",
36.             "S3Bucket": "examplebucket2",
37.             "PercentProgress": 0,
38.             "KmsKeyId": "arn:aws:kms:us-west-2:123456789012:key/2Zp9Utk/h3yCo8nvbEXAMPLEKEY",
39.             "ExportTaskIdentifier": "wednesday-afternoon-test",
40.             "IamRoleArn": "arn:aws:iam::123456789012:role/export-to-s3",
41.             "TotalExtractedDataInGB": 0,
42.             "SourceArn": "arn:aws:rds:us-west-2:123456789012:cluster:example-1-2019-10-30-06-45"
43.         }
44.     ]
45. }
```
To display information about a specific export task, include the `--export-task-identifier` option with the `describe-export-tasks` command\. To filter the output, include the `--Filters` option\. For more options, see the [ describe\-export\-tasks](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-export-tasks.html) command\.

### RDS API<a name="export-cluster-data.MonitorAPI"></a>

To display information about DB cluster exports using the Amazon RDS API, use the [DescribeExportTasks](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeExportTasks.html) operation\.

To track completion of the export workflow or to initiate another workflow, you can subscribe to Amazon Simple Notification Service topics\. For more information on Amazon SNS, see [Working with Amazon RDS event notification](USER_Events.md)\.

## Canceling a DB cluster export task<a name="export-cluster-data.Canceling"></a>

You can cancel a DB cluster export task using the AWS Management Console, the AWS CLI, or the RDS API\.

**Note**  
Canceling an export task doesn't remove any data that was exported to Amazon S3\. For information about how to delete the data using the console, see [ How do I delete objects from an S3 bucket?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/delete-objects.html) To delete the data using the CLI, use the [delete\-object](https://docs.aws.amazon.com/cli/latest/reference/s3api/delete-object.html) command\.

### Console<a name="export-cluster-data.CancelConsole"></a>

**To cancel a DB cluster export task**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Exports in Amazon S3**\.

   DB cluster exports are indicated in the **Source type** column\. Export status is displayed in the **Status** column\.

1. Choose the export task that you want to cancel\.

1. Choose **Cancel**\.

1. Choose **Cancel export task** on the confirmation page\.

 

### AWS CLI<a name="export-cluster-data.CancelCLI"></a>

To cancel an export task using the AWS CLI, use the [cancel\-export\-task](https://docs.aws.amazon.com/cli/latest/reference/rds/cancel-export-task.html) command\. The command requires the `--export-task-identifier` option\.

**Example**  

```
 1. aws rds cancel-export-task --export-task-identifier my-export
 2. {
 3.     "Status": "CANCELING",
 4.     "S3Prefix": "",
 5.     "S3Bucket": "examplebucket",
 6.     "PercentProgress": 0,
 7.     "KmsKeyId": "arn:aws:kms:us-west-2:123456789012:key/K7MDENG/bPxRfiCYEXAMPLEKEY",
 8.     "ExportTaskIdentifier": "my-export",
 9.     "IamRoleArn": "arn:aws:iam::123456789012:role/export-to-s3",
10.     "TotalExtractedDataInGB": 0,
11.     "SourceArn": "arn:aws:rds:us-west-2:123456789012:cluster:export-example-1"
12. }
```

### RDS API<a name="export-cluster-data.CancelAPI"></a>

To cancel an export task using the Amazon RDS API, use the [CancelExportTask](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CancelExportTask.html) operation with the `ExportTaskIdentifier` parameter\.

## Failure messages for Amazon S3 export tasks<a name="export-cluster-data.failure-msg"></a>

The following table describes the messages that are returned when Amazon S3 export tasks fail\.


| Failure message | Description | 
| --- | --- | 
| Failed to find or access the source DB cluster: \[cluster name\] | The source DB cluster can't be cloned\. | 
| An unknown internal error occurred\. |  The task has failed because of an unknown error, exception, or failure\.  | 
| An unknown internal error occurred writing the export task's metadata to the S3 bucket \[bucket name\]\. |  The task has failed because of an unknown error, exception, or failure\.  | 
| The RDS export failed to write the export task's metadata because it can't assume the IAM role \[role ARN\]\. |  The export task assumes your IAM role to validate whether it is allowed to write metadata to your S3 bucket\. If the task can't assume your IAM role, it fails\.  | 
| The RDS export failed to write the export task's metadata to the S3 bucket \[bucket name\] using the IAM role \[role ARN\] with the KMS key \[key ID\]\. Error code: \[error code\] |  One or more permissions are missing, so the export task can't access the S3 bucket\. This failure message is raised when receiving one of the following error codes: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/export-cluster-data.html) These error codes indicate that settings are misconfigured for the IAM role, S3 bucket, or KMS key\.  | 
| The IAM role \[role ARN\] isn't authorized to call \[S3 action\] on the S3 bucket \[bucket name\]\. Review your permissions and retry the export\. |  The IAM policy is misconfigured\. Permission for the specific S3 action on the S3 bucket is missing, which causes the export task to fail\.  | 
| KMS key check failed\. Check the credentials on your KMS key and try again\. | The KMS key credential check failed\. | 
| S3 credential check failed\. Check the permissions on your S3 bucket and IAM policy\. | The S3 credential check failed\. | 
| The S3 bucket \[bucket name\] isn't valid\. Either it isn't located in the current AWS Region or it doesn't exist\. Review your S3 bucket name and retry the export\. | The S3 bucket is invalid\. | 
| The S3 bucket \[bucket name\] isn't located in the current AWS Region\. Review your S3 bucket name and retry the export\. | The S3 bucket is in the wrong AWS Region\. | 

## Troubleshooting PostgreSQL permissions errors<a name="export-cluster-data.postgres-permissions"></a>

When exporting PostgreSQL databases to Amazon S3, you might see a `PERMISSIONS_DO_NOT_EXIST` error stating that certain tables were skipped\. This error usually occurs when the superuser, which you specified when creating the DB cluster, doesn't have permissions to access those tables\.

To fix this error, run the following command:

```
GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA schema_name TO superuser_name
```

For more information on superuser privileges, see [Master user account privileges](UsingWithRDS.MasterAccounts.md)\.

## File naming convention<a name="export-cluster-data.FileNames"></a>

Exported data for specific tables is stored in the format `base_prefix/files`, where the base prefix is the following:

```
export_identifier/database_name/schema_name.table_name/
```

For example:

```
export-1234567890123-459/rdststcluster/mycluster.DataInsert_7ADB5D19965123A2/
```

Output files use the following naming convention, where *partition\_index* is alphanumeric:

```
partition_index/part-00000-random_uuid.format-based_extension
```

For example:

```
1/part-00000-c5a881bb-58ff-4ee6-1111-b41ecff340a3-c000.gz.parquet
a/part-00000-d7a881cc-88cc-5ab7-2222-c41ecab340a4-c000.gz.parquet
```

The file naming convention is subject to change\. Therefore, when reading target tables, we recommend that you read everything inside the base prefix for the table\.

## Data conversion and storage format<a name="export-cluster-data.data-types"></a>

When you export a DB cluster to an Amazon S3 bucket, Amazon Aurora converts data to, exports data in, and stores data in the Parquet format\. For more information, see [Data conversion when exporting to an Amazon S3 bucket](aurora-export-snapshot.md#aurora-export-snapshot.data-types)\.