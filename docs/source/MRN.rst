.. _mcp-mrn:

MCP MRN namespace
================================
MCP MRN namespace is a subspace of the Maritime Resource Name (MRN) space, which is an official URN namespace.
This page aims to provide the syntax of the MCP `MRN (Maritime Resource Name) <https://www.iana.org/assignments/urn-formal/mrn>`__ which is described in `MCC Identity Management and Security: General Approach and Basic Requirements <https://maritimeconnectivity.net/docs/mcp-idsec-1-v2.pdf>`__.

MCP MRN syntax
--------------
The syntax of a MRN governed by the MCC (short: MCP MRN or MCP name) based on the Augmented Backus-Naur Form as specified in `[RFC5234] <https://tools.ietf.org/html/rfc5234>`__ is as follows:

  MCP-MRN = "urn" ":" "mrn" ":" "mcp" ":" MCP-TYPE ":" IPID ":" IPSS
  MCP-TYPE = "entity" / "mir" / "mms" / "msr" / LEGACY
  LEGACY = "device" / "org" / "user" / "vessel" / "service"
  IPID = <CountryCode> / 3*22IPID-CHAR
  IPID-CHAR = unreserved / pct-encoded
  IPSS = pchar *(pchar / "/")

, where "mcp" specifies that the governing organization is the MCC and other attributes are defined:

* **MCP-TYPE**: an entity type in the :ref:`MCP types mcp-type`
* **IPID**: *Identity Provider ID (IPID)* refers to a national authority or other kind of organization that acts as an identity provider within the MCP. If the identity provider is a national authority then the IPID must be a country code as defined by ISO 3166-1 alpha-2. Otherwise it will be a string of the same syntax as that for *OIDs*. The *IPID* must be unique across the urn:mrn:mcp namespace.
* **IPSS**: *Identity Provider Specific String (IPSS)* can be defined and managed by the respective identity provider in a way that is consistent and conforms to the definitions of the MRN namespace and requirements laid down by the MCC. In particular, the identity provider must ensure that the *IPSS* identifies a particular resource uniquely for its type within the domain of the identity provider.

Altogether, this will ensure that the resulting URN is globally unique.

Examples
^^^^^^^^^

* urn:mrn:mcp:entity:dma:alice - valid MCP MRN for a user, where 'dma' specifies the ID Provider,  and the subsequent *IPSS* string is defined to give the username.
* urn:mrn:iala:aton:gb:sco:6789-1 - valid MRN for a marine aid to navigation (AtoN) but not valid as an MCP MRN, where 'gb' stands for United Kingdom, 'sco' for Scotland, and the number is the scottish asset identifier.
* urn:mrn:mcp:entity:mirX:aton:gb:sco:6789-1 - valid MCP MRN for the same AtoN, where 'mirX' specifies the ID Provider, and the subsequent *IPSS* string is defined to first specify the type of the device, and then to follow the country-specific convention of the IALA scheme.
* urn:mrn:mcp:entity:mirY:org1:instance:s124/busan - valid MCP MRN for a service instance, where 'mirY' and 'org1' specifies the ID Provider and the organization respectively. Note the use of the character slash ("/") in the last element. It must be converted to percent-encoded ‘%2F’ by following `[RFC8141] <https://tools.ietf.org/html/rfc8141>`__ when the MRN is included as a part of an URL, e.g., ‘http://example.com/urn:mrn:mcp:service:mirY:org1:instance:s124%2Fbusan’.
