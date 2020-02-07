
# Session endpoints

## Create session

Create a session for a specified user.

Request property | Spec
-----------------|-----
Action                                | POST /sessions
[`SID` header](request.md#sid-header) |
Body model                            | [Request session](models.md#request-session); `expiresIn` optional; either `userId` or `email` must be specified
Scala                                 | `object CreateSession`

The most direct way to specify a user is with the `userId` field. Alternatively, an `email` field can be
provided, which must contain an email address belonging to a user. If the `userId` does not exist,
or no user can be found with the provided `email`, then the request will fail with a 400 status.

The deprecated `expiresIn` field is no longer used by the API, but will still be validated if provided in
the request body. It is therefore recommended to avoid including it.

Status | Response body spec
-------|-------------------
201 | [Response session](models.md#response-session)
400 | `{"error": 400, "message": "Missing field: userId or email"}` <br> At least one of `userId` and `email` must be provided.
404 | `{"error": 404, "message": "User '$userId' not found"}` <br> The `userId` field was specified in the request body with an invalid ID.
403 | `{"error": 403, "message": "Insufficient permissions (must be a partner)"}` <br> The provided `SID` is not a partner key, or does not belong to an authenticator or an admin of an org that the specified user is a member of.
401 | [Invalid credentials](responses.md#invalid-credentials)
400 | [Bad request](responses.md#bad-request)

## Sign in

Request property | Spec
---|---
Action | POST /signin/[`userId`](models.md#id-types)
Scala  | `class SignInById`

## Create partner key

Request property | Spec
---|---
Action | POST /user/[`userId`](models.md#id-types)/partnerkey
Scala  | `object GeneratePartnerKey`

Status | Response body spec
---|---
201 | `"{"key":"generated-partner-key"}`
404 | `{"error":404,"message":"User 'userId' not found"}`

## Delete partner key

Request property | Spec
---|---
Action | DELETE /users/[`userId`](models.md#id-types)/partnerkey
Scala  | `object RemovePartnerKey`

## Sign out

Request property | Spec
---|---
Action | POST /signout
Scala  | `object SignOut`

## Retrieve session information


Request property | Spec
---|---
Action | GET /session
Scala  | `object GetSession`

The way of getting current session information.

Status | Response body spec
-------|-------------------
200    | [Response session information](models.md#response-session-information)
   401 | [Invalid credentials](responses.md#invalid-credentials)