# Password management with Amazon Aurora and AWS Secrets Manager<a name="rds-secrets-manager"></a>

Amazon Aurora integrates with Secrets Manager to manage master user passwords for your DB clusters\.

**Topics**
+ [Overview of managing master user passwords with AWS Secrets Manager](#rds-secrets-manager-overview)
+ [Benefits of managing master user passwords with Secrets Manager](#rds-secrets-manager-benefits)
+ [Permissions required for Secrets Manager integration](#rds-secrets-manager-permissions)
+ [Enforcing Aurora management of the master user password in AWS Secrets Manager](#rds-secrets-manager-auth)
+ [Managing the master user password for a DB cluster with Secrets Manager](#rds-secrets-manager-db-cluster)
+ [Rotating the master user password secret for a DB cluster](#rds-secrets-manager-rotate-db-cluster)
+ [Viewing the details about a secret for a DB cluster](#rds-secrets-manager-view-db-cluster)
+ [Region and version availability](#rds-secrets-manager-availability)
+ [Limitations for Secrets Manager integration with Amazon Aurora](#rds-secrets-manager-limitations)

## Overview of managing master user passwords with AWS Secrets Manager<a name="rds-secrets-manager-overview"></a>

With AWS Secrets Manager, you can replace hard\-coded credentials in your code, including database passwords, with an API call to Secrets Manager to retrieve the secret programmatically\. For more information about Secrets Manager, see [AWS Secrets Manager User Guide](https://docs.aws.amazon.com/secretsmanager/latest/userguide/)\.

You can specify that Aurora manages the master user password in Secrets Manager for an Amazon Aurora DB cluster when you perform one of the following operations:
+ Create the DB cluster
+ Modify the DB cluster
+ Restore the DB cluster from Amazon S3 \(Aurora MySQL only\)

When you specify that Aurora manages the master user password in Secrets Manager, Aurora generates the password and stores it in Secrets Manager\. You can interact directly with the secret to retrieve the credentials for the master user\. You can also specify a customer managed key to encrypt the secret, or use the KMS key that is provided by Secrets Manager\.

Aurora manages the settings for the secret and rotates the secret every seven days by default\. You can modify some of the settings, such as the rotation schedule\. If you delete a DB cluster that manages a secret in Secrets Manager, the secret and its associated metadata are also deleted\.

To connect to a DB cluster with the credentials in a secret, you can retrieve the secret from Secrets Manager\. For more information, see [ Retrieve secrets from AWS Secrets Manager](https://docs.aws.amazon.com/secretsmanager/latest/userguide/retrieving-secrets.html) and [Connect to a SQL database with credentials in an AWS Secrets Manager secret](https://docs.aws.amazon.com/secretsmanager/latest/userguide/retrieving-secrets_jdbc.html) in the *AWS Secrets Manager User Guide*\.

## Benefits of managing master user passwords with Secrets Manager<a name="rds-secrets-manager-benefits"></a>

Managing Aurora master user passwords with Secrets Manager provides the following benefits:
+ Aurora automatically generates database credentials\.
+ Aurora automatically stores and manages database credentials in AWS Secrets Manager\.
+ Aurora rotates database credentials regularly, without requiring application changes\.
+ Secrets Manager secures database credentials from human access and plain text view\.
+ Secrets Manager allows retrieval of database credentials in secrets for database connections\.
+ Secrets Manager allows fine\-grained control of access to database credentials in secrets using IAM\.
+ You can optionally separate database encryption from credentials encryption with different KMS keys\.
+ You can eliminate manual management and rotation of database credentials\.
+ You can monitor database credentials easily with AWS CloudTrail and Amazon CloudWatch\.

For more information about the benefits of Secrets Manager, see [AWS Secrets Manager User Guide](https://docs.aws.amazon.com/secretsmanager/latest/userguide/)\.

## Permissions required for Secrets Manager integration<a name="rds-secrets-manager-permissions"></a>

Users must have the required permissions to perform operations related to Secrets Manager integration\. You can create IAM policies that grant permissions to perform specific API operations on the specified resources they need\. You can then attach those policies to the IAM permission sets or roles that require those permissions\. For more information, see [Identity and access management for Amazon Aurora](UsingWithRDS.IAM.md)\.

For create, modify, or restore operations, the user who specifies that Aurora manages the master user password in Secrets Manager must have permissions to perform the following operations:
+ `kms:DescribeKey`
+ `secretsmanager:CreateSecret`
+ `secretsmanager:TagResource`

For create, modify, or restore operations, the user who specifies the customer managed key to encrypt the secret in Secrets Manager must have permissions to perform the following operations:
+ `kms:Decrypt`
+ `kms:GenerateDataKey`
+ `kms:CreateGrant`

For modify operations, the user who rotates the master user password in Secrets Manager must have permissions to perform the following operation:
+ `secretsmanager:RotateSecret`

## Enforcing Aurora management of the master user password in AWS Secrets Manager<a name="rds-secrets-manager-auth"></a>

You can use IAM condition keys to enforce Aurora management of the master user password in AWS Secrets Manager\. The following policy doesn't allow users to create or restore DB instances or DB clusters unless the master user password is managed by Aurora in Secrets Manager\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Deny",
            "Action": ["rds:CreateDBInstance, rds:CreateDBCluster, rds:RestoreDBInstanceFromS3, rds:RestoreDBClusterFromS3"],
            "Resource": "*",
            "Condition": {
                "Bool": {
                    "rds:ManageMasterUserPassword": false
                }
            }
        }
    ]
}
```

For more information about using condition keys in IAM policies, see [Policy condition keys for Aurora](security_iam_service-with-iam.md#UsingWithRDS.IAM.Conditions) and [Example policies: Using condition keys](security_iam_id-based-policy-examples.md#UsingWithRDS.IAM.Conditions.Examples)\.

## Managing the master user password for a DB cluster with Secrets Manager<a name="rds-secrets-manager-db-cluster"></a>

You can configure Aurora management of the master user password in Secrets Manager when you perform the following actions:
+ [Creating an Amazon Aurora DB cluster](Aurora.CreateInstance.md)
+ [Modifying an Amazon Aurora DB cluster](Aurora.Modifying.md)
+ [Migrating data from an external MySQL database to an Amazon Aurora MySQL DB cluster](AuroraMySQL.Migrating.ExtMySQL.md)

You can use the RDS console, the AWS CLI, or the RDS API to perform these actions\.

### Console<a name="rds-secrets-manager-db-cluster-console"></a>

Follow the instructions for creating or modifying a DB cluster with the RDS console:
+ [Creating a DB cluster](Aurora.CreateInstance.md#Aurora.CreateInstance.Creating)
+ [Modifying a DB instance in a DB cluster](Aurora.Modifying.md#Aurora.Modifying.Instance)

  In the RDS console, you can modify any DB instance to specify the master user password management settings for the entire DB cluster\.
+ [Restoring an Amazon Aurora MySQL DB cluster from an Amazon S3 bucket](AuroraMySQL.Migrating.ExtMySQL.md#AuroraMySQL.Migrating.ExtMySQL.S3.Restore)

When you use the RDS console to perform one of these operations, you can specify that the master user password is managed by Aurora in Secrets Manager\. To do so when you are creating or restoring a DB cluster, select **Manage master credentials in AWS Secrets Manager** in **Credential settings**\. When you are modifying a DB cluster, select **Manage master credentials in AWS Secrets Manager** in **Settings**\.

The following image is an example of the **Manage master credentials in AWS Secrets Manager** setting when you are creating or restoring a DB cluster\.

![\[Manage master credentials in AWS Secrets Manager\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/secrets-manager-credential-settings.png)

When you select this option, Aurora generates the master user password and manages it throughout its lifecycle in Secrets Manager\.

![\[Manage master credentials in AWS Secrets Manager selected\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/secrets-manager-integration-create.png)

You can choose to encrypt the secret with a KMS key that Secrets Manager provides or with a customer managed key that you create\. After Aurora is managing the database credentials for a DB cluster, you can't change the KMS key that is used to encrypt the secret\.

You can choose other settings to meet your requirements\.

For more information about the available settings when you are creating a DB cluster, see [Settings for Aurora DB clusters](Aurora.CreateInstance.md#Aurora.CreateInstance.Settings)\. For more information about the available settings when you are modifying a DB cluster, see [Settings for Amazon Aurora](Aurora.Modifying.md#Aurora.Modifying.Settings)\.

### AWS CLI<a name="rds-secrets-manager-db-cluster-cli"></a>

To specify that Aurora manages the master user password in Secrets Manager, specify the `--manage-master-user-password` option in one of the following commands:
+ [create\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html)
+ [modify\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html)
+ [restore\-db\-cluster\-from\-s3](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-cluster-from-s3.html)

When you specify the `--manage-master-user-password` option in these commands, Aurora generates the master user password and manages it throughout its lifecycle in Secrets Manager\.

To encrypt the secret, you can specify a customer managed key or use the default KMS key that is provided by Secrets Manager\. Use the `--master-user-secret-kms-key-id` option to specify a customer managed key\. The AWS KMS key identifier is the key ARN, key ID, alias ARN, or alias name for the KMS key\. To use a KMS key in a different AWS account, specify the key ARN or alias ARN\. After Aurora is managing the database credentials for a DB cluster, you can't change the KMS key that is used to encrypt the secret\.

You can choose other settings to meet your requirements\.

For more information about the available settings when you are creating a DB cluster, see [Settings for Aurora DB clusters](Aurora.CreateInstance.md#Aurora.CreateInstance.Settings)\. For more information about the available settings when you are modifying a DB cluster, see [Settings for Amazon Aurora](Aurora.Modifying.md#Aurora.Modifying.Settings)\.

This example creates a DB cluster and specifies that Aurora manages the password in Secrets Manager\. The secret is encrypted using the KMS key that is provided by Secrets Manager\.

**Example**  
For Linux, macOS, or Unix:  

```
1. aws rds create-db-cluster \
2.      --db-cluster-identifier sample-cluster \
3.      --engine aurora-mysql \
4.      --engine-version 8.0 \
5.      --db-instance-class db.r5.large \
6.      --manage-master-user-password
```
For Windows:  

```
1. aws rds create-db-cluster ^
2.      --db-cluster-identifier sample-cluster ^
3.      --engine aurora-mysql ^
4.      --engine-version 8.0 ^
5.      --db-instance-class db.r5.large ^
6.      --manage-master-user-password
```

### RDS API<a name="rds-secrets-manager-db-cluster-api"></a>

To specify that Aurora manages the master user password in Secrets Manager, set the `ManageMasterUserPassword` parameter to `true` in one of the following operations:
+ [CreateDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html)
+ [ModifyDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html)
+ [RestoreDBClusterFromS3](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBClusterFromS3.html)

When you set the `ManageMasterUserPassword` parameter to `true` in one of these operations, Aurora generates the master user password and manages it throughout its lifecycle in Secrets Manager\.

To encrypt the secret, you can specify a customer managed key or use the default KMS key that is provided by Secrets Manager\. Use the `MasterUserSecretKmsKeyId` parameter to specify a customer managed key\. The AWS KMS key identifier is the key ARN, key ID, alias ARN, or alias name for the KMS key\. To use a KMS key in a different AWS account, specify the key ARN or alias ARN\. After Aurora is managing the database credentials for a DB cluster, you can't change the KMS key that is used to encrypt the secret\.

## Rotating the master user password secret for a DB cluster<a name="rds-secrets-manager-rotate-db-cluster"></a>

When Aurora rotates a master user password secret, Secrets Manager generates a new secret version for the existing secret\. The new version of the secret contains the new master user password\. Amazon Aurora changes the master user password for the DB cluster to match the password for the new secret version\.

You can rotate a secret immediately instead of waiting for a scheduled rotation\. To rotate a master user password secret in Secrets Manager, modify the DB cluster\. For information about modifying a DB cluster, see [Modifying an Amazon Aurora DB cluster](Aurora.Modifying.md)\.

You can rotate a master user password secret immediately with the RDS console, the AWS CLI, or the RDS API\.

### Console<a name="rds-secrets-manager-rotate-db-instance-console"></a>

To rotate a master user password secret using the RDS console, modify the DB cluster and select **Rotate secret immediately** in **Settings**\.

![\[Rotate a master user password secret immediately\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/secrets-manager-integration-rotate-aurora.png)

Follow the instructions for modifying a DB cluster with the RDS console in [Modifying the DB cluster by using the console, CLI, and API](Aurora.Modifying.md#Aurora.Modifying.Cluster)\. You must choose **Apply immediately** on the confirmation page\.

### AWS CLI<a name="rds-secrets-manager-rotate-db-instance-cli"></a>

To rotate a master user password secret using the AWS CLI, use the [modify\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) command and specify the `--rotate-master-user-password` option\. You must specify the `--apply-immediately` option when you rotate the master password\.

This example rotates a master user password secret\.

**Example**  
For Linux, macOS, or Unix:  

```
1. aws rds modify-db-cluster \
2.     --db-cluster-identifier mydbcluster \
3.     --rotate-master-user-password \
4.     --apply-immediately
```
For Windows:  

```
1. aws rds modify-db-cluster ^
2.     --db-cluster-identifier mydbcluster ^
3.     --rotate-master-user-password ^
4.     --apply-immediately
```

### RDS API<a name="rds-secrets-manager-rotate-db-instance-api"></a>

You can rotate a master user password secret using the [ModifyDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) operation and setting the `RotateMasterUserPassword` parameter to `true`\. You must set the `ApplyImmediately` parameter to `true` when you rotate the master password\.

## Viewing the details about a secret for a DB cluster<a name="rds-secrets-manager-view-db-cluster"></a>

You can retrieve your secrets using the console \([https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\) or the AWS CLI \([get\-secret\-value](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/get-secret-value.html) Secrets Manager command\)\.

You can find the Amazon Resource Name \(ARN\) of a secret managed by Aurora in Secrets Manager with the RDS console, the AWS CLI, or the RDS API\.

### Console<a name="rds-secrets-manager-view-db-cluster-console"></a>

**To view the details about a secret managed by Aurora in Secrets Manager**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the name of the DB cluster to show its details\.

1. Choose the **Configuration** tab\.

   In **Master Credentials ARN**, you can view the secret ARN\.  
![\[View the details about a secret managed by Aurora in Secrets Manager\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/secrets-manager-integration-view-cluster.png)

   You can follow the **Manage in Secrets Manager** link to view and manage the secret in the Secrets Manager console\.

### AWS CLI<a name="rds-secrets-manager-view-db-instance-cli"></a>

You can use the RDS AWS CLI [describe\-db\-clusters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html) command to find the following information about a secret managed by Aurora in Secrets Manager:
+ `SecretArn` – The ARN of the secret
+ `SecretStatus` – The status of the secret

  The possible status values include the following:
  + `creating` – The secret is being created\.
  + `active` – The secret is available for normal use and rotation\.
  + `rotating` – The secret is being rotated\.
  + `impaired` – The secret can be used to access database credentials, but it can't be rotated\. A secret might have this status if, for example, permissions are changed so that RDS can no longer access the secret or the KMS key for the secret\.

    When a secret has this status, you can correct the condition that caused the status\. If you correct the condition that caused status, the status remains `impaired` until the next rotation\. Alternatively, you can modify the DB cluster to turn off automatic management of database credentials, and then modify the DB cluster again to turn on automatic management of database credentials\. To modify the DB cluster, use the `--manage-master-user-password` option in the [modify\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) command\.
+ `KmsKeyId` – The ARN of the KMS key that is used to encrypt the secret

Specify the `--db-cluster-identifier` option to show output for a specific DB cluster\. This example shows the output for a secret that is used by a DB cluster\.

**Example**  

```
1. aws rds describe-db-clusters --db-cluster-identifier mydbcluster
```
The following sample shows the output for a secret:  

```
"MasterUserSecret": {
                "SecretArn": "arn:aws:secretsmanager:eu-west-1:123456789012:secret:rds!cluster-033d7456-2c96-450d-9d48-f5de3025e51c-xmJRDx",
                "SecretStatus": "active",
                "KmsKeyId": "arn:aws:kms:eu-west-1:123456789012:key/0987dcba-09fe-87dc-65ba-ab0987654321"
            }
```

When you have the secret ARN, you can view details about the secret using the [get\-secret\-value](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/get-secret-value.html) Secrets Manager CLI command\.

This example shows the details for the secret in the previous sample output\.

**Example**  
For Linux, macOS, or Unix:  

```
aws secretsmanager get-secret-value \
    --secret-id 'arn:aws:secretsmanager:eu-west-1:123456789012:secret:rds!cluster-033d7456-2c96-450d-9d48-f5de3025e51c-xmJRDx'
```
For Windows:  

```
aws secretsmanager get-secret-value ^
    --secret-id 'arn:aws:secretsmanager:eu-west-1:123456789012:secret:rds!cluster-033d7456-2c96-450d-9d48-f5de3025e51c-xmJRDx'
```

### RDS API<a name="rds-secrets-manager-rotate-db-instance-api"></a>

You can view the ARN, status, and KMS key for a secret managed by Aurora in Secrets Manager using the [DescribeDBClusters](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBClusters.html) RDS operation and setting the `DBClusterIdentifier` parameter to a DB cluster identifier\. Details about the secret are included in the output\.

When you have the secret ARN, you can view details about the secret using the [GetSecretValue](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_GetSecretValue.html) Secrets Manager operation\.

## Region and version availability<a name="rds-secrets-manager-availability"></a>

Feature availability and support varies across specific versions of each database engine and across AWS Regions\. For more information about version and Region availability with Secrets Manager integration with Amazon Aurora, see [Secrets Manager integration](Concepts.Aurora_Fea_Regions_DB-eng.Feature.SecretsManager.md)\. 

## Limitations for Secrets Manager integration with Amazon Aurora<a name="rds-secrets-manager-limitations"></a>

Managing master user passwords with Secrets Manager isn't supported for the following features:
+ DB clusters with read replicas
+ Read replicas
+ Amazon RDS Blue/Green Deployments
+ DB clusters that are part of an Aurora global database
+ Aurora Serverless v1 DB clusters
+ AWS CloudFormation