
# Common request properties

## `SID` header

In most cases, the `SID` header must be provided, with its value being a partner key,
authenticator session ID, or user session ID. Exceptions to this will be stated explicitly
in the documentation for those endpoints having different requirements.

## `Range` header

Optional header. Consult [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.35) for more information.
Support for this header was added to enable seeking in web videos.
