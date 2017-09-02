---
layout: post
title: "DNS CAA - A (not so) new mechanism for a better PKI"
description: "DNS CAA is a DNS record allowing domain owners to restrict which CA
are allowed to issue certificates for their domain. While it is a useful
additional security feature, it does not protect against unwilling CAs nor
mis-issued certificate being used in the wild. CA must implement it before
September 2017."
category: "ServerSide"
image: /assets/illustrations/google-testssl.png
tags: [security, TLS, PKI, DNS]
---
{% include JB/setup %}

**TLDR**: DNS CAA is a DNS record allowing domain owners to restrict which CA
are allowed to issue certificates for their domain. While it is a useful
additional security feature, it does not protect against unwilling CAs nor
mis-issued certificate being used in the wild. CA must implement it before
September 2017.

A few days ago, I discovered **DNS CAA** (which stands for *DNS Certification
Authority Authorization*) and wanted to get it a try. Here is a short write-up
about what I learned, and the steps if you want to protect your domain with DNS
CAA too.

While quite old (2013), this standard will [become mandatory in
September](https://blog.qualys.com/ssllabs/2017/03/13/caa-mandated-by-cabrowser-forum)
and all CA will have to implement it or face sanctions.


## A new DNS record

DNS CAA is actually a new DNS record (the "DNS CAA Resource Record" or DNS CAA
RR for the folks who love acronyms). [RFC
6844](https://tools.ietf.org/html/rfc6844) explains it role:

> The Certification Authority Authorization (CAA) DNS Resource Record
   allows a DNS domain name holder to specify one or more Certification
   Authorities (CAs) authorized to issue certificates for that domain.
   CAA Resource Records allow a public Certification Authority to
   implement additional controls to reduce the risk of unintended
   certificate mis-issue.

Okay, it allows us - domain owners - to specify **what CAs we are allowing to deliver certificates for our domain**.

Until now, any CA can deliver a certificate for any domain name. It is a known
weakness of the traditional web PKI, and we have seen many examples of CA
abusing their power to deliver counterfeit certificates ([the most recent one
being
Symantec](https://arstechnica.com/security/2017/03/google-takes-symantec-to-the-woodshed-for-mis-issuing-30000-https-certs/),
but also
[Trustwave](http://www.h-online.com/security/news/item/Trustwave-issued-a-man-in-the-middle-certificate-1429982.html),
among others). [Some even didn't issued these certificates on
purpose](https://nakedsecurity.sophos.com/2013/01/08/the-turktrust-ssl-certificate-fiasco-what-happened-and-what-happens-next/)
(sic).

Please note that some CAs mis-issuing certificates had actually been
compromised: Comodo and
[Diginotar](https://arstechnica.com/security/2011/03/independent-iranian-hacker-claims-responsibility-for-comodo-hack/),
[Verisign](https://www.cert.org/historical/advisories/CA-2001-04.cfm)... The new
DNS CAA Resource Record aims at preventing the first class of these issues:
non-compromised CA delivering certificates to the wrong person. DNS CAA will
have no effect if the CA is controlled by a malicious actor.

## How it works

Aside note: did you know the first checks restricting a CA's ability to deliver certificates
were written after... the French National CyberSecurity Agency (ANSSI) kind of
([the whole story is bit more
complicated](https://arstechnica.com/security/2013/12/french-agency-caught-minting-ssl-certificates-impersonating-google/))
delivered a certificate for "google.com". Of course, it wasn't officially
allowed to do it. So now, if you search for "ANSSI" in Mozilla's source code,
you will find [this
diff](https://hg.mozilla.org/mozilla-central/rev/218997c808a1#l1.34) explicitely
stating that any certificate delivered by the ANSSI is only valid for `*.fr`
domains, or a couple of other French-related TLDs. I find it pretty amazing to
have a hardcoded constraint in a browser just for my country's agency.

Unfortunately, this kind of event will still be possible with DNS CAA. More on
this in the next parts, but first, **what is this brand new DNS record?**

### The DNS CA RR

A DNS CA RR is composed of:

* some flags (currently just a "critical" flag if 128, otherwise value is 0)
* a tag name (`issue`, `issuewild`, `iodef`)
* a tag value

Thus, the following record states that *only Lets'Encrypt is allowed to deliver certificates for `example.com`*:

    example.com.  CAA 0 issue "letsencrypt.org"

It is possible to allow multiple CAs for a same domain:

    example.com.  CAA 0 issue "comodoca.com"
    example.com.  CAA 0 issue "letsencrypt.org"

`issuewild` says the CA is allowed to issue wildcard certs:

    example.com.  CAA 0 issue "letsencrypt.org"
    example.com.  CAA 0 issuewild "comodoca.com"

`iodef` is used to get notified of policy violations:

    example.com.  CAA 0 iodef "mailto:example@example.com"

For now, I don't know of any CA supporting this directive, but it is a good practice to use one so that you are prepared.

### DNS CAA only affects the certificate generation process

From the RFC:

> Before issuing a certificate, a compliant CA MUST check for
   publication of a relevant CAA Resource Record set.  If such a record
   set exists, a CA MUST NOT issue a certificate unless the CA
   determines that either (1) the certificate request is consistent with
   the applicable CAA Resource Record set or (2) an exception specified
   in the relevant Certificate Policy or Certification Practices
   Statement applies.

So the RR is **only checked at certificate issuance time**, but **not at "runtime"** in the browser, when a TLS connection is established.

This is important: DNS CAA will not protect you from the malicious CAs issuing
certificates for your domain (not like the NSA actually needed it). If you are
looking for this kind of protection, look at
[DANE](https://en.wikipedia.org/wiki/DNS-based_Authentication_of_Named_Entities)
(binding a x509 certificate to the DNS records) and
[HPKP](https://en.wikipedia.org/wiki/HTTP_Public_Key_Pinning) (HTTP Public Key
Pinning, basicaly the same idea but through a HTTP header).

## Do it yourself

Unlike HPKP that can be pretty tragic if misconfigured, you can safely reproduce
the following DNS CAA steps at home. The only inconvenience you may experience is an
administrative one while trying to renew your certificates, and updating your
DNS records will get you safe while not affecting your uptime or your visitors.

### Read and check DNS CAA records

Before adding our own records, let's check out how Google is using the CAA RR:

```bash
$ dig +nocomments type257 google.com
; <<>> DiG 9.10.4-P5 <<>> +nocomments type257 google.com
;; global options: +cmd
;google.com.                    IN      CAA
google.com.             86399   IN      CAA     0 issue "pki.goog"
google.com.             86399   IN      CAA     0 issue "symantec.com"
```

`dig` does not (yet) support this `CAA` RR, so we have to use `type257` instead.
Here we learn that only `pki.goog` and Symantec are allowed to deliver
certificates for `google.com` (and no one is allowed to deliver wildcard certs).

Fun fact: Google seems to be still experimenting with this new standard as domain names like "google.fr" are not (yet?) returning CAA records.

### Add your own records

Now, let's add you own records. Let's say you are using **Let's Encrypt** and
you want to protect your domain **blog.securem.eu**. Only Let's Encrypt should be
allowed to deliver certificates for this domain. You also want to be notified if
there is a **policy violation** (if someone tries to register blog.securem.eu on
GoDaddy for instance), at your email elon.musk@gmail.com (lucky you!). Here are the records
you need to add to your DNS zone file:

```dns
blog.securem.eu.	IN	CAA	0 issue "letsencrypt.org"
blog.securem.eu.	IN	CAA	0 iodef "mailto:elon.musk@gmail.com"
```

Beware: not all registrars currently support CAA records, you may try
[https://sslmate.com/labs/caa/](https://sslmate.com/labs/caa/) with the "legacy
mode" to make it work.

### Did you do well?

Tools such as [test.ssl](https://github.com/drwetter/testssl.sh ) or the [Qualys
Server Test](https://www.ssllabs.com/ssltest/) now check these records, and you
should now get a green result, yipeee!

![Example of Google's Qualy test results, as of July 3rd](/assets/illustrations/caa-dns-google.png)

## Take-away

While it is not yet supported by all the providers, this 4-year-old standard has
[recently become mandatory for
CAs](https://blog.qualys.com/ssllabs/2017/03/13/caa-mandated-by-cabrowser-forum).
Thus, **all of them will have to implement CAA verification by September**, so
be ready and add your own records today!

Please also note that this standard is **not bulletproof** but will
**contribute** to your overall security.

I will be more than happy to answer your questions, comments, and thoughts about
this "new" mechanism! :)

Feel free to comment on the [Hacker News conversation](https://news.ycombinator.com/item?id=15156106) or in the Discuss below.
