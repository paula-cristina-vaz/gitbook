<!--title:start-->
# Device Authorization Endpoint
<!--title:end-->
<!--shortdesc:start-->
Request device and user codes.
<!--shortdesc:end-->
<!--desc:start-->
Endpoint `/connect/deviceauthorization`  starts the device authorization flow.

| Parameter   | Description                        |
| ------------| ---------------------------------- |
| `client_id` | **Required** - The client identifier that  issues when the device client makes the registration at . |
| `client_secret` | Client secret either in the post request body or as a basic authentication header.<br/>If the client device configuration requires, add the client secret.|
| `scope` | One or more [ registered scopes](https://auth..net/.well-known/openid-configuration). If you do not specify `scope`,  STS issues a token for all explicitly allowed scopes.|

## Example

```http
POST https://auth..net/connect/deviceauthorization

    client_id=client_tv&
    client_secret=client_tv_secret&
    scope=openid api1
```

The following example shows how to request device and user codes using [IdentityModel client library](https://identitymodel.readthedocs.io/en/latest/)


```csharp
using IdentityModel.Client;

var client = new HttpClient();

var response = await client.RequestDeviceAuthorizationAsync(new DeviceAuthorizationRequest
{
    Address = "https://auth..net/connect/device_authorize",
    ClientId = "client_tv"
});
```
<!--desc:end-->
