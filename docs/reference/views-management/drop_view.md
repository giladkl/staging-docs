# drop\_view

The `drop_view` command stops the continuous maintenance of the epsio view and drops the foreign table.

## Syntax

```sql
CALL epsio.drop_view(<view_name>);
```

| Parameter  | Information                                |
| ---------- | ------------------------------------------ |
| view\_name | The name of the view wished to be dropped. |

## Examples

Dropping a view:

```sql
CALL epsio.drop_view('countries_population');
```
