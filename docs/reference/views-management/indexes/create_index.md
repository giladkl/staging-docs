# create\_index

The `create_index`  command creates a new index on a given view for the given columns.
Currently, only B-tree indexes are supported.

## Syntax

```sql
CALL epsio.create_index(<view_name>, <indexed_fields>);
```

| Parameter       | Information                                                            |
| --------------- |------------------------------------------------------------------------|
| view\_name      | The name of the view                                                   |
| indexed\_fields | A comma separated list of columns for which the index will be created. |

## Examples

Creating an index on the view `countries_population` for the columns `country_name`:

```sql
CALL epsio.create_index('countries_population', 'country_name');
```

Creating an index on the view `countries_population` for the columns `id, population`:

```sql
CALL epsio.create_index('countries_population', 'id, population');
```