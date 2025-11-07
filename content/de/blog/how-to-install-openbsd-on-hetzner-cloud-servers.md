---
title: "How to Install OpenBSD on ARM based Hetzner Cloud Servers"
description: "This tutorial describes how to install OpenBSD on ARM based Hetzner Cloud servers using the Hetzner rescue system"
date: 2024-05-23
image: "/images/blog/openbsd-logo.webp"
image_alt: "OpenBSD Logo"
---

Please [contact us](/index.html#contact "Blunix GmbH contact options") if anything is not clearly described, does not work, seems incorrect or if you require support.

Installing OpenBSD on a Hetzner cloud server is very easy and only takes about five minutes. Here is everything you need to know in easy to follow steps.

## Table of contents

- [Choosing the Correct Hetzner Cloud Server](#choosing-the-correct-hetzner-cloud-server)
- [Downloading and Flashing the OpenBSD Installer Inside the Hetzner Rescue System](#downloading-and-flashing-the-openbsd-installer-inside-the-hetzner-rescue-system)
- [Installing OpenBSD using the Hetzner Cloud Server KVM Console](#installing-openbsd-using-the-hetzner-cloud-server-kvm-console)
- [Most Basic Security Steps for the New OpenBSD Installation](#most-basic-security-steps-for-the-new-openbsd-installation)
  - [Setup Your SSH Public Key](#setup-your-ssh-public-key)
  - [Disable Password Based SSH Authentication](#disable-password-based-ssh-authentication)
  - [Disabling the Root User Password](#disabling-the-root-user-password)

## [Choosing the Correct Hetzner Cloud Server](#choosing-the-correct-hetzner-cloud-server)

Create a hetzner cloud server and make sure you choose the ARM variant:

![Choosing a ARM cloud server in the Hetzner WebUI](/images/blog/how-to-install-openbsd-on-hetzner-cloud-servers/hetzner-cloud-arm-server.webp)

After creating it, boot it into the rescue system:

![Booting the Hetzner rescue system](/images/blog/how-to-install-openbsd-on-hetzner-cloud-servers/hetzner-boot-rescue-ssytem.webp)

ARM based servers seem to boot significantly slower than regular ones - about one to two minutes until you can login with SSH. Just give it a minute ;-)

## [Downloading and Flashing the OpenBSD Installer Inside the Hetzner Rescue System](#downloading-and-flashing-the-openbsd-installer-inside-the-hetzner-rescue-system)

Go to [The OpenBSD 7.5 download page for ARM64 images](https://cdn.openbsd.org/pub/OpenBSD/7.5/arm64/) and download the miniroot image. The miniroot image is simply a dd-able image that you write directly to `/dev/sda `. It will start the installer after you reboot. Download the SHA256.sig as well:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-prompt="root@rescue ~ #">
<code class="language-bash">wget https://cdn.openbsd.org/pub/OpenBSD/7.5/arm64/miniroot75.img
wget https://cdn.openbsd.org/pub/OpenBSD/7.5/arm64/SHA256.sig</code></pre>

Verify the sha256sum of the image:

<pre class="command-line language-bash" data-output="2-3,5-99" data-continuation-str="\" data-prompt="root@rescue ~ #">
<code class="language-bash">sha256sum miniroot75.img 
05df229dc026785b5b3d1ec8b0dcd46780a2a5bdf99b7e739d83abf4ff7b3ff5  miniroot75.img

grep 05df229dc026785b5b3d1ec8b0dcd46780a2a5bdf99b7e739d83abf4ff7b3ff5 SHA256.sig 
SHA256 (miniroot75.img) = 05df229dc026785b5b3d1ec8b0dcd46780a2a5bdf99b7e739d83abf4ff7b3ff5</code></pre>

Simply write the miniroot image to disk using `dd `.

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-prompt="root@rescue ~ #">
<code class="language-bash">dd if=miniroot75.img of=/dev/sda bs=4M</code></pre>

After this, `reboot` to start the installation:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-prompt="root@rescue ~ #">
<code class="language-bash">reboot</code></pre>

## [Installing OpenBSD using the Hetzner Cloud Server KVM Console](#installing-openbsd-using-the-hetzner-cloud-server-kvm-console)

Now open the Hetzner KVM Console for your VM to configure and run the installation:

![Activating the Hetzner KVM console](/images/blog/how-to-install-openbsd-on-hetzner-cloud-servers/activate-hetzner-kvm-console.webp)

Here is a Screenshot of the OpenBSD installer viewed by the KVM console:

![Viewing the OpenBSD installer in the Hetzner KVM console](/images/blog/how-to-install-openbsd-on-hetzner-cloud-servers/viewing-the-openbsd-installer-in-the-kvm-console.webp)

For whatever reason, sometimes the KVM console screen would go blank. In this case simply click the red “Connect” button at the bottom right screen.

![Reconnecting to the Hetzner KVM console on black screen](/images/blog/how-to-install-openbsd-on-hetzner-cloud-servers/reconnect-to-kvm-console-on-black-screen.webp)

The installation itself is pretty straight forward and if you’re a bit into Linux (or BSD) you should understand everything. Most of the defaults can be accepted.

Here are all configuration options where we did not choose the defaults.

- System hostname: openbsd-test.blunix.com

- Allow root SSH login: yes

- Location of sets: http

As said, pretty much all of it is ENTER ENTER ENTER.

When the installation is finished, simply press ENTER ;-) to reboot.

![Viewing the finished OpenBSD installation in the Hetzner KVM console](/images/blog/how-to-install-openbsd-on-hetzner-cloud-servers/finished-openbsd-installation-view.webp)

OpenBSD boots rather quickly itself, compared to the rescue systems initial boot. After the reboot, you should be able to login using SSH. You can also see that the system booted in the Hetzner Cloud server KVM console:

![Viewing the booted OpenBSD installation in the KVM web console](/images/blog/how-to-install-openbsd-on-hetzner-cloud-servers/booted-openbsd-in-kvm-console.webp)

## [Most Basic Security Steps for the New OpenBSD Installation](#most-basic-security-steps-for-the-new-openbsd-installation)

After the server is reachable via SSH we recommend you to follow at least these steps for a reasonably secure setup.

### [Setup Your SSH Public Key](#setup-your-ssh-public-key)

Setup your ssh public keys on the server to be able to login without a password:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="user@workstation:~$">
<code class="language-bash">ssh-copy-id root@<your-servers-ip></code></pre>

### [Disable Password Based SSH Authentication](#disable-password-based-ssh-authentication)

Disable password authentication and enforce SSH keypair authentication in the SSH configuration file:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@openbsd:~#">
<code class="language-bash">sed -i 's/.*PasswordAuthentication.*/PasswordAuthentication no/g' /etc/ssh/sshd_config</code></pre>

Then restart sshd for the changes to take effect:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@openbsd:~#">
<code class="language-bash">rcctl restart sshd               
sshd(ok)
sshd(ok)</code></pre>

### [Disabling the Root User Password](#disabling-the-root-user-password)

Now that we can login without a password, we can remove the password we set during the installation. This will not set an empty password for root, but simply no password - as in no password would work, as well as an empty password would not work. You can then only login as root via SSH.

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@openbsd:~#">
<code class="language-bash">sed -i 's/^root:[^:]*:/root::/' /etc/master.passwd</code></pre>
