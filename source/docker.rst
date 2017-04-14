Docker
======

Keeto ships a fully configured Docker image providing an easy and fast
way for the establishment of an environment in order to become familiar
with Keeto. This chapter gives an overview about this environment,
describes how to set it up and ultimately use it.

Overview
--------

The Docker environment consists of the two containers 'keeto-openldap'
and 'keeto-openssh'. 'keeto-openldap' provides a Directory Service based
on OpenLDAP and 'keeto-openssh' runs an OpenSSH server configured with
Keeto. The Directory Service contains various entries reflecting the
access permissions, key material, users etc. for the OpenSSH server
running within the 'keeto-openssh' container.

Services
^^^^^^^^

The following ports are exposed by the Docker environment and bound
to the local machine:

+----------------+---------------------+----------------+
| Container      | Protocol            | Port           |
+================+=====================+================+
| keeto-openldap | LDAP Plain/StartTLS | 127.0.0.1:1389 |
+----------------+---------------------+----------------+
| keeto-openssh  | SSH                 | 127.0.0.1:1022 |
+----------------+---------------------+----------------+

Syslog Settings
^^^^^^^^^^^^^^^

The local /dev/log socket is mounted to the container's filesystem in
order to obtain syslog messages from container components. The
following syslog identifiers/facilities are used:

+-----------+-------------------+-----------------+
| Component | Syslog Identifier | Syslog Facility |
+===========+===================+=================+
| OpenLDAP  | slapd             | LOG_LOCAL4      |
+-----------+-------------------+-----------------+
| OpenSSH   | sshd              | LOG_LOCAL0      |
+-----------+-------------------+-----------------+
| Keeto     | keeto             | LOG_LOCAL1      |
+-----------+-------------------+-----------------+

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

.. _openssh-access-permissions:

OpenSSH Access Permissions
^^^^^^^^^^^^^^^^^^^^^^^^^^

+-----------+---------------+------------------+
| User      | Direct Access | Access On Behalf |
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
* LDAP client
* SSH client
* Syslog (optional)

Although any LDAP client will do Apache Directory Studio is recommended
as Keeto provides an export of the connection settings needed to
configure the client for the usage with the Docker environment. The SSH
client has to support either PKCS#8 or PuTTY's .ppk private key format
for public key authentication.

Setup
-----

All files needed to setup the Docker environment are included in the
'samples' directory shipped with the source distribution/RPM package.
If the RPM package has already been utilized to install Keeto use the
following command to locate the 'samples' directory and change to
the 'docker' directory within::

    <user>$ rpm -qd keeto

If you would like to use the source distribution grab the tarball from
https://keeto.io unpack the distribution and change to the 'docker'
directory within the 'samples' folder::

    <user>$ tar xvfz keeto-0.3.0-beta.tar.gz
    <user>$ cd keeto-0.3.0-beta/samples/docker

Now start the containers using Docker Compose with the following
command::

    <root>$ docker-compose up -d

Docker will download the images if they are not already available
locally and then start the environment. Finally you should see the
following output::

    Creating network "docker_keeto-net" with driver "bridge"
    Creating keeto-openldap
    Creating keeto-openssh

Thats it! The Keeto Docker environment is now fully operational.
Optionally configure your local syslog daemon to log messages from
container components. A sample configuration file for syslog-ng can be
found in the 'samples' directory.

Usage
-----

Now that the environment is up and running you are able to play around
and gain a better understanding of Keeto. You should definitely
configure your favorite LDAP client with the settings described in
:ref:`openldap-settings` and browse/modify the content in the
OpenLDAP Directory Service. If you are using Apache Directory Studio you
might wanna import the connection settings from the 'samples/docker/misc'
folder. Furthermore you should try the various predefined logins with
your SSH client and check log files. The OpenSSH access permissions can
be found at :ref:`openssh-access-permissions`. Keys for each user are
available in the 'samples/docker/keys' folder.

The following shows two exemplary logins with the OpenSSH SSH client
for a direct access and an access on behalf of another account::

    <user>$ ssh -i samples/docker/keys/birgit-key.pem -p 1022 birgit@localhost
    <user>$ ssh -i samples/docker/keys/oliver-key.pem -p 1022 slapd@localhost

