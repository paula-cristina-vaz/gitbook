<!--title:start-->
# Access Token
<!--title:end-->
<!--shortdesc:start-->
JWT access token claims.
<!--shortdesc:end-->

<!--desc:start-->

Represents a JWT (JSON Web Token) with security information that allows the client application to access resources in the Farfetch Platform. Client application information appear in the access token as claims. 


| Claims | Type |Description |
|------- |---- | -------- |
| `nbf` | Integer | Not-before time and date. Time and date before which the STS **must not** accept the access token. |
| `exp` | Integer | Expiration time and date. Time and date after which the STS **must not** accept the access token. |
| `iss` | String  |Issuer. URL of the STS that issued the access token. |
| `aud` | Array of String |Audience. List of scopes and resource URLs that the client application can access. |
| `client_id` | String |Alphanumeric identifier of the client application. |
| `client_uid` | Integer | Numeric identifier of the client application. |
| `client_tenantId` | Integer | Numeric identifier of the tenant that owns the client application. |
| `sub` | Integer | Numeric identifier of the customer for the tenant, also known as `TenantUserId`. |
| `auth_time` | Integer | Authentication time of the customer in the tenant. |
| `idp` | URL of the identity provider. |
| `tenantId` |Integer |  Numeric identifier of the tenant that owns the client application. |
| `uuid` | UUID |Universal unique identifier of the customer. |
| `email` | String |Customer email. |
| `scope` | Array of String |Array of scopes granted to the client application. |
| `amr` | Array of String |The authentication method. |

## Example

```json
{
  "nbf": 1562320651,
  "exp": 1562332651,
  "iss": "http://farfetch.net",
  "aud": [
    "http://farfetch.net/resources",
    "commerce.orders.read",
    "commerce.userAddresses.read",
    "commerce.wishlist.read",
    "id.users.read"
  ],
  "client_id": "farfetch_fflogin_ica_test_app",
  "client_uid": "10060",
  "client_tenantId": "10000",
  "sub": "30485486",
  "auth_time": 1562320650,
  "idp": "idsrv",
  "tenantId": "10000",
  "uuid": "a8f88612-b7ff-4b16-b5ce-651b795601a9",
  "email": "testuseraccount2@farfetch.com",
  "scope": [
    "openid",
    "commerce.orders.read",
    "commerce.userAddresses.read",
    "commerce.wishlist.read",
    "id.users.read"
  ],
  "amr": [
    "password"
  ]
}
```

<!--desc:end-->
