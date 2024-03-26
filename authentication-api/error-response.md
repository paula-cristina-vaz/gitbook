<!--title:start-->
# Error Response
<!--title:end-->
<!--shortdesc:start-->

Defines the structure of an error.
<!--shortdesc:end-->
<!--desc:start-->
<!--desc:end-->
<!--properties:start-->

| Properties | Type | Description |
|--- |--- | --- |
| `error` | Enumerate<br/>Required | One of the following:<ul><li>`invalid_request` - The request to the endpoint is missing a required parameter, includes an invalid parameter value, includes a parameter more than once, or is otherwise malformed.</li><li>`unauthorized_client` - The client application is not authorized to request an access token using the method implicit in `response_type`.</li><li> `access_denied` - The customer or the STS denied access data.</li><li>`unsupported_response_type` - The STS does not support the method in `response_type`.</li><li>`invalid_scope` - One or more of the scopes in `scope` is invalid, unknown, or malformed.</li><li>`server_error` - The STS encountered an internal error.</li><li>`temporarily_unavailable`- The STS is currently unable to handle the `/connect/authorize` request due to a temporary overloading or maintenance of the server.</li></ul> |
|`error_description` | String<br/>Optional |Human friendly description of the error.|
| `error_uri` | String<br/>Optional | Link to a web page with detailed information about the error. |
|`state` | String<br/>Required if the `/connect/authorize` request includes the `state` parameter.| State sent with `/connect/authorize` request.|

<!--properties:end-->

<!--example:start-->
<!--example:end-->