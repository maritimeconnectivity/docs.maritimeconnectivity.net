.. _mcp-mrn:

MCP MRN (Maritime Resource Name)
================================
MCP provides an identity based on a `MRN (Maritime Resource Name) <https://www.iana.org/assignments/urn-formal/mrn>`__.

MCP namespace
-------------
MCP namespace is a subspace of the Maritime Resource Name (MRN) space, which is an official URN namespace. The syntax definitions below use the Augmented Backus-Naur Form as specified in `[RFC5234] <https://tools.ietf.org/html/rfc5234>`__.

The syntax for a MRN is as follows::

  <MRN> ::= "urn" ":" "mrn" ":" <OID> ":" <OSS>
		    [ rq-components ]
		    [ "#" f-component ]
  <OID> ::= (alphanum) 0*20(alphanum / "-") (alphanum)
  <OSS> ::= <OSNID> ":" <OSNS>
  <OSNID> ::= (alphanum) 0*32(alphanum / "-") (alphanum)
  <OSNS> ::= pchar *(pchar / "/")

The rules for alphanum and pchar are defined in `[RFC3986] <https://tools.ietf.org/html/rfc3986>`__.
The optional rq-components and f-component are specified in `[RFC8141] <https://tools.ietf.org/html/rfc8141>`__.

"mrn" specifies that the URN is within the MRN namespace.
The *Organization ID (OID)* refers to an organization that is assigned a subspace of MRNs such as IMO, IALA, or the MCP. Syntactically,
it is a string that must be unique across the "mrn" scheme.
The *Organization Specific String (OSS)* is specified and managed by the governing organization in a consistent way conform to the definitions of the MRN namespace.
In particular, each organization must structure the *OSS* into two parts: the *Organization Specific Namespace ID (OSNID)*,
and the *Organization Specific Namespace String (OSNS)*.
The *OSNID* identifies a particular type of resource (uniquely within the governing organization),
while the *OSNS* identifies the particular resource (uniquely for its type within the governing organization).
Altogether, this ensures that the resulting URN is globally unique.

For a MRN governed by the MCC the *OID* reads "mcp", and the *OSNID* specifies one of the following types used within the MCP:
device, organization, user, vessel, service, mms, mir, and msr.
The latter three types are to be used for entities of the three MCP components MMS, MIR, and MSR respectively.
Moreover, the definition of the *OSNS* takes into account the distributed structure of the MCP:
identities can be provided and managed by several identity providers.
In detail, the syntax of a MRN governed by the MCC (short: MCP MRN or MCP name) is as follows::

  <MCP-MRN> ::= "urn" ":" "mrn" ":" "mcp" ":" <MCP-TYPE> ":" <IPID> ":" <IPSS>
  <MCP-TYPE> ::= "device" | "org" | "user" | "vessel" | "service" |
                 "mir" | "mms" | "msr"
  <IPID> ::= <CountryCode> | (alphanum) 0*20(alphanum / "-") (alphanum)
             <IPSS> ::= pchar *(pchar / "/")

"mcp" specifies that the governing organization is the MCC.
The next element is *MCP-TYPE*. As explained above this pins down one of the types currently used within the MCP.
The *Identity Provider ID (IPID)* refers to a national authority or other kind of organization that acts as an identity provider within the MCP.
If the identity provider is a national authority then the IPID must be a country code as defined by ISO 3166-1 alpha-2.
Otherwise it will be a string of the same syntax as that for *OIDs*. The *IPID* must be unique across the urn:mrn:mcp namespace.
The *Identity Provider Specific String (IPSS)* can be defined and managed by the respective identity provider in a way that is consistent and conforms to the definitions of the MRN namespace and requirements laid down by the MCC.
In particular, the identity provider must ensure that the *IPSS* identifies a particular resource uniquely for its type within the domain of the identity provider. Altogether, this will ensure that the resulting URN is globally unique.

Examples:

* urn:mrn:mcp:user:dma:alice - valid MCP MRN for a user, where dma specifies the ID Provider,  and the subsequent *IPSS* string is defined to give the username.
* urn:mrn:iala:aton:gb:sco:6789-1 - valid MRN for a marine aid to navigation (AtoN), where gb stands for United Kingdom, sco for Scotland, and the number is the scottish asset identifier. The example is from [4]. This is not a MCP MRN.
* urn:mrn:mcp:device:mirX:aton:gb:sco:6789-1 - valid MCP MRN for the same AtoN, where mirX specifies the ID Provider, and the subsequent *IPSS* string is defined to first specify the type of the device, and then to follow the country-specific convention of the IALA scheme.
* urn:mrn:mcp:service:mirY:org1:instance:s124/busan - valid MCP MRN for a service instance, where mirY and org1 specifies the ID Provider and the organization respectively. Note the use of the character slash ("/") in the last element. It must be converted to percent-encoded ‘%2F’ by following [RFC8141] when the MRN is included as a part of an URL, e.g., ‘http://example.com/urn:mrn:mcp:service:mirY:org1:instance:s124%2Fbusan’.

Requirements on decentralized MCP namespace
-------------------------------------------
The following requirements pin down that and how the MCP namespace can be managed decentrally.

* **ID1** The MCC can delegate the assignment of part of the MCP namespace to other organizations that act as identity providers. More concretely, this means that the organization, say X, must hold an *IPID*, say string "nameofx", and is then responsible for the namespace with the prefix "urn:mrn:mcp:<MCP-TYPE>:nameofx".
* **ID1.1** The MCC must ensure that each *IPID* refers to at most one identity provider.
* **ID1.2** Each Identity Provider must ensure to respect all syntax prescribed in the MRN specification. Moreover, each Identity Provider must ensure that each *IPSS* of their name space refers to at most one entity of their domain.
* **ID1.3** The MCC can give recommendations on how to structure the IPSS, e.g. to harmonize the syntax for particular types of entities. These recommendations will not be binding. However, the MCC reserves the right that a particular syntax can be binding with respect to conformance to certain profiles.
* **ID2** Every Identity Provider shall publish the syntax that describes their name space as well as provide a reference implementation that recognizes the strings of their namespace.
* **ID3** Every entity of the MCP shall hold exactly one MCP MRN (i.e. MRN governed by the MCP). This does not exclude that a MCP entity can hold other MRNs, but these must be within namespaces governed by other organizations (e.g. IMO).  Also, we will formulate exceptions concerning legacy MRNs within the MCP namespace.
* **ID3.1** Each Identity Provider shall ensure that each entity they register holds at most one MCP MRN within their namespace.
* **ID3.2** Each holder of a maritime entity shall ensure that this entity is registered with at most one MCP identity provider.

More detailed description is given in `MCC Identity Management and Security: General Approach and Basic Requirements <https://maritimeconnectivity.net/docs/mcp-idsec-1-v2.pdf>`__.
