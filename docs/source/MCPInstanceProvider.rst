.. _mcp-instance-provider:

MCP Instance Provider
================================
An MCP instance provider is an organization which takes responsibility of operating an MCP instance.
An MCP instance is a number of environments of running MCP software.
The MCP software is a software that meets the requirements set forth by the Maritime Connectivity platform Consortium (MCC).
MCP software comprises three main components; MIR (Maritime Identity Registry), MSR (Maritime Service Registry) or MMS (Maritime Messaging Service). The MIR itself has two subcomponents; the MIR PKI and the MIR OIDC broker.

An MCP environment is either a

* Running MIR PKI
* Running MIR PKI and MIR OIDC
* Running MSR
* Running MMS

Or any combination hereof.

An instance provider will always be running several environments - at least a staging and production environment, which is required by the system configuration requirements (see later). Most instance providers will typically have the need to run even more environments than that.
Any MCP instance running environments consisting of only an MMS and/or MSR environments without a MIR component will need to rely on another MCP instance with a running MIR for authentication purpose.

.. _mcp-instance-provider-list:

List of MCP Instance Providers
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
There are currently no fully compliant instances of MCP - since the final specifications for MCP are still in the process of being developed. However, two organisations are currently setting up instances that will be MCP compliant:

* `Navelink Industry Consortium <https://management.maritimecloud.net/#/apply>`__
* `SMART Navigation project <https://www.smartnav.org/eng/html/Index_New/>`__ by the Ministry of Ocean and Fisheries (Korea)

Up to date information can be found in the `MCP official page <https://maritimeconnectivity.net/#mcp_instances>`__.

.. _mcp-instance-provider-how-to:

How to be an MCP Instance Provider
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
MCC is actively working on the requirements for MCP Instance Provider. Please contact to the `secretariat <mailto:mcc@dmc.international>`_ to get further detail.
