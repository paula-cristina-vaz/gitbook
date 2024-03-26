<!--title:start-->
# Revocation Endpoint
<!--title:end-->
<!--shortdesc:start-->
Revoke issued tokens to end sessions.
<!--shortdesc:end-->

<!--desc:start-->

| Parameters              |  Description |
|------------------------ |---------------------------------------- |
| `token` | **Required** - Token to revoke.|
| `token_type_hint` | One of the following:<ul><li>access_token</li><li>refresh_token</li></ul>.|

## Example

```httpm
POST https://auth.farfetch.net/connect/revocation HTTP/1.1
Host: server.example.com
Content-Type: application/x-www-form-urlencoded
Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW

token=...&token_type_hint=refresh_token
```


The following example shows how to revoke a token using [IdentityModel client library](https://identitymodel.readthedocs.io/en/latest/)


```csharp
using IdentityModel.Client;

var client = new HttpClient();

var result = await client.RevokeTokenAsync(new TokenRevocationRequest
{
    Address = "https://auth.farfetch.net/connect/revocation",
    ClientId = "ff_amazing_client",
    ClientSecret = "ff_amazing_client_secret",

    Token = token
});
```

<!--desc:end-->

