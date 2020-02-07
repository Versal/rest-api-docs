# Versal For Orgs - Course Management

## Share or unshare course with orgs

Required permissions: org admin (permission `AdministerOrg`) in each of the given orgs (or parent), or partner key.

The request body must specify a map of VFO org ids to boolean values.
The course `courseId` will be shared with orgs having `true` value and unshared with orgs having `false` value in the map.

The given org ids must all belong to the same org container to which the user's VFO session belongs.
The course must not be previously shared in any different org container.

If the course is in Limbo state and it now shared with some orgs, it will leave the Limbo state.

If the course is now not shared with any org, it will enter the Limbo state with the same org container as the user's VFO session.

Request property | Spec
---|---
Action | PATCH /vfo/courses/[`courseId`](models.md#id-types)/orgs
Body model | map of [`orgId`](models.md#id-types) to [`boolean`](models.md#boolean)
Scala  | `class VFO_PatchCourseOrgRelations`


Status | Response body spec
---|---
200 | {}
403 | `{"error": 403, "message": "Insufficient permissions for org $orgId"}` <br> If the requesting user does not have privileges in the org `orgId` where they request to share a course.
404 | `{"error": 404, "message": "VFO Org ID $orgId not found in root container $rootOrgId"}` <br> If `orgId` was requested for sharing but does not belong to the same org container as the user session.
404 | `{"error": 404, "message": "Course '$courseKey' not found in Limbo of root container $rootOrgId"}` <br> If `courseId` does not belong to the Limbo state at the same root org container as the user session.

## Move courses to VFO Container

The request body must specify a list of courses (possibly empty).
These courses will be shared with the given org `orgId`, and unshared from any previous orgs or Limbo states they might have been in.
The user does not need to have privileges in any of the orgs where the courses were previously shared.
Both course learners and contributors will be added to the destination org container. 
They will not be removed from the origin org container. 

The requestor must be a partner key or the following requirements need to be met:

- requesting user must have a VFO session and be a member of the given org `orgId`
- requesting user must be the sole course creator ("publisher") of each of the given courses

Also, none of the courses must be already shared with the same org `orgId`.

If these conditions are not met for any of the given course ids, an error will be returned and no course associations will be changed.

Request property | Spec
---|---
Action | PUT /vfo/orgs/[`orgId`](models.md#id-types)/courses
Body model | [VFO move course request](models.md#vfo-move-course-request)
Scala  | `class VFO_MoveCourses`

Status | Response body spec
---|---
200 | {}
400 | `{"error": 400, "message": "User is not sole creator of the course '$courseKey'"}` <br> If there are other course creators, or if the user is not a course creator of this course.
400 | `{"error": 400, "message": "Course '$courseKey' is already shared with this org"}` <br> If the course was already shared with the org `orgId`.
404 | `{"error": 404, "message": "Course '$courseKey' not found"}` <br> If any of the given course IDs does not refer to an existing Versal course.

## Add courses to org

This endpoint allows to add multiple courses to an org. They are added after all currently associated orgs.

Request property | Spec
---|---
Action                                | POST /vfo/orgs/[`orgId`](models.md#id-types)/add_courses
[`SID` header](request.md#sid-header) |
Body model                            | A JSON list of [course key](models.md#id-types) as strings.
Scala                                 | `object VFO_AddManyCoursesToOrg`

Status | Response body spec
---|---
200 | `{}`
400 | [Bad request](responses.md#bad-request)
400 | `{"error":400,"message":"Some courses (courseId1, courseId2, ...) are already in org"}` <br> If any of the provided courses are already in the org.
401 | [Invalid credentials](responses.md#invalid-credentials)
403 | `{"error": 403, "message": "Invalid VFO credentials"}` <br> If requesting session is neither a partner key, nor the user session with AdministerOrg permission.
404 | `{"error": 404, "message": "OrgId not found"}` <br> If orgId is not a valid org.


## Remove courses from org

This endpoint allows to remove multiple courses from an org. They are added after all currently associated orgs.

Request property | Spec
---|---
Action                                | POST /vfo/orgs/[`orgId`](models.md#id-types)/remove_courses
[`SID` header](request.md#sid-header) |
Body model                            | A JSON list of [course key](models.md#id-types) as strings.
Scala                                 | `object VFO_RemoveManyCoursesFromOrg`

Status | Response body spec
---|---
200 | `{}`
400 | [Bad request](responses.md#bad-request)
400 | `{"error":400,"message":"Some courses (courseId1, courseId2, ...) are not associated with the org"}` <br> If any of the provided courses are not in the org.
401 | [Invalid credentials](responses.md#invalid-credentials)
403 | `{"error": 403, "message": "Invalid VFO credentials"}` <br> If requesting session is neither a partner key, nor the user session with AdministerOrg permission.
404 | `{"error": 404, "message": "OrgId not found"}` <br> If orgId is not a valid org.

## Reorder courses in an org

This endpoint allows to reorder courses in a org.

Request property | Spec
---|---
Action                                | POST /vfo/orgs/[`orgId`](models.md#id-types)/reorder_courses
[`SID` header](request.md#sid-header) |
Body model                            | [Reorder org course model](models.md#reorder-org-courses)
Scala                                 | `object VFO_ReorderOrgCourses`

Status | Response body spec
---|---
200 | `{}`
400 | [Bad request](responses.md#bad-request)
400 | `{"error":400,"message":"Course XYZ is not associated with org 123"}` <br> If any of the provided courses are not in the org.
401 | [Invalid credentials](responses.md#invalid-credentials)
403 | `{"error": 403, "message": "Invalid VFO credentials"}` <br> If requesting session is neither a partner key, nor the user session with AdministerOrg permission.
404 | `{"error": 404, "message": "Org orgId not found"}` <br> If orgId is not found.
