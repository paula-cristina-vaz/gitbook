<!--title:start-->
# Introspect Endpoint
<!--title:end-->
<!--shortdesc:start-->
Retrieve metadata about a token.
<!--shortdesc:end-->
<!--desc:start-->

Discover if a token is still active.

| Parameter | Description                        |
| --------- | ---------------------------------- |
| `token`   | Token that you want to introspect. |


<table>
<tr><th>Response Code</th><th></th></tr>
<tr><td>200 OK   </td><td>

```json
{
   "active": true,
   "sub": "123"
}
```

Unknown or expired tokens return `"atcive": false`.

</td></tr>
<tr><td>400</td><td>Invalid request.</td></tr>
<tr><td>401</td><td>Unauthorized.</td></tr>
</table>

## Example

```http
POST /connect/introspect
Authorization: Basic 2YotnFZFEjr1zCsicMWpAA

token=tGzv3JOkF0X...G5Qx2TlKWIA
```

The following example shows a response to a  `/connect/introspect` request:

```json
HTTP/1.1 200 OK
Content-Type: application/json

{
    "active": true,
    "sub": "123"
}

```

The following example shows how to introspect a token using [IdentityModel client library](https://identitymodel.readthedocs.io/en/latest/)


```csharp
using IdentityModel.Client;

var client = new HttpClient();

var response = await client.IntrospectTokenAsync(new TokenIntrospectionRequest
{
    Address = "https://auth.farfetch.net/connect/introspect",
    ClientId = "awesome_client",
    ClientSecret = "awesome_client_secret",

    Token = accessToken
});
```

<!--desc:end-->
