# Versal For Orgs - Sessions and Authentication

## Create VFO session

Creates a new session within a VFO org container (this is a "VFO session").
VFO sessions are required for most VFO endpoints, and are valid for an entire VFO org container rather than for a specific VFO org within a container.

Required permissions: any valid user session (either VFO or non-VFO), or a partner key.
The new session can be requested only for a user who already has some member privileges within the org container of `orgId`.

If a user session is given, the user id is derived from that session.
If a partner key is given, a user id must be specified in the request body.

The org id given in the request URL should be a root org.
(At the moment, a non-root org id is permitted, but the result will be the same as if the corresponding root org were given.)

Request property | Spec
---|---
Action | POST /vfo/orgs/[`orgId`](models.md#id-types)/sessions
Body model | [VFO session request](models.md#request-vfo-session)
Scala  | `class VFO_CreateOrgSession`

The newly created session is connected with the entire "org container", that is, the entire org tree starting from the given root org.

The newly created VFO session can be used both with VFO and non-VFO endpoints.

If the expiration interval is not specified, by default sessions expire in 24 hours of inactivity.
The maximum permitted expiration interval is 60 days of inactivity.
"Inactivity" means not using the VFO session id in headers of some rest-api endpoint request (whether a VFO or non-VFO endpoint).

Status | Response body spec
---|---
200 | [Response VFO session](models.md#response-vfo-session)
400 | `{"error": 400, "message": "Field must have type number: expiresIn"}` <br> If `expiresIn` is not a non-negative integer.
403 | `{"error": 403, "message": "Invalid VFO credentials"}` <br> If `SID` is not a valid VFO session or partner key.
500 | `{"error": 500, "message": "Malformed Org Tree"}` <br> If the given org belongs to a cyclic hierarchy, which is invalid.

## Create Single Sign-On (SSO) Authorization Request

This endpoint creates an SSO authorization request based on the Org's configuration. Currently, only SAML 2.0 
is supported for SSO.

Request property | Spec
---|---
Action | POST /vfo/orgs/[`orgId`](models.md#id-types)/sso/authrequest
Body model | [VFO single sign-on (SSO) authorization request input model](models.md#vfo-single-sign-on-sso-authorization-request-input-model)
Scala  | `VFO_CreateSSOAuthRequest`

Status | Response body spec
---|---
200 | [VFO single sign-on (SSO) authorization request response model](models.md#vfo-single-sign-on-sso-authorization-request-response-model)
401 | `{"error": 401, "message": "Invalid credentials"}` <br> If not called with a partner key or `frontend-sso` integration key.
400 | `{"error": 400, "message": "Invalid VFO container specified"}` <br> If called with a sub org .
400 | `{"error": 400, "message": "Invalid org ID specified : '$orgId'"}` <br> If any of the given org ID does not refer to an existing VFO Org.
400 | `{"error": 400, "message": "SAML SSO is not enabled""}` <br> If SAML is not enabled for the Org
500 | `{"error": 500, "message": Varies}` <br> If database error, error communicating with SSO Backend, or other unhandled exception

Error messages from the SSO Backend service will also be passed through to this endpoint. See [SSO Backend: Create SAML Authorization Response](https://github.com/Versal/sso-backend/blob/master/docs/APISpecification.md#create-saml-authorization-request)


## Create Single Sign-On (SSO) Session

This endpoint creates a VFO user session based on the response from an SSO-enabled Org's identify provider.
Currently, only SAML 2.0 is supported for SSO.

Request property | Spec
---|---
Action | POST /vfo/orgs/:orgId/sso/sessions
Body model | [VFO single sign-on (SSO) session input model](models.md#vfo-single-sign-on-sso-session-request-input-model)
Scala  | `VFO_CreateSSOSession`

Status | Response body spec
---|---
200 | [VFO user session](models.md#response-vfo-session)
401 | `{"error": 401, "message": "Invalid credentials"}` <br> If not called with a partner key or `frontend-sso` integration key.
400 | `{"error": 400, "message": "Invalid VFO container specified"}` <br> If called with a sub org .
400 | `{"error": 400, "message": "Invalid org ID specified : '$orgId'"}` <br> If any of the given org ID does not refer to an existing VFO Org.
400 | `{"error": 400, "message": "SAML SSO is not enabled"}` <br> If SAML is not enabled for the Org
400 | `{"error": 400, "message": "SSO method $method is not supported"}` <br> If the site does not support the requested SSO method
400 | `{"error": 400, "message": "User is not in org"}` <br> If the user in the SSO request is not part of the VFO container identified in the original authorization request
400 | `{"error": 400, "message": ""Response was not signed with provider's certificate""}` <br> if the authorization response is not signed by given provider
404 | `{"error": 404, "message": "User 'userKey' not found"}` <br> If a user record with the user identifier passed in by the SSO request can not be located
500 | `{"error": 500, "message": Varies}` <br> If database error, error communicating with SSO Backend, or other unhandled exception

Error messages from the SSO Backend service will also be passed through to this endpoint. See [SSO Backend: Unpack SAML IDP Response](https://github.com/Versal/sso-backend/blob/master/docs/APISpecification.md#unpack-saml-idp-response)

## Create VFO API Key

This endpoint creates a VFO API access key, which can be used to access the public facing course statistics api (https://github.com/Versal/course-statistics-api) for the org. These are created for root orgs only. 

Request property | Spec
---|---
Action | POST /vfo/orgs/[`orgId`](models.md#id-types)/keys
Body model | Empty
Scala  | `class VFO_CreateVFOAuthenticator`

Status | Response body spec
---|---
201 | {"key": "API_ACCESS_KEY"}
401 | `{"error": 401, "message": "Invalid credentials"}` <br> If not called with a partner key.
400 | `{"error": 400, "message": "Invalid VFO container specified"}` <br> If called with a sub org .
400 | `{"error": 400, "message": "Invalid org ID specified : '$orgId'"}` <br> If any of the given org ID does not refer to an existing VFO Org.

## Revoke VFO API Key

This endpoint revokes an existing VFO API access key.

Request property | Spec
---|---
Action | DELETE /vfo/orgs/[`orgId`](models.md#id-types)/keys/[`key`](models.md#id-types)
Body model | Empty
Scala  | `class VFO_RemoveVFOAuthenticator`

Status | Response body spec
---|---
204 | `""`
401 | `{"error": 401, "message": "Invalid credentials"}` <br> If not called with a partner key.
400 | `{"error": 400, "message": "Invalid VFO container specified"}` <br> If called with a sub org .
400 | `{"error": 400, "message": "Invalid org ID specified : '$orgId'"}` <br> If any of the given org ID does not refer to an existing VFO Org.
404 | `{"error": 404, "message": "Unknown auth key"}` <br> If the key does not exist for the given org ID.

