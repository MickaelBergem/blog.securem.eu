---
layout: post
title: "GitHub Pages generated a (rogue?) TLS cert for my own domain!"
description: "How the hell is this blog served over HTTPS by GitHub Pages?"
category: "ServerSide"
tags: ['Publishing','GitHub Pages','TLS','security','PKI']
image: /assets/illustrations/github-https-blog.png
---
{% include JB/setup %}

**TL;DR**: This blog is hosted on GitHub Pages, with a custom domain and over HTTPS. GitHub/Fastly
generated the certificate without me knowing, and while this is not supposed to happen.

**EDIT: this is a (silent) gradual release of HTTPS support for custom-domain websites hosted on GitHub
Pages. Read more at [the end of this post](#final-note). While it shows the certificate is not
"rogue", having certs generated without you knowing or expecting it (this is not an official
feature) remains surprising and unpleasant.**

## This blog is hosted on GitHub Pages

This blog is hosted on GitHub Pages, through Fastly (the CDN used by GitHub).

    $ host blog.securem.eu
    blog.securem.eu is an alias for mickaelbergem.github.io.
    mickaelbergem.github.io is an alias for sni.github.map.fastly.net.
    sni.github.map.fastly.net has address 151.101.17.147
    sni.github.map.fastly.net has IPv6 address 2a04:4e42:1d::403

I do have a custom domain ("blog.securem.eu"), ie not a \*.github.io one.

I am **not using CloudFlare**, which is commonly used to add HTTPS on top of a GitHub Pages website.

## GitHub Pages does not allow you to use HTTPS on custom domains

This is understandable as it would mean generating, storing and deploying a large number of
certificates for domains not owned by GitHub. I created a test (GitHub Pages website) at `test-github.securem.eu`.

![GitHub does not allow HTTPS for custom domains](/assets/illustrations/github-custom-domain-https.png)

On the official documentation, it is clearly stated:

> You can enforce HTTPS to add a layer of encryption for traffic to your GitHub Pages site if it has a github.io domain.

Trying to access this website through HTTPS will raise an security error:

> The certificate presented is only valid for www.github.com, \*.github.com, github.com, \*.github.io, github.io, \*.githubusercontent.com, githubusercontent.com

As of today, people have used CloudFlare to bring HTTPS to their custom-domain GitHub-Pages-hosted websites, which is not the case here.

## WTF

We have:

1. This blog is hosted on GitHub Pages with a custom domain, and no CloudFlare proxy.
2. GitHub Pages does not serve content over HTTPS for custom-domain websites

Then, we should get an error when trying to access it over HTTPS:

```
$ curl https://blog.securem.eu -v
*   Trying 151.101.17.147...
* TCP_NODELAY set
* Connected to blog.securem.eu (151.101.17.147) port 443 (#0)
* ALPN, offering h2
* ALPN, offering http/1.1
* TLSv1.2 (OUT), TLS handshake, Client hello (1):
* TLSv1.2 (IN), TLS handshake, Server hello (2):
* TLSv1.2 (IN), TLS handshake, Certificate (11):
* TLSv1.2 (IN), TLS handshake, Server key exchange (12):
* TLSv1.2 (IN), TLS handshake, Server finished (14):
* TLSv1.2 (OUT), TLS handshake, Client key exchange (16):
* TLSv1.2 (OUT), TLS change cipher, Client hello (1):
* TLSv1.2 (OUT), TLS handshake, Finished (20):
* TLSv1.2 (IN), TLS handshake, Finished (20):
* SSL connection using TLSv1.2 / ECDHE-RSA-AES128-GCM-SHA256
* ALPN, server accepted to use h2
* Server certificate:
*  subject: CN=blog.securem.eu
*  start date: Apr 15 15:48:38 2018 GMT
*  expire date: Jul 14 15:48:38 2018 GMT
*  subjectAltName: host "blog.securem.eu" matched cert's "blog.securem.eu"
*  issuer: C=US; O=Let's Encrypt; CN=Let's Encrypt Authority X3
*  SSL certificate verify ok.
* Using HTTP2, server supports multi-use
* Connection state changed (HTTP/2 confirmed)
* Copying HTTP/2 data in stream buffer to connection buffer after upgrade: len=0
* Using Stream ID: 1 (easy handle 0x56280a3da620)
> GET / HTTP/2
> Host: blog.securem.eu
> User-Agent: curl/7.59.0
> Accept: */*
>
* Connection state changed (MAX_CONCURRENT_STREAMS == 100)!
< HTTP/2 200
< server: GitHub.com
< content-type: text/html; charset=utf-8
< last-modified: Tue, 17 Apr 2018 14:40:47 GMT
< access-control-allow-origin: *
< expires: Wed, 18 Apr 2018 10:24:28 GMT
< cache-control: max-age=600
< x-github-request-id: D89E:34CA:4DBEB7:6CC5E4:5AD71A84
< accept-ranges: bytes
< date: Wed, 18 Apr 2018 10:14:29 GMT
< via: 1.1 varnish
< age: 1
< x-served-by: cache-lcy19247-LCY
< x-cache: HIT
< x-cache-hits: 1
< x-timer: S1524046470.828003,VS0,VE0
< vary: Accept-Encoding
< x-fastly-request-id: 2e84e2cab76ec88bccbc696a0c4984618c7f2a5f
< content-length: 9260
<


<!DOCTYPE html>
[...]
```

Oopsie?

So, yes, **this very website is served by GitHub Pages over HTTPS**. With a Let's Encrypt certificate generated a few days ago. Here is the certificate in the Certificate Transparency logs: [https://crt.sh/?id=399789700](https://crt.sh/?id=399789700).

**I did not generate this certificate**. GitHub (or Fastly, or someone else) did. And GitHub has the private key associated with the certificate, since they are serving the content over a trusted HTTPS connection.

![GitHub sometimes allow HTTPS for custom domains](/assets/illustrations/github-https-blog.png)


## Conclusion?

I still do not explain how this happened. The best guess is that GitHub is preparing for automatic Let's Encrypt certificate generation for custom domains, and tested it on a small (?) subset of the repositories.

One way to confirm this would be to check in the Certificate Transparency logs what other certificates have been generated around the same date and time, to see if other custom-domain GitHub Pages websites got new certificates. I didn't have time yet to investigate further.

Anyway, this looks weird since GitHub generated a certificate for a domain I own without notifying me or asking whether I was OK with it. I could have used [DNS CAA](https://blog.securem.eu/serverside/2017/07/02/dns-caa-what-why-and-how-this-new-pki-control-mechanism/), or alerts set on the Certificate Transparency logs to prevent this or be notified of this "rogue" certificate.

Let me know if you think of any other possible explanation, or if you know something about it.

What is most disturbing is the fact that HTTPS seems to work only on `blog.securem.eu`. Renaming to `blog-test.securem.eu` shows the usual behaviour (with no HTTPS), but switching back to `blog.securem.eu` enables HTTPS again.

PS: I am not mad at GitHub for doing it, just surprised since I wasn't expecting it and wasn't notified about it. In the end I am even happy to benefit from HTTPS, even if I feel less in control of what happens. The GitHub team does a really great job, and I am very happy to be able to use GitHub Pages for free (even without HTTPS).

<hr id="final-note">

<big>
Thanks on comments on [the HackerNews thread](https://news.ycombinator.com/item?id=16866208#16866487), I discovered [this thread](https://github.com/isaacs/github/issues/156#issuecomment-374870960) that suggests that **the
feature is progressively rolled out by GitHub**. There is also an "official confirmation" [in this gist](https://gist.github.com/coolaj86/e07d42f5961c68fc1fc8#gistcomment-2370070).
</big>

<big>
Hence, there is no evil attack taking place, but instead GitHub Pages silently doing DV (Domain
Validation) to generate certificates, since a few weeks.
</big>

What started as an unpleasant surprise (WTF is this certificate doing here?) changed to a happy
ending, since I got HTTPS deployed on this blog (by the GitHub team).

**I still encourage you to set up Certificate Transparency alerts to be sure all certificates
generated for your domains are legit.** I am currently testing [CertSpotter by SSLMate](https://sslmate.com/certspotter/)
(free up to 5 domains, covers all the subdomains).
