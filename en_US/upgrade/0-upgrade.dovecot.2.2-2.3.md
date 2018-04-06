# Upgrade Dovecot from 2.2.x to 2.3.x

Dovecot 2.3 breaks some backward compatible, and here's a short tutorial to
convert your Dovecot 2.2 config file to fully work with Dovecot 2.3.

For more details, please read Dovecot wiki page: [Upgrading Dovecot v2.2 to v2.3](https://wiki2.dovecot.org/Upgrading/2.3).

!!! attention

    * Currently only FreeBSD offers Dovecot 2.3 by the ports tree, other Linux
      or OpenBSD distributions still offers Dovecot 2.2, you should stick to
      Dovecot 2.2 if your Linux/BSD vendor doesn't offer 2.3 yet.
    * Dovecot 2.3 uses TLSv1 as minimal SSL protocol, if you prefer TLSv1.1 or
      TLSv1.2, please set the protocol version in parameter `ssl_min_protocol`
      like below:

      ```
      ssl_min_protocol = TLSv1.2
      ```

## Upgrade Dovecot on Linux/FreeBSD/OpenBSD

Open a shell terminal, and switch to Dovecot configuration directory first:

* On Linux/OpenBSD:
```
cd /etc/dovecot/
```

* On FreeBSD:
    * Please upgrade ports `mail/dovecot` and `mail/dovecot-pigeonhole` first.
      You can use tool like `portmaster` or `portupgrade` for this purpose.
      FYI: [Using the Ports Collection](https://www.freebsd.org/doc/handbook/ports-using.html)
    * After upgraded both ports, please run:

```
cd /usr/local/etc/dovecot/
```

Run commands below as root user, these commands will:

* Comment out parameter `ssl_protocols`
* Remove parameter `postmaster_address`
* Rename plugin names and parameters:
    * `stats` -> `old_stats`
    * `imap_status` -> `imap_old_stats`
    * `stats_refresh` -> `old_stats_refresh`
    * `service stats {}` -> `service old-stats {}`
    * `fifo_listener stats-mail {}` -> `fifo_listener old-stats-mail {}`
    * `stats_track_cmds` -> `old_stats_track_cmds`

```
perl -pi -e 's/^ssl_protocols/#${1}/g' dovecot.conf
perl -pi -e 's#(postmaster_address.*)##g' dovecot.conf
perl -pi -e 's#^(mail_plugins.*) stats(.*)#${1} old_stats${2}#g' dovecot.conf
perl -pi -e 's#imap_stats#imap_old_stats#g' dovecot.conf
perl -pi -e 's#service stats#service old-stats#g' dovecot.conf
perl -pi -e 's#fifo_listener stats-mail#fifo_listener old-stats-mail#g' dovecot.conf
perl -pi -e 's#stats_refresh#old_stats_refresh#g' dovecot.conf
perl -pi -e 's#stats_track_cmds#old_stats_track_cmds#g' dovecot.conf
```

* On RHEL/CentOS, please add new setting in `dovecot.conf`:

```
ssl_dh = </etc/pki/tls/dh2048_param.pem
```

* On Debian/Ubuntu/OpenBSD/FreeBSD, please add new setting in `dovecot.conf`:

```
ssl_dh = </etc/ssl/dh2048_param.pem
```