<!--title:start-->
# Login with Facebook
<!--title:end-->
<!--shortdesc:start-->
Provide to the customer the option to use a Facebook account to authenticate at Farfetch Platform.

<!--shortdesc:end-->
<!--desc:start-->
Provide to the customer the option to use a Facebook account to authenticate at Farfetch Platform.

## Context
A Farfetch user must be allowed to log in with Facebook on Portal. To support this we added a custom grant to Identity that allows the creation of an account if the user email does not exist on our database or merge the Facebook account with an existing Farfetch account.

## What

### Account login
When the user logs with Facebook and his email does not exist on the specified tenant a token will be returned with a special claim, the client that made the request must create the user account. Every time the user logins with Facebook Identity will verify the user and return a token to the client application.

### Account linking
If the user authenticates with a Facebook account who's email is already in use with a Farfetch account a message requiring account link will be returned.

## External login
If you want to understand STS internal flow for external logins and authentication/link flows you can found it here.

##  Grant name
The grant name chosen was **Facebook**.

> Grant types are case-sensitive.

## Parameters

### ​​Account login

|Parameter	|Value|
|----|----|
|grant_type	|Facebook|
|client_id	|[client_id]|
|client_secret|	[client_secret]|
|assertion	|[facebook_token]|
|scopes|	api|

### Account linking

|Parameter	|Value|
|----|----|
|ff_token	|[farfetch_token|


> On account linking we should use the account creation parameters plus the account linking ones.



## Assumptions

|Scenario|	Description|
|----|----|
|Every time this grant type is used a request is made to Facebook to verify the user.	|`GET /facebook?fields=id,name,email&access_token=the-facebook-access-token`|
|Merge needed error.	|​ We will validate if the email that user has on Facebook exists on STS storage for the specified tenant when it exists the error will be returned:`"Merge needed for email {UserEmail}"`|
|Create a new account.	|If the email that user has on Facebook does not exist on STS storage for the specified tenant an STS token will be returned and it will contain the claim newuser.|
|User login.	|If the user already exists on STS storage an STS token will be returned, this token will contain the claim amr with value external.|
|Merging a Farfetch account with a Facebook account.|	After the merge needed message, the account can be merged by sending an STS token (Farfetch token) and the Facebook token, if successfully linked an STS token will be returned.|
|A Facebook account without email.|If for some reason the request that we do to Facebook does not return an email, an error will be returned:`"No email returned from facebook"` <br/>This can happen if the user does not consent Facebook share the email with Farfetch.|

## Examples

To obtain a valid Facebook token for test you can use: https://developers.facebook.com/tools/explorer/
<!--desc:end-->
