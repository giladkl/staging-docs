
In order to define a query which will be handled by Epsio, just call `epsio.create_view` in **your original** database:

```postgresql
SELECT epsio.create_view('revenue_per_genre', 
    'SELECT movies.genre genre, sum(purchases.price) revenue 
     FROM purchases
     LEFT JOIN movies on movies.id = purchases.movie_id 
     GROUP BY movies.genre');
```

From that point on, Epsio will maintain the results of that query, updating it incrementally as changes are made.

## Query Views

In order to retrieve the results, just query your original database (which forwards the query to Epsio using the foreign data wrapper mechanism):

```postgresql
SELECT * FROM revenue_per_genre;
```

Since Epsio views function just like any other PostgreSQL view/table, you can also run more complex queries on top of the views you create:

```postgresql
SELECT genres.name, ROUND(revenue / 100) * 100
FROM revenue_per_genre
JOIN genres ON revenue_per_genre.genre=genres.id
ORDER BY revenue LIMIT 5
```
