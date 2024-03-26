<!--title:start-->
# Resource Owner
<!--title:end-->
<!--shortdesc:start-->
Authenticate the client application and the user with resource owner flow.
<!--shortdesc:end-->

<!--desc:start-->

## Before you start

This tutorial requires:

* A Client Application configured with access type `Resource Owner`.


## Overview
This flow is named "Resource Owner", because the client application obtains the access token using the client credentials and the username and password of the end user. [OAuth 2.0](https://tools.ietf.org/html/rfc6749) specifications define the Resource Owner Flow (ROF)

ROF is for client apps that have an end user and don't have authorization code support.

| Participants | |
|------------- |------- |
| Customer	| User or resource owner. It also represents the user-agent. The user-agent is the application that the resource owner uses to access the client (browser, native application, etc). |
| Client Application | Client application and the owner of the client application:<ul><li>The owner of the client application can be a Farfecth partner or Farfetch itself.</li><li>The client application can be a website, a single page application, a mobile application, etc. that uses FFL.</li></ul>The client application **must** be registered at Farfetch. |
| Farfetch STS | Security Token Service (STS). It represents the authentication server. |
| /wishlist | Protected resource in the resource server. /wishlist is used as an example of a resource.|

The following sequence diagram shows the ROF for a client application that wants to access the customer wish lists:

![](../images/flow-resource-owner.svg)

For simplicity, sequence diagrams only show messages for the use case where both the customer and the client application are valid. Other cases are explained in each section.

The ROF starts with the customer accessing the client application and clicking a login link.


## Steps

### 1. Access token request

The client application sends a request using [/connect/token](../authentication-api/token.md) to the Farfetch STS as follows:

```shell
curl --request POST \
    --url 'https://auth.farfetch.net/connect/token' \
    --header 'Content-Type: application/x-www-form-urlencoded' \
    --header 'accept: application/json' \
    --data 'grant_type=password' \
    --data 'client_id=ff_amazing_client' \
    --data 'client_secret=amazing_client_secret' \
    --data 'username=amazing.user' \
    --data 'password=amazing_user_password' \
    --data 'scope=wishlists.read' 
```

* `grant_type=password` indicates the type of flow the client application is using.
* `client_id` is the id that Farfetch issued for the client application when it was registered.
* `client_secret` is the client application password that Farfetch issued for the client application when it was registered.
* `username` and `password` are the customer username and password.
* `scope=wishlists.read` indicates the scopes that the client application is requesting.

After receiving the `/connect/token` request, Farfetch STS verifies the scope,  and the client and user credentials. If they are valid, it sends a `200 OK` to the client application with the `access_token`. Otherwise, it ends the flow with a `400 Bad Request`.

If Farfetch STS doesn't recognize the customer, it sends a `403 Forbidden` to the client application and the flow **must** end. 

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

* `access_token` and `token_type` allow the client application to request the wishlist of the customer.
* `expires_in` indicates that the `access_token` is valid for `2400` seconds.

Note that Farfetch STS doesn't provide a `refresh_token` for the ROF.

### 2. Protected resource access

With the access token and the token type, the client application can now read the /wishlists.

The following example shows a request from  /wishlists:

```shell
curl --request GET \
  --url https://api.farfetch.net/user/123456/wishlists \
  --header 'accept: application/json' \
  --header 'content-type: application/json' \
  --header 'authorization: Bearer AYjcyMzY3ZDhiNmJkNTY'
```

* The `authorization` header contains the token of type `Bearer` that the Farfetch STS sent in response to the `POST /connect/token` request.



## Implementation cheat sheet

To implement ROF use the following cheat sheet:

![](../images/sheet-resource-owner.svg)
<!--desc:end-->
