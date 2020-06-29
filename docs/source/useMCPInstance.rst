.. _mcp-instance-usage:

Using MCP instance
==================
There are two contexts of the usage of MCP instances, MCC MCP testbed and MCP Instance Provider's instances.
Primary purpose of MCC MCP testbed is assessing new release of reference implementation and its impact to existing test services.
All entities including the services in the testbed will not be acknowledged as MCP entities.

It is strongly recommended to use the MCP Instance Provider's instance if the reader considers to consume or provide MCP services in operational purpose (Please refer to the :ref:`List of MCP Instance Providers <mcp-instance-provider-list>`).
Enrollment of the MCC MCP testbed as an organization is free and open while that of MCP Instance Provider's instance depends on the policy set by each MCP Instance Provider.

.. _mcp-instance-usage-testbed:

Using MCC MCP testbed
---------------------
MCC (or rather partners of the MCC on behalf of the MCC) through two of its members, KRISO and Frequentis, operates three environments of the MCP:

* **test environment** which will be used for testing new versions of the MCP reference software as developed by the MCC (or rather partners of the MCC on behalf of the MCC).
* **staging environment** which will be used for testing new release candidates of the MCP reference software.
* **public demonstrator environment** for use by anyone for general tests/assessment of the MCP and services. This is mainly for promotional purposes.

Only MCC members will be granted access to the test and staging environments, and these will only be used for the purposes designated for them.
Public demonstrator environment will be, as the name indicates, made available to all (relevant) external stakeholders.

The status of staging and public demonstrator environments is available in `here <https://status.maritimeconnectivity.net/>`__.

Applying your organization
^^^^^^^^^^^^^^^^^^^^^^^^^^^
Users of the MCC MCP testbed will go through a very simple validation process.

1. New users apply for access through the management portal of the public demonstrator environment by filling out the `registration form <https://management.maritimecloud.net/#/apply>`__.
2. Their organizations will be assessed though their website (for test and staging: the organizations should be members of the MCC beforehand)
3. The users will be validated through email correspondence where they are requested to use an email address belonging to the official internet domain
4. Access to the environments are handled by the MCC secretariat, which may deviate from the above procedure if they deem appropriate

Once your organization has been approved you will have access to services in the environment you applied.
Any relevant organization is invited to join the testbed by applying through the management portal of following instances:

  * `public demonstrator environment <https://management.maritimeconnectivity.net/#/apply>`__
  * `staging environment <https://staging-management.maritimeconnectivity.net/#/apply>`__
  * `test environment <https://test-management.maritimeconnectivity.net/#/apply>`__

Please refer the manual of the management portal here: https://manual.maritimeconnectivity.net/

Using MCP testbed Certificates
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
In order to use the certificates issued by the MCP Identity Registry it is needed to add MCP Root Certificates to the relevant trustchain. Here you can download the root certificates for MCP instances:

The public demonstrator environment root certificates as PEM (one file, primarily for Apache/Nginx), in JKS (for Java) or a zip with the separate files (for windows).
The staging environment root certificates as PEM (one file, primarily for Apache/Nginx), in JKS (for Java) or a zip with the separate files (for windows).
The test environment root certificates as PEM (one file, primarily for Apache/Nginx), in JKS (for Java) or a zip with the separate files (for windows).

Adding your external Identity Provider to MCP testbed
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
If your organizations wishes to connect to MCP testbed as an Identity Provider, to enable your uses to authenticate in MCP testbed, please contact Oliver Haagh at oliver@dmc.international in order to set it up. Note that currently you need to expose interfaces that supports either OpenID Connect or SAML2.

MCP testbed use cases
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
* use of identity management or certificate case
* use of service consumer case
* use of service provider case

Using MCP Instance Provider
---------------------------
MCC will provide the criteria and the procedure on vetting of organizations in MCP Instance Provider and the guideline how an MCP Instance Provider handle MCP entities.
Please refer the policy of the MCP Instance Provider you want to apply (:ref:`List of MCP Instance Providers <mcp-instance-provider-list>`).

MCP Instance Provider use cases
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
* use of identity management or certificate case
* use of service consumer case
* use of service provider case
