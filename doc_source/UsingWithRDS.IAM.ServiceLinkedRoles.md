# Using service\-linked roles for Amazon Aurora<a name="UsingWithRDS.IAM.ServiceLinkedRoles"></a>

Amazon Aurora uses AWS Identity and Access Management \(IAM\)[ service\-linked roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_terms-and-concepts.html#iam-term-service-linked-role)\. A service\-linked role is a unique type of IAM role that is linked directly to Amazon Aurora\. Service\-linked roles are predefined by Amazon Aurora and include all the permissions that the service requires to call other AWS services on your behalf\. 

A service\-linked role makes using Amazon Aurora easier because you don't have to manually add the necessary permissions\. Amazon Aurora defines the permissions of its service\-linked roles, and unless defined otherwise, only Amazon Aurora can assume its roles\. The defined permissions include the trust policy and the permissions policy, and that permissions policy can't be attached to any other IAM entity\. For more information on managed policies, see [AWS managed policies for Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/rds-security-iam-awsmanpol.html)\.

You can delete the roles only after first deleting their related resources\. This condition protects your Amazon Aurora resources because you can't inadvertently remove permission to access the resources\.

For information about other services that support service\-linked roles, see [AWS services that work with IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_aws-services-that-work-with-iam.html) and look for the services that have **Yes ** in the **Service\-Linked Role** column\. Choose a **Yes** with a link to view the service\-linked role documentation for that service\.

## Service\-linked role permissions for Amazon Aurora<a name="service-linked-role-permissions"></a>

Amazon Aurora uses the service\-linked role named AWSServiceRoleForRDS to allow Amazon RDS to call AWS services on behalf of your DB clusters\.

The AWSServiceRoleForRDS service\-linked role trusts the following service to assume the role:
+ `rds.amazonaws.com`

This service\-linked role has a permissions policy attached to it called `AmazonRDSServiceRolePolicy` that grants it permissions to operate in your account\. The role permissions policy allows Amazon Aurora to complete certain actions on specified resources\. For information about this policy, including the JSON policy document, see [AmazonRDSServiceRolePolicy](https://docs.aws.amazon.com/aws-managed-policy/latest/reference/AmazonRDSServiceRolePolicy.html) in the *AWS Managed Policy Reference Guide*\.

**Note**  
You must configure permissions to allow an IAM entity \(such as a user, group, or role\) to create, edit, or delete a service\-linked role\. If you encounter the following error message:  
**Unable to create the resource\. Verify that you have permission to create service linked role\. Otherwise wait and try again later\.**  
 Make sure you have the following permissions enabled:   

```
{
    "Action": "iam:CreateServiceLinkedRole",
    "Effect": "Allow",
    "Resource": "arn:aws:iam::*:role/aws-service-role/rds.amazonaws.com/AWSServiceRoleForRDS",
    "Condition": {
        "StringLike": {
            "iam:AWSServiceName":"rds.amazonaws.com"
        }
    }
}
```
 For more information, see [Service\-linked role permissions](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html#service-linked-role-permissions) in the *IAM User Guide*\.

### Creating a service\-linked role for Amazon Aurora<a name="create-service-linked-role"></a>

You don't need to manually create a service\-linked role\. When you create a DB cluster, Amazon Aurora creates the service\-linked role for you\. 

**Important**  
If you were using the Amazon Aurora service before December 1, 2017, when it began supporting service\-linked roles, then Amazon Aurora created the AWSServiceRoleForRDS role in your account\. To learn more, see [A new role appeared in my AWS account](https://docs.aws.amazon.com/IAM/latest/UserGuide/troubleshoot_roles.html#troubleshoot_roles_new-role-appeared)\.

If you delete this service\-linked role, and then need to create it again, you can use the same process to recreate the role in your account\. When you create a DB cluster, Amazon Aurora creates the service\-linked role for you again\.

### Editing a service\-linked role for Amazon Aurora<a name="edit-service-linked-role"></a>

Amazon Aurora does not allow you to edit the AWSServiceRoleForRDS service\-linked role\. After you create a service\-linked role, you cannot change the name of the role because various entities might reference the role\. However, you can edit the description of the role using IAM\. For more information, see [Editing a service\-linked role](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html#edit-service-linked-role) in the *IAM User Guide*\.

### Deleting a service\-linked role for Amazon Aurora<a name="delete-service-linked-role"></a>

If you no longer need to use a feature or service that requires a service\-linked role, we recommend that you delete that role\. That way you don't have an unused entity that is not actively monitored or maintained\. However, you must delete all of your DB clusters before you can delete the service\-linked role\.

#### Cleaning up a service\-linked role<a name="service-linked-role-review-before-delete"></a>

Before you can use IAM to delete a service\-linked role, you must first confirm that the role has no active sessions and remove any resources used by the role\.

**To check whether the service\-linked role has an active session in the IAM console**

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane of the IAM console, choose **Roles**\. Then choose the name \(not the check box\) of the AWSServiceRoleForRDS role\.

1. On the **Summary** page for the chosen role, choose the **Access Advisor** tab\.

1. On the **Access Advisor** tab, review recent activity for the service\-linked role\.
**Note**  
If you are unsure whether Amazon Aurora is using the AWSServiceRoleForRDS role, you can try to delete the role\. If the service is using the role, then the deletion fails and you can view the AWS Regions where the role is being used\. If the role is being used, then you must wait for the session to end before you can delete the role\. You cannot revoke the session for a service\-linked role\. 

If you want to remove the AWSServiceRoleForRDS role, you must first delete *all* of your DB clusters\.

##### Deleting all of your clusters<a name="delete-service-linked-role.delete-rds-clusters"></a>

Use one of the following procedures to delete a single cluster\. Repeat the procedure for each of your clusters\.

**To delete a cluster \(console\)**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the **Databases** list, choose the cluster that you want to delete\.

1. For **Cluster Actions**, choose **Delete**\.

1. Choose **Delete**\.

**To delete a cluster \(CLI\)**  
See `[delete\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/delete-db-cluster.html)` in the *AWS CLI Command Reference*\.

**To delete a cluster \(API\)**  
See `[DeleteDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DeleteDBCluster.html)` in the *Amazon RDS API Reference*\.

You can use the IAM console, the IAM CLI, or the IAM API to delete the AWSServiceRoleForRDS service\-linked role\. For more information, see [Deleting a service\-linked role](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html#delete-service-linked-role) in the *IAM User Guide*\.