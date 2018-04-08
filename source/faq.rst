FAQ
===

How can I exclude accounts from being processed by Keeto?
---------------------------------------------------------

Keeto is associated with keyboard-interactive authentication via PAM.
Disabling keyboard-interactive authentication for specific users does
the trick. This can be configured in the sshd_config file of OpenSSH
through the Match directive as follows::

    Match User foo,bar
        AuthenticationMethods publickey
        AuthorizedKeysFile .ssh/authorized_keys

Not all certificates/CRL's in the certificate store are utilized
----------------------------------------------------------------

Make sure that every certificate/CRL has a corresponding symlink created
with the c_rehash utility. Beware that older version of c_rehash only
processed files with the extension .pem. If you are using such a version
consider symlinking or renaming the original file with the proper
extension.

Keeto doesn't seem to check the LDAP server certificate chain against the CRL
-----------------------------------------------------------------------------

If Keeto has been configured to check CRL's it depends on the crypto
library libldap has been linked against. As for now CRL checking is only
supported if libldap has been linked against OpenSSL. In any other case
the CRL check is skipped. Keeto logs the following entry if the CRL check
cannot be performed through libldap::

    [C] failed to set ldap option: key 'LDAP_OPT_X_TLS_CRLCHECK', value '2'

Note that this only applies to the verification of the LDAP server
certificate chain during secure connection establishment (StartTLS/LDAPS).
Validation of user certificates against the CRL will always be performed
if 'check_crl' has been set to '1' in the Keeto configuration file.

.. _faq-real-user:

What is the real user exported to the environment actually?
-----------------------------------------------------------

The environment variable $KEETOREALUSER contains the UID of the user who
owns the key that was used during public key authentication. This is
meaningful for scenarios where an account is shared across different
users and one would like to apply certain configuration based on the
real entity.

