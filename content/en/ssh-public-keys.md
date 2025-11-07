---
title: "Blunix GmbH SSH public keys for Linux Emergency Support"
description: "Our Consultants SSH public keys for Linux Server Support. Please add only the key of the Consultant you are talking to and remove it after the call is finished"
layout: "ssh-public-keys"
url: "/ssh-public-keys.html"
image: "/images/linux-emergency-support/background.webp"
hero_background: "/images/linux-emergency-support/background.webp"
hero_subtitle: "Please open your firewall for SSH access from gateway.blunix.com"
---

Please install **only** the SSH public key of the Blunix consultant you are currently talking to. Remove the key again as soon as the engagement is finished.

## Linux Emergency Support Consultants SSH public keys

<pre><code class="language-plaintext">
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIDdR6UEPCX8rxpdWSCRJ7qs/2blKeyKzzCeOunyZ3Ake f.winter@blunix.com
</code></pre>

<pre><code class="language-plaintext">
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAILMtrN645dDW010UTbTCE/Bu4GW60f0Dj3k4VIhDRCDF p.thurner@blunix.com
</code></pre>

<pre><code class="language-plaintext">
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAINZqLUZceWIm365F6MEop404K/SLqGgosasFoRx75Bi3 g.nair@blunix.com
</code></pre>

<pre><code class="language-plaintext">
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIKB4uC+mHtvwkDpvnSNV4xdIRVyKK1+J9Ulr/WhdSiQZ j.schaefer@blunix.com
</code></pre>

## How to setup the SSH public key on your servers

Blunix GmbH consultants require a Linux account with sudo permissions or `root@` access to your servers. Use the following steps to install the key:

<pre class="command-line" data-user="user" data-host="workstation" data-output="" data-continuation-str="\"><code class="language-bash">
ssh root@server
</code></pre>

<pre class="command-line" data-user="root" data-host="your-server" data-output="" data-continuation-str="\"><code class="language-bash">
mkdir ~/.ssh/
nano ~/.ssh/authorized_keys
</code></pre>

Paste **only** the SSH public key of the Blunix consultant you are working with. Save the file by pressing:

- `CTRL + o`
- `ENTER`
- `CTRL + x`

After your issue is resolved, remove the SSH public key again. In `nano` you can delete a line with:

- `CTRL + k`

Finally, set the correct permissions for the files:

<pre class="command-line" data-user="root" data-host="your-server" data-output="" data-continuation-str="\"><code class="language-bash">
chmod 700 ~/.ssh/
chmod 600 ~/.ssh/authorized_keys
</code></pre>
