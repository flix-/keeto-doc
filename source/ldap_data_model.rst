.. _ldap-data-model:

LDAP Data Model
===============

Keeto obtains all access permissions and the key material from a
central Directory Service. This section describes syntax and semantic
of the entities involved and how they relate to each other. Certain
attributes can be configured in the Keeto configuration file. Those
attributes are of the form <x> where x is referring to the key in the
Keeto configuration file.

SSH Server
----------

The SSH server entry is the starting point for the determination of
access permissions and key material. It specifies the relevant access
profiles that shall be taken into account. Keeto determines the right
SSH server entry through a unique identifier that is specified in the
Keeto configuration and must match the identifier in the SSH server
entry in the Directory Service. ::

  objectClass: top
  objectClass: keetoSSHServer

+--------------------+-----------+--------------+----------------------------------+
| Attribute          | Mandatory | Single-Value | Description                      |
+====================+===========+==============+==================================+
| cn                 | yes       | no           | RDN of SSH server entry.         |
+--------------------+-----------+--------------+----------------------------------+
| uid                | yes       | no           | Unique identifier of SSH server. |
|                    |           |              |                                  |
|                    |           |              | See also: <ssh_server_uid>.      |
+--------------------+-----------+--------------+----------------------------------+
| keetoAccessProfile | yes       | no           | DN to Keeto access profile.      |
+--------------------+-----------+--------------+----------------------------------+
| description        | no        | no           | SSH server description.          |
+--------------------+-----------+--------------+----------------------------------+

Access Profiles
---------------

Keeto supports two different access profiles. Direct access profiles
provide access to one's own account whereas access on behalf profiles
provide access to someone else's account.

Direct Access Profile
^^^^^^^^^^^^^^^^^^^^^

A direct access profile specifies a reference to a group of key
providers that shall be able to login with it's own account. Each key
provider's UID is checked against the UID of the user about to login.
If they match the X.509 certificates of the key provider will be taken
into account. Optionally keystore options can be specified that are
used for all key providers. A direct access profile can be enabled /
disabled. ::

  objectClass: top
  objectClass: keetoAccessProfile
  objectClass: keetoDirectAccessProfile

+----------------------+-----------+--------------+-------------------------------------+
| Attribute            | Mandatory | Single-Value | Description                         |
+======================+===========+==============+=====================================+
| cn                   | yes       | no           | RDN of direct access profile entry. |
+----------------------+-----------+--------------+-------------------------------------+
| keetoEnabled         | yes       | yes          | Enable / Disable profile.           |
+----------------------+-----------+--------------+-------------------------------------+
| keetoKeyProvider     | yes       | yes          | DN to Keeto key provider group.     |
+----------------------+-----------+--------------+-------------------------------------+
| keetoKeystoreOptions | no        | yes          | DN to Keeto keystore options.       |
+----------------------+-----------+--------------+-------------------------------------+
| description          | no        | no           | Direct access profile description.  |
+----------------------+-----------+--------------+-------------------------------------+

Access On Behalf Profile
^^^^^^^^^^^^^^^^^^^^^^^^

Access on behalf profiles enable authentication on behalf of someone
else. For that the UID of the user about to login is searched in the
target keystore's. On match all valid keys of all key providers are
synced into that target keystore. As with direct access profiles keystore
options can be specified optionally and the profile can be enabled /
disabled. ::

  objectClass: top
  objectClass: keetoAccessProfile
  objectClass: keetoAccessOnBehalfProfile

+----------------------+-----------+--------------+----------------------------------------+
| Attribute            | Mandatory | Single-Value | Description                            |
+======================+===========+==============+========================================+
| cn                   | yes       | no           | RDN of access on behalf profile entry. |
+----------------------+-----------+--------------+----------------------------------------+
| keetoEnabled         | yes       | yes          | Enable / Disable profile.              |
+----------------------+-----------+--------------+----------------------------------------+
| keetoKeyProvider     | yes       | yes          | DN to Keeto key provider group.        |
+----------------------+-----------+--------------+----------------------------------------+
| keetoTargetKeystore  | yes       | yes          | DN to Keeto target keystore group.     |
+----------------------+-----------+--------------+----------------------------------------+
| keetoKeystoreOptions | no        | yes          | DN to Keeto keystore options.          |
+----------------------+-----------+--------------+----------------------------------------+
| description          | no        | no           | Access on behalf profile description.  |
+----------------------+-----------+--------------+----------------------------------------+

Key Provider
------------

A key provider is an entry that provides the key material through
X.509 certificate(s). All relevant attributes are specified in the Keeto
configuration file to adjust it to different deployments. Key providers
are relevant for both direct access profiles and access on behalf profiles.

+-------------------------------+-----------+--------------+----------------------------+
| Attribute                     | Mandatory | Single-Value | Description                |
+===============================+===========+==============+============================+
| <ldap_key_provider_uid_attr>  | yes       | no           | UID of key provider.       |
+-------------------------------+-----------+--------------+----------------------------+
| <ldap_key_provider_cert_attr> | yes       | no           | X.509 certificate of user. |
+-------------------------------+-----------+--------------+----------------------------+

Target Keystore
---------------

A target keystore is only relevant for access on behalf profiles. It
specifies the accounts that can be used on behalf of the key providers.

+---------------------------------+-----------+--------------+-------------------------+
| Attribute                       | Mandatory | Single-Value | Description             |
+=================================+===========+==============+=========================+
| <ldap_target_keystore_uid_attr> | yes       | no           | UID of target keystore. |
+---------------------------------+-----------+--------------+-------------------------+

Groups
------

Both key providers and target keystores are not linked directly to an
access profile but rather through a group. For both cases the group
member attribute is obtained from the Keeto configuration file.

+------------------------------------------+-----------+--------------+----------------------------------------+
| Attribute                                | Mandatory | Single-Value | Description                            |
+==========================================+===========+==============+========================================+
| <ldap_key_provider_group_member_attr> /  | yes       | no           | DN to key provider / target keystore.  |
|                                          |           |              |                                        |
| <ldap_target_keystore_group_member_attr> |           |              |                                        |
+------------------------------------------+-----------+--------------+----------------------------------------+


Keystore Options
----------------

Keystore options can be optionally specified and linked to access
profiles to restrict access with regard to the location a user is
connecting from and the space of commands he is allowed to execute. ::

  objectClass: top
  objectClass: keetoKeystoreOptions

+----------------------------+-----------+--------------+-----------------------------------------+
| Attribute                  | Mandatory | Single-Value | Description                             |
+============================+===========+==============+=========================================+
| cn                         | yes       | no           | RDN of keystore options entry.          |
+----------------------------+-----------+--------------+-----------------------------------------+
| keetoKeystoreOptionFrom    | no        | yes          | authorized_keys 'from' option entry.    |
|                            |           |              |                                         |
|                            |           |              | See also: man sshd.                     |
+----------------------------+-----------+--------------+-----------------------------------------+
| keetoKeystoreOptionCommand | no        | yes          | authorized_keys 'command' option entry. |
|                            |           |              |                                         |
|                            |           |              | See also: man sshd.                     |
+----------------------------+-----------+--------------+-----------------------------------------+
| description                | no        | no           | Keystore options description.           |
+----------------------------+-----------+--------------+-----------------------------------------+

