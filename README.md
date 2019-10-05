# Discourse for YunoHost

[![Integration level](https://dash.yunohost.org/integration/discourse.svg)](https://ci-apps.yunohost.org/jenkins/job/discourse%20%28Community%29/lastBuild/consoleFull)
[![Install Discourse with YunoHost](https://install-app.yunohost.org/install-with-yunohost.png)](https://install-app.yunohost.org/?app=discourse)

> *This package allows you to install discourse quickly and simply on a YunoHost server.
If you don't have YunoHost, please see [here](https://yunohost.org/#/install) to know how to install and enjoy it.*

## Disclaimer

This package installs Discourse without Docker, for several reasons (mostly to support ARM architecture and low-profile servers, to mutualize nginx/postgresql/redis services and to simplify e-mail setup).
As stated by the Discourse team:
> The only officially supported installs of Discourse are [Docker](https://www.docker.io/) based. You must have SSH access to a 64-bit Linux server **with Docker support**. We regret that we cannot support any other methods of installation including cpanel, plesk, webmin, etc.

So please have this in mind when considering asking for Discourse support.

Moreover, you should have in mind Discourse [hardware requirements](https://github.com/discourse/discourse/blob/master/docs/INSTALL.md#hardware-requirements):
- modern single core CPU, dual core recommended
- 1 GB RAM minimum (with swap)
- 64 bit Linux compatible with Docker
- 10 GB disk space minimum

Finally, if installing on a low-end ARM device (e.g. Raspberry Pi):
- installation can last up to 3 hours,
- first access right after installation could take a couple of minutes.
## Overview
[Discourse](http://www.discourse.org) is the 100% open source discussion platform built for the next decade of the Internet. Use it as a:

- mailing list
- discussion forum
- long-form chat room

To learn more about the philosophy and goals of the project, [visit **discourse.org**](http://www.discourse.org).

**Shipped version:** 2.3.4

## Screenshots

<a href="https://bbs.boingboing.net"><img alt="Boing Boing" src="https://cloud.githubusercontent.com/assets/1385470/25397876/3fe6cdac-29c0-11e7-8a41-9d0c0279f5a3.png" width="720px"></a>
<a href="https://twittercommunity.com/"><img src="https://cloud.githubusercontent.com/assets/1385470/25397920/71b24e4c-29c0-11e7-8bcf-7a47b888412e.png" width="720px"></a>
<a href="http://discuss.howtogeek.com"><img src="https://cloud.githubusercontent.com/assets/1385470/25398049/f0995962-29c0-11e7-99d7-a3b9c4f0b357.png" width="720px"></a>
<a href="https://talk.turtlerockstudios.com/"><img src="https://cloud.githubusercontent.com/assets/1385470/25398115/2d560d96-29c1-11e7-9a96-b0134a4fedff.png" width="720px"></a>

<img src="https://www.discourse.org/a/img/about/mobile-devices-2x.jpg" alt="Mobile" width="414">

## Configuration

Use the admin panel of your discourse to configure this app.

### Configuring "Reply-By-Email"

* You should create a dedicated Yunohost user for Discourse whose mailbox will be used by the Discourse application. You can do this with `yunohost user create response`, for example. You should ensure that the email address is configured to be on your Discourse domain.

* You should then configure your Discourse `/var/www/discourse/config/discourse.conf` file with the correct SMTP configuration values. Please see [this comment](https://github.com/YunoHost-Apps/discourse_ynh/issues/2#issuecomment-409510325) for an explanation of what values to change. Please be aware, when you update the application, you will have to re-apply this configuration.

* You must enable the Pop3 configuration for Dovecot. See [this thread](https://forum.yunohost.org/t/how-to-enable-pop3-in-yunohost/1662/2) on how to do that. You can validate your configuration with `systemctl restart dovecot && dovecot -n`. Don't forget to open the ports you need (`995` is the default). You can validate that with `nmap -p 995 yunohostdomain.org`.

* You should then configure the Pop3 polling in the Discourse admin interface. Please see [this comment](https://meta.discourse.org/t/set-up-reply-via-email-support/14003) for how to do so. You will need to follow step 5 in that comment. You can specify your main Yunohost domain for the `pop3_polling_host`.

You should now be able to start testing. Try using the `/admin/email` "Send Test Email" and then view the "Sent" or "Skipped" etc. tabs. You should see a report on what happened with the email. You may also want to look in `/var/www/discourse/log/production.log` as well as `/var/www/mail.err`. You should perhaps also use [Rainloop](https://github.com/YunoHost-Apps/rainloop_ynh) or another Yunohost email client application to quickly test that both your user and the dedicated Yunohost Discourse user (`response@...`) is receiving mail.

## Documentation

 * Official documentation: https://www.discourse.org/
 * YunoHost documentation: There no other documentations, feel free to contribute.

## YunoHost specific features

 * Integration with YunoHost users and SSO:
   * LDAP integration: on the login pop-up, you can choose "Login with LDAP" and use your YunoHost credentials
   * private mode: Forum only accessible by YunoHost users
   * public mode: Visible by anyone

#### Multi-users support

Supported, with LDAP and SSO.

![Login Popup](https://raw.githubusercontent.com/jonmbake/screenshots/master/discourse-ldap-auth/login.png)

Default administrator and YunoHost users must login using LDAP:
* click on the "with LDAP" button
* use your YunoHost credentials

When disabling Local Login and other authentication services, clicking the `Login` or `Sign Up` button will directly bring up the LDAP Login popup.

![Disable Local](https://raw.githubusercontent.com/jonmbake/screenshots/master/discourse-ldap-auth/disable_local.png)

![LDAP Login Popup](https://raw.githubusercontent.com/jonmbake/screenshots/master/discourse-ldap-auth/ldap_popup.png)



#### Supported architectures

* Tested on x86_64
* Tested on ARM

## Limitations
### Known problems
* In the administration dashboard, The installed version is shown as `unknown` (due the fact that we don't use git for installation); you can safely ignore that as the YunoHost package will be maintained.
* On ARM devices, default generated avatars are missing the profile initials (they are only a plain discus)

### Known non-impacting log messages
```
fatal: Not a git repository (or any of the parent directories): .git

bash: BASH_XTRACEFD: 7: invalid value for trace file descriptor
```
## How-tos
### Install plugins
```
cd /var/www/discourse
sudo -i -u discourse RAILS_ENV=production bin/rake --trace plugin:install repo=https://github.com/discourse/discourse-solved (for example)
sudo -i -u discourse RAILS_ENV=production bin/rake --trace assets:precompile
systemctl restart discourse
```

## Links

 * Report a bug: https://github.com/YunoHost-Apps/discourse_ynh/issues
 * Discourse website: https://discourse.org/
 * YunoHost website: https://yunohost.org/

---

Developers info
---------------

Please do your pull request to the [testing branch](https://github.com/YunoHost-Apps/discourse_ynh/tree/testing).

To try the testing branch, please proceed like that.
```
sudo yunohost app install https://github.com/YunoHost-Apps/discourse_ynh/tree/testing --debug
or
sudo yunohost app upgrade discourse -u https://github.com/YunoHost-Apps/discourse_ynh/tree/testing --debug
```
