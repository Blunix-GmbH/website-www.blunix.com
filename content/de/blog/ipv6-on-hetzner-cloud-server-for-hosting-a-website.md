---
title: "Howto use IPv6 on Hetzner Cloud Servers to Host a Website"
description: "This easy to follow tutorial describes how to host a website with an IPv6 address on a Hetzner Cloud Server running Debian or Ubuntu Linux"
date: 2024-04-24
image: "/images/blog/hetzner-logo.webp"
image_alt: "Hetzner Logo"
---

Please [contact us](/index.html#contact "Blunix GmbH contact options") if anything is not clearly described, does not work, seems incorrect or if you require support.

## Table of contents

- [Introduction](#introduction)
- [Creating a Hetzner Cloud Server with an IPv6 Address](#creating-a-hetzner-cloud-server)
  - [Connecting to the Hetzner Cloud Server via SSH by using its IPv6 Address](#ssh-to-the-hetzner-cloud-server-via-ipv6)
- [Assigning additional IPv6 addresses](#assigning-additional-ipv6-addresses-to-the-hetzner-cloud-server)
  - [Temporarily Assigning IPv6 Addresses for Testing](#assigning-additional-ipv6-addresses-to-the-hetzner-cloud-server-temporary)
  - [Temporarily Making the Server Reachable for All IPs in the Subnet](#assigning-additional-ipv6-addresses-to-the-hetzner-cloud-server-temporary-whole-subnet)
  - [Permanently Assigning Individual Single IPv6 Addresses (Reboot Safe)](#assigning-additional-ipv6-addresses-to-the-hetzner-cloud-server-permanent)
  - [Permanently Assigning a whole IPv6 Subnet (Reboot Safe)](#assigning-additional-ipv6-addresses-to-the-hetzner-cloud-server-permanent-whole-subnet)
- [Setting up IPv6 DNS AAAA Records in the Hetzner DNS Management WebUI](#setting-up-ipv6-dns-aaaa-records-in-hetzner)
- [Setting up IPv6 records in /etc/hosts](#setting-up-ipv6-records-in-etc-hosts)
- [Generating a Letsencrypt Certificate for an IPv6 / AAAA Domain](#generating-a-letsencrypt-certificate-for-a-ipv6-domain)
- [Configuring the Nginx Webserver for Listening on IPv6 Addresses](#configuring-nginx-webserver-for-listening-on-ipv6-addresses)
  - [Configure Nginx to Listen on a Single IPv6 Address](#configuring-nginx-webserver-for-listening-on-a-single-ipv6-address)
  - [Configure Nginx to Listen on Multiple IPv6 Addresses](#configuring-nginx-webserver-for-listening-on-a-multiple-ipv6-addresses)
  - [Configure Nginx to Listen on an IPv6 Subnet](#configuring-nginx-webserver-for-listening-on-ipv6-subnets)
- [Configuring the Apache2 Webserver for Listening on IPv6 Addresses](#configuring-apache2-webserver-for-listening-on-ipv6-addresses)
  - [Configure Apache2 to Listen on a Single IPv6 Address](#configuring-apache2-webserver-for-listening-on-a-single-ipv6-address)
  - [Configure Apache2 to Listen on an IPv6 Subnet](#configuring-apache2-webserver-for-listening-on-ipv6-subnets)
- [Checking if the Website is Reachable](#checking-if-the-website-is-reachable)
  - [Checking the Website using a Browser](#checking-if-the-website-is-reachable)
  - [Testing the Website with Curl](#checking-if-the-website-is-reachable-via-curl)

## [Introduction](#introduction)

Before starting with IPv6 you should check if your Internet Service Provider (ISP) supports IPv6. You can do so by simply accessing [ipv6.google.com](https://ipv6.google.com/ "ipv6.google.com"). This is the google search page, but via IPv6 instead of IPv4. If this works for you, you can continue reading the article. If it does not, complain to your ISP to setup IPv6 or start using a VPN like [mullvad.net](https://mullvad.net/en "mullvad.net"), which will allow you to access IPv6.

At the time of this writing (April 2024), about [half the internet supports IPv6](https://www.google.com/intl/en/ipv6/statistics.html "www.google.com: global IPv6 statistics"), with [France and Germany having the best coverage at about 75%, the US only has about 45%](https://www.google.com/intl/en/ipv6/statistics.html#tab=per-country-ipv6-adoption "www.google.com: IPv6 statistics per country").

For hosting public websites, these aren't exactly shining numbers, but for hosting websites behind Content Delivery Networks (CDN) like [cloudflare](https://www.cloudflare.com/ "cloudflare.com"), this is irrelevant, as cloudflare supports IPv6 and can access your backend webserver at its IPv6 address.

At Blunix, we also like to exclusively assign IPv6 addresses to servers that are not meant to be publicly accessible, but only via an internal company VPN. For these company-internally used servers we use a (cost-free) IPv6 address for administrative purposes, with all WebUIs and other services only being accessible over the company internal VPN.

## [Creating a Hetzner Cloud Server with an IPv6 Address](#creating-a-hetzner-cloud-server)

Create a new server and make sure IPv6 is enabled. You can select to only use an IPv6 address for your server to save a bit of money. For this blog post, we selected to not assign an IPv4 address to the server. Pretty much all modern computers and mobile devices understand IPv6 by now, so you don't really need an IPv4 address anymore.

![Creating a Hetzner Server with an IPv6 Address or Subnet](/images/blog/howto-use-ipv6-on-hetzner-cloud-servers/hetzner-create-ipv6-server.webp)

After creating the cloud or physical server, the Hetzner Cloud WebUI shows you the IPv6 subnet that was assigned to the server. At the time of this writing, each server is assigned a /64 subnet. Fun fact: a /64 IPv6 subnet contains 18.45 quintillion, or 18.446.744.073.709.551.616, or 18.45 \* 10^18 usable IPv6 addresses. This should be enough to host a couple of websites. Refer to [the ripe net documentation on understanding IP addressing and CIDR charts](https://www.ripe.net/about-us/press-centre/understanding-ip-addressing/ "ripe.net: Understanding IP Addressing and CIDR Charts") for additional information.

![Viewing the IPv6 Subnet of the new Hetzner Server](/images/blog/howto-use-ipv6-on-hetzner-cloud-servers/hetzner-new-ipv6-server.webp)

### [Connecting to the Hetzner Cloud Server via SSH by using its IPv6 Address](#ssh-to-the-hetzner-cloud-server-via-ipv6)

If your subnet is `2a01:4f8:1c1c:dd6::/64`, then you can connect to the Hetzner Cloud server via SSH by adding a 1 at the end:

<pre class="command-line language-bash" data-output="2-14" data-continuation-str="\" data-user="user" data-host="workstation">
<code class="language-bash">ssh root@2a01:4f8:1c1c:dd6::1
The authenticity of host '2a01:4f8:1c1c:dd6::1 (2a01:4f8:1c1c:dd6::1)' can't be established.
ED25519 key fingerprint is SHA256:WBp/Jd5omTEuFOKI6tRfV5AwtBd9y+iS0de1cRr88B8.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '2a01:4f8:1c1c:dd6::1' (ED25519) to the list of known hosts.
Linux ipv6-blogpost 6.1.0-18-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.76-1 (2024-02-01) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
root@ipv6-blogpost:~# </code></pre>

## [Assigning additional IPv6 addresses](#assigning-additional-ipv6-addresses-to-the-hetzner-cloud-server)

Lets configure the network interface to be reachable on more than one IPv6 address.

### [Temporarily Assigning IPv6 Addresses for Testing](#assigning-additional-ipv6-addresses-to-the-hetzner-cloud-server-temporary)

To quickly add a new IPv6 address to your server, use the following command:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-user="root" data-host="ipv6-server">
<code class="language-bash">ip address add 2a01:4f8:1c1c:dd6::3 dev eth0</code></pre>

To check if this is working, run the following command from your workstation:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-user="user" data-host="workstation">
<code class="language-bash">ping6 2a01:4f8:1c1c:dd6::3</code></pre>

You can choose any address between the minimum and maximum value in the assigned subnet. Remember that IPv6 addresses are written hexadecimal values: "0-9" and "a-f".

Lowest possible address: `2a01:4f8:1c1c:dd6:0000:0000:0000:0000` Largest possible address: `2a01:4f8:1c1c:dd6:ffff:ffff:ffff:ffff`

More examples:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-user="root" data-host="ipv6-server">
<code class="language-bash">ip address add 2a01:4f8:1c1c:dd6:0000:0000:0000:1000 dev eth0
ip address add 2a01:4f8:1c1c:dd6:0000:0000:0815:0000 dev eth0
ip address add 2a01:4f8:1c1c:dd6:af9c:1c59:91a5:1d63 dev eth0</code></pre>

If you only use `ip address add`, the configuration will be lost after the next reboot. Refer to the next section on how to make those configurations reboot safe.

### [Temporarily Making the Server Reachable for All IPs in the Subnet](#assigning-additional-ipv6-addresses-to-the-hetzner-cloud-server-temporary-whole-subnet)

You can also make the server listen to every IP address in the whole /64 subnet like this:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-user="root" data-host="ipv6-server">
<code class="language-bash">ip route add local 2a01:4f8:1c1c:dd6::/64 dev eth0</code></pre>

### [Permanently Assigning Individual Single IPv6 Addresses (Reboot Safe)](#assigning-additional-ipv6-addresses-to-the-hetzner-cloud-server-permanent)

The IPv6 address that your server was accessible for after its creation is configured in `/etc/network/interfaces.d/50-cloud-init`:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-user="root" data-host="ipv6-server">
<code class="language-bash">cat /etc/network/interfaces.d/50-cloud-init
# This file is generated from information provided by the datasource.  Changes
# to it will not persist across an instance reboot.  To disable cloud-init's
# network configuration capabilities, write a file
# /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg with the following:
# network: {config: disabled}
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet dhcp

# control-alias eth0
iface eth0 inet6 static
    address 2a01:4f8:1c1c:dd6::1/64
    dns-nameservers 2a01:4ff:ff00::add:1 2a01:4ff:ff00::add:2
    gateway fe80::1</code></pre>

Copy the relevant section and create a new file `/etc/network/interfaces.d/99-custom`. Add a configuration block like this for each IPv6 address you want the server to be reachable at. Note that if you configure a whole subnet, the server will not be reachable for all IPs in the subnet - the block above will only make the server reachable at `2a01:4f8:1c1c:dd6::1`, not at every IP in `2a01:4f8:1c1c:dd6::1/64`! Refer to [this serverfault.com thread](https://serverfault.com/questions/1029300/why-is-24-used-in-etc-network-interfaces-when-configuring-static-ips "serverfault.com: thread on configuring subnets in /etc/network/interfaces") for additional information.

Remember that the normalized version of `2a01:4f8:1c1c:dd6::1` is `2A01:04F8:1C1C:0DD6:0000:0000:0000:0001`, and that specifying a /128 subnet in IPv6 is like specifying a /32 subnet in IPv4 - it refers to a single IP address.

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-user="root" data-host="ipv6-server">
<code class="language-bash">cat /etc/network/interfaces.d/99-custom

# app1.blunix.com
iface eth0 inet6 static
    address 2a01:4f8:1c1c:dd6:ffff:ffff:ffff:fff0/128
    dns-nameservers 2a01:4ff:ff00::add:1 2a01:4ff:ff00::add:2
    gateway fe80::1

# app2.blunix.com
iface eth0 inet6 static
    address 2a01:4f8:1c1c:dd6:1234:5678:90ab:cdef/128
    dns-nameservers 2a01:4ff:ff00::add:1 2a01:4ff:ff00::add:2
    gateway fe80::1</code></pre>

To apply changes to configuration files below `/etc/network/interfaces.d/` execute the following command. This will restart your servers network and the server will be unreachable for about two to three seconds. In this time your SSH session will hang, which is normal.

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-user="root" data-host="ipv6-server">
<code class="language-bash">systemctl restart networking</code></pre>

### [Permanently Assigning a whole IPv6 Subnet (Reboot Safe)](#assigning-additional-ipv6-addresses-to-the-hetzner-cloud-server-permanent-whole-subnet)

To create a reboot safe configuration for making the server reachable at all IPs in the subnet, simply add the route commands at the bottom:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-user="root" data-host="ipv6-server">
<code class="language-bash">cat /etc/network/interfaces.d/99-custom

# Reachable for all IPs in the /64
iface eth0 inet6 static
    address 2a01:4f8:1c1c:dd6::1/64
    dns-nameservers 2a01:4ff:ff00::add:1 2a01:4ff:ff00::add:2
    gateway fe80::1
    # Setup to respond to any address in the subnet
    post-up ip -6 route add local 2a01:4f8:1c1c:dd6::1/64 dev eth0
    pre-down ip -6 route del local 2a01:4f8:1c1c:dd6::1/64 dev eth0</code></pre>

## [Setting up IPv6 DNS AAAA Records in the Hetzner DNS Management WebUI](#setting-up-ipv6-dns-aaaa-records-in-hetzner)

For each IPv6 address you want to use for hosting a website, or any other service for that matter, configure one AAAA type DNS records. In Hetzners DNS Management WebUI, this can be done like this:

![Creating IPv6 Type AAAA DNS Records in the Hetzner DNS Management Webui](/images/blog/howto-use-ipv6-on-hetzner-cloud-servers/hetzner-dns-ipv6-aaaa.webp)

To verify that you setup the DNS record correctly you can simply ping the new domain. Make sure to use `ping6` instead of `ping`:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-user="user" data-host="workstation">
<code class="language-bash">ping6 -c 3 ipv6-blogpost.blunix.com
PING ipv6-blogpost.blunix.com(ipv6-blogpost.blunix.com (2a01:4f8:1c1c:dd6:1234:5678:90ab:cdef)) 56 data bytes
64 bytes from ipv6-blogpost.blunix.com (2a01:4f8:1c1c:dd6:1234:5678:90ab:cdef): icmp_seq=1 ttl=52 time=106 ms
64 bytes from ipv6-blogpost.blunix.com (2a01:4f8:1c1c:dd6:1234:5678:90ab:cdef): icmp_seq=2 ttl=52 time=206 ms
64 bytes from ipv6-blogpost.blunix.com (2a01:4f8:1c1c:dd6:1234:5678:90ab:cdef): icmp_seq=3 ttl=52 time=125 ms</code></pre>

## [Setting up IPv6 records in /etc/hosts](#setting-up-ipv6-records-in-etc-hosts)

You can also setup /etc/hosts records for IPv6 addresses using the exact same way you would setup IPv4 records:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-user="user" data-host="workstation">
<code class="language-bash">cat << EOF | sudo tee -a /etc/hosts
2a01:4f8:1c1c:dd6:1234:5678:90ab:cdef ipv6-blogpost.blunix.com</code></pre>

## [Generating a Letsencrypt Certificate for an IPv6 / AAAA Domain](#generating-a-letsencrypt-certificate-for-a-ipv6-domain)

There is nothing special to generating Letsencrypt certificates for IPv6 domains. In the following example we automatically agree to the terms of service (`--agree-tos`), spin up a standalone webserver (`--standalone`) and refuse to provide an email address, which only means that we will not be notified by email two weeks before the domain expires (`--register-unsafely-without-email`).

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-user="root" data-host="ipv6-server">
<code class="language-bash">certbot certonly --agree-tos --standalone --register-unsafely-without-email --domain ipv6-blogpost.blunix.com
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Account registered.
Requesting a certificate for ipv6-blogpost.blunix.com

Successfully received certificate.
Certificate is saved at: /etc/letsencrypt/live/ipv6-blogpost.blunix.com/fullchain.pem
Key is saved at:         /etc/letsencrypt/live/ipv6-blogpost.blunix.com/privkey.pem
This certificate expires on 2024-07-23.
These files will be updated when the certificate renews.
Certbot has set up a scheduled task to automatically renew this certificate in the background.

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
If you like Certbot, please consider supporting our work by:
 * Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
 * Donating to EFF:                    https://eff.org/donate-le
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -</code></pre>

## [Configuring the Nginx Webserver for Listening on IPv6 Addresses](#configuring-nginx-webserver-for-listening-on-ipv6-addresses)

The following example nginx vhost is configured to serve SSL for all of the 18.45 quintillion IPv6 addresses. Keep in mind that we have to create AAAA DNS Records for each IP, as well as configure them in `/etc/network/interfaces.d/` as described above for the IP to work.

The following SSL configuration is only an example - to create a proper configuration, take a look at [the mozilla SSL configuration generator for webservers](https://ssl-config.mozilla.org/ "ssl-config.mozilla.org: generate a secure SSL configuration for your webserver"). The following commands let you determine your nginx and openssl version:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-user="root" data-host="ipv6-server">
<code class="language-bash">nginx -v
nginx version: nginx/1.22.1

openssl version
OpenSSL 3.0.11 19 Sep 2023 (Library: OpenSSL 3.0.11 19 Sep 2023)</code></pre>

Here is the nginx vhost configuration for hosting a website using an IPv6 address on Hetzner Cloud. Notice the two listen directives, one for localhost (127.0.0.1) and one for one or all possible IPv6 addresses:

```bash
server {
    server_name ipv6-blogpost.blunix.com;
    listen 127.0.0.1:80;
    listen [::]:80;
    return 301 https://ipv6-blogpost.blunix.com$request_uri;
}

server {
    server_name ipv6-blogpost.blunix.com;

    listen 127.0.0.1:443 ssl http2;
    listen [::]:443 ssl http2;

    root /var/www/html;
    index index.html index.htm index.nginx-debian.html;
    location / {
        try_files $uri $uri/ =404;
    }

    ssl_certificate /etc/letsencrypt/live/ipv6-blogpost.blunix.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/ipv6-blogpost.blunix.com/privkey.pem;
    ssl_session_timeout 1d;
    ssl_session_cache shared:MozSSL:10m;
    ssl_session_tickets off;

    # intermediate configuration
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384:DHE-RSA-CHACHA20-POLY1305;
    ssl_prefer_server_ciphers off;

    # HSTS (ngx_http_headers_module is required) (63072000 seconds)
    add_header Strict-Transport-Security "max-age=63072000" always;
}
```

### [Configure Nginx to Listen on a Single IPv6 Address](#configuring-nginx-webserver-for-listening-on-a-single-ipv6-address)

To configure Nginx to only listen on a specific IPv6 address simply state the address in brackets:

```bash
listen [2a01:4f8:1c1c:dd6:1234:5678:90ab:cdef]:80;
listen [2a01:4f8:1c1c:dd6:1234:5678:90ab:cdef]:443 ssl http2;
```

### [Configure Nginx to Listen on Multiple IPv6 Addresses](#configuring-nginx-webserver-for-listening-on-a-multiple-ipv6-addresses)

When using public IP addresses, there is no good usecase for this. Normally, you should only use one public IP per website. However when using a public as well as an internal (private) IP, this can make sense.

In order for nginx to make a vhost available on multiple IP addresses, you can simply specify multiple listen directives to configure one Nginx Vhost to be available on multiple IPv6 addresses:

```bash
listen [2a01:4f8:1c1c:dd6:1234:5678:90ab:cdef]:80;
listen [2a01:4f8:1c1c:dd6:0000:0000:0000:1234]:80;
listen [2a01:4f8:1c1c:dd6:1234:5678:90ab:cdef]:443 ssl http2;
listen [2a01:4f8:1c1c:dd6:0000:0000:0000:1234]:443 ssl http2;
```

### [Configure Nginx to Listen on an IPv6 Subnet](#configuring-nginx-webserver-for-listening-on-ipv6-subnets)

Nginx does not provide an option to listen on a whole subnet, IPv4 or IPv6. You can configure Nginx to listen on all incoming IPv6 traffic like this:

```bash
listen [::]:443 ssl http2;
```

And then simply use your firewall to allow only the addresses you wish to let through.

## [Configuring the Apache2 Webserver for Listening on IPv6 Addresses](#configuring-apache2-webserver-for-listening-on-ipv6-addresses)

The following example apache2 vhost is configured to serve SSL for all of the 18.45 quintillion IPv6 addresses. Keep in mind that we have to create AAAA DNS Records for each IP, as well as configure them in `/etc/network/interfaces.d/` as described above for the IP to work.

The following SSL configuration is only an example - to create a proper configuration, take a look at [the mozilla SSL configuration generator for webservers](https://ssl-config.mozilla.org/ "ssl-config.mozilla.org: generate a secure SSL configuration for your webserver"). The following commands let you determine your apache2 and openssl version:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-user="root" data-host="ipv6-server">
<code class="language-bash">apache2 -v
Server version: Apache/2.4.59 (Debian)
Server built:   2024-04-05T12:02:26

openssl version
OpenSSL 3.0.11 19 Sep 2023 (Library: OpenSSL 3.0.11 19 Sep 2023)</code></pre>

First enable the apache2 ssl and headers modules:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-user="root" data-host="ipv6-server">
<code class="language-bash">a2enmod ssl headers</code></pre>

Here is the apache2 vhost configuration for hosting a website using an IPv6 address on Hetzner Cloud. Notice the two listen directives, one for localhost (127.0.0.1) and one for one or all possible IPv6 addresses:

```bash
<VirtualHost [::]:80>
    ServerName ipv6-blogpost.blunix.com
    DocumentRoot /var/www/html
    Redirect permanent / https://ipv6-blogpost.blunix.com
</VirtualHost>

<VirtualHost [::]:443>
    ServerName ipv6-blogpost.blunix.com
    DocumentRoot /var/www/html

    SSLEngine on
    SSLCertificateFile      /etc/letsencrypt/live/ipv6-blogpost.blunix.com/fullchain.pem
    SSLCertificateKeyFile   /etc/letsencrypt/live/ipv6-blogpost.blunix.com/privkey.pem
    Protocols h2 http/1.1
    Header always set Strict-Transport-Security "max-age=63072000"
    SSLProtocol             all -SSLv3 -TLSv1 -TLSv1.1
    SSLCipherSuite          ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384:DHE-RSA-CHACHA20-POLY1305
    SSLHonorCipherOrder     off
    SSLSessionTickets       off

</VirtualHost>
```

### [Configure Apache2 to Listen on a Single IPv6 Address](#configuring-apache2-webserver-for-listening-on-a-single-ipv6-address)

To configure Apache2 to only listen on a specific IPv6 address simply state the address in brackets:

```bash
listen [2a01:4f8:1c1c:dd6:1234:5678:90ab:cdef]:80;
listen [2a01:4f8:1c1c:dd6:1234:5678:90ab:cdef]:443 ssl http2;
```

### [Configure Apache2 to Listen on a Single IPv6 Address](#configuring-apache2-webserver-for-listening-on-a-single-ipv6-address)

While you can specify multiple IPv4 or IPv6 addresses in `/etc/apache2/ports.conf` like so:

```bash
Listen [2a01:4f8:1c1c:dd6:1234:5678:90ab:0000]:443
Listen [2a01:4f8:1c1c:dd6:1234:5678:90ab:1111]:443
Listen [2a01:4f8:1c1c:dd6:1234:5678:90ab:2222]:443
```

But you are still limited to one single IP in each VirtualHost configuration:

```bash
<VirtualHost [2a01:4f8:1c1c:dd6:1234:5678:90ab:0815]:443>
```

Which you can set to listen on all possible IPs:

```bash
<VirtualHost [::]:443>
```

From there you have to use the `ServerName` directive to sort the incoming requests:

```bash
ServerName ipv6-blogpost.blunix.com
```

### [Configure Apache2 to Listen on an IPv6 Subnet](#configuring-apache2-webserver-for-listening-on-ipv6-subnets)

Apache2 does not provide an option to listen on a whole subnet, neither IPv4 nor IPv6. You can configure Apache2 to listen on all incoming IPv6 traffic like this:

```bash
listen [::]:443 ssl http2;
```

And then simply use your firewall to allow only the addresses you wish to let through.

## [Checking if the Website is Reachable](#checking-if-the-website-is-reachable)

Your website should now be configured and reachable over its IPv6 address(es). Time to check if it is working correctly.

### [Checking the Website using a Browser](#checking-if-the-website-is-reachable)

Simply enter the URL in your browser:

![Viewing the apache2 Default Page Hosted with an IPv6 Address with a Browser](/images/blog/howto-use-ipv6-on-hetzner-cloud-servers/apache2-check-browser.webp)

### [Testing the Website with Curl](#checking-if-the-website-is-reachable-via-curl)

You can also use `curl` to check if the website is online:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-user="user" data-host="workstation">
<code class="language-bash">curl -6 --head --location http://ipv6-blogpost.blunix.com
HTTP/1.1 301 Moved Permanently
Server: nginx/1.22.1
Date: Wed, 24 Apr 2024 11:03:53 GMT
Content-Type: text/html
Content-Length: 169
Connection: keep-alive
Location: https://ipv6-blogpost.blunix.com/

HTTP/2 200 
server: nginx/1.22.1
date: Wed, 24 Apr 2024 11:03:54 GMT
content-type: text/html
content-length: 615
last-modified: Wed, 24 Apr 2024 09:20:47 GMT
etag: "6628ceef-267"
strict-transport-security: max-age=63072000
accept-ranges: bytes</code></pre>
