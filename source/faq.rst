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


