+++
title = "Easily use Docker and phpMyAdmin to browse a remote database"
date = "2021-01-29T14:10:48-06:00"
author = "James K. Pringle"
authorTwitter = "" #do not include @
cover = ""
tags = ["docker", "phpmyadmin", "databases"]
keywords = ["", ""]
description = ""
showFullContent = false
+++

If you need to browse a MySQL database, then [phpMyAdmin][phpmyadmin-home] is an intuitive and battle-tested piece of software to do that. 

[phpmyadmin-home]: https://www.phpmyadmin.net/

{{< image src="/static/img/2021/phpmyadmin-home.png" alt="Home page for phpMyAdmin" position="center" style="border-radius: 8px;" >}}

If you download it, however, all you get is a zip of PHP files and other paraphernelia. You could configure a PHP runtime through a local instance of Apache or nginx, but Docker provides an easier answer.

Head on over to Docker hub to find an official Docker image [landing page for phpMyAdmin][docker-hub].

[docker-hub]: https://hub.docker.com/_/phpmyadmin?tab=description

Download the image with:

```bash
docker pull phpmyadmin
```

The next step is to get the connection details for your remote MySQL database. In my case, I needed to connect with a database managed by Pantheon in a Drupal deploy. The following screenshot shows how to get the connection details (credit to [Travis Tomka][travis] for creating this!).

[travis]: https://atendesigngroup.com/about/travis-tomka

{{< image src="/static/img/2021/pantheon-database-steps.png" alt="Pantheon.io dashboard with database details" position="center" style="border-radius: 8px;" >}}

I need to copy into a text editor the host, username, password, and port. No need to take the database name since phpMyAdmin will discover it after connecting.

Next, use that information to fill in the following command for Docker:

```bash
docker run --name MY_ADMIN -d --rm -p 8080:80 \
-e PMA_HOST=MY_HOST \
-e PMA_USER=MY_USER \
-e PMA_PASSWORD=MY_PASSWORD \
-e PMA_PORT=MY_PORT \
phpmyadmin
```

Some notes:
- **--name** sets the container name to `MY_ADMIN`
- **-d** to detach the process so that it runs in the background
- **--rm** so that the container is removed when it is stopped
- **-p 8080:80** connects your local machine on port 8080 to what the container is running on port 80.
- **-e** sets environment variables for the container. These items—`PMA_HOST`, `PMA_USER`, `PMA_PASSWORD`, and `PMA_PORT`—are included so phpMyAdmin knows how to connect to the remote database. Set the values appropriately (e.g. from the Pantheon dashboard).
- This can be run on a single line (just remove all backslashes `\`).

Once this is entered into the terminal, a container starts to run. Connect to the container by opening a browser and going to `localhost:8080` et voilà!

{{< image src="/static/img/2021/phpmyadmin-db.png" alt="Pantheon.io dashboard with database details" position="center" style="border-radius: 8px;" >}}