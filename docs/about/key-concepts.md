## Incremental Materialized Views
Epsio's core technology is based on incremental materialized views.  
In traditional databases, materialized views are a way to store the result of a query as a table, which can then be queried more quickly in the future. However, these materialized views become stale as soon as the underlying data changes, requiring a full recalculation of the view. Incremental materialized views solve this problem by updating only the affected rows in the materialized view, rather than recalculating the entire view. This makes it possible to maintain fast query response times even as the underlying data changes, making Epsio a powerful tool for building high-performance applications.

## Foreign Data Wrappers
Foreign Data Wrappers (FDWs) are a powerful feature of PostgreSQL that enable you to access and manipulate data stored in external systems as if it were in a local PostgreSQL table. This feature makes use of the SQL-MED ("Management of External Data") standard, which allows the creation of tables that act as proxies for data stored in other databases, files, or services.

Epsio uses the FDW mechanism to seamlessly integrate with your existing PostgreSQL database. For every incremental view you create, a foreign table is also created in your database, the foreign table points to Epsio and allows you to query the same database and always get up-to-date results.

## Eventual Consistency
!!! quote "Eventual Consistency"
    _Eventual consistency is a consistency model used in distributed computing to achieve high availability that informally guarantees that, if no new updates are made to a given data item, eventually all accesses to that item will return the last updated value._ [Wikipedia](https://en.wikipedia.org/wiki/Eventual_consistency)

When underlying data is changed in a query defined in Epsio, the change might not be immediately reflected in Epsio. However, the incremental update process ensures that Epsio will eventually be consistent with the data. This might result in brief periods (usually only a few milliseconds) where the view lags slightly behind the latest state of the data.
