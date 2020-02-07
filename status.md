
# Status endpoints

## Get version

Retrieves version information.

Request property | Spec
---|---
Action                                | GET /version
[`SID` header](request.md#sid-header) | Not required.
Body model                            | _no body_
Scala                                 | `object GetVersion`

Use this endpoint to check if the API is up.

Status | Response body spec
---|---
200 | [Version](models.md#version)

## Get healthcheck

Retrieves the operational status.

Request property | Spec
---|---
Action                                | GET /healthcheck
[`SID` header](request.md#sid-header) | Not required.
Body model                            | _no body_
Scala                                 | `object HealthCheck`

Use this endpoint to check if the API is up and if not, what parts are not right.

Status | Response body spec
---|---
200 | [Healthcheck summary](models.md#healthcheck-summary)


## Get features

Retrieves the current feature flag settings.

Request property | Spec
---|---
Action                                | GET /features
[`SID` header](request.md#sid-header) | Must be a partner key.
Body model                            | _no body_
Scala                                 | `object GetFeatures`

Status | Response body spec
---|---
200 | [Features](models.md#features)
401 | [Invalid credentials](responses.md#invalid-credentials); if the `SID` header is not a partner key

## Update feature

Updates the specified feature flag settings.

Request property | Spec
---|---
Action                                | POST /features
[`SID` header](request.md#sid-header) | Must be a partner key.
Body model                            | [Features](models.md#features);
Scala                                 | `object UpdateFeatures`

Status | Response body spec
---|---
200 | _no body_
401 | [Invalid credentials](responses.md#invalid-credentials); if the `SID` header is not a partner key
400 | [Bad request](responses.md#bad-request)
