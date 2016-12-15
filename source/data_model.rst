Data Model
==========

General
-------

<x> := from config
Note that the overview does not specify what is possibly allowed by the
LDAP schema but rather what is supported by Keeto.

Entities
--------

SSH server
^^^^^^^^^^

The SSH server entry specifies the relevant access profiles that shall
be taken into account. Keeto determines the right SSH server entry
through a unique identifier that is specified in the Keeto configuration
that must match the identifier in the SSH server entry in the Directory
Server.::

  objectClass: top
  objectClass: keetoSSHServer

+--------------------+-----------+--------------+----------------------------------+
| Attribute          | Mandatory | Single-Value | Description                      |
+--------------------+-----------+--------------+----------------------------------+
| cn                 | yes       | no           | RDN of SSH server entry.         |
+--------------------+-----------+--------------+----------------------------------+
| uid                | yes       | yes          | Unique identifier of SSH server. |
|                    |           |              | See also: ssh_server_uid.        |
+--------------------+-----------+--------------+----------------------------------+
| keetoAccessProfile | yes       | no           | DN to Keeto access profile.      |
+--------------------+-----------+--------------+----------------------------------+
| description        | no        | no           | SSH server description.          |
+--------------------+-----------+--------------+----------------------------------+

Access profiles
^^^^^^^^^^^^^^^

Keeto supports two different access profiles. Direct access profiles
provide regular access to one's own account whereas access on behalf
profiles provide access to accounts of someone else.

Direct access profile
~~~~~~~~~~~~~~~~~~~~~

A direct access profile specifies a reference to a group of key
providers that shall be able to login with it's own account. Optionally
keystore options can be specified that are taken into account for all
key providers. A direct access profile can be enabled / disabled.::

  objectClass: top
  objectClass: keetoAccessProfile
  objectClass: keetoDirectAccessProfile

+----------------------+-----------+--------------+-------------------------------------+
| Attribute            | Mandatory | Single-Value | Description                         |
+----------------------+-----------+--------------+-------------------------------------+
| cn                   | yes       | no           | RDN of direct access profile entry. |
+----------------------+-----------+--------------+-------------------------------------+
| keetoEnabled         | yes       | yes          | Enable/Disable profile.             |
+----------------------+-----------+--------------+-------------------------------------+
| keetoKeyProvider     | yes       | yes          | DN to Keeto key provider.           |
+----------------------+-----------+--------------+-------------------------------------+
| keetoKeystoreOptions | no        | yes          | DN to Keeto keystore options.       |
+----------------------+-----------+--------------+-------------------------------------+
| description          | no        | no           | Direct access profile description.  |
+----------------------+-----------+--------------+-------------------------------------+

Access on behalf profile
~~~~~~~~~~~~~~~~~~~~~~~~

Access on behalf profiles enable authentication on behalf of someone
else. Target keystores specify a group of accounts that can be used
by all key providers on behalf. As with direct access profiles keystore
options can be specified optionally and profiles can be enabled /
disabled.::

  objectClass: top
  objectClass: keetoAccessProfile
  objectClass: keetoAccessOnBehalfProfile

+----------------------+-----------+--------------+----------------------------------------+
| Attribute            | Mandatory | Single-Value | Description                            |
+----------------------+-----------+--------------+----------------------------------------+
| cn                   | yes       | no           | RDN of access on behalf profile entry. |
+----------------------+-----------+--------------+----------------------------------------+
| keetoEnabled         | yes       | yes          | Enable/Disable profile.                |
+----------------------+-----------+--------------+----------------------------------------+
| keetoKeyProvider     | yes       | yes          | DN to Keeto key provider.              |
+----------------------+-----------+--------------+----------------------------------------+
| keetoTargetKeystore  | yes       | yes          | DN to Keeto target keystore.           |
+----------------------+-----------+--------------+----------------------------------------+
| keetoKeystoreOptions | no        | yes          | DN to Keeto keystore options.          |
+----------------------+-----------+--------------+----------------------------------------+
| description          | no        | no           | Access on behalf profile description.  |
+----------------------+-----------+--------------+----------------------------------------+

Key provider
^^^^^^^^^^^^

A key provider is an entry that provides the key material through
X.509 certificate(s). It is identified by its UID. All relevant
attributes are specified in the Keeto configuration file to adjust it
to several deployments. Key providers are relevant for both direct
access profiles and access on behalf profiles.

+-------------------------------+-----------+--------------+----------------------------+
| Attribute                     | Mandatory | Single-Value | Description                |
+-------------------------------+-----------+--------------+----------------------------+
| <ldap_key_provider_uid_attr>  | yes       | yes          | UID of key provider.       |
+-------------------------------+-----------+--------------+----------------------------+
| <ldap_key_provider_cert_attr> | yes       | no           | X.509 certificate of user. |
+-------------------------------+-----------+--------------+----------------------------+

Target keystore
^^^^^^^^^^^^^^^

A target keystore is only relevant for access on behalf profiles.
It specifies the accounts that can be used on behalf of the key providers.

+---------------------------------+-----------+--------------+-------------------------+
| Attribute                       | Mandatory | Single-Value | Description             |
+---------------------------------+-----------+--------------+-------------------------+
| <ldap_target_keystore_uid_attr> | yes       | yes          | UID of target keystore. |
+---------------------------------+-----------+--------------+-------------------------+

Groups
^^^^^^

Both key providers and target keystores are not linked individually but
rather in groups. To support various deployments the group member
attributes can be tailored in the Keeto configuraiton file.

+------------------------------------------+-----------+--------------+----------------------------------------+
| Attribute                                | Mandatory | Single-Value | Description                            |
+------------------------------------------+-----------+--------------+----------------------------------------+
| <ldap_key_provider_group_member_attr> /  | yes       | no           | DN to key provider /  target keystore. |
| <ldap_target_keystore_group_member_attr> |           |              |                                        |
+------------------------------------------+-----------+--------------+----------------------------------------+


Keystore options
^^^^^^^^^^^^^^^^

Keystore options can be optionally specified and linked to access
profiles to restrict access with regard to the location a user is
connecting from and the space of commands he is allowed to execute.::

  objectClass: top
  objectClass: keetoKeystoreOptions

+----------------------------+-----------+--------------+-----------------------------------------+
| Attribute                  | Mandatory | Single-Value | Description                             |
+----------------------------+-----------+--------------+-----------------------------------------+
| cn                         | yes       | no           | RDN of keystore options entry.          |
+----------------------------+-----------+--------------+-----------------------------------------+
| keetoKeystoreOptionFrom    | no        | yes          | authorized_keys 'from' option entry.    |
|                            |           |              | See also: man sshd.                     |
+----------------------------+-----------+--------------+-----------------------------------------+
| keetoKeystoreOptionCommand | no        | yes          | authorized_keys 'command' option entry. |
|                            |           |              | See also: man sshd.                     |
+----------------------------+-----------+--------------+-----------------------------------------+
| description                | no        | no           | Keystore options description.           |
+----------------------------+-----------+--------------+-----------------------------------------+

