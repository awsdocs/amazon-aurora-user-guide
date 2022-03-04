# Working with extensions and Amazon Aurora PostgreSQL<a name="Appendix.PostgreSQL.CommonDBATasks"></a>

## Using the oracle\_fdw extension to access foreign data in Aurora PostgreSQL<a name="Appendix.PostgreSQL.CommonDBATasks.oracle_fdw"></a>

For easy and efficient access to Oracle databases for Aurora PostgreSQL, you can use the PostgreSQL oracle\_fdw extension, which provides a foreign data wrapper\. For a complete description of this extension, see the [oracle\_fdw](https://github.com/laurenz/oracle_fdw) documentation\. 

The oracle\_fdw extension is supported on Amazon RDS for PostgreSQL versions 12\.7, 13\.3, and higher\.

### Turning on the oracle\_fdw extension<a name="postgresql-oracle-fdw.enabling"></a>

To use the oracle\_fdw extension, perform the following procedure\.

**To turn on the oracle\_fdw extension**
+ Run the following command using an account that has `rds_superuser` permissions\.

  ```
  CREATE EXTENSION oracle_fdw;
  ```

### Example using a foreign server linked to an RDS for Oracle database<a name="postgresql-oracle-fdw.example"></a>

The following example demonstrates using a foreign server linked to an RDS for Oracle database\.  

**To create a foreign server linked to an RDS for Oracle database**

1. Note the following for the RDS for Oracle DB instance:
   + Endpoint
   + Port
   + Database name

1. Create a foreign server\.

   ```
   test=> CREATE SERVER oradb FOREIGN DATA WRAPPER oracle_fdw OPTIONS (dbserver '//endpoint:port/DB_name');
   CREATE SERVER
   ```

1. Grant usage to a user who doesn't have `rds_superuser` permissions, for example `user1`\.

   ```
   test=> GRANT USAGE ON FOREIGN SERVER oradb TO user1;
   GRANT
   ```

1. Connect as `user1` and create a mapping to an Oracle user\.

   ```
   test=> CREATE USER MAPPING FOR user1 SERVER oradb OPTIONS (user 'oracleuser', password 'mypassword');
   CREATE USER MAPPING
   ```

1. Create a foreign table linked to an Oracle table\.

   ```
   test=> create foreign table mytab (a int) SERVER oradb OPTIONS (table 'MYTABLE');
   CREATE FOREIGN TABLE
   ```

1. Query the foreign table\.

   ```
   test=> select * from mytab;
    a
   ---
    1
   (1 row)
   ```

If the query reports the following error, check your security group and access control list \(ACL\) to make sure that both instances can communicate\.  

```
ERROR: connection for foreign table "mytab" cannot be established
DETAIL: ORA-12170: TNS:Connect timeout occurred
```

### Working with encryption in transit<a name="postgresql-oracle-fdw.encryption"></a>

PostgreSQL\-to\-Oracle encryption in transit is based on a combination of client and server configuration parameters\. For an example using Oracle 21c, see [About the Values for Negotiating Encryption and Integrity](https://docs.oracle.com/en/database/oracle/oracle-database/21/dbseg/configuring-network-data-encryption-and-integrity.html#GUID-3A2AF4AA-AE3E-446B-8F64-31C48F27A2B5) in the Oracle documentation\. The client used for oracle\_fdw on RDS is configured with `ACCEPTED`, meaning that the encryption depends on the Oracle database server configuration\.

### pg\_user\_mapping and pg\_user\_mappings permissions<a name="postgresql-oracle-fdw.permissions"></a>

In the following table, you can find an illustration of user mapping permissions using the example roles\. The `rdssu1` and `rdssu2` users have the `rds_superuser` role, and the `user1` user doesn't\.

**Note**  
You can use the `\du` metacommand in psql to list existing roles\.

```
test=> \du
                                                               List of roles
    Role name    |                         Attributes                         |                          Member of
-----------------+------------------------------------------------------------+-------------------------------------------------------------
 rdssu1          |                                                            | {rds_superuser}
 rdssu2          |                                                            | {rds_superuser}
 user1           |                                                            | {}
```

Users with the `rds_superuser` role can't query the `pg_user_mapping` table\. The following example uses `rdssu1`,

```
test=> SET SESSION AUTHORIZATION rdssu1;
SET
test=> select * from pg_user_mapping;
ERROR: permission denied for table pg_user_mapping
```

On RDS for PostgreSQL, all users—even ones with the `rds_superuser` role—can see only their own `umoptions` values in the `pg_user_mappings` table\. The following example demonstrates\.

```
test=> SET SESSION AUTHORIZATION rdssu1;
SET
test=> select * from pg_user_mappings;
 umid  | srvid | srvname | umuser | usename    |            umoptions
-------+-------+---------+--------+------------+----------------------------------
 16414 | 16411 | oradb   |  16412 | user1      |
 16423 | 16411 | oradb   |  16421 | rdssu1     | {user=oracleuser,password=mypwd}
 16424 | 16411 | oradb   |  16422 | rdssu2     |
(3 rows)

test=> SET SESSION AUTHORIZATION rdssu2;
SET
test=> select * from pg_user_mappings;
 umid  | srvid | srvname | umuser | usename    |            umoptions
-------+-------+---------+--------+------------+----------------------------------
 16414 | 16411 | oradb   |  16412 | user1      |
 16423 | 16411 | oradb   |  16421 | rdssu1     |
 16424 | 16411 | oradb   |  16422 | rdssu2     | {user=oracleuser,password=mypwd}
(3 rows)

test=> SET SESSION AUTHORIZATION user1;
SET
test=> select * from pg_user_mappings;
 umid  | srvid | srvname | umuser | usename    |           umoptions
-------+-------+---------+--------+------------+--------------------------------
 16414 | 16411 | oradb   |  16412 | user1      | {user=oracleuser,password=mypwd}
 16423 | 16411 | oradb   |  16421 | rdssu1     |
 16424 | 16411 | oradb   |  16422 | rdssu2     |
(3 rows)
```

Because of differences in implementation of `information_schema._pg_user_mappings` and `pg_catalog.pg_user_mappings`, a manually created `rds_superuser` requires additional permissions to view passwords in `pg_catalog.pg_user_mappings`\.

No additional permissions are required for a user with the `rds_superuser` role to view passwords in `information_schema._pg_user_mappings`\.

Users who don't have the `rds_superuser` role can view passwords in `pg_user_mappings` only under the following conditions:
+ The current user is the user being mapped and owns the server or holds the `USAGE` privilege on it\.
+ The current user is the server owner and the mapping is for `PUBLIC`\.