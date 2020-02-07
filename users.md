
# User endpoints

## Create user

Create a new, unique user record.

Request property | Spec
---------|------
Action                                | POST /users
[`SID` header](request.md#sid-header) |
Body model                            | [Base user](models.md#base-user); all fields optional
Scala                                 | `object AddUser`

If the `email` field is specified in the request body, but has the same value as an existing user's `email`,
then it is silently dropped. This is a legacy behavior that ensures user creation will not fail with
a 400 status if a duplicate email is provided.

If the `fullname` field is not specified, but the `firstname` and `lastname` fields are specified, then the
`fullname` field will be defined to have the value `$firstname $lastname`.

Site-wide VersalAdmin permissions can be granted with the payload `"permissions":"["VersalAdmin"]`. Only a Partner Key is
allowed to write to the `permissions[]` field. Updates coming from any other session type will be ignored.


Status | Response body spec
---|---
201 | [Response user](models.md#response-user)
403 | `{"error": 403, "message": "Insufficient permissions to create a user"}` <br> The provided `SID` is not a partner key, or does not belong to an authenticator or org administrator.
400 | `{"error": 400, "message": "The username '$username' is already taken"}` <br>  The username provided in the request body is the username of an existing user.
400 | `{"error": 400, "message": "The orgname '$username' is already taken"}` <br> The username provided in the request body is the orgname of an existing org. Usernames and orgnames are in the same namespace.
401 | [Invalid credentials](responses.md#invalid-credentials)
400 | [Bad request](responses.md#bad-request)

## Get user

Retrieves information about a specified user.

Request property | Spec
---|---
Action                                | GET /users/[`userKey`](models.md#user-key)
[`SID` header](request.md#sid-header) |
Body model                            | _no body_
Scala                                 | `class GetUser`

Status | Response body spec
---|---
200 | [Response user](models.md#response-user)
404 | `{"error": 404, "message": "User '$userKey' not found"}` <br> If `userKey` does not identify a user.
401 | [Invalid credentials](responses.md#invalid-credentials)

## Get current user

Retrieves information about the requesting user.

Request property | Spec
---|---
Action                                | GET /user
[`SID` header](request.md#sid-header) | Must be a user session ID.
Body model                            | _no body_
Scala                                 | `object GetUser`

Status | Response body spec
---|---
200 | [Response user](models.md#response-user)
401 | [Invalid credentials](responses.md#invalid-credentials), if `SID` does not identify a user.

## Delete gadget user state

**DEPRECATED**

Request property | Spec
---|---
Action | ~~DELETE /user/courses/[`courseId`](models.md#id-types)/progress~~
Scala  | `class DeleteGUState`

## Delete published gadget user state

**DEPRECATED**

Request property | Spec
---|---
Action | ~~DELETE /user/catalogs/staged/courses/[`courseId`](models.md#id-types)/progress~~
Scala  | `class DeleteGUState`

## Update user

Request property | Spec
---|---
Action | PUT /users/[`userId`](models.md#id-types)
Scala  | `class ReplaceUser`

User's course level and org level permissions are added in course and org endpoints respectively. 
Site-wide VersalAdmin permissions can be granted with the payload `"permissions":"["VersalAdmin"]`. Only a Partner Key is
allowed to update the `permissions[]` field. Updates coming from any other session type will be ignored.

## List started courses

List the courses started by a specified user.

Request property | Spec
---|---
Action                                                | GET /users/[`userId`](models.md#id-types)/courses
[`SID` header](request.md#sid-header)                 |
[Pagination params](pagination.md#request-parameters) |
`title` param                                         | Restricts results to courses with titles containing the given [string](models.md#string) value
`catalog` param                                       | With value `labs`, restricts results to published courses. With value `sandbox`, restricts results to courses the requesting user can edit. All other values are ignored.
Body model                                            | _no body_
Scala                                                 | `class GetMyStartedCourses`

See [list courses](courses.md#list-courses) for detailed behavior.

Status | Response body spec
---|---
200 | [paginated](pagination.md) array of [response course](models.md#response-course)
400 | `{"error": 400, "message": "Expected numeric value for: $userId"}` <br> If the provided `userId` is not an integer.
403 | `{"error": 403, "message": "A signed in user may only request their own courses"}` <br> If the requester is not using partner key credentials, and their user ID does not match the `userId` given in the endpoint path.
401 | [Invalid credentials](responses.md#invalid-credentials)
    | [Invalid pagination parameters](responses.md#invalid-pagination-parameters)

## Add subscription

Request property | Spec
---|---
Action | POST /users/[`userId`](models.md#id-types)/subscriptions
Scala  | `class AddSubscription`

## Remove subscription

Request property | Spec
---|---
Action | DELETE /users/[`userId`](models.md#id-types)/subscriptions/[`subscriptionType`](models.md#subscription)
Scala  | `class RemoveSubscription`

## List users

Request property | Spec
---|---
Action | GET /users
Scala  | `object GetUsers`

Status | Response body spec
---|---
200 | [paginated](pagination.md) array of [`user`](models.md#user)
403 | `{"error": 403, "message": "Not authorized to list users"}` if [`session ID`](models.md#id-types) is not a partner and `userId` does not belong to any org
403 | `{"error": 403, "message": "Org members only"}` if the `userId` does not belong to the org that is sought to be listed.

## Replace user image

Request property | Spec
---|---
Action | POST /user/image
Scala  | `object ReplaceUserImage`

## Custom trays: general remarks

A "tray" is an ordered sequence of "bundle items".
A "bundle item" is an ordered sequence of "gadget items".
A "gadget item" is a gadget type (e.g. `versal/header`) and a gadget config.

Users can add, remove, and reorder bundles in their trays.
However, users cannot change the order of gadget items in a bundle.

The trays labeled `downloads` and `clipboard` are always available for all users (do not need to be created). Any other trays need to be created.

## Create a custom tray

Permissions: partner or a user session with the same `userId`.

Request property | Spec
---|---
Action | POST /users/[`userId`](models.md#id-types)/trays
Body model | [tray request](models.md#custom-tray-request)
Scala  | `class AddTray`


Status | Response body spec
---|---
200 | [tray request](models.md#custom-tray-request)
404 | `{"error": 404, "message": "User not found"}` <br> If the provided `userId` does not correspond to any user.
401 | [Invalid credentials](responses.md#invalid-credentials)

## Get public trays of user

Permissions: partner or any user session.


Request property | Spec
---|---
Action | GET /users/[`userId`](models.md#id-types)/trays
Scala  | `class GetPublicTrays`

Status | Response body spec
---|---
200 | Array of [`tray label`](models.md#id-types)
404 | `{"error": 404, "message": "User not found"}` <br> If the provided `userId` does not correspond to any user.
401 | [Invalid credentials](responses.md#invalid-credentials)


## Add bundle to tray

Permissions: partner or a user session with the same `userId`.

Request property | Spec
---|---
Action | POST /users/[`userId`](models.md#id-types)/trays/[`tray label`](models.md#id-types)
Body model | [bundle item request](models.md#bundle-item-request)
Scala  | `class AddBundleToTray`

If `provider_id` is specified in the request, and if the tray already contains a bundle with the same `provider_id`, the previously existing bundle will be deleted after the new bundle is added to the tray.

Status | Response body spec
---|---
200 | {"id": ["`new bundle ID`"](models.md#id-types)}
400 | `{"error": 400, "message": "Invalid index value"}` <br>If the specified `index` value is negative or larger than the number of the existing bundles in the tray.
404 | `{"error": 404, "message": "Tray not found"}` <br> If the provided `tray label` does not correspond to any custom tray of this user.
401 | [Invalid credentials](responses.md#invalid-credentials)

## Delete bundle from tray

Permissions: partner or a user session with the same `userId`.

Request property | Spec
---|---
Action | DELETE /users/[`userId`](models.md#id-types)/trays/[`tray label`](models.md#id-types)/bundles/[`bundle id`](models.md#id-types)
Scala  | `class RemoveBundleFromTray`

Status | Response body spec
---|---
200 | {}
404 | `{"error": 404, "message": "Tray not found"}` <br> If the provided `tray label` does not correspond to any custom tray of this user.
401 | [Invalid credentials](responses.md#invalid-credentials)

## Clear all bundles from tray

Permissions: partner or a user session with the same `userId`.

Request property | Spec
---|---
Action | PUT /users/[`userId`](models.md#id-types)/trays/[`tray label`](models.md#id-types)
Body model | Empty list `[]`
Scala  | `class ReorderBundlesInTray`

Status | Response body spec
---|---
200 | {}
400 | "Invalid ordering"


## List bundles in tray

Permissions: partner or a user session with the same `userId`.
If the tray is `public`, can be any other user session. Otherwise returns 401 "Invalid credentials".

Request property | Spec
---|---
Action | GET /users/[`userId`](models.md#id-types)/trays/[`tray label`](models.md#id-types)
Scala  | `class GetFullTrayData`

Status | Response body spec
---|---
200 | [custom tray response](models.md#custom-tray-response)
404 | `{"error": 404, "message": "Tray not found"}` <br> If the provided `tray label` does not correspond to any custom tray of this user.
401 | [Invalid credentials](responses.md#invalid-credentials)

## Reorder bundles in tray

Request property | Spec
---|---
Action | PUT /users/[`userId`](models.md#id-types)/trays/[`tray label`](models.md#id-types)
Body model | A JSON array of bundle IDs as strings.
Scala  | `class ReorderBundlesInTray`

**Warning**: If some existing bundle IDs are missing in the body of this request, these bundles will be deleted from the tray.

Status | Response body spec
---|---
200 | {}
400 | "Invalid ordering"

## Update metadata for bundle in tray

Request property | Spec
---|---
Action | PUT /users/[`userId`](models.md#id-types)/trays/[`tray label`](models.md#id-types)/bundles/[`bundle id`](models.md#id-types)
Body model | [bundle item metadata request](models.md#bundle-item-metadata-request)
Scala  | `class UpdateBundleInTray`

Both parameters `"title"` and `"icon"` are optional. If parameters are given, their values will be used. If a parameter is not given, the previous value (if exists) will be unchanged.

Status | Response body spec
---|---
200 | {}
404 | `{"error": 404, "message": "Tray not found"}` <br> If the provided `tray label` does not correspond to any custom tray of this user.
404 | `{"error": 404, "message": "Bundle not found"}` <br> If the provided bundle ID does not correspond to any bundle ID in the given custom tray of this user.
401 | [Invalid credentials](responses.md#invalid-credentials)

