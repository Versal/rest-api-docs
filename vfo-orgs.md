# Versal For Orgs - Orgs and Org Trees

The database cannot guarantee the consistency of the tree structure (the absence of cycles, where an org is a descendant of itself).
For this reason, endpoints that query the org hierarchy will perform a runtime check (`VFORecursionAware.checkForInfiniteRecursion`).
If a cyclic structure is found (an org that is a descendant of itself), the error will be logged.

## Get org

Required permissions: VFO session of a user who belongs to the same org container, or partner key

Request property | Spec
---|---
Action | GET /vfo/orgs/[`orgId`](models.md#id-types)
Scala  | `class VFO_GetOrg`

Status | Response body spec
---|---
200 | [Response org](models.md#response-org)
403 | `{"error": 403, "message": "Invalid VFO credentials"}` <br> If `SID` is not a valid VFO session or partner key.

## Get org tree

Required permissions: VFO session of a user who belongs to the same org container, or partner key

Request property | Spec
---|---
Action | GET /vfo/orgs/[`orgId`](models.md#id-types)/orgs
Scala  | `class VFO_GetOrgTree`

Status | Response body spec
---|---
200 | [Org tree](models.md#org-tree)
403 | `{"error": 403, "message": "Invalid VFO credentials"}` <br> If `SID` is not a valid VFO session or partner key.
500 | `{"error": 500, "message": "Malformed Org Tree"}` <br> If the given org belongs to a cyclic hierarchy, which is invalid.

## Search orgs

Required permissions: partner key

Request property | Spec
---|---
Action | GET /vfo/orgs
[`SID` header](request.md#sid-header) |
[Pagination params](pagination.md#request-parameters) |
`isRoot` param | Optional boolean. Show only orgs that `isRoot` value matches the parameter value.   
`name` param | Optional string. Show only orgs that `orgName` value matches the parameter value.   
`orgId` param | Optional numeric. Show only orgs that `orgId` value matches the parameter value.   
Body model | _no body_
Scala  | `class VFO_SearchOrgs`

Status | Response body spec
---|---
200 | [paginated](pagination.md) array of [Response org](models.md#response-org)
401 | [Invalid credentials](responses.md#invalid-credentials)
403 | `{"error": 403, "message": "Insufficient permissions"}` <br> If session is not partner key.


## Create child org

A child org is a sub-org of an already existing parent org.

Required permissions: VFO session for a user with admin privileges (permission `AdministerOrg`) in the parent org, or partner key.

Request property | Spec
---|---
Action | POST /vfo/orgs/[`orgId`](models.md#id-types)/orgs
Body model | [base org](models.md#base-org)
Scala  | `class VFO_CreateSubOrg`

The suborg will be created under the parent org with given id `orgId`.

Note: The names of sibling sub-orgs must be unique. If a sibling sub-org named `"GAP Germany"` exists, attempting to create another
sibling sub-org with the same name (or with a name that differs only by lettercase, such as `"gap germany"`) under the same parent org
will not fail but will result in creating a new sibling org named `"gap germany 1"`.

VFO org names cannot duplicate Legacy Orgs org names.

Status | Response body spec
---|---
200 | [Response org](models.md#response-org)
400 | `{"error":400,"message":"Legacy Org $NAME already exists"}` <br> If $NAME is used by a Legacy Org
403 | `{"error": 403, "message": "Invalid VFO credentials"}` <br> If `SID` is not a valid VFO session or partner key.

## Reorder suborgs

This endpoint updates suborgs order for given org.

Required permissions: org admin (permission `AdministerOrg`) in the given org (or parent), or partner key.

Request property | Spec
---|---
Action | PUT /vfo/orgs/[`orgId`](models.md#id-types)/orgs/order
Body model | A JSON list of [org IDs](models.md#id-types) as strings.
Scala  | `class VFO_ReorderSuborgs`

Status | Response body spec
---|---
200 | `{}`
401 | `{"error":401,"message":"Invalid Credentials"}` if not partner key
403 | `{"error":403,"message":"Invalid VFO credentials"}` <br> If `SID` is not a valid VFO session or partner key.
400 | `{"error":400,"message":"all suborgs must be specified"}` if not all suborgs were specified

## Patch org info

Required permissions: org admin in the org being patched, or partner key.

Request property | Spec
---|---
Action | PATCH /vfo/orgs/[`orgId`](models.md#id-types)
Body model | [VFO org info request](models.md#vfo-org-info-request)
Scala  | `class VFO_PatchOrg`

All fields in the request body are optional.
The request will replace the org metadata with those fields that are given in the request body. Other fields will remain unchanged.
The address will be replaced with the given data, so it must be either given in full or omitted entirely from the request body.

Status | Response body spec
---|---
200 | Array of [Response org](models.md#response-org)
403 | `{"error": 403, "message": "Invalid VFO credentials"}` <br> If `SID` is not a valid VFO session or partner key.
500 | `{"error": 500, "message": "Malformed Org Tree"}` <br> If the given org belongs to a cyclic hierarchy, which is invalid.

## Delete org

Required permissions: org admin in a parent org of the org being deleted, or partner key.

Orgs that don't have any sub-orgs can be deleted along with all associated users and courses.
Other non-root orgs can be deleted only if all of the orgs down the hierarchy don't have any users or courses associated.
Root orgs additionally to the above cannot be referred in user container map nor in course container map. In practice this is possible only if no users or courses have ever been added anywhere in the organization.
If deleted by an org admin, the org admin must have permissions somewhere in a parent org.

Note that only a partner key can create a root org or add an admin to an empty root org.
Similarly, only a partner key can delete a root org or remove the last user from a root org.

Request property | Spec
---|---
Action | DELETE /vfo/orgs/[`orgId`](models.md#id-types)
Scala  | `class VFO_DeleteOrg`

Status | Response body spec
---|---
200 | Array of [Response org](models.md#response-org)
400 | `{"error": 400, "message": "Cannot delete org that has non-empty sub-orgs"}` <br> If the org has any non-empty sub-orgs down the hierarchy.
400 | `{"error": 400, "message": "Cannot delete root org that contains users or courses"}` <br> If the org is at root level and contains users or courses.
403 | `{"error": 403, "message": "Invalid VFO credentials"}` <br> If `SID` is not a valid VFO session or partner key.
500 | `{"error": 500, "message": "Malformed Org Tree"}` <br> If the given org belongs to a cyclic hierarchy, which is invalid.


## Upload org cover image

The user must be a signed in user and have `AdministerOrg` permission on the org. 

Request property | Spec
---|---
Action | POST /vfo/orgs/[`orgId`](models.md#id-types)/coverimage
Scala  | `class VFO_ReplaceCoverImage`
