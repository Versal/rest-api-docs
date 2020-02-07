# Versal For Orgs - Org Portals

The Org Portal project aims to provide our customers with a learner experience out of the box
that the admins can customize. The idea is that `SUBDOMAIN.versal.com` will automatically take 
them to their courses, organized into portals and topics.

# Configuration

## isPortalEnabled

This can be toggled on and off to enable or disable org portals for the container.

## portalSubdomain

The portal sub-domain name. When enabling org portals for the container, if this is not set,
this is set to a random name "CustomerXXXXXX" where XXXXXX = 6 alphanumeric characters. e.g.
"CustomerX3z56P". The partner key (not the org admin) can then update it to a string of
up to 40 alphanumeric characters. Once this is set, enabling/disabling org portals will not 
change the value. However, containers with the Org Portals feature disabled will not show up in search by 
subdomain results of [Search for VFO Container by Portal Subdomain](#search-for-vfo-container-by-portal-subdomain) 
even if the subdomain is set.

## defaultOrgPortalId

The default org portal id for this container. If not set, a default portal org with portal decorations 
is created and set in the config when enabling the container for portals. A partner key or org admin can then 
update this. Note that once this is set, enabling a container will not change the value. Note that
if the org is made to be not a portal using [Delete Portal](#delete-portal) or the underlying org itself is 
deleted using [Delete Org](vfo-orgs.md#delete-org), then this config will be updated under the hood to be empty. 

# Endpoints

The endpoints for org portal management are  

## Search for VFO Container by Portal Subdomain

Required permissions : Partner Key

Request property | Spec
---|---
Action | GET /vfo/orgportals 
`subdomain` param | Required, subdomain to search for
Scala  | `class VFO_GetContainerByPortalSubdomain`

Status | Response body spec
---|---
200 | [Response org](models.md#org-portal-search-response)
400 | `{"error": 400, "message": "Default Org Portal is not defined for container"}` <br> If container's default org portal wasn't set.
403 | `{"error": 403, "message": "Invalid VFO credentials"}` <br> If `SID` is not a partner key.
404 | `{"error": 404, "message": "Container for specified domain name not found"}` <br> If specified domain name is not found.

## Update Portal Subdomain name for a VFO Container

Required permissions : Partner Key

Request property | Spec
---|---
Action | POST /vfo/orgs/[`orgId`](models.md#id-types)/config/portalsubdomain
Body model | [Org portal domain name update model](models.md#org-portal-domain-name-update)
Scala | VFO_ReplacePortalSubdomain

Status | Response body spec
---|---
200 | Empty
400 | `{"error": 400, "message", "Org container is not portal enabled"}` if `isPortalEnabled` is `false` for the container
403 | `{"error": 403, "message": "Invalid VFO credentials"}` <br> If `SID` is not a partner key.
404 | `{"error": 404, "message": "Org :OID not found"}` <br> If specified org container is not found.

## Get org portal id by name

Request property | Spec
---|---
Action | GET /vfo/containers/[`containerId`](models.md#id-types)/portal
`name` param | Required, portal name to search for
Scala | VFO_GetOrgPortalByName

Status | Response body spec
---|---
200 | `{"orgId":"orgId"}`
400 | `{"error": 400, "message": "Parameter 'name' is required"}` <br> If parameter `name` is missing.
401 | `{"error": 401, "message": "Invalid credentials"}` <br> If not logged in.
404 | `{"error": 404, "message": "VFO Org ':containerId' not found"}` <br> If specified org container is not found.
404 | `{"error": 404, "message": "Org Portal ':name' not found in container"}` <br> If specified portal not found in given container.

## List topics

This endpoint allows user to see a list of topics for a portal.

Request property | Spec
---|---
Action | GET /vfo/orgs/[`portalId`](models.md#id-types)/topics
Scala | `GetTopics`

Status | Response Body Spec
---|---
200 | Array of [Org topic](models.md#org-topic)
403 | `{"error": 403, "message": "Invalid VFO credentials"}` <br> If not VFO user. 
403 | `{"error": 403, "message": "Insufficient permissions"}` <br> If anonymous session requested for private portal 
404 | `{"error": 404, "message": "Org ID is not marked as portal"}` <br> If `portalId` does not refer existing portal

## Create org marked as topic

This endpoint allows user to create org marked as topic for given portal.

Request property | Spec
---|---
Action | POST /vfo/orgs/[`portalId`](models.md#id-types)/topics
Body | [Org topic](models.md#create-topic)
Scala | `CreateTopic`

Status | Response Body Spec
---|---
200 | [Org topic](models.md#org-topic)
400 | `{"error": 400, "message": "Invalid input: name is ... chars, exceeding limit of 80"}` if provided org name value is too long
400 | `{"error": 400, "message": "Invalid input: non-alphabetic name"}` if provided org name value with only numbers
403 | `{"error": 403, "message": "Invalid VFO credentials"}` <br> If not VFO admin.

## Get topic

This endpoint allows user to get topic by ID.

Request property | Spec
---|---
Action | GET /vfo/orgs/[`orgId`](models.md#id-types)/topic_metadata
Scala | `GetTopic`

Status | Response Body Spec
---|---
200 | [Org topic](models.md#org-topic)
403 | `{"error": 403, "message": "Invalid VFO credentials"}` <br> If not VFO user. 
404 | `{"error": 404, "message": "Topic ID not found"}` <br> If topic not found.

## Modify topic

This endpoint allows user to update or set topic with provided ID.

Request property | Spec
---|---
Action | PATCH /vfo/orgs/[`orgId`](models.md#id-types)/topic_metadata
Body | [Org topic](models.md#org-topic)
Scala | `ModifyTopic`

Status | Response Body Spec
---|---
200 | [Org topic](models.md#org-topic)
403 | `{"error": 403, "message": "Invalid VFO credentials"}` <br> If not VFO admin.
404 | `{"error": 404, "message": "Topic ID not found"}` <br> If topic not found.

## Delete topic

This endpoint allows user to delete topic with provided ID.

Request property | Spec
---|---
Action | DELETE /vfo/orgs/[`orgId`](models.md#id-types)/topic_metadata
Scala | `DeleteTopic`

Status | Response Body Spec
---|---
200 | `{}`
403 | `{"error": 403, "message": "Invalid VFO credentials"}` <br> If not VFO admin. 
404 | `{"error": 404, "message": "Topic ID not found"}` <br> If topic not found.

## Create org marked as portal

This endpoint allows user to create org marked as portal under given org.

Request property | Spec
---|---
Action | POST /vfo/orgs/[`orgId`](models.md#id-types)/portals
Body   | [Create org portal](models.md#create-org-portal)
Scala  | `VFO_CreateOrgPortal`

Status | Response Body Spec
---|---
200 | [Org portal](models.md#org-portal)
400 | `{"error": 400, "message": "Invalid input: name is ... chars, exceeding limit of 80"}` if provided org name value is too long
400 | `{"error": 400, "message": "Invalid input: non-alphabetic name"}` if provided org name value with only numbers
400 | `{"error": 400, "message": "Self-provisioning cannot be enabled for private portals"}` if isPublic is false and selfProvisioningEnabled is true
400 | `{"error": 400, "message": "Invalid portal location"}` if a portal cannot be added here - Note that a portal cannot contain another portal in its subtree.
403 | `{"error": 403, "message": "Invalid VFO credentials"}` <br> If not VFO admin.


## Patch portal

This endpoint allows user to update or set portal metadata for given org.

Request property | Spec
---|---
Action | PATCH /vfo/orgs/[`orgId`](models.md#id-types)/portal_metadata
Body   | [Patch portal](models.md#patch-org-portal)
Scala  | `VFO_PatchOrgPortal`

Status | Response Body Spec
---|---
200 | [Org portal](models.md#org-portal)
400 | `{"error": 400, "message": "Invalid input: name is ... chars, exceeding limit of 40"}` if provided name value is too long
400 | `{"error": 400, "message": "Self-provisioning cannot be enabled for private portals"}` if isPublic is false and selfProvisioningEnabled is true
400 | `{"error": 400, "message": "Invalid portal location"}` if either `orgId` is a container, or the org that it represents contains or is contained by another portal
403 | `{"error": 403, "message": "Invalid VFO credentials"}` <br> If not VFO admin. 
404 | `{"error": 404, "message": "Org orgId is not marked as portal"}` <br> If org not marked as portal.

## Get portal

This endpoint allows user to get portal metadata.

Request property | Spec
---|---
Action | GET /vfo/orgs/[`orgId`](models.md#id-types)/portal_metadata
Scala  | `VFO_GetOrgPortal`

Status | Response Body Spec
---|---
200 | [Org portal](models.md#org-portal)
404 | `{"error": 404, "message": "Org orgId is not marked as portal"}` <br> If org not marked as portal.
403 | `{"error": 403, "message": "Invalid VFO credentials"}` <br> If not VFO admin. 

## Get container portals

This endpoint allows user to get all portals in container.

Request property | Spec
---|---
Action | GET /vfo/containers/[`containerId`](models.md#id-types)/portals
Scala  | `VFO_GetContainerOrgPortals`

Status | Response Body Spec
---|---
200 | Array of [org portals](models.md#org-portal)
403 | `{"error": 403, "message": "Invalid VFO credentials"}` <br> If not VFO admin. 

## Delete portal

This endpoint allows user to delete portal metadata.

Request property | Spec
---|---
Action | DELETE /vfo/orgs/[`orgId`](models.md#id-types)/portal_metadata
Scala  | `VFO_DeleteOrgPortal`

Status | Response Body Spec
---|---
200 | `{}`
404 | `{"error": 404, "message": "Org orgId is not marked as portal"}` <br> If org not marked as portal.
403 | `{"error": 403, "message": "Invalid VFO credentials"}` <br> If not VFO admin. 

## Get all courses for portal

This endpoint allows to get all courses in all topics under portal.

Request property | Spec
---|---
Action                                                | GET /vfo/containers/[`containerId`](models.md#id-types)/portals/[`portalId`](models.md#id-types)/courses
[`SID` header](request.md#sid-header)                 |
[Pagination params](pagination.md#request-parameters) |
`viewModel` param                                     | Valid values are: `portal` portal view model (default), `full` - print full course view model, `ids` - print only course ID property of view model, i.e.,`{"id":"CourseID"}`
`bookmarked` param                                    | Optional boolean, for `true` only courses bookmarked by the user in the session should be displayed.
`started` param                                       | Optional boolean, for `true` only courses started by the user in the session should be displayed.
`topicId` param                                       | Optional long, get all courses in specified topic
`ftContentSearch` param                               | Optional string. Performs a full-text search of course content for the given value.
Body model                                            | _no body_
Scala                                                 | `object GetCoursesForPortal`

This endpoint is available to partner key `SID` for private portal and for any logged user for public portal.

Status | Response body spec
---|---
200 | [paginated](pagination.md) array of [portal course](models.md#portal-course-view-model) (array of [base course](models.md#base-course) for `viewModel=full`)
400 | [Bad request](responses.md#bad-request)
400 | [Invalid pagination parameters](responses.md#invalid-pagination-parameters)
401 | [Invalid credentials](responses.md#invalid-credentials)
403 | `{"error": 403, "message": "Insufficient permissions"}` <br> If requesting session is neither a partner key, nor the user session for specified `bookmarkedByUser` parameter.
404 | `{"error": 404, "message": "Portal portalID not found"}` <br> If portalId is not existing portal within container, or container is not presently enabled for org portals.

## Get a topic's courses and all courses that can be added

This endpoint allows to get a topic's courses and all courses that can be added, ordered by title (case insensitive alphabetical)

Request property | Spec
---|---
Action                                                | GET /vfo/containers/[`containerId`](models.md#id-types)/portals/[`portalId`](models.md#id-types)/topics/[`topicId`](models.md#id-types)/searchcourses
[`SID` header](request.md#sid-header)                 |
[Pagination params](pagination.md#request-parameters) |
`search` param                                        | Optional string. Performs a full-text search of course content for the given value.
Body model                                            | _no body_
Scala                                                 | `object SearchTopicCourses`

Status | Response body spec
---|---
200 | [paginated](pagination.md) array of [course](models.md#base-course)
400 | [Bad request](responses.md#bad-request)
400 | [Invalid pagination parameters](responses.md#invalid-pagination-parameters)
400 | `{"error":400,"message":"Org container is not portal enabled"}` <br> If container is not presently enabled for org portals
401 | [Invalid credentials](responses.md#invalid-credentials)
403 | `{"error": 403, "message": "Invalid VFO credentials"}` <br> If requesting session is neither a partner key, nor the user session with AdministerOrg permission.
404 | `{"error": 404, "message": "Portal portalId not found"}` <br> If portalId is not existing portal within container, or container is not presently enabled for org portals.
404 | `{"error": 404, "message": "Topic topicId not found"}` <br> If topicId is not existing topic within portal.

## Bookmark course in portal

This endpoint allows user session to add a bookmark to the course.

Request property | Spec
---|---
Action                                                | PUT /vfo/containers/[`containerId`](models.md#id-types)/portals/[`portalId`](models.md#id-types)/courses/[`courseKey`](models.md#id-types)/bookmark
[`SID` header](request.md#sid-header)                 |
Body model                                            | _no body_
Scala                                                 | `object BookmarkPortalCourse`

This endpoint is available to any logged in user that has access to the specified portal.

Status | Response body spec
---|---
200 | `{}`
400 | [Bad request](responses.md#bad-request)
400 | `{"error":400, "message":"Org container is not portal enabled"}` <br> If the container is not portal enabled.
401 | [Invalid credentials](responses.md#invalid-credentials)
403 | `{"error": 403, "message": "Invalid VFO credentials"}` <br> If requesting session is not valid VFO user session.
404 | `{"error": 404, "message": "Course 'courseKey' not found in portal 'portalId'}"` <br> If there is no such course in requested portal.

## Unbookmark course in portal

This endpoint allows user session to remove a bookmark to the course.

Request property | Spec
---|---
Action                                                | DELETE /vfo/containers/[`containerId`](models.md#id-types)/portals/[`portalId`](models.md#id-types)/courses/[`courseKey`](models.md#id-types)/bookmark
[`SID` header](request.md#sid-header)                 |
Body model                                            | _no body_
Scala                                                 | `object UnbookmarkPortalCourse`

This endpoint is available to any logged in user that has access to the specified portal.

Status | Response body spec
---|---
200 | `{}`
400 | [Bad request](responses.md#bad-request)
400 | `{"error":400, "message":"Org container is not portal enabled"}` <br> If the container is not portal enabled.
401 | [Invalid credentials](responses.md#invalid-credentials)
403 | `{"error": 403, "message": "Invalid VFO credentials"}` <br> If requesting session is not valid VFO user session.
404 | `{"error": 404, "message": "Course 'courseKey' not found in portal 'portalId'}"` <br> If there is no such course in requested portal.

## Get Portal Course

This endpoint allows to get specified course under specified portal

Request property | Spec
---|---
Action                                                | GET /vfo/containers/[`containerId`](models.md#id-types)/portals/[`portalId`](models.md#id-types)/courses/[`courseKey`](models.md#id-types)
[`SID` header](request.md#sid-header)                 |
[Pagination params](pagination.md#request-parameters) |
Body model                                            | _no body_
Scala                                                 | `object GetPortalCourse`

This endpoint is available to partner key `SID` for private portal and for any logged user for public portal.

Status | Response body spec
---|---
200 | [portal course](models.md#portal-course-view-model)
400 | [Bad request](responses.md#bad-request)
401 | [Invalid credentials](responses.md#invalid-credentials)
403 | `{"error": 403, "message": "Insufficient permissions"}` <br> If requesting session is neither a partner key, nor the user session for specified `bookmarkedByUser` parameter.
404 | `{"error": 404, "message": "Portal portalID not found"}` <br> If portalId is not existing portal within container, or container is not presently enabled for org portals.
404 | `{"error": 404, "message": "Course 'courseKey' not found in portal 'portalId'}"` <br> If there is no such course in requested portal.

## Replace portal brand image

The user must be a signed in user and have `AdministerOrg` permission on the portal.

Request property | Spec
---|---
Action | POST /vfo/containers/[`orgId`](models.md#id-types)/portals/[`orgId`](models.md#id-types)/brandimage
Scala  | `class ReplacePortalBrandImage`

Status | Response body spec
---|---
200 | [updated image details](models.md#asset)
400 | [Bad request](responses.md#bad-request)
401 | [Invalid credentials](responses.md#invalid-credentials)
403 | `{"error": 403, "message": "Insufficient permissions"}` <br> If requesting session doesn't have the right authorization
404 | `{"error": 404, "message": "Portal portalID not found"}` <br> If portalId is not existing portal within container, or container is not presently enabled for org portals.
