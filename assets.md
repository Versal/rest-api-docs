
# Asset endpoints

## Get asset metadata

Retrieves information about the specified asset.

Request property | Spec
---|---
Action                                | GET /assets/[`assetId`](models.md#id-types)
[`SID` header](request.md#sid-header) | None.
Body model                            | _no body_
Scala                                 | `class GetAsset`

If `assetId` does not identify an asset, this endpoint does not fail with a 404. See the
[get asset representation](#get-asset-representation) endpoint for details.

Status | Response body spec
---|---
200 | [Asset](models.md#asset)

## Get asset representation

Retrieves the byte content of the specified asset representation.

Request property | Spec
---|---
Action                                    | GET /assets/[`reprId`](models.md#id-types) **OR** ~~GET /ars/[`reprId`](models.md#id-types)~~
[`SID` header](request.md#sid-header)     | None.
[`Range` header](request.md#range-header) |
Body model                                | _no body_
Scala                                     | `class GetAsset` **OR** ~~`class GetAssetRepresentation`~~

This endpoint is in need of some rethinking. The /assets/`reprId` path is overloading the path
for the [get asset metadata](#get-asset-metadata) endpoint: the code must first assume the path
parameter is an asset ID, and if that lookup fails, then check for a representation. A distinct
path for this endpoint is needed.

That distinct path shouldn't be provided by /ars/`reprId` though, because that was intended to be
a temporary path only. We switched some assets from being served by the API to being served by
CloudFront, but in the process we needed a way to test the mechanism by which the CloudFront URLs
are provided. The `assetUrlTemplate` field of the [course details](models.md#course-details) model
provides clients with a URL template that, when filled in with an asset representation ID,
provides a URL to retrieve that representation. It's now using CloudFront, but during testing,
it used the /ars/`reprId` path.

Status | Response body spec
---|---
200 | [File content](responses.md#file-content)
500 | `{"error": 500, "message": "Unexpected exception: Not Found (Service: Amazon S3; Status Code: 404; ...)}` <br> If `reprId` did not identify an asset representation. This really should have a 404 and not a 500 status code. 

## Patch asset repr

Request property | Spec
---|---
Action | PATCH /assets/[`assetId`](models.md#id-types)/representations/[`reprId`](models.md#id-types)
Scala  | `class PatchAssetRepr`

## Get gadget asset

Request property | Spec
---|---
Action | GET /assets/[`assetId`](models.md#id-types)/[`reprId`](models.md#id-types)
Scala  | `class GetGadgetAsset`

## Put asset

Request property | Spec
---|---
Action | PUT /assets/[`assetId`](models.md#id-types)
Scala  | `class ReplaceAsset`

Status | Response body spec
---|---
200 | `{}` <br> If asset is successfully replaced.
403 | `{"error": 403, "message": "User [$userId] is not an owner of asset [$assetId]"}` <br> If the `SID` user is not an owner of the asset.
401 | [Invalid credentials](responses.md#invalid-credentials), if `SID` is not for a user.
400 | [Bad request](responses.md#bad-request)
    
## List assets

Request property | Spec
---|---
Action | GET /assets
Scala  | `object GetAssets`

## Create asset

Request property | Spec
---|---
Action | POST /assets
Scala  | `object AddAsset`
