Getting Started
===============

Prerequisites
-------------

The following packages are needed in order to run Keeto:

* OpenSSH >= 6.2
* Directory Service
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

Source
^^^^^^

Grab the source tarball from https://keeto.io and unpack/build. Note
that the library installation directory for PAM modules (--libdir)
differs for various architectures/distros. Consult the documentation of
your distro to figure out the right path::

    <user>$ tar xvfz keeto-0.2.0-beta.tar.gz
    <user>$ cd keeto-0.2.0-beta
    <user>$ ./configure --libdir=/lib64/security
    <user>$ make
    <user>$ make check
    <root>$ make install

.. _rpm-installation:

RPM
^^^

Grab the RPM package from https://keeto.io and install::

    <root>$ rpm -i keeto-0.2.0-0.1.beta.el7.centos.x86_64.rpm

This installs the PAM modules and creates an initial configuration file
keeto.conf as well as the authorized_keys and cert_store directories
with the proper access permissions in /etc/ssh. Furthermore documentation
is deployed.

Configuration
-------------

This part describes the configuration of the various components based
on a installation from source. If an RPM package has been utilized
certain steps do not need to be performed (see :ref:`rpm-installation`).
Also notice that the samples directory is not present for an RPM based
installation. However all sample files included in the source
distribution are deployed as documentation during RPM installation.
Their location can be determined as follows::

    <user>$ rpm -qd keeto

Keeto
^^^^^

Copy the configuration file keeto.conf from the samples directory into
the OpenSSH configuration root folder and adjust it to your needs. An
explanation of the various options can be found within the file. As the
config contains sensitive data make sure it is only readable/modifiable
by a privileged user::

    <root>$ SSH_DIR=/etc/ssh
    <root>$ cp samples/keeto.conf $SSH_DIR
    <root>$ chmod 600 $SSH_DIR/keeto.conf

Furthermore create a directory where the authorized_keys files of the
users are placed and a directory for trusted CA certificates and CRL's::

    <root>$ mkdir $SSH_DIR/authorized_keys
    <root>$ chmod 711 $SSH_DIR/authorized_keys
    <root>$ mkdir $SSH_DIR/cert_store
    <root>$ chmod 700 $SSH_DIR/cert_store

Copy all trusted CA certificates and CRL's (optional) for verifying the
user certificates into the cert store. Make sure the whole chain except
the end entity certificates are present. If STARTTLS is used for the
the LDAP connection also include the necessary certificates here.
Finally create symlinks with::

    <root>$ c_rehash $SSH_DIR/cert_store

OpenSSH
^^^^^^^

Make sure at least the following options are reflected in your
sshd_config file::

    PubkeyAuthentication yes
    ChallengeResponseAuthentication yes
    AuthorizedKeysFile /etc/ssh/authorized_keys/%u
    UsePAM yes
    AuthenticationMethods keyboard-interactive:pam,publickey

If you are starting from scratch consider using the sshd_config file
provided in the samples directory as a starting point. Restart OpenSSH
after all changes are made.

PAM
^^^

Copy the PAM configuration file for sshd to inject Keeto into the
authentication process of OpenSSH::

    <root>$ cp samples/sshd /etc/pam.d

Syslog
^^^^^^

Keeto logs to the syslog facility specified in keeto.conf. Adjust your
syslog server accordingly. A sample config for syslog-ng can be found
in the samples directory that logs Keeto output to a local file.

Directory Service
^^^^^^^^^^^^^^^^^

Keeto consults a Directory Service in order to obtain current access
permissions and keys. The relevant entities and their relationship
are described in :ref:`ldap-data-model`. General configuration is software
dependent and not not outlined here. The samples directory however
contains relevant configurations for OpenLDAP and LDIF files for setting
up a testing environment.

