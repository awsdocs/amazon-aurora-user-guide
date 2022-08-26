# Master user account privileges<a name="UsingWithRDS.MasterAccounts"></a>

When you create a new DB cluster, the default master user that you use gets certain privileges for that DB cluster\. You can't change the master user name after the DB cluster is created\.

**Important**  
We strongly recommend that you do not use the master user directly in your applications\. Instead, adhere to the best practice of using a database user created with the minimal privileges required for your application\.

**Note**  
If you accidentally delete the permissions for the master user, you can restore them by modifying the DB cluster and setting a new master user password\. For more information about modifying a DB cluster, see [Modifying an Amazon Aurora DB cluster](Aurora.Modifying.md)\.

The following table shows the privileges and database roles the master user gets for each of the database engines\.


| Database engine | System privilege | Database role | 
| --- | --- | --- | 
| Amazon Aurora MySQL | `CREATE`, `DROP`, `GRANT OPTION`, `REFERENCES`, `EVENT`, `ALTER`, `DELETE`, `INDEX`, `INSERT`, `SELECT`, `UPDATE`, `CREATE TEMPORARY TABLES`, `LOCK TABLES`, `TRIGGER`, `CREATE VIEW`, `SHOW VIEW`, `LOAD FROM S3`, `SELECT INTO S3`, `ALTER ROUTINE`, `CREATE ROUTINE`, `EXECUTE`, `CREATE USER`, `PROCESS`, `SHOW DATABASES `, `RELOAD`, `REPLICATION CLIENT`, `REPLICATION SLAVE` | — | 
| Amazon Aurora PostgreSQL | `LOGIN`, `NOSUPERUSER`, `INHERIT`, `CREATEDB`, `CREATEROLE`, `NOREPLICATION`, `VALID UNTIL 'infinity'` | `RDS_SUPERUSER` For more information about RDS\_SUPERUSER, see [Understanding PostgreSQL roles and permissions](Appendix.PostgreSQL.CommonDBATasks.Roles.md)\. | 