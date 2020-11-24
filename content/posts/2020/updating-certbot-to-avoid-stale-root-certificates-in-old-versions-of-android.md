+++
title = "Updating certbot to avoid stale root certificates in old versions of Android"
date = "2020-11-22T16:19:04-06:00"
author = ""
authorTwitter = "" #do not include @
cover = ""
tags = ["android", "tls", "certbot", "lets-encrypt"]
keywords = ["", ""]
description = ""
showFullContent = false
+++

# The problem

The first step in an HTTPS connection between a client (think web browser) and a server is to negotiate what kind of
encryption will be used and to initialize the parameters for that encryption.  Once that is complete, the server and
client can communicate using the HTTP protocol.

[Let's Encrypt][0] is a developer-favorite service helping websites obtain TLS certificates, required for HTTPS, for
free.  As described in a [recent post from the team at Let's Encrypt][1], Let's Encrypt will no longer be cross signing
with IdenTrust and no longer using their root certificate "DST Root X3" in 2021. This is significant because Let's
Encrypt will be "standing on their own two feet" as they put it, relying on their own root certificate "ISRG Root X1".
Since this is a newer certificate, some operating systems&mdash;notably Android versions prior to 7.1.1&mdash;do not
have it.

The problem is that these older computers will not be able to connect to various websites and apps that are secured
using Let's Encrypt. Luckily there is a way to keep cross-signing with IdenTrust, the more establish certificate
authority, for a few more months. In order to do that, we need to have at least version `1.6.0` of `certbot`.

For work, we have configured our Ubuntu 18.04 server to use a Let's Encrypt certificate. We also have some phones with
Android version 6.0.0 that need to connect to it. This is a problem that will affect us.

[0]: https://letsencrypt.org/
[1]: https://letsencrypt.org/2020/11/06/own-two-feet.html

# Gathering data

Let's Encrypt certificates are managed by a command line tool called `certbot`. The code that was run at the very
beginning for setting up `certbot` was:

```
$ add-apt-repository -y ppa:certbot/certbot
$ apt-get -y update
$ apt-get -y install python-certbot-nginx
$ (crontab -l 2>/dev/null; echo "0 0 1 * * /usr/bin/certbot renew > /var/log/letsencrypt/letsencrypt.log") | crontab -
```

When logged into our server, I can run

```
$ certbot certificates
```

and see when certificates are set to expire.

As I have learned, there are several places that we can get packages from. Ubuntu approves and adds packages to their
own repositories.  There are also PPAs (Personal Package Archives). That is what was used here. PPAs are typically used
for previews of upcoming software.  Finally, there are snaps. This seems like a new area of expansion. The benefit to
snaps are that releases are controlled by the package maintainers, not when Ubuntu decides to update. Also, one snap
works on multiple platforms as opposed to traditional packages that need to be built for each version of an operating
system. Finally, snaps come with their dependencies pre-bundled. Read more at [Ubuntu's website on packages][2].

Perhaps `certbot` was originally meant to be installed through a PPA. But now, the recommended installation is as a
snap.

In any case, with the way our machines were configured before, running

```
$ apt update
$ apt upgrade
```

did not update `certbot`. Before and after running the update and upgrade commands, I would get this:

```
$ certbot --version
certbot 0.31.0
```

That led me to search for steps to upgrade `certbot` and I found the guide linked in that very same post about Let's
Encrypt standing on its own.

I followed the instructions at [https://certbot.eff.org/lets-encrypt/ubuntubionic-nginx][3]. This method installs
`certbot` as a snap using the snap package repository. Luckily, my Ubuntu came with snap already installed!

First, uninstall `certbot`:

```
$ apt-get remove certbot
```

That also removed `python-certbot-nginx` and `python3-certbot-nginx`, so I made a note to check this at the end of
installation.

Second, install `certbot` with snap

```
$ snap install --classic certbot
```

Third, make a link so that we can run `certbot` from the old location

```
$ ln -s /snap/bin/certbot /usr/bin/certbot
```

Finally, check the version and do a dry run of a renewal:

```
$ certbot --version           # Result: certbot 1.9.0
$ certbot renew --dry-run
```

And from the output of the dry run:

```
...
Cert not due for renewal, but simulating renewal for dry run
Plugins selected: Authenticator nginx, Installer nginx
...
```

it appears that this new `certbot` knows about the connection to `nginx`.

Now that `certbot` is updated, it is able to obtain the desired certificate continuing from January 2021. The last step
is to add a line to `/etc/letsencrypt/cli.ini` so that we activate that functionality. So I added

```
preferred-chain = DST Root CA X3
```

to that file.

Now, only time will tell if our TLS certificates are updated correctly. I hope this is all we needed to do!



[2]: https://ubuntu.com/about/packages
[3]: https://certbot.eff.org/lets-encrypt/ubuntubionic-nginx
