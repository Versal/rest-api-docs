
# Pagination

Most of the endpoints that return arrays of objects are paginated, to avoid generating
responses that are too large. This reduces load on both the client and server.

To change the behavior of the pagination, clients can specify configuration values as
request parameters. Pagination metadata is sent back in the response headers. Using
these mechanisms, clients can traverse a large collection of results a page at a time.

## Request parameters

Parameter | Type | Default | Description
:---|:---|---:|:---
`page`    | [integer](#integer) |  1 | The page number. Pages are numbered consecutively starting at one.
`perPage` | [integer](#integer) | 20 | The number of objects per page. Must be between 1 and 100, inclusive.

## Response header

The `X-Pagination` header provides pagination metadata in responses. Its value is a JSON object
with the following fields:

Field | Type | Description
---|---|---
`count`     | [integer](#integer) | The total number of items available in the requested collection.
`page`      | [integer](#integer) | The requested page number.
`pageCount` | [integer](#integer) | The number of pages available with the current `perPage` value.
`perPage`   | [integer](#integer) | The requested number of items per page.
