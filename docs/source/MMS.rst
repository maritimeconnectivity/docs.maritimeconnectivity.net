.. _mms:

Maritime Messaging Service (MMS)
================================
Maritime Messaging Service (MMS for short) is a messaging component that allows authorized maritime stakeholders to send and receive message in an efficient, reliable and seamless manner within the MCP to solve the problems of the current maritime wireless data communication system.
MMS has been implemented based on eight design goals and principles:

  (1) distributed and federated operation of MMS instances
  (2) network-agnostic information delivery
  (3) securing confidentiality of user-generated data
  (4) reflection of communication link usage for connectivity at sea
  (5) easy provision and use of location-based service at sea
  (6) system design based on up-to-date market standards
  (7) incorporation of widely used & proven open source components
  (8) vigilance in quality-of-service (QoS) management.

Core functions of MMS
---------------------
Message queueing (MQ)
^^^^^^^^^^^^^^^^^^^^^
Message queueing is a function that enables ships to use the e-Navigation service efficiently and stably by converting information received from an e-Navigation service provider or other ship, which is an information sender, into a message and storing it temporarily.  And then the MMS allows the information receiver, normally ship, to fetch the corresponding message

When using the MMS, we can expect to separate of request and response into two sessions. Therefore, if a service user in the situation that does not receive the information about the requested service caused by the sudden deterioration of the communication situation, which is the characteristic of the maritime wireless communication environment, the MMS allows receiving the response information stored in the message queue after the communication network is restored without duplicated service request. However, since it is assumed that the MMS and maritime service provider maintain a stable connection with the wire, the request message transmitted from the service user side is not stored in the queue separately but relayed to the maritime service provider as soon as it is received in the MMS.

MMS’s Message Queue also supports flexible queuing policy settings. By message queueing policy, Message queue designs the message priority, the time that the message is stored in the queue and message structure to be stored in the queue. Currently, the MMS message is provided throught a polling method that provides the message when requested by the ship rather than pusing it directly to the ship. With this method, it is possible to provide a message that is not transmitted to the ship through the retransmission request even after the communication of the ship is disconnected and restored.

Currently, the MMS supports two polling methods: pure polling and long polling.  Pure polling is a method by which the client checks to see if a message arrives at the MMS for a certain period. The MMS sends a message in response to a polling request if there is a message directed to the client, and it returns an empty message if there is no message to forward.
Pure polling does not require management of client session, but there is network load due to periodic message transmission, server load on client’s polling request, and message delay by a certain period even if there is no data to be delivered by client.

Long polling is a method in which a client sends a polling request and MMS server waits until it receives a message directed to the client and reponsds when the message arrives. The client sends a polling request as soon as the message is returned. Long polling responds to a polling request only when there is a message directed to the client, and sends the message to the client when the message arrives on the MMS. Therefore, the network load is less and the delay time of the message is less than the pure polling method. However, since the management of the client session is required in MMS, the server load and server complexity are increased. Long polling is a proposed method assuming that the service user is unlikely to accept the push method. Also, MMS implementation supporting push method is being considered.

Message Relaying (MR)
^^^^^^^^^^^^^^^^^^^^^
Message Relaying in the MMS is a function that allows a message to be delivered to a recipient without depending on a network locator based on a unique identifier defined by Maritime Resource Name (MRN). In a maritime situation where the ship’s Network Locator may change, sending a message that is dependent on the Network Locator may cause problems in reliability of information transmission. This is solved through message relaying.

Every MCP user has a unique identifier. Maritime Resource Name (MRN) is used as a unique identifier to uniquely identify maritime resources. MRN is defined by IALA and follows the Uniform Resource Name format defined by the IETF standardization body. The MMS performs message delivery based on the MRN of the MMS user. If the message sender specifies the MRN as the destination and sends the message to the MMS, the MMS delivers the message to the current Network Locator of the MMS user using the Casting Manager which has the Network Locator information for MRN.

Changing the maritime communication means changes the Network Locator of the ship. If the Network Locator of the destination ship to which the message is delivered is changed in a situation where the sender is unexpected, the message delivered to the ship's previous Network Locator can not be delivered to the ship. Here, the Network Locator indicates the address on the network where the communication system can receive the message at it. In the IP Network, IP address is the Network Locator of the network. Therefore, to receive a reliable message, a message must be transmitted based on a unique identifier rather than a network locator.

The current version of MMS is implemented to support pure polling and long polling, but the design for message relaying supports push as well. A ship can utilize various communication systems at sea and the network locator to be coupled with the ship is changed accordingly. Therefore, real time tracking of the network locator and mapping with MRN are essential if push mode is supported.

In order to deliver the MRN-based message to the MMS user, it is necessary to define the Source MRN and the Destination MRN in the header of the HTTP message via the MMS. In the IP network, the destination is the destination IP address. The destination IP address of the client using MMS is MMS, and the specified destination MRN is the end point to which the actual message is transmitted. For example, even if the ship's IP address changes, the ID is a fixed value, so messages destined for the ship's ID may be directed to the changed IP address. Therefore, in order to support the push mode, the MRN should be transmitted as a unique identifier to the user designated by the MRN by inquiring and utilizing the latest network locator mapped to the corresponding identifier.

What MCC governs in MMS
------------------------
* MMS reference implementation

MMS reference implementation
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
MCC governs the reference implementation on MMS as follows:

- MMS: <To be added>
