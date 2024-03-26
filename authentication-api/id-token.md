<!--title:start-->
# Id Token
<!--title:end-->
<!--shortdesc:start-->

JWT (JSON Web Token) with customer information. 
<!--shortdesc:end-->

<!--properties:start-->
| Claims | Type | Description |
|------- |-----|------- |
| `nbf` |  Integer<br/>Optional | Not-before time and date. Time and date before which the STS **must not** accept the id token. |
| `exp` |  Integer<br/>Optional |Expiration time and date. Time and date after which the STS **must not** accept the id token. |
| `iss` | String<br/>Optional |Issuer. URL of the STS that issued the id token. |
| `aud` |String<br/>Optional |Audience. Resource URLs that the customer owns. |
| `nonce` |String<br/>Optional |Code that the client application generates for each request. The client application sends the `nonce` with the request and Farfetch STS returns the `nonce` in the identity token to prevent replay attacks. Each `nonce` **must** be used only once and **should** be associated to a timestamp. |
| `iat` |  Integer<br/>Optional |Issued at time. Numeric date containing the time at which Farfetch STS issued the id token. |
| `at_hash` |String<br/>Required for `response_type` value `code id_token token` | Access token hash value. The base64url encoding of the left-most half of the hash of the octets of the ASCII representation of the `access_token`, where the hash algorithm used is the hash algorithm used in the `alg` header. |
| `sid` |String<br/>Optional |Security identifier. Unique and immutable identifier of the customer. Farfetch STS associates all properties of the customer. Each customer has its own `sid`. |
| `sub` |  Optional |Numeric identifier of the customer. |
| `auth_time` |String<br/>Integer<br/>Optional |Authentication time of the customer. |
| `idp` |String<br/>Optional |URL of the identity provider. |
| `tenantId` |String<br/>Optional |Numeric identifier of the tenant that owns the client application. |
| `uuid` | String<br/>Optional | Universal unique identifier of the customer. |
| `amr` | Array of Strings<br/>Optional | The authentication method. |

<!--properties:end-->
<!--example:start-->
## Example

```json
{
  "nbf": 1560419480,
  "exp": 1560423080,
  "iss": "http://fftech.info",
  "aud": "farfetch_login_external",
  "nonce": "a_sample_nonce_generated_in_client_to_be_included_in_token",
  "iat": 1560419480,
  "at_hash": "235QrOEnB4ydf_bzACoISQ",
  "sid": "39447b9fbb28dcca957d400b3a4a157a",
  "sub": "30485291",
  "auth_time": 1560415275,
  "idp": "idsrv",
  "tenantId": "10000",
  "uuid": "e2cc9c4d-217e-4ae3-a884-8e611a29e05c",
  "amr": [
    "password"
  ]
}
```

<!--example:end-->
