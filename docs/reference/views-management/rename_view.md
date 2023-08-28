# rename\_view

The `rename_view` command allows you to change the name of an epsio view. After the change, the view can only be referenced with the new name.

## Syntax

```sql
CALL epsio.rename_view(<old_view_name>, <new_view_name>);
```

| Parameter       | Information                         |
| --------------- | ----------------------------------- |
| old\_view\_name | The name of the view to be changed. |
| new\_view\_name | The new name for the view.          |


## Examples

Renaming a view named `countries_population`:

```postgresql
CALL epsio.rename_view('countries_population', 'old_countries_population');
```

Renaming views in a transaction in order to perform a "hot swap":

```postgresql
begin;
CALL epsio.drop_view('countries_population');
CALL epsio.rename_view('new_countries_population_view', 'countries_population');
commit;
```
