# Comparison of Aurora MySQL version 3 and MySQL 8\.0 Community Edition<a name="Aurora.AuroraMySQL.Compare-80-v3"></a>

You can use the following information to learn about the changes to be aware of when you convert from a different MySQL 8\.0–compatible system to Aurora MySQL version 3\.

 In general, Aurora MySQL version 3 supports the feature set of community MySQL 8\.0\.23\. Some new features from MySQL 8\.0 community edition don't apply to Aurora MySQL\. Some of those features aren't compatible with some aspect of Aurora, such as the Aurora storage architecture\. Other features aren't needed because the Amazon RDS management service provides equivalent functionality\. The following features in community MySQL 8\.0 aren't supported or work differently in Aurora MySQL version 3\. 

 For release notes for all Aurora MySQL version 3 releases, see [ Database engine updates for Amazon Aurora MySQL version 3](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraMySQLReleaseNotes/AuroraMySQL.Updates.30Updates.html) in the *Release Notes for Aurora MySQL*\. 

**Topics**
+ [MySQL 8\.0 features not available in Aurora MySQL version 3](#Aurora.AuroraMySQL.Compare-80-v3-features)
+ [Role\-based privilege model](#AuroraMySQL.privilege-model)
+ [Authentication](#AuroraMySQL.mysql80-authentication)

## MySQL 8\.0 features not available in Aurora MySQL version 3<a name="Aurora.AuroraMySQL.Compare-80-v3-features"></a>

 The following features from community MySQL 8\.0 aren't available or work differently in Aurora MySQL version 3\. 
+  Resource groups and associated SQL statements aren't supported in Aurora MySQL\. 
+  The Aurora storage architecture means that you don't have to manually manage files and the underlying storage for your database\. In particular, Aurora handles the undo tablespace differently than community MySQL does\. This difference from community MySQL has the following consequences: 
  +  Aurora MySQL doesn't support named tablespaces\. 
  +  The `innodb_undo_log_truncate` configuration setting is turned off and can't be turned on\. Aurora has its own mechanism for reclaiming storage space\. 
  +  Aurora MySQL doesn't have the `CREATE UNDO TABLESPACE`, `ALTER UNDO TABLESPACE ... SET INACTIVE`, and `DROP UNDO TABLESPACE` statements\. 
  +  Aurora sets the number of undo tablespaces automatically and manages those tablespaces for you\. 
+  TLS 1\.3 isn't supported in Aurora MySQL version 3\. 
+  The `aurora_hot_page_contention` status variable isn't available\. The hot page contention feature isn't supported\. For the full list of status variables not available in Aurora MySQL version 3, see [Status variables](Aurora.AuroraMySQL.Compare-v2-v3.md#AuroraMySQL.mysql80-status-vars)\. 
+  You can't modify the settings of any MySQL plugins\. 
+  The X plugin isn't supported\. 

## Role\-based privilege model<a name="AuroraMySQL.privilege-model"></a>

 With Aurora MySQL version 3, you can't modify the tables in the `mysql` database directly\. In particular, you can't set up users by inserting into the `mysql.user` table\. Instead, you use SQL statements to grant role\-based privileges\. You also can't create other kinds of objects such as stored procedures in the `mysql` database\. You can still query the `mysql` tables\. If you use binary log replication, changes made directly to the `mysql` tables on the source cluster aren't replicated to the target cluster\. 

 In some cases, your application might use shortcuts to create users or other objects by inserting into the `mysql` tables\. If so, change your application code to use the corresponding statements such as `CREATE USER`\. If your application creates stored procedures or other objects in the `mysql` database, use a different database instead\. 

 To export metadata for database users during the migration from an external MySQL database, you can use `mysqlpump` command instead of `mysqldump`\. Use the following syntax\. 

```
mysqlpump --exclude-databases=mysql --users
```

 This statement dumps all databases except for the tables in the `mysql` system database\. It also includes `CREATE USER` and `GRANT` statements to reproduce all MySQL users in the migrated database\. You can also use the [pt\-show\-grants tool](https://www.percona.com/doc/percona-toolkit/LATEST/pt-show-grants.html) on the source system to list `CREATE USER` and `GRANT` statements to reproduce all the database users\. 

 To simplify managing permissions for many users or applications, you can use the `CREATE ROLE` statement to create a role that has a set of permissions\. Then you can use the `GRANT` and `SET ROLE` statements and the `current_role` function to assign roles to users or applications, switch the current role, and check which roles are in effect\. For more information on the role\-based permission system in MySQL 8\.0, see [Using Roles](https://dev.mysql.com/doc/refman/8.0/en/roles.html) in the MySQL Reference Manual\. 

 Aurora MySQL version 3 includes a special role that has all of the following privileges\. This role is named `rds_superuser_role`\. The primary administrative user for each cluster already has this role granted\. The `rds_superuser_role` role includes the following privileges for all database objects: 
+  `ALTER` 
+  `APPLICATION_PASSWORD_ADMIN` 
+  `ALTER ROUTINE` 
+  `CONNECTION_ADMIN` 
+  `CREATE` 
+  `CREATE ROLE` 
+  `CREATE ROUTINE` 
+  `CREATE TABLESPACE` 
+  `CREATE TEMPORARY TABLES` 
+  `CREATE USER` 
+  `CREATE VIEW` 
+  `DELETE` 
+  `DROP` 
+  `DROP ROLE` 
+  `EVENT` 
+  `EXECUTE` 
+  `INDEX` 
+  `INSERT` 
+  `LOCK TABLES` 
+  `PROCESS` 
+  `REFERENCES` 
+  `RELOAD` 
+  `REPLICATION CLIENT` 
+  `REPLICATION SLAVE` 
+  `ROLE_ADMIN` 
+  `SET_USER_ID` 
+  `SELECT` 
+  `SHOW DATABASES` 
+  `SHOW VIEW` 
+  `TRIGGER` 
+  `UPDATE` 
+  `XA_RECOVER_ADMIN` 

 The role definition also includes `WITH GRANT OPTION` so that an administrative user can grant that role to other users\. In particular, the administrator must grant any privileges needed to perform binary log replication with the Aurora MySQL cluster as the target\. 

**Tip**  
 To see the full details of the permissions, enter the following statements\.   

```
SHOW GRANTS FOR rds_superuser_role@'%';
SHOW GRANTS FOR name_of_administrative_user_for_your_cluster@'%';
```

 Aurora MySQL version 3 also includes roles that you can use to access other AWS services\. You can set these roles as an alternative to `GRANT` statements\. For example, you specify `GRANT AWS_LAMBDA_ACCESS TO user` instead of `GRANT INVOKE LAMBDA ON *.* TO user`\. For the procedures to access other AWS services, see [Integrating Amazon Aurora MySQL with other AWS services](AuroraMySQL.Integrating.md)\. Aurora MySQL version 3 includes the following roles related to accessing other AWS services: 
+  `AWS_LAMBDA_ACCESS` role, as an alternative to the `INVOKE LAMBDA` privilege\. For usage information, [Invoking a Lambda function from an Amazon Aurora MySQL DB cluster](AuroraMySQL.Integrating.Lambda.md)\. 
+  `AWS_LOAD_S3_ACCESS` role, as an alternative to the `LOAD FROM S3` privilege\. For usage information, see [Loading data into an Amazon Aurora MySQL DB cluster from text files in an Amazon S3 bucket](AuroraMySQL.Integrating.LoadFromS3.md)\. 
+  `AWS_SELECT_S3_ACCESS` role, as an alternative to the `SELECT INTO S3` privilege\. For usage information, see [Saving data from an Amazon Aurora MySQL DB cluster into text files in an Amazon S3 bucket](AuroraMySQL.Integrating.SaveIntoS3.md)\. 
+  `AWS_SAGEMAKER_ACCESS` role, as an alternative to the `INVOKE SAGEMAKER` privilege\. For usage information, see [Using machine learning \(ML\) with Aurora MySQL](mysql-ml.md)\. 
+  `AWS_COMPREHEND_ACCESS` role, as an alternative to the `INVOKE COMPREHEND` privilege\. For usage information, see [Using machine learning \(ML\) with Aurora MySQL](mysql-ml.md)\. 

 When you grant access by using roles in Aurora MySQL version 3, you also activate the role by using the `SET ROLE role_name` or `SET ROLE ALL` statement\. The following example shows how\. Substitute the appropriate role name for `AWS_SELECT_S3_ACCESS`\. 

```
# Grant role to user
mysql> GRANT AWS_SELECT_S3_ACCESS TO 'user'@'domain-or-ip-address'

# Check the current roles for your user. In this case, the AWS_SELECT_S3_ACCESS role has not been activated.
# Only the rds_superuser_role is currently in effect.
mysql> SELECT CURRENT_ROLE();
+--------------------------+
| CURRENT_ROLE()           |
+--------------------------+
| `rds_superuser_role`@`%` |
+--------------------------+
1 row in set (0.00 sec)

# Activate all roles associated with this user using SET ROLE.
# You can activate specific roles or all roles.
# In this case, the user only has 2 roles, so we specify ALL.
mysql> SET ROLE ALL;
Query OK, 0 rows affected (0.00 sec)

# Verify role is now active
mysql> SELECT CURRENT_ROLE();
+--------------------------------------------------+
| CURRENT_ROLE()                                   |
+--------------------------------------------------+
| `AWS_LAMBDA_ACCESS`@`%`,`rds_superuser_role`@`%` |
+--------------------------------------------------+
```

## Authentication<a name="AuroraMySQL.mysql80-authentication"></a>

 In community MySQL 8\.0, the default authentication plugin is `caching_sha2_password`\. Aurora MySQL version 3 still uses the `mysql_native_password` plugin\. You can't change the `default_authentication_plugin` setting\. 