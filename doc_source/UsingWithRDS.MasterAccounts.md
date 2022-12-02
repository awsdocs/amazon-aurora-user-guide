# Master user account privileges<a name="UsingWithRDS.MasterAccounts"></a>

When you create a new DB cluster, the default master user that you use gets certain privileges for that DB cluster\. You can't change the master user name after the DB cluster is created\.

**Important**  
We strongly recommend that you do not use the master user directly in your applications\. Instead, adhere to the best practice of using a database user created with the minimal privileges required for your application\.

**Note**  
If you accidentally delete the permissions for the master user, you can restore them by modifying the DB cluster and setting a new master user password\. For more information about modifying a DB cluster, see [Modifying an Amazon Aurora DB cluster](Aurora.Modifying.md)\.

The following table shows the privileges and database roles the master user gets for each of the database engines\.

<a name="master-user-account-privileges"></a>[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/UsingWithRDS.MasterAccounts.html)