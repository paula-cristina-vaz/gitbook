<!--title:start-->
# Userinfo Endpoint
<!--title:end-->
<!--shortdesc:start-->
Retrieve user claims.
<!--shortdesc:end-->

<!--desc:start-->

| Parameters      | Description          |
|-----------------|----------------------|
| `Authorization` | Bearer access token. |


## Example 

```http
GET /connect/userinfo
Authorization: Bearer eyJ0eXAiOiJKV....eyJpc3MiOiJodHRwOi8vZ.S4Rg-em8y7
```

The following example shows a response to a  `/connect/userinfo` request:

```json
HTTP/1.1 200 OK
Content-Type: application/json

{
    "sub": "248289761001",
    "name": "Bob Smith",
    "given_name": "Bob",
    "family_name": "Smith"
}
```

The following example shows how to retrieve user claims using [IdentityModel client library](https://identitymodel.readthedocs.io/en/latest/)

```csharp
using IdentityModel.Client;

var client = new HttpClient();

var response = await client.GetUserInfoAsync(new UserInfoRequest
{
    Address = disco.UserInfoEndpoint,
    Token = token
});
```

<!--desc:end-->
