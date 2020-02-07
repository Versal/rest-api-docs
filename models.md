# Types and request/response models

## Primitive types

### object

The JSON `object` type.

### string

The JSON `string` type.

### number

The JSON `number` type.

### integer

A [number](#number) that does not have a fractional part.

### boolean

One of the JSON values `true` or `false`.


### date

A [string](#string) with a date in ISO 8601 format.

### date-time

A [string](#string) containing a date and time in ISO 8601 format.

### slug

A [string](#string) containing only the following ASCII characters:
- Uppercase letters (`A`-`Z`)
- Lowercase letters (`a`-`z`)
- Digits (`0`-`9`)
- `+`, `-`, or `_`

### email

A [string](#string) containing an email address. Our (non-standard) definition of an email
address is given by the following grammar:
- **email** ::= **email-char**+ `@` **email-char**+
- **email-char** ::= any character other than the following:
    - `@` (at-sign)
    - ` ` (space)
    - `\t` (horizontal tab)
    - `\n` (new line)
    - `\v` (vertical tab)
    - `\f` (form feed)
    - `\r` (carriage return)

### org role

A [string](#string) having one of the following values:
- `admin`
- `instructor`
- `learner`

### org status

A [string](#string) having one of the following values:
- `TRIAL`
- `ACTIVE`
- `EXPIRED`

This field indicates whether a VFO container is a trial, a paid org (active) or an expired trial. All orgs
start out as trials. 

### org type

A [string](#string) having one of the following values:
- `container`
- `base`
- `portal`
- `topic`

### location data

Field | Type | Description
------|------|------------
`streetAddress` | [string](#string) | House number and street.
`extendedAddress` | [string](#string) | (Optional) A second line in the address.
`locality`     | [string](#string)  | Name of the town, city, or other locality.
`region`      | [string](#string)  | US state (e.g. "California") or other regional division within country.
`postalCode` | [string](#string)  | US or other country postal code.
`countryName`   | [string](#string)  | E.g. "USA" or other country name.

### uuid

A [string](#string) containing a UUID produced by calling the Java method [`java.util.UUID.randomUUID`](http://docs.oracle.com/javase/7/docs/api/java/util/UUID.html#randomUUID()).

### gadget type

A [string](#string) in the format `$devKey/$name`, with an optional suffix `@$version`, where `devKey` is
the [user key](#user-key) of the gadget developer, `name` is the [slug](#slug)-format name of the gadget,
and `version` is the [gadget version](#gadget-version).

### gadget version

A [string](#string) in the format `$major.$minor.$patch`, where `major`, `minor`, and `patch` are
non-negative integers.

### gadget catalog

A [string](#string) having one of the following values:
- `sandbox`: The gadget has been uploaded, and can be used in courses, but those courses cannot be published.
- `approved`: The gadget is approved for use in published courses.
- `rejected`: The gadget is rejected for use in published (or any) courses, and will not appear to authors.
- `deprecated`: A newer version of a gadget with the same developer and name has been uploaded and approved.

## user key

A [string](#string) that is either a username (in [slug](#slug) format) or a [user ID](#id-types).

## course key

A [string](#string) that is either a [course ID](#id-types) or encodes a 64-bit signed integer, which is the
"internal" database ID of the course. The internal ID is still in use for historical reasons, but should
eventually be phased out.

## ID types

To be valid, a value of an ID type must denote an existing object.

ID type | Base type         | Additional constraints
--------|-------------------|------------
asset                | [uuid](#uuid)     | None.
asset representation | [uuid](#uuid)     | None.
bundle id            | [string](#string) | Parseable as a 64-bit signed integer.
course               | [string](#string) | An arbitrary sequence of lowercase letters (a-z) and digits (0-9). Minimum length of six characters. The intent is for the IDs to be non-sequential and therefore hard to guess.
course instance      | [string](#string) | Parseable as a 64-bit signed integer.
gadget instance      | [string](#string) | None.
gadget manifest      | [string](#string) | Parseable as a 64-bit signed integer.
lesson               | [string](#string) | None.
org                  | [string](#string) | Parseable as a 64-bit signed integer
program              | [string](#string) | Parseable as a 64-bit signed integer.
session              | [uuid](#uuid)     | None.
tray label           | [string](#string) | None.
user                 | [string](#string) | Parseable as a 64-bit signed integer.
lock                 | [string](#string) | Parseable as a 64-bit signed integer.
userGroupId          | [string](#string) | Parseable as a 64-bit signed integer.

## Models

When JSON object models are supplied in request bodies, it should be assumed that all specified fields are required, and all unspecified fields are ignored. Any differences from the default behavior should be documented in each endpoint. For JSON object models supplied in response bodies, each field will be provided unless it is undefined, again with execeptions from the rule documented on a per-endpoint basis.

### Address

Field | Type | Description
------|------|------------
`streetAddress`   | [string](#string) | Number and street
`extendedAddress` | [string](#string) | Apartment, suite, floor, etc.
`locality`        | [string](#string) | City, town, village, etc.
`region`          | [string](#string) | State, county, province, etc.
`postalCode`      | [string](#string) | e.g. ZIP code
`countryName`     | [string](#string) | e.g. `United States of America` or `USA`

### Asset

Field | Type | Description
------|------|------------
`id`              | [asset ID](#id-types)                                  | Unique API-generated identifier.
`title`           | [string](#string)                                      | Asset title.
`tags`            | array of [string](#string)                             | A sorted sequence of labels, intended to describe the asset and be useful for categorizing it or finding it in a search.
`representations` | array of [asset representation](#asset-representation) | Contains metadata about each specific media file that can be used to display the asset.

### Asset representation

Field | Type | Description
------|------|------------
`id`          | [asset representation ID](#id-types) | Unique API-generated identifier.
`location`    | [string](#string)                    | The path under which the representation's content can be found in storage.
`contentType` | [string](#content-type)              | The [Internet media type](http://en.wikipedia.org/wiki/Internet_media_type) of the representation.
`original`    | [bool](#bool)                        | `true` if the representation was uploaded by an API client; `false` if the representation was generated from the original.
`scale`       | [string](#string)                    | The height and width of the representation in pixels, e.g. `"640x480"`.
`available`   | [bool](#bool)                        | `true` if the representation is available at `location` in storage; `false` if is not (yet) available; and omitted if it will never be available (e.g., because video transcoding was disabled when the asset was uploaded).

### Authenticator access
Field | Type | Description
------|------|------------
`authenticator` | [authenticator ID](#integer)  | Unique API-generated identifier
`role`          | [program role](#org-role)     | Authenticator's role within a program

### Billing contact

Field | Type | Description
---|---|---
`address` | [address](#address) | The billing address.
`name`    | [string](#string)   | Name of the person or entity being billed.
`phone`   | [string](#string)   | Contact phone number.
`email`   | [string](#string)   | Contact email address.

### Command rejection

A JSON object of the form `{"$commandId": {"_1": $status, "_2": "$message"}}`, where
- `commandId` is the ID of the command that was rejected;
- `status` is the HTTP status code of the response;
- `message` is the error message provided with the response.

### Base course

Field | Type | Description
---|---|---
`authorBio`  | [string](#string)                              | A description of the author(s) of the course. Omitted if not defined.
`catalogs`   | array of [string](#string)                     | Always contains `sandbox`, because the course is always editable. Will also contain `labs` if the course has been published.
`courseId`   | [course ID](#id-types)                         | Duplicate of `id`.
`coverImage` | [asset](#asset)                                | An image representing the course. Omitted if the course does not have a cover image.
`createdAt`  | [date-time](#date-time)                        | The creation time of the course.
`currentPosition`  | [progress](#progress)                    | Information on the user's progress in the course. If `null`, the user does not have any progress (i.e., they have not started the course).
`duration`   | [string](#duration)                            | Legacy field defining the number of seconds a session of this course would last, formatted as an integer.
`id`         | [course ID](#id-types)                         | Unique, API-generated identifier.
`isEditable` | [boolean](#boolean)                            | Has value `true` if the requesting user can edit the course; `false` otherwise.
`longDesc`   | [string](#string)                              | A paragraph or longer description of the course. Omitted if not defined.
`org`        | [org](#org)                                    | Information about the course's org, if it belongs to one. Otherwise omitted.
`palette`    | array of [gadget manifest](#gadget-manifest)   | The manifests of all gadget instances in the course.
`programId`  | [program ID](#id-types)                        | The ID of the program the course belongs to.
`public`     | [boolean](#boolean)                            | Whether or not the course is in a public program.
`revision`   | [course revision](#course-revision)            | Omitted if the course state does not correspond to a saved revision. Has value `null` if the course sandbox is published (legacy publishing). Otherwise, the value is an object describing what revision the course state corresponds to.
`shortDesc`  | [string](#string)                              | A summary statement of the course. Omitted if not defined.
`status`     | [string](#string)                              | Has the value `complete` if the requesting user has finished the course. Omitted otherwise.
`tags`       | array of [string](#string)                     | Tags associated with the course, ordered lexicographically.
`title`      | [string](#string)                              | The title of the course. Omitted if the course doesn't have a title.
`theme`      | [theme revision](#theme-revision)              | The theme revision assigned for the course. Optional. 
`themeId`    | [theme ID](#id-types)                          | The ID of theme to be assigned for the course. Optional. 
`users`      | array of [course publisher](#course-publisher) | The users with publishing rights for the course.
`vfo`        | array of [response org](#response-org)         | The list of VFO orgs where the course is shared (possibly, an empty list).
`isBlendedCover` | [boolean](#boolean) |
`coverColor` | [string](#string) | html color value: from `#000` to `#fff` and from `#000000` to `#ffffff`, case insensitive
`containerId` | [org ID](#id-types) | VFO container Id for a course, if it belongs to one
`settings`   | [object](#object)                              | Any valid JSON object.

### Course details

A [base course](#base-course) extended with the following fields:

Field | Type | Description
---|---|---
`assetUrlTemplate` | [string](#string)          | Provides a template which, when an [asset ID](#id-types) is substituted into it, produces a URL where that asset's content can be retrieved. This allows clients to not have to know where assets are stored; they can determine the location using this field.
`isTracked`        | [boolean](#boolean)        | Whether or not the user is tracked in the course's program.
`isBlocked`        | [boolean](#boolean)        | Whether or not completion of the course is blocked, e.g., by a quiz that must be passed to proceed.
`lessons`          | array of [lesson](#lesson) | The lessons of the course, in order.
`lastPublished`    | [date-time](#date-time)    | Timestamp of the recent revision publishing.

### Course response

A [base course](#base-course) extended with the following fields:

Field | Type | Description
------|------|------------
`authors` | array of [course author](#course-author) | Information on each author of the course.

### Course author

Field | Type | Description
---|---|---
`id` | [user ID](#id-types) | The user ID of the course author.

### Course publisher

A [response user](#response-user) object with only the fields `username`, `fn`, `fullName`, `image`, and
the following extra field:

Field | Type | Description
---|---|---
`permissions` | array of [string](#string) | An array, either empty or containing the single element `PublishCourses`.

### Theme revision

JSON object representation of the most recent revision for specific theme ID.

Field | Type | Description
---|---|---
`themeId`    | [theme ID](#id-types)          | Theme ID  
`revisionId` | [theme revision ID](#id-types) | Revision ID. Most recent for specific theme ID.

### Course revision

Field | Type | Description
---|---|---
`createdAt`  | [string](#string)               | The date and time when the course revision was created, as specified by [`java.sql.Timestamp.toString()`](http://docs.oracle.com/javase/7/docs/api/java/sql/Timestamp.html#toString()).
`revisionId` | [course instance ID](#id-types) | Unique, API-generated identifier.

## Course revision data

Field | Type | Description
---|---|---
`courseId`  | [course ID](#id-types)  | A new revision was created for this course.
`revisionId` | [course instance ID](#id-types) | Unique, API-generated identifier for the new course revision.

### Course grade statistics response

Field | Type | Description
---|---|---
`total_learners`  | [integer](#integer) | Total number of all tracked learners in the course.
`avg_score`  | [number](#number) | Average [cumulative grade](#cumulative-grade) among all tracked learners who completed the course, between 0 and 1.
`quizzes` | array of [course grade statistics quiz response](#course-grade-statistics-quiz-response) | Average scores for each quiz-like gadgets.

### Course grade statistics quiz response

Field | Type | Description
---|---|---
`name`  | [boolean](#boolean) | Name of the quiz-like gadget.
`title` | [string](#string) | The title of the quiz-like gadget. (The `title` attribute of the gadget's config.)
`learners`  | [integer](#integer) | The number of tracked learners who submitted any responses for this gadget.
`avg_score` | [number](#number) | Average score, between 0 and 1, over all tracked learners who submitted any responses to this gadget.

### Course user quiz grade response

Field | Type | Description
---|---|---
`id`   |  [string](#string) | Gadget instance ID in the course.
`name`  | [boolean](#boolean) | Name of the quiz-like gadget.
`title` | [string](#string) | The title of the quiz-like gadget. (The `title` attribute of the gadget's config.)
`score` | [number](#number) | Score, between 0 and 1, computed for this gadget.

### Course user data response

Field | Type | Description
---|---|---
`id` | [user ID](#id-types) | User ID
`email` | [string](#string) | User's email
`title` | [string](#string) | Title of the course
`lastActivity` | [date-time](#date-time) | Timestamp of last activity of the user in the course
`courseCompleted` | [boolean](#boolean) | True if course was completed
`currentLessonId` | [lesson ID](#id-types) | Last visited lesson's ID
`lastSurveyCompleted` | [string](#string) | Title of the last survey completed (if applicable)

### Course user update request

Field | Type | Description
---|---|---
`tracked` | [boolean](#boolean) | (Optional) Set the user to tracked or not tracked for the given course
`roles` | Array of [string](#string) | (Optional) Set the user's roles in the given course. Allowed roles: `"author"`, `"contributor"`, `"publisher"`. One or more roles can be specified.

### Course user update response

A [Course user update request](#course-user-update-request) augmented with the following fields:

Field | Type | Description
------|------|------------
`user`          | [Response user](#response-user)                | User whose permission information was requested.

Note that the "publisher" role subsumes the "author" role, and the "author" role is listed only if "publisher" is not present.

Field | Type | Description
---|---|---
`tracked` | [boolean](#boolean) | (Optional) Set the user to tracked or not tracked for the given course
`roles` | Array of [string](#string) | (Optional) Set the user's roles in the given course. Allowed roles: `"author"`, `"contributor"`, `"publisher"`. One or more roles can be specified.

### Course progress response

Field | Type | Description
---|---|---
`completed`  | [boolean](#boolean) | Whether the user has completed the course.
`currentPosition` | [progress](#progress) | Information on the user's progress in the course. If `null`, the user does not have any progress (i.e., they have not started the course).
`cumulativeGrade` | [number](#number) | Cumulative grade of the user in the course.

### Course userstate response

Field | Type | Description
---|---|---
`currentPosition` | [progress](#progress) | Information on the user's progress in the course. If `null`, the user does not have any progress (i.e., they have not started the course).
`userstates` | map from [gadget instance ID](#id-types)s to [gadget user state](#gadget-user-state)s

### Cumulative grade

The cumulative grade is a floating-point number between 0 and 1, rounded to 2 digits after the decimal point.
This is the score achieved by the user in the course, counting all quiz-like gadgets in the course.

Quiz-like gadgets are gadgets complying with the Quiz 1.0 API.
Currently these gadgets are whitelisted in the code (see `CourseEndpoints.scala`, in the class `GetCourseStatistics`, the value `allowedGadgetNames`) as `quiz`, `am-sample-quiz` (used in tests only), `critical-reading`, `math`, `writing`.

The cumulative grade is computed by finding all quiz-like gadgets in the course, grading each one on the scale between 0 and 1 (so all quiz-like gadgets have equal weight). All these gadget scores are added and divided by the total number of quiz-like gadgets in the course. So, the cumulative grade is 1.0 only if the user has achieved 100% on *each* quiz-like gadget in the course.

If the user never submitted a quiz, the grade is 0 for that quiz. The cumulative grade is also 0 if there are no quiz-like gadgets in the course.

### Gadget grade response

This response applies to quiz-like gadgets.

Field | Type | Description
---|---|---
`user`  | {"id": [user ID](#id-types)} | The user's id
`grade` | [number](#number) | Grade for that user

### Course user response

Field | Type | Description
---|---|---
`course`  | [course progress response](#course-progress-response) | The current progress of the user in the course.
`gadgets` | array of [gadget instance response](#gadget-instance-response) | All gadgets in the course, with their userstates for this user.
`user`    | [response user](#response-user) | User information.

### Features

Field | Type | Description
---|---|---
`key`  | String | The feature key name
`isEnabled` | Boolean |
`config`    | String | Optional configuration data for the feature

Here are the features currently known to the API:
- `disassociateCourseIdWithCommandId`
- `enforceIncrementalGadgetVersions`
- `gadgetApproval`
- `restrictUpdateCourseUser`
- `superadminPutCourse`
- `usernames`
- `validateGadgetPalette`
- `autoEnrollCourses`

Use the [update features](status.md#update-features) endpoint to create a new feature name.

`autoEnrollCourses` takes a comma-separated list of course keys as its `config` value. For example: "crs123","crs456".
Other features do not use the config field.

### Gadget config

An arbitrary JSON object that defines the appearance and behavior of a gadget instance in a course. There
is no spec for it because each gadget stores different data in it.

### Base gadget instance

Field | Type | Description
---|---|---
`config`    | [gadget config](#gadget-config) | The configuration of the gadget instance.
`type`      | [gadget type](#gadget-type)     | The gadget type that the instance derives from.

## Gadget instance request

This is used to insert a new gadget into a lesson.

Contains all of the fields in [base gadget instance](#base-gadget-instance), plus the following:

Field | Type | Description
---|---|---
`index` | [integer](#integer) | (Optional) The zero-based index at which to insert the gadgets into the lesson. Default is to add to the end of lesson.

## Many gadgets request

This is used to insert many new gadgets at once into a lesson.

Field | Type | Description
---|---|---
`index` | [integer](#integer) | (Optional) The zero-based index at which to insert the gadgets into the lesson. Default is to add to the end of lesson.
`gadgets` | Array of [base gadget instance](#base-gadget-instance)

### Gadget instance response

Contains all of the fields in [base gadget instance](#base-gadget-instance),
plus the following:

Field | Type | Description
---|---|---
`id`        | [gadget instance ID](#id-types)         | Gadget instance identifier. Must be unique within the instance's lesson, but it is not required to be globally unique. Can be specified by the user in certain cases.
`userState` | [gadget user state](#gadget-user-state) | The user state of the gadget instance, for a particular user.

### Gadget manifest

Field | Type | Description
---|---|---
`author`         | [string](#string)                 | Name of the gadget's author. (Optional.)
`documentationUrl`| [string](#string)                 | URL for a tutorial document explaining the usage of the gadget. (Optional.)
`defaultConfig`  | [gadget config](#gadget-config)   | Config to use when an instance of the gadget is added to a course.
`description`    | [string](#string)                 | Description of the gadget.
`exampleUrl`     | [string](#string)                 | URL for a brief demonstration of the gadget. (Optional.)
`exec`           | [string](#string)                 | How gadget logic is executed on the server, if applicable. (Optional.)
`id`             | [gadget manifest ID](#id-types)   | Unique, API-generated identifier.
`latestVersion`  | [gadget version](#gadget-version) | The highest approved version out of all gadget manifests having the same developer user and gadget name.
`launcher`       | [string](#string)                 | Used only by the frontend. Admissible values include "iframe" and "react". (Optional.)
`name`           | [slug](#slug)                     | Name of the gadget. A developer can upload multiple gadgets related by name, but having different versions.
`noToggleSwitch` | [boolean](#boolean)               | Used only by the frontend.
`noAudioRecorder` | [boolean](#boolean)               | Used only by the frontend.
`noAudioPlayer`  | [boolean](#boolean)               | Used only by the frontend.
`private`        | array of [string](#string)        | Names of gadget config fields that should only be seen by authors, e.g. a quiz answer key. The fields will be removed from gadget configs before sending them to learners.
`responsive`     | [boolean](#boolean)               | Indicates whether the gadget supports responsive (mobile) display. Defaults to false.
`status`         | [gadget status](#gadget-status)   | Modifiable properties of the gadget.
`title`          | [string](#string)                 | Display name of the gadget.
`username`       | [string](#string)                 | The developer's username if it exists, otherwise their user ID.
`version`        | [gadget version](#gadget-version) | The version number of the gadget.
`baseUrl`        | [string](#string)                 | The base URL of the gadget project.

Note: `baseUrl` is a _computed_ field, that is, the gadget author does not need to specify it when uploading a gadget, and if specified, this field will be ignored during upload. The platform will compute this URL when fetching the gadget manifest. An example value is `/gadgets/versal/slideshow-iframe/0.2.4/`

### Gadget status

Field | Type | Description
---|---|---
`catalog` | [gadget catalog](#gadget-catalog) | The lifecycle state of the gadget.
`hidden`  | [boolean](#boolean)               | Whether or not the gadget is hidden from the user interface.

### Gadget user state

An arbitrary JSON object that stores any persistent state of a user's interaction with a gadget instance. There
is no spec for it because each gadget stores different data in it.

### Lesson

Field | Type | Description
---|---|---
`gadgets`      | array of [gadget instance response](#gadget-instance-response) | The gadget instances in the lesson, in order of appearance.
`id`           | [lesson ID](#id-types)                                         | Lesson identifier. Must be unique within the course instance, but it is not required to be globally unique. Can be specified by the user in certain cases.
`isAccessible` | [boolean](#boolean)                                            | Whether or not the lesson can be viewed, due to blocking from e.g. a quiz that must be passed to proceed. Lessons after a blocked lesson are not accessible.
`title`        | [string](#string)                                              | The lesson title.

### Org

Field | Type | Description
------|------|------------
`address` | [address](#address)                 | The mailing address of the org. Omitted if not defined.
`billing` | [billing contact](#billing-contact) | Contact information for billing the org. Omitted if not defined.
`id`      | [org ID](#id-types)                 | Unique, API-generated identifier.
`image`   | [asset](#asset)                     | Asset metadata for the image representing the org. Omitted if not defined.
`title`   | [string](#string)                   | Word or short phrase naming or describing the org. Omitted if not defined.
`orgName` | [slug](#slug)                       | A unique, human-readable user identifier. It is used in the type strings of gadgets created by the org in place of the org ID, if defined.
`users`   | array of [org member](#org-member)  | Contains one element for each user belonging to the org.

### Org member

Field | Type | Description
------|------|------------
`user`  | [user](#user)                  | The user belonging to the org.
`roles` | array of [org role](#org-role) | The user's roles in the org.

### Org member role update

Field | Type | Description
------|------|------------
`roles` | array of [org role](#org-role) | The user's roles in the org.

### Progress

Field | Type | Description
---|---|---
~~`currentLesson`~~ | [integer](#integer)     | ~~One-based index of the lesson in the most recent [progress update](courses.md#update-course-progress).~~
~~`lastLesson`~~    | [integer](#integer)     | ~~One-based index given by `currentLesson` before the most recent progress update.~~
`currentLessonId` | [string](#string)     | Lesson ID of the current lesson in the most recent [progress update](courses.md#update-course-progress).
`visitedRevisionId` | [course instance ID](#id-types) | The ID of the last visited course revision.
`maxLessonId` | [string](#string) | The ID of the highest-indexed lesson the learner has visited in the course.
`availableLessonCount` | [integer](#integer) | Total number of lessons available in the last visited course revision.
`updatedAt`     | [date-time](#date-time) | Date and time of the most recent progress update.

### Progress update

Field | Type | Description
---|---|---
~~`lessonIndex`~~ | [integer](#integer) | **DEPRECATED** One-based index of the lesson that the learner has reached. (Optional, deprecated.)
~~`currentLesson`~~ | [integer](#integer) | **DEPRECATED** One-based index of the lesson that the learner has reached. (Optional, deprecated.)
`currentLessonId` | [string](#string) | Lesson ID of the lesson that the learner has reached. (Optional until transition to new site code.)

When this data is used as request body for updating the progress information,
one or both of `lessonIndex` or `currentLessonId` may be specified. If `currentLessonId` is specified, `lessonIndex` is ignored.

When this data is used as response for fetching the progress information, all three fields will be reported (for compatibility).

### Position update

Field | Type | Description
---|---|---
`displaySection` | [slug](#slug) | Learner current page in standalone player
`currentLessonId` | [string](#string) | Lesson ID of the lesson that the learner has reached. (Required if `displaySection` value is `LESSON`)

When this data is used as request body for updating the progress information. If `displaySection` is not equal `LESSON`, `currentLessonId` is ignored.

### Session

#### Request session

Field | Type | Description
------|------|------------
`userId`        | [user ID](#id-types) | The session user.
`email`         | [email](#email)      | The email address of the session user.
~~`expiresIn`~~ | [number](#number)    | **DEPRECATED - HAS NO EFFECT** <br> The duration of the session, in milliseconds. Must be a non-negative integer.

#### Response session

Field | Type | Description
------|------|------------
`sessionId` | [session ID](#id-types)         | Unique, API-generated identifier.
`user`      | [response user](#response-user) | The owner of the session.

#### Response session information

Field | Type | Description
------|------|------------
`sessionType`          | [string](#string)    | Self-explanatory. It is one of the following values: `PartnerKey`, `InternalIntegrationKey`, `LegacyAuthenticator`, `VFOAccessKey`, `VFOUserSession`, `PlainUserSession`.
`userId`               | [user ID](#id-types) | The session user if user session. Otherwise not present.
`vfoRootOrgId`         | [org ID](#id-types)  | ID of the VFO org if VFO session. Otherwise not present.
`isVFOContainerLocked` | [boolean](#boolean)  | Self-explanatory.

### Subscription

Field | Type | Description
------|------|------------
`type` | [string](#string) | Self-explanatory. It currently has a single allowed value, `VersalPro`.

Using an object instead of a string leaves room for adding more fields in the future.

### User

#### Base user

Field | Type | Description
------|------|------------
`username`        | [slug](#slug)       | A unique, human-readable user identifier. It is used in the type strings of gadgets created by the user in place of their user ID, if defined.
`email`           | [email](#email)     | Unique email address.
`firstname`       | [string](#string)   | Given name.
`lastname`        | [string](#string)   | Family name.
`fullname`        | [string](#string)   | Full name.
`fn`              | [string](#string)   | Alternate full name.
`displayname`     | [string](#string)   | Formatted display name chosen from user's non-null name fields. </br> Display name is chosen in the following order: `fullName`, `fn`, `firstName + " " + lastName`, `firstName`, `lastName`, `"Unknown"`
`company`         | [string](#string)   | Legacy field.
`country`         | [string](#string)   | Legacy field.
`location`        | [string](#string)   | Place of residence.
`longDesc`        | [string](#string)   | A paragraph about the user.
`name`            | [string](#string)   | Legacy field.
`newsletter`      | [string](#string)   | Legacy field.
`newsletter_lang` | [string](#string)   | Legacy field.
`registered`      | [boolean](#boolean) | Legacy field.
`shortDesc`       | [string](#string)   | A brief statement about the user.
`state`           | [string](#string)   | Legacy field.
`usage`           | [string](#string)   | Legacy field.
`website`         | [string](#string)   | The URL of the user's website.
`zip`             | [string](#string)   | Legacy field.

#### Response user

A [base user](#base-user) extended with the following fields:

Field | Type | Description
------|------|------------
`id`            | [user ID](#id-types)                   | Unique API-generated identifier.
`image`         | [asset](#asset)                        | Profile image asset metadata.
`orgs`          | array of [org](#org)                   | Orgs the user belongs to.
`subscriptions` | array of [subscription](#subscription) | Subscriptions granted to the user.
`marketingRoles`| [marketing roles](#marketing-roles)    | Marketing roles of the user.

#### Marketing roles

A set of fields, which describe user roles:

Field | Type | Description
------|------|------------
`isAdmin`      | [boolean](#boolean) | If user has `AdministerOrg` permission in at least one org.
`isInstructor` | [boolean](#boolean) | If user has `TeachCourses` permission without `AdministerOrg` in at least one org.
`isLearner`    | [boolean](#boolean) | If user is enrolled in at least one course, but he is not administrator, instructor, nor course editor.

### Version

Text, not JSON. An example:

```
 project: rest-api
 version: 0.110.3
git hash: 27e80d2
   built: 10 Feb 2015 16:42:14 PST
     tag: rapi6
```

### Healthcheck

#### Healthcheck result

Field | Type | Description
------|------|------------
`passed` | [boolean](#boolean) | `true` if single check passed.
`details`| Any | Optional details of why the single check passed or failed.

#### Healthcheck summary
Field | Type | Description
------|------|------------
`passed` | [boolean](#boolean)                       | `true` if application is ready to take requests.
`results`| Map([string](#string), [healthcheck result](#healthcheck-result)) | Results of each single check.


### Custom trays

#### Custom tray request

Field | Type | Description
------|------|------------
`label`        | [slug](#slug)       | Tray ID
`title`        | [string](#string)   | Human-readable title of the tray.
`public`       | [boolean](#boolean) | (Optional, default = false) Whether the tray is public (can be read by other users).


#### Custom tray response

Field | Type | Description
------|------|------------
`title`        | [string](#string)      | Human-readable title of the tray.
`bundles`      | array of [bundle item responses](#bundle-item-response)     | An ordered collection of bundle items that exist in the tray.

#### Bundle item response

Field | Type | Description
------|------|------------
`id`        | [bundle ID](#id-types) | ID of the bundle.
`title`     | [string](#string)  | (Optional) A short title of the bundle.
`icon`      | [string](#string)  | (Optional) The bundle's icon URL.
`provider_id` | [string](#string)  | (Optional) The ID of this bundle as given by the bundle's provider.
`gadgets`   | array of [base gadget instance](#base-gadget-instance) | An ordered collection of gadget items in the bundle.
`manifests`   | array of [gadget manifest](#gadget-manifest) | A set of manifests for all gadget items in the bundle, sorted by gadget type.

#### Bundle item metadata request

Field | Type | Description
------|------|------------
`title`     | [string](#string)  | (Optional) A short title of the bundle.
`icon`      | [string](#string)  | (Optional) The bundle's icon URL.

#### Bundle item request

Field | Type | Description
------|------|------------
`index`        | [integer](#integer)      | (Optional, default = 0) Zero-based index at which a new bundle must be inserted into a tray.
`title`     | [string](#string)  | (Optional) A short title of the bundle.
`icon`      | [string](#string)  | (Optional) The bundle's icon URL.
`provider_id` | [string](#string)  | (Optional) The ID of this bundle as given by the bundle's provider.
`gadgets`   | array of [base gadget instance](#base-gadget-instance) | An ordered collection of gadget items in the new bundle.

### Versal For Organizations (VFO)

#### Base org

Field | Type | Description
------|------|------------
`name`     | [string](#string)  | (Optional) A short name of the org.
`status`   | [org status](#org-status) | (Optional) Org Status
`website`  | [string](#string) | (Optional) Website
`location` | [string](#string) | (Optional) Location
`image`    | [asset](#asset)   | (Optional) Asset metadata for the image representing the org. When creating org, the asset can contain only the asset id.
`location` | [location data](#location-data)  | (Optional) Organization's geographical location.
`website`  | [string](#string)  | (Optional) The org's website URL.

#### VFO org info request

Field | Type | Description
------|------|------------
`name`     | [string](#string)  | (Optional) A short name of the org.
`image_id`  | [asset ID](#id-types) | (Optional) Unique API-generated identifier for the org image (obtained from the API after uploading an image asset).
`location` | [location data](#location-data)  | (Optional) Organization's geographical location.
`website`  | [string](#string)  | (Optional) The org's website URL.

#### Response org

[Base org](#base-org) extended with the following fields:

Field | Type | Description
------|------|------------
`id`          | [org ID](#id-types) | ID of the org.
`parentId`    | [org ID](#id-types) | ID of the org's parent
`containerId` | [org ID](#id-types) | ID of the org's container
`orgType`     | [org type](#org-type) |
`settings`    | [object](#object)   | Any valid JSON object.

#### Org Status Update Model

Field | Type | Description
------|------|------------
orgStatus | [org status](#org-status) | New value for org status

#### Org Status Response Model

Field | Type | Description
------|------|------------
orgId | [org ID](#id-types) | ID of the org
orgStatus | [org status](#org-status) | New value for org status

#### Org configs

Field | Type | Description
------|------|------------
`isPortalEnabled` | [boolean](#boolean) | Does this org have org portals?
`defaultOrgPortalId` | [org ID](#id-types) | ID of the default org portal
`providers` | Array of String | (Optional) Org Portal providers
`learnerTrackingMethod` | [string](#string)  | (Optional) Valid values - `per-course` or `org-wide`; defaults to `org-wide`
`defaultThemeId` | [theme ID](#id-types)  | (Optional) Theme ID.
`settings`    | [object](#object)   | Any valid JSON object.

#### Reorder Org Courses

Field | Type | Description
------|------|------------
courseKey | [course id](#id-types) | Course Key
newIndex | [integer](#integer) | 1 based index - new position


#### SSO configs

Field | Type | Description
------|------|------------
`isSSOEnabled` | [boolean](#boolean)  | Is SSO enabled for the organization?
`ssoMethod` | [string](#string) | (Optional) SSO Method. Mandatory if `isSSOEnabled` is set to true
`ssoDetails` | [model](#sso-details)  | (Optional) SSO details. Mandatory if `isSSOEnabled` is set to `true`.

#### SSO details

Configure SSO details.

SAML config

Field | Type | Description
------|------|------------
samlIdpEntityId | [string](#string) | Entity ID at the IDP for this SAML integration
samlIdpUrl | [string](#string) | URL for the IDP
samlAssertionIsSigned | [boolean](#boolean) | Does the IDP sign assertions?
samlRequestIsSigned | [boolean](#boolean) | Does the IDP sign requests?
samlNameIDFormat    | [string](#string) | Format of the user ID we receive from the IDP. Acceptable values: `"urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress"`, `"urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified"`
samlJITProvisioningEnabled | [boolean](#boolean) | If `true`, when we receive a valid SSO request a user will be created automatically if one does not already exist. Mutually exclusive with `samlScimEnabled`
samlUserDataDictionary | Map([string](#string), [string](#string)) | Maps properties of our User model to properties received from the IDP. Valid keys: "GIVEN_NAME", "FAMILY_NAME", "FULL_NAME". Required if `samlJITProvisioningEnabled` is true.  
samlScimEnabled | [boolean](#boolean) | If `false`, SCIM endpoints will be unavailable for org. Defaults to `false`. Mutually exclusive with `samlJITProvisioningEnabled` 

#### Certificate

Field | Type | Description
------|------|------------
`id` | [boolean](#id-types)  | Certificate ID.
`certificateType` | [string](#string) | Certificate Type. Only PEM for now.
`expirationDate` | [model](#date-time)  | Certificate expiration date.

#### Create topic

Field | Type | Description
------|------|------------
`name` | [string](#string) | (Mandatory) name - maximum 80 characters.
`description` | [Topic](#string) | (Optional) topic description.
`settings`   | [object](#object) | Any valid JSON object.


#### Org topic

Field | Type | Description
------|------|------------
`id` | [org ID](#id-types) | ID of the org. Present in responses. Ignored in requests.
`name` | [string](#string) | (Mandatory) Topic name - maximum 40 characters.
`description` | [string](#string)  | (Mandatory) Free description string.
`coursesCount` | [integer](#integer)  | (Optional) number of courses associated with topic.
`settings`   | [object](#object) | Any valid JSON object.


#### Org topic tree

Field | Type | Description
------|------|------------
`id` | [org ID](#id-types) | ID of the org. Present in responses. Ignored in requests.
`name` | [string](#string) | (Mandatory) Topic name - maximum 40 characters.
`description` | [string](#string)  | (Mandatory) Free description string.
`topics` | List(topics)(#org-topic-tree) | The recursive list of subtopics.

#### Patch topic

Field | Type | Description
------|------|------------
`name` | [string](#string) | (Optional) Topic name - maximum 80 characters.
`description` | [string](#string)  | (Optional) Free description string.
`settings`   | [object](#object) | Any valid JSON object.


#### Org portal

Field | Type | Description
------|------|------------
`orgId` | [org ID](#id-types) | ID of the org.
`name` | [string](#string) | (Mandatory) Portal name - maximum 80 characters.
`description` | [string](#string)  | (Mandatory) Free description string.
`isPublic` | [boolean](#boolean)  | (Mandatory) Indicator whether portal is public. 
`selfProvisioningEnabled` | [boolean](#boolean)  | (Mandatory) Indicator whether self provisioning is enabled for public portal. 
`anonymousAccessEnabled` | [boolean](#boolean) | (Mandatory) Indicator whether anonymous (i.e. unknown signed in user) can access.
`allCoursesViewEnabled` | [boolean](#boolean)  | (Mandatory) Indicator whether portal is turn on or off
`enabled` | [boolean](#boolean)  | (Mandatory)
`brandImage` | [Asset](#asset) | (Updated separately)
`settings`   | [object](#object) | Any valid JSON object.


#### Create org portal

Field | Type | Description
------|------|------------
`name` | [string](#string) | (Mandatory) name - maximum 80 characters.
`description` | [string](#string)  | (Optional) Free description string.
`isPublic` | [boolean](#boolean)  | (Mandatory) Indicator whether portal is public. 
`selfProvisioningEnabled` | [boolean](#boolean)  | (Optional) Indicator whether self provisioning is enabled for public portal. Defaults to false. 
`anonymousAccessEnabled` | [boolean](#boolean) | (Mandatory) Indicator whether anonymous (i.e. unknown signed in user) can access.
`allCoursesViewEnabled` | [boolean](#boolean)  | (Optional) Indicator whether for enabling/disabling all topics page in portals UI. Defaults to false. 
`enabled` | [boolean](#boolean)  | (Optional) Indicator whether portal is turn on or off. Defaults to true.
`settings`   | [object](#object) | Any valid JSON object.
 

#### Patch org portal

Field | Type | Description
------|------|------------
`name` | [string](#string) | (Optional) Portal name - maximum 80 characters.
`description` | [string](#string)  | (Optional) Free description string.
`isPublic` | [boolean](#boolean)  | (Optional) Indicator whether portal is public (default is `false`). 
`selfProvisioningEnabled` | [boolean](#boolean)  | (Optional) Indicator whether self provisioning is enabled for public portal. Defaults to false. 
`anonymousAccessEnabled` | [boolean](#boolean) | (Mandatory) Indicator whether anonymous (i.e. unknown signed in user) can access.
`allCoursesViewEnabled` | [boolean](#boolean)  | (Optional) Indicator whether for enabling/disabling all topics page in portals UI. Defaults to false. 
`enabled` | [boolean](#boolean)  | (Optional) Indicator whether portal is turn on or off. Defaults to true.
`settings`   | [object](#object) | Any valid JSON object.
 

#### Portal course author
Field | Type | Description
------|------|------------
`id` | [user ID](#id-types) | Unique API-generated identifier.
`name` | [string](#string) | (Optional) user name.
`email` | [string](#string) | (Optional) email address.
`bio` | [string](#string) | (Optional) user bio.
`image` | [assets](#asset) | (Optional) user image.

#### Portal course view model

Field | Type | Description
------|------|------------
`courseId` | [Course Key](#id-types) | Unique API-generated identifier.
`authors` | array of [users](#portal-course-author) | course authors
`image` | array of [assets](#asset) | (Optional) | course image
`title` | [string](#string) | course title
`isAuthor` | [boolean](#boolean) | Has value `true` if the requesting user is author of the course; `false` otherwise.
`updated` | [integer](#integer)  | last edit timestamp
`created` | [integer](#integer)  | creation time timestamp
`longDesc` | [string](#string) | (Optional) Course long description
`shortDesc` | [string](#string) | (Optional) Course short description
`tags` | array of [string](#string) | Course tags
`isBookmarked` | [boolean](#boolean) | Has value `true` if the requesting user bookmarked thiscourse; `false` otherwise.

#### Org tree

[Response org](#response-org) extended with the following fields` |

Field | Type | Description
------|------|------------
`permissions` | array of [string](#string) | A non-empty array, currently supported VFO permissions include `AdministerOrg` and `TeachCourses`.
`orgs`      | array of [org tree](#org-tree)  | (Optional) The org's child orgs if they exist.

#### Request VFO session

Field | Type | Description
------|------|------------
`userId`    | [user ID](#id-types) | (Optional) The user for whom a session is requested.
`email`     | [email](#email)      | The email address of the user for whom a session is requested.
`expiresIn` | [number](#number)    | (Optional) The initial duration of the session, in milliseconds. Must be a non-negative integer.

#### Response VFO session

Field | Type | Description
------|------|------------
`sessionId` | [session ID](#id-types)         | Unique, API-generated identifier.
`userId`    | [user ID](#id-types) | The user to whom the session belongs.
`expiresIn` | [number](#number)    | The initial duration of the session, in milliseconds. 

### VFO Org permissions

Field | Type | Description
---|---|---
`permissions` | array of [string](#string) | A non-empty array, currently supported VFO permissions include `AdministerOrg` `TeachCourses`, and `LearnCourses`.

### Response VFO user permissions

Field | Type | Description
---|---|---
user | [VFO short user](#vfo-short-user)
memberships | array of [VFO user membership](#vfo-user-membership) | A non-empty array listing all explicit memberships of a user in suborgs.

### VFO short user

Field | Type | Description
------|------|------------
`id`            | [user ID](#id-types)                   | Unique API-generated identifier.
`username`      | [slug](#slug)       | A unique, human-readable user identifier. It is used in the type strings of gadgets created by the user in place of their user ID, if defined.
`email`         | [email](#email)     | Unique email address.
`fullname`      | [string](#string)   | Alternate full name.
`displayname`   | [string](#string)   | Formatted display name chosen from user's non-null name fields. </br> Display name is chosen in the following order: `fullName`, `fn`,  `firstName + " " + lastName`, `firstName`, `lastName`, `"Unknown"`

### VFO user membership

Field | Type | Description
------|------|------------
`orgId` | [org ID](#id-types) | ID of the org.
`permissions` | array of [string](#string) | A non-empty array, currently supported VFO permissions include `AdministerOrg` and `TeachCourses`.

### VFO move course data

Field | Type | Description
------|------|------------
`id`  | [course ID](#id-types) | Course id for the course being moved.

### VFO move course request

Field | Type | Description
------|------|------------
`courses` | array of [VFO move course data](#vfo-move-course-data) | A possibly empty array of course data for courses that need to be moved.

### VFO single sign-on (SSO) authorization request input model

Field | Type | Description
------|------|------------
`method` | [string](#string) | Single sign-on method. Accepted values: "SAML"
`resource` | [string](#string) | URL for the protected page or resource the end user is trying to access

### VFO single sign-on (SSO) authorization request response model
Field | Type | Description
------|------|------------
`SAMLRequest` | [string](#string) | Base64-encoded XML document
`destination` | [string](#string) | URL for the Identity Provider's SSO request endpoint

### VFO single sign-on (SSO) session request input model
`method` | [string](#string) | Single sign-on method. Accepted values: "SAML"
`data`   | object            | Varies according to SSO method. Accepted shapes: `{"SAMLResponse":string}`

### VFO single sign-on (SSO) session request response model
Field | Type | Description
------|------|------------
`resource` | [string](#string) | URL for the protected page or resource the end user is trying to access
 `session` | [VFO Session](#response-VFO-session) | The created VFO session
 
### User group
Field | Type | Description
------|------|------------
`name` | [string](#string) | User group name - maximum 40 characters. 

### Response user group

Field | Type | Description
------|------|------------
`id`   | [user group ID](#id-types) | Unique API-generated identifier.
`name` | [string](#string)          | User group name - maximum 40 characters.

### Permissions

#### Get Course Permissions Legacy View Model

Field | Type | description
---|---|---
`permissions` | array of [string](#string) | The course permissions for specified session for the specified course

#### Get Course Permissions New View Model

Field | Type | description
---|---|---
`coursePermissions` | Map from [string](#string) to Set of [string](#string) | Map of course key to set of course permissions for specified session for that course

#### Get Org Permissions View Model

Field | Type | description
---|---|---
`orgPermissions` | Map from [string](#string) to Set of [string](#string) | Map of stringified org ids to set of VFO org permissions for specified session for that org

### Report Gen

#### Learners Report

The body for the learners report generation request

Field | Type | description
---|---|---
courses | List(courses)(#course-key) | List of course keys containing one and only one course key

#### Course completion report

The body for the course completion report generation request

Field | Type | description
---|---|---
startDate | [date](#date) | A start date for a report
endDate | [date](#date) | An end date for a report

### Learner Class Decoration

#### Learner class  (also in course details response)

Field | Type | Description
---|---|---
`courseId` | [course ID](#id-types) | Course ID
`userId`   | [user ID](#id-types)   | User ID
`linkMode` | [string](#string)      | "course" OR "overview"
`class`    | [class](#class)        | class properties
`parent`   | [parent](#parent)      | parent properties
`provider` | [provider](#provider)  | provider properties

#### learnerClass class

Field | Type | Description
---|---|---
`id`          | [string](#string)       | class id
`title`       | [string](#string)       | class title
`description` | [string](#string)       | class description
`startDate`   | [date-time](#date-time) | class start date

#### learnerClass parent

Field | Type | Description
---|---|---
`url`   | [string](#string) | parent url
`title' | [string](#string) | parent title

#### learnerClass provider

Field | Type | Description
---|---|---
`id`    | [string](#string) | provider id
`name`  | [string](#string) | provider name


### Locks

#### Gadget lock subject

Field | Type | Description
---|---|---
`courseId` | [course ID](#id-types) | Course ID
`gadgetId` | [gadget ID](#id-types) | Gadget ID

#### Lock request

Field | Type | Description
---|---|---
`type`    | [string](#string)                    | Resource type. For now, only `gadget` is acceptable.
`subject` | [lock subject](#gadget-lock-subject) | Resource lock subject. For now, only gadgets can be locked.

#### Resource lock

Field | Type | Description
---|---|---
`type`      | [string](#string)                    | Resource type. For now, it's always `gadget`.
`subject`   | [lock subject](#gadget-lock-subject) | Resource lock subject. For now, only gadgets can be locked.
`ownerId`   | [user ID](#id-types)                 | Lock owner. Only the owner can perform updates on the locked resource.
`expiresIn` | [integer](#integer)                  | Number of milliseconds after which the lock will be released.


### Themes

#### Theme 

Field | Type | Description
---|---|---
`ownerType` | [string](#string)   | Mandatory when creating new theme. Ignored when updating. Owner type. Currently only `VFO_ORG` is supported. 
`ownerId`   | [org_ID](#id-types) | Mandatory when creating new theme. Ignored when updating. Currently always the Org ID.
`theme` | [theme object](#theme-object) | Mandatory. Theme object.

#### Theme object

An arbitrary JSON object that stores any theme attributes. There is no spec for this object.

### SCIM

#### SCIM token response
Field | Type | Description
---|---|---
`token` | [string](#string) | token value
`expiryDate` | [date-time](#date-time) | token expiry date

#### SCIM get users response

SCIM get users response is defined by [`RFC-7644`](https://tools.ietf.org/html/rfc7644#section-3.4.2)
and [`RFC-643`](https://tools.ietf.org/html/rfc7643#section-4.1.1).

Field | Type | Description
---|---|---
`schemas` | [string](#string) | `["urn:ietf:params:scim:api:messages:2.0:ListResponse"]`
`totalResults` | [integer](#integer) | 
`startIndex` | [integer](#integer) | The 1-based index of the first result in the current set of list results.  REQUIRED when partial results are returned due to pagination.
`itemsPerPage` | [integer](#integer) | The number of resources returned in a list response page. REQUIRED when partial results are returned due to pagination.
`Resources` | Array of [users](#scim-user) |

#### SCIM user
Field | Type | Desctiption
---|---|---
`id` | [integer](#integer) |
`externalId` | [string](#string) | Optional user id from external system
`userName` | [email](#email) | user email
`displayName` | [string](#string) | full name
`name` | [SCIM user name](#scim-user-name) | user name

#### SCIM user name
Field | Type | Desctiption
---|---|---
`formatted` | [string](#string) | The full name
`givenName` | [string](#string) |
`familyName` | [string](#string) |

#### Create SCIM user request

Create SCIM user response is defined by [`RFC-7644`](https://tools.ietf.org/html/rfc7644#section-3.3)
and [`RFC-7643`](https://tools.ietf.org/html/rfc7643#section-4.1.1).

Field | Type | Description
---|---|---
`schemas` | [string](#string) | `["urn:ietf:params:scim:schames:core:2.0:User"]`
`userName` | [email](#email) | user email
`externalId` | [string](#string) | Optional user id from external system
`name` | [SCIM user name](#scim-user-name) | user name

#### Create SCIM user response

Create SCIM user response is defined by [`RFC-7644`](https://tools.ietf.org/html/rfc7644#section-3.3)
and [`RFC-7643`](https://tools.ietf.org/html/rfc7643#section-4.1.1).
The URI of the created user is included in the HTTP `Location` header and in `meta.location` field

Field | Type | Description
---|---|---
`id` | [integer](#integer) |
`schemas` | [string](#string) | `["urn:ietf:params:scim:schames:core:2.0:User"]`
`externalId` | [string](#string) | Optional user id from external system
`userName` | [email](#email) | user email
`displayName` | [string](#string) | full name
`name` | [SCIM user name](#scim-user-name) | user name
`meta` | [SCIM meta](#scim-meta) |

#### SCIM meta

Field | Type | Description
---|---|---
`location` | [string](#string) | URL to the newly created user

#### Get SCIM user response

Get SCIM user response is defined by [`RFC-7644`](https://tools.ietf.org/html/rfc7644#section-3.4.2) 
and [`RFC-7643`](https://tools.ietf.org/html/rfc7643#section-4.1.1). 

Field | Type | Description
---|---|---
`id` | [integer](#integer) |
`schemas` | [string](#string) | `["urn:ietf:params:scim:schames:core:2.0:User"]`
`externalId` | [string](#string) | Optional user id from external system
`userName` | [email](#email) | user email
`displayName` | [string](#string) | full name
`name` | [SCIM user name](#scim-user-name) | user name
`meta` | [SCIM meta](#scim-meta) |

#### Replace SCIM user request

Replace SCIM user response is defined by [`RFC-7644`](https://tools.ietf.org/html/rfc7644#section-3.5.1) 
and [`RFC-7643`](https://tools.ietf.org/html/rfc7643#section-4.1.1).

Field | Type | Description
---|---|---
`schemas` | [string](#string) | `["urn:ietf:params:scim:schames:core:2.0:User"]`
`userName` | [email](#email) | user email
`name` | [SCIM user name](#scim-user-name) | user name

#### Patch SCIM user request

Patch SCIM user response is defined by [`RFC-7644`](https://tools.ietf.org/html/rfc7644#section-3.5.2).

Field | Type | Description
---|---|---
`schemas` | [string](#string) | `["urn:ietf:params:scim:schames:core:2.0:PatchOp"]`
`Operations` | [SCIM patch operation](#scim-patch-operation)| Array of operations to be applied

#### SCIM patch operation

Field | Type | Description
---|---|---
`op` | [string](#string) | Type of operation. One of `add`, `remove`, `replace`. Case insensitive.
`path` | [string](#string) | Path to the value that is being changed. Currently supported: `userName`, `name.givenName`, `name.familyName`, `name.formatted`.
`value` | [array](#array) | Array of single object with the single property `value` inside.


#### Replace SCIM user response

Replace SCIM user response is defined by [`RFC-7644`](https://tools.ietf.org/html/rfc7644#section-3.5.1)
and [`RFC-7643`](https://tools.ietf.org/html/rfc7643#section-4.1.1).
The URI of the replaced user is included in the HTTP `Location` header and in `meta.location` field

Field | Type | Description
---|---|---
`id` | [integer](#integer) |
`externalId` | [string](#string) | Optional user id from external system
`schemas` | [string](#string) | `["urn:ietf:params:scim:schames:core:2.0:User"]`
`userName` | [email](#email) | user email
`displayName` | [string](#string) | full name
`name` | [SCIM user name](#scim-user-name) | user name
`meta` | [SCIM meta](#scim-meta) |

#### Dashboard user courses

Field | Type | Description
---|---|---
`title` | [string](#string) |
`status` | [string](#string) | Available statuses: `started`, `in-progress`, `completed`
`percentageCompleted` | [integer](#integer) | progress
`startDate`  | [date-time](#date-time)      | The start date of the course.
`finishDate`  | [date-time](#date-time)     | The finish date of the course.
`score`  | [number](#number) | [Cumulative grade](#cumulative-grade), between 0 and 1.

### Org Portal Types

#### Org portal search response

Field | Type | Description
---|---|---
orgId | [org ID](#id-types) | ID of the org.
defaultOrgPortal | [Org Portal](#org-portal) | Object representing the default portal

#### Org portal domain name update 

Field | Type | Description
---|---|---
subdomain | String | (Mandatory) New portal subdomain

### Plan features

#### Plan

Text representing valid JSON.

#### Plan feature overrides

Field | Type | Description
---|---|---
`overrides` | [string](#string) | Text representing valid JSON.
