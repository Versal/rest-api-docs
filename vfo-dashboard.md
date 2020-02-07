# Versal For Orgs - Org Dashboard

## Org Dashboard GET Members

This Endpoint is used to list all members for a given org, with the following data on each member - 

 * id - Numeric User ID
 * firstName - First Name (Optional)
 * lastName - Last Name (Optional)
 * fn - Full Name (Optional)
 * fullName - Full Name (Optional)
 * location - Location (Optional)
 * longDesc - Long Description (Optional)
 * shortDesc - Short Description (Optional)
 * username - User Name in the system (Optional)
 * enrolledCount - Number of org courses enrolled in
 * completedCount - Number of org courses completed
 * lastSeen - Time instant last seen
 * role - Role in the org, one of admin, instructor, org-learner (associated to org explicitly), course-learner (associated to the org only via course)
 * email - Email Address (Optional)
 * website - Website (Optional)
 * displayName - Formatted display name chosen from user's non-null name fields. Display name is chosen in the following
    order: fullName, fn,  firstName + " " + lastName, firstName, lastName, "Unknown"
 
There are seven optional query parameters -
 * includeAnonymousUsers - true/false trigger, whether the results should include anonymous users (no email, username or any name field defined). Default is `false`. 
 * role - Comma-separated list of roles to filter by 
 * filter - Text field to search in the firstname, lastname and email
 * sort - Text field to indicate Sort criterion for the returned resultset - fullName, lastSeen, role. Note: `fullName` 
    uses the formatted displayName as the sort field
 * order - Text field to indicate sort cardinality - ascending , descending
 * since - Unix timestamp to filter members that last visit was after that time
 * until - Unix timestamp to filter members that last visit was before that time
 
Either both `sort` and `order` have to be specified, or neither. Only one being specified will 
result in a Bad Request error. 
 
Request property | Spec
---|---
Action | GET /vfo/orgs/[`orgId`](models.md#id-types)/dashboard/members
[Pagination params](pagination.md#request-parameters) |
`filter` param | See above
`includeAnonymousUsers` param | See above
`role` param | See above
`sort` param | See above
`order` param | See above
`since` param | See above
`until` param | See above
Scala  | `VFO_GetUsersForDashboard`

Status | Response body spec
---|---
200 | [paginated](pagination.md) array of [User entries]
401 | `{"error": 401, "message": "Invalid credentials"}` <br> If not called with partner key or org admin SID for the org.
403 | `{"error": 403, "message": "Invalid VFO credentials"}` <br> If the user is not an admin in one of the orgs in this tree.
400 | `{"error": 400, "message": "Invalid org ID specified : '$orgId'"}` <br> If any of the given org ID does not refer to an existing VFO Org.
400 | `{"error": 400, "message": "Both sort and order are mandatory if one of them is supplied" if only one is specified}`
400 | `{"error": 400, "message": "Bad sort criterion - XXX"}` if bad sort criterion.
400 | `{"error": 400, "message": "Bad sort order - XXX"}` if bad sort order.
500 | `{"error": 500, "message": Varies}` <br> If database error, or other unhandled exception.

## Org Dashboard GET member courses

This Endpoint is used to list all courses for given user within given org - sorted by start date ascending.
 
Request property | Spec
---|---
Action | GET /vfo/orgs/[`orgId`](models.md#id-types)/dashboard/members/[`userId`](models.md#id-types)/courses
[Pagination params](pagination.md#request-parameters) |
`title` param | filter courses by given title
`status` param | filter courses by given status
Scala  | `VFO_GetUserCoursesForDashboard`

Status | Response body spec
---|---
200 | [paginated](pagination.md) array of [User course entries](models.md#dashboard-user-courses)
400 | `{"error": 400, "message": "Invalid VFO container specified"}` <br> If any of the given org ID does not refer to an existing VFO Org.
403 | `{"error": 403, "message": "Invalid VFO credentials"}`
404 | `{"error": 404, "message": "User 'userId' not found"}` 
404 | `{"error": 404, "message": "User 'userId' not found in container 'orgId'"}`
500 | `{"error": 500, "message": Varies}` <br> If database error, or other unhandled exception.

## Org Dashboard GET portal users

This Endpoint is used to list all users within given portal together with container's Admins and Instructors - also possible to filter by role

Request property | Spec
---|---
Action | GET /vfo/container/[`orgId`](models.md#id-types)/portal/[`portalId`](models.md#id-types)/dashboard/members
[Pagination params](pagination.md#request-parameters) |
`role` param | See at the top of page
`filter` param | See at the top of page
`includeAnonymousUsers` param | See at the top of page
`since` param | See at the top of page
`until` param | See at the top of page
Scala  | `VFO_GetPortalUsersForDashboard`

Status | Response body spec
---|---
200 | [paginated](pagination.md) array of [User entries]
401 | `{"error": 401, "message": "Invalid credentials"}` <br> If not called with partner key or org admin SID for the org.
403 | `{"error": 403, "message": "Invalid VFO credentials"}`
400 | `{"error": 400, "message": "Invalid org ID specified : '$orgId'"}` <br> If any of the given org ID does not refer to an existing VFO Org.
400 | `{"error": 400, "message": "Invalid role: 'role'"}` <br> If provided role name does not correspond to these used in domain
500 | `{"error": 500, "message": Varies}` <br> If database error, or other unhandled exception.
