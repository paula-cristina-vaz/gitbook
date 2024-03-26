<!--title:start-->
# Authorize Endpoint
<!--title:end-->
<!--shortdesc:start-->

Requests an authorization code or an access token to the Farfetch Security Token Service (STS), depending on the value of the parameter `response_type`.
<!--shortdesc:end-->

<!--desc:start-->


| Parameters | Type | Description |
|------------|------|----------------------------- |
| `client_id` | Integer<br/>Required | The client identifier that Farfetch issues when the client makes the registration at Farfetch.|
| `code_challenge` | String<br/>Required when using PKCE | The `code_challenge` is a code verifier encrypted. |
| `redirect_uri` | String<br/>Required | URI to where Farfetch redirects the client application after a successful authentication of the customer.<br/><br/>**Note**: It **must** match exactly one of the URIs registered for the client application at Farfetch. |
| `response_type` | Enumerate<br/>Required | Type of response that the client application is requesting. It **must** be one of the following values:<ul><li>`code` - it requests an authorization code.</li><li>`token` - it requests an access token.</li><li>`id_token` - it requests an identity token.</li><li>`id_token token` - it requests an access and an identity token.</li><li>`code id_token` - it requests an authorization code and an identity token.</li><li>`code id_token token` - it requests an authorization code, an identity token, and an access token.</li></ul> |
| `scope` |  String<br/>Required | One or more Farfetch available scopes.  <ul><li>[Front-office scopes](https://auth.farfetch.net/.well-known/openid-configuration).</li><li>[Back-office scopes](https://authbo.farfetch.net/.well-known/openid-configuration)</li></ul>Optionally, add the following special scopes:<ul><li>`openid`: if the `response_type` includes `id_token`, you **must** add this scope ro receive the identity scope.</li><li>`offline_access`: add this scope to request a `refresh_token`. </li></ul> |
| `nonce` |  String<br/>Required when using the implicit flow | Code that the client application generates for each `/connect/authorize` request. The client application sends the `nonce` with the `/connect/authorize` request  and Farfetch STS returns the `nonce` in the identity token to prevent replay attacks. Each `nonce` **must** be used only once and **should** be associated to a timestamp. |
| `state` |  String<br/>Optional and recommended | The `state` is a value that client application generates and sends with the `/connect/authorize` request.  Farfetch STS echoes back the `state` to the client application in the token response. When the client application receives the `state` in the token response, it can correlate the response with the request.<br/><br/> The use of the `state` value prevents cross-site request forgery and replay attacks. |
| `acr_values` | Enumerate<br/>Optional | Additional authentication related information. It **must** be one of the following values:<ul><li>Custom value - Farfetch STS returns customized login pages.</li><li>`idp:name_of_idp` - Directs the customer directly to the STS with name `name_of_idp`.</li></ul> |
| `code_challenge_method` | Enumerate<br/>Optional | Method that the client application used to generate the `code_challenge`. It **must** be one of the following values:<ul><li>`plain`  - Indicates that the  `code_challenge` is using plain text.</li><li>`S256` - Indicates the `code_challenge` is hashed with `SHA256`.</li></ul>**Note:** Avoid the code challenge method  `plain`. It's less secure, because the `code_verifier` is sent without any transformation and it can be easily used by an interceptor. |
|`login_hint` |  String<br/>Optional |The phone number or email of the customer. The value of the `login_hint` will pre-fill the username on the login page. |
| `max_age`| Integer<br/>Optional |Allowable elapsed time, in seconds, since the last time the customer was actively authenticated at Farfetch. |
| `prompt` | Optional |The type of customer interaction. It must be one of the following values: <ul><li>`none`</li><li>`consent` - The customer **should** be prompted to give consent.</li><li>`login` - The customer **should** be prompted to login.</li><li>`consent` and `login` - The customer **should** be prompted to login and give consent.</li></ul> |
| `response_mode` | Enumerate<br/>Optional |Indicates how the client application is expecting the response of the `/connect/authorize` request. It **must** be one of the following values: <ul><li>`form_post` - The client application expects the response as a form post.</li><li>`fragment` - The client application expects a fragment encoded redirect</li><li>`query` - The client application expects the response as an HTML query string.</li></ul> **Notes:**<ul><li>If `response_type` is `id_token` or `token`, `response_mode` **must** not be `query`.</li><li>In implicit and hybrid flows, the default value is `fragment`. Otherwise, `form_post`.</li></ul> |
|`ui_locales` |  String<br/>Optional |The display language of the login. Use `ISO 639-1` standard language codes. |


|Response Code| |
|---|---|
|200 OK| [Authorize Response](./authorize-response.md) |



## Example

```http
GET https://auth.farfetch.net/connect/authorize
 ?response_type=code id_token
 &client_id=very_special_client
 &redirect_uri=https://myapp/callback
 &scope=openid read
 &nonce=xyz
 &state=abc
```
The form-encoding was removed and the line breaks added for readability. 

The following example shows a response to a  `/connect/authorize` request:

```http
Location: https://client.example.com/cb
   ?code=SplxlOBeZQQYbYS6WxSbIA
   &state=abc&nonce=xyz
```

> **Attention:** If the returned `state`, `nonce`,  `code_challenge`, or `code_challenge_method`  are not the same as the ones you sent in the `/connect/authorize` request, abort the flow, because you have been attacked.


## Replacement parameters

Replacement parameters are parameters that can replace a query string of required, recommended, and optional parameters. Instead of using a query string the client application can generate a JWT  (JSON Web Token) and send it encoded in the request URL or it can send the address to the location where the JWT is stored.

The following table lists the replacement parameters:

| Parameters | Type | Description |
|------------------------ |--------------------------|----------------------------------- |
|`request`| JWT | A JWT with the required, recommended, and optional parameters defined as claims ([Section 6.1 of Open ID Connect Core 1.0](https://openid.net/specs/openid-connect-core-1_0.html#RequestObject)). |
|`request_uri`| String |A URL referencing a resource that contains a JWT with the required, recommended, and optional parameters defined as claims. |

* You can sign and encrypt the JWT.
* If you send a parameter both in the query string and in a JWT, the value in the JWT supersedes the one in the query string.

The following code shows an example of a request JWT:

```json
{
   "iss": "s6BhdRkqt3",
   "aud": "https://server.example.com",
   "response_type": "code id_token",
   "client_id": "s6BhdRkqt3",
   "redirect_uri": "https://client.2example.org/cb",
   "scope": "openid",
   "state": "af0ifjsldkj",
   "nonce": "n-0S6_WzA2Mj",
   "max_age": 86400,
   "claims":
    {
     "userinfo":
      {
       "given_name": {"essential": true},
       "nickname": null,
       "email": {"essential": true},
       "email_verified": {"essential": true},
       "picture": null
      },
     "id_token":
      {
       "gender": null,
       "birthdate": {"essential": true},
       "acr": {"values": ["browns"]}
      }
    }
 }
```

The following example shows how to request an authorization code using [IdentityModel client library](https://identitymodel.readthedocs.io/en/latest/)

```csharp
var ru = new RequestUrl("https://auth.farfetch.net/connect/authorize");

var url = ru.CreateAuthorizeUrl(
    clientId: "client",
    responseType: "code id_token",
    redirectUri: "https://client.2example.org/cb",
    scope: "openid read");
```

<!--desc:end-->
