Getting Started
===============

Prerequisites
-------------

The following packages are needed in order to run Keeto:

* OpenSSH >= 6.2
* LDAP Server
* Syslog
* PAM
* pkg-config >= 0.9.9
* libConfuse >= 2.7
* libcheck >= 0.9.9
* OpenSSL 1.0.x
* libldap

Make sure those components are installed and configured prior setting
up Keeto.

Installation
------------

Grab the latest source tarball from https://keeto.io and unpack it::

    $ tar xvfz keeto-x.x.x.tar.gz
    $ cd keeto-x.x.x
    $ ./configure --libdir=/lib/security   # for 32 bit systems
    $ ./configure --libdir=/lib64/security # for 64 bit systems 
    $ make
    $ make check
    $ sudo make install

Configuration
-------------

Keeto
^^^^^

Copy the configuration file pox509.conf from the samples directory into
the OpenSSH configuration root folder. As the config contains sensitive
data make sure it is only readable/modifiable by a priviledged user::

    $ SSH_DIR=/etc/ssh
    $ cp samples/pox509.conf $SSH_DIR
    $ chown root:root $SSH_DIR/pox509.conf
    $ chmod 600 $SSH_DIR/pox509.conf

Furthermore create a directory where the authorized_keys files of the
users are placed and a directory for trusted ca certificates and crl's::

    $ mkdir $SSH_DIR/authorized_keys
    $ chown root:root $SSH_DIR/authorized_keys
    $ chmod 711 $SSH_DIR/authorized_keys
    $ mkdir $SSH_DIR/cert_store
    $ chown root:root $SSH_DIR/cert_store
    $ chmod 700 $SSH_DIR/cert_store

Copy all trusted ca certificates and crl's (optional) into the cert
store and create symlinks with::

    $ c_rehash $SSH_DIR/cert_store

Finally edit the configuration file and adjust it to your needs. An
explanation of the various options can be obtained from the following
table <TODO>.

OpenSSH
^^^^^^^

PAM
^^^

Syslog
^^^^^^

LDAP Server
^^^^^^^^^^^

