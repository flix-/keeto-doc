Docker
======

Keeto ships a fully configured Docker image providing an easy and fast
way for the establishment of an environment in order to become familiar
with Keeto. This chapter gives an overview about this environment,
describes how to set it up and ultimately use it.

Overview
--------

The Docker environment consists of the following containers:

+-----------------+------------------------------+
| Container       | Description                  |
+=================+==============================+
| keeto-openssh   | Keeto-enabled OpenSSH server |
+-----------------+------------------------------+
| keeto-openldap  | Directory Service            |
+-----------------+------------------------------+
| keeto-syslog-ng | Central logging server       |
+-----------------+------------------------------+
| keeto-mariadb   | Audit database               |
+-----------------+------------------------------+

The OpenSSH server obtains access permissions and key material from the
Directory Service and sends audit relevant SSH session information to
the central logging server which stores the records in the audit
database.

Services
^^^^^^^^

The following ports are exposed by the Docker environment:

+----------------+---------------------+-----------------+
| Container      | Protocol            | Ip:Port         |
+================+=====================+=================+
| keeto-openssh  | SSH                 | 127.0.0.1:1022  |
+----------------+---------------------+-----------------+
| keeto-openldap | LDAP Plain/StartTLS | 127.0.0.1:1389  |
+----------------+---------------------+-----------------+
| keeto-openldap | LDAPS               | 127.0.0.1:1636  |
+----------------+---------------------+-----------------+
| keeto-mariadb  | MariaDB             | 127.0.0.1:13306 |
+----------------+---------------------+-----------------+

.. _openldap-settings:

OpenLDAP Settings
^^^^^^^^^^^^^^^^^

+---------------+-------------------------------------+
| Option        | Value                               |
+===============+=====================================+
| LDAP URI      | ldap://127.0.0.1:1389               |
+---------------+-------------------------------------+
| Base DN       | dc=keeto,dc=io                      |
+---------------+-------------------------------------+
| Bind DN       | cn=directory-manager,dc=keeto,dc=io |
+---------------+-------------------------------------+
| Bind password | test123                             |
+---------------+-------------------------------------+

.. _mariadb-settings:

MariaDB Settings
^^^^^^^^^^^^^^^^

+----------+-----------------+
| Option   | Value           |
+==========+=================+
| Ip:Port  | 127.0.0.1:13306 |
+----------+-----------------+
| Database | keeto-audit     |
+----------+-----------------+
| Username | root            |
+----------+-----------------+
| Password | test123         |
+----------+-----------------+

.. _openssh-access-permissions:

OpenSSH Access Permissions
^^^^^^^^^^^^^^^^^^^^^^^^^^

+-----------+---------------+------------------+
| Key/User  | Direct Access | Access On Behalf |
+===========+===============+==================+
| birgit    | Yes           | \-               |
+-----------+---------------+------------------+
| bjoern    | No            | keeto            |
+-----------+---------------+------------------+
| oliver    | No            | slapd, opendj    |
+-----------+---------------+------------------+
| sebastian | No            | keeto            |
+-----------+---------------+------------------+
| trixi     | No            | slapd, opendj    |
+-----------+---------------+------------------+
| wolfgang  | Yes           | \-               |
+-----------+---------------+------------------+

Prerequisites
-------------

The following software packages are needed in order to run the Keeto
Docker environment:

* Docker
* Docker Compose
* SSH client
* LDAP client (optional)
* MariaDB client (optional)

Although any LDAP client will do Apache Directory Studio is recommended
as Keeto provides an export of the connection settings needed to
configure the client for the usage with the Docker environment. The SSH
client has to support either PKCS#8 or PuTTY's .ppk private key format
for public key authentication.

Setup
-----

Grab the source code tarball from https://keeto.io and unpack the
distribution. All files needed to setup the Docker environment are
included in the 'samples/docker' directory::

    <user>$ wget https://keeto.io/static/downloads/keeto-0.4.1-beta/keeto-0.4.1-beta.tar.gz
    <user>$ tar xvfz keeto-0.4.1-beta.tar.gz
    <user>$ cd keeto-0.4.1-beta/samples/docker

Now start the containers using Docker Compose with the following
command::

    <root>$ docker-compose up -d

Docker will download the images if they are not already available
locally and subsequently start the environment. Finally you should see
the following output::

    Creating network "docker_keeto-net" with driver "bridge"
    Creating keeto-mariadb
    Creating keeto-openldap
    Creating keeto-syslog-ng
    Creating keeto-openssh

Thats it! The Keeto Docker environment is now fully operational.

Usage
-----

Now that the environment is up and running you are able to play around
and gain a better understanding of Keeto. Configure your favourite LDAP
client with the settings described in :ref:`openldap-settings` and
browse/modify the content in the OpenLDAP Directory Service. If you are
using Apache Directory Studio you might wanna import the connection
settings from the 'samples/docker/misc' folder. The environment comes
with some predefined access permissions as described in
:ref:`openssh-access-permissions`. The private key material for the
various logins is available in the 'samples/docker/keys' folder. Note
that some SSH clients require the private key to have certain access
permissions. If you are using such a client change permissions of the
private key file accordingly.

The following two examples show logins with the OpenSSH SSH client for
a user that has direct access and another one that has access on behalf
of another account::

    <user>$ chmod 600 keys/birgit-key.pem
    <user>$ ssh -i keys/birgit-key.pem -p 1022 birgit@localhost
    <user>$ chmod 600 keys/oliver-key.pem
    <user>$ ssh -i keys/oliver-key.pem -p 1022 slapd@localhost

Connect to the database (see: :ref:`mariadb-settings`) to have a look at
the SSH session logging.

