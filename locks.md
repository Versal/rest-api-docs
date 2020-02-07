
# Locking endpoints

## Get active locks

Retrieves all active locks for the specified subject. For now, it lists all the gadget locks in the specified course.

Request property | Spec
---|---
Action                                | GET /locks
[`SID` header](request.md#sid-header) |
`subject.courseId` param              | [`courseKey`](models.md#course-key). Required.
Body model                            | _no body_
Scala                                 | `class GetActiveLocks`

Status | Response body spec
---|---
200 | Array of [Resource lock](models.md#resource-lock)
404 | `{"error": 404, "message": "Course '$courseKey' not found"}` <br> If `courseKey` does not identify a course.
403 | `{"error": 403, "message": "Insufficient permissions"}` <br> If the user is not authorized to update the requested resource.
401 | [Invalid credentials](responses.md#invalid-credentials)

## Lock resource

Creates a new lock on the specified resource.
User can have only one lock on the resource of the one type.
If user requests another lock, the old one gets automatically released.

Request property | Spec
---|---
Action                                | POST /locks
[`SID` header](request.md#sid-header) | Must be a user `SID`.
Body model                            | [Lock request](models.md#lock-request)
Scala                                 | `class AcquireResourceLock`

Status | Response body spec
---|---
201 | [Resource lock](models.md#resource-lock)
404 | `{"error": 404, "message": "Course '$courseKey' not found"}` <br> If `courseKey` does not identify a course.
404 | `{"error": 404, "message": "Gadget '$gadgetId' not found"}` <br> If `lessonId` does not identify a gadget in the course.
403 | `{"error": 403, "message": "Resource locked"}` <br> If the requested lock subject is already locked by another user.
403 | `{"error": 403, "message": "Insufficient permissions"}` <br> If the user is not authorized to update the requested resource.
401 | [Invalid credentials](responses.md#invalid-credentials), if `SID` is not for a user.
400 | [Bad request](responses.md#bad-request)

## Unlock resource

Deletes specified lock.

Request property | Spec
---|---
Action     | DELETE /locks/[`lockId`](models.md#id-types)
Body model | _no body_
Scala      | `class ReleaseResourceLock`

Status | Response body spec
---|---
200 | `{}` If lock was successfully released
403 | `{"error": 403, "message": "Insufficient permissions"}` <br> If the lock was created by another user.
404 | `{"error": 404, "message": "Lock '$lockId' not found"}` <br> If the lock does not exist
