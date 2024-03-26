<!--title:start-->
# Token Exchange Flow
<!--title:end-->
<!--shortdesc:start-->
Request the exchange of access tokens to delegate permissions on a trusted client application.
<!--shortdesc:end-->
<!--desc:start-->

## Before you start

This tutorial requires:

* A Client Application configured with access type `Token Exchange`.
* An actor configured with access type `Client Credentials`.

## Overview

This flow is named "Token Exchange", because Farfetch STS issues a second access token that allows a resource A to access a resource B on behalf of the customer and the client application. The client application delegates in resource A the necessary permissions that allow resource A to act on behalf of the customer. The access token claims inform Resource B who is the customer, the client application and who is the delegate that is acting on behalf of the customer and the client application.

**Delegation** works as follows:

1. The customer successfully logs in to the client application.
2. The client application obtains an access token.
3. The client application makes a request to Resource A.
4. Resource A obtains an access token.
5. Resource A requests Farfetch STS to issue a new token with the permissions that allow Resource A to act on behalf of the Customer.
6. With the new access token, Resource A makes requests to Resource B on behalf of the Customer.

The Token Exchange flow involves the following participants:

| Participants | |
|------------- |------- |
| Customer | User or resource owner. |
| Client application | Application where the customer authenticates and that interacts with Farfetch Platform.  |
| Resource A | The resource that will act on behalf of the Customer to get information from Resource B.|
| Resource B | The resource that contains the requested information. |
| Farfetch STS | Farfetch Security Token Service (STS). It represents the authentication server. |

The following sequence diagram shows the Token Exchange flow for a resource that wants to access a second resource on behalf of the customer:

![](../images/flow-token-exchange.svg)

For simplicity, sequence diagrams only show messages for the use case where the client application is valid. Other cases are explained in each section.


## Steps

### 1. Request access tokens

The customer logs in to the client application. The client application proceeds as follows:

1. Requests an access token using the pre-defined authentication flow. For example, authorization code flow with PKCE.
2. With the access token makes a request to Resource A.

After receiving the request from the client application, Resource A requests an access token using the client credentials flow.

In this flow:
* The client application is the subject.
* Resource A is the actor.

### 2. Exchange the access token

Resource A sends a request using [/connect/token](../authentication-api/token.md) to Farfech STS as follows:

```shell
  curl --location --request POST \
    --url 'https://auth.farfetch.net/connect/token' \
    --header 'Accept: application/json' \
    --header 'Content-Type: application/x-www-form-urlencoded' \
    --data-urlencode 'grant_type=urn:ietf:params:oauth:grant-type:token-exchange' \
    --data-urlencode 'resource=https://api.farfecth.net/resourceb/' \
    --data-urlencode 'client_id=ff_amazing_client' \
    --data-urlencode 'subject_token=tGzv3JOkF0...XG5Qx2TlKWIA' \
    --data-urlencode 'subject_token_type=urn:ietf:params:oauth:token-type:access_token' \
    --data-urlencode 'actor_token=2YotnFZFEj...r1zCsicMWpAA' \
    --data-urlencode 'actor_token_type=urn:ietf:params:oauth:token-type:access_token' \
    --data-urlencode 'scope=resourceb.read respourceb.write' \
```

* `grant_type=urn:ietf:params:oauth:grant-type:token-exchange` indicates the type of flow the Resource A is using.
* `resource` indicates the Resource B.
* `client_id` is the id that Farfetch issued for the client application when it was registered.
* Subject:
  - `subject_token` is the access token that identifies the customer and the client application.
  - `subject_token_type` indicates the token type. It **must** be `urn:ietf:params:oauth:token-type:access_token`.
* Actor:
  - `actor_token` is the access token that identifies the Resource A.
  - `actor_token_type` indicates the token type. It **must** be `urn:ietf:params:oauth:token-type:access_token`.
* `scope` indicates the scopes that the Resource A is requesting to access Resource B on behalf of the customer and the client application.
  

After receiving the `/connect/token` request, Farfetch STS verifies the credentials and the access tokens. If it recognizes the customer, the client application and the actor, it sends a `200 OK` to Resource A with the `access_token` containing the new scopes.

```json
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: no-cache, no-store

{
    "token_type": "Bearer",
    "expires_in": 3600,
    "access_token": "aHGF6ghjYh...r1zCsicMWpAA",
    "scope": "resoruceb.read respourceb.write",
    "issued_token_type": "urn:ietf:params:oauth:token-type:access_token"
}
```
* `access_token` and `token_type` allow the Resource A to request Resource B information on behalf of the customer and the client application.

The following code shows an example of the claims in the access token.

```json
 {
   "aud":"https://consumer.example.com",
   "iss":"https://issuer.example.com",
   "exp":1443904177,
   "nbf":1443904077,
   "sub":"customer@farfetch.com",
   "client_id": "ff_amazing_client",
   "scope": "resoruceb.read respourceb.write",
   "act":
   {
     "sub":"admin@farfetch.com",
     "client_id": "ff_amazing_actor"
   }
 }

```
### 3. Make a request on behalf of the customer

Resource A sends a request to Resource B on behalf of the customer.

```shell
  curl --location --request GET \
    --url 'https://api.farfecth.net/resourceb/12345' \
    --header 'Authorization: Bearer aHGF6ghjYh...r1zCsicMWpAA' \
```
<!--desc:end-->
