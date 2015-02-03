# Errors you may see while maintaining iRedMail server

[TOC]

## Postfix
### Sender address rejected: not owned by user user@domain.ltd

This error is caused by restriction rule `reject_sender_login_mismatch` in
Postfix parameter `smtpd_recipient_restrictions`, in file `/etc/postfix/main.cf`:

```
smtpd_recipient_restrictions =
    ...
    reject_sender_login_mismatch,
    ...
```

It will reject the request when $smtpd_sender_login_maps specifies an owner
for the MAIL FROM address, but the client is not (SASL) logged in as that MAIL
FROM address owner; or when the client is (SASL) logged in, but the client
login name doesn't own the MAIL FROM address according to $smtpd_sender_login_maps.
Check [manual page of Postfix configuration file](http://www.postfix.org/postconf.5.html#reject_sender_login_mismatch) for more details.

Removing `reject_sender_login_mismatch` and restarting Postfix service fixes
this issue.

> If you want to allow some users to send as other users, or allow all users
> to send as their alias addresses, or allow member of mail list/alias to send
> as mail list/alias, you should try iRedAPD plugin `reject_sender_login_mismatch`
> instead (requires iRedAPD-1.4.4 or later releases).
> 
> Read comments in file `/opt/iredapd/plugins/reject_sender_login_mismatch.py`,
> then enable it in iRedAPD config file `/opt/iredapd/settings.py` (`plugins = `),
> restart iRedAPD service. That's all.


## Amavisd

### connect to 127.0.0.1[127.0.0.1]:10024: Connection refused

This error means Amavisd service is not running, please try to start it first.

* RHEL/CentOS/FreeBSD/OpenBSD: ```# service amavisd restart```
* Debian/Ubuntu: ```# service amavis restart```

After restarted amavisd service, please check its
[log file](./file.locations.html#amavisd) to make sure it's running.

Notes:

* At least 1GB memory is required for a low traffic mail server. If your
  server doesn't have enough memory, Amavisd may stop running automatically after
  running for a while. If it's just a testing server, you can follow
  [our tutorial](./completely.disable.amavisd.clamav.spamassassin.html)
  to disable some features of Amavisd to keep it running, or disable it completely.