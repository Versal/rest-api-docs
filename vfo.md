# Versal For Organizations (VFO) endpoints

Versal For Organizations (VFO) provides multi-tenancy for customers of the Course API. 
Each tenant is represented by a top-level organization, referred to as a _VFO Container_. 
The VFO container can optionally be the root node for a tree of child organizations, a.k.a. _sub-orgs_. Customers can, 
for example, use their container's org tree to represent their business organizational structure.
Together, the set of VFO org trees forms a forest.   

Users and Courses have relationships to VFO containers. A user account may be related to multiple VFO containers. A user
becomes attached to the container by being added with permissions to some organization within the container, or by being associated 
with a course that belongs to the container (e.g., by learning the course.)

A course may be related to only one container. Within the container, a course can be shared with multiple organizations. 

Permissions given to users in a VFO Container cascade down the organization tree. For example, a user who has the
`TeachCourses` permission in "Parent Org" also implicitly has the `TeachCourses` permission in "Child Org" and "Grandchild Org". 

Course sharing does _not_ cascade down the organization tree, so a course shared with "Parent Org" must be explicitly shared
with "Child Org" and "Grandchild Org" in order to be visible there.

Org Portals provide a branded landing area for learners and visitors to discover and interact with courses belonging to a VFO Container.
Portals contain a set of Course Topics for organizing courses. A VFO Container may have multiple Portals. 

Note that all endpoints listed below are aliased to work starting with `/orgs`. e.g. `POST /orgs/orgs` is the same
as `POST /vfo/orgs`, `PUT /orgs/orgs/:OID/config` is the same as `PUT /vfo/orgs/:OID/config`.

[Course Management](vfo-courses.md)

[Org Dashboard](vfo-dashboard.md)

[Org Portals](vfo-portals.md)

[Orgs and Org Trees](vfo-orgs.md)

[Sessions and Authentication](vfo-authentication.md)

[User Management](vfo-users.md)

[VFO Containers](vfo-containers.md)
