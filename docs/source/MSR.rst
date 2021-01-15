.. _msr:

Maritime Service Registry (MSR)
================================

Maritime Service Registry (MSR for short) is for service discovery and documentation. It fulfills the role of a design-time registry which stores human- and machine-readable documentation about the design and use of services, and a runtime registry containing the current information on where to reach a running service. Both these major parts come with extensive search functionality.
This ensures the ability for service consumers to find the service they want to consume, as well as application developers to retrieve the documentation needed for learning how to interact with a service.

Basic concepts
^^^^^^^^^^^^^^
MSR features a logical concept that separates individual implementations from their design, and furthermore from the implemented service specification which corresponds to the documentation types of the IALA's G1128 e-Navigation technical service specification guideline.
Three main parts of a service description are as follows:

* **Specification**: A technology-agnostic description of a service on a logical level, e.g. "A Weather Service"
* **Technical Design**: A description of the technology-bound, actual realization of a service on a technical level.
* **Instance**: Information about the actual URI and other relevant data about a specific running service instance.

Service Specification
^^^^^^^^^^^^^^^^^^^^^
A service specification provides operational information about a service without going into detail regarding the technology used. It is a logical description of the data and operations made available by that service, as well as their operational relevance. This information is available in both human readable DOC format, as well as machine-parseable XML, and contains the following:

* Operational context
* Interface descriptions
* Data structures
* Dynamic behavior
* Specification author

Technical Design
^^^^^^^^^^^^^^^^
The design goes into detail regarding the offered functions in a technology-specific manner, but does not contain information about a specific service instance. It contains:

* Reference to the corresponding specification
* Description of the used technology
* Detailed data structure description (If different from the specification)
* Design author

Service Instance
^^^^^^^^^^^^^^^^
Service instance is described in human readable form via an **Implementation Manual**, and in machine readable XML form via the **Instance Description**.

* **Implementation Manual** contains detailed information regarding the technologies used in the implementation, deployment instructions, and other instance-specific information. Different instances of the same specification may be run by different operators.
* **Instance Description** contains the service coverage area, various descriptive fields (keywords, etc.), geographic area, UN/LOCODE, as well as the URI under which to access this service instance.


Authentication in MSR
^^^^^^^^^^^^^^^^^^^^^^
MSR is designed to be used in conjunction with MIR, which handles authorization. In general, the usage of MSR can be summarized via the following steps:

1. Authenticate with Identity Registry via certificate/username and password/other means
2. Receive access token
3. Send requests to Service Registry, including the access token
4. Receive and handle the response to the request.

The authentication with the Identity Registry is out of scope for this documentation, as more information on this topic can be found in the corresponding ID Registry documentation. The Bearer Token received from the ID Registry is a Java Web Token in Base64-encoded form, which has to be passed to MSR with every request. The token has a limited time of validity, after which it has to be re-requested.
MSR will respond with a HTTP 503 Error if the token is out of date, the authentication token is invalid, or no token was provided. To include the authentication token, it should be included in the HTTP header via the “Authorization” header field as follows::

  GET /api/example HTTP/1.1
  Host: msr.exampledomain.com
  Authorization: Bearer SlAV32hkKG…

MSR API is stateless, there is no http session being used for authentication. The organization ID is encoded within the token and is set by the Identity Registry based on your authentication credentials. Services registered under your organization ID can only be modified by users or clients with the same organization ID.

Both ServiceAdministrationInterface and ServiceLookupInterface work in the same fashion, with the exception of the administrative functions requiring the organization ID of the client to match the organization ID of the resource to be modified.

Tokens may time out, so if the token is rejected, a new token should be requested from the Registry.

Basic usage
^^^^^^^^^^^^^

How to register a service to MSR
***********************************
To register a service, the following steps should be followed:

* Authenticate with MIR
* Register a Service Specification in MSR
* Register a Service Design in the SR which refers to the Specification
* Register a Service Instance

If any of the higher level requirements are already met, for example a new instance for an existing Design is to be registered, then the existing items don’t have to be duplicated but can be referenced.
Each of the service fragments (Specification, Design and Instance) consist of a human-readable (in PDF format) and a machine readable part (in XML). The Schema for the XML can be downloaded from the Service Registry.

How to query a service in MSR
***********************************
To query the MSR for a desired service, the flow is as follows:

* Access SR API to query Specifications, Designs and Instance information

MSR API access
***********************************
MSR is accessed by means of a REST API via HTTP protocol.
The MSR API is standardized by MCC and uses Swagger (https://swagger.io/), the OpenAPI specification, to describe the details.
An online API overview plus sample generation page can be found at MSR of MCC Testbed (https://sr.maritimeconnectivity.net/swagger-ui/index.html).

Geographic Functionality
^^^^^^^^^^^^^^^^^^^^^^^^
Service Coverage Geometry
********************************
Each service instance may be registered with an associated coverage area. This area is encoded in the service instance XML description, and should be sent in Well Known Text (WKT) format.
The service coverage area may consist of one or more geographic shapes or points. It should be noted that while a service may be defined using just one or more latitude/longitude point coordinates, it is advisable to always use an area shape like a polygon. Otherwise, queries using a point coordinate may not be able to intersect with and find the service instance.
Service coverage geometries follow the right-hand rule, meaning that points of a polygon that encloses an area have to be defined in counterclockwise order. Holes in a covered area (exclusion zones) have to be expressed as a polygon with its points defined in clockwise order.
Services registered with no coverage geometry are treated as being available world-wide, unless an UN/LOCODE has been set.

UN/LOCODE
************
Alternatively to the WKT geometry definition, service instances may also be registered with a UN/LOCODE. This is an alphanumerical designation that is mapped to a lat/lon coordinate internally. This coordinate is then also considered in geographic searches where the search geometry forms an area, like a polygon. The primary use for UN/LOCODE is a direct match for that designator, so the geographic match is implemented as a secondary means to find location-based service instances.
UN/LOCODE and custom geo-coverage are mutually exclusive. A service instance with an UN/LOCODE cannot also use a custom coverage geometry.

Geographic Queries
********************
MSR provides several API functions for querying services based on geographic location:

* **searchUnlocode** returns service instances that match the given UN/LOCODE
* **searchLocation** returns instances whose coverage area intersects with a given lat/lon coordinate.
* **searchGeometryWKT** finds all instances whose coverage area intersects with the given search geometry.
* **searchGeometryGeoJSON** Same as the WKT search, but accepts the search geometry in GeoJSON format.

Note that the same rules as for the definition of coverage areas (right-hand rule) apply: inclusive shapes have to be in counter-clockwise order, exclusion shapes in clockwise order.

Query Filter Format
^^^^^^^^^^^^^^^^^^^^^
Most API functions for retrieving service information support an optional field called "query", which can be used to narrow down results based on a set of filter attributes. For example, while a geo-query may return both REST and SOAP services, it is possible to apply a filter so only REST services are returned.

Available Filter Attributes
************************************

* instanceId
* specificationId
* designId
* name
* comment
* status
* organizationId
* keywords
* version
* mmsi
* imo
* serviceType
* unlocode
* endpointUri
* endpointType

Query
************
The query parameter is a string with the following layout::

  field1:value1 AND field2:value2 AND field3:value3

For example::

  serviceType:VIS AND organizationId:SMA

What MCC governs in MSR
^^^^^^^^^^^^^^^^^^^^^^^
* :ref:`MCP namespace <mcp-mrn>`
* REST API (https://sr.maritimeconnectivity.net/v2/api-docs)
* MSR reference implementation

MSR reference implementation
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
MCC governs the reference implementation on MSR as follows:

- MSR: https://github.com/MaritimeConnectivityPlatform/mc-serviceregistry
