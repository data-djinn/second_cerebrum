[[Python]] [[Data Engineering]] [[cleaning data]]
# Constraints
## Data type constraints
- use `df.info()` & `df.describe()` for quick stats
- use method `df.astype('category')` to convert, check with `df.dtype` attribute
## Data range constraints
How to handle:
- drop data
`movies.drop(movies[movies['avg_rating'] > 5]. index)
- setting custom mins & maxs
`movies.loc[movies['avg_rating'] > 5, 'avg_rating'] = 5` (convert bad data to max)
- treat as missing & impute
- setting custom value depending on business assumptions
## Uniqueness constraints
- 