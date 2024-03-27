<!--title:start-->
# OAuth 2.0 Overview
<!--title:end-->
<!--shortdesc:start-->
OAuth 2.0 is an authorization framework.
<!--shortdesc:end-->

<!--desc:start-->
The OAuth 2.0 authorization framework enables a third-party application to obtain limited access to an HTTP service, either on
behalf of a resource owner by orchestrating an approval interaction
between the resource owner and the HTTP service, or by allowing the
third-party application to obtain access on its own behalf.


In the traditional client-server authentication model, the client
requests an access-restricted resource (protected resource) on the
server by authenticating with the server using the resource owner's
credentials.  In order to provide third-party applications access to
restricted resources, the resource owner shares its credentials with
the third party.  This creates several problems and limitations:

* Third-party applications are required to store the resource owner's credentials for future use, typically a password in clear-text.
* Servers are required to support password authentication, despite the security weaknesses inherent in passwords.
* Third-party applications gain overly broad access to the resource owner's protected resources, leaving resource owners without any ability to restrict duration or access to a limited subset of resources.
* Resource owners cannot revoke access to an individual third party without revoking access to all third parties, and must do so by changing the third party's password.
*  Compromise of any third-party application results in compromise of the end-user's password and all of the data protected by that password.

OAuth addresses these issues by introducing an authorization layer
and separating the role of the client from that of the resource
owner.  In OAuth, the client requests access to resources controlled
by the resource owner and hosted by the resource server, and is
issued a different set of credentials than those of the resource
owner.

Instead of using the resource owner's credentials to access protected
resources, the client obtains an access token -- a string denoting a
specific scope, lifetime, and other access attributes.  Access tokens
are issued to third-party clients by an authorization server with the
approval of the resource owner.  The client uses the access token to
access the protected resources hosted by the resource server.

For example, an end-user (resource owner) can grant a printing
service (client) access to her protected photos stored at a photo-
sharing service (resource server), without sharing her username and
password with the printing service.  Instead, she authenticates
directly with a server trusted by the photo-sharing service
(authorization server), which issues the printing service delegation-
specific credentials (access token).

This specification is designed for use with HTTP.  The
use of OAuth over any protocol other than HTTP is out of scope.

The OAuth 1.0 protocol, published as an informational
document, was the result of a small ad hoc community effort.  This
Standards Track specification builds on the OAuth 1.0 deployment
experience, as well as additional use cases and extensibility
requirements gathered from the wider IETF community.  The OAuth 2.0
protocol is not backward compatible with OAuth 1.0.  The two versions
may co-exist on the network, and implementations may choose to
support both.  However, it is the intention of this specification
that new implementations support OAuth 2.0 as specified in this
document and that OAuth 1.0 is used only to support existing
deployments.  The OAuth 2.0 protocol shares very few implementation
details with the OAuth 1.0 protocol.  Implementers familiar with
OAuth 1.0 should approach this document without any assumptions as to
its structure and details.
<!--desc:end-->
