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


Not all certificates / CRL's in the certificate store are utilized
------------------------------------------------------------------

Make sure that every certificate / CRL has a corresponding symlink
created with the c_rehash utility. Beware that older version of c_rehash
only processed files with the extension .pem. If you are using such a
version consider symlinking or renaming the original file with the proper
extension.

