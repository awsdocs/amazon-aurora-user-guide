# LWLock:MultiXact<a name="apg-waits.lwlockmultixact"></a>

The `LWLock:MultiXactMemberBuffer`, `LWLock:MultiXactMemberBuffer`, `LWLock:MultiXactMemberSLRU`, and `LWLock:MultiXactOffsetSLRU` wait events indicate that a session is waiting to retrieve a list of transactions that involve the same row in a given table\. 
+ `LWLock:MultiXactMemberBuffer` – A process is waiting for I/O on a simple least\-recently used \(SLRU\) buffer for a multixact member\.
+ `LWLock:MultiXactMemberSLRU` – A process is waiting to access the simple least\-recently used \(SLRU\) cache for a multixact member\.
+ `LWLock:MultiXactOffsetBuffer` – A process is waiting for I/O on a simple least\-recently used \(SLRU\) buffer for a multixact offset\.
+ `LWLock:MultiXactOffsetSLRU` – A process is waiting to access the simple least\-recently used \(SLRU\) cache for a multixact offset\.

**Topics**
+ [Supported engine versions](#apg-waits.xactsync.context.supported)
+ [Context](#apg-waits.lwlockmultixact.context)
+ [Likely causes of increased waits](#apg-waits.lwlockmultixact.causes)
+ [Actions](#apg-waits.lwlockmultixact.actions)

## Supported engine versions<a name="apg-waits.xactsync.context.supported"></a>

This wait event information is supported for all versions of Aurora PostgreSQL\.

## Context<a name="apg-waits.lwlockmultixact.context"></a>

A *multixact* is a secondary data structure that stores a list of transaction IDs \(XIDs\) that reference the same table row\. When a single transaction references a row in a table, the transaction ID is stored in the table\. When multiple transactions reference the same row in a table, the list of transaction IDs is stored in the multixact data structure\. The multixact wait events indicate that a session is retrieving from the data structure the list of transactions that refer to a given row in a table\.

## Likely causes of increased waits<a name="apg-waits.lwlockmultixact.causes"></a>

Three common causes of multixact use are as follows\.

1. **Sub\-transactions from explicit savepoints\.** – Explicitly creating a savepoint in your transactions spawns new transactions for the same row\. For example, using `SELECT FOR UPDATE`, then `SAVEPOINT`, and then `UPDATE`\. 

1. **Sub\-transactions from PL/pgSQL EXCEPTION clauses\.** – Each `EXCEPTION` clause that you write in your PL/pgSQL functions or procedures creates a `SAVEPOINT` internally\.

1. **Foreign keys** – Multiple transactions acquire a shared lock on the parent record\.

Some drivers, object\-relational mappers \(ORMs\), and abstraction layers have configuration options for automatically wrapping all operations with savepoints\. This can generate many multixact wait events in some workloads\. The PostgreSQL JDBC Driver's `autosave` option is an example of this\. For more information, see [pgJDBC](https://jdbc.postgresql.org/) in the PostgreSQL JDBC documentation\. Another example is the PostgreSQL ODBC driver and its `protocol` option\. For more information, see [psqlODBC Configuration Options](https://odbc.postgresql.org/docs/config.html) in the PostgreSQL ODBC driver documentation\. 

When a given row is included in a multiple transaction operation, processing the row requires retrieving transaction IDs from the `multixact` listings\. If lookups can't get the multixact from the memory cache, the data structure must be read from the Aurora storage layer\. This I/O from storage means that SQL queries can take longer\. Memory cache misses can start occurring with heavy usage due to a large number of multiple transactions\. All these factors contribute to an increase in this wait event\.

## Actions<a name="apg-waits.lwlockmultixact.actions"></a>

The vacuum process eventually removes multixacts\. If your Aurora PostgreSQL DB cluster experiences many `LWLock:MultiXact` wait events, check that your Aurora PostgreSQL DB cluster's vacuum process is configured optimally, to run as often as necessary to keep these wait events to a minimum\. 

Long\-running transactions cause the vacuum to retain transaction information until the transaction is committed or until the read\-only transaction is closed\. We recommend that you proactively monitor and manage long\-running transactions\. If possible, modify your application to avoid or minimize your use of long\-running transactions\. 

If this wait event spikes suddenly and affects your production environment, you can try using `VACUUM FREEZE` on the problem table to resolve the issue immediately\. However, this is only a temporary solution\. You should analyze the DDL \(data definition language\) used to create your tables and make sure the table structures and indexes are well designed\. 