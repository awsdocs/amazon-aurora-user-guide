# Deleting an Aurora Serverless v1 DB cluster<a name="aurora-serverless.delete"></a>

When you create an Aurora Serverless v1 DB cluster using the AWS Management Console, the **Enable default protection** option is enabled by default unless you deselect it\. That means that you can't immediately delete an Aurora Serverless v1 DB cluster that has **Deletion protection** enabled\. To delete Aurora Serverless v1 DB clusters that have deletion protection by using the AWS Management Console, you first modify the cluster to remove this protection\. For information about using the AWS CLI for this task, see [AWS CLI](#aurora-serverless.delete.cli)\.

**To disable deletion protection using the AWS Management Console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **DB clusters**\.

1. Choose your Aurora Serverless v1 DB cluster from the list\.

1. Choose **Modify** to open your DB cluster's configuration\. The Modify DB cluster page opens the Settings, Capacity settings, and other configuration details for your Aurora Serverless v1 DB cluster\. Deletion protection is in the **Additional configuration** section\. 

1. Uncheck the **Enable deletion protection** option in the **Additional configuration** properties card\.

1. Choose **Continue**\. The **Summary of modifications** appears\.

1. Choose **Modify cluster** to accept the summary of modifications\. You can also choose **Back** to modify your changes or **Cancel** to discard your changes\.

After deletion protection is no longer active, you can delete your Aurora Serverless v1 DB cluster by using the AWS Management Console\. 

## Console<a name="aurora-serverless.delete.console"></a>

**To delete an Aurora Serverless v1 DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the **Resources** section, choose **DB Clusters**\.

1. Choose the Aurora Serverless v1 DB cluster that you want to delete\.

1. For **Actions**, choose **Delete**\. You're prompted to confirm that you want to delete your Aurora Serverless v1 DB cluster\. 

1. We recommend that you keep the preselected options:
   + **Yes** for **Create final snapshot?**
   + Your Aurora Serverless v1 DB cluster name plus `-final-snapshot` for **Final snapshot name**\. However, you can change the name for your final snapshot in this field\.  
![\[Screenshot of deleting Aurora Serverless v1 database cluster\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-sles-delete-db-1.png)

   If you choose **No** for **Create final snapshot?** you can't restore your DB cluster using snapshots or point\-in\-time recovery\.

1. Choose **Delete DB cluster**\. 

Aurora Serverless v1 deletes your DB cluster\. If you chose to have a final snapshot, you see your Aurora Serverless v1 DB cluster's status change to "Backing\-up" before it's deleted and no longer appears in the list\.

## AWS CLI<a name="aurora-serverless.delete.cli"></a>

Before you begin, configure your AWS CLI with your AWS Access Key ID, AWS Secret Access Key, and the AWS Region where your Aurora Serverless v1 DB cluster For more information, see [Configuration basics](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html#cli-configure-quickstart-config) in the AWS Command Line Interface User Guide\.

You can't delete an Aurora Serverless v1 DB cluster until after you first disable deletion protection for clusters configured with this option\. If you try to delete a cluster that has this protection option enabled, you see the following error message\.

```
1. An error occurred (InvalidParameterCombination) when calling the DeleteDBCluster
2.   operation: Cannot delete protected Cluster, please disable deletion protection and try again.
```

You can change your Aurora Serverless v1 DB cluster's deletion\-protection setting by using the [modify\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html) AWS CLI command as shown in the following:

```
aws rds modify-db-cluster --db-cluster-identifier your-cluster-name --no-deletion-protection
```

This command returns the revised properties for the specified DB cluster\. You can now delete your Aurora Serverless v1 DB cluster\. 

We recommend that you always create a final snapshot whenever you delete an Aurora Serverless v1 DB cluster\. The following example of using the AWS CLI [delete\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/delete-db-cluster.html) shows you how\. You provide the name of your DB cluster and a name for the snapshot\.

For Linux, macOS, or Unix:

```
aws rds delete-db-cluster --db-cluster-identifier \
  your-cluster-name --no-skip-final-snapshot \
  --final-db-snapshot-identifier name-your-snapshot
```

For Windows:

```
aws rds delete-db-cluster --db-cluster-identifier ^
  your-cluster-name --no-skip-final-snapshot ^
  --final-db-snapshot-identifier name-your-snapshot
```