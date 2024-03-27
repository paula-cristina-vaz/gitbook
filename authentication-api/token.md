<!--title:start-->
# Token Endpoint
<!--title:end-->
<!--shortdesc:start-->

Requests an access token using the back-end channel to  Security Token Service (STS). 
<!--shortdesc:end-->
<!--desc:start-->

| Parameters              | Type | Description |
|------------------------ |-------------------- | -------------------- |
| `client_id` | Integer<br/>Required |The client identifier that  issues when the client makes the registration at .|
| `code` | String<br/>Required if `grant_type=authorization_code` | The code that  STS issues in response to a `GET /connect/authorize` request.|
| `grant_type` | Enumerate<br/>Required |One of the following <ul><li>`AliPay`</li><li>`DeviceToken`</li><li>`Facebook`</li><li>`GuestUser`</li><li>`Impersonate`</li><li>`OpenId`</li><li>`WeChat`</li><li>`WeChatMini`</li><li>`authorization_code`</li><li>`client_credentials`</li><li>`ica_password`</li><li>`implicit`</li><li>`password`</li><li>`refresh_token`</li><li>`urn:ietf:params:oauth:grant-type:device_code`</li><li>`urn:ietf:params:oauth:grant-type:token-exchange`</li></ul>|
| `redirect_uri` | String<br/>Required if `grant_type=authorization_code`| URI to where  redirects the client application after a successful authentication of the customer.<br/><br/>**Note**: It must match exactly one of the URIs registered for the client application at . |
| `refresh_token` |String<br/>Required if `grant_type=refresh_token` | The refresh token to request another access token.|
| `client_secret` | String<br/>Optional |Client secret either in the post request body or as a basic authentication header. |
| `scope` | String<br/>Optional |One or more [ registered scopes](https://<authorization-servcer-url>/.well-known/openid-configuration). If you do not specify `scope`,  STS issues a token for all explicitly allowed scopes.|
| `code_verifier` | String<br/>Optional |The PKCE proof key that  STS issues in an [authorization code flow with PKCE](../how-to/authorization-code-with-pkce.md). |

## Token exchange parameters

| Parameters              | Type | Description |
|------------------------ |-------------------- | -------------------- |
| `grant_type` | Enumerate<br/>Required | **Must** have value `urn:ietf:params:oauth:grant-type:token-exchange`. |
| `resource` | String<br/>Optional | URI that indicates the target resource where the client intend to use the requested security token. |
| `audience` | String<br/>Optional | The logical name of the target resource where the client intend to use the requested security token. |
| `scope` | String<br/>Optional |One or more registered scopes. If you do not specify `scope`,  STS issues a token for all explicitly allowed scopes.|
| `requested_token_type` | Enumerate<br/>Optional | One of the following:<ul><li>`urn:ietf:params:oauth:token-type:access_token` - Indicates that the token is an OAuth 2.0 access token issued by the given authorization server.</li></ul> | 
| `subject_token` | JWT<br/>Required| The security token that represents the identity of the subject on behalf of whom the request is being made. | 
| `subject_token_type` |  Enumerate<br/>Optional | One of the following:<ul><li>`urn:ietf:params:oauth:token-type:access_token` - Indicates that the token is an OAuth 2.0 access token issued by the given authorization server.</li></ul> |
| `actor_token` | JWT<br/>Optional| The security token that represents the identity of the actor that acts on behalf of subject.  | 
| `actor_token_type` |  Enumerate<br/>Optional | One of the following:<ul><li>`urn:ietf:params:oauth:token-type:access_token` - Indicates that the token is an OAuth 2.0 access token issued by the given authorization server.</li></ul> |


## Token response 

| Response Code | |
|---- |---- |
| 200 OK | [Token Response](#token-response) |


The following table lists the properties in the response:

| Property | Type | Description|
|---- |---- |----|
| `access_token` | JWT<br/>Required |The access token that  STS issues. |
| `token_type` | Enumerate<br/>Required |Token type. One of the following:<ul><li>`Bearer`</li><li>`Basic`</li></ul>|
| `expires_in` | Integer<br/>Required | The lifetime in seconds of the `access_token`. |
| `refresh_token` | JWT<br/>Optional |The refresh token that can be used to obtain a new access token without asking for a new customer login.<br/><br/>**Note**:  STS doesn't issue refresh tokens for the implicit authentication flow.|
| `scope` | String<br/>Required if the scopes associated to the client application at  STS are dierent from the ones in request `/connect/authorize`. | Returns the scopes associated to the client application in  STS. |


```http
   {     
       "access_token":"2YotnFZFEj...r1zCsicMWpAA",     
       "token_type":"Basic",     
       "expires_in":3600,     
       "refresh_token":"tGzv3JOkF0...XG5Qx2TlKWIA",
       "scopes": "read"
    }
```

Line breaks were added for readability.


## Examples

### Grant type: authorization code

```shell
curl --request POST \
  --url https://<authorization-servcer-url>/connect/token \
  --header 'accept: application/json' \
  --header 'content-type: application/x-www-form-urlencoded' \
  --data 'client_id=amazing_client' \
  --data 'client_secret=amazing_client_secret' \
  --data 'grant_type=authorization_code' \
  --data 'code=hdh922' \
  --data 'redirect_uri=https://amazingclientapp.net/callback-success'
```

The form-encoding was removed for readability. 

The following code shows an example of the `/connect/token` response:

```json
   HTTP/1.1 200 OK   
   Content-Type: application/json;charset=UTF-8   
   Cache-Control: no-store   
   Pragma: no-cache

   {     
       "access_token":"2YotnFZFEjr1zCsicMWpAA",     
       "token_type":"access_token",     
       "expires_in":3600,     
       "refresh_token":"tGzv3JOkF0XG5Qx2TlKWIA"     
    }
```
Line breaks were added for readability.

The following example shows how to request a token using [IdentityModel client library](https://identitymodel.readthedocs.io/en/latest/)

```c#
using IdentityModel.Client;

var client = new HttpClient();

var response = await client.RequestAuthorizationCodeTokenAsync(new AuthorizationCodeTokenRequest
{
    Address = TokenEndpoint,

    ClientId = "amazing_client",
    ClientSecret = "amazing_client_secret",

    Code = "hdh922",
    CodeVerifier = "ksajdhkf98798sfd987sdf98sdf897",
    RedirectUri = "https://amazingclientapp.net/callback-success"
});
```
<!--desc:end-->
