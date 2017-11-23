---
layout: post
title: "Versioned DNS records with Gandi's LiveDNS"
description: "I wrote a small tool to version your zone files with git or any other version control system, using Gandi's LiveDNS API."
category: "ServerSide"
tags: [Infrastructure As Code, tool, python, script, DNS, DevOps]
---
{% include JB/setup %}

**TL;DR:** I wrote a tool to manage DNS records through any GIT repository, hence versioning the zone
records. It uses Gandi's LiveDNS API.

When updating my DNS records, I need to use the web interface of my registrar (Gandi). The zone records are
versioned, but I can't easily display diffs, let users propose changes through Pull Requests, or
comment on potential implications of changes before applying the change. The best source version control
available today is **git**, so why not using it for DNS zones?

Being a supporter of **Infrastructure-as-Code**, I wrote a small
Python tool to *manage my DNS records from a versioned (GIT) repository*. You need to be a user of Gandi's LiveDNS, 
or you will have to adapt the tool to your registrar's API.

## Configuration

1. Fork the main repository: [https://github.com/MickaelBergem/gandi-livedns-zone-manager](https://github.com/MickaelBergem/gandi-livedns-zone-manager)
2. Clone it locally
3. Retrieve your LiveDNS API key: go to [your Gandi account](https://account.gandi.net/), under "Security", "Generate your API key".
4. Put your key in a `api_key.txt` file in the repository
5. Test your configuration by running `./livedns.py view`

This should output your current zones:

```bash
$ ./livedns.py view
== Zone test-zone [580549ec-c25f-11e7-9d8f-00163e6dc886] ==
        No domain associated with this zone.
        1 records in this zone:

        CNAME   10800   test                access.mail.gandi.net.
```

## Usage

```bash
# Pull the zones and write them on disk
$ ./livedns.py pull
== Zone test-zone [580549ec-c25f-11e7-9d8f-00163e6dc886] ==
        Writing... done (test-zone_580549ec-c25f-11e7-9d8f-00163e6dc886.txt)
$ ls -l zones
-rw-r--r-- 1 suixo users  133 24 nov.  00:06 test-zone_580549ec-c25f-11e7-9d8f-00163e6dc886.txt
# Now make some changes in your zone files
$ vim zones/test-zone_580549ec-c25f-11e7-9d8f-00163e6dc886.txt
# And push the changes to your registrar 
$ ./livedns.py push
Found zone test-zone (580549ec-c25f-11e7-9d8f-00163e6dc886)
        Uploading the zone... ok
        Server answered: DNS Zone Record Created
# The changes are immediately effective
$ dig CNAME test.test-domain.org
[...]
;; ANSWER SECTION:
test.test-domain.org. 10800    IN      CNAME   access.mail.gandi.net.
[...]
```

# Benefits

* you can manage the zone files exactly like usual source files, with your favorite Version Control System such as git
* if you are using GitHub or a similar platform, your collaborators can submit Pull Requests to change the zone file
* changes are effective immediately

I hope this little tool will help you, do not hesitate to share suggestions or to report bugs on [the project page](https://github.com/MickaelBergem/gandi-livedns-zone-manager) :)
