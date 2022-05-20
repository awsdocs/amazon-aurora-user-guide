# Cloning a volume for an Amazon Aurora DB cluster<a name="Aurora.Managing.Clone"></a><a name="cloning"></a>

By using Aurora cloning, you can create a new cluster that uses the same Aurora cluster volume and has the same data as the original\. The process is designed to be fast and cost\-effective\. The new cluster with its associated data volume is known as a *clone*\. Creating a clone is faster and more space\-efficient than physically copying the data using other techniques, such as restoring a snapshot\.

Aurora supports many different types of cloning: 
+  You can create an Aurora provisioned clone from a provisioned Aurora DB cluster\. 
+  You can create an Aurora Serverless v1 clone from an Aurora Serverless v1 DB cluster\. 
+  You can also create Aurora Serverless v1 clones from Aurora provisioned DB clusters, and you can create provisioned clones from Aurora Serverless v1 DB clusters\. 
+  Clusters with Aurora Serverless v2 instances follow the same rules as provisioned clusters\. 

 When you create a clone using a different deployment configuration than the source, the clone is created using the latest minor version of the source's Aurora DB engine\.

A cloned Aurora Serverless DB cluster has the same behavior and limitations as any Aurora Serverless v1 DB cluster\. For more information, see [Using Amazon Aurora Serverless v1](aurora-serverless.md)\. 

When you create clones from your Aurora DB clusters, the clones are created in your AWS account—the same account that owns the source Aurora DB cluster\. However, you can also share provisioned Aurora DB clusters and clones with other AWS accounts\. For more information, see [Cross\-account cloning with AWS RAM and Amazon Aurora](#Aurora.Managing.Clone.Cross-Account)\.\.

Cross\-account cloning currently doesn't support cloning Aurora Serverless v1 DB clusters\. For more information, see [Limitations of cross\-account cloning](#Aurora.Managing.Clone.CrossAccount.Limitations)\.

**Topics**
+ [Overview of Aurora cloning](#Aurora.Clone.Overview)
+ [Limitations of Aurora cloning](#Aurora.Managing.Clone.Limitations)
+ [How Aurora cloning works](#Aurora.Managing.Clone.Protocol)
+ [Creating an Amazon Aurora clone](#Aurora.Managing.Clone.create)
+ [Cross\-account cloning with AWS RAM and Amazon Aurora](#Aurora.Managing.Clone.Cross-Account)

## Overview of Aurora cloning<a name="Aurora.Clone.Overview"></a>

Aurora uses a *copy\-on\-write protocol* to create a clone\. This mechanism uses minimal additional space to create an initial clone\. When the clone is first created, Aurora keeps a single copy of the data that is used by the source Aurora DB cluster and the new \(cloned\) Aurora DB cluster\. Additional storage is allocated only when changes are made to data \(on the Aurora storage volume\) by the source Aurora DB cluster or the Aurora DB cluster clone\. To learn more about the copy\-on\-write protocol, see [How Aurora cloning works](#Aurora.Managing.Clone.Protocol)\. 

Aurora cloning is especially useful for quickly setting up test environments using your production data, without risking data corruption\. You can use clones for many types of applications, such as the following:
+ Experiment with potential changes \(schema changes and parameter group changes, for example\) to assess all impacts\. 
+ Run workload\-intensive operations, such as exporting data or running analytical queries on the clone\. 
+ Create a copy of your production DB cluster for development, testing, or other purposes\.

You can create more than one clone from the same Aurora DB cluster\. You can also create multiple clones from another clone\. 

After creating an Aurora clone, you can configure the Aurora DB instances differently from the source Aurora DB cluster\. For example, you might not need a clone for development purposes to meet the same high availability requirements as the source production Aurora DB cluster\. In this case, you can configure the clone with a single Aurora DB instance rather than the multiple DB instances used by the Aurora DB cluster\.

When you finish using the clone for your testing, development, or other purposes, you can delete it\.

## Limitations of Aurora cloning<a name="Aurora.Managing.Clone.Limitations"></a>

Aurora cloning currently has the following limitations:
+ You can't create a clone in a different AWS Region than the source Aurora DB cluster\. 
+ You can't create an Aurora Serverless v1 clone from a nonencrypted provisioned Aurora DB cluster\. 
+ You can't create a Aurora Serverless v1 clone from a MySQL 5\.6\-compatible provisioned cluster, or a provisioned clone of a MySQL 5\.6\-compatible Aurora Serverless v1 cluster\.
+ You can't create more than 15 clones based on a copy or based on another clone\. After creating 15 clones, you can create copies only\. However, you can create up to 15 clones of each copy\. 
+ You can't create a clone from an Aurora DB cluster without the parallel query feature to a cluster that uses parallel query\. To bring data into a cluster that uses parallel query, create a snapshot of the original cluster and restore it to the cluster that's using the parallel query feature\.
+ You can't create a clone from an Aurora DB cluster that has no DB instances\. You can only clone Aurora DB clusters that have at least one DB instance\.
+ You can create a clone in a different virtual private cloud \(VPC\) than that of the Aurora DB cluster\. If you do, the subnets of the VPCs must map to the same Availability Zones\.

## How Aurora cloning works<a name="Aurora.Managing.Clone.Protocol"></a>

Aurora cloning works at the storage layer of an Aurora DB cluster\. It uses a *copy\-on\-write* protocol that's both fast and space\-efficient in terms of the underlying durable media supporting the Aurora storage volume\. You can learn more about Aurora cluster volumes in the [Overview of Aurora storage](Aurora.Overview.StorageReliability.md#Aurora.Overview.Storage)\.

**Topics**
+ [Understanding the copy\-on\-write protocol](#Aurora.Managing.Clone.Protocol.Before)
+ [Deleting a source cluster volume](#Aurora.Managing.Clone.Deleting)

### Understanding the copy\-on\-write protocol<a name="Aurora.Managing.Clone.Protocol.Before"></a>

An Aurora DB cluster stores data in pages in the underlying Aurora storage volume\. 

For example, in the following diagram you can find an Aurora DB cluster \(A\) that has four data pages, 1, 2, 3, and 4\. Imagine that a clone, B, is created from the Aurora DB cluster\. When the clone is created, no data is copied\. Rather, the clone points to the same set of pages as the source Aurora DB cluster\.

![\[Amazon Aurora cluster volume with 4 pages for source cluster, A, and clone, B\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-cloning-copy-on-write-protocol-1.png)

When the clone is created, no additional storage is usually needed\. The copy\-on\-write protocol uses the same segment on the physical storage media as the source segment\. Additional storage is required only if the capacity of the source segment isn't sufficient for the entire clone segment\. If that's the case, the source segment is copied to another physical device\. 

In the following diagrams, you can find an example of the copy\-on\-write protocol in action using the same cluster A and its clone, B, as shown preceding\. Let's say that you make a change to your Aurora DB cluster \(A\) that results in a change to data held on page 1\. Instead of writing to the original page 1, Aurora creates a new page 1\[A\]\. The Aurora DB cluster volume for cluster \(A\) now points to page 1\[A\], 2, 3, and 4, while the clone \(B\) still references the original pages\.

![\[Amazon Aurora source DB cluster volume and its clone, both with changes.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-cloning-copy-on-write-protocol-2.png)

On the clone, a change is made to page 4 on the storage volume\. Instead of writing to the original page 4, Aurora creates a new page, 4\[B\]\. The clone now points to pages 1, 2, 3, and to page 4\[B\], while the cluster \(A\) continues pointing to 1\[A\], 2, 3, and 4\.

![\[Amazon Aurora source DB cluster volume and its clone, both with changes.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-cloning-copy-on-write-protocol-3.png)

As more changes occur over time in both the source Aurora DB cluster volume and the clone, more storage is needed to capture and store the changes\. 

### Deleting a source cluster volume<a name="Aurora.Managing.Clone.Deleting"></a>

When you delete a source cluster volume that has one or more clones associated with it, the clones aren't affected\. The clones continue to point to the pages that were previously owned by the source cluster volume\. 

## Creating an Amazon Aurora clone<a name="Aurora.Managing.Clone.create"></a>

You can create a clone in the same AWS account as the source Aurora DB cluster\. To do so, you can use the AWS Management Console or the AWS CLI and the procedures following\.

To allow another AWS account to create a clone or to share a clone with another AWS account, use the procedures in [Cross\-account cloning with AWS RAM and Amazon Aurora](#Aurora.Managing.Clone.Cross-Account)\.

By using Aurora cloning, you can do the following types of cloning operations:
+ Create a provisioned Aurora DB cluster clone from a provisioned Aurora DB cluster\. 
+ Create an Aurora Serverless v1 cluster clone from an Aurora Serverless v1 DB cluster\.
+ Create an Aurora Serverless v1 DB cluster clone from a provisioned Aurora DB cluster\. 
+ Create an Aurora provisioned DB cluster clone from an Aurora Serverless v1 DB cluster\. 

Aurora Serverless v1 DB clusters are always encrypted\. When you clone an Aurora Serverless v1 DB cluster into a provisioned Aurora DB cluster, the provisioned Aurora DB cluster is encrypted\. You can choose the encryption key, but you can't disable the encryption\. To clone from a provisioned Aurora DB cluster to an Aurora Serverless v1 cluster, you need an encrypted provisioned Aurora DB cluster\. 

### Console<a name="Aurora.Managing.Clone.Console"></a>

The following procedure describes how to clone an Aurora DB cluster using the AWS Management Console\.

Creating a clone using the AWS Management Console results in an Aurora DB cluster with one Aurora DB instance\.

 These instructions apply for DB clusters owned by the same AWS account that is creating the clone\. If the DB cluster is owned by a different AWS account, see [Cross\-account cloning with AWS RAM and Amazon Aurora](#Aurora.Managing.Clone.Cross-Account) instead\. 

**To create a clone of a DB cluster owned by your AWS account using the AWS Management Console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\. 

1. Choose your Aurora DB cluster from the list, and for **Actions**, choose **Create clone**\.  
![\[Creating a clone starts by selecting your Aurora DB cluster.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-cloning-create-clone-1.png)

   The Create clone page opens, where you can configure **Instance specifications**, **Connectivity**, and other options for the Aurora DB cluster clone\. 

1. In the **Instance specifications** section, do the following:

   1. For **DB cluster identifier**, enter the name that you want to give to your cloned Aurora DB cluster\.  
![\[Creating a clone starts by selecting your Aurora DB cluster.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-cloning-create-clone-2.png)

   1. For **Capacity type**, choose **Provisioned** or **Serverless** as needed for your use case\. 

      You can choose **Serverless** only if the source Aurora DB cluster is an Aurora Serverless v1 DB cluster or is a provisioned Aurora DB cluster that is encrypted\.
      + If you choose **Provisioned**, you see a **DB instance size** configuration card\.  
![\[To create a provisioned clone from an Aurora DB cluster, specify the DB instance size.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-cloning-create-clone-3-provisioned.png)

        You can accept the provided setting, or you can use a different DB instance class for your clone\.
      + If you choose **Serverless**, you see a **Capacity settings** configuration card\.  
![\[To create a Serverless clone from an Aurora DB cluster, specify the capacity.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-cloning-create-clone-3-serverless.png)

        You can accept the provided settings, or you can change them for your use case\. 

   1. For **Additional configuration**, choose settings as you usually do for your Aurora DB clusters\.

      Additional settings include your choice for the database name and whether you want to use many optional features\. These features include backup, Enhanced Monitoring, exporting logs to Amazon CloudWatch, deletion protection, and so on\. 

      Some of the choices displayed depend on the type of clone that you are creating\. For example, Aurora Serverless doesn't support Amazon RDS Performance Insights, so that option isn't shown in this case\.

      Encryption is a standard option available in **Additional configuration**\. Aurora Serverless DB clusters are always encrypted\. You can create an Aurora Serverless clone only from an Aurora Serverless DB cluster or an encrypted provisioned Aurora DB cluster\.  However, you can choose a different key for the Aurora Serverless clone than that used for the encrypted provisioned cluster\.   
![\[Only encrypted provisioned Aurora DB clusters can be used to create Aurora Serverless clones.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-cloning-create-clone-3-encryption.png)

      When you create an Aurora Serverless clone from an Aurora Serverless DB cluster, you can choose a different key for the clone\.  
![\[You can use a different encryption key for the clone than that used for the source Aurora Serverless DB cluster.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-cloning-create-clone-3-encryption-2.png)

   1. Finish entering all settings for your Aurora DB cluster clone\. To learn more about Aurora DB cluster and instance settings, see [Creating an Amazon Aurora DB cluster](Aurora.CreateInstance.md)\. 

1. Choose **Create clone** to launch the Aurora clone of your chosen Aurora DB cluster\.

When the clone is created, it's listed with your other Aurora DB clusters in the console **Databases** section and displays its current state\. Your clone is ready to use when its state is **Available**\. 

### AWS CLI<a name="Aurora.Managing.Clone.CLI"></a>

Using the AWS CLI for cloning your Aurora DB cluster involves a couple of steps\. 

The `restore-db-cluster-to-point-in-time` AWS CLI command that you use results in an empty Aurora DB cluster with 0 Aurora DB instances\. That is, the command restores only the Aurora DB cluster, not the DB instances for that cluster\. You do that separately after the clone is available\. The two steps in the process are as follows: 

1. Create the clone by using the [restore\-db\-cluster\-to\-point\-in\-time](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-cluster-to-point-in-time.html) CLI command\. The parameters that you use with this command control the capacity type and other details of the empty Aurora DB cluster \(clone\) being created\. 

1. Create the Aurora DB instance for the clone by using the [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) CLI command to recreate the Aurora DB instance in the restored Aurora DB cluster\. 

The commands following assume that the AWS CLI is set up with your AWS Region as the default\. This approach saves you from passing the `--region` name in each of the commands\. For more information, see [Configuring the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html)\. You can also specify the `--region` in each of the CLI commands that follow\. 

**Topics**
+ [Creating the clone](#Aurora.Managing.Clone.CLI.create-empty-clone)
+ [Checking the status and getting clone details](#Aurora.Managing.Clone.CLI.check-status-get-details)
+ [Creating the Aurora DB instance for your clone](#Aurora.Managing.Clone.CLI.create-db-instance)
+ [Parameters to use for cloning](#Aurora.Managing.Clone.CLI.parameter-summary)

#### Creating the clone<a name="Aurora.Managing.Clone.CLI.create-empty-clone"></a>

The specific parameters that you pass to the `[restore\-db\-cluster\-to\-point\-in\-time](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-cluster-to-point-in-time.html)` CLI command vary\. What you pass depends on the engine\-mode type of the source DB cluster—Serverless or Provisioned—and the type of clone that you want to create\.

Use the following procedure to create an Aurora Serverless clone from an Aurora Serverless DB cluster, or to create a provisioned Aurora clone from a provisioned Aurora DB cluster\. 

**To create a clone of the same engine mode as the source Aurora DB cluster**
+ Use the `[restore\-db\-cluster\-to\-point\-in\-time](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-cluster-to-point-in-time.html)` CLI command and specify values for the following parameters:
  + `--db-cluster-identifier` – Choose a meaningful name for your clone\. You name the clone when you use the [restore\-db\-cluster\-to\-point\-in\-time](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-cluster-to-point-in-time.html) CLI command\. You then pass the name of the clone in the [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) CLI command\. 
  + `--restore-type` – Use `copy-on-write` to create a clone of the source DB cluster\. Without this parameter, the `restore-db-cluster-to-point-in-time` restores the Aurora DB cluster rather than creating a clone\.
  + `--source-db-cluster-identifier` – Use the name of the source Aurora DB cluster that you want to clone\.
  + `--use-latest-restorable-time` – This value points to the latest restorable volume data for the clone\.

The following example creates a clone named `my-clone` from a cluster named `my-source-cluster`\.

For Linux, macOS, or Unix:

```
aws rds restore-db-cluster-to-point-in-time \
    --source-db-cluster-identifier my-source-cluster \
    --db-cluster-identifier my-clone \
    --restore-type copy-on-write \
    --use-latest-restorable-time
```

For Windows:

```
aws rds restore-db-cluster-to-point-in-time ^
    --source-db-cluster-identifier my-source-cluster ^
    --db-cluster-identifier my-clone ^
    --restore-type copy-on-write ^
    --use-latest-restorable-time
```

The command returns the JSON object containing details of the clone\. Check to make sure that your cloned DB cluster is available before trying to create the DB instance for your clone\. For more information, see [Checking the status and getting clone details](#Aurora.Managing.Clone.CLI.check-status-get-details)\. 

**To create a clone with a different engine mode than the source Aurora DB cluster**
+ Use the `[restore\-db\-cluster\-to\-point\-in\-time](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-cluster-to-point-in-time.html)` CLI command and specify values for the following parameters:
  + `--db-cluster-identifier` – Choose a meaningful name for your clone\. You name the clone when you use the [restore\-db\-cluster\-to\-point\-in\-time](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-cluster-to-point-in-time.html) CLI command\. You then pass the name of the clone in the [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) CLI command\. 
  + `--engine-mode` – Use this parameter only to create clones that are of a different type than the source Aurora DB cluster\. Choose the value to pass with `--engine-mode` as follows:
    + Use `provisioned` to create a provisioned Aurora DB cluster clone from an Aurora Serverless DB cluster\.
    + Use `serverless` to create an Aurora Serverless DB cluster clone from a provisioned Aurora DB cluster\. When you specify `serverless` engine mode, you can also choose `--scaling-configuration`\.
  + `--restore-type` – Use `copy-on-write` to create a clone of the source DB cluster\. Without this parameter, the `restore-db-cluster-to-point-in-time` restores the Aurora DB cluster rather than creating a clone\.
  + `--scaling-configuration` – \(Optional\) Use only with `--engine-mode serverless` to configure the minimum and maximum capacity for the clone\. If you don't use this parameter, Aurora creates the clone using a minimum capacity of 1\. It uses a maximum capacity that matches the capacity of the source provisioned Aurora DB cluster\. 
  + `--source-db-cluster-identifier` – Use the name of the source Aurora DB cluster that you want to clone\.
  + `--use-latest-restorable-time` – This value points to the latest restorable volume data for the clone\.

The following example creates an Aurora Serverless clone \(`my-clone`\) from a provisioned Aurora DB cluster named `my-source-cluster`\. The provisioned Aurora DB cluster is encrypted\.

For Linux, macOS, or Unix:

```
aws rds restore-db-cluster-to-point-in-time \
    --source-db-cluster-identifier my-source-cluster \
    --db-cluster-identifier my-clone \
    --engine-mode serverless \
    --scaling-configuration MinCapacity=8, MaxCapacity=64 \
    --restore-type copy-on-write \
    --use-latest-restorable-time
```

For Windows:

```
aws rds restore-db-cluster-to-point-in-time ^
    --source-db-cluster-identifier my-source-cluster ^
    --db-cluster-identifier my-clone ^
    --engine-mode serverless ^
    --scaling-configuration MinCapacity=8, MaxCapacity=64 ^
    --restore-type copy-on-write ^
    --use-latest-restorable-time
```

These commands return the JSON object containing details of the clone that you need to create the DB instance\. You can't do that until the status of the clone \(the empty Aurora DB cluster\) has the status **Available**\.

**Note**  
 The [restore\-db\-cluster\-to\-point\-in\-time](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-cluster-to-point-in-time.html) AWS CLI command only restores the DB cluster, not the DB instances for that DB cluster\. You must invoke the [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) command to create DB instances for the restored DB cluster, specifying the identifier of the restored DB cluster in `--db-cluster-identifier`\. You can create DB instances only after the `restore-db-cluster-to-point-in-time` command has completed and the DB cluster is available\. 

 For example, suppose you have a cluster named `tpch100g` that you want to clone\. The following Linux example creates a cloned cluster named `tpch100g-clone` and a primary instance named `tpch100g-clone-instance` for the new cluster\. You don't need to supply some parameters, such as `--master-username reinvent` and `--master-user-password`\. Aurora automatically determines those from the original cluster\. You do need to specify the DB engine to use\. Thus, the example tests the new cluster to determine the right value to use for the `--engine` parameter\. 

```
$ aws rds restore-db-cluster-to-point-in-time \
  --source-db-cluster-identifier tpch100g \
  --db-cluster-identifier tpch100g-clone \
  --restore-type copy-on-write \
  --use-latest-restorable-time

$ aws rds describe-db-clusters \
  --db-cluster-identifier tpch100g-clone \
    --query '*[].[Engine]' \
    --output text
aurora

$ aws rds create-db-instance \
  --db-instance-identifier tpch100g-clone-instance \
  --db-cluster-identifier tpch100g-clone \
  --db-instance-class db.r5.4xlarge \
  --engine aurora
```

#### Checking the status and getting clone details<a name="Aurora.Managing.Clone.CLI.check-status-get-details"></a>

You can use the following command to check the status of your newly created empty DB cluster\.

```
$ aws rds describe-db-clusters --db-cluster-identifier my-clone --query '*[].[Status]' --output text
```

Or you can obtain the status and the other values that you need to [create the DB instance for your clone](#Aurora.Managing.Clone.CLI.create-db-instance) by using the following AWS CLI query\. 

For Linux, macOS, or Unix:

```
aws rds describe-db-clusters --db-cluster-identifier my-clone \
  --query '*[].{Status:Status,Engine:Engine,EngineVersion:EngineVersion,EngineMode:EngineMode}'
```

For Windows:

```
aws rds describe-db-clusters --db-cluster-identifier my-clone ^
  --query "*[].{Status:Status,Engine:Engine,EngineVersion:EngineVersion,EngineMode:EngineMode}"
```

This query returns output similar to the following\.

```
[
  {
        "Status": "available",
        "Engine": "aurora-mysql",
        "EngineVersion": "5.7.mysql_aurora.2.09.1",
        "EngineMode": "provisioned"
    }
]
```

#### Creating the Aurora DB instance for your clone<a name="Aurora.Managing.Clone.CLI.create-db-instance"></a>

Use the [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) CLI command to create the DB instance for your clone\. 

The `--db-instance-class` parameter is used for provisioned Aurora DB clusters only\.

For Linux, macOS, or Unix:

```
aws rds create-db-instance \
    --db-instance-identifier my-new-db \
    --db-cluster-identifier my-clone \
    --db-instance-class  db.r5.4xlarge \
    --engine aurora-mysql
```

For Windows:

```
aws rds create-db-instance ^
    --db-instance-identifier my-new-db ^
    --db-cluster-identifier my-clone ^
    --db-instance-class  db.r5.4xlarge ^
    --engine aurora-mysql
```

For an Aurora Serverless clone created from an Aurora Serverless DB cluster, you specify only a few parameters\. The DB instance inherits the `--engine-mode`, `--master-username`, and `--master-user-password` properties from the source DB cluster\. You can change the `--scaling-configuration`\.

For Linux, macOS, or Unix:

```
aws rds create-db-instance \
    --db-instance-identifier my-new-db \
    --db-cluster-identifier my-clone \
    --engine aurora-postgresql
```

For Windows:

```
aws rds create-db-instance ^
    --db-instance-identifier my-new-db ^
    --db-cluster-identifier my-clone ^
    --engine aurora-postgresql
```

#### Parameters to use for cloning<a name="Aurora.Managing.Clone.CLI.parameter-summary"></a>

The following table summarizes the various parameters used with `restore-db-cluster-to-point-in-time` to clone Aurora DB clusters\.


| Parameter | Description | 
| --- | --- | 
|  \-\-source\-db\-cluster\-identifier | Use the name of the source Aurora DB cluster that you want to clone\. | 
|  \-\-db\-cluster\-identifier | Choose a meaningful name for your clone\. You name your clone with the ` restore-db-cluster-to-point-in-time` command\. Then you pass this name to the `create-db-instance` command\.  | 
| \-\-engine\-mode |  Use this parameter to create clones that are of a different type than the source Aurora DB cluster\. Choose the value to pass with `--engine-mode` as follows: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Managing.Clone.html)  | 
|  \-\-restore\-type | Specify `copy-on-write` as the `--restore-type` to create a clone of the source DB cluster rather than restoring the source Aurora DB cluster\.  | 
| \-\-scaling\-configuration | Use this parameter with `--engine-mode serverless` to configure the minimum and maximum capacity for the clone\. If you don't use this parameter, Aurora creates the Aurora Serverless clone using a minimum capacity of 1 and a maximum capacity of 16\. | 
|  \-\-use\-latest\-restorable\-time | This value points to the latest restorable volume data for the clone\. | 

## Cross\-account cloning with AWS RAM and Amazon Aurora<a name="Aurora.Managing.Clone.Cross-Account"></a>

By using AWS Resource Access Manager \(AWS RAM\) with Amazon Aurora, you can share Aurora DB clusters and clones that belong to your AWS account with another AWS account or organization\. Such *cross\-account cloning* is much faster than creating and restoring a database snapshot\. You can create a clone of one of your Aurora DB clusters and share the clone\. Or you can share your Aurora DB cluster with another AWS account and let the account holder create the clone\. The approach that you choose depends on your use case\.

For example, you might need to regularly share a clone of your financial database with your organization's internal auditing team\. In this case, your auditing team has its own AWS account for the applications that it uses\. You can give the auditing team's AWS account the permission to access your Aurora DB cluster and clone it as needed\. 

On the other hand, if an outside vendor audits your financial data you might prefer to create the clone yourself\. You then give the outside vendor access to the clone only\.

You can also use cross\-account cloning to support many of the same use cases for cloning within the same AWS account, such as development and testing\. For example, your organization might use different AWS accounts for production, development, testing, and so on\. For more information, see [Overview of Aurora cloning](#Aurora.Clone.Overview)\. 

Thus, you might want to share a clone with another AWS account or allow another AWS account to create clones of your Aurora DB clusters\. In either case, start by using AWS RAM to create a share object\. For complete information about sharing AWS resources between AWS accounts, see the [AWS RAM User Guide](https://docs.aws.amazon.com/ram/latest/userguide/)\. 

Creating a cross\-account clone requires actions from the AWS account that owns the original cluster, and the AWS account that creates the clone\. First, the original cluster owner modifies the cluster to allow one or more other accounts to clone it\. If any of the accounts is in a different AWS organization, AWS generates a sharing invitation\. The other account must accept the invitation before proceeding\. Then each authorized account can clone the cluster\. Throughout this process, the cluster is identified by its unique Amazon Resource Name \(ARN\)\. 

As with cloning within the same AWS account, additional storage space is used only if changes are made to the data by the source or the clone\. Charges for storage are then applied at that time\. If the source cluster is deleted, storage costs are distributed equally among remaining cloned clusters\. 

**Topics**
+ [Limitations of cross\-account cloning](#Aurora.Managing.Clone.CrossAccount.Limitations)
+ [Allowing other AWS accounts to clone your cluster](#Aurora.Managing.Clone.CrossAccount.yours)
+ [Cloning a cluster that is owned by another AWS account](#Aurora.Managing.Clone.CrossAccount.theirs)

### Limitations of cross\-account cloning<a name="Aurora.Managing.Clone.CrossAccount.Limitations"></a>

 Aurora cross\-account cloning has the following limitations: 
+ You can't clone an Aurora Serverless cluster across AWS accounts\. 
+ You can't view or accept invitations to shared resources with the AWS Management Console\. Use the AWS CLI, the Amazon RDS API, or the AWS RAM console to view and accept invitations to shared resources\. 
+ You can't create new clones from a clone that's been shared with your AWS account\. 
+ You can't share resources \(clones or Aurora DB clusters\) that have been shared with your AWS account\.
+ You can create a maximum of 15 cross\-account clones from any single Aurora DB cluster\. 
+  Each of the 15 cross\-account clones must be owned by a different AWS account\. That is, you can only create one cross\-account clone of a cluster within any AWS account\. 
+  After you clone a cluster, the original cluster and its clone are considered to be the same for purposes of enforcing limits on cross\-account clones\. You can't create cross\-account clones of both the original cluster and the cloned cluster within the same AWS account\. The total number of cross\-account clones for the original cluster and any of its clones can't exceed 15\. 
+ You can't share an Aurora DB cluster with other AWS accounts unless the cluster is in an `ACTIVE` state\. 
+ You can't rename an Aurora DB cluster that's been shared with other AWS accounts\. 
+  You can't create a cross\-account clone of a cluster that is encrypted with the default RDS key\. 
+ You can't create nonencrypted clones in one AWS account from encrypted Aurora DB clusters that have been shared by another AWS account\. The cluster owner must grant permission to access the source cluster's AWS KMS key\. However, you can use a different key when you create the clone\.  

### Allowing other AWS accounts to clone your cluster<a name="Aurora.Managing.Clone.CrossAccount.yours"></a>

 To allow other AWS accounts to clone a cluster that you own, use AWS RAM to set the sharing permission\. Doing so also sends an invitation to each of the other accounts that's in a different AWS organization\. 

 For the procedures to share resources owned by you in the AWS RAM console, see [Sharing resources owned by you](https://docs.aws.amazon.com/ram/latest/userguide/working-with-sharing.html) in the *AWS RAM User Guide*\. 

**Topics**
+ [Granting permission to other AWS accounts to clone your cluster](#Aurora.Managing.Clone.CrossAccount.granting)
+ [Checking if a cluster that you own is shared with other AWS accounts](#Aurora.Managing.Clone.CrossAccount.confirming)

#### Granting permission to other AWS accounts to clone your cluster<a name="Aurora.Managing.Clone.CrossAccount.granting"></a>

 If the cluster that you're sharing is encrypted, you also share the AWS KMS key for the cluster\. You can allow AWS Identity and Access Management \(IAM\) users or roles in one AWS account to use a KMS key in a different account\. 

To do this, you first add the external account \(root user\) to the KMS key's key policy through AWS KMS\. You don't add the individual IAM users or roles to the key policy, only the external account that owns them\. You can only share a KMS key that you create, not the default RDS service key\. For information about access control for KMS keys, see [Authentication and access control for AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/control-access.html)\. 

##### Console<a name="Aurora.Managing.Clone.CrossAccount.granting.console"></a>

**To grant permission to clone your cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1.  In the navigation pane, choose **Databases**\. 

1.  Choose the DB cluster that you want to share to see its **Details** page, and choose the **Connectivity & security** tab\. 

1.  In the **Share DB cluster with other AWS accounts** section, enter the numeric account ID for the AWS account that you want to allow to clone this cluster\. For account IDs in the same organization, you can begin typing in the box and then choose from the menu\. 
**Important**  
 In some cases, you might want an account that is not in the same AWS organization as your account to clone a cluster\. In these cases, for security reasons the console doesn't report who owns that account ID or whether the account exists\.   
Be careful entering account numbers that are not in the same AWS organization as your AWS account\. Immediately verify that you shared with the intended account\. 

1.  On the confirmation page, verify that the account ID that you specified is correct\. Enter `share` in the confirmation box to confirm\. 

    On the **Details** page, an entry appears that shows the specified AWS account ID under **Accounts that this DB cluster is shared with**\. The **Status** column initially shows a status of **Pending**\. 

1.  Contact the owner of the other AWS account, or sign in to that account if you own both of them\. Instruct the owner of the other account to accept the sharing invitation and clone the DB cluster, as described following\. 

##### AWS CLI<a name="Aurora.Managing.Clone.CrossAccount.granting.cli"></a>

**To grant permission to clone your cluster**

1.  Gather the information for the required parameters\. You need the ARN for your cluster and the numeric ID for the other AWS account\. 

1.  Run the AWS RAM CLI command [https://docs.aws.amazon.com/cli/latest/reference/ram/create-resource-share.html](https://docs.aws.amazon.com/cli/latest/reference/ram/create-resource-share.html)\.  

   For Linux, macOS, or Unix:

   ```
   aws ram create-resource-share --name descriptive_name \
     --region region \
     --resource-arns cluster_arn \
     --principals other_account_ids
   ```

   For Windows:

   ```
   aws ram create-resource-share --name descriptive_name ^
     --region region ^
     --resource-arns cluster_arn ^
     --principals other_account_ids
   ```

    To include multiple account IDs for the `--principals` parameter, separate IDs from each other with spaces\. To specify whether the permitted account IDs can be outside your AWS organization, include the `--allow-external-principals` or `--no-allow-external-principals` parameter for `create-resource-share`\. 

##### AWS RAM API<a name="Aurora.Managing.Clone.CrossAccount.granting.api"></a>

**To grant permission to clone your cluster**

1.  Gather the information for the required parameters\. You need the ARN for your cluster and the numeric ID for the other AWS account\. 

1.  Call the AWS RAM API operation [CreateResourceShare](https://docs.aws.amazon.com/ram/latest/APIReference/API_CreateResourceShare.html), and specify the following values: 
   +  Specify the account ID for one or more AWS accounts as the `principals` parameter\. 
   +  Specify the ARN for one or more Aurora DB clusters as the `resourceArns` parameter\. 
   +  Specify whether the permitted account IDs can be outside your AWS organization by including a Boolean value for the `allowExternalPrincipals` parameter\. 

##### Recreating a cluster that uses the default RDS key<a name="Aurora.Managing.Clone.CrossAccount.granting.defaultkey"></a>

If the encrypted cluster that you plan to share uses the default RDS key, make sure to recreate the cluster\. To do this, create a manual snapshot of your DB cluster, use an AWS KMS key, and then restore the cluster to a new cluster\. Then share the new cluster\. To perform this process, take the following steps\.

**To recreate an encrypted cluster that uses the default RDS key**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1.  Choose **Snapshots** from the navigation pane\. 

1.  Choose your snapshot\. 

1.  For **Actions**, choose **Copy Snapshot**, and then choose **Enable encryption**\. 

1.  For **AWS KMS key**, choose the new encryption key that you want to use\. 

1.  Restore the copied snapshot\. To do so, follow the procedure in [Restoring from a DB cluster snapshot](aurora-restore-snapshot.md)\. The new DB instance uses your new encryption key\. 

1.  \(Optional\) Delete the old DB cluster if you no longer need it\. To do so, follow the procedure in [Deleting a DB cluster snapshot](aurora-delete-snapshot.md#DeleteDBClusterSnapshot)\. Before you do, confirm that your new cluster has all necessary data and that your application can access it successfully\. 

#### Checking if a cluster that you own is shared with other AWS accounts<a name="Aurora.Managing.Clone.CrossAccount.confirming"></a>

 You can check if other users have permission to share a cluster\. Doing so can help you understand whether the cluster is approaching the limit for the maximum number of cross\-account clones\. 

 For the procedures to share resources using the AWS RAM console, see [Sharing resources owned by you](https://docs.aws.amazon.com/ram/latest/userguide/working-with-sharing.html) in the *AWS RAM User Guide*\. 

##### AWS CLI<a name="Aurora.Managing.Clone.CrossAccount.confirming.cli"></a>

**To find out if a cluster that you own is shared with other AWS accounts**
+  Call the AWS RAM CLI command [https://docs.aws.amazon.com/cli/latest/reference/ram/list-principals.html](https://docs.aws.amazon.com/cli/latest/reference/ram/list-principals.html), using your account ID as the resource owner and the ARN of your cluster as the resource ARN\. You can see all shares with the following command\. The results indicate which AWS accounts are allowed to clone the cluster\. 

  ```
  aws ram list-principals \
      --resource-arns your_cluster_arn \
      --principals your_aws_id
  ```

##### AWS RAM API<a name="Aurora.Managing.Clone.CrossAccount.confirming.api"></a>

**To find out if a cluster that you own is shared with other AWS accounts**
+  Call the AWS RAM API operation [ListPrincipals](https://docs.aws.amazon.com/ram/latest/APIReference/API_ListPrincipals.html)\. Use your account ID as the resource owner and the ARN of your cluster as the resource ARN\. 

### Cloning a cluster that is owned by another AWS account<a name="Aurora.Managing.Clone.CrossAccount.theirs"></a>

 To clone a cluster that's owned by another AWS account, use AWS RAM to get permission to make the clone\. After you have the required permission, use the standard procedure for cloning an Aurora cluster\. 

 You can also check whether a cluster that you own is a clone of a cluster owned by a different AWS account\. 

 For the procedures to work with resources owned by others in the AWS RAM console, see [Accessing resources shared with you](https://docs.aws.amazon.com/ram/latest/userguide/working-with-shared.html) in the *AWS RAM User Guide\.* 

**Topics**
+ [Viewing invitations to clone clusters that are owned by other AWS accounts](#Aurora.Managing.Clone.CrossAccount.viewing)
+ [Accepting invitations to share clusters owned by other AWS accounts](#Aurora.Managing.Clone.CrossAccount.accepting)
+ [Cloning an Aurora cluster that is owned by another AWS account](#Aurora.Managing.Clone.CrossAccount.cloning)
+ [Checking if a DB cluster is a cross\-account clone](#Aurora.Managing.Clone.CrossAccount.checking)

#### Viewing invitations to clone clusters that are owned by other AWS accounts<a name="Aurora.Managing.Clone.CrossAccount.viewing"></a>

 To work with invitations to clone clusters owned by AWS accounts in other AWS organizations, use the AWS CLI, the AWS RAM console, or the AWS RAM API\. Currently, you can't perform this procedure using the Amazon RDS console\. 

 For the procedures to work with invitations in the AWS RAM console, see [Accessing resources shared with you](https://docs.aws.amazon.com/ram/latest/userguide/working-with-shared.html) in the *AWS RAM User Guide*\. 

##### AWS CLI<a name="Aurora.Managing.Clone.CrossAccount.viewing.cli"></a>

**To see invitations to clone clusters that are owned by other AWS accounts**

1.  Run the AWS RAM CLI command [https://docs.aws.amazon.com/cli/latest/reference/ram/get-resource-share-invitations.html](https://docs.aws.amazon.com/cli/latest/reference/ram/get-resource-share-invitations.html)\. 

   ```
   aws ram get-resource-share-invitations --region region_name
   ```

    The results from the preceding command show all invitations to clone clusters, including any that you already accepted or rejected\. 

1.  \(Optional\) Filter the list so you see only the invitations that require action from you\. To do so, add the parameter `--query 'resourceShareInvitations[?status==`PENDING`]'`\. 

##### AWS RAM API<a name="Aurora.Managing.Clone.CrossAccount.viewing.api"></a>

**To see invitations to clone clusters that are owned by other AWS accounts**

1.  Call the AWS RAM API operation [https://docs.aws.amazon.com/ram/latest/APIReference/API_GetResourceShareInvitations.html](https://docs.aws.amazon.com/ram/latest/APIReference/API_GetResourceShareInvitations.html)\. This operation returns all such invitations, including any that you already accepted or rejected\. 

1.  \(Optional\) Find only the invitations that require action from you by checking the `resourceShareAssociations` return field for a `status` value of `PENDING`\. 

#### Accepting invitations to share clusters owned by other AWS accounts<a name="Aurora.Managing.Clone.CrossAccount.accepting"></a>

 You can accept invitations to share clusters owned by other AWS accounts that are in different AWS organizations\. To work with these invitations, use the AWS CLI, the AWS RAM and RDS APIs, or the AWS RAM console\. Currently, you can't perform this procedure using the RDS console\. 

 For the procedures to work with invitations in the AWS RAM console, see [Accessing resources shared with you](https://docs.aws.amazon.com/ram/latest/userguide/working-with-shared.html) in the *AWS RAM User Guide*\. 

##### Console<a name="Aurora.Managing.Clone.CrossAccount.accepting.console"></a>

**To accept an invitation to share a cluster from another AWS account**

1.  Find the invitation ARN by running the AWS RAM CLI command [https://docs.aws.amazon.com/cli/latest/reference/ram/get-resource-share-invitations.html](https://docs.aws.amazon.com/cli/latest/reference/ram/get-resource-share-invitations.html), as shown preceding\. 

1.  Accept the invitation by calling the AWS RAM CLI command [https://docs.aws.amazon.com/cli/latest/reference/ram/accept-resource-share-invitation.html](https://docs.aws.amazon.com/cli/latest/reference/ram/accept-resource-share-invitation.html), as shown following\. 

   For Linux, macOS, or Unix:

   ```
   aws ram accept-resource-share-invitation \
     --resource-share-invitation-arn invitation_arn \
     --region region
   ```

   For Windows:

   ```
   aws ram accept-resource-share-invitation ^
     --resource-share-invitation-arn invitation_arn ^
     --region region
   ```

##### AWS RAM and RDS API<a name="Aurora.Managing.Clone.CrossAccount.accepting.api"></a>

**To accept invitations to share somebody's cluster**

1.  Find the invitation ARN by calling the AWS RAM API operation [https://docs.aws.amazon.com/ram/latest/APIReference/API_GetResourceShareInvitations.html](https://docs.aws.amazon.com/ram/latest/APIReference/API_GetResourceShareInvitations.html), as shown preceding\. 

1.  Pass that ARN as the `resourceShareInvitationArn` parameter to the RDS API operation [AcceptResourceShareInvitation](https://docs.aws.amazon.com/ram/latest/APIReference/API_AcceptResourceShareInvitation.html)\. 

#### Cloning an Aurora cluster that is owned by another AWS account<a name="Aurora.Managing.Clone.CrossAccount.cloning"></a>

 After you accept the invitation from the AWS account that owns the DB cluster, as shown preceding, you can clone the cluster\. 

##### Console<a name="Aurora.Managing.Clone.CrossAccount.cloning.console"></a>

**To clone an Aurora cluster that is owned by another AWS account**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1.  In the navigation pane, choose **Databases**\. 

    At the top of the database list, you should see one or more items with a **Role** value of `Shared from account #account_id`\. For security reasons, you can only see limited information about the original clusters\. The properties that you can see are the ones such as database engine and version that must be the same in your cloned cluster\. 

1.  Choose the cluster that you intend to clone\. 

1.  For **Actions**, choose **Create clone**\. 

1.  Follow the procedure in [Console](#Aurora.Managing.Clone.Console) to finish setting up the cloned cluster\. 

1. As needed, enable encryption for the cloned cluster\. If the cluster that you are cloning is encrypted, you must enable encryption for the cloned cluster\. The AWS account that shared the cluster with you must also share the KMS key that was used to encrypt the cluster\. You can use the same KMS key to encrypt the clone, or your own KMS key\. You can't create a cross\-account clone for a cluster that is encrypted with the default KMS key\. 

    The account that owns the encryption key must grant permission to use the key to the destination account by using a key policy\. This process is similar to how encrypted snapshots are shared, by using a key policy that grants permission to the destination account to use the key\. 

##### AWS CLI<a name="Aurora.Managing.Clone.CrossAccount.cloning.cli"></a>

**To clone an Aurora cluster owned by another AWS account**

1.  Accept the invitation from the AWS account that owns the DB cluster, as shown preceding\. 

1.  Clone the cluster by specifying the full ARN of the source cluster in the `source-db-cluster-identifier` parameter of the RDS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-cluster-to-point-in-time.html](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-cluster-to-point-in-time.html), as shown following\. 

    If the ARN passed as the `source-db-cluster-identifier` hasn't been shared, the same error is returned as if the specified cluster doesn't exist\. 

   For Linux, macOS, or Unix:

   ```
   aws rds restore-db-cluster-to-point-in-time \
     --source-db-cluster-identifier=arn:aws:rds:arn_details \
     --db-cluster-identifier=new_cluster_id \
     --restore-type=copy-on-write \
     --use-latest-restorable-time
   ```

   For Windows:

   ```
   aws rds restore-db-cluster-to-point-in-time ^
     --source-db-cluster-identifier=arn:aws:rds:arn_details ^
     --db-cluster-identifier=new_cluster_id ^
     --restore-type=copy-on-write ^
     --use-latest-restorable-time
   ```

1.  If the cluster that you are cloning is encrypted, encrypt your cloned cluster by including a `kms-key-id` parameter\. This `kms-key-id` value can be the same one used to encrypt the original DB cluster, or your own KMS key\. Your account must have permission to use that encryption key\. 

   For Linux, macOS, or Unix:

   ```
   aws rds restore-db-cluster-to-point-in-time \
     --source-db-cluster-identifier=arn:aws:rds:arn_details \
     --db-cluster-identifier=new_cluster_id \
     --restore-type=copy-on-write \
     --use-latest-restorable-time \
     --kms-key-id=arn:aws:kms:arn_details
   ```

   For Windows:

   ```
   aws rds restore-db-cluster-to-point-in-time ^
     --source-db-cluster-identifier=arn:aws:rds:arn_details ^
     --db-cluster-identifier=new_cluster_id ^
     --restore-type=copy-on-write ^
     --use-latest-restorable-time ^
     --kms-key-id=arn:aws:kms:arn_details
   ```

    The account that owns the encryption key must grant permission to use the key to the destination account by using a key policy\. This process is similar to how encrypted snapshots are shared, by using a key policy that grants permission to the destination account to use the key\. An example of a key policy follows\. 

   ```
   {
     "Id": "key-policy-1",
     "Version": "2012-10-17",
     "Statement": [
       {
         "Sid": "Allow use of the key",
         "Effect": "Allow",
         "Principal": {"AWS": [
           "arn:aws:iam::account_id:user/KeyUser",
           "arn:aws:iam::account_id:root"
         ]},
         "Action": [
           "kms:CreateGrant",
           "kms:Encrypt",
           "kms:Decrypt",
           "kms:ReEncrypt",
           "kms:GenerateDataKey*",
           "kms:DescribeKey"
         ],
         "Resource": "*"
       },
       {
         "Sid": "Allow attachment of persistent resources",
         "Effect": "Allow",
         "Principal": {"AWS": [
           "arn:aws:iam::account_id:user/KeyUser",
           "arn:aws:iam::account_id:root"
         ]},
         "Action": [
           "kms:CreateGrant",
           "kms:ListGrants",
           "kms:RevokeGrant"
         ],
         "Resource": "*",
         "Condition": {"Bool": {"kms:GrantIsForAWSResource": true}}
       }
     ]
   }
   ```

**Note**  
The [restore\-db\-cluster\-to\-point\-in\-time](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-cluster-to-point-in-time.html) AWS CLI command restores only the DB cluster, not the DB instances for that DB cluster\. To create DB instances for the restored DB cluster, invoke the [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) command\. Specify the identifier of the restored DB cluster in `--db-cluster-identifier`\.   
You can create DB instances only after the `restore-db-cluster-to-point-in-time` command has completed and the DB cluster is available\.

##### RDS API<a name="Aurora.Managing.Clone.CrossAccount.cloning.api"></a>

**To clone an Aurora cluster owned by another AWS account**

1.  Accept the invitation from the AWS account that owns the DB cluster, as shown preceding\. 

1.  Clone the cluster by specifying the full ARN of the source cluster in the `SourceDBClusterIdentifier` parameter of the RDS API operation [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBClusterToPointInTime.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBClusterToPointInTime.html)\. 

    If the ARN passed as the `SourceDBClusterIdentifier` hasn't been shared, then the same error is returned as if the specified cluster doesn't exist\. 

1.  If the cluster that you are cloning is encrypted, include a `KmsKeyId` parameter to encrypt your cloned cluster\. This `kms-key-id` value can be the same one used to encrypt the original DB cluster, or your own KMS key\. Your account must have permission to use that encryption key\. 

    When you clone a volume, the destination account must have permission to use the encryption key used to encrypt the source cluster\. Aurora encrypts the new cloned cluster with the encryption key specified in `KmsKeyId`\. 

    The account that owns the encryption key must grant permission to use the key to the destination account by using a key policy\. This process is similar to how encrypted snapshots are shared, by using a key policy that grants permission to the destination account to use the key\. An example of a key policy follows\. 

   ```
   {
     "Id": "key-policy-1",
     "Version": "2012-10-17",
     "Statement": [
       {
         "Sid": "Allow use of the key",
         "Effect": "Allow",
         "Principal": {"AWS": [
           "arn:aws:iam::account_id:user/KeyUser",
           "arn:aws:iam::account_id:root"
         ]},
         "Action": [
           "kms:CreateGrant",
           "kms:Encrypt",
           "kms:Decrypt",
           "kms:ReEncrypt",
           "kms:GenerateDataKey*",
           "kms:DescribeKey"
         ],
         "Resource": "*"
       },
       {
         "Sid": "Allow attachment of persistent resources",
         "Effect": "Allow",
         "Principal": {"AWS": [
           "arn:aws:iam::account_id:user/KeyUser",
           "arn:aws:iam::account_id:root"
         ]},
         "Action": [
           "kms:CreateGrant",
           "kms:ListGrants",
           "kms:RevokeGrant"
         ],
         "Resource": "*",
         "Condition": {"Bool": {"kms:GrantIsForAWSResource": true}}
       }
     ]
   }
   ```

**Note**  
The [RestoreDBClusterToPointInTime](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBClusterToPointInTime.html) RDS API operation restores only the DB cluster, not the DB instances for that DB cluster\. To create DB instances for the restored DB cluster, invoke the [CreateDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) RDS API operation\. Specify the identifier of the restored DB cluster in `DBClusterIdentifier`\. You can create DB instances only after the `RestoreDBClusterToPointInTime` operation has completed and the DB cluster is available\.

#### Checking if a DB cluster is a cross\-account clone<a name="Aurora.Managing.Clone.CrossAccount.checking"></a>

 The `DBClusters` object identifies whether each cluster is a cross\-account clone\. You can see the clusters that you have permission to clone by using the `include-shared` option when you run the RDS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html)\. However, you can't see most of the configuration details for such clusters\. 

##### AWS CLI<a name="Aurora.Managing.Clone.CrossAccount.checking.cli"></a>

**To check if a DB cluster is a cross\-account clone**
+  Call the RDS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html)\. 

   The following example shows how actual or potential cross\-account clone DB clusters appear in `describe-db-clusters` output\. For existing clusters owned by your AWS account, the `CrossAccountClone` field indicates whether the cluster is a clone of a DB cluster that is owned by another AWS account\. 

   In some cases, an entry might have a different AWS account number than yours in the `DBClusterArn` field\. In this case, that entry represents a cluster that is owned by a different AWS account and that you can clone\. Such entries have few fields other than `DBClusterArn`\. When creating the cloned cluster, specify the same `StorageEncrypted`, `Engine`, and `EngineVersion` values as in the original cluster\. 

  ```
  $ aws rds describe-db-clusters --include-shared --region us-east-1
  {
    "DBClusters": [
        {
            "EarliestRestorableTime": "2019-05-01T21:17:54.106Z",
            "Engine": "aurora",
            "EngineVersion": "5.6.10a",
            "CrossAccountClone": false,
  ...
        },
        {
            "EarliestRestorableTime": "2019-04-09T16:01:07.398Z",
            "Engine": "aurora",
            "EngineVersion": "5.6.10a",
            "CrossAccountClone": true,
  ...
        },
        {
            "StorageEncrypted": false,
            "DBClusterArn": "arn:aws:rds:us-east-1:12345678:cluster:cluster-abcdefgh",
            "Engine": "aurora",
            "EngineVersion": "5.6.10a",
        }
    ]
  }
  ```

##### RDS API<a name="Aurora.Managing.Clone.CrossAccount.checking.api"></a>

**To check if a DB cluster is a cross\-account clone**
+  Call the RDS API operation [DescribeDBClusters](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBClusters.html)\. 

   For existing clusters owned by your AWS account, the `CrossAccountClone` field indicates whether the cluster is a clone of a DB cluster owned by another AWS account\. Entries with a different AWS account number in the `DBClusterArn` field represent clusters that you can clone and that are owned by other AWS accounts\. These entries have few fields other than `DBClusterArn`\. When creating the cloned cluster, specify the same `StorageEncrypted`, `Engine`, and `EngineVersion` values as in the original cluster\. 

   The following example shows a return value that demonstrates both actual and potential cloned clusters\. 

  ```
  {
    "DBClusters": [
        {
            "EarliestRestorableTime": "2019-05-01T21:17:54.106Z",
            "Engine": "aurora",
            "EngineVersion": "5.6.10a",
            "CrossAccountClone": false,
  ...
        },
        {
            "EarliestRestorableTime": "2019-04-09T16:01:07.398Z",
            "Engine": "aurora",
            "EngineVersion": "5.6.10a",
            "CrossAccountClone": true,
  ...
        },
        {
            "StorageEncrypted": false,
            "DBClusterArn": "arn:aws:rds:us-east-1:12345678:cluster:cluster-abcdefgh",
            "Engine": "aurora",
            "EngineVersion": "5.6.10a"
        }
    ]
  }
  ```