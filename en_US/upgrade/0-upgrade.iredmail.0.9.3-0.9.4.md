# Upgrade iRedMail from 0.9.3 to 0.9.4

[TOC]

__WARNING: THIS IS STILL A DRAFT DOCUMENT, PLEASE DO NOT APPLY IT.__

## ChangeLog

> We offer remote upgrade service, check [the price](../support.html) and [contact us](../contact.html).

## General (All backends should apply these steps)

### Update `/etc/iredmail-release` with new iRedMail version number

iRedMail stores the release version in `/etc/iredmail-release` after
installation, it's recommended to update this file after you upgraded iRedMail,
so that you can know which version of iRedMail you're running. For example:

```
# File: /etc/iredmail-release

0.9.4
```

### Upgrade iRedAPD (Postfix policy server) to the latest 1.7.1

Please follow below tutorial to upgrade iRedAPD to the latest stable release:
[Upgrade iRedAPD to the latest stable release](./upgrade.iredapd.html)

Detailed release notes are available [here](./iredapd.releases.html).

### Upgrade iRedAdmin (open source edition) to the latest stable release

Please follow this tutorial to upgrade iRedAdmin open source edition to the
latest stable release:
[Upgrade iRedAdmin to the latest stable release](./migrate.or.upgrade.iredadmin.html)

### Upgrade Roundcube webmail to the latest stable release

Please follow Roundcube official tutorial to upgrade Roundcube webmail to the
latest stable release immediately: [How to upgrade Roundcube](http://trac.roundcube.net/wiki/Howto_Upgrade)

Note: package `rsync` must be installed on your server before upgrading.

### Fixed: No daily cron job to backup SQL/LDAP database

In iRedMail-0.9.3, there's no daily cron job to backup SQL/LDAP databases.
Please add them manually with command `crontab -e -u root`.

> Notes:
>
> * Please make sure the path to backup scripts
>   (`/var/vmail/backup/backup_XXX.sh`) are correct.
>
> * On FreeBSD and OpenBSD, the path of `bash` shell is `/usr/local/bin/bash`.

* For OpenLDAP backend, you need 2 daily cron jobs, one for SQL database, one
  another one for LDAP:

```
# iRedMail: Backup OpenLDAP data (at 03:00 AM)
0   3   *   *   *   /bin/bash ${BACKUP_SCRIPT_OPENLDAP}

# iRedMail: Backup MySQL databases (at 03:30AM)
30   3   *   *   *   /bin/bash /var/vmail/backup/backup_pgsql.sh
```

* For MySQL/MariaDB backends, you need 1 daily cron job:

```
# iRedMail: Backup MySQL databases (at 03:30AM)
30   3   *   *   *   /bin/bash /var/vmail/backup/backup_pgsql.sh
```

* For PostgreSQL backend, you need 1 daily cron job:
```
# iRedMail: Backup PostgreSQL databases (at 03:01AM)
1   3   *   *   *   /bin/bash /var/vmail/backup/backup_pgsql.sh
```

### FreeBSD: Fix incorrect file permission of /etc/mail/mailer.conf

> This is applicable to only FreeBSD.

iRedMail generates /etc/mail/mailer.conf with permission 0600 during
installation, but it should be world-readable. Please fix this issue with
command below:

```
chmod +r /etc/mail/mailer.conf
```