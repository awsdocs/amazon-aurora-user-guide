# Connecting to your DB cluster using IAM authentication and the AWS SDK for Python \(Boto3\)<a name="UsingWithRDS.IAMDBAuth.Connecting.Python"></a>

You can connect to an Aurora MySQL or Aurora PostgreSQL DB cluster with the AWS SDK for Python \(Boto3\) as described following\.

The following are prerequisites for connecting to your DB cluster using IAM authentication:
+ [Enabling and disabling IAM database authentication](UsingWithRDS.IAMDBAuth.Enabling.md)
+ [Creating and using an IAM policy for IAM database access](UsingWithRDS.IAMDBAuth.IAMPolicy.md)
+ [Creating a database account using IAM authentication](UsingWithRDS.IAMDBAuth.DBAccounts.md)

In addition, make sure the imported libraries in the sample code exist on your system\.

The code examples use profiles for shared credentials\. For information about the specifying credentials, see [Credentials](https://boto3.amazonaws.com/v1/documentation/api/latest/index.htmlguide/credentials.html) in the AWS SDK for Python \(Boto3\) documentation\.

**Topics**
+ [Generating an IAM authentication token](#UsingWithRDS.IAMDBAuth.Connecting.Python.AuthToken)
+ [Connecting to a DB cluster](#UsingWithRDS.IAMDBAuth.Connecting.Python.AuthToken.Connect)

## Generating an IAM authentication token<a name="UsingWithRDS.IAMDBAuth.Connecting.Python.AuthToken"></a>

You can call the `generate_db_auth_token` method to obtain a signed token\. Provide the DB cluster endpoint, port, user name, AWS Region, and DB engine to generate the token for connecting to a DB cluster with IAM credentials\.

This code generates an IAM authentication token for an Aurora MySQL DB cluster\.

```
import sys
import boto3
import os

ENDPOINT="mysqlcluster.cluster-123456789012.us-east-1.rds.amazonaws.com"
PORT="3306"
USR="jane_doe"
REGION="us-east-1"
os.environ['LIBMYSQL_ENABLE_CLEARTEXT_PLUGIN'] = '1'

#gets the credentials from .aws/credentials
session = boto3.Session(profile_name='RDSCreds')
client = session.client('rds')

token = client.generate_db_auth_token(DBHostname=ENDPOINT, Port=PORT, DBUsername=USR, Region=REGION)
```

This code generates an IAM authentication token for an Aurora PostgreSQL DB cluster\.

```
import sys
import boto3
import os

ENDPOINT="postgresmycluster.cluster-123456789012.us-east-1.rds.amazonaws.com"
PORT="5432"
USR="jane_doe"
REGION="us-east-1"

#gets the credentials from .aws/credentials
session = boto3.Session(profile_name='RDSCreds')
client = session.client('rds')

token = client.generate_db_auth_token(DBHostname=ENDPOINT, Port=PORT, DBUsername=USR, Region=REGION)
```

## Connecting to a DB cluster<a name="UsingWithRDS.IAMDBAuth.Connecting.Python.AuthToken.Connect"></a>

The following code example shows how to generate an authentication token, and then use it to connect to a DB cluster\. 

To run this code example, you need the [AWS SDK for Python \(Boto3\)](http://aws.amazon.com/sdk-for-python/), found on the AWS site\.

Modify the values of the following variables as needed:
+ `ENDPOINT` – The endpoint of the DB cluster that you want to access
+ `PORT` – The port number used for connecting to your DB cluster
+ `USER` – The database account that you want to access\.
+ `REGION` – The AWS Region where the DB cluster is running
+ `DBNAME` – The database that you want to access

This code connects to an Aurora MySQL DB cluster\.

```
import mysql.connector
import sys
import boto3
import os

ENDPOINT="mysqlcluster.cluster-123456789012.us-east-1.rds.amazonaws.com"
PORT="3306"
USR="jane_doe"
REGION="us-east-1"
DBNAME="mydb"
os.environ['LIBMYSQL_ENABLE_CLEARTEXT_PLUGIN'] = '1'

#gets the credentials from .aws/credentials
session = boto3.Session(profile_name='default')
client = session.client('rds')

token = client.generate_db_auth_token(DBHostname=ENDPOINT, Port=PORT, DBUsername=USR, Region=REGION)

try:
    conn =  mysql.connector.connect(host=ENDPOINT, user=USR, passwd=token, port=PORT, database=DBNAME, ssl_ca='[full path]rds-combined-ca-bundle.pem')
    cur = conn.cursor()
    cur.execute("""SELECT now()""")
    query_results = cur.fetchall()
    print(query_results)
except Exception as e:
    print("Database connection failed due to {}".format(e))
```

This code connects to an Aurora PostgreSQL DB cluster\.

```
import psycopg2
import sys
import boto3
import os

ENDPOINT="postgresmycluster.cluster-123456789012.us-east-1.rds.amazonaws.com"
PORT="5432"
USR="jane_doe"
REGION="us-east-1"
DBNAME="mydb"

#gets the credentials from .aws/credentials
session = boto3.Session(profile_name='RDSCreds')
client = session.client('rds')

token = client.generate_db_auth_token(DBHostname=ENDPOINT, Port=PORT, DBUsername=USR, Region=REGION)

try:
    conn = psycopg2.connect(host=ENDPOINT, port=PORT, database=DBNAME, user=USR, password=token, sslmode='prefer', sslrootcert="[full path]rds-combined-ca-bundle.pem")
    cur = conn.cursor()
    cur.execute("""SELECT now()""")
    query_results = cur.fetchall()
    print(query_results)
except Exception as e:
    print("Database connection failed due to {}".format(e))
```