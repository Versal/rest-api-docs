
# Endpoint documentation

This directory contains documentation for our API endpoints, organized into files based on the
first component of the URI path, e.g. `users.md`, `courses.md`, etc. There are a few extra files too:
- `models.md`: defines all of the data schemas for request and response bodies, and the data types that comprise them.
- `pagination.md`: describes how to traverse paginated responses.
- `request.md`: describes common request properties that would be tedious and error-prone to repeat for every endpoint.
- `responses.md`: describes common responses and their meanings, that would be tedious and error-prone to
  repeat for every endpoint.

## Documenting an endpoint

Throughout this section, we'll be using the [sessions.md](sessions.md) file for examples of each of
the components of well-written endpoint documentation. Please refer to that file when you need more detail. Each
subsection that follows will be about one piece of the documentation, in order from top to bottom.

### File heading

The file should start with a level-one heading based on the name of the file:

```markdown
# Session endpoints
```

### Endpoint heading

Each endpoint should have a level-two heading with a human-readable, English-only name. This allows us to
easily link to specific endpoints with fragment IDs, like the [create session](sessions.md#create-session) endpoint.
Deprecated endpoints should be rendered with a ~~strikethrough~~, as in the second example.

```markdown
## Create session

## ~~Start course~~
```

### Endpoint summary

The heading should be immediately followed by a one-line summary of the endpoint. It needs to be short so
that it can be read while skimming the page. Deprecation should also be indicated here, with **[DEPRECATED]**
before the summary, as in the second example.

```markdown
Create a session for a specified user.

**[DEPRECATED]** Join a course as a learner.
```

### Request properties

The summary should be followed by the _request properties_ table, which provides key facts about making a request to
the endpoint. Formatting it as a table and keeping it close to the endpoint heading means that it will be
able to be read at a glance.

```markdown
Request property | Spec
-----------------|-----
Action                                | POST /sessions
[`SID` header](request.md#sid-header) |
Body model                            | [Request session](models.md#request-session); `expiresIn` optional; either `userId` or `email` must be specified
Scala                                 | `object CreateSession` 
```

Here's a meta-table describing each property:

Request property | Explanation
-----------------|------------
Action                | The HTTP method, URI path, and query parameters, if any. The value should be in normal text, with URI path parameters rendered as `code` and linking to the appropriate type in `models.md`. The format of query parameter values hasn't been decided yet, but this table entry will be updated once it is.
`SID` header          | Requirements that the `SID` header value must satisfy to avoid a 401 response. In most cases this should just be a link to the default requirements, as shown above. If the requirements are different, describe them in the table.
Pagination parameters | If present, specifies that the endpoint is paginated and the behavior can be controlled via request parameters. This should always just be a link to the pagination description; see [list started courses](courses.md#list-started-courses) for an example.
Body model            | A link to the [models.md](models.md) fragment that describes the request body model, followed by endpoint-specific constraints imposed on that model. Notice that the link to the request body model uses a simple filename and fragment ID. We'll cover model documentation a bit later.
Scala                 | The name of the Scala class or object that implements the endpoint.

It's possible that other rows in the table may be needed in some cases, to document other headers, or to provide
details on query parameters. If a row is used often enough it should be added to this guide, in the table above.

### Endpoint description

This section is free-form, and should be used to describe the behavior of the endpoint in detail. Especially important
to include are requirements, constraints, or behaviors that are surprising, and also _reasons_ why the endpoint
works as it does. Here's an example from [create session](sessions.md#create-session):

```markdown
The most direct way to specify a user is with the `userId` field. Alternatively, an `email` field can be
provided, which must contain an email address belonging to a user. If the `userId` does not exist,
or no user can be found with the provided `email`, then the request will fail with a 400 status.

The deprecated `expiresIn` field is no longer used by the API, but will still be validated if provided in
the request body. It is therefore recommended to avoid including it.
```

### Response table

This section describes the responses sent by the endpoint. Again, a table is used to
keep the information compact on the page. Here's the section for our example endpoint:

```markdown
Status | Response body spec
-------|-------------------
201 | [Response session](models.md#response-session)
400 | `{"error": 400, "message": "Missing field: userId or email"}` <br> At least one of `userId` and `email` must be provided.
404 | `{"error": 404, "message": "User '$userId' not found"}` <br> The `userId` field was specified in the request body with an invalid ID.
403 | `{"error": 403, "message": "Insufficient permissions (must be a partner)"}` <br> The provided `SID` is not a partner key, or does not belong to an authenticator or an admin of an org that the specified user is a member of.
401 | [Invalid credentials](responses.md#invalid-credentials)
400 | [Bad request](responses.md#bad-request)
```

Notice that the successful response is in the first row, since it is likely to be searched for frequently. It should
always link to a response body model definition, and can optionally also specify constraints, similar to the request
body model. The other rows should describe error responses, and always in the same format: body schema, `<br>`, explanation.
The final rows in the table are for error responses that many endpoints have in common. To avoid error-prone repetition,
simply link to each response description as shown. Status codes should not be specified, in case they are changed in
the future.

## Documenting types and models

The [`models.md`](models.md) file describes all of the data that is sent in request and response bodies. It's
divided into several sections, which are described below, and link to the corresponding sections in `models.md`.

### [Primitive types](models.md#primitive-types)

Assigns names to the basic (non-composite) data types used in requests and responses. It starts with the "fundamental"
JSON types like [string](models.md#string), [number](models.md#number), and [boolean](models.md#boolean). Any type
that can be described as constraints on one of the JSON types should also be included here, such as [slug](models.md#slug)
or [uuid](models.md#uuid).

### [ID types](models.md#id-types)

Since all of the ID types (e.g., user ID, course ID, asset ID, ...) are very similar to each other, it makes more sense
to document them together in a single table. When linking to an ID type from another place, just use the `#id-types`
fragment ID. The table format should be pretty self-explanatory.

### [Models](models.md#models)

Every JSON object that appears in a request or response body should be documented in this section. Groups of related
models get their own sub-sections here, and if there is more than one model in the group, each one gets its own
sub-sub-section. See [Session](models.md#session) for an example.

Each model is described by listing all of its fields out in a table. Nothing about whether fields are optional or
required should be documented here; that's up to the individual endpoints to specify. Three columns are defined for
each row:

Column | Explanation
-------|------------
**Field**       | The name of the field in the JSON object.
**Type**        | The type of the field value. This can link to anything defined in this file, including primitive types and models.
**Description** | A few remarks on what the field is, any global constraints on it, and hopefully why it is useful.

Here's an example, for the [org](models.md#org) model:

```markdown

### Org

A JSON object with the following fields:

Field | Type | Description
------|------|------------
`id`      | [org ID](#id-types)                | Unique, API-generated identifier.
`title`   | [string](#string)                  | Word or short phrase naming or describing the org.
`orgName` | [slug](#slug)                      | A unique, human-readable user identifier. It is used in the type strings of gadgets created by the org in place of the org ID, if defined.
`users`   | array of [org member](#org-member) | Contains one element for each user belonging to the org.
```

## Cryptography (Hashing) library

To Hash passwords, the bouncy castle library's password based key derivation function is used. Extend the trait
CryptoOps inside the crypto library. See examples of PartnerIDHandler etc. To get a new supersecret seed
(SODIUM_CHLORIDE) do the following in a console

```
import com.versal.restapi.crypto
crypto.newSalt
```

If somebody gives you an ID (say a partner id) and you want to check if its in the database, (assuming no clear
IDs are stored which will be the final state of the hashing / migration process), do the following in a console

```
import com.versal.restapi.crypto.PartnerIDHandler
PartnerIDHandler.hashPartnerKey("MY_GIVEN_KEY")
```

You can look for this hashed key in the hashed_partner table, for example.

## Add a new local partner key

If a developer is running a rest api locally off a prod database dump, and wants to insert a partner key,
open a db session to the underlying database and a sbt scala console

```
import com.versal.restapi.crypto
crypto.newLocalPartnerKeyInserts(ownerId)
```

`ownerId` is a existing user id.

This will give you the new partner key and sql to be run to create it

## Add a new user and partner key for that user

This can be used to seed a new database with no users in it at all -
open a db session to the underlying database and a sbt scala console

```
import com.versal.restapi.support
support.virginUserAndPartnerKey(3, "Donald", "Trump", "trump@maga.org")
```
