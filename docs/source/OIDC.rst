.. _mcp-oidc:

Open ID Connect (OIDC)
======================
`OpenID Connect <https://openid.net/connect>`__ is the protocol chosen to be used for user federation in MCP, and it should be supported by Service Providers. It is an interoperable authentication protocol based on the `OAuth 2.0 <https://oauth.net/2/>`__ family of specifications. It uses straightforward REST/JSON message flows with a design goal of "making simple things simple and complicated things possible". It’s uniquely easy for developers to integrate, compared to any preceding Identity protocols.

OpenID Connect lets developers authenticate their users across websites and apps without having to own and manage password files. For the app builder, it provides a secure verifiable, answer to the question: "What is the identity of the person currently using the browser or native app that is connected to me?"

OpenID Connect allows for clients of all types, including browser-based JavaScript and native mobile apps, to launch sign-in flows and receive verifiable assertions about the identity of signed-in users.

OpenID Connect provides authentication details in JWT tokens, that can be encrypted or digitally signed.

Keycloak
^^^^^^^^^^
`Keycloak <https://www.keycloak.org/>`__ is one of many products that includes support for OpenID Connect, and it is the product that currently provides MCP Identity Broker which is the cornerstone in MCP user federation.

Keycloak is an open source product developed by RedHat. Keycloak can be set up to work in different ways. It can be set up as an Identity Broker in which case it will link to other Identity Providers, which is what MCP Identity Broker does, or it can be set up to work as an Identity Provider, using either a database or LDAP/AD as a backend. Due the ability to connect to LDAP/AD, Keycloak can be used as quick and easy way to set up a Identity Provider.

.. _mcp-token:

MCP token
^^^^^^^^^
The first thing you should keep in mind is that the use of *mrn* and *org* in this chapter is based on :ref:`MCP namespace <mcp-mrn>`.

MCP expects the following attributes in the OpenID Connect JWT Access Token:

+--------------------+-----------------------------------------------------------------------------------------+
| Attribute          | Description                                                                             |
+====================+=========================================================================================+
| preferred_username | The username of the user in the parent organization.                                    |
+--------------------+-----------------------------------------------------------------------------------------+
| email              | The email of the user.                                                                  |
+--------------------+-----------------------------------------------------------------------------------------+
| given_name         | Firstname of the user.                                                                  |
+--------------------+-----------------------------------------------------------------------------------------+
| family_name        | Lastname of the user.                                                                   |
+--------------------+-----------------------------------------------------------------------------------------+
| name               | Full name of the user.                                                                  |
+--------------------+-----------------------------------------------------------------------------------------+
| org                | The Maritime Resource Name of the organization the user is a member of.                 |
+--------------------+-----------------------------------------------------------------------------------------+
| permissions        | List of permissions for this user assigned by the organization the user is a member of. |
+--------------------+-----------------------------------------------------------------------------------------+
| mrn                | The Maritime Resource Name of the user.                                                 |
+--------------------+-----------------------------------------------------------------------------------------+

These attributes will be directly mapped from attributes provided by the organizations Identity Provider, so the Identity Provider must also provide these attributes, except for the "org"-attribute.

Getting connected to MCP
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
If your organizations wishes to connect to MCP as an Identity Provider, to enable your uses to authenticate in MCP,
please contact `Oliver Haagh <mailto:oliver@dmc.international>`__ in order to set it up. Note that currently you need to expose interfaces that supports either OpenID Connect or SAML2.

Within the scope of the MCC MCP testbed, organizations can get users registered in special project Identity Providers, supplied by MCP. To join MCP please fill out the form at `Apply <https://management.maritimecloud.net/#/apply>`__ .

Setting up an OIDC Identity Provider
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
OpenID Connect is supported by the latest ADFS and `Keycloak <https://www.keycloak.org/>`__ releases. MCP Identity Broker only supports the `OpenID Connect Authorization Code Flow <https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth>`__ when connecting to Identity Providers. This limitation only applies when the Identity Broker connects to Identity Providers, not when Services/Clients connects to the Identity Broker.

As default MCP Identity Broker expect the following attributes to be provided by an OpenID Connect Identity Provider:

+--------------------+-----------------------------------------------------------------------------------------+
| Attribute          | Description                                                                             |
+====================+=========================================================================================+
| preferred_username | The username of the user in the parent organization.                                    |
+--------------------+-----------------------------------------------------------------------------------------+
| email              | The email of the user.                                                                  |
+--------------------+-----------------------------------------------------------------------------------------+
| given_name         | Firstname of the user.                                                                  |
+--------------------+-----------------------------------------------------------------------------------------+
| family_name        | Lastname of the user.                                                                   |
+--------------------+-----------------------------------------------------------------------------------------+
| name               | Full name of the user.                                                                  |
+--------------------+-----------------------------------------------------------------------------------------+
| permissions        | List of permissions for this user assigned by the organization the user is a member of. |
+--------------------+-----------------------------------------------------------------------------------------+

If your Identity Provider has the values in different attributes, some mapping can be set up.

The Identity Broker will generate and attach the organizations MRN and the users MRN to the user.

Setting up an OpenID Connect Identity Provider for multiple organizations
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
MCP has some special Identity Providers that handles the authentication for multiple organizations. Current examples are "IALA" and "BIMCO ExtraNet". These Identity Providers are responsible for vetting the organizations they provide authentication for, so that it is confirmed that the organization is who they claim to be. New organizations can be added by these Identity Providers. Since MCP currently needs to know about organizations centrally to be able to (among other things) issue certificates, some extra information is needed from these Identity Providers, to be able to create them in the central Identity Registry, if they are not already known.

The extra information must be given as attributes, in addition to the attributes mentioned in 'Setting up an OpenID Connect Identity Provider':

As default MCP Identity Broker expect the following attributes to be provided by an OpenID Connect Identity Provider:

+-------------+---------------------------------------------------------------------------------------------------------------+
| Attribute   | Description                                                                                                   |
+=============+===============================================================================================================+
| mrn         | The Maritime Resource Name of the user.                                                                       |
+-------------+---------------------------------------------------------------------------------------------------------------+
| org         | The Maritime Resource Name of the parent organization of the user.                                            |
+-------------+---------------------------------------------------------------------------------------------------------------+
| org-name    | Human readable name of the parent organizations.                                                              |
+-------------+---------------------------------------------------------------------------------------------------------------+
| org-address | Address of the organization. It must be without linebreaks, ending with comma and the country of the address. |
+-------------+---------------------------------------------------------------------------------------------------------------+

Note that the MRN must be on the form "urn:mrn:mcl:user:dma@iala:thc" and "urn:mrn:mcl:org:dma@iala" for user and organization respectively. In this case the organization is "dma" whos identity is guaranteed by "iala".

Setting up an SAML2 Identity Provider
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
SAML2 is supported by older ADFS releases.

+--------------------------------------------------------------------+-----------------------------------------------------------------------------------------+
| Attribute                                                          | Description                                                                             |
+====================================================================+=========================================================================================+
| NAMEID                                                             | The username of the user in the parent organization.                                    |
+--------------------------------------------------------------------+-----------------------------------------------------------------------------------------+
| http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress | The email of the user.                                                                  |
+--------------------------------------------------------------------+-----------------------------------------------------------------------------------------+
| http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname    | Firstname of the user.                                                                  |
+--------------------------------------------------------------------+-----------------------------------------------------------------------------------------+
| http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname      | Lastname of the user.                                                                   |
+--------------------------------------------------------------------+-----------------------------------------------------------------------------------------+
| http://schemas.microsoft.com/ws/2008/06/identity/claims/role       | List of permissions for this user assigned by the organization the user is a member of. |
+--------------------------------------------------------------------+-----------------------------------------------------------------------------------------+

If your Identity Provider has the values in different attributes, some mapping can be set up.

The Identity Broker will generate and attach the organizations MRN and the users MRN to the user.

.. _cert-to-token:

Obtaining an OIDC Token using a Certificate
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
It is possible to obtain OpenID Connect Tokens using certificate authentication. The idea is that instead of authenticating by being redirected to an Identity Provider as in the normal OpenID Connect flow, you authenticate at the Identity Broker by using your certificate (that has been issued by MCP Identity Registry). This authentication would work in the same way as when authenticating to any service. When authentication has been succesful the Identity Broker can then issue a JWT-token, which is what the OpenId Connect authentication use. So in effect what we have is a "bridge" between the 2 authentication approaches.

An example of use could be that a device (which has been issued certificates) wishes to authenticate securely with a service, but the service only supports OpenId Connect authentication. Using the approach mentioned above, the device can use its certificate to get an OpenId Connect token, which can then be used to authenticate to the service.

The flow looks like the diagram below:

.. image:: _static/image/diagram_oidc_authentication_using_cert.png
    :align: center
    :alt: getting a token from certificate

Example of Obtaining an OIDC Token using a Certificate
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
In this simple example we will assume that a certificate and key—​pair has been issued to the entity who wishes to authenticate. This example makes use of curl a command line tool available on Linux and Mac OS X.

The authentication involves 2 steps:

1. Obtaining a temporary Authorization Code using a certificate.
2. Obtaining a OpenId Connect Token using the Authorization Code.

These 2 steps are actually standard in the OpenID Connect Authorization Code Flow, though normally certificates are not the standard authentication method.

First we obtain the code by issuing this command::

  curl --verbose --location --cookie "" --key PrivateKey.pem --cert Certificate.pem 'https://maritimeid.maritimecloud.net/auth/realms/MaritimeCloud/protocol/openid-connect/auth?client_id=cert2oidc&redirect_uri=http%3A%2F%2Flocalhost%3A99&response_type=code&kc_idp_hint=certificates&scope=openid'

Let us break down the command:

* ``curl --verbose --location --cookie ""``: ``curl`` is the tool itself. ``--verbose`` means it will be in verbose mode, ``--location`` means curl will follow HTTP redirects and ``--cookie ""`` activates the use of HTTP cookies which means that cookies received will be remember and used during redirects. We need to follow redirects since that is used by OpenID Connect to go back and forth between servers, and the verbose mode is needed because we would like to see where we are redirected — especially the last redirect, but more about that later.

* ``--key PrivateKey.pem --cert Certificate.pem``: Here the private key and the certificate is given to curl in PEM format.

* The last part is the URL which itself is multiple parts:

   * Address of the authentication endpoint: ``https://maritimeid.maritimecloud.net/auth/realms/MaritimeCloud/protocol/openid-connect/auth``

   * Parameters: ``client_id=cert2oidc&redirect_uri=http%3A%2F%2Flocalhost&response_type=code&kc_idp_hint=certificates&scope=openid``. These can be also be broken down:

    + ``client_id=cert2oidc``: This is a special OpenID Connect client setup to be used for certificate authentication.

    + ``redirect_uri=http%3A%2F%2Flocalhost%3A99``: This is where the authentication server will redirect to at the end of the authentication. The parameter is URL encoded and decoded looks like this: http://localhost:99. This address is meant to be invalid, since we want the last redirect to fail.

    + ``response_type=code``: This defines that we uses the Authorization Flow as mentioned above.

    + ``kc_idp_hint=certificates``: This tells the Identity Broker that we wants to authenticate using the Certificate Identity Provider.

    + ``scope=openid``: And finally, this define that we are using OpenID Connect.

When the command runs it returns a lot of output, due to being in verbose mode. We will not go into detail, but quite a few redirects happens, as described in the sequences diagram above. The last redirect however fails, which is intended. The final output will look something like this::

  * Issue another request to this URL: 'http://localhost:99?code=uss.Yw6k4rXOJiR6IF4a2Y7tYC1-Eqoo8dHSUwjfuIFDfpI.543a63db-9d22-45f7-85b6-a258059c0825.6826c662-6b68-423a-a248-71bd3e69dab0'
  * Rebuilt URL to: http://localhost:99/?code=uss.Yw6k4rXOJiR6IF4a2Y7tYC1-Eqoo8dHSUwjfuIFDfpI.543a63db-9d22-45f7-85b6-a258059c0825.6826c662-6b68-423a-a248-71bd3e69dab0
  *   Trying 127.0.0.1...
  * connect to 127.0.0.1 port 99 failed: Connection refused
  * Failed to connect to localhost port 99: Connection refused
  * Closing connection 1
  curl: (7) Failed to connect to localhost port 99: Connection refused


Here we can recognize ``http://localhost:99`` from the ``redirect_uri`` parameter described earlier. We can also see that the ``code`` parameter is in the url, in this case with the value ``uss.Yw6k4rXOJiR6IF4a2Y7tYC1-Eqoo8dHSUwjfuIFDfpI.543a63db-9d22-45f7-85b6-a258059c0825.6826c662-6b68-423a-a248-71bd3e69dab0``. It is this code we need to in the second step of authentication to get the OpenID Connect Tokens. The code is only valid for a very limited time (less than a minute) and can only be used once. We will again use ``curl`` in the second step::

  curl --data "grant_type=authorization_code&client_id=cert2oidc&code=uss.Yw6k4rXOJiR6IF4a2Y7tYC1-Eqoo8dHSUwjfuIFDfpI.543a63db-9d22-45f7-85b6-a258059c0825.6826c662-6b68-423a-a248-71bd3e69dab0&redirect_uri=http%3A%2F%2Flocalhost%3A99" https://maritimeid.maritimecloud.net/auth/realms/MaritimeCloud/protocol/openid-connect/token

Again, let us break down the command. In this case the command consist of 3 parts, ``curl`` — the tool itself, data-parameters and an URL. We will concentrated on the data-parameters. Note that this is a HTTP POST request, which is why the parameters is supplied in a separate argument and not as part of the URL.

* ``grant_type=authorization_code``: This specifies that we will use an authorization code to authenticate ourself in this call.

* ``client_id=cert2oidc``: The id of the special client, as mentioned above.

* ``code=uss.Yw6k4rXOJiR6IF4a2Y7tYC1-Eqoo8dHSUwjfuIFDfpI.543a63db-9d22-45f7-85b6-a258059c0825.6826c662-6b68-423a-a248-71bd3e69dab0``: The code we obtained earlier.

* ``redirect_uri=http%3A%2F%2Flocalhost%3A99``: The redirect url, the same as before, though not used for actual redirection in this case.

When this call runs there will be no redirection, so we do not need to tell curl to follow redirects. Instead the returned output will be the tokens that we wish to use, in a format like this::

  {
    "access_token":"eyJhbGciOiJ...uXoHudIM1yiDBYj8g",
    "expires_in":300,
    "refresh_expires_in":1800,
    "refresh_token":"eyJhbGciOiJ...iv7rKSa__IKy983Gg",
    "token_type":"bearer",
    "id_token":"eyJhbGciOiJ...Ycp2GupfpTTgRkhtnw",
    "not-before-policy":0,
    "session_state":"94487eaa-b77f-4b6c-8db1-c574fc6a09da"
  }

The access_token is the token that should be used we communicating with services in MCP context. The token should be embedded in the HTTP header. When using curl it can be done like this::

  curl -H "Authorization: Bearer eyJhbGciOiJ...uXoHudIM1yiDBYj8g" https://api.maritimecloud.net/oidc/api/org/DMA

The refresh_token is used to re-authenticate to get a new set of tokens when the access_token has expired, in this case 300 seconds after it has been issued, as seen in the expires_in attribute. The new set of tokens can then be obtain with a HTTP POST like this::

  curl --data "grant_type=refresh_token&client_id=cert2oidc&refresh_token=eyJhbGciOiJ...iv7rKSa__IKy983Gg" https://maritimeid.maritimecloud.net/auth/realms/MaritimeCloud/protocol/openid-connect/token
