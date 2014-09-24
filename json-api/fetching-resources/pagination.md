# Pagination on Resource Collections

In keeping with good API design, additional pagination information is provided. The limit, offset, & total are sent along with the response document as part of the meta property in the top level object.

    "offset": 0,
    "limit": 2,
    "total": 7,

The **limit** variable is included as a state variable by default in the model objects. It tells the query building part of the model what the LIMIT on the query should be.
The **offset** is an integer multiple of the limit and represents the first record in that corresponding page. In our example, if the limit is 2 and we want to see the third page of results our offset would be **page - 1** multiplied by the **limit** which equals 4, then the offset should be 4.

The **total** is a count of all the records available in the data that fit the current request vars without the limit applied to them.

