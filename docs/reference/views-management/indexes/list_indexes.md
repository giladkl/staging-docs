# list\_indexes

The `list_indexes` function returns information about all active epsio views in the system.

## Syntax

```sql
SELECT * FROM epsio.list_indexes(<view_name>); 
```

| Parameter  | Information           |
| ---------- | --------------------- |
| view\_name | The name of the view. |

## Output

| Column           | Information                       |
| ---------------- | --------------------------------- |
| indexed\_columns | The columns that make each index. |



## Examples

Querying the indexes of the view `countries_population`:

```sql
SELECT * FROM epsio.list_indexes('countries_population'); 
```