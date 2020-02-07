
# Gadget endpoints

## List gadget manifests

Retrieve all gadget manifests matching given criteria.

Request property | Spec
---|---
Action                                | GET /gadgets
[`SID` header](request.md#sid-header) |
`user` param                          | Optional parameter. Only useful value is `me`. See below for details.
`catalog` param                       | Optional parameter. Must be a [gadget catalog](models.md#gadget-catalog). Defaults to `approved` if omitted.
Body model                            | _no body_
Scala                                 | `object GetGadgetManifests`

The gadget manifests are retrieved in groups based on several criteria. First, if `user=me` is specified,
then all gadget manifests created by the user identified by `SID` are obtained (if `SID` is a partner key,
then this group is empty). Otherwise, if `user` has any other value, or is unspecified, then _all_ gadget
manifests that don't belong to an org are obtained. Second, all gadget manifests belonging to the orgs
that the `SID` user is a member of are collected. These groups are then merged together, keeping only the
manifests having the specified `catalog`. The resulting set of manifests is returned in the response.

Status | Response body spec
---|---
200 | array of [gadget manifest](models.md#gadget-manifest) (**not paginated**)
401 | [Invalid credentials](responses.md#invalid-credentials)

## Get gadget file

Download the file at the given `filePath` from the gadget project bundle specified by `devKey`, `gadgetName`,
and `gadgetVersion`.

Request property | Spec
---|---
Action                                    | GET /gadgets/[`devKey`](models.md#developer-key)/[`gadgetName`](models.md#slug)/[`gadgetVersion`](models.md#gadget-version)/`filePath`
[`SID` header](request.md#sid-header)     | None.
[`Range` header](request.md#range-header) |
Body model                                | _no body_
Scala                                     | `class GetGadgetFile`

Status | Response body spec
---|---
404 | `{"error": 404, "message": "Gadget '$devKey@$gadgetName/$gadgetVersion' not found"}` <br> If there is no gadget identified by `devKey`, `gadgetName,` and `gadgetVersion`.
404 | `{"error": 404, "message": "File '$filePath' not found"}` <br> If there is no file at `filePath` in the gadget's file bundle.
    | [File content](responses.md#file-content)

## Get gadget manifest

Request property | Spec
---|---
Action | GET /gadgets/[`gadgetId`](models.md#id-types)
Scala  | `class GetGadgetManifest`

## Update gadget status

Request property | Spec
---|---
Action | POST /gadgets/[`gadgetId`](models.md#id-types)/status
Scala  | `class ReplaceGStatus`

## Approve gadget

Request property | Spec
---|---
Action | POST /gadgets/[`gadgetId`](models.md#id-types)/approval
Scala  | `class ReplaceGStatus`

## Reject gadget

Request property | Spec
---|---
Action | POST /gadgets/[`gadgetId`](models.md#id-types)/rejection
Scala  | `class ReplaceGStatus`

## Get gadget source bundle

Request property | Spec
---|---
Action | GET /gadgets/bundle/[`gadgetId`](models.md#id-types)/sources.zip
Scala  | `class GetBundle`

## Get compiled gadget bundle

Request property | Spec
---|---
Action | GET /gadgets/bundle/[`gadgetId`](models.md#id-types)/compiled.zip
Scala  | `class GetCompiled`

## Get gadget bundle manifest

Request property | Spec
---|---
Action | GET /gadgets/bundle/[`gadgetId`](models.md#id-types)
Scala  | `class GetGadgetManifest`

## Get gadget manifest by type

Request property | Spec
---|---
Action | GET /gadgets/[`devKey`](models.md#developer-key)/[`gadgetName`](models.md#slug)/[`gadgetVersion`](models.md#gadget-version)/manifest
Scala  | `class GetGadgetManifestByUGV`

## Get gadget source bundle by type

Request property | Spec
---|---
Action | GET /gadgets/[`devKey`](models.md#developer-key)/[`gadgetName`](models.md#slug)/[`gadgetVersion`](models.md#gadget-version)/code.zip
Scala  | `class GetGadgetZip`

## Get compiled gadget bundle by type

Request property | Spec
---|---
Action | GET /gadgets/[`devKey`](models.md#developer-key)/[`gadgetName`](models.md#slug)/[`gadgetVersion`](models.md#gadget-version)/compiled.zip
Scala  | `class GetGadgetZip`

## Create gadget

Request property | Spec
---|---
Action | POST /gadgets
Scala  | `object AddGadgetProject`

Status | Response body spec
---|---
400 | `{"error": 404, "message": "$VERSION is not a semantically valid version"}` <br> if the supplied version does not conform to the SemVer [specification](http://semver.org/).

