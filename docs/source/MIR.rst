.. _mir:

Maritime Identity Registry (MIR)
================================
Maritime Identity Registry (MIR for short) is an identity provider and an authority for identities of persons, organizations or ships that are using the MCP.
Technically MIR consists of a PKI library and a RESTful API for managing identities and certificates which are standardized by MCC.
Under the API MIR can authenticate an user, based on two methods:

* :ref:`Public Key Infrastructure (PKI)<mcp-pki>`.
* :ref:`Open ID Connect (OIDC)<mcp-oidc>`.

MIR API accepts either a certificate (PKI) or a token (OIDC) for user authentication.
After the authentication and following authorization, the user can register and manage the entities through the `MIR API <https://api.maritimecloud.net/v2/api-docs>`__, with :ref:`a proper right what we defined as a role <mir-authorization>`.
One important feature as the identity management is to issue or revoke a X509 client certificate for entities that are already registered in the API database.

.. _mcp-type:

MCP entity types
-----------------
MCP entity types are described in `MCC Identity Management and Security: General Approach and Basic Requirements <https://maritimeconnectivity.net/docs/mcp-idsec-1-v2.pdf>`__ and used in the :ref:`MCP namespace <mcp-mrn>` as *<MCP-TYPE>*.

The data model of each entity in MIR is given in the `Swagger file of MIR <https://api.maritimecloud.net/v2/api-docs>`__.


.. _mcp-pki:

Public Key Infrastructure (PKI)
-------------------------------
A Public Key Infrastructure (PKI) is a set of hardware, software, people, policies, and procedures needed to create, manage, distribute, use, store, and revoke digital authentication certificates and manage public-key encryption. Thereby helping an organization establish and maintain a trustworthy networking environment. There is no inherent requirement for using a PKI based solution for enabling secure machine to machine (M2M) communication, but it is the most commonly used solution and lots of software, standards and best practices exists for utilizing it. The choice of using PKI based on the X.509 standard for M2M communication in MCP was thus straightforward.

The key piece in a PKI architecture is a PKI CA (Certificate Authority) which is an entity that issues digital authentication certificates. A digital certificate that certifies the ownership of a public key by the named subject of the certificate. An obvious example would be creating a certificate for a vessel, which can serve to certify that a given document was indeed signed by someone in possession of the certificate issued to that vessel.

One of the most important aspects of designing a PKI based architecture is the certificate hierarchy planning because the design will affect how certificates are validated and used by PKI-enabled actors. Normally a PKI based architecture is arranged in a tree like hierarchy with a single root entity in the top and with numerous leaves called sub CAs. Each sub CA can have their own sub CA thereby forming a tree with a single entity at the top. Each leave in the tree is responsible for creating certificates, for example, ships or organizations. The reason for doing this is to be able to delegate the responsibility to different parties. For example, in the case of MCP one could envision that at some point in the future every flag state would be their own sub CA. Having the sole responsibility of issuing certificates for vessels registered under their own flag.

.. image:: _static/image/pki_hierarchy.png
    :align: center
    :alt: PKI hierarchy

In the current version of MCP we are working with four sub CAs that have the responsibility for issuing all certificates. The four sub CAs are IALA, BIMCO, SMART Navigation Project and the MCP Identity Registry itself.

The most important functionality of a CA is issuing digital certificates. A digital certificate certifies the ownership of a public key by the named subject of the certificate. This allows others (relying parties) to rely upon signatures or on assertions made about the private key that corresponds to the certified public key. In this model of trust relationships, a CA is a trusted third party—trusted both by the subject (owner) of the certificate and by the party relying upon the certificate. In the case of MCP these certificates are typically used to make secure connections between maritime actors over the Internet. A certificate is required in order to avoid the case that a malicious party which happens to be on the path to the target server pretends to be the actual target. Such a scenario is commonly referred to as a man-in-the-middle attack. The client uses the CA certificate to verify the CA signature on the server certificate, as part of the checks before establishing a secure connection. Likewise, the server has the option of inspecting the clients certificate before allowing it to connect.

To issue a new certificate for, for example, a vessel an administrator for the organization who owns the ship will need to log in to MCP Portal and use its functionality for issuing new certificates. The certificate being issued will contain information about the name of the ship, the owner, the flag state and other attributes such as MMSI and IMO number. In the current implementation there is no validation of this information other than that the organization must have been accepted when signing up. We do not expect this to be a problem for the foreseeable future as the number of participating parties is still relatively small. In the future where many more organizations have been added it might, for example, be possible to integrate with national registries so an automated checks of these information can be made.

After having issued a certificate the administrator can now install it on the ship in some way. The actual logistics about how and where to install it is outside of the scope of the identity registry as this might vary a lot between organizations and projects. This also reduces the functionality of the identity registry to just provide the core functionality of Identity management allowing users to be able to build innovative solutions on top of it. This also applies directly to machine to machine communication. The identity registry places no restrictions about what kind of machine to machine communication protocols that should be used, it just provides the basic infrastructure to allow for each machine to authenticate the host in the other end. Letting each project select their protocols if needed.

.. _mcp-pki-cert-profile:

MCP Certificate
^^^^^^^^^^^^^^^^
MCP can issue X.509 certificates for the users which can then be used for authentication. Service providers relying on X.509 certificate authentication must obtain and install MCP root certificate into their webservice.

Please note that the use of *MCP MRN* and *MRN* is based on :ref:`MCP namespace <mcp-mrn>` excluding *Subsidiary MRN*.

The standard information present in an X.509 certificate includes:

* **Version** – which X.509 version applies to the certificate (which indicates what data the certificate must include)

* **Serial number** – A unique assigned serial number that distinguishes it from other certificates

* **Algorithm information** – the algorithm used to sign the certificate

* **Issuer distinguished name** – the name of the entity issuing the certificate (MCP)

* **Validity period of the certificate** – the number of months that the certificate is valid

* **Subject distinguished name** – the name of the identity the certificate is issued to

* **Subject public key information** – the public key associated with the identity

The Subject distinguished name field will consists of the following items:

+------------------------+----------+-----------+-----------+-------------------+--------+--------------------+
| Field                  | User     | Vessel    | Device    | Service           | MMS    | Organization       |
+========================+==========+===========+===========+===================+========+====================+
|CN (CommonName)         |Full name |Vessel name|Device name|Service Domain Name|MMS name|Organization Name   |
+------------------------+----------+-----------+-----------+-------------------+--------+--------------------+
|O (Organization)        |                            Organization MCP MRN                                    |
+------------------------+----------+-----------+-----------+-------------------+--------+--------------------+
|OU (Organizational Unit)|"user"    |"vessel"   |"device"   |"service"          |"mms"   |"organization"      |
+------------------------+----------+-----------+-----------+-------------------+--------+--------------------+
|E (Email)               |User email|                                                    |Organization email  |
+------------------------+----------+-----------+-----------+-------------------+--------+--------------------+
|C (Country)             |                             Organization country code                              |
+------------------------+----------+-----------+-----------+-------------------+--------+--------------------+
|UID                     |                               MCP MRN                                              |
+------------------------+----------+-----------+-----------+-------------------+--------+--------------------+

An example of the fields for a vessel could look like this::

  C=DK, O=urn:mrn:mcp:org:idp1:dma, OU=vessel, CN=JENS SØRENSEN, UID=urn:mrn:mcp:vessel:idp1:dma:jens-soerensen

Finally, In additions to the information stored in the standard X.509 attributes listed above, the X509v3 extension SubjectAlternativeName (SAN) extension is used to store extra information. There already exists some predefined fields for the SAN extension, but they do not match the need we have for maritime related fields. Therefore the “otherName” field is used, which allows for using a Object Identifier (OID) to define custom fields. The OIDs currently used are not registered at ITU, but are randomly generated using a tool provided by ITU (see http://www.itu.int/en/ITU-T/asn1/Pages/UUID/uuids.aspx). See the table below for the fields defined, the OIDs of the fields and which kind of entities that uses the fields.

+-----------------+------------------------------------------------+---------------------------------------+
| Name            | Object Identifier (OID)                        | Used by                               |
+=================+================================================+=======================================+
| Flagstate       |`2.25.323100633285601570573910217875371967771`  | Vessel, Service                       |
+-----------------+------------------------------------------------+---------------------------------------+
| Callsign        |`2.25.208070283325144527098121348946972755227`  | Vessel, Service                       |
+-----------------+------------------------------------------------+---------------------------------------+
| IMO number      |`2.25.291283622413876360871493815653100799259`  | Vessel, Service                       |
+-----------------+------------------------------------------------+---------------------------------------+
| MMSI number     |`2.25.328433707816814908768060331477217690907`  | Vessel, Service                       |
+-----------------+------------------------------------------------+---------------------------------------+
| AIS shiptype    |`2.25.107857171638679641902842130101018412315`  | Vessel, Service                       |
+-----------------+------------------------------------------------+---------------------------------------+
| Port of register|`2.25.285632790821948647314354670918887798603`  | Vessel, Service                       |
+-----------------+------------------------------------------------+---------------------------------------+
| Ship MRN        |`2.25.268095117363717005222833833642941669792`  | Service                               |
+-----------------+------------------------------------------------+---------------------------------------+
| MRN             |`2.25.271477598449775373676560215839310464283`  | Vessel, User, Device, Service, MMS    |
+-----------------+------------------------------------------------+---------------------------------------+
| Permissions     |`2.25.174437629172304915481663724171734402331`  | Vessel, User, Device, Service, MMS    |
+-----------------+------------------------------------------------+---------------------------------------+
| Subsidiary MRN  |`2.25.133833610339604538603087183843785923701`  | Vessel, User, Device, Service, MMS    |
+-----------------+------------------------------------------------+---------------------------------------+
| Home MMS URL    |`2.25.171344478791913547554566856023141401757`  | Vessel, User, Device, Service, MMS    |
+-----------------+------------------------------------------------+---------------------------------------+
| URL             |`2.25.245076023612240385163414144226581328607`  | MMS                                   |
+-----------------+------------------------------------------------+---------------------------------------+

Encoding of string values in certificates must follow the specifications defined in RFC 5280, and where possible it is highly recommended to use UTF-8.

Authentication with certificate
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
To illustrate the authentication flow using a certificate the sequence diagram below is provided.

.. image:: _static/image/cert_authentication_flow.png
    :align: center
    :alt: the sequence diagram of cert authentication flow

Alternatively it is possible to get a token from certificate. See more detail in :ref:`Obtaining an OIDC Token using a Certificate section<cert-to-token>`.

Revocation of MCP certificate
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
A crucial part of any PKI is to support revocation of certificates, so that certificates that belongs to entities who is no longer trusted, affiliation has change, etc., can be mark as not trusted any more. Anyone who wishes to validate a certificate can then check if the certificate has been marked as revoked. The checking of the certificate revocation status can be done in two ways:

1. Call the OCSP interface provided by the Identity Registry for each certificate.
2. Periodically download a Certificate Revocation File from the Identity Registry and use it check certificates locally.

The endpoints for both the OCSP interface and the Certificate Revocation File are embedded into the certificates issued by MCP Identity Registry, and are currently https://api.maritimecloud.net/x509/api/certificates/crl and https://api.maritimecloud.net/x509/api/certificates/ocsp.

.. _mcp-oidc:

Open ID Connect (OIDC)
----------------------
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


Authentication with OIDC
^^^^^^^^^^^^^^^^^^^^^^^^^
To illustrate the authentication flows the sequence diagrams below is provided.

The first diagram below shows the standard `OpenID Connect Authorization Code Flow <http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth>`__ involving a browser being used by the user to access a service in the form of a webpage.

.. image:: _static/image/oidc_authentication_flow.png
    :align: center
    :alt: the sequence diagram of cert authentication flow

The second diagram shows the flow used when an authenticated user is accessing a backend service. For browser based services this scenario is often used when the browser retrieves data from backend services. In this scenario since the user is authenticated, the user has a token that is presented for authentication for the backend service.

.. image:: _static/image/backend_service_authentication_flow.png
    :align: center
    :alt: the sequence diagram of cert authentication flow

Getting connected to MCP
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
There are some requirements to enable identity brokerage in MCP.

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


.. _mir-authorization:

Authorization in MIR
--------------------

As an example of how authorization can be done, let us have a look at how it is handled inside the MCP Identity Registry. When it comes to authorization, the Identity Registry will have the same information about its users as any other service in MCP.

The Identity Registry currently has these roles:

+--------------------+-----------------+--------------+--------------------+----------------------+-----------------------+----------------------+--------------------+--------------------+------------+
| Role               | Approve New Org | Edit Own Org | Maintain Org Users | Maintain Org Vessels | Maintain Org Services | Maintain Org Devices | Maintain Org MMSes | Maintain Org Roles | Delete Org |
+--------------------+-----------------+--------------+--------------------+----------------------+-----------------------+----------------------+--------------------+--------------------+------------+
| ROLE_SITE_ADMIN    |        X        |       X      |          X         |           X          |           X           |           X          |          X         |          X         |      X     |
+--------------------+-----------------+--------------+--------------------+----------------------+-----------------------+----------------------+--------------------+--------------------+------------+
| ROLE_ORG_ADMIN     |                 |       X      |          X         |           X          |           X           |           X          |          X         |          X         |            |
+--------------------+-----------------+--------------+--------------------+----------------------+-----------------------+----------------------+--------------------+--------------------+------------+
| ROLE_ENTITY_ADMIN  |                 |              |          X         |           X          |           X           |           X          |          X         |                    |            |
+--------------------+-----------------+--------------+--------------------+----------------------+-----------------------+----------------------+--------------------+--------------------+------------+
| ROLE_USER_ADMIN    |                 |              |          X         |                      |                       |                      |                    |                    |            |
+--------------------+-----------------+--------------+--------------------+----------------------+-----------------------+----------------------+--------------------+--------------------+------------+
| ROLE_VESSEL_ADMIN  |                 |              |                    |           X          |                       |                      |                    |                    |            |
+--------------------+-----------------+--------------+--------------------+----------------------+-----------------------+----------------------+--------------------+--------------------+------------+
| ROLE_SERVICE_ADMIN |                 |              |                    |                      |           X           |                      |                    |                    |            |
+--------------------+-----------------+--------------+--------------------+----------------------+-----------------------+----------------------+--------------------+--------------------+------------+
| ROLE_DEVICE_ADMIN  |                 |              |                    |                      |                       |           X          |                    |                    |            |
+--------------------+-----------------+--------------+--------------------+----------------------+-----------------------+----------------------+--------------------+--------------------+------------+
| ROLE_MMS_ADMIN     |                 |              |                    |                      |                       |                      |          X         |                    |            |
+--------------------+-----------------+--------------+--------------------+----------------------+-----------------------+----------------------+--------------------+--------------------+------------+
| ROLE_APPROVE_ORG   |        X        |              |                    |                      |                       |                      |                    |                    |            |
+--------------------+-----------------+--------------+--------------------+----------------------+-----------------------+----------------------+--------------------+--------------------+------------+
| ROLE_USER          |                 |              |                    |                      |                       |                      |                    |                    |            |
+--------------------+-----------------+--------------+--------------------+----------------------+-----------------------+----------------------+--------------------+--------------------+------------+

A few things should be noted:

* "Maintain" (as mentioned in the table above) means to be able to create, update and delete, as well as issuing and revoking certificates.

* Excluding entities with the role ROLE_SITE_ADMIN, it is not possible for entities to see entities from other organizations.

* A ROLE_SITE_ADMIN can maintain entities and organizations beyond his own organization.

* Any entity, regardless of roles, can see all entities from its own organization, though some sensitive information from services is filtered for non-admins.

* Only a ROLE_SITE_ADMIN can assign ROLE_SITE_ADMIN and ROLE_APPROVE_ORG roles.

* A ROLE_APPROVE_ORG can create a user for an organization if and only if there is no users for the organization (this is used for creating the first administrative user for an organization).

In this example we will focus on **ROLE_USER** and **ROLE_ORG_ADMIN**. Let us assume that an Organization (DMA) wants to grant members of the internal "E-navigation" department administrative rights in the MCP Identity Registry. In DMAs Identity Provider setup the department name is automatically added to the "permissions" attribute. So to make this mapping the current DMA administrator sets up a role mapping between the permission "E-navigation" and the role ROLE_ORG_ADMIN. Once this is done, all members of the DMA E-navigation department will have administrative rights for the DMA organization inside the Identity Registry. As noted earlier, these rights only apply inside the Identity Registry. Other services must create a similar setup with mapping of roles and permissions.

Brokered User Federation
^^^^^^^^^^^^^^^^^^^^^^^^
In most federated setups it starts from the website (Service Provider) that need authentication and the identity provider, normally presented with a "Log in with X" link, where X could be Facebook, Google, etc. MCP has 2 steps for it, where the first step is MCP Identity Broker which presents the user with a list of available identity providers, which is the second step.
For a deeper understanding of how this is actually done please read the `Identity Broker overview section from the Keycloak manual <https://www.keycloak.org/docs/latest/server_admin/index.html#_identity_broker_overview>`__.

MCP supports the brokered user federation as long as non-MCP identity providers follow OAuth 2.0 by means of the federation of identity providers.
The federation is the means of linking distinct identity management systems to a person’s electronic identity and attributes. For example, a shipping company might expose all their users in LDAP or Active Directory to MCP in such a way as they appear as MCP users. Thereby bypassing the need to manage their users directly in MCP. This also means that MCP is not responsible for management of users.
In practical terms, federation means that users asked to authenticate in MCP will be redirected to a login webpage supplied by their organization where they can login using their organizational id.
Since the authentication process is the responsibility of the organizations, it is also up to the individual organizations to choose an appropriate authentication method. While most will likely use classic username/password authentication, multi factor security, biometric security or other approaches could be used.

What MCC governs in MIR
^^^^^^^^^^^^^^^^^^^^^^^
* :ref:`MCP namespace <mcp-mrn>`
* :ref:`MCP types and its hierarchy <mcp-type>`
* :ref:`PKI certificate profile <mcp-pki-cert-profile>`
* :ref:`OIDC Token <mcp-token>`
* REST API (https://api.maritimecloud.net/v2/api-docs)
* MCP Instance Provider root CA list
* MIR reference implementation

MIR reference implementation
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
MCC governs the reference implementations on MIR as follows:

* MCP-PKI library for handling certificates: https://github.com/MaritimeConnectivityPlatform/MCP-PKI
* MIR API: https://github.com/MaritimeConnectivityPlatform/IdentityRegistry
* MIR Identity Broker: https://github.com/MaritimeConnectivityPlatform/MCPKeycloakSpi

MIR Identity Broker which enables the token-based user authentication is based on `Keycloak <https://www.keycloak.org/>`__ which is an OpenID Connect (OIDC) server developed by Red Hat, but including two MCP specific plugins for synchronization of user data with MIR API and converting MCP client certificates to OIDC tokens.
Giving a detailed account of the synchronization part when the API is called to create a new user with corresponding information it is registered in the API database and also the ID Broker accounts.
The synchronization is provoked when a user logs in using an external identity provider by registering the user’s information to the API database.
In our testbed we use the federation to enable the participants across different projects to register and utilize MCP services established by the projects, as well as validate the identity management concept of MCP.
