.. _mcp-history:

History
==============

Maritime Connectivity Platform, formerly known as Maritime Cloud project started out as an internal project at the Danish Maritime Authority in the autumn of 2012. As part of the e-Navigation process the Danish Maritime Authority had been working on an e-Navigation Prototype Display system called EPD. The EPD consist of two applications for demonstrating potential e-navigation solutions. An ECDIS like ship side application and a shore side application.
During the development it was clear that AIS was a severe limiting factor when communicating between the ship side and the shore side. Putting a lot of limitations of the different kind of maritime services that could be developed. Especially three issues was identified.

* **Lack of bandwidth**: Only limited amounts of data could be transferred. Often using complex encoding schemes such as application specific AIS messages.
* **Ease of development**: There was no easy way to simulate AIS communication without a very complex developer environment.
* **Limited Signal Coverage**: There are certain types of services where the actors communicating might not all be within reach of radio signals.

The first prototype was built in the winter of 2012 and was implemented in the EPD in spring 2013. It just featured basic point to point communication. This is basically what is the Maritime Message Service now. During the summer of 2013 the vision for a general framework for maritime communication was created. Including registries for services and identities in addition to the message based framework. It also got its name the “Maritime Connectivity Platform” as a sort of umbrella name for the various underlying services. In 2014 the first release of the MCP reference implementation was made available for the public.
