# Timeout:PgSleep<a name="apg-waits.timeoutpgsleep"></a>

The `Timeout:PgSleep` event occurs when a server process has called the `pg_sleep` function and is waiting for the sleep timeout to expire\.

**Topics**
+ [Supported engine versions](#apg-waits.timeoutpgsleep.context.supported)
+ [Likely causes of increased waits](#apg-waits.timeoutpgsleep.causes)
+ [Actions](#apg-waits.timeoutpgsleep.actions)

## Supported engine versions<a name="apg-waits.timeoutpgsleep.context.supported"></a>

This wait event information is supported for all versions of Aurora PostgreSQL\.

## Likely causes of increased waits<a name="apg-waits.timeoutpgsleep.causes"></a>

This wait event occurs when an application, stored function, or user issues a SQL statement that calls one of the following functions:
+ `pg_sleep`
+ `pg_sleep_for`
+ `pg_sleep_until`

The preceding functions delay execution until the specified number of seconds have elapsed\. For example, `SELECT pg_sleep(1)` pauses for 1 second\. For more information, see [Delaying Execution](https://www.postgresql.org/docs/current/functions-datetime.html#FUNCTIONS-DATETIME-DELAY) in the PostgreSQL documentation\.

## Actions<a name="apg-waits.timeoutpgsleep.actions"></a>

Identify the statement that was running the `pg_sleep` function\. Determine if the use of the function is appropriate\.