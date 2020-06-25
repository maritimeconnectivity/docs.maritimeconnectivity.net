.. _mcp-type:

MCP entity type
===============
In order to be able to describe some of the concepts we are working with, here is a short introduction of the various entities we envision will interact with the identity registry.

Identity management and security is a very complex and comprehensive field. So wherever possible we must limit non-essential functionality. So clearly, there are some maritime entities that should not be a part of the identity registry. Therefore, the Identity Registry should not be:

* Managing information about entities that does not need to have access rights. For example, route or container information. While information about routes can be accessed by various users and systems. Routes by them self does not need access rights to access other information. It is only users and systems/devices that need access right.
* Maintaining information about entities that are not security related. For example, business addresses of users and organizations. Or location information about entities that can be used, for example, for routing messages to the right location.

The main reason for excluding all but non-essential security related information is that it opens up the never ending discussion about what exactly should be maintained in the identity registry.
If we make a generalized information store that maintains and provides query capabilities for all kinds of information about users and systems/devices.
For example, business addresses, geographical location. We might as well use this functionality for storing other queryable information.
For example, maintaining information about routes or cargo.

The MCP entity type is defined and used in the :ref:`MCP namespace <mcp-mrn>` as *<MCP-TYPE>*.

.. image:: _static/image/MCP_entity_hierarchy.png
    :align: center
    :alt: MCP entity hierarchy

The internal identity management system is built of a hierarchy with organizations on top, that can have different types of entities (except organization) them, where one entity can only belong to one organization.

Organization
^^^^^^^^^^^^
In MCP an organization is an entity of an MCP Instance Provider, such as an institution, a company, an international organization, a national or local authority, that has a collective goal and is linked to an external environment.

In order to be able to use the functions of MCP in any way, an organization needs to be signed up to MCP. In the context of the EfficienSea 2.0 and STM projects, this is currently done be sending an email with various information about the organization to a MCP test bed administrator.

In the future a more automated process involving a validation workflow by some organization may validate the relationship between an organization in the real world, and the issued maritime digital identity. How this validation is to take place is still up for discussion. However, one possible solution would be for the maritime authorities in which a given organization is registered to put the stamp of approval on the signup application.

Once an organization has been registered (and validated), an identity administrator of that organization will be able to create and manage maritime identities that belong to this organization, such as Users, Vessels Devices or Services. This administration is typically done via MCP Portal which is a web based client. For some larger organizations it might make sense to integrate directly via the underlying APIs.

All organizations that are registered in MCP must be registered with a unique MRN.

The MRN is used in numerous places. For example, all service specifications from a given organization includes the organizations MRN.

Examples of valid organization MCP MRN are:

* urn:mrn:mcp:org:<IPID>:imo
* urn:mrn:mcp:org:<IPID>:iala
* urn:mrn:mcp:org:<IPID>:dma
* urn:mrn:mcp:org:<IPID>:portofrotterdam
* urn:mrn:mcp:org:<IPID>:vts-oeresund
* urn:mrn:mcp:org:<IPID>:amsa@iala

, where <IPID> states the identity provider ID.

MRN has been approved by `IANA <https://www.iana.org/>`__ as an official URN namespace. For more information on the MRN specification you can read more at https://www.iana.org/assignments/urn-formal/mrn

Information required to be registered in MIR (case-sensitive)::

  mandatory: mrn, name, email, url, address, country
  optional: mrnSubsidiary, homeMMSUrl

Each of definitions is given from the `corresponding data model in the Swagger file <https://api-x509.maritimecloud.net/v2/api-docs>`__.

Vessel
^^^^^^^
Vessel describes any floating object used for the carriage of people or goods.

The main need for registering vessels in MCP is so that digital authentication certificates can be issued for them. Thereby enabling secure communication between vessels as well as digitally signing of documents. Users might also use these authentication certificates for other purposes. The important thing is that the functionality is there.

As part of the authentication certificate of a vessel its name, MMSI number, IMO number, callsign and possible other attributes are included in the header of the authentication certificate.

Vessel can relate multiple services with itself where the relation can be made by Services.

Information required to be registered in MIR (case-sensitive)::

  mandatory: org, mrn, name
  optional: permission, imo-number, mmsi-number, callsign, flagstate, ais-class, port-of-register, mrnSubsidiary, homeMMSUrl

Each of definitions is given from the `corresponding data model in the Swagger file <https://api-x509.maritimecloud.net/v2/api-docs>`__.

Service
^^^^^^^^
Service refers to a digital service. For example, a weather service that is available to other services for machine to machine communication. Services need to be registered in such a way that it can successfully authenticate users.

Information required to be registered in MIR (case-sensitive)::

  mandatory: org, mrn, name, instanceVersion
  optional: permission, vessel, certDomainName, oidcAccessType, oidcClientId, oidcClientSecret, oidcRedirectUri, mrnSubsidiary, homeMMSUrl

Each of definitions is given from the `corresponding data model in the Swagger file <https://api-x509.maritimecloud.net/v2/api-docs>`__.

Information required to be registered in MSR::

  mandatory: name, version, comment, instanceId (mrn)
  optional: geometry, geometryContentType, keywords, status, organizationId (mrn), unlocode, endpointUri, endpointType, mmsi, imo, serviceType, designId (mrn), specificationId (mrn)


User
^^^^^
User mainly refers to human users. Human users differ from other actors in that they typically use a username/password to login which implies a different interaction pattern with the identity registry than say communication between vessels.

Information required to be registered in MIR (case-sensitive)::

  mandatory: org, mrn, firstName, lastName, email
  optional: permission, mrnSubsidiary, homeMMSUrl

Each of definitions is given from the `corresponding data model in the Swagger file <https://api-x509.maritimecloud.net/v2/api-docs>`__.

Device
^^^^^^^
Device can be any number of entities that are not covered by the other entity types. It could for example be a lighthouse, an ECDIS or a server that needs to be able to authenticate itself.

Information required to be registered in MIR (case-sensitive)::

  mandatory: org, mrn, name
  optional: permission, mrnSubsidiary, homeMMSUrl

Each of definitions is given from the `corresponding data model in the Swagger file <https://api-x509.maritimecloud.net/v2/api-docs>`__.

MMS
^^^
MMS is a running instance of Maritime Messaging Service (MMS), one of the MCP core components.

Information required to be registered in MIR (case-sensitive)::

  mandatory: org, mrn, name, url
  optional: permission, mrnSubsidiary, homeMMSUrl

Each of definitions is given from the `corresponding data model in the Swagger file <https://api-x509.maritimecloud.net/v2/api-docs>`__.

MIR
^^^
MIR is a running instance of Maritime Identity Registry (MIR), one of the MCP core components.
It is not implemented yet in our reference implementation.

MSR
^^^
MSR is a running instance of Maritime Service Registry (MSR), one of the MCP core components.
It is not implemented yet in our reference implementation.
