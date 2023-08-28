# FAQ
??? question "What kind of databases does Epsio work with?"
      Epsio’s first version works with PostgreSQL. MySQL, SQL Server are in private alpha.

??? question "Can I use Epsio for managed cloud databases?"
    Yes, Epsio can be used with managed cloud database deployments such as Amazon RDS and Aurora, Google Cloud SQL, etc...
    
??? question "How does Epsio compare with caching solutions or materialized views?"
    Unlike caching solutions or materialized views, Epsio never recalculates the entire dataset. It only performs the minimum calculations needed to update the result, which leads to massively reduced query times, significant cost savings, and a highly scalable architecture.

    Epsio is suitable for complex queries containing `JOIN`, `GROUP BY`, subqueries, and more.

??? question "How does Epsio compare with pg_ivm?"
    Both Epsio and pg_ivm allow you to incrementally maintain materialized views in a PostgreSQL deployment. However, they have a few key differences:

    |  Features | pg_ivm  | Epsio  |
    | ------------ | ------------ | ------------ |
    | Stability  |  Not recommended for production deployments. |  Production Ready. |
    |  Operator support  |  Limited support, missing support for `OUTER` `JOIN`s, `ORDER BY`, and more.   | Supports most commonly used PostgreSQL operators. |
    | Supported PostgreSQL Workloads |  Can only be used in self-managed deployments of PostgreSQL (requires installation of a PostgreSQL extension). | Supports both managed and self-managed deployments of PostgreSQL. |
    |  Consistency Level  |   Consistent, blocking writes until the MV update is complete. | Eventual Consistent, never effecting write performance. |

??? question "How does Epsio compare with Materialize?"
    Epsio and Materialize both incrementally maintain results of SQL queries. However, they are designed with different primary use-cases and architectural considerations in mind.

    |   | Materialize  | Epsio  |
    | ------------ | ------------ | ------------ |
    |  Primary Use Case  | Replace Stream Processesors with SQL. | Provide instant & up to date results for heavy SQL queries. |
    |  Query Methodology  |  Requires to query Materialize to fetch query results. |  Writes back results to your PostgreSQL / uses FDWs, so there is no need to query another endpoint. |
    |  Tradeoffs  |  Higher "freshness" of data,<br>Higher infrastructure costs. | Lower "freshness" of data,<br>Lower infrastructure costs. |
    |  Deployment Method  |  External to your cloud account, data needs to be forwarded to Materialize. | Deployed within your account, no sensitive data leaves your environment. |
        |  Operator support  |  Supports most commonly used PostgreSQL operators.   | Supports most commonly used PostgreSQL operators. |

??? question "How does Epsio compare with Timescale's Continues Aggregates?"
    Both Epsio and Timescale's Continues Aggregates allow you to incrementally maintain materialized views in a PostgreSQL deployment. However, they have some key differences:
    
    |   | Continues Aggregates  | Epsio  |
    | ------------ | ------------ | ------------ |
    |  Syntax Support  |  Supports only time-series data (supports only queries on Hypertables). Doesn't incrementally maintain `JOIN`s.  |  Supports any PostgreSQL table and data, and all types of `JOIN`s.|
    |  Deployment  |  Requires the Timescale extension to be installed. | Doesn't require any extension installed, can be used with a managed PostgreSQL instance |

??? question "How does Epsio compare with ReadySet?"
     |   | ReadySet  | Epsio  |
    | ------------ | ------------ | ------------ |
    |  Query Results Maintained  |  "Partially" maintains query results based on usage/parameters (similar to a cache). As a result, the 99th percentile of response time may still remain very high. |  Constantly maintains the complete results of the defined queries, ensuring that query response times are always low. |
    |  Deployment  |  Acts as a proxy. If it crashes, it breaks the entire access to the production database. | Sits "behind" the database, does not affect non-Epsio related database queries. |
    |  Stability  |  Not recommended for production deployments.  | Production ready. |

??? question "Is my data secure?"
    We take your data security very seriously.

    The Epsio engine can be deployed in your cloud account, in such a way that your sensitive data never leaves your cloud environment and is not accessible to Epsio cloud.

    Additionally, all communication between Epsio and the original database is encrypted and secured using industry-standard security practices.
    
??? question "Is Epsio open source?"
    No, Epsio is not open-source. However, we offer a free trial for interested users to test out our product before committing.