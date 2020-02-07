
# Feature plans endpoints

In order to update our product to support tiered pricing for customers we are
storing subscription plan information in the rest api database. A Plan refers to 
a collection of features. It is represented by a JSON of the type key(String) to any 
value (String, list, more complex json etc.). This JSON Is interpreted by the front end.
The API has no opinion about this. However, other forms of JSON (e.g. a plain list of strings,
or a plain list of complex objects, or an empty json {}) are not acceptable even though 
they are valid JSON. 

A container is associated with a plan (by plan id). However it can also have overrides
for special deals that may have been cut. These overrides will add to or replace the 
plan's feature text for a given key but never delete a key.

## Get feature plans

Retrieves all feature plans.

Request property | Spec
---|---
Action                                | GET /plans
[`SID` header](request.md#sid-header) 
Body model                            | _no body_

Status | Response body spec
---|---
200 | Array of [Feature plan](models.md#plan)
401 | [Invalid credentials](responses.md#invalid-credentials)

## Get feature plan

Retrieves feature plan.

Request property | Spec
---|---
Action                                | GET /plans/[`planId`](models.md#id-types)
[`SID` header](request.md#sid-header) 
Body model                            | _no body_

Status | Response body spec
---|---
200 | [Feature plan](models.md#plan)
401 | [Invalid credentials](responses.md#invalid-credentials)

## Create feature plan

Creates new feature plan

Request property | Spec
---|---
Action                                | POST /plans
[`SID` header](request.md#sid-header) 
Body model                            | [Feature plan](models.md#plan)

Status | Response body spec
---|---
201 | [Feature plan](models.md#plan)
401 | [Invalid credentials](responses.md#invalid-credentials)

## Update feature plan

Updates feature plan

Request property | Spec
---|---
Action                                | PUT /plans/[`planId`](models.md#id-types)
[`SID` header](request.md#sid-header) 
Body model                            | [Plan](models.md#plan)

Status | Response body spec
---|---
200 | [Feature plan](models.md#plan)
401 | [Invalid credentials](responses.md#invalid-credentials)

## Delete feature plan

Deletes feature plan.

Request property | Spec
---|---
Action                                | DELETE /plans/[`planId`](models.md#id-types)
[`SID` header](request.md#sid-header) 
Body model                            | _no body_

Status | Response body spec
---|---
204 | _no body_
400 | `{"error":400,"message":"The plan is in use"}` <br> If the plan is used in any container.
401 | [Invalid credentials](responses.md#invalid-credentials)

# Org plan

## Get plan for org 

Request property | Spec
---|---
Action                                | GET /containers/[`containerId`](models.md#id-types)/plan
[`SID` header](request.md#sid-header) 
Body model                            | _no body_

Status | Response body spec
---|---
200 | `{ "planId": "PLAN_ID" }`
401 | [Invalid credentials](responses.md#invalid-credentials)
403 | `{"error": 403, "message": "Insufficient VFO permissions"}` <br> If the user is not authorized to update the requested resource. - partner key or org admin at root

## Set plan for org

Request property | Spec
---|---
Action                                | PUT /containers/[`containerId`](models.md#id-types)/plan
[`SID` header](request.md#sid-header) 
Body model                            | `{ "planId": "PLAN_ID" }`

Status | Response body spec
---|---
204 | _no body_
401 | [Invalid credentials](responses.md#invalid-credentials)
403 | `{"error": 403, "message": "Insufficient VFO permissions"}` <br> If the user is not authorized to update the requested resource. - partmer key or org admin at root
400 | If overrides present

## Reset feature plan for org 

Request property | Spec
---|---
Action                                | DELETE /containers/[`containerId`](models.md#id-types)/plan
[`SID` header](request.md#sid-header) 
Body model                            | _no body_

Status | Response body spec
---|---
204 | _no body_
401 | [Invalid credentials](responses.md#invalid-credentials) 
403 | `{"error": 403, "message": "Insufficient VFO permissions"}` <br> If the user is not authorized to update the requested resource. - partner key only
400 | If overrides present

## Get plan with overrides 

Request property | Spec
---|---
Action                                | GET /containers/[`containerId`](models.md#id-types)/plan-features
[`SID` header](request.md#sid-header) 
Body model                            | _no body_

Status | Response body spec
---|---
200 | [Feature overrides](models.md#plan)
401 | [Invalid credentials](responses.md#invalid-credentials)
403 | `{"error": 403, "message": "Insufficient VFO permissions"}` <br> If the user is not authorized to update the requested resource. - partner key or org admin at root

## Set feature overrides

Request property | Spec
---|---
Action                                | PUT /containers/[`containerId`](models.md#id-types)/plan-overrides
[`SID` header](request.md#sid-header) 
Body model                            | [Feature overrides](models.md#plan-feature-overrides) ... body like {"overrides":"...JSON...}

Status | Response body spec
---|---
204 | _no body_
401 | [Invalid credentials](responses.md#invalid-credentials)
403 | `{"error": 403, "message": "Insufficient permissions"}` <br> If the user is not authorized to update the requested resource. if not org admin at root or partner key

## Get feature overrides

Request property | Spec
---|---
Action                                | GET /containers/[`containerId`](models.md#id-types)/plan-overrides
[`SID` header](request.md#sid-header) 
Body model                            | _no body_

Status | Response body spec
---|---
200 | [Feature overrides](models.md#plan-feature-overrides)
401 | [Invalid credentials](responses.md#invalid-credentials)
403 | `{"error": 403, "message": "Insufficient VFO permissions"}` <br> If the user is not authorized to update the requested resource. .. org admin at root or partner key

## Reset feature overrides

Request property | Spec
---|---
Action                                | DELETE /containers/[`containerId`](models.md#id-types)/plan-overrides
[`SID` header](request.md#sid-header) 
Body model                            | _no body_

Status | Response body spec
---|---
204 | _no body_
401 | [Invalid credentials](responses.md#invalid-credentials)
403 | `{"error": 403, "message": "Insufficient VFO permissions"}` <br> If the user is not authorized to update the requested resource. .. org admin at root or partner key

