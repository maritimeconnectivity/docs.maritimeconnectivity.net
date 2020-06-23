.. _mcp-term:

Terminology
===============

List of terms
^^^^^^^^^^^^^

Maritime Connectivity Platform (MCP)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
A communication framework enabling efficient, secure, reliable and seamless electronic information exchange among all authorized maritime stakeholders across available communication systems”, based on the IMO e-navigation strategy.

Maritime Connectivity Platform Consortium (MCC)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
An international association that governs standards, reference implementations, and testbed environments related to MCP, and will maintain the root CA list of MCP instance providers.

Maritime Identity Registry (MIR)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Technical MCP core component enabling an authentication of all maritime stakeholders in the context of the MCP and thus increasing the security and reliability of communication .

Maritime Service Registry (MSR)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Technical MCP core component that serves as a central reference point to provide and find services and thus to improve the visibility and accessibility of available information and services in the maritime domain, like yellow pages of maritime service.

Maritime Messaging Service (MMS)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Technical MCP core component that provides shore-based messaging service for the provision of flexible communication between ship and shore by using multiple available communication links and for delivering geo-location based messages.

MCP core component
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Either MIR, MSR, or MMS.

MCP instance
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
A running instance of MCP core component.

MCP environment (in the context of MCP instance)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
An environment can be categorized as one of its deployment purposes such as test, staging, and production (refer to `deployment environment <https://en.wikipedia.org/wiki/Deployment_environment>`__). An environment consists of one or more MCP instances.

MCP instance provider
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
An organization which takes responsibility of operating two (staging and production) or more MCP environments.

MCP service
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
A digital service which is registered in an MSR (not from the MCC MCP testbed) and has submitted three documents of IALA's G1128 e-Navigation technical service specification guideline to the MSR.

MCP portal
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
A front-end service enabling MCP users utilize identity management and investigating MCP services, based on MIR and MSR respectively. We provide a `manual <http://manual.maritimeconnectivity.net/>`__ for how to use it.

Key actor
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Maritime entities which needs to be authenticate for access right, is not both public and security related, and interact with the identity registry.

Organization (as MCP entity type)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
An organization is an entity, such as an institution, company or an association that has a collective goal and is linked to an external environment. Examples, include international organizations such as IMO, IALA, and IHO. National authorities such as US Coastguard, Swedish Maritime Administration. Local authorities such as VTS-Oeresund, Port of Rotterdam, Hong Kong SAR. Or commercial companies such as Transas or Maris.

User (as MCP entity type)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Users mainly refers to human users. Human users differ from other actors in that they typically use a username/password to login which implies a different interaction pattern with the identity registry then say communication between vessels.

Device (as MCP entity type)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Devices can be any number of entities that is not covered by the other entity types. It could for example be a lighthouse, an ECDIS or a server that needs to be able to authenticate itself. For example an ECDIS system.

Service (as MCP entity type)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Services refers to digital services, as described above. For example, a weather service that is available to other services for machine to machine communication. Services needs to be registered in such a way that it can successfully authenticate users.

Vessel (as MCP entity type)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Vessels describes any floating object used for the carriage of people or goods. The main need for registering vessels in the Maritime Connectivity Platform is so that digital authentication certificates can be issued for them. Thereby enabling secure communication between vessels as well as digitally signing of documents. Users might also use these authentication certificates for other purposes. The important thing is that the functionality is there. As part of the authentication certificate of a vessel its name, MMSI number, IMO number, call sign and possible other attributes is included in the header of the authentication certificate

Identity
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
The set of attributes that uniquely identifies a security principal. A security principal can have many different accounts that it uses to access various applications in the network. These accounts can be identified by these applications using different attributes of this entity. For example, a user can be known in the e-mail service by an e-mail ID, whereas that same user can be known in the human resource application by an employee number. The global set of such attributes constitutes the identity of the entity.

MCP entity
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
An entity registered at an MIR instance (not including the MCC MCP testbed).

Maritime Resource Name (MRN)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
A naming scheme that can uniquely identify any maritime resource on a global scale. By maritime resource, it means anything that has an identity of some kind. This could be organizations, employees, a person, a physical or a virtual object, for instance an electronic document, a buoy, a ship, a mariner, a nautical chart or an electronic service (e.g., “today’s weather report for the Oresund Strait”). Not all resources are “retrievable” in an electronic sense; For example, human beings, corporations, and buoys. However, they can still be considered a resource. (from IALA webpage https://www.iala-aism.org/technical/data-modelling/mrn/)

MCP namespace
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
The subspace of the MRN namespace that is governed by the MCC.

Identity provider
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
A system entity that creates, maintains, and manages identity information for principals while providing authentication services to relying applications within a federation or distributed network. Identity providers offer user authentication as a service. Relying party applications, such as web applications, outsource the user authentication step to a trusted identity provider. Such a relying party application is said to be federated, that is, it consumes federated identity. (from Wikipedia)

Identity broker
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
An Identity Broker is a special type of the identity provider and an intermediary service that connects multiple service providers with different identity providers. As an intermediary service, the identity broker is responsible for creating a trust relationship with an external identity provider in order to use its identities to access internal services exposed by service providers. (from Red Hat: https://access.redhat.com/documentation/en-us/red_hat_single_sign-on/7.0/html/server_administration_guide/identity_broker )

Authentication
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
The process of verifying the identity claimed by an entity based on its credentials.

Authorization
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
The process of establishing a specific entitlement that is consistent with authorization policies.

Authorization policies
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Declarations that define entitlements of a security principal and any constraints related to that entitlement.

Entitlements
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
The actions an entity in a network is allowed to perform and the resources to which it is allowed access.

Federated identity
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Is the means of linking a person’s electronic identity and attributes, stored across multiple distinct identity management systems

Public Key Infrastructure (PKI)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
A public key infrastructure (PKI) is a set of roles, policies, hardware, software and procedures needed to create, manage, distribute, use, store and revoke digital certificates and manage public-key encryption. The purpose of a PKI is to facilitate the secure electronic transfer of information for a range of network activities such as e-commerce, internet banking and confidential email. It is required for activities where simple passwords are an inadequate authentication method and more rigorous proof is required to confirm the identity of the parties involved in the communication and to validate the information being transferred. (from Wikipedia https://en.wikipedia.org/wiki/Public_key_infrastructure)

Open ID Connect (OIDC)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
OpenID Connect is a simple identity layer on top of the OAuth 2.0 protocol, which allows computing clients to verify the identity of an end-user based on the authentication performed by an authorization server, as well as to obtain basic profile information about the end-user in an interoperable and REST-like manner. In technical terms, OpenID Connect specifies a RESTful HTTP API, using JSON as a data format. (from Wikipedia https://en.wikipedia.org/wiki/OpenID_Connect)

Identity administration
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
The act of managing information associated with the identity of a security principal. The information can be used by the identity management infrastructure itself to determine administrative privileges.

Identity management policies
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Policies affecting the management of identities which includes naming policies and security policies.

Realm
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
A collection of identities and associated policies which is typically used when enterprises want to isolate user populations and enforce different identity management policies for each population.

Security principals
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
The subjects of authorization policies, such as users, user groups, and roles. A security principal can be a human or any application entity with an identity in the network and credentials to assert the identity.

Almanac
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
An offline version of parts of MSR and MIR, to be used if no stable internet connection is available for lookup in the online versions of SR and IR and thus to always allow access to the most relevant information during a journey
