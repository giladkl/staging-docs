# create\_view

The `create_view` command creates a new view that will be incrementally maintained by epsio. Much like [Postgres' materialized views](https://www.postgresql.org/docs/current/rules-materializedviews.html), the results of the given query will be pre-calculated and materialized into a table. However, with epsio - when the underlying data changes, epsio updates the query results accordingly.

## Syntax

```sql
CALL epsio.create_view(<view_name>, <query>);
```

| Parameter  | Information                 |
| ---------- | --------------------------- |
| view\_name | A unique name for the view. |
| query      | Full SQL query.             |

## Details

After creating a new view, epsio processes the data that already exists in the relevant tables. This phase is known as the `population` phase and can take time depending on the size of your dataset. When the population is over, the view's status will be changed to `running` and the view will be ready to be queried and process changes in realtime.


When creating a view on tables that you haven't created views on before, you'll need to add those tables to the publication that was created during deployment and set a replica identity for the table:  
`#!postgresql ALTER TABLE <table_name> REPLICA IDENTITY FULL;`  
`#!postgresql ALTER PUBLICATION epsio_publication ADD TABLE <table_name>;`

##  Examples

Creating a view:

```sql
CALL epsio.create_view('countries_population',
    'SELECT country.name, sum(city.population) AS total_population
    FROM playground.country
    JOIN playground.city ON country.code = city.country_code
    group by country.name');
```

Querying from the newly created view:

```sql
SELECT * FROM countries_population;
```
