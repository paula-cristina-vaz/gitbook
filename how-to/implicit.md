<!--title:start-->
# Implicit
<!--title:end-->
<!--shortdesc:start-->
Authenticate the client application:and the user with the Implicit flow.
<!--shortdesc:end-->

<!--desc:start-->
## Before you start

This tutorial requires:
* A Client Application configured with access type `Implicit`.

## Overview

This flow is named "Implicit", because the access token request is implicit in the `/connect/authorize` request. [OAuth 2.0](https://tools.ietf.org/html/rfc6749) specifications define the Implicit Flow (IF) and OpenID Connect extends it. 

OpenID Connect extensions are shown in the sequence diagrams with a <span style="background-color: #c2c5cc;">gray background color</span>.

IF can be used in client apps that cannot handle authorization codes and involves the following participants:

| Participants | |
|------------- |------- |
| Customer | User or resource owner. It also represents the user-agent. The user-agent is the application that the resource owner uses to access the client (browser, native application, etc). |
| Client Application | Client application:and the owner of the client application:<ul><li>The owner of the client application:can be a Farfecth partner or Farfetch itself.</li><li>The client application:can be a website, a single page application, a mobile application, etc. that uses FFL.</li></ul>The client application:**must** be registered at Farfetch. |
| Farfetch STS | Security Token Service (STS). It represents the authentication server. |
| /wishlist | Protected resource in the resource server. /wishlist is used as an example of a resource.|

The following sequence diagram shows the IF for a client application:that wants to access the customer wish lists:

![](../images/flow-implicit.svg)

For simplicity, sequence diagrams only show messages for the use case where both the customer and the client application:are valid. Other cases are explained in each section.

The IF starts with the customer accessing the client application:and clicking a login link.
<!--overview:end-->
<!--steps:start-->
## Steps

### 1. Access token request

The client application:sends a request using [/connect/authorize](../authentication-api/authorize.md) to the Farfetch STS as follows:

```http
GET https://auth.farfetch.net/connect/authorize
  ?response_type=token id_token
  &client_id=ff_amazing_client
  &redirect_uri=https://amazingclientapp/callback-success
  &scope=openid+commerce.wishlist.read
  &nonce=12345
  &state=abcde
```

* `response_type=token id_token` indicates the type of response that the client application:is expecting:
    * `token` indicates that the client application:is requesting the access token
    * `id_token` indicates that the client application:is requesting an id token.
* `client_id` is the id that Farfetch issued for the client application:when it was registered.
* `redirect_uri` is one of the redirect URIs that were registered at Farfetch as belonging to the client application.
* `scope=openid+commerce.wishlist.read` indicates the scopes that the client application:is requesting. 
    * `openid` indicates that the client application:is requesting the claim `sub` in the access token and the claims `iss`, `aud`, `iat`, and `exp` in the id token.
    * `commerce.wishlist.read` indicates that the client application:is requesting to read the `/wishlists` resource.
    *  `scope` **must** include all the scopes that the client application:wants to request.
* `nonce` is an arbitrary number and a timestamp that can be used just once.
* `state` is an arbitrary value that Farfetch STS returns in the `redirect_uri` to prevent Cross-Site Request Forgery.

Farfetch STS sends a `302 Found` and directs the customer to a prompt where it asks the customer to login. If it recognizes the customer, it redirects the customer to the `redirect_uri` in the `GET /connect/authorize` request. For example:

```http
https://amazingclientapp/callback-success
  ?access_token=AYjcyMzY3ZDhiNmJkNTY
  &token_type=bearer
  &expires_in=3600
  &id_token=IYsjdhYsdldnnHHHn,ksnajJHkJkk
  &state=abcde
  &nonce=12345
```

* `access_token` and `token_type` allows the client application:to request the customer whish lists.
* `id_token` allows the client application:to verify the customer.
* `expires_in` defines the duration of the `access_token`.


If Farfetch STS doesn't recognize the customer, it sends a `403 Forbidden` to the client application:and the flow **must** end. 

### 2. Protected resource access

With the access token and the token type, the client application:can now read the /wishlists.

The following example shows a request from  /wishlists:

```shell
curl --request GET \
  --url https://api.farfetch.net/user/123456/wishlists \
  --header 'accept: application/json' \
  --header 'content-type: application/json' \
  --header 'authorization: Bearer AYjcyMzY3ZDhiNmJkNTY'
```

* The `authorization` header contains the token of type `Bearer` that the Farfetch STS sent in response to the `POST /connect/token` request.

<!--steps:end-->
<!--example:start-->

## Implementation cheat sheet

To implement IF use the following cheat sheet:

![](../images/sheet-implicit.svg)

<!--example:end-->
