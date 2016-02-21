---
layout: post
title: "Flooding the Phisherman (with fake credentials)"
description: "This morning I woke up with another phishing mail in my mailbox. I remembered @averagesecguy flooding the fake website with fake credentials, and decided to do the same."
category: Projects
tags: [netsec, phishing, flooding, fight back, python]
---
{% include JB/setup %}

This morning, another phishing email in my inbox. Short message, and a link to [http://priveservicetemporaire.fr/](http://priveservicetemporaire.fr/) (targets French users).

I remembered reading this tweet from [@averagesecguy](https://twitter.com/averagesecguy) :

<blockquote class="twitter-tweet" data-cards="hidden" lang="fr"><p>Find a phishing site? Overwhelm it with fake credentials. <a href="https://t.co/nS56XNb6ek">https://t.co/nS56XNb6ek</a> It&#39;s preconfigured for a phishing site I found today.</p>&mdash; Stephen Haywood (@averagesecguy) <a href="https://twitter.com/averagesecguy/status/569906030475399170">23 Février 2015</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

I didn't found the tweet and the link in the first place, so I built [a small Python script](https://gist.github.com/MickaelBergem/811e6635f3054cad929a) to do the same. It just posts random credentials on the given URL and checks the response to be sure everything went fine.

<script src="https://gist.github.com/MickaelBergem/811e6635f3054cad929a.js"></script>

## Release the Kraken !

Then I began to flood the phishing website :

<pre><small>$ python3 phisherflooder.py
#0000   Envoi : (.abxlsniznaan@gmail.com, MHPTwHcJw)
#0001   Envoi : (ukyf.pksw8rnp_hxkimb2gena@laposte.net, RiwnRKQ6DVKiVOKM)
#0002   Envoi : (zc1fmuuzqrtsp8hxscpf@live.net, gThR4c)
#0003   Envoi : (rijnikhdj4pa4dtdjcmlc4nyvgwc@gmail.com, 1WP83dxul3O6ECx)
#0004   Envoi : (ezd9_92zbosrisy@orange.fr, AF8oKV3cEn)

[...]

#0075   Envoi : (l1x3e40vkjnua8zr@yahoo.com, nQWCCi)
</small></pre>

After the 75<sup>th</sup> post, the flood stopped working: the website was throwing an error ("Error sending the message").
My guess is that the form sends an email to the "phisherman" instead of storing the credentials on the server, and the hoster limits the number of sent email per hour (75 mail / hour ?). Trying a few hours after gave the same result.

Now the phisherman is receiving hundreds of fake credentials into his mailbox, each day. Nice, eh ?

![Phisherman vs Water](/assets/illustrations/phisherman.gif)

Okay, now even if someone fills in their credentials, nothing will be sent.
But even if I plan to let the script run until the website finally gets down, there is something more efficient to do: report the incident !

## Reporting phishing incidents

*Whois* can give us the "abuse" registrar contact, and information about who and when the domain name has been registered.

```
whois priveservicetemporaire.fr -H
```

Sending an email to report the phishing website will hopefully result in the server being shut down.

I then looked at the email, especially the headers to see from where did the email come from :

    Received: from webmail.no-log.org (webmail.no-log.org [80.67.172.39])
        (using TLSv1 with cipher ADH-AES256-SHA (256/256 bits))
        (No client certificate requested)
        by lpn-prd-vrin014 (Postfix) with ESMTPS id 558E9180143;
        Sun,  8 Mar 2015 06:10:00 +0100 (CET)
    Received: by webmail.no-log.org (Postfix, from userid 33)
        id 1802C4BDCC; Sun,  8 Mar 2015 06:10:00 +0100 (CET)
    X-Squirrel-UserHash: QFEPDQEJKSAAAAAAABpdRlU=
    X-Squirrel-FromHash: AgsCDl4DCio=
    Message-ID: <cf9a229bb89f6718c704165a5462e8b3.squirrel@webmail.no-log.org>
    Date: Sun, 8 Mar 2015 06:10:00 +0100
    Subject: infos m.laposte
    From: P.arametres-La-poste@no-log.org
    User-Agent: SquirrelMail/1.4.21

So this email has been sent from a webmail *webmail.no-log.org* : their website shows an abuse email contact. I then sent them the full email (with headers) to them so they can block the corresponding account.

## TL;DR

* received a phishing email
* built a tool to flood the fake website with fake credentials
* reached a limit that prevents new credentials to be submitted within a period of time
* reported the phishing campaign to the hoster and the SMTP provider
