
<!--title:start-->
# Authorization Code Flow with PKCE 
<!--title:end-->
<!--shortdesc:start-->
Authenticate the client application and the user using the authorization code flow with PKCE.
<!--shortdesc:end-->
<!--desc:start-->
## Before you start

This tutorial requires:

* A Client Application configured with access type `Authorization Code`.

## Overview

This flow is an extension to the [authorization code flow](authorization-code.md) and is named "Authorization Code Flow with Proof Key for Code Exchange" (ACF-PKCE). 

The client application uses an authorization code to request the access token and an encrypted proof key for code exchange (PKCE, reads as "pixy") to request the authorization code. [OAuth 2.0](https://tools.ietf.org/html/rfc6749) defines the ACF, [RFC 7636](https://tools.ietf.org/html/rfc7636) defines the PKCE, and OpenID Connect extends the ACF-PKCE. OpenID Connect extensions are shown in the sequence diagrams with a <span style="background-color: #c2c5cc;">gray background color</span>.

ACF-PCKE is optimized for web apps and involves the following participants:

| Participants | |
|------------- |------- |
| Customer | User or resource owner. It also represents the user-agent. The user-agent is the application that the resource owner uses to access the client (browser, native application, etc). |
| Client Application | Client application and the owner of the client application:<ul><li>The owner of the client application can be a Farfecth partner or Farfetch itself.</li><li>The client application can be a website, a single page application, a mobile application, etc.</li></ul>The client application **must** be registered at Farfetch. |
| Farfetch STS | Security Token Service (STS). It represents the authentication server. |
| /wishlist | Protected resource in the resource server. /wishlist is used as an example of a resource.|

The following sequence diagram shows the ACF-PKCE for a client application that wants to access the customer wish lists:

![](../images/flow-authorization-code-pkce.svg)

For simplicity, sequence diagrams only show messages for the use case where both the customer and the client application are valid. Other cases are explained in each section.

The ACF-PKCE starts with the customer accessing the client application and clicking a login link.

<!--overview:end-->
<!--steps:start-->
## Steps

### 1. PCKE generation

As the customer opens the client application and clicks a login link, the client application starts the PKCE generation process. The client application has to create a `code_verifier` and generate a `code_challenge`. To generate the `code_challenge`, the client application uses the `code_verifier` and encryption. 

The following table describes the `code_verifier` and `code_challenge` generation process:

| | PKCE generation| |
|---- |--- |--- |
| 1 | Create a `code_verifier`. | Generate a random string with length between `43` and `128` of the following unreserved characters ([Section 2.3 of RFC 3986](https://tools.ietf.org/html/rfc3986#section-2.3)): <ul><li>Alpha (`[A-Z]` and `[a-z]`) </li><li>Digits (`[0-9]`)</li><li>Special (`-`, `.`, `_`, and `~`) </li></ul>For example, `SDIL_Ksdkljlsd239847-sdcfsd~2342342.dfsdfUz`|
|2| Convert the `code_verifier` to ascii codes. | Convert the random string characters to its ascii codes.<br/>For example, `083 068 073 076 095 ... 100 102 085 122 010` |
|3| Encrypt the ascii sequence| Use the [SHA256 algorithm](https://tools.ietf.org/html/rfc4634) to encrypt the ascii sequence.<br/>For example, `2375c8927bc3...5e5a813416f7658` |
|4| Encode the encrypted sequence | Use a Base64URL encoder to encode the encrypted sequence.<br/>For example, `MjM3NW...MTM0MTZmNzY1OA==` |

After generating the `code_verifier` and the `code_challenge`, the client application can request the authorization code.

> The code challenge is only valid as long as the authorization code lives. If the client application doesn't get a token during this period it has to create a new `code_verifier` and generate the new  `code_challenge`.

### 2. Authorization Code request

The client application sends a request using [/connect/authorize](../authentication-api/authorize.md) to the Farfetch STS as follows:

```http
GET https://auth.farfetch.net/connect/authorize
  ?response_type=code id_token
  &client_id=ff_amazing_client
  &redirect_uri=https://amazingclientapp/callback-success
  &scope=openid+offline_access+commerce.wishlist.read
  &nonce=12345
  &state=abcde
  &code_challenge=MWE4Y2QyNTZlY...NGU4MzM5NjMwYTIxNGUxYTI4Nw
  &code_challenge_method=S256
```

* `response_type=code id_token` indicates the type of response that the client application is expecting:
    * `code` indicates that the client application expects an authorization code.
    * `id_token` indicates that the client application expects an id token.
* `client_id` is the id that Farfetch issued for the client application when it was registered.
* `redirect_uri` is one of the redirect URIs that were registered at Farfetch as belonging to the client application.
* `scope=openid+offline_access+commerce.wishlist.read` indicates the scopes that the client application is requesting.
    * `openid` indicates that the client application is requesting the claim `sub` in the [access token](../authentication-api/access-token.md) and the claims `iss`, `aud`, `iat`, and `exp` in the [id token](../authentication-api/id-token.md).
    * `offline_access` indicates that the client application is requesting a refresh token.
    * `commerce.wishlist.read` indicates that the client application is requesting to read the `/wishlists` resource.
    *  `scope` **must** include all the scopes that the client application wants to request.
* `nonce` is an arbitrary number and a timestamp that can be used just once.
* `state` is an arbitrary value that Farfetch STS returns in the `redirect_uri` to prevent Cross-Site Request Forgery.
* `code_challenge` is the encrypted `code_verifier`.
* `code_challenge_method=S256` indicates that the client application generated the `code_challenge` using SHA256 encryption.

> If you want customized pages for your login, add `acr_values` with the proper value to the `/connect/authorize` request. For example, `acr_values=browns` for Browns customized pages. 

Farfetch STS sends a `302 Found` and directs the customer to a prompt where it asks the customer to login. If it recognizes the customer, it redirects the customer to the `redirect_uri` in the `GET /connect/authorize` request. For example:

```http
https://amazingclientapp/callback-success
    ?code=hdh922
    &code_challenge=MWE4Y2QyNTZlY...NGU4MzM5NjMwYTIxNGUxYTI4Nw
    &code_challenge_method=S256
    &state=abcde
    &nonce=12345
```

* `code` is the authorization code.

The client application compares the received `code_challenge` with the one generated from its `code_verifier`. 
If they match, the client application can use the authorization `code` to request the access token. Otherwise, the flow **must** end.

If Farfetch STS doesn't recognize the customer, it sends a `403 Forbidden` to the client application and the flow **must** end. 

### 3. Access Token request

The client application sends a request using [/connect/token](../authentication-api/token.md) to the Farfetch STS as follows:

```shell
curl --request POST \
  --url https://auth.farfetch.net/connect/token \
  --header 'accept: application/json' \
  --header 'content-type: application/x-www-form-urlencoded' \
  --data 'client_id=ff_amazing_client' \
  --data 'client_secret=amazing_client_secret' \
  --data 'grant_type=authorization_code' \
  --data 'code=hdh922' \
  --data 'redirect_uri=https://amazingclientapplication.net/callback-success' \
  --data 'code_verifier=SDIL_Ksdkljlsd239847-sdcfsd~2342342.dfsdfU'
```

* `client_id` is the id that Farfetch issued for the client application when it was registered.
* `client_secret` is the client application *password* that Farfetch issued for the client application when it was registered.
* `grant_type=authorization_code` indicates the type of flow the client application is using.
* `code` is the authorization code that the client application received in the `redirect_uri`
* `redirect_uri` **must** be the same as the one used in the `GET /connect/authorize` request.
* `code_verifier` is the random string that the client application created to generate the `code_challenge`.

After receiving, `/connect/token` request, Farfetch STS proceeds as follows:

1. Farfetch STS validates the client credentials. 
    * If the client credentials are valid, Farfetch STS uses the `code_verifier` to generate a `code_challenge`. 
    * Otherwise, it ends the flow with a `403 Forbidden`.
3. Compares the generated `code_challenge` with the one received before. 
    * If the code challenges match and they are valid, it verifies the authorization `code`.
    * Otherwise, it ends the flow with a `400 Bad Request`.
5. Farfetch STS validates the authorization `code`.
    *  If the authorization `code` is valid, it generates the access and id tokens and sends a `200 OK` to the client application with the access and id tokens.
    * Otherwise,  it ends the flow with a `400 Bad Request`.


```json
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: no-store
Pragma: no-cache

{
  "access_token": "AYjcyMz...Y3ZDhiNmJkNTY",
  "refresh_token": "RjY2Nj...M5NzA2OWJjuE7c",
  "token_type": "bearer",
  "expires_in": 2400,
  "id_token": "YThshT88...dasjhNmJkNTY"
}
```

* `access_token` and `token_type` allow the client application to request the customer wish lists.
* `id_token` allows the client application to verify the customer.
* `refresh_token` the client application to request another `access_token` without asking the customer to login.

### 4. Protected resource access

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

To implement ACF-PKCE use the following cheat sheet:

![](../images/sheet-authorization-code-with-pkce.svg)
<!--desc:end-->