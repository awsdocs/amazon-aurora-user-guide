# Cloning an Aurora DB Cluster Volume<a name="Aurora.Managing.Clone"></a>

Using the Aurora cloning feature, you can quickly and cost\-effectively create a new cluster containing a duplicate of an Aurora cluster volume and all its data\. We refer to the new cluster and its associated cluster volume as a *clone*\. Creating a clone is faster and more space\-efficient than physically copying the data using a different technique such as restoring a snapshot\.

**Tip**  
 If you aren't familiar with the Aurora cluster volume and what kinds of data it holds, you can learn more about it by reading [Overview of Aurora Storage](Aurora.Overview.StorageReliability.md#Aurora.Overview.Storage)\. 

**Topics**
+ [Overview of Aurora Cloning](#Aurora.Clone.Overview)
+ [Limitations of Aurora Cloning](#Aurora.Managing.Clone.Limitations)
+ [Copy\-on\-Write Protocol for Aurora Cloning](#Aurora.Managing.Clone.Protocol)
+ [Deleting a Source Cluster Volume](#Aurora.Managing.Clone.Deleting)
+ [Creating an Aurora Clone Through the AWS Management Console](#Aurora.Managing.Clone.Console)
+ [Creating an Aurora Clone Through the AWS CLI](#Aurora.Managing.Clone.CLI)
+ [Cross\-Account Cloning](#Aurora.Managing.Clone.Cross-Account)

## Overview of Aurora Cloning<a name="Aurora.Clone.Overview"></a>

Aurora cloning uses a *copy\-on\-write protocol*\. With this mechanism, a clone requires only minimal additional space when first created\. In the beginning, Aurora maintains a single copy of the data, which is used by both the original and new DB clusters\. Aurora allocates new storage only when data changes, either on the source cluster or the cloned cluster\. You can make multiple clones from the same DB cluster\. You can also create additional clones from other clones\. For more information on how the copy\-on\-write protocol works in the context of Aurora storage, see [Copy\-on\-Write Protocol for Aurora Cloning](#Aurora.Managing.Clone.Protocol)\.

You can use cloning in a variety of use cases, especially where you don't want to have an impact on your production environment\. Some examples are the following: 
+ Experiment with and assess the impact of changes, such as schema changes or parameter group changes\.
+ Perform workload\-intensive operations, such as exporting data or running analytical queries\.
+ Create a copy of a production DB cluster in a nonproduction environment for development or testing\.

 Many of those use cases involve short\-lived clones\. In these cases, you run tests or query\-intensive jobs without causing overhead on the original cluster\. Then you delete the clone when that work is finished\. If either the original cluster or the cloned cluster have a high volume of write operations, the underlying storage diverges over time, reducing the efficiency advantage of cloning\. You can create a new clone if you need to perform similar work on the data later\. 

**Tip**  
 The cloned cluster starts with either one or zero associated DB instances\. Creating a clone using the AWS Management Console produces a cluster with a single DB instance\. Creating a clone using the AWS CLI or the RDS API produces an empty cluster with no associated DB instances\. You can configure the DB instances of the clone differently than the original cluster to match the requirements of the workload for the clone\. For example, the cloned cluster might not have the same high availability requirements as the original\. In that case, you might use multiple DB instances for the original cluster but only a single DB instance for the clone\. 

## Limitations of Aurora Cloning<a name="Aurora.Managing.Clone.Limitations"></a>

There are some limitations involved with Aurora cloning, described following:
+ You cannot create clones across AWS Regions\. Each clone must be created in the same Region as the source cluster\.
+ Currently, you are limited to 15 clones based on a copy, including clones based on other clones\. After that, only copies can be created\. However, each copy can also have up to 15 clones\. 
+ Currently, you cannot clone from a cluster without the parallel query feature, to a cluster where parallel query is enabled\. To bring data into a cluster that uses parallel query, create a snapshot of the original cluster and restore it to a cluster where the parallel query option is enabled\.
+ You can provide a different virtual private cloud \(VPC\) for your clone\. However, the subnets in those VPCs must map to the same set of Availability Zones\.

## Copy\-on\-Write Protocol for Aurora Cloning<a name="Aurora.Managing.Clone.Protocol"></a>

The following scenarios illustrate how the copy\-on\-write protocol works\.

**Topics**
+ [Before Cloning](#Aurora.Managing.Clone.Protocol.Before)
+ [After Cloning](#Aurora.Managing.Clone.After)
+ [When a Change Occurs on the Source Cluster Volume](#Aurora.Managing.Clone.Protocol.SourceWrite)
+ [When a Change Occurs on the Clone](#Aurora.Managing.Clone.Protocol.CloneWrite)

### Before Cloning<a name="Aurora.Managing.Clone.Protocol.Before"></a>

Data in a source cluster volume is stored in pages\. In the following diagram, the source cluster volume has four pages\.

![\[Amazon Aurora source cluster volume, before cloning\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/AuroraClone001.png)

### After Cloning<a name="Aurora.Managing.Clone.After"></a>

As shown in the following diagram, there are no changes in the source data after cloning\. Both the source cluster volume and the clone point to the same four pages\. None of the pages has been physically copied, so no additional storage is required\.

![\[Amazon Aurora source cluster volume and clone, after cloning\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/AuroraClone002.png)

### When a Change Occurs on the Source Cluster Volume<a name="Aurora.Managing.Clone.Protocol.SourceWrite"></a>

In the following example, the source cluster volume makes a change to the data in `Page 1`\. Instead of writing to the original `Page 1`, additional storage is used to create a new page, called `Page 1'`\. The source cluster volume now points to the new `Page 1'`, and also to `Page 2`, `Page 3`, and `Page 4`\. The clone continues to point to `Page 1` through `Page 4`\.

![\[Amazon Aurora source cluster volume and clone, after a change occurs in the source\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/AuroraClone003.png)

### When a Change Occurs on the Clone<a name="Aurora.Managing.Clone.Protocol.CloneWrite"></a>

In the following diagram, the clone cluster volume has also made a change, this time in `Page 4`\. Instead of writing to the original `Page 4`, additional storage is used to create a new page, called `Page 4'`\. The source cluster volume continues to point to `Page 1'`, and also `Page 2` through `Page 4`, but the clone now points to `Page 1` through `Page 3`, and also `Page 4'`\.

![\[Amazon Aurora source cluster volume and clone, after a change occurs on the clone\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/AuroraClone004.png)

As shown in the second scenario, after cloning there is no additional storage required at the point of clone creation\. However, as changes occur in the source cluster volume and the clone, only the changed pages are created, as shown in the third and fourth scenarios\. As more changes occur over time in both the source cluster volume and the clone, you need incrementally more storage to capture and store the changes\. 

## Deleting a Source Cluster Volume<a name="Aurora.Managing.Clone.Deleting"></a>

When you delete a source cluster volume that has one or more clones associated with it, the clones are not affected\. The clones continue to point to the pages that were previously owned by the source cluster volume\. 

## Creating an Aurora Clone Through the AWS Management Console<a name="Aurora.Managing.Clone.Console"></a>

The following procedure describes how to clone an Aurora DB cluster using the AWS Management Console\.

 These instructions apply for DB clusters owned by the same AWS account that is creating the clone\. If the DB cluster is owned by a different AWS account, see [Cross\-Account Cloning](#Aurora.Managing.Clone.Cross-Account) instead\. 

**To create a clone of a DB cluster owned by your AWS account using the AWS Management Console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\. Choose the DB cluster that you want to create a clone of\.

1. For **Actions**, choose **Create clone**\.

1. On the **Create clone** page, type a name for the primary instance of the clone DB cluster as the **DB instance identifier**\.

   If you want to, set any other settings for the clone DB cluster\. For information about the different DB cluster settings, see [New Console](Aurora.CreateInstance.md#Aurora.CreateInstance.Creating.Console)\.

1. Choose **Create clone** to launch the clone DB cluster\.

## Creating an Aurora Clone Through the AWS CLI<a name="Aurora.Managing.Clone.CLI"></a>

The following procedure describes how to create an Aurora clone using the AWS CLI\.

**To create a clone using the AWS CLI**
+ Call the [restore\-db\-cluster\-to\-point\-in\-time](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-cluster-to-point-in-time.html) AWS CLI command and supply the following values:
  + `--source-db-cluster-identifier` – the name of the source DB cluster to create a clone of\.
  + `--db-cluster-identifier` – the name of the clone DB cluster\.
  + `--restore-type copy-on-write` – values that indicate to create a clone DB cluster\.
  + `--use-latest-restorable-time` – specifies that the latest restorable backup time is used\.

  The following example creates a clone of the DB cluster named `sample-source-cluster`\. The name of the clone DB cluster is `sample-cluster-clone`\.

  For Linux, macOS, or Unix:

  ```
  aws rds restore-db-cluster-to-point-in-time \
      --source-db-cluster-identifier sample-source-cluster \
      --db-cluster-identifier sample-cluster-clone \
      --restore-type copy-on-write \
      --use-latest-restorable-time
  ```

  For Windows:

  ```
  aws rds restore-db-cluster-to-point-in-time ^
      --source-db-cluster-identifier sample-source-cluster ^
      --db-cluster-identifier sample-cluster-clone ^
      --restore-type copy-on-write ^
      --use-latest-restorable-time
  ```

**Note**  
The [restore\-db\-cluster\-to\-point\-in\-time](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-cluster-to-point-in-time.html) AWS CLI command only restores the DB cluster, not the DB instances for that DB cluster\. You must invoke the [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) command to create DB instances for the restored DB cluster, specifying the identifier of the restored DB cluster in `--db-cluster-identifier`\. You can create DB instances only after the `restore-db-cluster-to-point-in-time` command has completed and the DB cluster is available\.

## Cross\-Account Cloning<a name="Aurora.Managing.Clone.Cross-Account"></a>

 With Amazon Aurora, you can share an Aurora DB cluster with another AWS account or AWS organization\. By sharing this way, you can clone the DB cluster and access the clone from the other account or organization\. 

 For example, if you use separate accounts for production and testing, you can create a clone of production data in your test account\. You can use this clone to experiment with different parameters and run expensive online analytical processing \(OLAP\) queries, all without having an impact on the production environment\. You can give outside companies access to your database, for example for an external vendor to train a machine learning model\. Cross\-account cloning is much faster in such situations than creating and restoring a database snapshot\. 

 To authorize sharing, you use AWS Resource Access Manager \(AWS RAM\)\. For more information about controlling access through AWS RAM, see the [AWS RAM User Guide](https://docs.aws.amazon.com/ram/latest/userguide/)\. 

 Creating a cross\-account clone requires actions from the AWS account that owns the original cluster, and the AWS account that creates the clone\. First, the owner modifies the cluster to allow one or more other accounts to clone it\. If any of the accounts are in a different AWS organization, AWS generates a sharing invitation and the other account must accept the invitation before proceeding\. Then each authorized account can clone the cluster\. Throughout this process, the cluster is identified by its unique Amazon Resource Name \(ARN\)\. 

 You're only charged for additional storage space if you make data changes\. If the source cluster is deleted, storage costs are distributed equally among remaining cloned clusters\. 

**Topics**
+ [Limitations of Cross\-Account Cloning](#Aurora.Managing.Clone.CrossAccount.Limitations)
+ [Allowing Other AWS Accounts to Clone Your Cluster](#Aurora.Managing.Clone.CrossAccount.yours)
+ [Cloning a Cluster Owned by Another AWS Account](#Aurora.Managing.Clone.CrossAccount.theirs)

### Limitations of Cross\-Account Cloning<a name="Aurora.Managing.Clone.CrossAccount.Limitations"></a>

 Aurora cross\-account cloning has the following limitations: 
+  You can't clone an Aurora Serverless cluster across AWS accounts\. 
+  You can't clone an Aurora global database cluster across AWS accounts\. 
+  Viewing and accepting sharing invitations requires using the AWS CLI the Amazon RDS API, or the AWS RAM console\. Currently, you can't perform this procedure using the Amazon RDS console\. 
+  When you make a cross\-account cluster, you can't make additional clones of that new cluster or share the cloned cluster with other AWS accounts\. 
+  The maximum number of cross\-account clones that you can have for any Aurora cluster is 15\. 
+  Your cluster must be in `ACTIVE` state at the time that you share it with other AWS accounts\. 
+  While an Aurora cluster is shared with other AWS accounts, you can't rename the cluster\. 
+  You can't create a cross\-account clone of a cluster that is encrypted with the default RDS key\. 
+  When an encrypted cluster is shared with you, you must encrypt the cloned cluster\. The key you use can be different from the encryption key for the original cluster\. The cluster owner must also grant you permission to access the AWS Key Management Service \(AWS KMS\) customer master key \(CMK\) for the original cluster\. 

### Allowing Other AWS Accounts to Clone Your Cluster<a name="Aurora.Managing.Clone.CrossAccount.yours"></a>

 To allow other AWS accounts to clone a cluster that you own, use AWS RAM to set the sharing permission\. Doing so also sends an invitation to each of the other accounts that's in a different AWS organization\. 

 For the procedures to share resources owned by you in the AWS RAM console, see [Sharing Resources Owned by You](https://docs.aws.amazon.com/ram/latest/userguide/working-with-sharing.html) in the AWS RAM User Guide\. 

**Topics**
+ [Granting Permission to Other AWS Accounts to Clone Your Cluster](#Aurora.Managing.Clone.CrossAccount.granting)
+ [Checking If a Cluster You Own Is Shared with Other AWS Accounts](#Aurora.Managing.Clone.CrossAccount.confirming)

#### Granting Permission to Other AWS Accounts to Clone Your Cluster<a name="Aurora.Managing.Clone.CrossAccount.granting"></a>

 If the cluster that you're sharing is encrypted, you also share the customer master key \(CMK\) for the cluster\. You can allow AWS Identity and Access Management \(IAM\) users or roles in one AWS account to use a CMK in a different account\. To do this, you first add the external account \(root user\) to the CMK's key policy through AWS KMS\. You don't add the individual IAM users or roles to the key policy, only the external account that owns them\. You can only share a CMK that you create, not the default RDS service key\. For information about access control for CMKs, see [Authentication and Access Control for AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/control-access.html)\. 

##### Console<a name="Aurora.Managing.Clone.CrossAccount.granting.console"></a>

**To grant permission to clone your cluster using the AWS Management Console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1.  In the navigation pane, choose **Databases**\. 

1.  Choose the DB cluster that you want to share to see its **Details** page, and choose the **Connectivity & security** tab\. 

1.  In the **Share DB cluster with other AWS accounts** section, enter the numeric account ID for the AWS account that you want to allow to clone this cluster\. For account IDs in the same organization, you can begin typing in the box and then choose from the menu\. 
**Important**  
 In some cases, you might want an account that is not in the same AWS organization as your account to clone a cluster\. In these cases, for security reasons the AWS Management Console doesn't report who owns that account ID or whether the account exists\.   
 Be careful entering account numbers that are not in the same AWS organization as your AWS account\. Immediately verify that you shared with the intended account\. 

1.  On the confirmation page, verify that the account ID that you specified is correct\. Enter `share` in the confirmation box to confirm\. 

    On the **Details** page, an entry appears showing the specified AWS account ID under **Accounts that this DB cluster is shared with**\. The **Status** column initially shows a status of **Pending**\. 

1.  Contact the owner of the other AWS account, or sign in to that account if you own both of them\. Instruct the owner of the other account to accept the sharing invitation and clone the DB cluster, as described following\. 

##### AWS CLI<a name="Aurora.Managing.Clone.CrossAccount.granting.cli"></a>

**To grant permission to clone your cluster using the AWS CLI**

1.  Gather the information for the required parameters\. You need the ARN for your cluster and the numeric ID for the other AWS account\. 

1.  Run the RAM CLI command [https://docs.aws.amazon.com/cli/latest/reference/ram/create-resource-share.html](https://docs.aws.amazon.com/cli/latest/reference/ram/create-resource-share.html)\. 

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

##### RAM API<a name="Aurora.Managing.Clone.CrossAccount.granting.api"></a>

**To grant permission to clone your cluster using the RAM API**

1.  Gather the information for the required parameters\. You need the ARN for your cluster and the numeric ID for the other AWS account\. 

1.  Call the RAM API operation [CreateResourceShare](https://docs.aws.amazon.com/ram/latest/APIReference/API_CreateResourceShare.html), and specify the following values: 
   +  Specify the account IDs of one or more AWS accounts as the `principals` parameter\. 
   +  Specify the ARNs of one or more Aurora DB clusters as the `resourceArns` parameter\. 
   +  Specify whether the permitted account IDs can be outside your AWS organization or not by including a Boolean value for the `allowExternalPrincipals` parameter\. 

##### Recreating a Cluster that Uses Default RDS Key<a name="Aurora.Managing.Clone.CrossAccount.granting.defaultkey"></a>

**To recreate an encrypted cluster that uses the default RDS key**

 If the encrypted cluster that you intend to share uses the default RDS key, you must recreate it using a customer master key \(CMK\) and share the new cluster instead\. You do so by creating a manual snapshot of your DB cluster and restoring it to a new cluster\. Use the following steps\. 

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1.  Choose **Snapshots** from the navigation pane\. 

1.  Choose your snapshot\. 

1.  For **Actions**, choose **Copy Snapshot**, and then choose **Enable encryption**\. 

1.  For **Master key**, choose the new encryption key that you want to use\. 

1.  Restore the copied snapshot\. To do so, follow the procedure in [Restoring from a DB Cluster Snapshot](USER_RestoreFromSnapshot.md)\. The new DB instance uses your new encryption key\. 

1.  \(Optional\) Delete the old DB cluster if you no longer need it\. To do so, follow the procedure in [Deleting a DB Cluster Snapshot](USER_DeleteSnapshot.md#USER_DeleteDBClusterSnapshot)\. Before you do, confirm that your new cluster has all necessary data and that your application can access it successfully\. 

#### Checking If a Cluster You Own Is Shared with Other AWS Accounts<a name="Aurora.Managing.Clone.CrossAccount.confirming"></a>

 You can check if other users have permission to share a cluster\. Doing so can help you understand whether the cluster is approaching the limit for the maximum number of cross\-account clones\. 

 For the procedures to share resources using the AWS RAM console, see [Sharing Resources Owned by You](https://docs.aws.amazon.com/ram/latest/userguide/working-with-sharing.html) in the AWS RAM User Guide\. 

##### AWS CLI<a name="Aurora.Managing.Clone.CrossAccount.confirming.cli"></a>

**To find out if a cluster you own is shared with other AWS accounts using the AWS CLI**
+  Call the RAM CLI command [https://docs.aws.amazon.com/cli/latest/reference/ram/list-principals.html](https://docs.aws.amazon.com/cli/latest/reference/ram/list-principals.html), using your account ID as the resource owner and the ARN of your cluster as the resource ARN\. You can see all shares with the following command\. The results indicate which AWS accounts are allowed to clone the cluster\. 

  ```
  aws ram list-principals \
      --resource-arns your_cluster_arn \
      --principals your_aws_id
  ```

##### RAM API<a name="Aurora.Managing.Clone.CrossAccount.confirming.api"></a>

**To find out if a cluster you own is shared with other AWS accounts using the RAM API**
+  Call the RAM API operation [ListPrincipals](https://docs.aws.amazon.com/ram/latest/APIReference/API_ListPrincipals.html)\. Use your account ID as the resource owner and the ARN of your cluster as the resource ARN\. 

### Cloning a Cluster Owned by Another AWS Account<a name="Aurora.Managing.Clone.CrossAccount.theirs"></a>

 To clone a cluster that's owned by another AWS account, use AWS RAM to get permission to make the clone\. After you have the required permission, you use the standard procedure for cloning an Aurora cluster\. 

 You can also check whether a cluster that you own is a clone of a cluster owned by a different AWS account\. 

 For the procedures to work with resources owned by others in the AWS RAM console, see [Accessing Resources Shared With You](https://docs.aws.amazon.com/ram/latest/userguide/working-with-shared.html) in the AWS RAM User Guide\. 

**Topics**
+ [Viewing Invitations to Clone Clusters Owned by Other AWS Accounts](#Aurora.Managing.Clone.CrossAccount.viewing)
+ [Accepting Invitations to Share Clusters Owned by Other AWS Accounts](#Aurora.Managing.Clone.CrossAccount.accepting)
+ [Cloning an Aurora Cluster Owned by Another AWS Account](#Aurora.Managing.Clone.CrossAccount.cloning)
+ [Checking If a DB Cluster is a Cross\-Account Clone](#Aurora.Managing.Clone.CrossAccount.checking)

#### Viewing Invitations to Clone Clusters Owned by Other AWS Accounts<a name="Aurora.Managing.Clone.CrossAccount.viewing"></a>

 To work with invitations to clone clusters owned by AWS accounts in other AWS organizations, use the AWS CLI, the AWS RAM console, or the AWS RAM API\. Currently, you can't perform this procedure using the Amazon RDS console\. 

 For the procedures to work with invitations in the AWS RAM console, see [Accessing Resources Shared With You](https://docs.aws.amazon.com/ram/latest/userguide/working-with-shared.html) in the AWS RAM User Guide\. 

##### AWS CLI<a name="Aurora.Managing.Clone.CrossAccount.viewing.cli"></a>

**To see invitations to clone clusters owned by other AWS accounts using the AWS CLI**

1.  Run the RAM CLI command [https://docs.aws.amazon.com/cli/latest/reference/ram/get-resource-share-invitations.html](https://docs.aws.amazon.com/cli/latest/reference/ram/get-resource-share-invitations.html)\. 

   ```
   aws ram get-resource-share-invitations --region region_name
   ```

    The results from the preceding command show all invitations to clone clusters, including any that you already accepted or rejected\. 

1.  \(Optional\) Filter the list so you see only the invitations that require action from you\. To do so, add the parameter `--query 'resourceShareInvitations[?status==`PENDING`]'`\. 

##### RAM API<a name="Aurora.Managing.Clone.CrossAccount.viewing.api"></a>

**To see invitations to clone clusters owned by other AWS accounts using the RAM API**

1.  Call the RAM API operation [https://docs.aws.amazon.com/ram/latest/APIReference/API_GetResourceShareInvitations.html](https://docs.aws.amazon.com/ram/latest/APIReference/API_GetResourceShareInvitations.html)\. This operation returns all such invitations, including any that you already accepted or rejected\. 

1.  \(Optional\) Find only the invitations that require action from you by checking the `resourceShareAssociations` return field for a `status` value of `PENDING`\. 

#### Accepting Invitations to Share Clusters Owned by Other AWS Accounts<a name="Aurora.Managing.Clone.CrossAccount.accepting"></a>

 You can accept invitations to share clusters owned by other AWS accounts that are in different AWS organizations\. To work with these invitations, use the AWS CLI, the RAM and RDS APIs, or the RAM console\. Currently, you can't perform this procedure using the RDS console\. 

 For the procedures to work with invitations in the AWS RAM console, see [Accessing Resources Shared With You](https://docs.aws.amazon.com/ram/latest/userguide/working-with-shared.html) in the AWS RAM User Guide\. 

##### Console<a name="Aurora.Managing.Clone.CrossAccount.accepting.console"></a>

**To accept an invitation to share a cluster from another AWS account using the AWS CLI**

1.  Find the invitation ARN by running the RAM CLI command [https://docs.aws.amazon.com/cli/latest/reference/ram/get-resource-share-invitations.html](https://docs.aws.amazon.com/cli/latest/reference/ram/get-resource-share-invitations.html), as shown preceding\. 

1.  Accept the invitation by calling the RAM CLI command [https://docs.aws.amazon.com/cli/latest/reference/ram/accept-resource-share-invitation.html](https://docs.aws.amazon.com/cli/latest/reference/ram/accept-resource-share-invitation.html), as shown following\. 

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

##### RAM and RDS API<a name="Aurora.Managing.Clone.CrossAccount.accepting.api"></a>

**To accept invitations to share somebody's cluster using the RAM and RDS APIs**

1.  Find the invitation ARN by calling the RAM API operation [https://docs.aws.amazon.com/ram/latest/APIReference/API_GetResourceShareInvitations.html](https://docs.aws.amazon.com/ram/latest/APIReference/API_GetResourceShareInvitations.html), as shown preceding\. 

1.  Pass that ARN as the `resourceShareInvitationArn` parameter to the RDS API operation [AcceptResourceShareInvitation](https://docs.aws.amazon.com/ram/latest/APIReference/API_AcceptResourceShareInvitation.html)\. 

#### Cloning an Aurora Cluster Owned by Another AWS Account<a name="Aurora.Managing.Clone.CrossAccount.cloning"></a>

 After you accept the invitation from the AWS account that owns the DB cluster, as shown preceding, you can clone the cluster\. 

##### Console<a name="Aurora.Managing.Clone.CrossAccount.cloning.console"></a>

**To clone an Aurora cluster owned by another AWS account using the AWS Management Console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1.  In the navigation pane, choose **Databases**\. 

    At the top of the database list, you should see one or more items with a **Role** value of `Shared from account #account_id`\. For security reasons, you can only see limited information about the original clusters\. The properties that you can see are the ones such as database engine and version that must be the same in your cloned cluster\. 

1.  Choose the cluster that you intend to clone\. 

1.  For **Actions**, choose **Create clone**\. 

1.  Follow the procedure in [Creating an Aurora Clone Through the AWS Management Console](#Aurora.Managing.Clone.Console) to finish setting up the cloned cluster\. 

1.  As needed, enable encryption for the cloned cluster\. If the cluster that you are cloning is encrypted, you must enable encryption for the cloned cluster\. The AWS account that shared the cluster with you must also share the AWS KMS customer master key \(CMK\) that was used to encrypt the cluster\. You can use the same CMK to encrypt the clone, or your own CMK\. You can't create a cross\-account clone for a cluster that is encrypted with the default RDS CMK\. 

    The account owning the encryption key must grant permission to use the key to the destination account by using a key policy\. This process is similar to how encrypted snapshots are shared, by using a key policy that grants permission to the destination account to use the key\. 

##### AWS CLI<a name="Aurora.Managing.Clone.CrossAccount.cloning.cli"></a>

**To clone an Aurora cluster owned by another AWS account using the AWS CLI**

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

1.  If the cluster that you are cloning is encrypted, encrypt your cloned cluster by including a `kms-key-id` parameter\. This `kms-key-id` value can be the same one used to encrypt the original DB cluster, or your own customer master key \(CMK\)\. Your account must have permission to use that encryption key\. 

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

    The account owning the encryption key must grant permission to use the key to the destination account by using a key policy\. This process is similar to how encrypted snapshots are shared, by using a key policy that grants permission to the destination account to use the key\. An example of a key policy follows\. 

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
The [restore\-db\-cluster\-to\-point\-in\-time](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-cluster-to-point-in-time.html) AWS CLI command only restores the DB cluster, not the DB instances for that DB cluster\. You must invoke the [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) command to create DB instances for the restored DB cluster, specifying the identifier of the restored DB cluster in `--db-cluster-identifier`\. You can create DB instances only after the `restore-db-cluster-to-point-in-time` command has completed and the DB cluster is available\.

##### RDS API<a name="Aurora.Managing.Clone.CrossAccount.cloning.api"></a>

**To clone an Aurora cluster owned by another AWS account using the RDS API**

1.  Accept the invitation from the AWS account that owns the DB cluster, as shown preceding\. 

1.  Clone the cluster by specifying the full ARN of the source cluster in the `SourceDBClusterIdentifier` parameter of the RDS API operation [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBClusterToPointInTime.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBClusterToPointInTime.html)\. 

    If the ARN passed as the `SourceDBClusterIdentifier` hasn't been shared, then the same error is returned as if the specified cluster doesn't exist\. 

1.  If the cluster that you are cloning is encrypted, include a `KmsKeyId` parameter to encrypt your cloned cluster\. This `kms-key-id` value can be the same one used to encrypt the original DB cluster, or your own customer master key \(CMK\)\. Your account must have permission to use that encryption key\. 

    When cloning a volume, the destination account must have permission to use the encryption key used to encrypt the source cluster\. Aurora encrypts the new cloned cluster with the encryption key specified in `KmsKeyId`\. 

    The account owning the encryption key must grant permission to use the key to the destination account by using a key policy\. This process is similar to how encrypted snapshots are shared, by using a key policy that grants permission to the destination account to use the key\. An example of a key policy follows\. 

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
The [RestoreDBClusterToPointInTime](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBClusterToPointInTime.html) RDS API operation only restores the DB cluster, not the DB instances for that DB cluster\. You must invoke the [CreateDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) RDS API operation to create DB instances for the restored DB cluster, specifying the identifier of the restored DB cluster in `DBClusterIdentifier`\. You can create DB instances only after the `RestoreDBClusterToPointInTime` operation has completed and the DB cluster is available\.

#### Checking If a DB Cluster is a Cross\-Account Clone<a name="Aurora.Managing.Clone.CrossAccount.checking"></a>

 The `DBClusters` object identifies whether each cluster is a cross\-account clone\. You can see the clusters that you have permission to clone by using the `include-shared` option when you run the RDS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html)\. However, you can't see most of the configuration details for such clusters\. 

##### AWS CLI<a name="Aurora.Managing.Clone.CrossAccount.checking.cli"></a>

**To check if a DB cluster is a cross\-account clone using the AWS CLI**
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

**To check if a DB cluster is a cross\-account clone using the RDS API**
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