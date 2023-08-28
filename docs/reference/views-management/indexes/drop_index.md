# drop\_index

The `drop_index` command drops an existing index on a given view.



## Syntax

```sql
CALL epsio.drop_index(<view_name>, <indexed_fields>);
```

| Parameter       | Information                                             |
| --------------- | ------------------------------------------------------- |
| view\_name      | The name of the view for which the index will dropped.. |
| indexed\_fields | A comma separated list of the index's columns.          |

## Examples

Dropping the index on the view `countries_population` for the columns `country_name`:

```sql
CALL epsio.drop_index('countries_population', 'country_name');
```

Dropping the index on the view `countries_population` for the columns `id, population`:

```sql
CALL epsio.drop_index('countries_population', 'id, population');
```