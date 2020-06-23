.. _setup-mir:

Setting up MIR
==============

Introduction
------------
The MCP Identity Registry (MIR) is an identity solution targeted against actors of the maritime domain. It consists of three parts – an identity broker for logging in and federating users from other identity providers, a public key infrastructure for issuing and managing digital certificates and an API for management and access to the two first parts.
For the identity broker Keycloak has been chosen as it is open source and because it is based on OpenID Connect which is a widely used protocol for token based authentication.
This document will provide a generic guideline of how to setup and deploy the MIR on a local system.
The MCP is formerly known as the Maritime Cloud and therefore there will still be references to that in this document.
As the different components of the MIR rely on each other the configuration of one component may rely on the configuration of another component, but this will be documented as thoroughly as possible.
Complementary documentation of setup can be found at:

- https://github.com/MaritimeConnectivityPlatform/IdentityRegistry
- https://github.com/MaritimeConnectivityPlatform/MCP-PKI
- https://github.com/MaritimeConnectivityPlatform/MCPKeycloakSpi

For easier management of the IR API it is advised to also setup the MCP management portal. The code for this can be found at https://github.com/MaritimeConnectivityPlatform/MCP-Portal.

We strongly recommend you to read the `MIR setup guide <https://github.com/MaritimeConnectivityPlatform/IdentityRegistry/blob/master/setup/guide/MIR_setup.pdf>`__ to get more details.

Prerequisites
--------------
- Java JDK 8 (OpenJDK is preferred, but not a requirement)
- Maven 3+
- A running MySQL/MariaDB instance with databases and users created
- Keycloak 9.0.0 (Only needed if not using the Docker image)
- NGINX
- Docker (if you want to run the identity registry using Docker containers)
- A SMTP server

Setting up PKI
--------------
A part of the MIR is based on public key certificates.
For this a custom PKI has been developed to issue and manage certificates for stakeholders.
To set up the PKI you must get the source code for it from
https://github.com/MaritimeConnectivityPlatform/MCP-PKI/archive/v0.10.0-hotfix1.zip
‘git clone https://github.com/MaritimeConnectivityPlatform/MCP-PKI.git’.
Alternatively you can also get the needed executable from
https://github.com/MaritimeConnectivityPlatform/MCP-PKI/releases/download/v0.10.0-hotfix1/mc-pki-0.10.0-jar-with-dependencies.jar.
After you have done this you can build the project from the directory storing the code using the following command:

  mvn clean install

After this has finished there should now be a new folder called target.
This folder contains the resulting artifacts of the build process, including the jar file mc-pki-0.10.0-SNAPSHOT-jar-with- dependencies.jar which is the artifact that is going to be used to generate the root certificate authority (CA) and sub-CAs for the PKI.

The next step is to generate the root CA. This can be done using the following command where you should of course replace the different fields with information relevant to your setup. For example here urn:mrn:mcp:ca:maritimeconnectivity will be the MRN for the root CA, where you might want it to be called something else in your setup::

  java -jar mc-pki-0.10.0-SNAPSHOT-jar-with-dependencies.jar \
  --init \
  --root-ca-alias urn:mrn:mcp:ca:maritimeconnectivity \ --truststore-path mcp-truststore.jks \ --truststore-password changeit \
  --root-keystore-path root-ca-keystore.jks \ --root-keystore-password changeit \ --root-key-password changeit \
  --x500-name "C=DK, ST=Denmark, L=Copenhagen, O=MCP Test, OU=MCP Test, CN=MCP Test Root Certificate, E=info@maritimeconnectivity.net" \
  --crl-endpoint "https://api.example.com/x509/api/certificates/crl/urn:mrn:mcp:ca:maritimeconnectivity"

You should now have two different keystore files called mcp-truststore.jks, which contains the root CA certificate, and root-ca-keystore.jks, which contains. The password for both of these is ‘changeit’, but can be set to something else by changing the values of truststore-password and root- keystore-password.
Now you will need to create a certificate revocation list (CRL) for the root CA. This can be done using the following command::

  java -jar mc-pki-0.10.0-SNAPSHOT-jar-with-dependencies.jar \
  --generate-root-crl \
  --root-ca-alias urn:mrn:mcp:ca:maritimeconnectivity \ --root-keystore-path root-ca-keystore.jks \ --root-keystore-password changeit \
  --root-key-password changeit \ --revoked-subca-file revoked-subca.csv \ --root-crl-path root-ca.crl

The file revoked-subca.csv should either be empty or contain the sub CAs that have been revoked separated by commas and should have a format like this
<serial-number>;<revocation-reason>;<revocation-date>
The resulting file root-ca.crl contains the list of sub CAs that have been revoked.

The next step is then to generate the sub CA(s) that is going to be used for issuing client certificates. The command below shows an example of how to do this::

  java -jar mc-pki-0.10.0-SNAPSHOT-jar-with-dependencies.jar \ --create-subca \
  --root-ca-alias urn:mrn:mcp:ca:maritimeconnectivity \ --root-keystore-path root-ca-keystore.jks \ --root-keystore-password changeit \ --root-key-password changeit \
  --truststore-path mc-truststore.jks \
  --truststore-password changeit \
  --subca-keystore subca-keystore.jks \
  --subca-keystore-password changeit \
  --subca-key-password changeit \
  --x500-name "UID=urn:mrn:mcp:ca:mcp-idreg, C=DK, ST=Denmark, L=Copenhagen, O=MCP Test, OU=MCP Test, CN=MCP Test Identity Registry, E=info@maritimeconnectivity.net" \
  --crl-endpoint "https://api.example.com/x509/api/certificates/crl/urn:mrn:mcp:ca:mcp-idreg"

This creates a sub CA that contains the information given in x500-name and signed by the root CA. The sub CA certificate is added to mc-truststore.jks and the keys of the sub CA are stored in subca-
keystore.jks. Note that in crl-endpoint you should change ‘api.example.com’ to whatever domain you are using and the MRN after ‘crl’ should be changed to whatever you wrote in ‘UID’.
All the files generated in this section should be stored for later usage.

Setting up database
----------------------------
Prerequisite of installing both Keycloak and identity registry API is an installation of MariaDB. For Keycloak, it is important to set exactly same environment defined in the dockerfile of MCPKeycloakSPI (https://github.com/MaritimeConnectivityPlatform/MCPKeycloakSpi/blob/ master/docker/Dockerfile). For example, if it contains ‘ENV DB_DATABASE keycloak’, you have to create a database ‘keycloak’ through CREATE DATABASE keycloak;. It is also required to set a dedicated user id in mysql.user and a password that perfectly corresponds to the values in the dockerfile. For the case using the prebuilt Docker container will be described later the database environment setup should be matched to the dockerfile of the repository of it.
Identity registry API provides bash files to setup the initial database, which are available on the repository (https://github.com/MaritimeConnectivityPlatform/IdentityRegistry/tree/master/setup). ‘setup-db.sh’ that should be executed first includes execution of a series of SQL statements in ‘create-database-and-user.sql’. After that flywaydb will handle creation and migration of tables when the program starts, and will fail if the tables already exists. Importing the content from ‘create-mc-org.sql’ should only be done after the first execution as described in the section Putting everything together. For the case to remove the database and the user you can just execute ‘drop- db.sh’ or just ‘drop-db-and-user.sql’.

Setting up Keycloak
----------------------------
Setting up Keycloak can be done using either a standalone installation of Keycloak or using the prebuilt Docker container.
Using the Docker container is recommended, but if a standalone installation is required a guide on how to use this can be found at https://github.com/ MaritimeConnectivityPlatform/MCPKeycloakSpi.

Using a Docker container
^^^^^^^^^^^^^^^^^^^^^^^^
A prebuilt Docker container for Keycloak with MCP specific functionality can be found at https://cloud.docker.com/u/dmadk/repository/docker/dmadk/keycloak-mysql-mc-ha. At the time of writing the latest tag for this is 0.10.0. The latest tag is built from the latest code from the main git branch and is therefore not guaranteed to be stable.
For creating the Docker container using the 0.10.0 tag you can use the following command:
docker create --name=mir-keycloak --restart=unless-stopped -p 8080:8080 \ -v <directory for configurations>:/mc-eventprovider-conf \
-e MC_IDREG_SERVER_ROOT=https://api-x509.example.com \
-e JGROUPS_DISCOVERY_EXTERNAL_IP=<valid IP address> dmadk/keycloak-mysql-mc-ha:0.10.0
The directory that is mounted to ‘/mc-eventprovider-conf’ in the container must contain the following files:

* mc-truststore.jks
* idbroker-updater.jks

The first one is the one that was generated earlier. The second one is only needed if user federation
is used and will be described later on how to generate.
The URL given as value for the variable MC_IDREG_SERVER_ROOT must be set to the same value as the one set in the NGINX configuration.
If running Keycloak in clustered mode the value of JGROUPS_DISCOVERY_EXTERNAL_IP must be set to an IP address that Keycloak is reachable on. If it is not to be run in clustered mode it can just be set to 127.0.0.1 or another random IP.
As default the database and the user that is to be used for it are set to be keycloak and the password for the database user is set to be password. If you want to use other values than these you can set them as variables in the above command as described at https://hub.docker.com/r/jboss/keycloak.
To start Keycloak you can then use the command::

  docker start mir-keycloak

Setting up Realms in Keycloak
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
When you have gotten Keycloak up and running you will need to setup the needed Realms.
To login to the admin interface of Keycloak go to http://localhost:8080 if you have Keycloak running on localhost or else use the domain set in your NGINX configuration.
The first time you login you will need to setup an admin user and password.
After that you will need to import the three [name]-realm.json files from https://github.com/MaritimeConnectivityPlatform/IdentityRegistry/tree/master/setup.
This can be done in the admin interface of Keycloak by hovering the mouse over the dropdown ‘Select realm’ and then click ‘Add realm’. Here you can then import the [name]-realm.json files one by one.
Note that the URLs to OIDC clients, identity providers, etc. are set to localhost in the [name]-realm.json files so these will need to be updated to the correct URLs after the files have been imported.

Setting up MIR API
----------------------------
<to be updated>

Setting up NGINX
----------------------------
Setting up NGINX is not required, but it is highly recommended to use it as a reverse proxy because it makes handling of TLS and client certificate authentication a lot easier.
In the supplied file nginx.conf is an example of how NGINX can be used to proxy incoming requests to the Identity Registry API and Keycloak assuming that they are running on the same machine.
There are some requirements that need to be met to use the configuration.
The first one is that you need to have a domain registered with the sub-domains api, api-x509, maritimeid, maritimeid-x509. These can of course be changed to something else in the configuration is necessary.
The second requirement is that you need to have a TLS certificate for your domain. The path to the certificate should then be defined in the variable ssl_certificate and then the path to the corresponding private key should be defined in the variable ssl_certificate_key.
The third requirement is that the variable ssl_crl should point to an up-to-date file that contains the CRLs of the root CA and the sub CAs concatenated together. How to set this up will be described later in this document.
The fourth requirement is that the variable ssl_client_certificate must point to a file that contains the certificates of the root CA and the sub CAs in PEM format. These can be extracted from the mc- truststore.jks generated in the previous step using a tool like KeyStore Explorer.
NGINX can either be installed and run directly on the machine or in a Docker container. Note that if you choose to run it in a Docker container it is recommended to run it with the option --net=host so it binds directly to the network interface of the host instead of using the default bridge driver.
