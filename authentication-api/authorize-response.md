<!--title:start-->
# Authorize Response
<!--title:end-->
<!--shortdesc:start-->
Successful response to `/connect/authorize`.
<!--shortdesc:end-->

<!--desc:start-->

If customer login is successful `/connect/authorize` responds with an authorization code as a query string of the `redirect_uri` using the `applicationx-www-form-urlencoded` format. 

<!--desc:end-->

<!--properties:start-->

| Properties | Type | Description |
|--- |--- |---|
| `code` | String<br/>Required | The authorization code that  STS generates. Note that the authorization code expires in 10 min.<br/><br/>**Attention:** Do not use the authorization code in more than one token request. If you use an authorization code in more than one token request, all your tokens will be revoked. |
| `nonce` | String<br/>Required | If the `nonce` parameter was sent in the `/connect/authorize` request. It **must** be the same `nonce` that the client application sent in the `/connect/authorize` request. |
| `state` | String<br/>Required | If the `state` parameter was sent in the `/connect/authorize` request. It **must** be the same `state` that the client application sent in the `/connect/authorize` request. |
| `code_challenge` | String<br/>Required when using PKCE| The `code_challenge` sent in the `/connect/authorize` request. It **must** be the same `code_challenge` that the client application sent in the `/connect/authorize` request. |
| `code_challenge_method` | Enumerate<br/>Required When using PKCE| The `code_challenge_method` sent in the `/connect/authorize` request. It **must** be the same `code_challenge_method` that the client application sent in the `/connect/authorize` request. |

<!--properties:end-->

<!--example:start-->

## Example

```http
https://client.example.com/cb
   ?code=SplxlOBeZQQYbYS6WxSbIA
   &state=abc&nonce=xyz
```
<!--example:end-->
