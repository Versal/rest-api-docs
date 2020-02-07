# Spoof Data Endpoints

Some endpoints need spoof data to work. To simplify writing these endpoints,
a table spoof data has been made and the following endpoint -

## Update Spoof Data

Request property | Spec
---|---
Action | PUT /spoofdata
Body model | Spoof data model (see below)

Status | Response body spec
---|---
200 | OK
401 | {"error":401,"message":"Invalid credentials"} if not partner key

The body model is

```
{"spoofKey":"SPOOF_KEY","spoofValue":"ESCAPED_JSON_PAYLOAD"}
```

where escaped JSON payload is like 

```
{\"name\":\"John\",\"age\":48,\"locations\":[\"San Francisco\",\"Las Vegas\"]}
```

for a JSON payload of 

```
{"name":"John","age":48,"locations":["San Francisco","Las Vegas"]}
```