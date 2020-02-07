
# Internal integrations endpoints

For non-enduser-initiated endpoints like the learner activity api looking up user permissions,
getting a course outline, etc. from the course api, use a Course API Key.
This will prevent unnecessary computations and also shore up security in endpoints like the get course outline,
for which permissions computations are impossible,
due to the user possibly not having a current role in the course.

Generally integrations configuration is the matter of appropriate DB migration,
that will mark the valid endpoint names as accessible for the specific integration.
The Integration API only provides the endpoints for creating and deleting the integration keys.

## Generate integration key

Generates the new integration key for the specific integration.
It doesn't store this generated key in the clear format. Only the hashed version is stored.

Request property | Spec
---|---
Action                                | POST /integrations/[`integration_name`]/keys
[`SID` header](request.md#sid-header) | Must be a partner key.
Body model                            | _no body_
Scala                                 | `class GenerateIntegrationKey`

Status | Response body spec
---|---
201 |
404 | `{"error": 404, "message": "Integration '$integration_name' not found"}` <br> If `integration_name` does not identify a valid integration.
401 | [Invalid credentials](responses.md#invalid-credentials), if `SID` is not a partner key.
400 | [Bad request](responses.md#bad-request)

## Remove integration key

Removes the specified integration key from the integration.

Request property | Spec
---|---
Action     | DELETE /integrations/[`integration_name`]/keys/[`key`]
Body model | _no body_
Scala      | `class RemoveIntegrationKey`

Status | Response body spec
---|---
204 | If integration key was successfully removed
404 | `{"error": 404, "message": "Integration '$integration_name' not found"}` <br> If `integration_name` does not identify a valid integration.
404 | `{"error": 404, "message": "Key '$key' not found"}` <br> If the `key` does not identify a valid key
