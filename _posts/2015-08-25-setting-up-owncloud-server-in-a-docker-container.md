---
layout: post
title: "Setting up an ownCloud Server in a Docker container"
description: ""
category: "Serverside"
tags: ['Docker', 'ownCloud', 'opensource']
---
{% include JB/setup %}

*TLDR*: this post explains how to setup a dockerized ownCloud server with a PostgreSQL database, persisting the data
across reboots or image upgrades.

I finally reinstalled my entire VPS, using the provisioning tool Ansible, 
and I wanted to have a more isolated install of [ownCloud](https://owncloud.org/). 
I decided to use a *Docker container* to run this application in a more *isolated* and *portable* way,
and to avoid installing this application directly on the server.

## So I used Docker

First question, what image to use? When typing `owncloud` on [DockerHub](https://hub.docker.com/search/?q=owncloud&page=1&isAutomated=0&isOfficial=0&starCount=0&pullCount=0),
I immediately see there is [a (recent) *official image*](https://hub.docker.com/_/owncloud/), simply named `owncloud`.

We can try to run it right now to check everything works fine:

    docker run --rm -it --name owncloud -p 80:80 owncloud

![Screenshot of the first run page](/assets/illustrations/docker-owncloud-1.png)

That was quick. Now, you can exit/stop this container.

### We will need a database

If you don't already have a database up and running you want to use,
 you can either install one on the host, or *spin up a second container* for it. 
Being an adept of PostgreSQL, let's use [the associate container](https://hub.docker.com/_/postgres/):

    docker run --name owncloud-postgres -e POSTGRES_PASSWORD=mysecretpassword -d postgres
    docker run --rm -it --link owncloud-postgres:owncloud-db --name owncloud -p 80:80 owncloud

Instead of a SQLite database, we use a *PostgreSQL* one, with user `postgres`,
 the password supplied when creating the container (here `mysecretpassword`),
 the database `postgres`, and the host `owncloud-db`.

You should be able to get a running instance by entering these settings in the startup page of ownCloud.
If it doesn't work you can see the debug log (created after the first request on owncloud), using:

    docker exec -it owncloud tail -f /var/www/html/data/owncloud.log

### Persisting the data across container destruction and upgrade

We will need to *store the ownCloud data and configuration folders outside of the container*, 
as upgrading the container would destroy everything. 

This can be achieved by using Docker Volumes, and even *[docker data volumes](https://docs.docker.com/userguide/dockervolumes/#creating-and-mounting-a-data-volume-container)*.
We will need to do the same for the *PostgreSQL container* (think "database backup").

At this point, copy-pasting the commands to run these containers begins to be unreadable,
 and painful. What we want is to describe how containers should be started, linked, and 
configured, so let's use [Docker Compose](https://docs.docker.com/compose/).

## Docker Compose

The concept is simple: *use a YAML file to describe how your containers should be... composed 
together*, and then let a `docker-compose up` start everything!

You can install it with `pip install docker-compose`.

Here is the `docker-compose.yml` file:

    # Composition of the containers needed

    owncloud:
      container_name: owncloud
      image: owncloud
      ports:
       - 80:80
      links:
       - postgres:owncloud-db
    
    postgres:
      image: postgres
      environment:
        - POSTGRES_PASSWORD=mysecretpassword

All you have to do now is `docker-compose up` to start all the containers.

![Screenshot of ownCloud configured and running](/assets/illustrations/docker-owncloud-running.png)

### Persist the data

Let's add some persistence by adding some *data volumes*: we want to create "data" containers for 
PostgreSQL (for the data itself) and ownCloud (configurations and files stored).

The folders we need to persist are:

* `/etc/postgresql` for the database configuration
* `/var/lib/postgresql` for the database content itself
* `/var/www/html/data` for owncloud's data
* `/var/www/html/config` for owncloud's configuration

This is achieved using the `docker-compose.yml` file I uploaded [in this Gist](https://gist.github.com/MickaelBergem/524f8fcb39a3ad565663), take a look at it.

To check all the data is persisted, you can star files (or remove/create files), and then remove the `postgres` and 
`owncloud` containers to check if the next `docker-comose up` does persist these modifications:

    docker rm -f test_owncloud_1 test_postgres_1 # Replace "test" by the name of your folder
    docker-compose up
    
Then go back in your browser:

![The changes are still here!](/assets/illustrations/docker-owncloud-starred.png)

Here you are! You have a fully functional ownCloud installation running locally!

### Upgrade?

Please note that the `owncloud-data_1` and `postgres-data_1` containers should never be deleted.
To upgrade the other non-data containers (postgres or owncloud) for example when a new image is available:
 
    docker pull owncloud
    docker pull postgres
    docker-compose -d

The `-d` flag is to *detach* from the containers so they can start in the background :)

## Last settings

To setup your own cloud, don't forget to change the `hostname` and `domainname` lines in the compose file.

You may also want to enable HTTPS, this will be for another blog post.
