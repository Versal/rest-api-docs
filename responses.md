
# Common responses

## Successful

### File content

If the `Range` header was not specified in the request, then the status code is 200 and the
response body is the byte content of the entire file. The following headers are included:
- `Content-Type` - The [Internet media type](http://en.wikipedia.org/wiki/Internet_media_type) of the file.</li>
- `Content-Length` - The number of bytes in the response body.</li>
- `Accept-ranges: bytes` - The `Range` header format that this endpoint accepts.</li>
- `Cache-Control: max-age=$cacheTimeout, s-maxage=$cacheTimeout, public` - The `cacheTimeout` is set in the API config file.

If the `Range` header _is_ specified, then the status code is 206 and the response body is the byte content of the ranges
specified in the header. In addition to the headers from the non-`Range` response, the following header is also provided:
- `Content-Range` - The ranges of content in the response body.

## Failed

### Invalid credentials

`{"error": 401, "message": "Invalid credentials"}`

The `SID` header was not provided, was not a partner key, or was not a valid session ID for a user or authenticator.

### Bad request

`{"error": 400, "message": "..."}` (messages are varied)

Parsing or validating the request parameters or body failed. Make sure you are sending the request as specified by the endpoint documentation.

### Invalid pagination parameters

`{"error": 400, "message": "Param number expected"}`

At least one of the pagination request parameters `page` and `perPage` could not be parsed as integer values.

### Course is not staged

`{"error": 403, "message": "Course is not staged"}`

Your request (such as "Get user progress") makes sense only for a course that has at least one published revision, but this course has none.

