# list\_views

The `list_views` function returns information about all active epsio views in the system.

## Syntax

```sql
SELECT * FROM epsio.list_views(); 
```

### Output

| Column        | Information                                                                                                |
| ------------- | ---------------------------------------------------------------------------------------------------------- |
| view\_name    | A unique name for the view.                                                                                |
| status        | The current status of the view. See [#view-status](list_views.md#view-status "mention") for more details. |
| message       | An optional message regarding the view. For example: an error message.                                     |
| query         | The full SQL query that was used to create the view.                                                       |
| last\_updated | Datetime of the last status update.                                                                        |

## Details

### View Status

The status of a view could be one of the following:

| Status     | Information                                                                                                                                     |
| ---------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| Running    | The view has been created, and the initial population has been completed. It is now being constantly updated as data changes.                   |
| Populating | The view is currently processing the data that was resident in the tables before the view was created. Thus, it is not yet ready to be queried. |
| Error      | An error has occurred while creating the view or while processing data. More information might be available in the `message` column.            |



## Examples

```sql
SELECT view_name, query FROM epsio.list_views(); 
```

```sql
SELECT * FROM epsio.list_views() WHERE view_name = 'population_sum'; 
```
