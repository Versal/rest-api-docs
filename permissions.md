# About Permissions

## Concepts

Course API permissions grant an API session the ability to perform an action on some object. Permissions are divided into
three sub-types: `CoursePermission`, `OrgPermission`, and `SitewidePermission`. `CoursePermission`s grant authorization to 
act on a Course.`OrgPermission`s grant authorization to act on a VFO Org. `SiteWidePermission`s, currently in development,
will reflect authorization to perform site-wide administrative actions.

Permission sub-types cannot be mixed (e.g., a user cannot be granted the `PublishCourse` permission within a VFO group).
`OrgPermission`s can implicitly grant `CoursePermission`s. However, `CoursePermission`s cannot grant `OrgPermission`s, 
`OrgPermission`s cannot grant other `OrgPermission`s, and `CoursePermission`s cannot grant other `CoursePermission`s.                                                                                                                                             

## Org Permissions

<table>
    <tr>
        <th>Name</th>
        <th>Granted By</th>
        <th>Authorizations</th>
        <th>Notes</th>
    </tr>
    <tr>
        <td><code>AdministerOrg</code></td>
        <td>User ID + VFO Org ID (<code>vfo_user_org_permission</code>)</td>
        <td> 
            <ul>
                <li>Modify VFO Org metadata</li>
                <li>Manage groups below the VFO Org</li>
                <li>Delete the VFO Org</li>
                <li>Modify permissions for users within the VFO Org</li>
                <li>Share and unshare courses with the VFO Org</li>
                <li>Invite learners to courses shared with the VFO Org</li>
                <li>Learn, edit, publish, and view analytics for courses shared with the VFO Org</li>
                <li>Add and remove contributors to courses shared with the VFO Org</li>
            </ul>
        </td>
        <td></td>
    </tr>
    <tr>
        <td><code>TeachCourses</code></td>
        <td>User ID + VFO Org ID (<code>vfo_user_org_permission</code>)</td>   
        <td>
            <ul>
                <li>Learn and view analytics for courses shared with the VFO Org</li>
                <li>Invite learners to courses shared with the VFO Org</li>
                <li>Share and unshare courses with the VFO Org, if you are a course editor</li>
            </ul>
        </td>
    </tr>
    <tr>
        <td><code>LearnCourses</code></td>
        <td>User ID + VFO Org ID (<code>vfo_user_org_permission</code>)</td>
        <td>
            <ul>
                <li>Learn courses shared with the VFO Org</li>
                <li>Share and unshare courses with the VFO Org, if you are a course editor</li>
            </ul>
        </td>
        <td></td>
    </tr>
</table>
        
## Course Permissions
<table>
    <tr>
        <th>Name</th>
        <th>Granted By</th>
        <th>Authorizations</th>
        <th>Notes</th>
    </tr>
    <tr>
        <td><code>ArchiveCourse</code></td>
        <td>
            <ul>
                <li>User ID + Program ID (<code>user_access</code>)</li>
                <li>Authenticator ID + Program ID (<code>authenticator_access</code>)</li>
                <li>Implicitly by having <code>AdministerOrg</code> permission in a VFO Org the course is shared with</li>
            </ul> 
        </td>
        <td>
            Mark the course as deleted
        </td>
        <td></td>
    </tr>
    <tr>
        <td><code>EnrollInAPublishedCourse</code></td>
        <td>
            <ul>
                <li>User ID + Program ID (<code>user_access</code>)</li>
                <li>Authenticator ID + Program ID (<code>authenticator_access</code>)</li>
                <li>Implicitly by having <code>AdministerOrg</code> or `TeachCourses` permission in a VFO Org the course is shared with</li>
                <li>Implicitly if the course is public</li>
            </ul>
        </td>
        <td>
            Learn the published instance of the course
        </td>
        <td></td>
    </tr>
    <tr>
        <td><code>InsertConfigureDeleteYourOwnGadgetInstances</code></td>
        <td>
            Modify course content
        </td>
        <td>
            <ul>
                <li>User ID + Program ID (<code>user_access</code>)</li>
                <li>Authenticator ID + Program ID (<code>authenticator_access</code>)</li>
                <li>Implicitly by having <code>AdministerOrg</code> permission in a VFO Org the course is shared with</li>
            </ul> 
        </td>
        <td></td>
    </tr>
    <tr>
        <td><code>InstructCourse</code></td>
        <td>Implicitly by having <code>AdministerOrg</code> or `TeachCourses` permission in a VFO Org the course is shared with</td>
        <td>None</td>
        <td>
            The <code>InstructCourse</code> permission replaces the use of `TeachCourses` in the `Course.permissions[]` view model property.
            This permission is used to indicate that the calling session has the Instructor role with request to the course. 
            The <code>InstructCourse</code> permission is virtualized (never granted directly in `user_access` or `authenticator_access`).
        </td>
    </tr>
    <tr>
        <td><code>ManageAllAuthoringInvitationsAndPermissions</code></td>
        <td>
            <ul>
                <li>User ID + Program ID (<code>user_access</code>)</li>
                <li>Authenticator ID + Program ID (<code>authenticator_access</code>)</li>
                <li>Implicitly by having <code>AdministerOrg</code> permission in a VFO Org the course is shared with</li>
            </ul>
        </td>
        <td>
            Add and remove course editors and publishers
        </td>
        <td></td>
    </tr>
    <tr>
        <td><code>PublishCourses</code></td>
        <td>
            <ul>
                <li>User ID + Program ID (<code>user_access</code>)</li>
                <li>Authenticator ID + Program ID (<code>authenticator_access</code>)</li>
                <li>Implicitly by having <code>AdministerOrg</code> permission in a VFO Org the course is shared with</li>
            </ul>
        </td>
        <td>
            Journal a new course revision and mark the revision as published.
        </td>
        <td>
            If the caller is a VFO session or has an active Versal Pro subscription, having the <code>PublishCourses</code> permission
            implicitly grants the <code>SetProgramVisibility</code>, `TrackLearners`, and `ViewCourseAnalytics` permissions.
        </td>
    </tr>
    <tr>
        <td><code>SetProgramVisibility</code></td>
        <td>
            <ul>
                <li>User ID + Program ID (<code>user_access</code>)</li>
                <li>Authenticator ID + Program ID (<code>authenticator_access</code>)</li>
                <li>Implicitly by having <code>AdministerOrg</code> permission in a VFO Org the course is shared with</li>
                <li>Implicitly by having the <code>PublishCourses</code> permission together with a Versal Pro subscription or VFO session</li>
            </ul>
        </td>
        <td>
            Toggle course public/private setting
        </td>
        <td></td>
    </tr>
    <tr>
        <td><code>TrackLearners</code></td>
        <td>
            <ul>
                <li>User ID + Program ID (<code>user_access</code>)</li>
                <li>Authenticator ID + Program ID (<code>authenticator_access</code>)</li>
                <li>Implicitly by having <code>AdministerOrg</code> or `TeachCourses` permission in a VFO Org the course is shared with</li>
                <li>Implicitly by having the <code>PublishCourses</code> permission together with a Versal Pro subscription or VFO session</li>
            </ul>
        </td>
        <td>
            None
        </td>
        <td>
        </td>
    </tr>
    <tr>
        <td><code>ViewCourseAnalytics</code></td>
        <td>
            <ul>
                <li>User ID + Program ID (<code>user_access</code>)</li>
                <li>Authenticator ID + Program ID (<code>authenticator_access</code>)</li>
                <li>Implicitly by having <code>AdministerOrg</code> or `TeachCourses` permission in a VFO Org the course is shared with</li>
                <li>Implicitly by having the <code>PublishCourses</code> permission together with a Versal Pro subscription or VFO session</li>
            </ul>
        </td>
        <td>
            <ul>
                <li>View learner progress data for the course</li>
                <li>View learner grades for the course</li>
                <li>Toggle learner as tracked/untracked for the course</li>
                <li>View learner activity reports from LAAPI</li>
                <li>View learner user state for the course</li>
            </ul>
        </td>
        <td></td>
    </tr>
    <tr>
        <td><code>ViewUnpublishedCourseAsLearner</code></td>
        <td>
            <ul>
                <li>User ID + Program ID (<code>user_access</code>)</li>
                <li>Authenticator ID + Program ID (<code>authenticator_access</code>)</li>
                <li>Implicitly by having <code>AdministerOrg</code> permission in a VFO Org the course is shared with</li>
            </ul>
        </td>
        <td>
            Learn the unpublished instance of the course
        </td>
        <td></td>
    </tr>
</table>  

## Site Wide Permissions
<table>
    <tr>
        <th>Name</th>
        <th>Granted By</th>
        <th>Authorizations</th>
        <th>Notes</th>
    </tr>
    <tr>
        <td>
            <code>VersalAdmin</code>
       </td>
       <td>
            User (<code>user_permission</code>)
       </td>
       <td>None</td>
       <td>In development</td>
   </tr>
</table>


## Summary of Implicit Permission Grants

<table>
    <tr>
        <th>Explicit Org Permission</th>
        <th>Implicit Course Permissions</th>
    </tr>
    <tr>
        <td><code>AdministerOrg</code></td>
        <td>
            <code>ArchiveCourse</code>
            <br/>
            <code>EnrollInAPublishedCourse</code>
            <br/>
            <code>InsertConfigureDeleteYourOwnGadgetInstances</code>
            <br/>
            <code>InstructCourse</code>
            <br/>
            <code>ManageAllAuthoringInvitationsAndPermissions</code>
            <br/>
            <code>PublishCourses</code>
            <br/>
            <code>SetProgramVisibility</code>
            <br/>
            <code>TrackLearners</code>
            <br/>
            <code>ViewCourseAnalytics</code>
            <br/>
            <code>ViewUnpublishedCourseAsLearner</code>
        </td>
    </tr>
    <tr>
        <td><code>TeachCourses</code></td>
        <td>
            <code>EnrollInAPublishedCourse</code>
            <br/>
            <code>InstructCourse</code>
            <br/>
            <code>TrackLearners</code>
            <br/>
            <code>ViewCourseAnalytics</code>
        </td>
    </tr>
    <tr>
        <td><code>LearnCourses</code></td>
        <td>
            <code>EnrollInAPublishedCourse</code>
        </td>
    </tr>   
</table>


<table>
    <tr>
        <th>Explicit Account Subscription</th>
        <th>Implicit Course Permissions</th>
    </tr>
    <tr>
        <td>Versal Pro or VFO</td>
        <td>
            If caller has the <code>PublishCourses</code> permission:
            <br/>
            <code>SetProgramVisibility</code>
            <br/>
            <code>TrackLearners</code>
            <br/>
            <code>ViewCourseAnalytics</code>
        </td>
    </tr>
</table>


            
# Permissions endpoints

## Get permissions

List the calling session's permissions on an object

Request property | Spec
---|---
Action                                | GET /permissions
[`SID` header](request.md#sid-header) | Any authenticated caller
`modelType` parameter                 | Indicates whether to return legacy model or new map-style model
`searchType` parameter                | Indicates what object type's permissions are being requested. Valid types: `Course`,`VFOContainer`
`id` parameter                        | Identifier key for the object
Body model                            | _no body_
Scala                                 | `object GetPermissions`

Use this endpoint to get the permissions of the calling session on the specified entity, as search type and id. 
For `searchType=Course` the `modelType` can be `new` or `legacy` , case insensitive. If not specified it defaults to `legacy`
For `searchType=VFOContainer` the `modelType` can be `new`, case insensitive.

Status | Response body spec
---|---
200 | For `Course` search: If `modelType` is `legacy` [Course Permission Legacy View Model](models.md#get-course-permissions-legacy-view-model) else [Course Permission New View Model](models.md#get-course-permissions-new-view-model). For `VFOContainer` search: [Org Permission View Model](models.md#get-org-permissions-view-model)
400 | `searchType` is required
400 | `id` is required
400 | Invalid `searchType`
400 | `Unknown modelType 'modelType'`
403 | `Insufficient permissions`
