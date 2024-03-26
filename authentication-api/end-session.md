<!--title:start-->
# Endsession Endpoint
<!--title:end-->
<!--shortdesc:start-->

Requests a customer logout.
<!--shortdesc:end-->

<!--desc:start-->

| Parameters | Type | Description |
|------------------------ |-------- | ------------ |
| `id_token_hint` | String<br/>Optional |The `id_token` that Farfetch STS issued for the customer. If the client application sends the `id_token_hint` Farfetch STS won't prompt the customer for a logout confirmation. |
| `post_logout_redirect_uri` | String<br/>Optional |URI to redirect the customer after logout. `post_logout_redirect_uri` **must be** a registered URI for the client application. |
| `state` | String<br/>Optional |If the client application sends a valid `post_logout_redirect_uri`, the client application can send a `state`. <br/><br/> The `state` is a value that client application generates and sends with the `/connect/endsession` request.  Farfetch STS echoes back the `state` to the client application with the `post_logout_redirect_uri`. When the client application receives the `state` in the token response, it can correlate the response with the request<br/><br/> The use of the `state` value prevents cross-site request forgery and replay attacks.|

## Example

```http
GET https://auth.farfetch.net/connect/endsession
   ?id_token_hint=eyJhbG...BushKA
   &post_logout_redirect_uri=http://localhost/index.html
```

The following example shows how to end a session using [IdentityModel client library](https://identitymodel.readthedocs.io/en/latest/)

```csharp
var ru = new RequestUrl("https://auth.farfetch.net/connect/end_session");

var url = ru.CreateEndSessionUrl(
    idTokenHint: "eyJhbG...BushKA",
    postLogoutRedirectUri: "http://localhost/index.html");
```

<!--desc:end-->
