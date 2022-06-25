# Understanding PostgreSQL roles and permissions<a name="Appendix.PostgreSQL.CommonDBATasks.Roles"></a>

When you create an Aurora PostgreSQL DB cluster using the AWS Management Console, an administrator account is created at the same time\. By default, its name is `postgres`, as shown in the following screenshot:

![\[The default login identity for Credentials in the Create database page is postgres.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/default-login-identity-apg-rpg.png)

You can choose another name rather than accept the default \(`postgres`\)\. If you do, the name you choose must start with a letter and be between 1 and 16 alphanumeric characters\. For simplicity's sake, we refer to this main user account by its default value \(`postgres`\) throughout this guide\.

 If you use the `create-db-cluster` AWS CLI rather than the AWS Management Console, you create the user name by passing it with the `master-username` parameter\. For more information, see [Create an Aurora PostgreSQL DB cluster](CHAP_GettingStartedAurora.CreatingConnecting.AuroraPostgreSQL.md#CHAP_GettingStarted.AuroraPostgreSQL.CreateDBCluster)\.

Whether you use the AWS Management Console, the AWS CLI, or the Amazon RDS API, and whether you use the default `postgres` name or choose a different name, this first database user account is a member of the `rds_superuser` group and has `rds_superuser` privileges\.

**Topics**
+ [Understanding the rds\_superuser role](#Appendix.PostgreSQL.CommonDBATasks.Roles.rds_superuser)
+ [Controlling user access to the PostgreSQL database](#Appendix.PostgreSQL.CommonDBATasks.Access)
+ [Delegating and controlling user password management](#Appendix.PostgreSQL.CommonDBATasks.RestrictPasswordMgmt)
+ [Using SCRAM for PostgreSQL password encryption](#PostgreSQL_Password_Encryption_configuration)

## Understanding the rds\_superuser role<a name="Appendix.PostgreSQL.CommonDBATasks.Roles.rds_superuser"></a>

In PostgreSQL, a *role* can define a user, a group, or a set of specific permissions granted to a group or user for various objects in the database\. PostgreSQL commands to `CREATE USER` and `CREATE GROUP` have been replaced by the more general, `CREATE ROLE` with specific properties to distinguish database users\. A database user can be thought of as a role with the LOGIN privilege\. 

**Note**  
The `CREATE USER` and `CREATE GROUP` commands can still be used\. For more information, see [Database Roles](https://www.postgresql.org/docs/current/user-manag.html) in the PostgreSQL documentation\.

The `postgres` user is the most highly privileged database user on your Aurora PostgreSQL DB cluster\. It has the characteristics defined by the following `CREATE ROLE` statement\. 

```
CREATE ROLE postgres WITH LOGIN NOSUPERUSER INHERIT CREATEDB CREATEROLE NOREPLICATION VALID UNTIL 'infinity'
```

The properties `NOSUPERUSER`, `NOREPLICATION`, `INHERIT`, and `VALID UNTIL 'infinity'` are the default options for CREATE ROLE, unless otherwise specified\. 

By default, `postgres` has privileges granted to the `rds_superuser` role\. The `rds_superuser` role allows the `postgres` user to do the following: 
+ Add extensions that are available for use with Aurora PostgreSQL\. For more information, see [Working with extensions and foreign data wrappers](Appendix.PostgreSQL.CommonDBATasks.md)\. 
+ Create roles for users and grant privileges to users\. For more information, see [CREATE ROLE](https://www.postgresql.org/docs/current/sql-createrole.html) and [GRANT](https://www.postgresql.org/docs/14/sql-grant.html) in the PostgreSQL documentation\. 
+ Create databases\. For more information, see [CREATE DATABASE](https://www.postgresql.org/docs/14/sql-createdatabase.html) in the PostgreSQL documentation\.
+ Grant `rds_superuser` privileges to user roles that don't have these privileges, and revoke privileges as needed\. We recommend that you grant this role only to those users who perform superuser tasks\. In other words, you can grant this role to database administrators \(DBAs\) or system administrators\.
+ Grant \(and revoke\) the `rds_replication` role to database users that don't have the `rds_superuser` role\. 
+ Grant \(and revoke\) the `rds_password` role to database users that don't have the `rds_superuser` role\. 
+ Obtain status information about all database connections by using the `pg_stat_activity` view\. When needed, `rds_superuser` can stop any connections by using `pg_terminate_backend` or `pg_cancel_backend`\. 

In the `CREATE ROLE postgres...` statement, you can see that the `postgres` user role specifically disallows PostgreSQL `superuser` permissions\. Aurora PostgreSQL is a managed service, so you can't access the host OS, and you can't connect using the PostgreSQL `superuser` account\. Many of the tasks that require `superuser` access on a stand\-alone PostgreSQL are managed automatically by Aurora\. 

For more information about granting privileges, see [GRANT](http://www.postgresql.org/docs/current/sql-grant.html) in the PostgreSQL documentation\.

The `rds_superuser` role is one of several *predefined* roles in an Aurora PostgreSQL DB cluster\. 

**Note**  
In PostgreSQL 13 and earlier releases, *predefined* roles are known as *default* roles\.

In the following list, you find some of the other predefined roles that are created automatically for a new Aurora PostgreSQL DB cluster\. Predefined roles and their privileges can't be changed\. You can't drop, rename, or modify privileges for these predefined roles\. Attempting to do so results in an error\. 
+ **rds\_password** – A role that can change passwords and set up password constraints for database users\. The `rds_superuser` role is granted this role by default, and can grant the role to database users\. `For more information, see [Controlling user access to the PostgreSQL databaseControlling user access to PostgreSQL](#Appendix.PostgreSQL.CommonDBATasks.Access)\. 
+ **rdsadmin** – A role that's created to handle many of the management tasks that the administrator with `superuser` privileges would perform on a standalone PostgreSQL database\. This role is used internally by Aurora PostgreSQL for many management tasks\. 

To see all predefined roles, you can connect to the primary instance of your Aurora PostgreSQL DB cluster and use the `psq1 \du` metacommand\. The output looks as follows: 

```
List of roles
  Role name   |      Attributes                   |      Member of      
--------------+-----------------------------------+------------------------------------
postgres      | Create role, Create DB           +| {rds_superuser}
              | Password valid until infinity     |
rds_superuser | Cannot login                      | {pg_monitor,pg_signal_backend,
              |                                  +|   rds_replication,rds_password}
...
```

In the output, you can see that `rds_superuser` isn't a database user role \(it can't login\), but it has the privileges of many other roles\. You can also see that database user `postgres` is a member of the `rds_superuser` role\. As mentioned previously, `postgres` is the default value in the Amazon RDS console's **Create database** page\. If you chose another name, that name is shown in the list of roles instead\. 

## Controlling user access to the PostgreSQL database<a name="Appendix.PostgreSQL.CommonDBATasks.Access"></a>

New databases in PostgreSQL are always created with a default set of privileges in the database's `public` schema that allow all database users and roles to create objects\. These privileges allow database users to connect to the database, for example, and create temporary tables while connected\.

To better control user access to the databases instances that you create on your Aurora PostgreSQL DB cluster primary node , we recommend that you revoke these default `public` privileges\. After doing so, you then grant specific privileges for database users on a more granular basis, as shown in the following procedure\. 

**To set up roles and privileges for a new database instance**

Suppose you're setting up a database on a newly created Aurora PostgreSQL DB cluster for use by several researchers, all of whom need read\-write access to the database\. 

1. Use `psql` \(or pgAdmin\) to connect to the primary DB instance on your Aurora PostgreSQL DB cluster: 

   ```
   psql --host=your-cluster-instance-1.666666666666.aws-region.rds.amazonaws.com --port=5432 --username=postgres --password
   ```

   When prompted, enter your password\. The `psql` client connects and displays the default administrative connection database, `postgres=>`, as the prompt\.

1. To prevent database users from creating objects in the `public` schema, do the following:

   ```
   postgres=> REVOKE CREATE ON SCHEMA public FROM PUBLIC;
   REVOKE
   ```

1. Next, you create a new database instance:

   ```
   postgres=> CREATE DATABASE lab_db;
   CREATE DATABASE
   ```

1. Revoke all privileges from the `PUBLIC` schema on this new database\.

   ```
   postgres=> REVOKE ALL ON DATABASE lab_db FROM public;
   REVOKE
   ```

1. Create a role for database users\.

   ```
   postgres=> CREATE ROLE lab_tech;
   CREATE ROLE
   ```

1. Give database users that have this role the ability to connect to the database\.

   ```
   postgres=> GRANT CONNECT ON DATABASE lab_db TO lab_tech;
   GRANT
   ```

1. Grant all users with the `lab_tech` role all privileges on this database\.

   ```
   postgres=> GRANT ALL PRIVILEGES ON DATABASE lab_db TO lab_tech;
   GRANT
   ```

1. Create database users, as follows:

   ```
   postgres=> CREATE ROLE lab_user1 LOGIN PASSWORD 'change_me';
   CREATE ROLE
   postgres=> CREATE ROLE lab_user2 LOGIN PASSWORD 'change_me';
   CREATE ROLE
   ```

1. Grant these two users the privileges associated with the lab\_tech role:

   ```
   postgres=> GRANT lab_tech TO lab_user1;
   GRANT ROLE
   postgres=> GRANT lab_tech TO lab_user2;
   GRANT ROLE
   ```

At this point, `lab_user1` and `lab_user2` can connect to the `lab_db` database\. This example doesn't follow best practices for enterprise usage, which might include creating multiple database instances, different schemas, and granting limited permissions\. For more complete information and additional scenarios, see [Managing PostgreSQL Users and Roles](http://aws.amazon.com/blogs/database/managing-postgresql-users-and-roles/)\. 

For more information about privileges in PostgreSQL databases, see the [GRANT](https://www.postgresql.org/docs/current/static/sql-grant.html) command in the PostgreSQL documentation\.

## Delegating and controlling user password management<a name="Appendix.PostgreSQL.CommonDBATasks.RestrictPasswordMgmt"></a>

As a DBA, you might want to delegate the management of user passwords\. Or, you might want to prevent database users from changing their passwords or reconfiguring password constraints, such as password lifetime\. To ensure that only the database users that you choose can change password settings, you can turn on the restricted password management feature\. When you activate this feature, only those database users that have been granted the `rds_password` role can manage passwords\. 

**Note**  
To use restricted password management, your Aurora PostgreSQL DB cluster must be running Amazon Aurora PostgreSQL 10\.6 or higher\.

By default, this feature is `off`, as shown in the following:

```
postgres=> SHOW rds.restrict_password_commands;
  rds.restrict_password_commands
--------------------------------
 off
(1 row)
```

To turn on this feature, you use a custom parameter group and change the setting for `rds.restrict_password_commands` to 1\. Be sure to reboot your Aurora PostgreSQL's primary DB instance so that the setting takes effect\. 

With this feature active, `rds_password` privileges are needed for the following SQL commands:

```
CREATE ROLE myrole WITH PASSWORD 'mypassword';
CREATE ROLE myrole WITH PASSWORD 'mypassword' VALID UNTIL '2023-01-01';
ALTER ROLE myrole WITH PASSWORD 'mypassword' VALID UNTIL '2023-01-01';
ALTER ROLE myrole WITH PASSWORD 'mypassword';
ALTER ROLE myrole VALID UNTIL '2023-01-01';
ALTER ROLE myrole RENAME TO myrole2;
```

Renaming a role \(`ALTER ROLE myrole RENAME TO newname`\) is also restricted if the password uses the MD5 hashing algorithm\. 

With this feature active, attempting any of these SQL commands without the `rds_password` role permissions generates the following error: 

```
ERROR: must be a member of rds_password to alter passwords
```

We recommend that you grant the `rds_password` to only a few roles that you use solely for password management\. If you grant `rds_password` privileges to database users that don't have `rds_superuser` privileges, you need to also grant them the `CREATEROLE` attribute\.

Make sure that you verify password requirements such as expiration and needed complexity on the client side\. If you use your own client\-side utility for password related changes, the utility needs to be a member of `rds_password` and have `CREATE ROLE` privileges\. 

## Using SCRAM for PostgreSQL password encryption<a name="PostgreSQL_Password_Encryption_configuration"></a>

The Salted Challenge Response Authentication Mechanism \(SCRAM\) is an alternative to PostgreSQL's default message digest \(MD5\) algorithm for encrypting passwords\. The SCRAM authentication mechanism is considered more secure than MD5\. To learn more about these two different approaches to securing passwords, see [Password Authentication](https://www.postgresql.org/docs/14/auth-password.html) in the PostgreSQL documentation\.

We recommend that you use SCRAM rather than MD5 as the password encryption scheme for your Aurora PostgreSQL DB cluster\. As of the Aurora PostgreSQL 14 release, SCRAM is supported in all available Aurora PostgreSQL versions, including versions 10, 11, 12, 13, and 14\. It's a cryptographic challenge\-response mechanism that uses the `scram-sha-256` algorithm for password authentication and encryption\. 

You might need to update libraries for your client applications to support SCRAM\. For example, JDBC versions prior to 42\.2\.0 don't support SCRAM\. For more information, see [PostgreSQL JDBC Driver](https://jdbc.postgresql.org/documentation/changelog.html#version_42.2.0) in the PostgreSQL JDBC Driver documentation\. For a list of other PostgreSQL drivers and SCRAM support, see [List of drivers](https://wiki.postgresql.org/wiki/List_of_drivers) in the PostgreSQL documentation\.

**Note**  
Aurora PostgreSQL version 14 and higher support `scram-sha-256` for password encryption by default for new DB clusters\. That is, the default DB cluster parameter group \(`default.aurora-postgresql14`\) has its `password_encryption` value set to `scram-sha-256`\.

### Setting up Aurora PostgreSQL DB cluster to require SCRAM<a name="PostgreSQL_Password_Encryption_configuration.preliminary"></a>

For Aurora PostgreSQL 14\.3 and higher versions, you can require Aurora PostgreSQL DB cluster to accept only passwords that use the `scram-sha-256` algorithm\.

**Note**  
Currently, this capability isn't supported with RDS Proxy\.

Before making changes to your system, be sure you understand the complete process, as follows:
+ Obtain information about all roles and password encryption for all database users\. 
+ Double\-check the parameter settings for your Aurora PostgreSQL DB cluster for the parameters that control password encryption\.
+ If your Aurora PostgreSQL DB cluster uses a default parameter group, you need to create a custom DB cluster parameter group and apply it to your Aurora PostgreSQL DB cluster so that you can modify parameters when needed\. If your Aurora PostgreSQL DB cluster uses a custom parameter group, you can modify the necessary parameters later in the process, as needed\. 
+ Change the `password_encryption` parameter to `scram-sha-256`\.
+ Notify all database users that they need to update their passwords\. Do the same for your `postgres` account\. The new passwords are encrypted and stored using the `scram-sha-256` algorithm\.
+ Verify that all passwords are encrypted using as the type of encryption\. 
+ If all passwords use `scram-sha-256`, you can change the `rds.accepted_password_auth_method` from `md5+scram` to `scram-sha-256`\.

**Warning**  
After you change the `rds.accepted_password_auth_method` to `scram-sha-256` alone, any users \(roles\) with `md5`–encrypted passwords won't be able to connect\. 

#### Getting ready to require SCRAM for your Aurora PostgreSQL DB cluster<a name="PostgreSQL_Password_Encryption_configuration.getting-ready"></a>

Before making any changes to your Aurora PostgreSQL DB cluster, check all existing database user accounts and the type of encryption used for passwords\. You can do so by using the `rds_tools` extension\. This extension is supported on Aurora PostgreSQL 13\.1 and higher releases\. 

**To obtain a list of database users \(roles\) and password encryption methods**

1. Use `psql` to connect to the primary instance of your Aurora PostgreSQL DB cluster , as shown in the following:

   ```
   psql --host=cluster-name-instance-1.111122223333.aws-region.rds.amazonaws.com --port=5432 --username=postgres --password
   ```

1. Install the `rds_tools` extension:

   ```
   postgres=> CREATE EXTENSION rds_tools;
   CREATE EXTENSION
   ```

1. Get a listing of roles and encryption:

   ```
   postgres=> SELECT * FROM 
         rds_tools.role_password_encryption_type();
   ```

   You see output similar to the following:

   ```
          rolname        | encryption_type
   ----------------------+-----------------
    pg_monitor           |
    pg_read_all_settings |
    pg_read_all_stats    |
    pg_stat_scan_tables  |
    pg_signal_backend    |
    lab_tester           | md5
    user_465             | md5
    postgres             | md5
   (8 rows)
   ```

#### Creating custom DB cluster parameter group<a name="PostgreSQL_Password_Encryption_configuration.custom-parameter-group"></a>

**Note**  
If your Aurora PostgreSQL DB cluster already uses a custom parameter group, you don't need to create a new one\. 

For an overview of parameter groups for Aurora, see [Creating a DB cluster parameter group](USER_WorkingWithDBClusterParamGroups.md#USER_WorkingWithParamGroups.CreatingCluster)\. 

The pasword encryption type used for passwords is set in one parameter, `password_encryption`\. The encryption that the Aurora PostgreSQL DB cluster allows is set in another parameter, `rds.accepted_password_auth_method`\. Changing either of these from the default values requires that you create a custom DB cluster parameter group and apply it to your cluster\. 

**To create a custom DB cluster parameter group**

You can also use the AWS Management Console or the RDS API to create a custom DB cluster parameter group \. For more information, see [Creating a DB cluster parameter group](USER_WorkingWithDBClusterParamGroups.md#USER_WorkingWithParamGroups.CreatingCluster)\. 

1. Use the `[create\-db\-cluster\-parameter\-group](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster-parameter-group.html) ` CLI command to create the custom parameter group for the cluster\. This example uses `aurora-postgresql13` as the source for this custom parameter group\. 

   For Linux, macOS, or Unix:

   ```
   aws rds create-db-cluster-parameter-group --db-cluster-parameter-group-name 'docs-lab-scram-passwords' \
     --db-parameter-group-family aurora-postgresql13  --description 'Custom DB cluster parameter group for SCRAM'
   ```

   For Windows:

   ```
   aws rds create-db-cluster-parameter-group --db-cluster-parameter-group-name "docs-lab-scram-passwords" ^
     --db-parameter-group-family aurora-postgresql13  --description "Custom DB cluster parameter group for SCRAM"
   ```

   Next, associate the custom parameter group with your cluster\. 

1. Use the `[modify\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster.html)` CLI command to apply this custom parameter group to your Aurora PostgreSQL DB cluster: 

   For Linux, macOS, or Unix:

   ```
   aws rds modify-db-cluster --db-cluster-identifier 'your-instance-name' \
           ----db-cluster-parameter-group-name "docs-lab-scram-passwords
   ```

   For Windows:

   ```
   aws rds modify-db-cluster --db-cluster-identifier "your-instance-name" ^
           ----db-cluster-parameter-group-name "docs-lab-scram-passwords
   ```

   To re\-synchronize your Aurora PostgreSQL DB cluster with your custom DB cluster parameter group, you need to reboot the primary and all other instances of the cluster\. We recommend that you schedule this to occur during your regular maintenance window to minimize impact to your users\.

#### Configuring password encryption to use SCRAM<a name="PostgreSQL_Password_Encryption_configuration.configure-password-encryption"></a>

The password encryption mechanism used by an Aurora PostgreSQL DB cluster is set in the DB cluster parameter group the the `password_encryption` parameter\. Allowed values are unset, `md5`, or `scram-sha-256`\. The default value depends on the Aurora PostgreSQL version, as follows:
+ Aurora PostgreSQL 14 – Default is `scram-sha-256`
+ Aurora PostgreSQL 13 – Default is `md5`

With a custom DB cluster parameter group attached to your Aurora PostgreSQL DB cluster, you can modify values for the password encryption parameter\.

![\[The RDS Console shows the default values for the password_encryption parameters for Aurora PostgreSQL.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/apg-pwd-encryption-md5-scram-1.png)

**To change password encryption setting to `scram-sha-256`**
+ Change the value of the password encryption to `scram-sha-256`, as shown following\. The change can be applied immediately because the parameter is dynamic, so a restart isn't required for the change to take effect\. 

  For Linux, macOS, or Unix:

  ```
  aws rds modify-db-cluster-parameter-group --db-parameter-group-name \
    'docs-lab-scram-passwords' --parameters 'ParameterName=password_encryption,ParameterValue=scram-sha-256,ApplyMethod=immediate'
  ```

  For Windows:

  ```
  aws rds modify-db-parameter-group --db-parameter-group-name ^
    "docs-lab-scram-passwords" --parameters "ParameterName=password_encryption,ParameterValue=scram-sha-256,ApplyMethod=immediate"
  ```

#### Migrating passwords for user roles to SCRAM<a name="PostgreSQL_Password_Encryption_configuration.migrating-users"></a>



**To migrate database user \(role\) passwords from MD5 to SCRAM**

1. Log in as the administrator user \(default user name, `postgres`\) as shown following:

   ```
   psql --host=cluster-name-instance-1.111122223333.aws-region.rds.amazonaws.com --port=5432 --username=postgres --password
   ```

1. Check the setting of the `password_encryption` parameter on your RDS for PostgreSQL DB instance by using the following command:

   ```
   postgres=> SHOW password_encryption;
    password_encryption
   ---------------------
    md5
    (1 row)
   ```

1. Change the value of this parameter to `scram-sha-256`\. This is a dynamic parameter, so you don't need to reboot the instance after making this change\. Check the value again to make sure it's now set to `scram-sha-256`, as follows: 

   ```
   postgres=> SHOW password_encryption;
    password_encryption
   ---------------------
    scram-sha-256
    (1 row)
   ```

1. Notify all database users to change their passwords\. Be sure to also change your own password for account `postgres` \(the database user with `rds_superuser` privileges\)\. 

   ```
   labdb=> ALTER ROLE postgres WITH LOGIN PASSWORD 'change_me';
   ALTER ROLE
   ```

1. Repeat the process for all databases on your Aurora PostgreSQL DB cluster\. 

#### Changing parameter to require SCRAM<a name="PostgreSQL_Password_Encryption_configuration.require-scram"></a>

This is the final step in the process\. After you make the change in the following procedure, any user accounts \(roles\) that still use `md5` encryption for passwords won't be able to log in to the Aurora PostgreSQL DB cluster\. 

The `rds.accepted_password_auth_method` specifies the encryption method that the Aurora PostgreSQL DB cluster accepts for a user password during the login process\. The default value is `md5+scram`, meaning that either method is accepted\. In the image, you can find the default setting for this parameter\.

![\[The RDS console showing the default and allowed values for the rds.accepted_password_auth_method parameters.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/pwd-encryption-md5-scram-2.png)

The allowed values for this parameter are `md5+scram` or `scram` alone\. Changing this parameter value to `scram` makes this a requirement\. 

**To change the parameter value to require SCRAM authentication for passwords**

Verify that all database user passwords for all databases on your Aurora PostgreSQL DB cluster use `scram-sha-256` for password encryption\. 

1. Query `rds_tools` to verify that all passwords are using `scram-sha-256` for encryption, as follows:

   ```
   postgres=> SELECT * FROM rds_tools.role_password_encryption_type();
     rolname        | encryption_type
     ----------------------+-----------------
     pg_monitor           |
     pg_read_all_settings |
     pg_read_all_stats    |
     pg_stat_scan_tables  |
     pg_signal_backend    |
     lab_tester           | scram-sha-256
     user_465             | scram-sha-256
     postgres             | scram-sha-256
     ( rows)
   ```

1. Repeat the query across all database instances in your Aurora PostgreSQL DB cluster\. 

   If all passwords use `scram-sha-256`, you can proceed\. 

1. Change the value of the accepted password authentication to `scram-sha-256`, as follows:

   For Linux, macOS, or Unix:

   ```
   aws rds modify-db-cluster-parameter-group --db-cluster-parameter-group-name 'docs-lab-scram-passwords' \
     --parameters 'ParameterName=rds.accepted_password_auth_method,ParameterValue=scram,ApplyMethod=immediate'
   ```

   For Windows:

   ```
   aws rds modify-db-cluster-parameter-group --db-cluster-parameter-group-name "docs-lab-scram-passwords" ^
     --parameters "ParameterName=rds.accepted_password_auth_method,ParameterValue=scram,ApplyMethod=immediate"
   ```