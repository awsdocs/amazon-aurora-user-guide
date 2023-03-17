# Managing a DB cluster in a domain<a name="aurora-mysql-kerberos-managing"></a>

You can use the AWS CLI or the RDS API to manage your DB cluster and its relationship with your managed Active Directory\. For example, you can associate an Active Directory for Kerberos authentication and disassociate an Active Directory to turn off Kerberos authentication\. You can also move a DB cluster to be externally authenticated by one Active Directory to another\.

For example, using the Amazon RDS API, you can do the following:
+ To reattempt turning on Kerberos authentication for a failed membership, use the `ModifyDBInstance` API operation and specify the current membership's directory ID\.
+ To update the IAM role name for membership, use the `ModifyDBInstance` API operation and specify the current membership's directory ID and the new IAM role\.
+ To turn off Kerberos authentication on a DB cluster, use the `ModifyDBInstance` API operation and specify `none` as the domain parameter\.
+ To move a DB cluster from one domain to another, use the `ModifyDBInstance` API operation and specify the domain identifier of the new domain as the domain parameter\.
+ To list membership for each DB cluster, use the `DescribeDBInstances` API operation\.

## Understanding domain membership<a name="aurora-mysql-kerberos-managing.understanding"></a>

After you create or modify your DB cluster, it becomes a member of the domain\. You can view the status of the domain membership for the DB cluster by running the [describe\-db\-clusters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html) CLI command\. The status of the DB cluster can be one of the following:
+ `kerberos-enabled` – The DB cluster has Kerberos authentication turned on\.
+  `enabling-kerberos` – AWS is in the process of turning on Kerberos authentication on this DB cluster\.
+ `pending-enable-kerberos` – Turning on Kerberos authentication is pending on this DB cluster\.
+ `pending-maintenance-enable-kerberos` – AWS will attempt to turn on Kerberos authentication on the DB cluster during the next scheduled maintenance window\.
+ `pending-disable-kerberos` – Turning off Kerberos authentication is pending on this DB cluster\.
+ `pending-maintenance-disable-kerberos` – AWS will attempt to turn off Kerberos authentication on the DB cluster during the next scheduled maintenance window\.
+ `enable-kerberos-failed` – A configuration problem has prevented AWS from turning on Kerberos authentication on the DB cluster\. Check and fix your configuration before reissuing the DB cluster modify command\.
+ `disabling-kerberos` – AWS is in the process of turning off Kerberos authentication on this DB cluster\.

A request to turn on Kerberos authentication can fail because of a network connectivity issue or an incorrect IAM role\. For example, suppose that you create a DB cluster or modify an existing DB cluster and the attempt to turn on Kerberos authentication fails\. In this case, reissue the modify command or modify the newly created DB cluster to join the domain\.