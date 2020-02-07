
# Themes endpoints

## Create new theme

Creates new theme and assigns unique ID to it. The new theme is saved as draft and requires publishing.

Request property | Spec
---|---
Action                                | POST /themes
[`SID` header](request.md#sid-header) | Must be a SID that is authorized for the specific owner.
Body model                            | [Theme](models.md#theme)
Scala                                 | `class CreateTheme`

Status | Response body spec
---|---
201 | [Theme](models.md#theme)
404 | `{"error": 404, "message": "VFO Org '$ownerId' not found"}` <br> If `ownerId` value does not identify an org.
403 | `{"error": 403, "message": "Insufficient permissions"}` <br> If the SID is not authorized for the specific owner.
401 | [Invalid credentials](responses.md#invalid-credentials), if `SID` is not for a user.
400 | [Bad request](responses.md#bad-request)


## Get theme

Retrieves the theme by ID.

Request property | Spec
---|---
Action                                | GET /themes/[`themeId`](models.md#id-types)?mode=[`mode`](models.md#string)
[`SID` header](request.md#sid-header) | Must be authenticated SID.
`mode` param                          | Optional. Can be either `draft` or `published`. Defaults to `published`
Body model                            | _no body_
Scala                                 | `class GetTheme`

Status | Response body spec
---|---
200 | [Theme](models.md#theme)
404 | `{"error": 404, "message": "Theme '$themeId' not found"}` <br> If `themeId` does not identify a theme.
404 | `{"error": 404, "message": "Published theme '$themeId' not found"}` <br> If published theme is requested and yet `themeId` is unpublished.
403 | `{"error": 403, "message": "Insufficient permissions"}` <br> If the SID is not authenticated.
401 | [Invalid credentials](responses.md#invalid-credentials)
400 | [Bad request](responses.md#bad-request)

## Update theme draft

Updates the theme by ID.

Request property | Spec
---|---
Action                                | PUT /themes/[`themeId`](models.md#id-types)
[`SID` header](request.md#sid-header) | Must be a SID that is authorized for the specific owner.
Body model                            | [Theme](models.md#theme)
Scala                                 | `class UpdateThemeDraft`

Status | Response body spec
---|---
200 | [Theme](models.md#theme)
404 | `{"error": 404, "message": "Theme '$themeId' not found"}` <br> If `themeId` does not identify a theme.
404 | `{"error": 404, "message": "VFO Org '$ownerId' not found"}` <br> If `ownerId` value does not identify an org.
403 | `{"error": 403, "message": "Insufficient permissions"}` <br> If the SID is not authorized for the specific owner.
401 | [Invalid credentials](responses.md#invalid-credentials), if `SID` is not for a user.
400 | [Bad request](responses.md#bad-request)

## Publish theme

Marks current draft version as published.

Request property | Spec
---|---
Action                                | POST /themes/[`themeId`](models.md#id-types)/publish
[`SID` header](request.md#sid-header) | Must be a SID that is authorized for the specific owner.
Body model                            | _no body_
Scala                                 | `class PublishTheme`

Status | Response body spec
---|---
200 | _no body_
404 | `{"error": 404, "message": "Theme '$themeId' not found"}` <br> If `themeId` does not identify a theme.
403 | `{"error": 403, "message": "Insufficient permissions"}` <br> If the SID is not authorized for the specific owner.
401 | [Invalid credentials](responses.md#invalid-credentials), if `SID` is not for a user.
400 | [Bad request](responses.md#bad-request)
