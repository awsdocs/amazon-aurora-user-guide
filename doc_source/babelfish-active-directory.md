# Babelfish for Aurora PostgreSQL with AWS Managed Microsoft Active Directory<a name="babelfish-active-directory"></a>

Babelfish for Aurora PostgreSQL 15\.2 version supports authentication to your DB cluster using Kerberos\. This method allows you to use Microsoft Windows Authentication to authenticate users when they connect to your Babelfish database\. To do so, you must first configure your DB cluster to use AWS Directory Service for Microsoft Active Directory for Kerberos authentication\. For more information, see [What is AWS Directory Service?](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/what_is.html) in the *AWS Directory Service Administration Guide*\.

## Setting up Kerberos Authentication<a name="babelfish-active-directory-setup"></a>

 Babelfish for Aurora PostgreSQL DB cluster can connect using two different ports, but Kerberos authentication setup is a one\-time process\. Therefore, you have to first set up Kerberos authentication for your DB cluster\. For more information, see [ Setting up Kerberos authentication](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/postgresql-kerberos-setting-up.html)\. After completing the setup, ensure that you can connect with a PostgreSQL client using Kerberos\. For more information, see [Connecting with Kerberos Authentication](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/postgresql-kerberos-connecting.html)\. 

## Login and user provisioning in Babelfish<a name="babelfish-active-directory-login"></a>

 Windows logins created from the Tabular Data Stream \(TDS\) port can be used either with the TDS port or the PostgreSQL port\. First, these logins that can use Kerberos for authentication have to be provisioned from the TDS port before it is used by the T\-SQL users and applications to connect to a Babelfish database\. When creating Windows logins, administrators can provide the login using either the Fully Qualified Domain Name \(FQDN\) or the NetBIOS domain name\. NetBIOS domain is the subdomain of the FQDN\. For example, if the FQDN is `CORP.EXAMPLE.COM`, then NetBIOS domain can be `CORP`\. If the NetBIOS domain name format is provided for a login, a mapping must exist to the FQDN\. 

### Managing NetBIOS domain name to FQDN mapping<a name="babelfish-active-directory-login-netbios"></a>

To manage mappings between NetBIOS domain name and FQDN, Babelfish provides system stored procedures to add, remove, and truncate mappings\. Only a user with a `sysadmin` role can run these procedures\.

To create mapping between NetBIOS and FQDN, use the Babelfish provided system stored procedure `babelfish_add_domain_mapping_entry`\.

**Example**  

```
EXEC babelfish_add_domain_mapping_entry 'netbios_domain_name', 'fully_qualified_domain_name'
```
Both arguments must have a valid value and are not NULL\.

The following example shows how to create the mapping between the NetBIOS name CORP and FQDN CORP\.EXAMPLE\.COM:

**Example**  

```
EXEC babelfish_add_domain_mapping_entry 'corp', 'corp.example.com'
```
You must enter the NetBIOS domain name in lowercase\.

To delete an existing mapping entry, use the system stored procedure babelfish\_remove\_domain\_mapping\_entry:

**Example**  

```
EXEC babelfish_remove_domain_mapping_entry 'netbios_domain_name'
```
You must enter the NetBIOS domain name in lowercase\.

The following example shows how to remove the mapping for the NetBIOS name CORP:

**Example**  

```
EXEC babelfish_remove_domain_mapping_entry 'corp'
```

To remove all existing mapping entries, use the system stored procedure babelfish\_truncate\_domain\_mapping\_table:

**Example**  

```
EXEC babelfish_truncate_domain_mapping_table
```

To view all mappings between NetBIOS and FQDN, use the following query:

**Example**  

```
SELECT netbios_domain_name, fq_domain_name FROM babelfish_domain_mapping;
```

### Managing Logins<a name="babelfish-active-directory-login-managing"></a>

**Create logins**  
Connect to the DB through the TDS endpoint using a login that has the correct permissions\. If there is no database user created for the login, then the login is mapped to guest user\. If the guest user is not enabled, then the login attempt fails\.

Create a Windows login using the following query\. The `FROM WINDOWS` option allows authentication using Active Directory\.

```
CREATE LOGIN login_name FROM WINDOWS [WITH DEFAULT_DATABASE=database]
```

**Example**  
The following example shows creating a login for the Active Directory user \[corp\\test1\] with a default database of db1\.

```
CREATE LOGIN [corp\test1] FROM WINDOWS WITH DEFAULT_DATABASE=db1
```

This example assumes that there is a mapping between the NetBIOS domain CORP and the FQDN CORP\.EXAMPLE\.COM\. If there is no mapping, then you must provide the FQDN \[CORP\.EXAMPLE\.COM\\test1\]\.

**Drop login**  
To drop a login, use the same syntax as for any login, as shown in the following example:

```
DROP LOGIN [domain\login]
```

**Alter login**  
To alter a login, use the same syntax as for any login, as in the following example:

```
ALTER LOGIN [domain\login] { ENABLE|DISABLE|WITH DEFAULT_DATABASE=[master] }
```

The ALTER LOGIN command supports limited options for Windows logins, including the following:
+ DISABLE – To disable a login\. You can't use a disabled login for authentication\.
+ ENABLE – To enable a disabled login\.
+ DEFAULT\_DATABASE – To change the default database of a login\.

**Note**  
All password management is performed through AWS Directory Service, so the ALTER LOGIN command doesn't allow database administrators to change or set passwords for Windows logins\.

### Connecting to Babelfish for Aurora PostgreSQL with Kerberos authentication<a name="babelfish-active-directory-kerberos"></a>

Typically, the database users who authenticate using Kerberos are doing so from their client machines\. These machines are members of the Active Directory domain\. They use Windows Authentication from their client applications to access the Babelfish for Aurora PostgreSQL server on the TDS port\.

### Connecting to Babelfish for Aurora PostgreSQL on the PostgreSQL port with Kerberos authentication<a name="babelfish-active-directory-kerberos-pgport"></a>

 You can use logins created from the TDS port with either the TDS port or the PostgreSQL port\. However, PostgreSQL uses case\-sensitive comparisons by default for usernames\. For Aurora PostgreSQL to interpret Kerberos usernames as case\-insensitive, you must set the `krb_caseins_users` parameter as `true` in the custom Babelfish cluster parameter group\. This parameter is set to `false` by default\. For more information, see [ Configuring for case\-insensitive user names](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/postgresql-kerberos-setting-up.html#postgresql-kerberos-setting-up.create-logins.set-case-insentive)\. In addition, you must specify the login username in the format <login@FQDN> from the PostgreSQL client applications\. You can't use <domain\\login> format \. 

### Frequently occurring errors<a name="babelfish-active-directory-errors"></a>

You can configure forest trust relationships between your on\-premises Microsoft Active Directory and the AWS Managed Microsoft AD\. For more information, see [Create a trust relationship](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/postgresql-kerberos-setting-up.html#postgresql-kerberos-setting-up.create-trust)\. Then, you must connect using a specialized domain specific endpoint instead of using the Amazon domain `rds.amazonaws.com` in the host endpoint\. If you don't use the correct domain specific endpoint, you might encounter the following error:

```
Error: “Authentication method "NTLMSSP" not supported (Microsoft SQL Server, Error: 514)"
```

 This error occurs when the TDS client can't cache the service ticket for the supplied endpoint URL\. For more information, see [Connecting with Kerberos](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/postgresql-kerberos-connecting.html)\. 