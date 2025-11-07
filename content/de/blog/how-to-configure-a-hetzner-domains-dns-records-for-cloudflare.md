---
title: "How to Configure a Hetzner Domains DNS Records for Cloudflare"
description: "This blog post describes how to correctly setup the Nameserver NS DNS records on for a domain registered with Hetzner for using it with Cloudflare"
date: 2024-05-07
image: "/images/blog/cloudflare-logo.webp"
image_alt: "Cloudflare Logo"
---

Please [contact us](/index.html#contact "Blunix GmbH contact options") if anything is not clearly described, does not work, seems incorrect or if you require support.

When setting the Nameserver records to cloudflare, this is not done using the regular DNS WebUI of hetzner. Instead, you have to do this directly where all the domains that are hosted with Hetzner are listed.

You can find the required settings at [https://robot.hetzner.com/domain](https://robot.hetzner.com/domain "robot.hetzner.com/domain") and NOT at [https://dns.hetzner.com/](https://dns.hetzner.com/ "dns.hetzner.com")

Select your domain and change the Nameserver settings there:

**Before:**

![Hetzner Domain WebUI Nameservers before](/images/blog/how-to-configure-a-hetzner-domains-dns-records-for-cloudflare/before.webp)

**After:**

![Hetzner Domain WebUI Nameservers after](/images/blog/how-to-configure-a-hetzner-domains-dns-records-for-cloudflare/after.webp)

You can use

this DNS verification tool

to verify if your records are setup correctly with cloudflare.

You can completely delete the zone for your domain at [https://dns.hetzner.com/](https://dns.hetzner.com/ "dns.hetzner.com").
