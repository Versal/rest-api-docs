
# Program endpoints

## Update program

Request property | Spec
---|---
Action | POST /programs/[`programId`](models.md#id-types)
Permissions | Session must have ```SetProgramVisibility``` permission in the program.
Scala  | `class AlterProgram`

## Update user access

Request property | Spec
---|---
Action | PUT /programs/[`programId`](models.md#id-types)/users/[`userId`](models.md#id-types)
Permissions | Session must have the ```ManageAllAuthoringInvitationsAndPermissions``` permission in the program.
Body model | ```{"id":"user_id","role":"role_name"}```
Scala  | `class ReplaceUserAccess`

## Revoke user access

Request property | Spec
---|---
Action | DELETE /programs/[`programId`](models.md#id-types)/users/[`userId`](models.md#id-types)
Permissions | Session must be associated with a user ID. The user must have 
            | the `ManageAllAuthoringInvitationsAndPermissions` permission in the program.
Scala  | `class DeleteUserAccess`

## Update user group access

Request property | Spec
---|---
Action | PUT /programs/[`programId`](models.md#id-types)/usergroups/[`groupId`](models.md#id-types)
Permissions | Session must have the ```ManageAllAuthoringInvitationsAndPermissions``` permission in the program.
Body model | ```{"role":"role_name"}```
Scala  | `class ReplaceUserGroupAccess`


Status | Response body spec
---|---
200 | 
400 | `{"error": 400, "message": "User groups are not enabled"}`<br> if feature flag `enableUserGroups` not enabled
400 | `{"error": 400, "message": "Invalid role '...'"}` <br> if invalid role provided
400 | `{"error": 400, "message": "Invalid user group ID specified : '...'"}` <br> if provided user group id is not numeric
400 | `{"error": 400, "message": "Expected numeric value for: ..."}` <br> if provided `programId` is not numeric
403 | `{"error": 403, "message": "Insufficient permissions"}`
404 | `{"error": 404, "message": "Program '...' not found"}` <br> If `programId` not found
404 | `{"error": 404, "message": "User group '...' not found"}` <br> If `groupId` not found

## Revoke user group access

Request property | Spec
---|---
Action | DELETE /programs/[`programId`](models.md#id-types)/usergroups/[`groupId`](models.md#id-types)
Permissions | Session must be associated with a user ID. The user must have 
            | the `ManageAllAuthoringInvitationsAndPermissions` permission in the program.
Scala  | `class RevokeUserGroupAccess`


Status | Response body spec
---|---
200 | 
400 | `{"error": 400, "message": "User groups are not enabled"}`<br> if feature flag `enableUserGroups` not enabled
400 | `{"error": 400, "message": "Invalid role '...'"}` <br> if invalid role provided
400 | `{"error": 400, "message": "Invalid user group ID specified : '...'"}` <br> if provided user group id is not numeric
400 | `{"error": 400, "message": "Expected numeric value for: ..."}` <br> if provided `programId` is not numeric
403 | `{"error": 403, "message": "Insufficient permissions"}`
404 | `{"error": 404, "message": "Program '...' not found"}` <br> If `programId` not found
404 | `{"error": 404, "message": "User group '...' not found"}` <br> If `groupId` not found