<!--title:start-->
# Client Credentials
<!--title:end-->
<!--shortdesc:start-->
Authenticate your client application with client credentials.
<!--shortdesc:end-->
<!--desc:start-->
## Overview

This flow is named "Client Credentials", because the client application obtains the Access Token using only its client credentials.

[OAuth 2.0](https://tools.ietf.org/html/rfc6749) specifications define the Client Credentials Flow (CCF)

CCF is for client apps authentication only. CCF involves the following participants:

| Participants | |
|------------- |------- |
| Client Application | Client application. The client application is an application that interacts with Farfetch Platform without user intervention. For example, a client application that does stock synchronization every night. <br/><br/>The client application **must** be registered at Farfetch. |
| Farfetch STS | Security Token Service (STS). It represents the authentication server. |
| /stocks | Protected resource in the resource server. /stocks is used as an example of a resource.|

The following sequence diagram shows the CCF for a client application that wants to access the stocks:

![](../images/flow-client-credentials.svg)

For simplicity, sequence diagrams only show messages for the use case where the client application is valid. Other cases are explained in each section.

CCF starts with the execution of the client application.
<!--overview:end-->
<!--steps:start-->
## Steps

### 1. Access token request

The client application sends a request using `/connect/token` to the Farfetch STS as follows:

```shell
curl --request POST \
    --url 'https://authbo.farfetch.net/connect/token' \
    --header 'Content-Type: application/x-www-form-urlencoded' \
    --header 'accept: application/json' \
    --data 'grant_type=client_credentials' \
    --data 'client_id=ff_amazing_client' \
    --data 'client_secret=amazing_client_secret' \
    --data 'scope=stocks' 
```

* `grant_type=client_credentials` indicates the type of flow the client application is using.
* `client_id` is the id that Farfetch issued for the client application when it was registered.
* `client_secret` is the client application password that Farfetch issued for the client application when it was registered.
* `scope=stocks` indicates the scopes that the client application is requesting. 

After receiving the `/connect/token` request, Farfetch STS verifies the credentials and the scope of the client application. If the client credentials and the scope are valid, it sends a `200 OK` to the client application with the `access_token`. Otherwise, it ends the flow with a `400 Bad Request`.

```json
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: no-store
Pragma: no-cache

{
  "access_token": "AYjcyMzY...3ZDhiNmJkNTY",
  "token_type": "Bearer",
  "expires_in": 2400
}
```

* `access_token` and `token_type` allow the client application to request stocks.
* `expires_in` indicates that the `access_token` is valid for `2400` seconds.

Note that Farfetch STS doesn't provide a `refresh_token` for the CCF.

### 2. Protected resource access

With the access token and the token type, the client application can now access /stocks.

The following example shows a request from /stocks:

```shell
curl --request GET \
  --url https://api.farfetch.net/stocks \
  --header 'accept: application/json' \
  --header 'content-type: application/json' \
  --header 'authorization: Bearer AYjcyMzY3...ZDhiNmJkNTY'
```

* The `authorization` header contains the token of type `Bearer` that the Farfetch STS sent in response to the `POST /connect/token` request.

<!--steps:end-->
<!--example:start-->
## Implementation cheat sheet

To implement IF use the following cheat sheet:

![](../images/sheet-client-credentials.svg)

<!--desc:end-->
