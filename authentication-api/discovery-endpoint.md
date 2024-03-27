<!--title:start-->
# Discovery Endpoint
<!--title:end-->
<!--shortdesc:start-->
Discover scopes, claims, grant types, etc.
<!--shortdesc:end-->
<!--desc:start-->

Use the discovery endpoint to retrieve metadata about  Security Token Services (FSTS).

| FSTS         | URI                                                            |
|------------- | -------------------------------------------------------------- |
| Front-oice | <https://auth..net/.well-known/openid-configuration>   |
| Back-oice  | <https://authbo..net/.well-known/openid-configuration> |


## Example

The following example shows how to retrieve front-oice FSTS onpenid configuration using [IdentityModel client library](https://identitymodel.readthedocs.io/en/latest/)

```csharp
var client = new HttpClient();

var disco = await client.GetDiscoveryDocumentAsync("https://auth..net/.well-known/openid-configuration");
```
<!--desc:end-->

