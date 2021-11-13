# ipc:damrecordtxack<a name="apg-waits.ipcdamrecordtxac"></a>

The `ipc:damrecordtxack` event occurs when Aurora PostgreSQL in a session using database activity streams generates an activity stream event, then waits for that event to become durable\. 

**Topics**
+ [Relevant engine versions](#apg-waits.ipcdamrecordtxac.context.supported)
+ [Context](#apg-waits.ipcdamrecordtxac.context)
+ [Causes](#apg-waits.ipcdamrecordtxac.causes)
+ [Actions](#apg-waits.ipcdamrecordtxac.actions)

## Relevant engine versions<a name="apg-waits.ipcdamrecordtxac.context.supported"></a>

This wait event information is relevant for all Aurora PostgreSQL 10\.7 and higher 10 versions, 11\.4 and higher 11 versions, and all 12 and 13 versions\.

## Context<a name="apg-waits.ipcdamrecordtxac.context"></a>

In synchronous mode, durability of activity stream events is favored over database performance\. While waiting for a durable write of the event, the session blocks other database activity, causing the `ipc:damrecordtxack` wait event\.

## Causes<a name="apg-waits.ipcdamrecordtxac.causes"></a>

The most common cause for the `ipc:damrecordtxack` event to appear in top waits is that the Database Activity Streams \(DAS\) feature is a holistic audit\. Higher SQL activity generates activity stream events that need to be recorded\.

## Actions<a name="apg-waits.ipcdamrecordtxac.actions"></a>

We recommend different actions depending on the causes of your wait event:
+ Reduce the number of SQL statements or turn off database activity streams\. Doing this reduces the number of events that require durable writes\.
+ Change to asynchronous mode\. Doing this helps to reduce contention on the `ipc:damrecordtxack` wait event\.

  However, the DAS feature can't guarantee the durability of every event in asynchronous mode\.