
# Course endpoints

## Get course

Retrieves information about a specified course.

Request property | Spec
---|---
Action                                | GET /courses/[`courseKey`](models.md#course-key)
[`SID` header](request.md#sid-header) |
`depth` param                         | Controls the amount of gadget data in the response. Can be one of the following values: <ul><li>`lessons` - the gadgets within each lesson are omitted from the response</li><li>`sections` - only the Versal `header`/`section` gadgets are included within each lesson; all others are omitted</li><li>`full` - all gadgets are included within each lesson in the response</li><li>`noblocking` - all gadgets are included with all lessons regardless of lesson blocking</li></ul> If not specified, defaults to `lessons`.
~~`deep` param~~                      | **[DEPRECATED]** Boolean-valued parameter that was the precursor to `depth`. If `true`, equivalent to `depth=full`; otherwise, or if not specified, equivalent to `depth=lessons`. If `depth` is also specified, this is ignored.
Body model                            | _no body_
Scala                                 | `class GetCourse`

The course instance that is sent back in the response depends on who is making the request. If `SID` is
a partner key, or represents an author of the course, then the editable ("sandbox") instance is returned.
Otherwise, the published instance is returned, if it exists.

Status | Response body spec
---|---
200 | [Course details](models.md#course-details)
404 | `{"error": 404, "message": "Course '$courseKey' not found"}` <br> If `courseKey` does not identify a course.
403 | `{"error": 403, "message": "Insufficient permissions"}` <br> If `SID` does not have either of the `EnrollInAPublishedCourse` or `ViewUnpublishedCourseAsLearner` permissions.
403 | `{"error": 403, "message": "Course is not staged"}` <br> If `SID` is not a partner key or course author, and the course is not published.
401 | [Invalid credentials](responses.md#invalid-credentials)

## Get lesson

Request property | Spec
---|---
Action                                | GET /courses/[`courseKey`](models.md#course-key)/lessons/[`lessonId`](models.md#id-types)
[`SID` header](request.md#sid-header) |
Body model                            | _no body_
Scala                                 | `class GetLesson`

The lesson that is sent back in the response depends on who is making the request. If `SID` is
a partner key, or represents an author of the course, then the lesson from the editable ("sandbox")
course instance is returned. Otherwise, the lesson from the published instance is returned, if the
course is published.

Status | Response body spec
---|---
200 | [Lesson](models.md#lesson)
404 | `{"error": 404, "message": "Course '$courseKey' not found"}` <br> If `courseKey` does not identify a course.
404 | `{"error": 404, "message": "Lesson '$lessonId' not found"}` <br> If `lessonId` does not identify a lesson in the course identified by `courseKey`.
403 | `{"error": 403, "message": "Insufficient permissions"}` <br> If `SID` does not have either of the `EnrollInAPublishedCourse` or `ViewUnpublishedCourseAsLearner` permissions.
403 | `{"error": 403, "message": "Course is not staged"}` <br> If `SID` is not a partner key or course author, and the course is not published.
401 | [Invalid credentials](responses.md#invalid-credentials)

## Add gadget to lesson

Inserts a new gadget instance of a specified type into a lesson. The new gadget will have the default config.

Request property | Spec
---|---
Action                                | POST /courses/[`courseKey`](models.md#course-key)/lessons/[`lessonId`](models.md#id-types)/gadgets
[`SID` header](request.md#sid-header) | Must be a user `SID`.
Body model                            | [Gadget instance request](models.md#gadget-instance-request)
Scala                                 | `class AddGadget`

Status | Response body spec
---|---
201 | `{"id": "$gadgetInstanceId"}`, where `gadgetInstanceId` is the ID of the new gadget instance.
404 | `{"error": 404, "message": "Course '$courseKey' not found"}` <br> If `courseKey` does not identify a course.
404 | `{"error": 404, "message": "Lesson '$lessonId' not found"}` <br> If `lessonId` does not identify a lesson in the course.
403 | `{"error": 403, "message": "User [$userId] is not a course author"}` <br> If the `SID` user is not an author of the course.
403 | `{"error": 403, "message": "Insufficient permissions: gadget visibility"}` <br> If there is not already a gadget of the same type in the course, and the gadget is owned by an org that is not the same as the course's org.
401 | [Invalid credentials](responses.md#invalid-credentials), if `SID` is not for a user.
422 | `{"error": 422, "message": "No value for field $fieldName" }` <br> If gadget is a quiz and a required field is missing in quiz config. 
422 | `{"error": 422, "message": "No questions" }` <br> If gadget is a quiz and the `questions` field is missing or is an empty list. 
422 | `{"error": 422, "message": "No answers for a question" }` <br> If gadget is a quiz and `answers` field is missing or is an empty list for at least one of the questions. 
422 | `{"error": 422, "message": "answerKey refers to non-existent answer" }` <br> If gadget is a quiz and `answerKey` refers to a non-existent answer. 
422 | `{"error": 422, "message": "answerKey refers to non-existent question" }` <br> If gadget is a quiz and `answerKey` refers to a non-existent question. 
422 | `{"error": 422, "message": "answerKey does not contain answers to all questions" }` <br> If gadget is a quiz and `answerKey` does not contain answers to all questions. 
400 | [Bad request](responses.md#bad-request)

## Add many gadgets to lesson

Inserts an array of gadget instances into a lesson. Each new gadget will have its default config.

The path `many_gadgets` is active but deprecated in favor of the more descriptive `insert_gadgets`.

Request property | Spec
---|---
Action                                | ~~POST /courses/[`courseKey`](models.md#course-key)/lessons/[`lessonId`](models.md#id-types)/many_gadgets~~
Action                                | POST /courses/[`courseKey`](models.md#course-key)/lessons/[`lessonId`](models.md#id-types)/insert_gadgets
[`SID` header](request.md#sid-header) | Must be a user `SID` (session must be linked to a user account).
Body model                            | [Many-gadgets request](models.md#many-gadgets-request)
Scala                                 | `class AddManyGadgets`

Status | Response body spec
---|---
201 | `["id1", "id2", ...]` - array of new gadget instance IDs.
404 | `{"error": 404, "message": "Course '$courseKey' not found"}` <br> If `courseKey` does not identify a course.
404 | `{"error": 404, "message": "Lesson '$lessonId' not found"}` <br> If `lessonId` does not identify a lesson in the course.
403 | `{"error": 403, "message": "User [$userId] is not a course author"}` <br> If the `SID` user is not an author of the course.
403 | `{"error": 403, "message": "Insufficient permissions: gadget visibility"}` <br> If there is not already a gadget of the same type in the course, and the gadget is owned by an org that is not the same as the course's org.
401 | [Invalid credentials](responses.md#invalid-credentials), if `SID` is not for a user.
422 | `{"error": 422, "message": "No value for field $fieldName" }` <br> If gadget is a quiz and a required field is missing in quiz config. 
422 | `{"error": 422, "message": "No questions" }` <br> If gadget is a quiz and the `questions` field is missing or is an empty list. 
422 | `{"error": 422, "message": "No answers for a question" }` <br> If gadget is a quiz and `answers` field is missing or is an empty list for at least one of the questions. 
422 | `{"error": 422, "message": "answerKey refers to non-existent answer" }` <br> If gadget is a quiz and `answerKey` refers to a non-existent answer. 
422 | `{"error": 422, "message": "answerKey refers to non-existent question" }` <br> If gadget is a quiz and `answerKey` refers to a non-existent question. 
422 | `{"error": 422, "message": "answerKey does not contain answers to all questions" }` <br> If gadget is a quiz and `answerKey` does not contain answers to all questions. 
400 | [Bad request](responses.md#bad-request)

## Replace gadget instance config

Sets the configuration for the specified gadget instance to the value provided in the request body.

Specifying `lessons/:lessonId` in the request URL is deprecated.

Request property | Spec
---|---
Action                                | ~~PUT /courses/[`courseId`](models.md#id-types)/lessons/[`lessonId`](models.md#id-types)/gadgets/[`gadgetId`](models.md#id-types)/config~~
Action                                | PUT /courses/[`courseId`](models.md#id-types)/gadgets/[`gadgetId`](models.md#id-types)/config
[`SID` header](request.md#sid-header) | Must be a user `SID`.
Body model                            | [Gadget config](models.md#gadget-config)
Scala                                 | `class ReplaceGConfig`

Note that if the body cannot be parsed as a JSON object, no error response will be generated, but the config will
be treated as the empty JSON object (`{}`) in circumstances where it needs to be parsed by the API. This is for
performance reasons, since parsing/validation is an expensive operation.

Status | Response body spec
---|---
200 | `{}`
404 | `{"error": 404, "message": "Course '$courseId' not found"}` <br> If `courseId` does not identify a course.
404 | `{"error": 404, "message": "GadgetInstance '$gadgetId' not found"}` <br> If `lessonId` and `gadgetId` do not identify a gadget instance in the editable course instance.
403 | `{"error": 403, "message": "User [$userId] is not a course author"}` <br> If the requesting user does not have authoring permissions on the course.
403 | `{"error": 403, "message": "Resource locked"}` <br> If the gadget is locked by another user.
422 | `{"error": 422, "message": "No value for field $fieldName" }` <br> If gadget is a quiz and a required field is missing in quiz config. 
422 | `{"error": 422, "message": "No questions" }` <br> If gadget is a quiz and the `questions` field is missing or is an empty list. 
422 | `{"error": 422, "message": "No answers for a question" }` <br> If gadget is a quiz and `answers` field is missing or is an empty list for at least one of the questions. 
422 | `{"error": 422, "message": "answerKey refers to non-existent answer" }` <br> If gadget is a quiz and `answerKey` refers to a non-existent answer. 
422 | `{"error": 422, "message": "answerKey refers to non-existent question" }` <br> If gadget is a quiz and `answerKey` refers to a non-existent question. 
422 | `{"error": 422, "message": "answerKey does not contain answers to all questions" }` <br> If gadget is a quiz and `answerKey` does not contain answers to all questions. 
401 | [Invalid credentials](responses.md#invalid-credentials), if `SID` is not for a user.

## Replace gadget user state

Sets the user state for the `SID` user on the specified gadget instance to the value provided in the request body.

Specifying `lessons/:lessonId` in the request URL is deprecated.

Request property | Spec
---|---
Action                                | ~~PUT /courses/[`courseKey`](models.md#course-key)/lessons/[`lessonId`](models.md#id-types)/gadgets/[`gadgetId`](models.md#id-types)/userstate~~
Action                                | PUT /courses/[`courseKey`](models.md#course-key)/gadgets/[`gadgetId`](models.md#id-types)/userstate
[`SID` header](request.md#sid-header) | Must be a user `SID`.
Body model                            | [Gadget user state](models.md#gadget-user-state)
Scala                                 | `class ReplaceGuState`

This endpoint is very permissive, and not in a good way. It doesn't check whether `lessonId` or `gadgetId` actually
identify items within the course. It does check whether the `SID` user has learner permissions on the course, or
authoring permissions if the course isn't published, but then it _ignores_ those checks due to historical
command log "rejections" (see `UserState.allowUnstaged()`).

To avoid the performance hit of always parsing the user state JSON in the request body, the manifest corresponding
to the specified gadget instance is retrieved, and its `exec` field is checked. If the value is `quiz` or `publicQuiz`,
then the user state JSON is parsed and quiz grading is performed. Otherwise, the user state is stored exactly as sent,
without parsing (even if it's not actually JSON).

The quiz grading process is complex, and modifies the user state provided in the request body before it is persisted.
Please refer to `UserState.processQuiz()` and `UserState.processPublicQuiz()` in the code for more details.

An informal rule for this endpoint is to be robust to error conditions. There are several places in the code where
data lookups can fail; these are all generally handled in the same way, by setting the user state to an empty value.
The few errors that can actually occur are documented below.

Status | Response body spec
---|---
200 | The new value of the [gadget user state](models.md#gadget-user-state).
400 | `{"error": 400, "message": "Course '$courseKey' not found"}` <br> If `courseKey` does not identify a course.
401 | [Invalid credentials](responses.md#invalid-credentials), if `SID` is not for a user.

## Update course progress

Records the lesson that the `SID` user is currently learning from.

Requires a user session (not partner key or authenticator session).

Request property | Spec
---|---
Action | PUT /courses/[`courseKey`](models.md#course-key)/progress
[`SID` header](request.md#sid-header) | Must be a user `SID`.
Body model | [Progress update](models.md#progress-update)
Scala  | `class ReplaceProgress`

The `SID` user is also recorded as starting the course if they aren't already a learner; this replaces the
functionality of the deprecated [~~start course~~](#start-course) endpoint. This was done to avoid an extra
request, since the frontend updates the progress for the first lesson anyway.

Status | Response body spec
---|---
200 | [Progress update](models.md#progress-update)
400 | `{"error": 400, "message": "Course '$courseKey' not found"}` <br> If `courseKey` does not identify a course.
400 | `{"error": 400, "message": "Lesson index or lesson ID is required"}` <br> If neither `lessonIndex` nor `currentLessonId` is given in the request body.
403 | `{"error": 403, "message": "Insufficient permissions"}` <br> If `SID` does not have either of the `EnrollInAPublishedCourse` or `ViewUnpublishedCourseAsLearner` permissions.
403 | `{"error": 403, "message": "Course is not staged"}` <br> If `SID` is not a partner key or course author, and the course is not published.
404 | `{"error": 404, "message": "Requested lesson not found"}` <br> If `currentLessonId` in the request body is not a valid lesson ID for a lesson in this course, or if `lessonIndex` in the request body is not between 1 and the number of lessons, inclusive.
403 | `{"error": 403, "message": "Can't start at or progress to blocked lesson"}` <br> If `lessonIndex` is greater than the index of the first blocked lesson, and the `SID` user is not a course author.
401 | [Invalid credentials](responses.md#invalid-credentials), if `SID` is not for a user.

Using `lessonIndex` in the request body is deprecated. Use `currentLessonId` instead.


## Update course position

Records the lesson or section that the `SID` user is currently learning from.

Requires a user session (not partner key or authenticator session).

Request property | Spec
---|---
Action | PUT /courses/[`courseKey`](models.md#course-key)/position
[`SID` header](request.md#sid-header) | Must be a user `SID`.
Body model | [Position update](models.md#position-update)
Scala  | `class ReplacePosition`

The `SID` user is also recorded as starting the course if they aren't already a learner.

Status | Response body spec
---|---
200 | [Position update](models.md#position-update)
400 | `{"error": 400, "message": "Course '$courseKey' not found"}` <br> If `courseKey` does not identify a course.
400 | `{"error": 400, "message": "currentLessonId is required"}` <br> If `displaySection` is `LESSON` but `currentLessonId` is missing in the request body.
400 | `{"error": 400, "message": "Invalid slug format"}` <br> If `displaySection` contains not alphanumeric char.
400 | `{"error": 400, "message": "Invalid input: displaySection is ... chars, exceeding limit of 128"}` <br> If `displaySection` is too long.
403 | `{"error": 403, "message": "Insufficient permissions"}` <br> If `SID` does not have either of the `EnrollInAPublishedCourse` or `ViewUnpublishedCourseAsLearner` permissions.
403 | `{"error": 403, "message": "Course is not staged"}` <br> If `SID` is not a partner key or course author, and the course is not published.
404 | `{"error": 404, "message": "Requested lesson not found"}` <br> If `currentLessonId` in the request body is not a valid lesson ID for a lesson in this course.
403 | `{"error": 403, "message": "Can't start at or progress to blocked lesson"}` <br> If `currentLessonId` is after the first blocked lesson, and the `SID` user is not a course author.
401 | [Invalid credentials](responses.md#invalid-credentials), if `SID` is not for a user.

## Add learner to course

Add a course learner.

Request property | Spec
---|---
Action                                | PUT /courses/[`courseId`](models.md#id-types)/learners/[`learnerId`](models.md#id-types)
[`SID` header](request.md#sid-header) | Must be a partner key; other sessions get a 401
Body model                            | _no body_
Scala                                 | `class AddLearnerToCourse`

A timestamp is recorded for the course start time.

Status | Response body spec
---|---
200 | `{}`
404 | `{"error": 404, "message": "Course '$courseId' not found"}` <br> The path parameter `courseId` does not identify a course.
404 | `{"error": 404, "message":" User '$learnerId' not found"}` <br> The path parameter `learnerId` does not identify a user.
403 | `{"error": 403, "message": "Course is not staged"}` <br> The requesting user is not an author of the course and the course is not published.
401 | [Invalid credentials](responses.md#invalid-credentials); the `SID` is not a partner key.

## ~~Start course~~

**[DEPRECATED]** Join a course as a learner.

Request property | Spec
---|---
Action                                | POST /courses/[`courseId`](models.md#id-types)/start
[`SID` header](request.md#sid-header) | Must be the `SID` of a user; partner keys and authenticators will get a 401
Body model                            | _no body_
Scala                                 | `class StartCourse`

A timestamp is recorded for the course start time.

Status | Response body spec
---|---
201 | `{"id": "$courseId"}`
404 | `{"error": 404, "message": "Course '$courseId' not found"}` <br> The path parameter `courseId` does not identify a course.
403 | `{"error": 403, "message": "Insufficient permissions"}` <br> The requesting user does not have learner permissions in the course's program.
403 | `{"error": 403, "message": "Course is not staged"}` <br> The requesting user is not an author of the course and the course is not published.
   | [Invalid credentials](responses.md#invalid-credentials); the `SID` was not for a user.

## List courses

Retrieves all courses matching criteria specified in the request.

Request property | Spec
---|---
Action                                                | GET /courses
[`SID` header](request.md#sid-header)                 |
[Pagination params](pagination.md#request-parameters) |
`author` param                                        | Restricts results to courses where the given [user ID](models.md#id-types) is a course creator (VFO context dependent) or course contributor (VFO context agnostic).
`role` param                                          | Accepts a comma-separated list of roles. Restricts results to courses where the calling session has the given `role` with respect to the course. Valid roles are `publisher` (a.k.a creator, VFO context dependent), `author` (a.k.a. contributor, VFO context agnostic), `instructor` (a.k.a educator, VFO context dependent), and `learner` (VFO context agnostic).
`permission` param                                    | Accepts a comma-separated list of permissions. Restricts results to courses where the calling session has the given `permission` with respect to the course.
`orgId` param                                         | Restricts results to courses belonging to the given legacy [org ID](models.md#id-types) or [org name](models.md#slug).
`title` param                                         | Restricts results to courses with titles containing the given [string](models.md#string) value.
`catalog` param                                       | With value `labs`, restricts results to published courses. With value `sandbox`, restricts results to courses the requesting user can edit. All other values are ignored.
`orgshares` param                                     | Accepts a comma-separated list of [VFO Org ID](models.md#id-types). Restricts results to courses shared with any of the VFO Orgs in the list. Orgs must cohere with the session's VFO container. Caller must be a member (via any VFO permission) of each org in the list.
`rootOrgId` param                                     | Restricts results to courses attached to the VFO container [VFO_Org ID](models.md#id-types).
`viewModel` param                                     | Valid values are: `full` - print full course view model (default), `ids` - print only course ID property of view model, i.e.,`{"id":"CourseID"}`
Body model                                            | _no body_
Scala                                                 | `object GetCourses`

The courses that are listed depend on who is making the request, as follows:
- If `SID` is a partner key, list only sandbox instances
- If `SID` is for a user session, each course listed must meet at least one of the following criteria:
    - the course belongs to a free program and the given `SID` was issued by the Versal authenticator
    - the course belongs to an org program and the given `SID` is for an admin of that org
    - the given `SID` and its authenticator both have either `EnrollInAPublishedCourse` or
      `ViewUnpublishedCourseAsLearner` permissions in the course's program

The particular instance listed for each course depends on the following:
- If `SID` is a partner key, or representing a user that can edit the course, the sandbox instance is listed
- Otherwise, if the course is published, the published instance is listed
- Otherwise, no instances of that course are listed

Status | Response body spec
---|---
200 | [paginated](pagination.md) array of [base course](models.md#base-course)
401 | [Invalid credentials](responses.md#invalid-credentials)
400 | [Bad request](responses.md#bad-request)
    | [Invalid pagination parameters](responses.md#invalid-pagination-parameters)

## Search courses

Retrieves all courses matching specified criteria

Request property | Spec
---|---
Action | `GET /courses/search`
[`SID` header](request.md#sid-header) |
[Pagination params](pagination.md#request-parameters) |
`authorname` | Author name: [string](models.md#string)
`authorid` | Author: [user ID](models.md#id-types)
`containerId` | Container ID [VFO_Org ID](models.md#id-types)
`portalId` | Portal ID [VFO_Org ID](models.md#id-types)
`topicId` | Topic ID [VFO_Org ID](models.md#id-types)
`gadget` | gadget name [string](models.md#string)
`tag` | tag [string](models.md#string)
`text` | course content full text search criterion [string](models.md#string)
`viewModel` | Valid values are: `full` - print full course view model (default), `ids` - print only course ID property of view model, i.e.,`{"id":"CourseID"}`
`portalView` | `true` or `false`. Optional. Default `false`.
Body model | _no body_
Scala | `object SearchCourses`

Status | Response body spec
---|---
200 | [paginated](pagination.md) array of: <br>[base course](models.md#base-course) if `viewModel` is `full`<br>[course ID](models.md#id-types) if `viewModel` is `ids`
401 | [Invalid credentials](responses.md#invalid-credentials)
400 | [Bad request](responses.md#bad-request)
400 | [Invalid pagination parameters](responses.md#invalid-pagination-parameters)


## Create course

Request property | Spec
---|---
Action | POST /courses
Scala  | `object AddCourse`

## Clone course

Request property | Spec
---|---
Action | POST /courses/[`courseId`](models.md#id-types)/clone
Scala  | `object CloneCourse`

Status | Response body spec
---|---
201 | `{}` <br> If course is successfully cloned.
401 | [Invalid credentials](responses.md#invalid-credentials)
404 | `{"error": 404, "message": "Course '$courseKey' not found"}` <br> If `courseKey` does not identify a course.

    
## Get course palette

Request property | Spec
---|---
Action | GET /courses/[`courseId`](models.md#id-types)/palette
Scala  | `class GetPalette`

## Upgrade palette

Request property | Spec
---|---
Action | PUT /courses/[`courseId`](models.md#id-types)/palette/[`devKey`](models.md#developer-key)/[`gadgetName`](models.md#slug)/[`gadgetVersion`](models.md#gadget-version)
Scala  | `class UpgradePalette`

## Get gadget instance

Specifying `lessons/:lessonId` in the request URL is deprecated.

Request property | Spec
---|---
Action | ~~GET /courses/[`courseId`](models.md#id-types)/lessons/[`lessonId`](models.md#id-types)/gadgets/[`gadgetId`](models.md#id-types)~~
Action | GET /courses/[`courseId`](models.md#id-types)/gadgets/[`gadgetId`](models.md#id-types)
Scala  | `class GetGadget`

## Replace gadget instance

Request property | Spec
---|---
Action | PUT /courses/[`courseId`](models.md#id-types)/lessons/[`lessonId`](models.md#id-types)/gadgets/[`gadgetId`](models.md#id-types)
Scala  | `class ReplaceGadget`

Status | Response body spec
---|---
200 | {}
403 | `{"error": 403, "message": "Resource locked"}` <br> If some gadgets in the course are locked by another user.
422 | `{"error": 422, "message": "No value for field $fieldName" }` <br> If gadget is a quiz and a required field is missing in quiz config. 
422 | `{"error": 422, "message": "No questions" }` <br> If gadget is a quiz and the `questions` field is missing or is an empty list. 
422 | `{"error": 422, "message": "No answers for a question" }` <br> If gadget is a quiz and `answers` field is missing or is an empty list for at least one of the questions. 
422 | `{"error": 422, "message": "answerKey refers to non-existent answer" }` <br> If gadget is a quiz and `answerKey` refers to a non-existent answer. 
422 | `{"error": 422, "message": "answerKey refers to non-existent question" }` <br> If gadget is a quiz and `answerKey` refers to a non-existent question. 
422 | `{"error": 422, "message": "answerKey does not contain answers to all questions" }` <br> If gadget is a quiz and `answerKey` does not contain answers to all questions. 

## Delete gadget

Request property | Spec
---|---
Action | DELETE /courses/[`courseId`](models.md#id-types)/lessons/[`lessonId`](models.md#id-types)/gadgets/[`gadgetId`](models.md#id-types)
Scala  | `class DeleteGadget`

Status | Response body spec
---|---
204 |
403 | `{"error": 403, "message": "Resource locked"}` <br> If the gadget is locked by another user.

## Delete many gadgets from lesson

Delete a list of gadget instances from a lesson. Gadget IDs must be valid for tr

Request property | Spec
---|---
Action                                | POST /courses/[`courseKey`](models.md#course-key)/lessons/[`lessonId`](models.md#id-types)/delete_gadgets
[`SID` header](request.md#sid-header) |
Body model                            | {"gadgets": List of [`gadgetId`](models.md#id-types)}
Scala                                 | `class DeleteManyGadgets`

Status | Response body spec
---|---
201 | `["id1", "id2", ...]` - array of new gadget instance IDs.
404 | `{"error": 404, "message": "Course '$courseKey' not found"}` <br> If `courseKey` does not identify a course.
404 | `{"error": 404, "message": "Lesson '$lessonId' not found"}` <br> If `lessonId` does not identify a lesson in the course.
403 | `{"error": 403, "message": "User [$userId] is not a course author"}` <br> If the `SID` user is not an author of the course.
400 | `{"error": 400, "message": "Empty gadget list"}`
404 | `{"error": 404, "message": "Invalid gadget instance IDs: 1, 2, ..., n"}`
403 | `{"error": 403, "message": "Locked gadget instance IDs: 1, 2, ..., n"}` <br> If some of the gadgets are locked by another user.
401 | [Invalid credentials](responses.md#invalid-credentials), if `SID` is not for a user.
400 | [Bad request](responses.md#bad-request)


## Get gadget instance config

Specifying `lessons/:lessonId` in the request URL is deprecated.

Request property | Spec
---|---
Action | ~~GET /courses/[`courseId`](models.md#id-types)/lessons/[`lessonId`](models.md#id-types)/gadgets/[`gadgetId`](models.md#id-types)/config~~
Action | GET /courses/[`courseId`](models.md#id-types)/gadgets/[`gadgetId`](models.md#id-types)/config
Scala  | `class GetGConfig`

## Patch course

Makes a course public or private.

Request property | Spec
---|---
Action | PATCH /courses/[`courseId`](models.md#id-types)
Body model | { "public": `true` or `false` }
Scala  | `class PatchCourse`

## "Partially overwrite" course

The request body contains a full or partial course JSON.
In that course JSON, all lesson IDs and gadget instance IDs must be distinct.

Any new metadata will be merged into the existing course metadata.
For example:
If `lessons` exist in the request body, all the previous lesson and gadget data will be replaced by the new lesson and gadget data from the request body.
If `lessons` does not exist in the request body, the previous lesson and gadget data will remain unchanged.
The same applies to other fields such as `tags`, etc.

The course will be updated in sandbox but the published revision (if it exists) will remain the same.

Request property | Spec
---|---
Action | PUT /courses/[`courseId`](models.md#id-types)
Body model | [Course details](models.md#course-details) without `palette` and `catalogs`, and with all top-level fields optional.
Scala  | `class PartiallyOverwriteCourse`

Status | Response body spec
---|---
200 | {}
400 | `{"error": 400, "message": "Lesson IDs and gadget instance IDs must be unique within course"}` <br> If the given course JSON contains any duplicate lesson IDs or gadget instance IDs.
403 | `{"error": 403, "message": "Locked gadget instance IDs: 1, 2, ..., n"}` <br> If some gadgets in the course are locked by another user.


## Delete course
Calling session must be a partner key or user. Calling session must have `ArchivePermission` on the course via
direct grant or VFO administrator status.


Request property | Spec
---|---
Action | DELETE /courses/[`courseId`](models.md#id-types)
Scala  | `class DeleteCourse`

Status | Response body spec
204 |
403 | `{"error": 403, "message": "Locked gadget instance IDs: 1, 2, ..., n"}` <br> If some gadgets in the course are locked by another user.


## Undelete Course

Calling session must be a partner key or user. Calling session must have `ArchivePermission` on the course via
direct grant or VFO administrator status.

Request property | Spec
---|---
Action | PUT /courses/[`courseId`](models.md#id-types)/restore
Scala  | `class RestoreCourse`

Status | Response body spec
200 | `{}`
40x | `{"error": 40x, "message": "Varies"}`

## Get course index

**DEPRECATED**

Request property | Spec
---|---
Action | ~~GET /courses/[`courseId`](models.md#id-types)/index~~
Scala  | `class GetCourseIndex`

## Get lessons

Request property | Spec
---|---
Action | GET /courses/[`courseId`](models.md#id-types)/lessons
Scala  | `class GetLessons`

## Create lesson

Adds a lesson at the end of the course.

Request body contains a lesson JSON.

Request property | Spec
---|---
Action | POST /courses/[`courseId`](models.md#id-types)/lessons
Scala  | `class AddLesson`

## Update lesson metadata

Request property | Spec
---|---
Action | PUT /courses/[`courseId`](models.md#id-types)/lessons/[`lessonId`](models.md#id-types)
Scala  | `class ChangeLessonMetaData`

## Delete lesson

Request property | Spec
---|---
Action | DELETE /courses/[`courseId`](models.md#id-types)/lessons/[`lessonId`](models.md#id-types)
Scala  | `class DeleteLesson`

Status | Response body spec
204 |
403 | `{"error": 403, "message": "Locked gadget instance IDs: 1, 2, ..., n"}` <br> If some gadgets in the course are locked by another user.

## Reorder lessons

**DEPRECATED**

Request property | Spec
---|---
Action | ~~PUT /courses/[`courseId`](models.md#id-types)/lessons/order~~
Body model | A JSON list of lesson IDs as strings.
Scala  | `class ReorderLessons`

**Warning**: If some existing lesson IDs are missing in the body of this request, these lessons will be deleted from the course.

Status | Response body spec
---|---
200 | {}
400 | "Invalid ordering"

## Get gadget instances

Request property | Spec
---|---
Action | GET /courses/[`courseId`](models.md#id-types)/lessons/[`lessonId`](models.md#id-types)/gadgets
Scala  | `class GetGadgets`

## Reorder gadget instances

**DEPRECATED**

Request property | Spec
---|---
Action | ~~PUT /courses/[`courseId`](models.md#id-types)/lessons/[`lessonId`](models.md#id-types)/gadgets/order~~
Body model | A JSON list of lesson IDs as strings.
Scala  | `class ReorderGadgets`

Status | Response body spec
---|---
200 | {}
400 | "Invalid ordering"
403 | `{"error": 403, "message": "Locked gadget instance IDs: 1, 2, ..., n"}` <br> If some gadgets in the course are locked by another user.

## Get course completion

Request property | Spec
---|---
Action | GET /courses/[`courseId`](models.md#id-types)/completion
Scala  | `class GetCompletion`

## Update course completion

Request property | Spec
---|---
Action | PUT /courses/[`courseId`](models.md#id-types)/completion
Scala  | `class CourseCompletion`

## Get course progress

Permissions: Any authenticated user or partner key.

Request property | Spec
---|---
Action | GET /courses/[`courseId`](models.md#id-types)/progress
Scala  | `class GetProgress`

## Get course overview

Request property | Spec
---|---
Action | GET /courses/[`courseId`](models.md#id-types)/overview
Scala  | `class GetOverview`

## Get all gadget user states

Permissions: must be the same user, a course creator (publisher), or partner.

Request property | Spec
---|---
Action | GET /courses/[`courseId`](models.md#id-types)/users/[`userId`](models.md#id-types)/userstates
Scala  | `class GetCourseUserStates`

Status | Response body spec
---|---
200 | [course userstate response](responses.md#course-userstate-response)
401 | [Invalid credentials](responses.md#invalid-credentials)

## Get gadget grades

For a quiz-like gadget, returns the grades for all users that have a userstate for this gadget.

Endpoint can be used by partner key or by course creator (publisher) or by a course author (contributor).

Specifying `lessons/:lessonId` in the request URL is deprecated.

Request property | Spec
---|---
Action | ~~GET /courses/[`courseId`](models.md#id-types)/lessons/[`lessonId`](models.md#id-types)/gadgets/[`gadgetId`](models.md#id-types)/grades~~
Action | GET /courses/[`courseId`](models.md#id-types)/gadgets/[`gadgetId`](models.md#id-types)/grades
Scala  | `class GetGadgetGrades`

Status | Response body spec
---|---
200 | [paginated](pagination.md) array of [gadget grade response](models.md#gadget-grade-response)
403 | [Course is not staged](responses.md#course-is-not-staged)
401 | [Invalid credentials](responses.md#invalid-credentials)

## Drop course

Delete the course learner data about the SID user (make it as if the user has not started the course).

Requires a user SID (not partner key).

Request property | Spec
---|---
Action | DELETE /courses/[`courseId`](models.md#id-types)/start
Scala  | `class DropCourse`

## Drop course

Delete the course learner data about the specified user `userId` (make it as if the user has not started the course).

Requires the session user to have permission `PublishCourses` on the course.

Request property | Spec
---|---
Action | DELETE /courses/[`courseId`](models.md#id-types)/users/[`userId`](models.md#id-types)/start
Scala  | `class DropCourseForUser`

## Get gadget user state

Specifying `lessons/:lessonId` in the request URL is deprecated.

Request property | Spec
---|---
Action | ~~GET /courses/[`courseId`](models.md#id-types)/lessons/[`lessonId`](models.md#id-types)/gadgets/[`gadgetId`](models.md#id-types)/userstate~~
Action | GET /courses/[`courseId`](models.md#id-types)/gadgets/[`gadgetId`](models.md#id-types)/userstate
Scala  | `class GetGuState`

## Get gadget user states

Specifying `lessons/:lessonId` in the request URL is deprecated.

Request property | Spec
---|---
Action | ~~GET /courses/[`courseId`](models.md#id-types)/lessons/[`lessonId`](models.md#id-types)/gadgets/[`gadgetId`](models.md#id-types)/userstates~~
Action | GET /courses/[`courseId`](models.md#id-types)/gadgets/[`gadgetId`](models.md#id-types)/userstates
Scala  | `class GetGuStates`

## Delist from labs

**DEPRECATED**

Request property | Spec
---|---
Action | ~~POST /courses/[`courseId`](models.md#id-types)/delist/labs~~
Scala  | `class DelistFromLabs`

## Unpublish course

**DEPRECATED**

Request property | Spec
---|---
Action | ~~POST /courses/[`courseId`](models.md#id-types)/unpublish/labs~~
Scala  | `class DelistFromLabs`

## ~~Stage course~~

**DELETED**

~~An old endpoint for publishing a course.~~
Superceded by POST [/catalogs/staged/courses](catalogs.md#publish-course)

Request property | Spec
---|---
Action | ~~POST /courses/[`courseId`](models.md#id-types)/stage~~
Scala  | `class StageCourse`

## Replace course brand image

The session user should have `PublishCourses` permission on the course, or be an org admin.

Request property | Spec
---|---
Action | POST /courses/[`courseId`](models.md#id-types)/coursebrandimage
Scala  | `class ReplaceCourseBrandImage`

## Delete course brand image

The session user should have `PublishCourses` permission on the course, or be an org admin.

Request property | Spec
---|---
Action | DELETE /courses/[`courseId`](models.md#id-types)/coursebrandimage
Scala  | `class DeleteCourseBrandImage`

## Replace course cover image

The session user should have `PublishCourses` permission on the course, or be an org admin.

Request property | Spec
---|---
Action | POST /courses/[`courseId`](models.md#id-types)/coverimage
Scala  | `class ReplaceCoverImage`

## Delete course cover image

The session user should have `PublishCourses` permission on the course, or be an org admin.

Request property | Spec
---|---
Action | DELETE /courses/[`courseId`](models.md#id-types)/coverimage
Scala  | `class DeleteCoverImage`


## Delete course cover color

The session user should have `PublishCourses` permission on the course, or be an org admin.

Request property | Spec
---|---
Action | DELETE /courses/[`courseId`](models.md#id-types)/covercolor
Scala  | `class DeleteCoverColor`

## Set course settings

In order to update course settings, the caller must be a user session and at least one of the following conditions must hold:

- the caller has the permission `PublishCourses` for the course
- the course is shared on a VFO org, and the caller has `AdministerOrg` permission in that org

Request property | Spec
---|---
Action | PUT /courses/[`courseId`](models.md#id-types)/settings
Body model | Any valid JSON object (models.md#object)
Scala  | `class SetCourseSettings`

Status | Response body spec
---|---
200 | `{}`
401 | [Invalid credentials](responses.md#invalid-credentials)
403 | `{"error": 403, "message": "Insufficient permissions"}` <br> If `SID` does not have `PublishCourses` permission for the course, nor `AdministerOrg` permission in the org.
404 | `{"error": 404, "message": "Course '$courseKey' not found"}` <br> If `courseKey` does not identify a course.

## Create course revision

In order to be able to publish a course, at least one of the following conditions must hold:

- the caller has the permission `PublishCourses` for the course
- the course belongs to a legacy org, and the caller has either `Admin` or `Author` role in that org
- the course is shared on a VFO org, and the caller has `AdministerOrg` permission in that org

Request property | Spec
---|---
Action | POST /courses/[`courseId`](models.md#id-types)/revisions
Body model | empty
Scala  | `class AddCourseRevision`

Status | Response body spec
---|---
201 | [Course revision data](models.md#course-revision-data)

## Revert course edits

Request property | Spec
---|---
Action | PUT /courses/[`courseId`](models.md#id-types)/head
Scala  | `class ReplaceCourseHead`

## Get all tracked users in the course

Endpoint can be used by partner key or by course creator (publisher) or by a course author (contributor).

Results are produced for the latest published version of the course. If no published revision exists, an error is returned.

Request property | Spec
---|---
Action | GET /courses/[`courseId`](models.md#id-types)/users?tracked=true
`name` param | text field to search in the display name
Scala  | `class GetCourseUsers`

Note: the parameter `tracked=true` is mandatory. (See `class GetCourseUsers` in `CourseEndpoints.scala`.)

Status | Response body spec
---|---
200 | [paginated](pagination.md) array of [course user response](models.md#course-user-response)
401 | [Invalid credentials](responses.md#invalid-credentials)
400 | [Bad request](responses.md#bad-request) also when `tracked=true` is omitted
403 | [Course is not staged](responses.md#course-is-not-staged)

Each gadget object now has a `userState` with a `pass` value. This value is computed and normally not originally present in the gadget's user state.

## Get gadget user data

**DEPRECATED**

Request property | Spec
---|---
Action | ~~GET /courses/[`courseId`](models.md#id-types)/lessons/[`lessonId`](models.md#id-types)/gadgets/[`gadgetId`](models.md#id-types)/userdata~~
Action | ~~GET /courses/[`courseId`](models.md#id-types)/gadgets/[`gadgetId`](models.md#id-types)/userdata~~
Scala  | `class GetGadgetUserdata`

## Get course user data

Endpoint can be used by partner key or by course creator (publisher) or by a course author (contributor).

Results are produced for the latest published version of the course. If no published revision exists, an error is returned.

Request property | Spec
---|---
Action | GET /courses/[`courseId`](models.md#id-types)/userdata
`userIds` param  | Restricts the results to users given by a comma-separated list of [`userId`](models.md#id-types)
Scala  | `class GetCourseUserdata`

Status | Response body spec
---|---
200 | [paginated](pagination.md) array of [course user data response](models.md#course-user-data-response)
403 | [Course is not staged](responses.md#course-is-not-staged)

## Get course user

Endpoint can be used by partner key or by course creator (publisher) or by a course author (contributor).

Results are produced for the latest published version of the course. If no published revision exists, an error is returned.

Request property | Spec
---|---
Action | GET /courses/[`courseId`](models.md#id-types)/users/[`userId`](models.md#id-types)
Scala  | `class GetCourseUser`

Status | Response body spec
---|---
200 | [course user response](models.md#course-user-response)
403 | [Course is not staged](responses.md#course-is-not-staged)

## Patch course user

This endpoint has exactly the same implemented behavior as PUT with the same URL (see below).

Request property | Spec
---|---
Action | PATCH /courses/[`courseId`](models.md#id-types)/users/[`userId`](models.md#id-types)
Scala  | `class UpdateCourseUser`

## Update properties of course user

For a given course, set tracked state of given user, and set user's role for the course.
Supported roles are `author`, `contributor`, `publisher`. Currently `author` and `contributor` are the same role.

To be able to set tracked state, the requesting user must have a `TrackLearners` permission for the course,
or (if the course belongs to a VFO org) have the VFO `TeachCourses` permission in that org.

The requesting user is allowed to update course roles only if condition (1) or (2) holds:

1. The user has `ManageAllAuthoringInvitationsAndPermissions` permission on the course.
2. The user has `InsertConfigureDeleteYourOwnGadgetInstances`, is asking to change *their own* permissions (not another user's),
and is not asking to set `ManageAllAuthoringInvitationsAndPermissions` (the "publisher" role).

Also, it is not permitted to remove the sole author of the course from the author role.

Request property | Spec
---|---
Action | PUT /courses/[`courseId`](models.md#id-types)/users/[`userId`](models.md#id-types)
Body model | [Course user update request](models.md#course-user-update-request)
Scala  | `class UpdateCourseUser`

Status | Response body spec
---|---
200 | [course user update response](models.md#course-user-update-response)
403 | `{"error": 403, "message": "Insufficient permissions"}` <br> If none of the above conditions are met.
403 | `{"error": 403, "message": "Cannot remove the only course author"}` <br> If requesting to remove the `author` role from the only remaining author.
404| "Role[s] not valid" if some role names are not one of `"author"`, `"contributor"`, `"publisher"`

## Get full course data and user progress

Request property | Spec
---|---
Action | GET /courses/[`courseId`](models.md#id-types)/users/[`userId`](models.md#id-types)/state
Scala  | `class GetFullCourseUserProgressData`

## Delete course state

Request property | Spec
---|---
Action | DELETE /courses/[`courseId`](models.md#id-types)/users/[`userId`](models.md#id-types)/state
Scala  | `class DeleteCourseState`

## Get cumulative user grade for course

Endpoint can be used by partner key or by course creator (publisher).

Results are produced for the latest published version of the course. If no published revision exists, an error is returned.

Request property | Spec
---|---
Action | GET /courses/[`courseId`](models.md#id-types)/users/[`userId`](models.md#id-types)/grade
Scala  | `class GetCourseUserGrade`

Status | Response body spec
---|---
200 | `{ cumulativeGrade: 0.0 }`
403 | [Course is not staged](responses.md#course-is-not-staged)
401 | [Invalid credentials](responses.md#invalid-credentials)

For the definition of "cumulative grade", see [models.md#cumulative-grade].

## Get user's quiz grades for course

Endpoint can be used by partner key or by course creator (publisher).

Results are produced for the latest published version of the course. If no published revision exists, an error is returned.

Request property | Spec
---|---
Action | GET /courses/[`courseId`](models.md#id-types)/users/[`userId`](models.md#id-types)/grades
Scala  | `class GetCourseUserGrade`

Status | Response body spec
---|---
200 | `{ cumulativeGrade: 0.0, gadgets: array of [course user quiz grade response](models.md#course-user-quiz-grade-response)}`
403 | [Course is not staged](responses.md#course-is-not-staged)
401 | [Invalid credentials](responses.md#invalid-credentials)

For the definition of "cumulative grade", see [models.md#cumulative-grade].

## Get cumulative grade statistics for course

Endpoint can be used only by course creator (publisher).

Results are produced for the latest published version of the course. If no published revision exists, an error is returned.

Request property | Spec
---|---
Action | GET /courses/[`courseId`](models.md#id-types)/grade_statistics
Scala  | `class GetCourseStatistics`

Status | Response body spec
---|---
200 | [grade statistics response](models.md#course-grade-statistics-response)
403 | [Course is not staged](responses.md#course-is-not-staged)
401 | [Invalid credentials](responses.md#invalid-credentials)


## Learner Class Decoration

## Create learner class

Request property | Spec
---|---
Action | POST /courses/[`courseId`](models.md#id-types)/users/[`userId`](models.md#id-types)/class
Body model | [learnerClass](models.md#learner-class)
Scala  | `object AddCohort`

## Get learner class

Request property | Spec
---|---
Action | GET /courses/[`courseId`](models.md#id-types)/users/[`userId`](models.md#id-types)/class
Scala  | `class GetCohort`

## Update learner class

Request property | Spec
---|---
Action | PUT /courses/[`courseId`](models.md#id-types)/users/[`userId`](models.md#id-types)/class
Body model | [learnerClass](models.md#learner-class)
Scala  | `class UpdateCohort`

## Patch learner class

This endpoint has exactly the same implemented behavior as PUT with the same URL (see above).

Request property | Spec
---|---
Action | PATCH /courses/[`courseId`](models.md#id-types)/users/[`userId`](models.md#id-types)/class
Body model | [learnerClass](models.md#learner-class)
Scala  | `class UpdateCohort`

## Delete LearnerClass

Request property | Spec
---|---
Action | DELETE /courses/[`courseId`](models.md#id-types)/users/[`userId`](models.md#id-types)/class
Scala  | `class DeleteCohort`

## Learners Report

In order to facilitate the front end, a GET method is needed to download reports.
An download token that will expire in a configured time (60 seconds recommended) is sent back.
To get the report, before the token expires the caller has to call `GET http(s)://URL_BASE/api2/reports/downloads?token=token-value` with no SID.
The caller can check if report is ready to download by calling `GET http(s)://URL_BASE/api2/reports/downloadstatus?token=token-value`

## Start generating report

Request property | Spec
---|---
Action | POST /reports/learnersdownload
Body model | [learnersReport](models.md#learners-report)
Scala | `class GenerateLearnerReport`

Status | Response body spec
---|---
200 | A download token value in the form `{"downloadToken":"token-value"}`
400 | `{"error": 400, "message":"One and only one course must be specified"}` if zero or more than one course specified
403 | `{"error": 403, "message": "Insufficient permissions"}` If SID does not have the `ViewCourseAnalytics` permission
404 | `{"error": 404, "message": "Course '$courseKey' not found"}` If `courseKey` does not identify a course.
413 | `Data set too large` if requested report will be too large (it is configured by `reporting.max-records-per-report` parameter)

## Check report status

Request property | Spec
---|---
Action | GET /reports/downloadstatus?token=token-value
Scala | `class GetReportStatus`

Request property | Spec
---|---
200 | Report status `{"status":"status_value"}` possible status_value are: `incomplete`, `failure`, `ready`
404 | `Token 'token-value' not found`

## Download report

Request property | Spec
---|---
Action | GET /reports/downloads?token=token-value
Scala | `class GetLearnerReport`

Request property | Spec
---|---
200 | Report content
404 | `Token 'token-value' not found`

## Update user group access

Request property | Spec
---|---
Action | PUT /courses/[`courseId`](models.md#id-types)/usergroups/[`groupId`](models.md#id-types)
Permissions | Session must have the ```ManageAllAuthoringInvitationsAndPermissions``` permission in the program.
Body model | ```{"role":"role_name"}```
Scala  | `class ReplaceUserGroupCourseAccess`


Status | Response body spec
---|---
200 | 
400 | `{"error": 400, "message": "User groups are not enabled"}`<br> if feature flag `enableUserGroups` not enabled
400 | `{"error": 400, "message": "Invalid role '...'"}` <br> if invalid role provided
400 | `{"error": 400, "message": "Invalid user group ID specified : '...'"}` <br> if provided user group id is not numeric
403 | `{"error": 403, "message": "Insufficient permissions"}`
404 | `{"error": 404, "message": "Course '...' not found"}` <br> If `courseId` not found
404 | `{"error": 404, "message": "User group '...' not found"}` <br> If `groupId` not found

## Revoke user group access

Request property | Spec
---|---
Action | DELETE /courses/[`courseId`](models.md#id-types)/usergroups/[`groupId`](models.md#id-types)
Permissions | Session must be associated with a user ID. The user must have 
            | the `ManageAllAuthoringInvitationsAndPermissions` permission in the program.
Scala  | `class RevokeUserGroupCourseAccess`


Status | Response body spec
---|---
200 | 
400 | `{"error": 400, "message": "User groups are not enabled"}`<br> if feature flag `enableUserGroups` not enabled
400 | `{"error": 400, "message": "Invalid role '...'"}` <br> if invalid role provided
400 | `{"error": 400, "message": "Invalid user group ID specified : '...'"}` <br> if provided user group id is not numeric
403 | `{"error": 403, "message": "Insufficient permissions"}`
404 | `{"error": 404, "message": "Course '...' not found"}` <br> If `courseId` not found
404 | `{"error": 404, "message": "User group '...' not found"}` <br> If `groupId` not found

## Create SCORM key
Request property | Spec
---|---
Action | POST /courses/[`courseId`](models.md#id-types)/scorm_keys
Permissions | Caller must be a Partner Key or an author
Scala  | `class AddScormKey`


Status | Response body spec
---|---
201 | `{key: UUID, containerId: VFOOrgId, courseKey: CourseKey, maxUsers: Int}` 
400 | `{"error": 400, "message": "Not a VFO course"}`<br> if course is not part of a VFO container
401 | `{"error": 401, "message": "Invalid credentials"}`
403 | `{"error": 403, "message": "Insufficient permissions"}`
404 | `{"error": 404, "message": "Course '...' not found"}` <br> If `courseId` not found

## Create SCORM session
Request property | Spec
---|---
Action | POST /courses/[`courseId`](models.md#id-types)/scorm_sessions
Permissions | Caller must be a SCORM key
Body model | `{"learnerId": String (optional), "learnerName": String (optional)}`
Scala  | `class AddScormSession`


Status | Response body spec
---|---
201 | `{key: UUID, containerId: VFOOrgId, courseKey: CourseKey, userId: UserId}` 
401 | `{"error": 401, "message": "Invalid credentials"}`
403 | `{"error": 401, "message": "Invalid VFO credentials"}`
403 | `{"error": 403, "message": "Anonymous users are not allowed for private courses"}`<br> If course is private and no learner identifiers are given
404 | `{"error": 404, "message": "Course '...' not found"}` <br> If `courseId` not found

## Delete SCORM key
Request property | Spec
---|---
Action | DELETE /courses/[`courseId`](models.md#id-types)/scorm_keys/[`key`](models.md#uuid)
Permissions | Caller must be a Partner Key or an author
Body model |
Scala  | `class DeleteScormKey`


Status | Response body spec
---|---
200 |  
401 | `{"error": 401, "message": "Invalid credentials"}`
403 | `{"error": 401, "message": "Invalid VFO credentials"}`
403 | `{"error": 403, "message": "Insufficient permissions"}`
404 | `{"error": 404, "message": "Course '...' not found"}` <br> If `courseId` not found
