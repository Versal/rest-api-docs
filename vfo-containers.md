# Versal For Orgs - VFO Containers

## Create new container (root org)

A root org has no parent org and forms a VFO container.

All containers start with status `TRIAL`.  

Required permissions: partner key

Request property | Spec
---|---
Action | POST /vfo/orgs
Body model | [base org](models.md#base-org)
Scala  | `class VFO_CreateRootOrg`

Note: The names of root orgs must be globally unique. If a root org named `"GAP"` exists, attempting to create another
root org with the same name (or with a name that differs only by lettercase, such as `"gap"`)
will not fail but will result in creating a new root org named `"gap 1"`.

VFO org names cannot duplicate Legacy Orgs org names.

When an org is created, a default config for this org is created in the system with the
following values

Parameter | Value
---|---
isPortalEnabled | False
portalSubdomain | Not set
defaultOrgPortalId | not set
providers | not set
learnerTrackingMethod | org-wide
defaultThemeId | not set
brandImage | not set

Status | Response body spec
---|---
200 | [Response org](models.md#response-org)
400 | `{"error":400,"message":"Legacy Org $NAME already exists"}` <br> If $NAME is used by a Legacy Org
403 | `{"error": 403, "message": "Invalid VFO credentials"}` <br> If `SID` is not a valid VFO session or partner key.


## Get container status

This endpoint retrieves the container's org status

Required permissions: Partner Key

Request property | Spec
---|---
Action | GET /vfo/orgs/[`orgId`](models.md#id-types)/orgstatus
Scala  | `class VFO_GetOrgStatus`

Status | Response body spec
---|---
200 | [Org Status Response Model](models.md#org-status-response-model)
401 | `{"error":401,"message":"Invalid Credentials"}` if not partner key
400 | `{"error":400,"message":"Invalid VFO container specified"}` if not root org

## Update org status

This endpoint updates the container's org status.
Marking a container as 
`EXPIRED` archives all the user sessions associated with that org and blocks creation of new ones.

Required permissions: Partner Key

Request property | Spec
---|---
Action | PATCH /vfo/orgs/[`orgId`](models.md#id-types)/orgstatus
Body model | [Org Status Update Model](models.md#org-status-update-model)
Scala  | `class VFO_UpdateOrgStatus`

Status | Response body spec
---|---
200 | [Org Status Response Model](models.md#org-status-response-model)
401 | `{"error":401,"message":"Invalid Credentials"}` if not partner key
400 | `{"error":400,"message":"Invalid VFO container specified"}` if not root org
400 | `{"error":400,"message":"OTHER MESSAGE"}` if other failure, e.g. bad inputs

## Set SSO Config

This endpoint updates the SSO config. This should be called on root org only.

Setting `ssoDetails` to empty object {} deletes the value. This endpoint cannot be used to set sub-values
below the top level e.g. `ssoDetails / samlIdpUrl`. To set such values, please GET the config, and set the
whole top level item. 

To remove the SSO method, set the SSO Method to `NO_SSO`

Request property | Spec
---|---
Action | PATCH /vfo/containers/[`orgId`](models.md#id-types)/sso_config
Body model | [VFO sso configs](models.md#sso-configs)
Scala  | `class PatchSSOConfig`

Status | Response body spec
---|---
200 | [VFO org configs](models.md#org-configs)
401 | `{"error": 401, "message": "Invalid credentials"}` <br> If not called with a partner key or an org admin credential.
400 | `{"error": 400, "message": "Invalid VFO container specified"}` <br> If called with a sub org .
400 | `{"error": 400, "message": "Invalid org ID specified : '$orgId'"}` <br> If any of the given org ID does not refer to an existing VFO Org.
400 | `{"error": 400, "message": "Invalid config ($reason)` <br> If the set of config parameters is not consistent.

## Retrieve SSO Config

This endpoint retrieves the org config associated with the org. This should be called on root org only.

Request property | Spec
---|---
Action | GET /vfo/containers/[`orgId`](models.md#id-types)/sso_config
Body model | Empty
Scala  | `class GetSSOConfig`

Status | Response body spec
---|---
200 | [VFO org configs](models.md#org-configs)
401 | `{"error": 401, "message": "Invalid credentials"}` <br> If not called with a partner key or an org admin credential.
400 | `{"error": 400, "message": "Invalid VFO container specified"}` <br> If called with a sub org .
400 | `{"error": 400, "message": "Invalid org ID specified : '$orgId'"}` <br> If any of the given org ID does not refer to an existing VFO Org.

## Reset SSO Config

This endpoint resets the org config associated with the org. This should be called on root org only.

Request property | Spec
---|---
Action | DELETE /vfo/containers/[`orgId`](models.md#id-types)/sso_config
Body model | Empty
Scala  | `class ResetSSOConfig`

Status | Response body spec
---|---
200 | [VFO org configs](models.md#org-configs)
401 | `{"error": 401, "message": "Invalid credentials"}` <br> If not called with a partner key or an org admin credential.
400 | `{"error": 400, "message": "Invalid VFO container specified"}` <br> If called with a sub org .
400 | `{"error": 400, "message": "Invalid org ID specified : '$orgId'"}` <br> If any of the given org ID does not refer to an existing VFO Org.

## Upload certificate

This endpoint sets the certificate for the container.

Request body is the Multipart with parts: 

Part | Spec 
---|---
certificate | binary certificate file
certificateType | currently only PEM is supported
 
Request property | Spec
---|---
Action | POST /vfo/containers/[`orgId`](models.md#id-types)/sso_config/SAML/certificates
Body model | Multipart
Scala  | `class UploadCertificate`

Status | Response body spec
---|---
200 | `{}`
401 | `{"error": 401, "message": "Invalid credentials"}` <br> If not called with a partner key or an org admin credential.
400 | `{"error": 400, "message": "Invalid VFO container specified"}` <br> If called with a sub org .
400 | `{"error": 400, "message": "Invalid org ID specified : '$orgId'"}` <br> If any of the given org ID does not refer to an existing VFO Org.
404 | `{"error": 404, "message": "VFO Org 'ORGID' Not Found"} <br> if non-org is set as default portal id

## List certificates

This endpoint list the for the container.
 
Request property | Spec
---|---
Action | GET /vfo/containers/[`orgId`](models.md#id-types)/sso_config/SAML/certificates
Body model | Empty
Scala  | `class ListCertificates`

Status | Response body spec
---|---
200 | Array of [certificates](models.md#certificate).
401 | `{"error": 401, "message": "Invalid credentials"}` <br> If not called with a partner key or an org admin credential.
400 | `{"error": 400, "message": "Invalid VFO container specified"}` <br> If called with a sub org .
400 | `{"error": 400, "message": "Invalid org ID specified : '$orgId'"}` <br> If any of the given org ID does not refer to an existing VFO Org.
404 | `{"error": 404, "message": "VFO Org 'ORGID' Not Found"} <br> if non-org is set as default portal id

## Download certificate

This endpoint retrieves certificate for the container. The result is the file attachment to be downloaded.

Request property | Spec
---|---
Action | GET /vfo/containers/[`orgId`](models.md#id-types)/sso_config/SAML/certificates/[`certId`](models.md#id-types)
Body model | Empty
Scala  | `class DownloadCertificate`

Status | Response body spec
---|---
200 | Attached binary certificate
401 | `{"error": 401, "message": "Invalid credentials"}` <br> If not called with a partner key or an org admin credential.
400 | `{"error": 400, "message": "Invalid VFO container specified"}` <br> If called with a sub org .
400 | `{"error": 400, "message": "Invalid org ID specified : '$orgId'"}` <br> If any of the given org ID does not refer to an existing VFO Org.
404 | `{"error": 404, "message": "Certificate 'CERTID' not found"} <br> if certificate is not present

## Delete certificate

This endpoint deletes certificate for the container.

Request property | Spec
---|---
Action | DELETE /vfo/containers/[`orgId`](models.md#id-types)/sso_config/SAML/certificates/[`certId`](models.md#id-types)
Body model | Empty
Scala  | `class DeleteCertificate`

Status | Response body spec
---|---
200 | `{}`
401 | `{"error": 401, "message": "Invalid credentials"}` <br> If not called with a partner key or an org admin credential.
400 | `{"error": 400, "message": "Invalid VFO container specified"}` <br> If called with a sub org .
400 | `{"error": 400, "message": "Invalid org ID specified : '$orgId'"}` <br> If any of the given org ID does not refer to an existing VFO Org.
404 | `{"error": 404, "message": "Certificate 'CERTID' not found"} <br> if certificate is not present

## Set Org Config

This endpoint updates the org config. This should be called on root org only.

If the org portal is being enabled for the first time for this container then a random domain name 
(`CustomerABCDEF` where `ABCDEF` is a 6 alphanumeric string) will be assigned. This cannot be changed by 
the org admin. To update the org portal a different [endpoint](vfo-portals.md#Update-Portal-Subdomain-name-for-a-VFO-Container)
must be used. 

Request property | Spec
---|---
Action | PATCH /vfo/orgs/[`orgId`](models.md#id-types)/config
Body model | [VFO org configs](models.md#org-configs)
Scala  | `class VFO_SetConfig`

Status | Response body spec
---|---
200 | [VFO org configs](models.md#org-configs)
401 | `{"error": 401, "message": "Invalid credentials"}` <br> If not called with a partner key or an org admin credential.
400 | `{"error": 400, "message": "Invalid VFO container specified"}` <br> If called with a sub org .
400 | `{"error": 400, "message": "Invalid org ID specified : '$orgId'"}` <br> If any of the given org ID does not refer to an existing VFO Org.
400 | `{"error": 400, "message": "Invalid config ($reason)` <br> If the set of config parameters is not consistent.
404 | `{"error": 404, "message": "VFO Org 'ORGID' Not Found"} <br> if non-org is set as default portal id
400 | `{"error": 400, "message": "Cannot set an org portal from a different container as a default org portal id"} <br> if an org from a different container is used 

## Retrieve Org Config

This endpoint retrieves the org config associated with the org. This should be called on root org only.

Request property | Spec
---|---
Action | GET /vfo/orgs/[`orgId`](models.md#id-types)/config
Body model | Empty
Scala  | `class VFO_GetConfig`

Status | Response body spec
---|---
200 | [VFO org configs](models.md#org-configs)
401 | `{"error": 401, "message": "Invalid credentials"}` <br> If not called with a partner key or an org admin credential.
400 | `{"error": 400, "message": "Invalid VFO container specified"}` <br> If called with a sub org .
400 | `{"error": 400, "message": "Invalid org ID specified : '$orgId'"}` <br> If any of the given org ID does not refer to an existing VFO Org.

## Reset Org Config

This endpoint resets the org config associated with the org to default values. This should be called on root org only.

Request property | Spec
---|---
Action | DELETE /vfo/orgs/[`orgId`](models.md#id-types)/config
Body model | Empty
Scala  | `class VFO_ResetConfig`

Status | Response body spec
---|---
200 | `{}`
401 | `{"error": 401, "message": "Invalid credentials"}` <br> If not called with a partner key or an org admin credential.
400 | `{"error": 400, "message": "Invalid VFO container specified"}` <br> If called with a sub org .
400 | `{"error": 400, "message": "Invalid org ID specified : '$orgId'"}` <br> If any of the given org ID does not refer to an existing VFO Org.

## Reset Org Default Theme

This endpoint resets the org default theme to none. This should be called on root org only.

Request property | Spec
---|---
Action | DELETE /vfo/orgs/[`orgId`](models.md#id-types)/config/defaultThemeId
Body model | Empty
Scala  | `class VFO_ResetDefaultTheme`

Status | Response body spec
---|---
200 | `{}`
401 | `{"error": 401, "message": "Invalid credentials"}` <br> If not called with a partner key or an org admin credential.
400 | `{"error": 400, "message": "Invalid VFO container specified"}` <br> If called with a sub org .
400 | `{"error": 400, "message": "Invalid org ID specified : '$orgId'"}` <br> If any of the given org ID does not refer to an existing VFO Org.

## Replace default course brand image

The user must be a signed in user and have `AdministerOrg` permission on the org. 

Request property | Spec
---|---
Action | POST /vfo/orgs/[`orgId`](models.md#id-types)/config/coursebrandimage
Scala  | `class VFO_ReplaceCourseBrandImage`

## Delete default course brand image

The user must be a signed in user and have `AdministerOrg` permission on the org or use Partner Key.

Request property | Spec
---|---
Action | DELETE /vfo/orgs/[`orgId`](models.md#id-types)/config/coursebrandimage
Scala  | `class VFO_DeleteCourseBrandImage`

## Container reports

In order to facilitate the front end, a GET method is needed to download reports.
A download token that will expire in a configured time (60 minutes recommended) is sent back.
To get the report, before the token expires the caller has to call `GET http(s)://URL_BASE/api2/reports/downloads?token=token-value` with no SID.
The caller can check if report is ready to download by calling `GET http(s)://URL_BASE/api2/reports/downloadstatus?token=token-value`

## All Courses Learners report

This endpoint is to start generating all courses learners report

Request property | Spec
---|---
Action | POST /vfo/containers/[`orgId`]/reports/all_courses_learners
Scala | `class CreateAllCoursesLearnersReport`

Status | Response body spec
---|---
200 | A download token value in the form `{"downloadToken":"token-value"}`
400 | `{"error":400,"message":"Invalid VFO container specified"}` if `orgId` is not a valid container
401 | [Invalid credentials](responses.md#invalid-credentials)

## Courses completion report

This endpoint is to start generating courses completion report

Request property | Spec
---|---
Action | POST /vfo/containers/[`orgId`]/reports/course_completions
Body model | [Course completion report](models.md#course-completion-report)
Scala | `class CreateCourseCompletionReport`

Status | Response body spec
---|---
200 | A download token value in the form `{"downloadToken":"token-value"}`
400 | `{"error":400,"message":"Invalid VFO container specified"}` if `orgId` is not a valid container
401 | [Invalid credentials](responses.md#invalid-credentials)


