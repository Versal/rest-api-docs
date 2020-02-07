# Versal For Orgs - User Management

## Add user to org

Required permissions: org admin (permission `AdministerOrg`) in the given org (or parent), or partner key.

The request body must specify an array of VFO permissions.

Request property | Spec
---|---
Action | PUT /vfo/orgs/[`orgId`](models.md#id-types)/users/[`userId`](models.md#id-types)
Body model | [VFO org permissions](models.md#vfo-org-permissions)
Scala  | `class VFO_PutOrgUserWithPermissions`

## Ban user from VFO container

Required permissions: root administrator or partner key

Only valid for root orgs

Calling user ID must be different from passed-in user ID (prevents self-deletion)
         
This endpoint removes a user from a VFO container and retracts any permissions that user was granted to objects (orgs,
courses) owned by that container. Specifically, this endpoint will:

1. Delete all entries in `vfo_user_org_permission` for this user on orgs owned by the container
1. Delete all entries in `user_access` for this user on courses owned by the container
1. Delete the entry in `vfo_user_container_map` for this user and the container

Request property | Spec
---|---
Action | DELETE /vfo/orgs/[`orgId`](models.md#id-types)/users/[`userId`](models.md#id-types)
Body model | none
Scala | `class VFO_RemoveUserFromContainer`

Status | Response body spec
---|---
200 | No body
400 | Invalid VFO container specified (if org ID is invalid, nonexistant, or not a root org)
400 | Cannot self-delete from VFO container (if caller tries to delete their own user ID)
403 | Invalid VFO credentials (if caller is not a container root admin or a partner)
404 | User not found in container

## Batch ban multiple users from VFO container

Required permissions: root administrator or partner key

Only valid for root orgs

Calling user ID must not be present in the passed-in user ID list (prevents self-deletion)
         
This endpoint removes a list of users from a VFO container and retracts any permissions those users were granted to objects (orgs,courses) owned by that container. This endpoint is transactional (all succeed or all fail).

Specifically, this endpoint will:

1. Delete all entries in `vfo_user_org_permission` for this user on orgs owned by the container
1. Delete all entries in `user_access` for this user on courses owned by the container
1. Delete the entry in `vfo_user_container_map` for this user and the container

Request property | Spec
---|---
Action | POST /vfo/orgs/[`orgId`](models.md#id-types)/delete_users
Body model | `{"users": Array of`[`userId`](models.md#id-types)`}`
Scala | `class VFO_BatchRemoveContainerUsers`

Status | Response body spec
---|---
200 | No body
400 | Invalid VFO container specified (if org ID is invalid, nonexistant, or not a root org)
400 | Cannot self-delete from VFO container (if caller tries to delete their own user ID)
403 | Invalid VFO credentials (if caller is not a container root admin, partner or vfo access key)

## Restore user to VFO container

Users can be removed from a VFO container, at which time their org and course associations
(permissions)  are archived as "user history".  This endpoint restores org and course associations 
for a user from their most recently stored history (there can be many, if a user is restored and 
deleted more than once. Must use the root org ID (or container ID) not an ID for a sub org. 

It errors out completely only on the following cases
1. Invalid (not present in db) user Id 
2. Invalid VFO Org ID
3. Not-root VFO Org ID
4. History is not saved for this combination of (user Id, Org Id)
5. User already in the container - No changes will be made for this case, only 400 returned

Things can have changed after the history snapshot was taken, and we can have seven kinds of errors:

1. A VFO org id in the org permissions map can be found in the system 
2. A VFO org id in the org permissions map is not in the container
3. A permission in the org permissions map is not found in the system
4. A course id in the course permissions map is not found in the system
5. A course id in the course permissions map is not associated with the container
6. A permission in the course permissions map is not found in the system
7. A permission sets for a course do not cohere to one of the allowed roles (author or 
publisher), i.e. have some missing permissions

Any of these kinds of errors will not stop processing of the rest of the history digest, but will
be reported back in the response body. It will restore whatever possible. 

Required permissions: Partner Key

Request property | Spec
---|---
Action | POST /vfo/orgs/[`orgId`](models.md#id-types)/users/[`userId`](models.md#id-types)/restore
Body model | none
Scala | `class VFO_RestoreUserToContainer`

Status | Response body spec
---|---
200 | {"restoreErrors":[.. list.. of.. errors ..]}
400 | No saved user history for user id :uid, container :oid
400 | User :uid already in container :oid
400 | Invalid VFO credentials (if caller is not a container root admin)
404 | VFO Org :oid not found
404 | User :uid not found

## Add user group to org

Required permissions: org admin (permission `AdministerOrg`) in the given org (or parent), or partner key.

The request body must specify an array of VFO permissions.

Request property | Spec
---|---
Action | PUT /vfo/orgs/[`orgId`](models.md#id-types)/usergroups/[`userGroupId`](models.md#id-types)
Body model | [VFO org permissions](models.md#vfo-org-permissions)
Scala  | `class ReplaceUserGroupOrgAccess`

## Delete user group from org

Required permissions: root administrator or partner key

Request property | Spec
---|---
Action | DELETE /vfo/orgs/[`orgId`](models.md#id-types)/usergroups/[`userGroupId`](models.md#id-types)
Body model | none
Scala | `class RevokeUserGroupOrgAccess`

Status | Response body spec
---|---
200 | No body
400 | Invalid VFO container specified (if org ID is invalid, nonexistant, or not a root org)
403 | Invalid VFO credentials (if caller is not a container root admin or a partner)
404 | User group not found in container


## Get VFO containers (root orgs) for user

Required permissions: user session (VFO or non-VFO), or partner key.

If user session is given, the user must have the same user id as given in the request URL.

Request property | Spec
---|---
Action | GET /vfo/users/[`userId`](models.md#id-types)/orgs
Scala  | `class VFO_GetUserRootOrgs`

Returns the array of root orgs corresponding to each of the VFO containers where the given user has any VFO permissions.

Status | Response body spec
---|---
200 | Array of [Response org](models.md#response-org)
403 | `{"error": 403, "message": "Invalid VFO credentials"}` <br> If `SID` is not a valid VFO session or partner key.
500 | `{"error": 500, "message": "Malformed Org Tree"}` <br> If the given org belongs to a cyclic hierarchy, which is invalid.

## Get info and permissions of users in VFO container

Required permissions: org admin anywhere in the org container, or partner key.

Request property | Spec
---|---
Action | GET /vfo/orgs/[`orgId`](models.md#id-types)/users
Scala  | `class VFO_GetOrgUsers`

Returns information about all users who have any permissions anywhere in the org container to which `orgId` belongs.
Here, `orgId` can be a root org or any other suborg.

Status | Response body spec
---|---
200 | Array of [Response VFO user permissions](models.md#response-vfo-user-permissions)
403 | `{"error": 403, "message": "Invalid VFO credentials"}` <br> If `SID` is not a valid VFO session or partner key.
500 | `{"error": 500, "message": "Malformed Org Tree"}` <br> If the given org belongs to a cyclic hierarchy, which is invalid.


## Get info and permissions of single user in VFO container

Required permissions: org container admin, or partner key.

Request property | Spec
---|---
Action | GET /vfo/orgs/[`orgId`](models.md#id-types)/users/[`userId`](models.md#id-types)
Scala  | `class VFO_GetOrgUser`

Returns information about user who have any permissions anywhere in the org container to which `orgId` belongs.

Status | Response body spec
---|---
200 | [Response VFO user permissions](models.md#response-vfo-user-permissions)
400 | `{"error": 400, "message": "Invalid VFO container specified"}`
403 | `{"error": 403, "message": "Invalid VFO credentials"}` <br> If `SID` is not a valid VFO session or partner key.
404 | `{"error":404,"message":"User 'userId' not found in container 'orgId'"}`
500 | `{"error": 500, "message": "Malformed Org Tree"}` <br> If the given org belongs to a cyclic hierarchy, which is invalid.

# User Group

## Create user group

Required permission: org container admin or partner key

Request property | Spec
---|---
Action | POST /vfo/containers/[`containerId`](models.md#id-types)/usergroups
Body model | [User group](models.md#user-group)
Scala  | `class VFO_CreateUserGroup`

Returns newly created user group.

Status | Response body spec
---|---
201 | [Response user group](models.md#response-user-group)
400 | `{"error": 400, "message": "User groups are not enabled"}`<br> if feature flag `enableUserGroups` not enabled
400 | `{"error": 400, "message": "Invalid VFO container specified"}`
400 | `{"error": 400, "message": "Invalid input: name is ... chars, exceeding limit of 40"}` <br> if user group name is too long
400 | `{"error": 400, "message":"'...' is already in use"}` <br> if user group name is already exist in container
403 | `{"error": 403, "message": "Invalid VFO credentials"}` <br> If `SID` is not a valid VFO session or partner key.
404 | `{"error": 404, "message": "VFO Org '9999999' not found"}` <br> If `containerId` not found

## Update user group

Required permission: org container admin or partner key

Request property | Spec
---|---
Action | PUT /vfo/containers/[`containerId`](models.md#id-types)/usergroups/[`userGroupId`](models.md#id-types)
Body model | [User group](models.md#user-group)
Scala  | `class VFO_UpdateUserGroup`

Returns updated user group.

Status | Response body spec
---|---
200 | [Response user group](models.md#response-user-group)
400 | `{"error": 400, "message": "User groups are not enabled"}`<br> if feature flag `enableUserGroups` not enabled
400 | `{"error": 400, "message": "Invalid VFO container specified"}`
400 | `{"error": 400, "message": "Invalid input: name is ... chars, exceeding limit of 40"}` <br> if user group name is too long
400 | `{"error": 400, "message": "'...' is already in use"}` <br> if user group name is already exist in container
400 | `{"error": 400, "message": "Invalid user group ID specified : '...'"}` <br> if provided user group id is not numeric
403 | `{"error": 403, "message": "Invalid VFO credentials"}` <br> If `SID` is not a valid VFO session or partner key.
404 | `{"error": 404, "message": "VFO Org '...' not found"}` <br> If `containerId` not found
404 | `{"error": 404, "message": "User group '...' not found"}` <br> if `userGroupId` not found
404 | `{"error": 404, "message": "User group '...' not found in container '...'"}`  <br> if `userGroupId` not found in `containerId`

## Delete user group

Required permission: org container admin or partner key

Request property | Spec
---|---
Action | Delete /vfo/containers/[`containerId`](models.md#id-types)/usergroups/[`userGroupId`](models.md#id-types)
Body model | empty
Scala  | `class VFO_DeleteUserGroup`

Status | Response body spec
---|---
200 | 
400 | `{"error": 400, "message": "User groups are not enabled"}`<br> if feature flag `enableUserGroups` not enabled
400 | `{"error": 400, "message": "Invalid VFO container specified"}`
400 | `{"error": 400, "message": "Invalid user group ID specified : '...'"}` <br> if provided user group id is not numeric
403 | `{"error": 403, "message": "Invalid VFO credentials"}` <br> If `SID` is not a valid VFO session or partner key.
404 | `{"error": 404, "message": "VFO Org '...' not found"}` <br> If `containerId` not found
404 | `{"error": 404, "message": "User group '...' not found"}` <br> if `userGroupId` not found
404 | `{"error": 404, "message": "User group '...' not found in container '...'"}`  <br> if `userGroupId` not found in `containerId`

## Add user to group

Required permission: org container admin or partner key

Request property | Spec
---|---
Action | PUT /vfo/containers/[`containerId`](models.md#id-types)/usergroups/[`userGroupId`](models.md#id-types)/users/[`userId`](models.md#id-types)
Body model | empty
Scala  | `class VFO_AddUserToGroup`

Status | Response body spec
---|---
200 | [user](models.md#response-user)
400 | `{"error": 400, "message": "User groups are not enabled"}`<br> if feature flag `enableUserGroups` not enabled
400 | `{"error": 400, "message": "Invalid VFO container specified"}`
400 | `{"error": 400, "message": "Invalid user group ID specified : '...'"}` <br> if provided user group id is not numeric
400 | `{"error": 400, "message":"User '...' is already a member of group '...'"}` <br> id user is already a member of given user group
403 | `{"error": 403, "message": "Invalid VFO credentials"}` <br> If `SID` is not a valid VFO session or partner key.
404 | `{"error": 404, "message": "VFO Org '...' not found"}` <br> If `containerId` not found
404 | `{"error": 404, "message": "User group '...' not found"}` <br> if `userGroupId` not found
404 | `{"error": 404, "message": "User group '...' not found in container '...'"}`  <br> if `userGroupId` not found in `containerId`
404 | `{"error": 404, "message": "User '...' not found"}` <br> if `userId` not found

## Delete user from group

Required permission: org container admin or partner key

Request property | Spec
---|---
Action | Delete /vfo/containers/[`containerId`](models.md#id-types)/usergroups/[`userGroupId`](models.md#id-types)/users/[`userId`](models.md#id-types)
Body model | empty
Scala  | `class VFO_DeleteUserFromGroup`

Status | Response body spec
---|---
200 | 
400 | `{"error": 400, "message": "User groups are not enabled"}`<br> if feature flag `enableUserGroups` not enabled
400 | `{"error": 400, "message": "Invalid VFO container specified"}`
400 | `{"error": 400, "message": "Invalid user group ID specified : '...'"}` <br> if provided user group id is not numeric
403 | `{"error": 403, "message": "Invalid VFO credentials"}` <br> If `SID` is not a valid VFO session or partner key.
404 | `{"error": 404, "message": "VFO Org '...' not found"}` <br> If `containerId` not found
404 | `{"error": 404, "message": "User group '...' not found"}` <br> if `userGroupId` not found
404 | `{"error": 404, "message": "User group '...' not found in container '...'"}`  <br> if `userGroupId` not found in `containerId`
404 | `{"error": 404, "message": "User '...' not found"}` <br> if `userId` not found
404 | `{"error": 404, "message": "User '...' not found in group '...'"}` <br> if `userId` is not member of `userGroupId`

## List group users

Required permission: org container admin or partner key

Request property | Spec
---|---
Action | GET /vfo/containers/[`containerId`](models.md#id-types)/usergroups/[`userGroupId`](models.md#id-types)/users
[Pagination params](pagination.md#request-parameters) |
Body model | empty
Scala  | `class VFO_GetUserGroupUsers`

Status | Response body spec
---|---
200 | [paginated](pagination.md) array of [users](models.md#response-user)
400 | `{"error": 400, "message": "User groups are not enabled"}`<br> if feature flag `enableUserGroups` not enabled
400 | `{"error": 400, "message": "Invalid VFO container specified"}`
400 | `{"error": 400, "message": "Invalid user group ID specified : '...'"}` <br> if provided user group id is not numeric
403 | `{"error": 403, "message": "Invalid VFO credentials"}` <br> If `SID` is not a valid VFO session or partner key.
404 | `{"error": 404, "message": "VFO Org '...' not found"}` <br> If `containerId` not found
404 | `{"error": 404, "message": "User group '...' not found"}` <br> if `userGroupId` not found
404 | `{"error": 404, "message": "User group '...' not found in container '...'"}`  <br> if `userGroupId` not found in `containerId`

## Get user group

Required permission: org container admin or partner key

Request property | Spec
---|---
Action | GET /vfo/containers/[`containerId`](models.md#id-types)/usergroups/[`userGroupId`](models.md#id-types)
Body model | empty
Scala  | `class VFO_GetUserGroup`

Returns updated user group.

Status | Response body spec
---|---
200 | [Response user group](models.md#response-user-group)
400 | `{"error": 400, "message": "User groups are not enabled"}`<br> if feature flag `enableUserGroups` not enabled
400 | `{"error": 400, "message": "Invalid VFO container specified"}`
400 | `{"error": 400, "message": "Invalid user group ID specified : '...'"}` <br> if provided user group id is not numeric
403 | `{"error": 403, "message": "Invalid VFO credentials"}` <br> If `SID` is not a valid VFO session or partner key.
404 | `{"error": 404, "message": "VFO Org '...' not found"}` <br> If `containerId` not found
404 | `{"error": 404, "message": "User group '...' not found"}` <br> if `userGroupId` not found
404 | `{"error": 404, "message": "User group '...' not found in container '...'"}`  <br> if `userGroupId` not found in `containerId`

## List user groups

Required permission: org container admin or partner key

Request property | Spec
---|---
Action | GET /vfo/containers/[`containerId`](models.md#id-types)/usergroups
[Pagination params](pagination.md#request-parameters) |
Body model | empty
Scala  | `class VFO_GetUserGroups`

Returns updated user group.

Status | Response body spec
---|---
200 | [paginated](pagination.md) array of [user group](models.md#response-user-group)
400 | `{"error": 400, "message": "User groups are not enabled"}`<br> if feature flag `enableUserGroups` not enabled
400 | `{"error": 400, "message": "Invalid VFO container specified"}`
400 | `{"error": 400, "message": "Invalid user group ID specified : '...'"}` <br> if provided user group id is not numeric
403 | `{"error": 403, "message": "Invalid VFO credentials"}` <br> If `SID` is not a valid VFO session or partner key.
404 | `{"error": 404, "message": "VFO Org '...' not found"}` <br> If `containerId` not found

# Portals

## Delete user from portal

Required permission: org container admin or partner key

Removes all user's course enrollments for courses shared in this portal.
Bans user's journal updates.

Request property | Spec
---|---
Action | POST /vfo/container/[`containerId`](models.md#id-types)/portal/[`portalId`](models.md#id-types)/delete_users
Body model | `{"users": Array of`[`userId`](models.md#id-types)`}`
Scala | `class VFO_BatchRemovePortalUsers`

Status | Response body spec
---|---
200 | No body
400 | Invalid VFO container or portal specified
400 | Cannot self-delete from VFO container (if caller tries to delete their own user ID)
403 | Invalid VFO credentials (if caller is not a container root admin, partner or vfo access key)