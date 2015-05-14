# What's Security? #

**Security** is the mechanism to restrict the access to certain system resources to some users.
i.e. To allow the access to administrative pages just to users with administrative privileges.

Security comprises two major operations: The Authentication and the Authorization.

  * **Authentication** is the process of confirming who someone is or what something is, it is, confirm a claimed identity. For example, motorists identify themselves to police by presenting a driver's license. Police compare the photo and description on the driver's license with the motorist to authenticate identity.
  * **Authorization** is the act of granting permission for someone or something to conduct an act. Even when identity and authentication have indicated who someone is, authorization may be needed to establish what he or she is allowed to do.

# Security in Lion #

As part of the lion framework, there is a layer to support the security principles described abobe.
The followin diagram shows the main classes and relationships that belong to this layer:

![https://lion-framework.googlecode.com/git/doc/images/Security/security.png](https://lion-framework.googlecode.com/git/doc/images/Security/security.png)

The most fundamental classes here are both the AuthenticationManager and AuthorizationManager.
The AuthenticationManager is the one in charge of the authentication management while the AuthorizationManager is the one in charge of the authorization management (just read the class names :).

As you can see in the diagram, there are several interfaces (the ones with the prefix I, like ICredentials, IAuthenticator, ...). Lion allows to specify declarativelly which classes will be used for each interface. This capability allows to customize/extend the security layer without altering the root behaviour.

## Authentication ##

The AuthenticationManager main task is to perform the user logon. To make it happens, it exposes the method AuthenticationManager::logon().

The AuthenticationManager has a set of authenticators (classes implementing the IAuthenticator). To perform the logon, it delegates to each one until the user is successfully authenticated. The authentication fails if all the authenticators fail.
Once it occurs, the user instance is stored in the AuthenticationManager. It also activate some roles in the UserSession.

AuthenticationManager::logout() just unset the authenticated user from AuthenticationManager. Also unset the active roles from the UserSession.

For more information regarding the authentication, see the Authentication in Lion section.

## Authorization ##

The AuthorizationManager main task is to check if the authenticated user has permission to access to each system resource.
The most important thing to know is the fact that the AuthorizationManager does not check the permissions associated to the already authenticated user, but the ones stored in the UserSession.

For more information regarding the authorization, see the Authorization in Lion section.
