---
layout: post
title: "Linux users: running docker without sudo is dangerous"
description: "Running docker on Linux equals having root privileges on the host. Learn why."
category: "ServerSide"
tags: ['linux','docker','security','DevOps']
image: /assets/illustrations/docker-logo-horizontal.png
---
{% include JB/setup %}

**TL;DR**: unless you are using a docker-machine (default on OSX and Windows), **running docker commands equals being root on the host**. If you added yourself in the docker group or changed the permission of the Docker socket, you (or a malicious program) **can become root without needing the password**.

As a technical mentor for startups, I often get to talk with CTOs. A few days ago, I discovered that one of them was using docker on his Linux laptop without having to use sudo. This is very dangerous and you should know the consequences.

![docker logo](/assets/illustrations/docker-logo-horizontal.png)

## Docker Engine

Docker is awesome. Replicating a MongoDB database locally, spinning up a Redis server or a Selenium node in seconds, versioning your app’s requirements, all this substantially speeds up our developer lives.

As we type `sudo docker run`, `sudo docker-compose logs`, etc. several times per day or per hour, we might be tempted to drop the “sudo” part to avoid typing a password every 20 minutes. The two major “classic” solutions are either:

* adding yourself in a “docker” group
* changing the permission on the docker socket

If this is your case or if you plan to use one of these technique (or any other, actually), you need to know something:

<big>Accessing the Docker Engine equals having root access on the host</big>

Why so? Let’s take a practical example:

```
$ docker run -v /:/hostOS -i -t ubuntu

root@7a064e899aa3:/# whoami
root
root@7a064e899aa3:/# cat /hostOS/etc/shadow
at:!:17339::::::
...
```

Yeah, I just read the content of a file only root can read, and which contains my (encrypted) password a few lines after. Obviously, I can also write to it to change the password, or create a new user with root privileges, etc.

## What happened

Thanks to the `-v` option, we “mounted” the host root folder `/` on the `/hostOS/` folder inside the container.

Running any container image with the root user (default for the majority of images) gives us root privileges inside the container, thus… root privileges over all the files on the host. Now, you should start to understand that it allows you to read (and write!) any file, including the list of system passwords (`/etc/shadow`), any file containing credentials, and finally executing any command.

One could even create a root-owned program with the setuid bit set, allowing anybody executing it to execute it as root. Such a program could be used without Docker, by virtually anybody on the system, and even after the docker permission issue is fixed.

On OSX and Windows, the Docker Engine cannot typically directly execute on the host, so you have to setup a Docker Machine, which is basically a Virtual Machine running a derivative of Linux, and in which the Docker Engine is executed. On these systems, you gain root privileges on the VM itself and not directly on the host. There are still shared volumes with the actual host, and VM evasion vulnerabilities, though.

## Understanding the risk

> Ok, but I am the only user on my machine, so why bother?

There is a large number of reasons why you don’t run everything as root on your machine, and two are particularly important:

* users execute untrusted code (ever copy-pasted a `curl https://.../script.sh | sh` command?)
* software you execute have vulnerabilities (something as widespread as the Atom editor recently let almost everybody execute anything on your computer: see https://statuscode.ch/2017/11/from-markdown-to-rce-in-atom/) - this is more frequent than you think!

On **servers**, there are typically multiple users (or there should be): one per developer or team, one for the continuous deployment, etc. This is even more dangerous, since servers usually take care of sensitive data (passwords, personal information, more credentials, etc).

## What to do now?

Now that you know what removing the `sudo` prefix actually does, you have two solutions:

* always use `sudo`: this ensures you are allowed to become root otherwise, and reminds you of the root powers you are gaining
* accept the risk associated to opening a **privilege escalation vulnerability** on your system, and keep in mind that anybody or any (flawed or malicious) program using the docker command will gain root-like privileges

I obviously recommend the first solution ;)

<hr/>

Found this article useful? Share it with your colleagues or friends!

Have a question? Ask it here or tweet me at [@msuixo](https://twitter.com/msuixo).

See also [https://docs.docker.com/engine/security/security/#docker-daemon-attack-surface](https://docs.docker.com/engine/security/security/#docker-daemon-attack-surface)
