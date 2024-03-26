<!--title:start-->
# Token Examples
<!--title:end-->
<!--shortdesc:start-->
Example of the two returned tokens when authenticating with a guest user.
<!--shortdesc:end-->

<!--desc:start-->

## Back-office token example

Example of tokens obtained from the back-office service token provider.

### Authentication flow: `password`

```json
{
  "alg": "RS256",
  "kid": "1B394C64CBB3F7D224649EB649FCA3FD3B948DE3",
  "typ": "JWT",
  "x5t": "GzlMZMuz99IkZJ62Sfyj_TuUjeM"
}.{
  "nbf": 1637593582,
  "exp": 1637617582,
  "iss": "http://farfetch.com",
  "aud": [
    "http://farfetch.com/resources",
    "api"
  ],
  "client_id": "farfetch_digitalAsset",
  "client_uid": "10004",
  "client_tenantId": "10000",
  "sub": "145814",
  "auth_time": 1637593582,
  "idp": "idsrv",
  "tenantId": "10000",
  "uuid": "ec4e3f87-75fb-1923-86f6-989a18c804d7",
  "email": "happy.staff.member@farfetch.com",
  "impersonate": "true",
  "role": [
    "Commercial Dashboard",
    "Operational Dashboard",
    "PreOrderSettingsManager",
    "ProductAdmin",
  ],
  "jti": "b84bbcb291de1e84b514e1f542b60d78",
  "scope": [
    "stockpoints",
    "api"
  ],
  "amr": [
    "password"
  ]
}.[Signature]
```

### Authentication flow: `client_credentials`

```json
{
  "alg": "RS256",
  "kid": "1B394C64CBB3F7D224649EB649FCA3FD3B948DE3",
  "typ": "JWT",
  "x5t": "GzlMZMuz99IkZJ62Sfyj_TuUjeM"
}.{
  "nbf": 1637580710,
  "exp": 1637595110,
  "iss": "http://farfetch.com",
  "aud": [
    "http://farfetch.com/resources",
    "api"
  ],
  "client_id": "farfetch",
  "client_uid": "10000",
  "client_tenantId": "10000",
  "jti": "4d4d15357bc30e44472406f6d56a88f0",
  "scope": [
    "api"
  ]
}.[Signature]
```

## Token examples for guest user 

```json
{
    "alg": "RS256",
    "kid": "1B394C64CBB3F7D224649EB649FCA3FD3B948DE3",
    "typ": "JWT",
    "x5t": "GzlMZMuz99IkZJ62Sfyj_TuUjeM"
  }.{
    "nbf": 1637236221,
    "exp": 1637248221,
    "iss": "http://farfetch.com",
    "aud": [
      "http://farfetch.com/resources",
      "api"
    ],
    "client_id": "farfetch",
    "client_uid": "10000",
    "client_tenantId": "10000",
    "client_guest": "5000009680399253",
    "jti": "35f188bff1e9357838716a247d9cdc61",
    "scope": [
      "api"
    ]
  }.[Signature]
```

<!--desc:end-->
