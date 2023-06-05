# Connecting to your DB cluster using IAM authentication from the command line: AWS CLI and psql client<a name="UsingWithRDS.IAMDBAuth.Connecting.AWSCLI.PostgreSQL"></a>

You can connect from the command line to an Aurora PostgreSQL DB cluster with the AWS CLI and psql command line tool as described following\.

**Prerequisites**  
The following are prerequisites for connecting to your DB cluster using IAM authentication:
+ [Enabling and disabling IAM database authentication](UsingWithRDS.IAMDBAuth.Enabling.md)
+ [Creating and using an IAM policy for IAM database access](UsingWithRDS.IAMDBAuth.IAMPolicy.md)
+ [Creating a database account using IAM authentication](UsingWithRDS.IAMDBAuth.DBAccounts.md)

**Note**  
For information about connecting to your database using pgAdmin with IAM authentication, see the blog post [Using IAM authentication to connect with pgAdmin Amazon Aurora PostgreSQL or Amazon RDS for PostgreSQL](http://aws.amazon.com/blogs/database/using-iam-authentication-to-connect-with-pgadmin-amazon-aurora-postgresql-or-amazon-rds-for-postgresql/)\.

**Topics**
+ [Generating an IAM authentication token](#UsingWithRDS.IAMDBAuth.Connecting.AWSCLI.AuthToken.PostgreSQL)
+ [Connecting to an Aurora PostgreSQL cluster](#UsingWithRDS.IAMDBAuth.Connecting.AWSCLI.Connect.PostgreSQL)

## Generating an IAM authentication token<a name="UsingWithRDS.IAMDBAuth.Connecting.AWSCLI.AuthToken.PostgreSQL"></a>

The authentication token consists of several hundred characters so it can be unwieldy on the command line\. One way to work around this is to save the token to an environment variable, and then use that variable when you connect\. The following example shows how to use the AWS CLI to get a signed authentication token using the `generate-db-auth-token` command, and store it in a `PGPASSWORD` environment variable\.

```
export RDSHOST="mypostgres-cluster.cluster-123456789012.us-west-2.rds.amazonaws.com"
export PGPASSWORD="$(aws rds generate-db-auth-token --hostname $RDSHOST --port 5432 --region us-west-2 --username jane_doe )"
```

In the example, the parameters to the `generate-db-auth-token` command are as follows:
+ `--hostname` – The host name of the DB cluster \(cluster endpoint\) that you want to access
+ `--port` – The port number used for connecting to your DB cluster
+ `--region` – The AWS Region where the DB cluster is running
+ `--username` – The database account that you want to access

The first several characters of the generated token look like the following\.

```
mypostgres-cluster.cluster-123456789012.us-west-2.rds.amazonaws.com:5432/?Action=connect&DBUser=jane_doe&X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Expires=900...
```

**Note**  
You cannot use a custom Route 53 DNS record or an Aurora custom endpoint instead of the DB cluster endpoint to generate the authentication token\.

## Connecting to an Aurora PostgreSQL cluster<a name="UsingWithRDS.IAMDBAuth.Connecting.AWSCLI.Connect.PostgreSQL"></a>

The general format for using psql to connect is shown following\.

```
psql "host=hostName port=portNumber sslmode=verify-full sslrootcert=full_path_to_ssl_certificate dbname=DBName user=userName password=authToken"
```

The parameters are as follows:
+ `host` – The host name of the DB cluster \(cluster endpoint\) that you want to access
+ `port` – The port number used for connecting to your DB cluster
+ `sslmode` – The SSL mode to use

  When you use `sslmode=verify-full`, the SSL connection verifies the DB cluster endpoint against the endpoint in the SSL certificate\.
+ `sslrootcert` – The full path to the SSL certificate file that contains the public key

  For more information, see [Securing Aurora PostgreSQL data with SSL/TLS](AuroraPostgreSQL.Security.md#AuroraPostgreSQL.Security.SSL)\.

  To download an SSL certificate, see [Using SSL/TLS to encrypt a connection to a DB cluster](UsingWithRDS.SSL.md)\.
+ `dbname` – The database that you want to access
+ `user` – The database account that you want to access
+ `password` – A signed IAM authentication token

**Note**  
You cannot use a custom Route 53 DNS record or an Aurora custom endpoint instead of the DB cluster endpoint to generate the authentication token\.

The following example shows using psql to connect\. In the example, psql uses the environment variable `RDSHOST` for the host and the environment variable `PGPASSWORD` for the generated token\. Also, */sample\_dir/* is the full path to the SSL certificate file that contains the public key\.

```
export RDSHOST="mypostgres-cluster.cluster-123456789012.us-west-2.rds.amazonaws.com"
export PGPASSWORD="$(aws rds generate-db-auth-token --hostname $RDSHOST --port 5432 --region us-west-2 --username jane_doe )"
                    
psql "host=$RDSHOST port=5432 sslmode=verify-full sslrootcert=/sample_dir/global-bundle.pem dbname=DBName user=jane_doe password=$PGPASSWORD"
```

If you want to connect to a DB cluster through a proxy, see [Connecting to a proxy using IAM authentication](rds-proxy-setup.md#rds-proxy-connecting-iam)\.