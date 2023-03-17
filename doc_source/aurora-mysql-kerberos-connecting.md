# Connecting to Aurora MySQL with Kerberos authentication<a name="aurora-mysql-kerberos-connecting"></a>

To avoid errors, use a MySQL client with version 8\.0\.26 or higher on Unix platforms, 8\.0\.27 or higher on Windows\.

## Using the Aurora MySQL Kerberos login to connect to the DB cluster<a name="aurora-mysql-kerberos-connecting.login"></a>

To connect to Aurora MySQL with Kerberos authentication, you log in as a database user that you created using the instructions in [Step 6: Create Aurora MySQL users that use Kerberos authentication](aurora-mysql-kerberos-setting-up.md#aurora-mysql-kerberos-setting-up.create-logins)\.

At a command prompt, connect to one of the endpoints associated with your Aurora MySQL DB cluster\. When you're prompted for the password, enter the Kerberos password associated with that user name\.

When you authenticate with Kerberos, a *ticket\-granting ticket* \(TGT\) is generated if one doesn't already exist\. The `authentication_kerberos` plugin uses the TGT to get a *service ticket*, which is then presented to the Aurora MySQL database server\.

You can use the MySQL client to connect to Aurora MySQL with Kerberos authentication using either Windows or Unix\.

### Unix<a name="aurora-mysql-kerberos-connecting.login.unix"></a>

You can connect by using either one of the following methods:
+ Obtain the TGT manually\. In this case, you don't need to supply the password to the MySQL client\.
+ Supply the password for the Active Directory login directly to the MySQL client\.

The client\-side plugin is supported on Unix platforms for MySQL client versions 8\.0\.26 and higher\.

**To connect by obtaining the TGT manually**

1. At the command line interface, use the following command to obtain the TGT\.

   ```
   kinit user_name
   ```

1. Use the following `mysql` command to log in to the DB instance endpoint of your DB cluster\.

   ```
   mysql -h DB_instance_endpoint -P 3306 -u user_name -p
   ```
**Note**  
Authentication can fail if the keytab is rotated on the DB instance\. In this case, obtain a new TGT by rerunning `kinit`\.

**To connect directly**

1. At the command line interface, use the following `mysql` command to log in to the DB instance endpoint of your DB cluster\.

   ```
   mysql -h DB_instance_endpoint -P 3306 -u user_name -p
   ```

1. Enter the password for the Active Directory user\.

### Windows<a name="aurora-mysql-kerberos-connecting.login.win"></a>

On Windows, authentication is usually done at login time, so you don't need to obtain the TGT manually to connect to the Aurora MySQL DB cluster\. The case of the database user name must match the character case of the user in the Active Directory\. For example, if the user in the Active Directory appears as `Admin`, the database user name must be `Admin`\.

The client\-side plugin is supported on Windows for MySQL client versions 8\.0\.27 and higher\.

**To connect directly**
+ At the command line interface, use the following `mysql` command to log in to the DB instance endpoint of your DB cluster\.

  ```
  mysql -h DB_instance_endpoint -P 3306 -u user_name
  ```

## Kerberos authentication with Aurora global databases<a name="aurora-mysql-kerberos-connecting.global"></a>

Kerberos authentication for Aurora MySQL is supported for Aurora global databases\. To authenticate users on the secondary DB cluster using the Active Directory of the primary DB cluster, replicate the Active Directory to the secondary AWS Region\. You turn on Kerberos authentication on the secondary cluster using the same domain ID as for the primary cluster\. AWS Managed Microsoft AD replication is supported only with the Enterprise version of Active Directory\. For more information, see [Multi\-Region replication](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_configure_multi_region_replication.html) in the *AWS Directory Service Administration Guide*\.

## Migrating from RDS for MySQL to Aurora MySQL<a name="aurora-mysql-kerberos-connecting.rds"></a>

After you migrate from RDS for MySQL with Kerberos authentication enabled to Aurora MySQL, modify users created with the `auth_pam` plugin to use the `authentication_kerberos` plugin\. For example:

```
ALTER USER user_name IDENTIFIED WITH 'authentication_kerberos' BY 'realm_name';
```

## Preventing ticket caching<a name="aurora-mysql-kerberos.destroy-tickets"></a>

If a valid TGT doesn't exist when the MySQL client application starts, the application can obtain and cache the TGT\. If you want to prevent the TGT from being cached, set a configuration parameter in the `/etc/krb5.conf` file\.

**Note**  
This configuration only applies to client hosts running Unix, not Windows\.

**To prevent TGT caching**
+ Add an `[appdefaults]` section to `/etc/krb5.conf` as follows:

  ```
  [appdefaults]
    mysql = {
      destroy_tickets = true
    }
  ```

## Logging for Kerberos authentication<a name="aurora-mysql-kerberos.logging"></a>

The `AUTHENTICATION_KERBEROS_CLIENT_LOG` environment variable sets the logging level for Kerberos authentication\. You can use the logs for client\-side debugging\.

The permitted values are 1–5\. Log messages are written to the standard error output\. The following table describes each logging level\.


| Logging level | Description | 
| --- | --- | 
| 1 or not set | No logging | 
| 2 | Error messages | 
| 3 | Error and warning messages | 
| 4 | Error, warning, and information messages | 
| 5 | Error, warning, information, and debug messages | 