# Sorting

Sorting results is a snap. Like the “Indexable” behavior above, each model derived from KModelDatabase gets a sortable behavior added to it. That means with no effort, two state variables get added to your models:

The `sort` variable gives us the column by which to sort our results.
The `direction` is either the familiar `asc` for ascending (which is the default) and `desc` for descending. In combination, they get used in the Model to build the `order by` part of your query. A request in our example with `&sort=category_id&direction=desc` will produce a query against database that has the following:

    ORDER BY tbl.category_id DESC

