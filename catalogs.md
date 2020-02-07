
# Catalog endpoints

## Get published course

Request property | Spec
---|---
Action | GET /catalogs/staged/courses/[`courseId`](models.md#id-types)
Scala  | `class GetCourse`

## Delete published course

One of the following permissions must hold:
- the session user must have `PublishCourses` permission on the course
- the course belongs to a legacy org, and the caller has `Admin` or `Author` role in that org
- the course is shared on a VFO org, and the caller has `AdministerOrg` permission in that org

Request property | Spec
---|---
Action | DELETE /catalogs/staged/courses/[`courseId`](models.md#id-types)
Scala  | `class DeleteCatalogCourse`

## Get published lessons

**DEPRECATED**

Request property | Spec
---|---
Action | ~~GET /catalogs/staged/courses/[`courseId`](models.md#id-types)/lessons~~
Scala  | `class GetLessons`

## Get published lesson

Request property | Spec
---|---
Action | GET /catalogs/staged/courses/[`courseId`](models.md#id-types)/lessons/[`lessonId`](models.md#id-types)
Scala  | `class GetLesson`

## Get published gadget instances

Request property | Spec
---|---
Action | GET /catalogs/staged/courses/[`courseId`](models.md#id-types)/lessons/[`lessonId`](models.md#id-types)/gadgets
Scala  | `class GetGadgets`

## Get published gadget instance

**DEPRECATED**

Request property | Spec
---|---
Action | ~~GET /catalogs/staged/courses/[`courseId`](models.md#id-types)/lessons/[`lessonId`](models.md#id-types)/gadgets/[`gadgetInstanceId`](models.md#id-types)~~
Action | ~~GET /catalogs/staged/courses/[`courseId`](models.md#id-types)/gadgets/[`gadgetInstanceId`](models.md#id-types)~~
Scala  | `class GetGadget`

## Get published course overview

**DEPRECATED**

Request property | Spec
---|---
Action | ~~GET /catalogs/staged/courses/[`courseId`](models.md#id-types)/overview~~
Scala  | `class GetOverview`

## Get published gadget instance config

Request property | Spec
---|---
Action | ~~GET /catalogs/staged/courses/[`courseId`](models.md#id-types)/lessons/[`lessonId`](models.md#id-types)/gadgets/[`gadgetInstanceId`](models.md#id-types)/config~~
Action | GET /catalogs/staged/courses/[`courseId`](models.md#id-types)/gadgets/[`gadgetInstanceId`](models.md#id-types)/config
Scala  | `class GetGConfig`

## Replace published gadget instance config

Request property | Spec
---|---
Action | ~~PUT /catalogs/staged/courses/[`courseId`](models.md#id-types)/lessons/[`lessonId`](models.md#id-types)/gadgets/[`gadgetInstanceId`](models.md#id-types)/config~~
Action | PUT /catalogs/staged/courses/[`courseId`](models.md#id-types)/gadgets/[`gadgetInstanceId`](models.md#id-types)/config
Scala  | `class ReplaceGConfig`

## Publish course

In order to be able to publish a course, at least one of the following conditions must hold:

- the caller has the permission `PublishCourses` for the course
- the course belongs to a legacy org, and the caller has either `Admin` or `Author` role in that org
- the course is shared on a VFO org, and the caller has `AdministerOrg` permission in that org

The request body specifies the course ID and the revision ID.
The revision must have been created earlier; see [Create course revision](courses.md#create-course-revision).

Request property | Spec
---|---
Action | POST /catalogs/staged/courses
Body model | [Course revision data](models.md#course-revision-data)
Scala  | `class AddCourseToCatalog`

Status | Response body spec
---|---
201 | [Course revision data](models.md#course-revision-data) | The response is an exact copy of the request body


## Delete published course

**DEPRECATED**

Request property | Spec
---|---
Action | ~~DELETE /labs/[`courseId`](models.md#id-types)~~
Scala  | `class DelistFromLabs`

## Get published course progress

Request property | Spec
---|---
Action | GET /catalogs/staged/courses/[`courseId`](models.md#id-types)/progress
Scala  | `class GetProgress`

## Put published course progress

Request property | Spec
---|---
Action | PUT /catalogs/staged/courses/[`courseId`](models.md#id-types)/progress
Scala  | `class ReplaceProgress`

## Get published course user state

** DEPRECATED **

## Start published course

Request property | Spec
---|---
Action | POST /catalogs/staged/courses/[`courseId`](models.md#id-types)/start
Scala  | `class StartCourse`

## Get published gadget instance user state

Request property | Spec
---|---
Action | ~~GET /catalogs/staged/courses/[`courseId`](models.md#id-types)/lessons/[`lessonId`](models.md#id-types)/gadgets/[`gadgetInstanceId`](models.md#id-types)/userstate~~
Action | GET /catalogs/staged/courses/[`courseId`](models.md#id-types)/gadgets/[`gadgetInstanceId`](models.md#id-types)/userstate
Scala  | `class GetGuState`

## Put published gadget instance user state

Request property | Spec
---|---
Action | ~~PUT /catalogs/staged/courses/[`courseId`](models.md#id-types)/lessons/[`lessonId`](models.md#id-types)/gadgets/[`gadgetInstanceId`](models.md#id-types)/userstate~~
Action | PUT /catalogs/staged/courses/[`courseId`](models.md#id-types)/gadgets/[`gadgetInstanceId`](models.md#id-types)/userstate
Scala  | `class ReplaceGuState`

** REMOVED **
Request property | Spec
---|---
Action | ~~GET /catalogs/staged/courses/[`courseId`](models.md#id-types)/state/[`userId`](models.md#id-types)~~
Scala  | ~~`class GetUserState`~~

