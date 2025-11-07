---
title: "Ultimate Guide About Linux Server Security Basics for Debian"
description: "This guide explains all essential Linux security essentials for a fresh Debian Linux server and describes all required basic security measures."
date: 2024-06-04
image: "/images/blog/debian-logo.webp"
image_alt: "Debian Linux Logo"
---

Please [contact us](/#contact "Blunix GmbH contact options") if anything is not clearly described, does not work, seems incorrect or if you require support.

This extensive tutorial thoroughly explains the necessary security measures to secure a fresh Debian Linux server installation. It contains and explains all steps required to keep your server safe when it is publicly reachable over the internet.

It is written for those who "just want to host wordpress" or similar tools on Debian servers. It covers the essential security measures, provides insights on additional steps you can take and explains why basic security is usually sufficient for most hosting requirements.

If you follow this tutorial, you can consider your server to be secure.

## Table of contents

- [First Step - Upgrading the System and Rebooting](#first-step---upgrading-the-system-and-rebooting)
- [How to Secure SSH](#how-to-secure-ssh)
  - [Replacing SSH Passwords with SSH Keypair Based Authentication](#replacing-ssh-passwords-with-ssh-keypair-based-authentication)
    - [Generating a SSH Keypair with Private and Public Key](#generating-a-ssh-keypair-with-private-and-public-key)
    - [Setting Up the New SSH Public Key on the Server](#setting-up-the-new-ssh-public-key-on-the-server)
  - [Disabling SSH Password Authentication on the Server](#disabling-ssh-password-authentication-on-the-server)
  - [Disabling Unrequired Features in the SSH Daemon](#disabling-unrequired-features-in-the-ssh-daemon)
  - [Verifying the SSH Daemon Configuration File](#verifying-the-ssh-daemon-configuration-file)
  - [Restarting the SSH Daemon using Systemds systemctl Command](#restarting-the-ssh-daemon-using-systemds-systemctl-command)
  - [Configuring fail2ban to Block IPs With to Many Failed Authentication Attempts](#configuring-fail2ban-to-block-ips-with-to-many-failed-authentication-attempts)
    - [Is There a Need for fail2ban when SSH Password Authentication is Disabled?](#is-there-a-need-for-fail2ban-when-ssh-password-authentication-is-disabled)
- [Configuring Automatic and Unattended apt Package Upgrades](#configuring-automatic-and-unattended-apt-package-upgrades)
  - [Installing Unattended apt Upgrades](#installing-unattended-apt-upgrades)
  - [How Systemd Triggers the Automatic Apt Upgrades](#how-systemd-triggers-the-automatic-apt-upgrades)
    - [Systemd Timer and Systemd Service for apt update](#systemd-timer-and-systemd-service-for-apt-update)
    - [Systemd Timer and Systemd Service for apt upgrade](#systemd-timer-and-systemd-service-for-apt-upgrade)
    - [Configuring the Interval at which Automatic Unattended apt Upgrades are Triggered](#configuring-the-interval-at-which-automatic-unattended-apt-upgrades-are-triggered)
  - [Fine-Grained Configuration of What to Install with Automatic apt Upgrades](#fine-grained-configuration-of-what-to-install-with-automatic-apt-upgrades)
  - [Automatically Installing Security Upgrades for Additionally Configured Non-Debian Managed Apt Repositories](#automatically-installing-security-upgrades-for-additionally-configured-non-debian-managed-apt-repositories)
  - [Additional Configuration of Unattended Upgrades](#additional-configuration-of-unattended-upgrades)
- [Securing Linux User and Linux Group Accounts](#securing-linux-user-and-linux-group-accounts)
  - [Securely Creating Linux Users and Groups Intended for Programs and Tools](#securely-creating-linux-users-and-groups-intended-for-programs-and-tools)
  - [Securely Creating Linux Users Intended for Humans](#securely-creating-linux-users-intended-for-humans)
  - [Configuring Remote SSH Login Access for Linux Users](#configuring-remote-ssh-login-access-for-linux-users)
  - [Adding Linux Users to Existing Groups](#adding-linux-users-to-existing-groups)
- [Using the UMASK to Create Files and Directories with Secure Permissions](#using-the-umask-to-create-files-and-directories-with-secure-permissions)
  - [What is the umask](#what-is-the-umask)
  - [Setting a Temporary umask for the Current Shell](#setting-a-temporary-umask-for-the-current-shell)
  - [Setting a Permanent Umask for Your Default Shell](#setting-a-permanent-umask-for-your-default-shell)
  - [Security Preperations Before Creating new Linux Users](#security-preperations-before-creating-new-linux-users)
  - [Setting the umask for a Linux User that Runs a Specific Program](#setting-the-umask-for-a-linux-user-that-runs-a-specific-program)
- [Using Sudo to Configure Selectively Elevating Linux User Permissions](#using-sudo-to-configure-selectively-elevating-linux-user-permissions)
  - [Granting Passwordless sudo Privileges to Humans](#granting-passwordless-sudo-privileges-to-humans)
  - [Granting Password-Protected sudo Privileges to Humans](#granting-password-protected-sudo-privileges-to-humans)
  - [Granting sudo Privileges to Specific Commands Only](#granting-sudo-privileges-to-specific-commands-only)
- [Debian Linux Server Network Firewall Security Basics](#debian-linux-server-network-firewall-security-basics)
  - [What Do Firewalls for Filtering Incoming Traffic Actually Do](#what-do-firewalls-for-filtering-incoming-traffic-actually-do)
    - [Verifying Your Firewall Configuration for Filtering Incoming Traffic](#verifying-your-firewall-configuration-for-filtering-incoming-traffic)
    - [Advanced Methods of Scanning for Security Issues](#advanced-methods-of-scanning-for-security-issues)
  - [What Do Firewalls for Filtering Outgoing Traffic Actually Do](#what-do-firewalls-for-filtering-outgoing-traffic-actually-do)
    - [Blacklisting Destinations with a Firewall for Outoing Traffic](#blacklisting-destinations-with-a-firewall-for-outoing-traffic)
    - [Whitelisting Destinations with a Firewall for Outoing Traffic](#whitelisting-destinations-with-a-firewall-for-outoing-traffic)
  - [Common Mistake: Do Not Forget About Firewalling Your IPv6 Addresses](#common-mistake-do-not-forget-about-firewalling-your-ipv6-addresses)
  - [What Firewall Wrapper or Tool to Choose](#what-firewall-wrapper-or-tool-to-choose)
  - [Best Possible Network Security for Company-Internally Used Tools](#best-possible-network-security-for-company-internally-used-tools)
- [Security Basics for Webservers: Nginx and Apache2](#security-basics-for-webservers-nginx-and-apache2)
  - [Checking Response Headers for Leaking Information](#checking-response-headers-for-leaking-information)
  - [Using Properly Configured SSL](#using-properly-configured-ssl)
- [Physical Servers: Unattended Updates for Firmwares for the BIOS / UEFI and Other Physical Components like SSDs](#physical-servers-unattended-updates-for-firmwares-for-the-bios-uefi-and-other-physical-components-like-ssds)
- [Additional Security Considerations](#additional-security-considerations)

## [First Step - Upgrading the System and Rebooting](#first-step---upgrading-the-system-and-rebooting)

When you get a fresh Debian Server from your cloud or physical server provider, the first step should always be to install all available upgrades and then, if required, reboot. Most of the time IaaS providers have slightly outdated templates.

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
apt update
apt upgrade
</code></pre>

You can check if a reboot is required like this - if the file exists and lists kernel packages (linux-image), a reboot is required.

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
cat /var/run/reboot-required.pkgs
linux-image-6.1.0-20-amd64
linux-image-6.1.0-21-amd64
</code></pre>

As you can see, a new kernel is available and you should reboot to use it:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
reboot
</code></pre>

## [How to Secure SSH](#how-to-secure-ssh)

Let's start with SSH, the only service that almost every Debian Linux server that has a public IP address has installed by default.

### [Replacing SSH Passwords with SSH Keypair Based Authentication](#replacing-ssh-passwords-with-ssh-keypair-based-authentication)

The first problem with SSH is that almost every server or cloud provider gives you a fresh Debian with some sort of password for SSH. This is because using SSH keypair authentication is often complicated to new users. Lets demystify that.

#### [Generating a SSH Keypair with Private and Public Key](#generating-a-ssh-keypair-with-private-and-public-key)

Let's generate a new ssh keypair. We will generate a keypair using the [ed25519 signature algorithm](https://en.wikipedia.org/wiki/EdDSA#Ed25519 "wikipedia.org: ed25519"), which at the time of this writing is considered the most secure choice for new SSH keypairs. Enter the following command, and then just keep pressing ENTER.

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-prompt="user@workstation:~$">
<code class="language-bash">
ssh-keygen -t ed25519
</code></pre>

It will produce the following output:

```
1  Generating public/private ed25519 key pair.
 2  Enter file in which to save the key (/home/user/.ssh/id_ed25519):
 3  Enter passphrase (empty for no passphrase):
 4  Enter same passphrase again:
 5  Your identification has been saved in /home/user/.ssh/id_ed25519
 6  Your public key has been saved in /home/user/.ssh/id_ed25519.pub
 7  The key fingerprint is:
 8  SHA256:aIxPK+6UxUWrgCAUWkBKDYMldx+nvOBqJnMW3xYVDxg user@host
 9  The key's randomart image is:
10  +--[ED25519 256]--+
11  |OXB . Eo=        |
12  |*=.+ o.= =       |
13  |o . o + + .      |
14  |   . * *         |
15  |  . o @ S        |
16  |   + B o         |
17  |o * = =          |
18  | B o o           |
19  |   .o            |
20  +----[SHA256]-----+
```

Lets go through this output line by line:

Line 1 is pretty obvious.

Line 2 lets you define the location for your new private key. Note that I just pressed ENTER here to use the default location. If you do not have a keypair yet, simply use the default location as well. If you have multiple SSH keypairs, provide a custom location for the new keyair. In this case you will have to add them to your `ssh-agent `using the following command:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="user@workstation:~$">
<code class="language-bash">
ssh-add ~/.ssh/customer/id_ed25519
Identity added: /home/user/.ssh/customer/id_ed25519 (p.thurner@customer.com)
</code></pre>

Line 3 and 4 ask for a password for the ssh private key, meaning that if you want to use the private key to login to a server, you have to decrypt it using a password first. If you are SURE that your workstation will not be accessed by another person while you are logged in and you are not physically present (as in an [evil maid attack](https://en.wikipedia.org/wiki/Evil_maid_attack "wikipedia.org: evil maid attack")), and if you are not considering that malware could be installed on your computer to extract your SSH private key, then you can omit setting a password for the SSH private key. When you set a password for your SSH private key, you will have to enter that when you use the private key to login to a server. The program `ssh-agent `caches that password for you, by default indefinitely, but you can define that when you start the `ssh-agent `with the `-t `argument. From `man ssh-agent `:

```
-t life
    Set a default value for the maximum lifetime of identities added to the agent. The lifetime may be specified in seconds or
    in a time format specified in sshd_config(5). A lifetime specified for an identity with ssh-add(1) overrides this value.
    Without this option the default maximum lifetime is forever.
```

Hence, if you use `ssh-agent `, which you will surely do to not have to enter your SSH private key password 50 times a day, then by default it will cache that password forever (that means until you log out or shutdown / reboot). If another person gets physical access to your laptop while you are logged in, the password is hence useless. It is hence best to make sure your workstation is turned off when you are not using it.

Line 5 tells you that the ssh PRIVATE key is now at `/home/user/.ssh/id_ed25519 `. This is what you use to login to servers. DO NOT GIVE THIS FILE TO ANYONE!

Line 6 tells you that your SSH public key is now at `/home/user/.ssh/id_ed25519.pub `. As the word public implies, you can [publish your SSH public keys in the internet if you want](/ssh-public-keys.html "blunix.com: consultants ssh public keys") without any security risk. The contents of this file is what you later upload to servers in the `~/.ssh/authorized_keys `file.

Line 7 and 8 is the public key fingerprint, which is a unique identifier derived from the public key and is often used to verify the authenticity of the key. You can display the fingerprint later using the following command:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="user@workstation:~$">
<code class="language-bash">
ssh-keygen -lf ~/.ssh/id_ed25519
256 SHA256:aIxPK+6UxUWrgCAUWkBKDYMldx+nvOBqJnMW3xYVDxg user@host (ED25519)
</code></pre>

The SSH public key fingerprint is a base64 encoded sha256 hash of the key portion of the ssh public key. For better understanding, here is how you generate the fingerprint from a SSH public key using bash commands:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="user@workstation:~$">
<code class="language-bash">
cut -d &#x27; &#x27; -f 2 ~/.ssh/id_ed25519.pub | base64 -d | sha256sum | cut -d &#x27; &#x27; -f 1 | xxd -r -p | base64
aIxPK+6UxUWrgCAUWkBKDYMldx+nvOBqJnMW3xYVDxg=
</code></pre>

Line 9 to 20 this random art image is a visual representation of the public key. It's designed to provide a unique and a for humans easily recognizable visual fingerprint of the key. This visual fingerprint is supposed to help users to verify the authenticity of the public key. In my opinion this is not really useful for humans. In my 20 years of working with Linux, I have not done this once. You can display the random art image for an existing public (or private) key like so - it is the same output for both the private and public key, as you can generate the public key from a given private key:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="user@workstation:~$">
<code class="language-bash">
ssh-keygen -lv -f ~/.ssh/id_ed25519.pub
256 SHA256:aIxPK+6UxUWrgCAUWkBKDYMldx+nvOBqJnMW3xYVDxg user@host (ED25519)
+--[ED25519 256]--+
|OXB . Eo=        |
|*=.+ o.= =       |
|o . o + + .      |
|   . * *         |
|  . o @ S        |
|   + B o         |
|o * = =          |
| B o o           |
|   .o            |
+----[SHA256]-----+
</code></pre>

In case you were curious: this is how to generate a public key from a private key, should you ever "loose" the public key:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-prompt="user@workstation:~$">
<code class="language-bash">
ssh-keygen -y -f ~/.ssh/id_ed25519
</code></pre>

#### [Setting Up the New SSH Public Key on the Server](#setting-up-the-new-ssh-public-key-on-the-server)

In order to "install" your new SSH public key on the server you administrate, use the following command. It will copy your newly generated default ssh public key `~/.ssh/id_ed25519 `to `root@server:/root/.ssh/authorized_keys `. If you have multiple SSH public keys, use the `-i ~/.ssh/customer/id_ed25519 `argument.

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="user@workstation:~$">
<code class="language-bash">
ssh-copy-id root@www.blunix.com
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
root@www.blunix.com&#x27;s password:

Number of key(s) added: 1

Now try logging into the machine, with:   &quot;ssh &#x27;root@www.blunix.com&#x27;&quot;
and check to make sure that only the key(s) you wanted were added.
</code></pre>

To verify that the key was installed simply try to login to the server. It should not ask for a password anymore:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="user@workstation:~$">
<code class="language-bash">
ssh root@blunix.com
Linux www.blunix.com 6.1.0-18-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.76-1 (2024-02-01) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Sun May 26 06:39:56 2024 from 146.70.134.46
root@www.blunix.com:~#
</code></pre>

After this, it is safe to remove the password for the root user. Note that this will not set an empty password, as in allow everyone to login, but it writes a `! `instead of a password hash in `/etc/shadow `, which means that no password will ever be accepted - the only way to become the root user then is to login via ssh using the private key to the public key that is now stored in `/root/.ssh/authorized_keys `.

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
passwd --delete --lock root
</code></pre>

### [Disabling SSH Password Authentication on the Server](#disabling-ssh-password-authentication-on-the-server)

Now that we can login to the Server without a password, we should disable password based authentication on the server completely. After that, the only way to login with SSH on the server is with the SSH private key we setup in the previous section.

It used to be common place to directly edit `/etc/ssh/sshd_config `. Recent Debian Linux versions now feature a directory `/etc/ssh/sshd_config.d/ `, in which you can place overrides to the default configuration file. This is preferred, so that apt can automatically manage the original configuration file and place updates to the default configuration options there if required. If you make changes to it, apt will not update this file with new versions released by the package maintainer.

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
echo &quot;PasswordAuthentication no&quot; | tee -a /etc/ssh/sshd_config.d/99-custom.conf
</code></pre>

### [Disabling Unrequired Features in the SSH Daemon](#disabling-unrequired-features-in-the-ssh-daemon)

The default SSH daemon installation on Debian comes with a lot of features you MOST likely do not need. We can safely disable those. Common candidates are:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
cat &lt;&lt; EOF | tee -a /etc/ssh/sshd_config.d/99-custom.conf
AllowTcpForwarding: no
HostbasedAuthentication: no
PermitEmptyPasswords: no
PermitTunnel: no
X11Forwarding: no
EOF
</code></pre>

Refer to the [sshd config manual page](https://www.man7.org/linux/man-pages/man5/sshd_config.5.html "man7.org: sshd_config manual") for additonal information on each configuration option. As described in the next section, verify and restart the SSH daemon configuration after making changes.

### [Verifying the SSH Daemon Configuration File](#verifying-the-ssh-daemon-configuration-file)

Before restarting the ssh daemon, it is helpful to make sure we did not make any syntax errors in the configuration files. For this, use the following command:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
sshd -t
/etc/ssh/sshd_config line 124: no argument after keyword &quot;NoSuchOption&quot;
/etc/ssh/sshd_config: terminating, 1 bad configuration options
</code></pre>

As you can see, the configuration option `NoSuchOption `does not exist and sshd is complaining about it. When controlling the ssh daemon process with systemd, systemd also uses this check:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
grep ExecStartPre /lib/systemd/system/ssh.service
ExecStartPre=/usr/sbin/sshd -t
</code></pre>

Hence, if we would try to just restart the SSH daemon using systemd while the configuration file is borked, it would fail:

<pre class="command-line language-bash" data-output="2-4,6-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
systemctl restart sshd.service
Job for ssh.service failed because the control process exited with error code.
See &quot;systemctl status ssh.service&quot; and &quot;journalctl -xeu ssh.service&quot; for details.

journalctl _COMM=sshd --lines=2
May 26 07:00:52 www.blunix.com sshd[1615811]: /etc/ssh/sshd_config: line 125: Bad configuration option: NoSuchOption
May 26 07:00:52 www.blunix.com sshd[1615811]: /etc/ssh/sshd_config: terminating, 1 bad configuration options
</code></pre>

You can make 100% sure that your configuration changes were applied by using `sshd -T `to print the configuration that is applied to `sshd `when it is started. Note that the runtime configuration options is spelled lowercase, for example `passwordauthentication `, while the configuration options in `/etc/ssh/sshd_config `and inside `/etc/ssh/sshd_config.d/ `are spelled CamelCase like `PasswordAuthentication `.

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
sshd -T | grep --ignore-case PasswordAuthentication
passwordauthentication no
</code></pre>

### [Restarting the SSH Daemon using Systemds systemctl Command](#restarting-the-ssh-daemon-using-systemds-systemctl-command)

After making changes to the SSH daemons config file, we have to restart the SSH daemon to apply the changes. When you configured everything to your satisfaction, restart the SSH daemon using the systemd `systemctl `command:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
systemctl restart sshd.service
</code></pre>

And make sure it is running correctly:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
systemctl status sshd.service
● ssh.service - OpenBSD Secure Shell server
     Loaded: loaded (/lib/systemd/system/ssh.service; enabled; preset: enabled)
     Active: active (running) since Sun 2024-05-26 06:44:44 CEST; 3min 16s ago
       Docs: man:sshd(8)
             man:sshd_config(5)
    Process: 1615569 ExecStartPre=/usr/sbin/sshd -t (code=exited, status=0/SUCCESS)
   Main PID: 1615570 (sshd)
      Tasks: 1 (limit: 2244)
     Memory: 1.4M
        CPU: 26ms
     CGroup: /system.slice/ssh.service
             └─1615570 &quot;sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startups&quot;

May 26 06:44:43 www.blunix.com systemd[1]: Starting ssh.service - OpenBSD Secure Shell server...
May 26 06:44:44 www.blunix.com sshd[1615570]: Server listening on 0.0.0.0 port 22.
May 26 06:44:44 www.blunix.com sshd[1615570]: Server listening on :: port 22.
May 26 06:44:44 www.blunix.com systemd[1]: Started ssh.service - OpenBSD Secure Shell server.
</code></pre>

### [Configuring fail2ban to Block IPs With to Many Failed Authentication Attempts](#configuring-fail2ban-to-block-ips-with-to-many-failed-authentication-attempts)

The fail2ban filter for sshd is designed to permanently drop all connections from IP addresses that failes to authenticate more than a given number of times within a given timeframe.

#### [Is There a Need for fail2ban when SSH Password Authentication is Disabled?](#is-there-a-need-for-fail2ban-when-ssh-password-authentication-is-disabled)

There are millions of bots scanning the internet for SSH servers that accept passwords, and if they do, these bots try weak passwords to see if they can log in. However now that we have disabled password based authentication above, logging in with a password is not possible all together anymore. Hence all of those bots will fail. The chance of an attacker "guessing" our SSH private key is (ridiculously close to) zero.

Disabling password authentication doesn't keep bots from trying of course. Failed SSH authentication attempts are a little bit like bugs crashing into your windshields when you drive on the highway - its completely normal and not a danger in any way. Here are the logfiles for failed attempts from the server that runs the www.blunix.com website for the timeframe of 24 hours:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
journalctl _COMM=sshd --grep=&#x27;^invalid user&#x27; --no-pager --since &quot;2024-05-25 00:00:00&quot; --until &quot;2024-05-25 23:59:59&quot; | nl
1   May 25 01:18:59 www.blunix.com sshd[1608441]: Invalid myuser myusername from 85.209.11.27 port 54444
2   May 25 01:34:51 www.blunix.com sshd[1608627]: Invalid myuser myuser from 192.227.148.214 port 47718
3   May 25 01:34:52 www.blunix.com sshd[1608629]: Invalid myuser myuser from 192.227.148.214 port 47730
4   May 25 01:34:53 www.blunix.com sshd[1608631]: Invalid myuser myuser from 192.227.148.214 port 47736
[...]
863 May 25 23:29:49 www.blunix.com sshd[1612608]: Invalid user admin from 85.209.11.27 port 56752
864 May 25 23:38:25 www.blunix.com sshd[1612614]: Invalid user pi from 24.77.153.102 port 50518
865 May 25 23:38:25 www.blunix.com sshd[1612615]: Invalid user pi from 24.77.153.102 port 50524
866 May 25 23:48:24 www.blunix.com sshd[1612624]: Invalid user telecomadmin from 194.169.175.36 port 21468
</code></pre>

As you can see, this happens once or twice a day ;-)

You can see from the log lines that multiple attempts originate from the same IP addresses. Failed SSH login attempts cause negligable load on the server. It is completely safe to ignore that. It also doesn't hurt to install fail2ban to block those of course. Having fail2ban installed might have one upside - there might be an SSH daemon exploit that is not publicly known and fixed that requires an attacker to attempt to login multiple times. By banning IPs with multiple failed login attempts, this might prevent such an exploit from being successful. However an attacker could of course utilize multiple IP addresses. However if I wouldn't include fail2ban here, this blog post might feel a bit incomplete. Hence, here is how to install and configure fail2ban to protect the SSH daemon:

<pre class="command-line language-bash" data-output="2,4-6,8,10-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
apt-get install fail2ban

systemctl enable fail2ban.service
Synchronizing state of fail2ban.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable fail2ban

systemctl restart fail2ban.service

systemctl status fail2ban.service
● fail2ban.service - Fail2Ban Service
     Loaded: loaded (/lib/systemd/system/fail2ban.service; enabled; preset: enabled)
     Active: active (running) since Sun 2024-05-26 07:43:39 CEST; 3s ago
       Docs: man:fail2ban(1)
   Main PID: 1616302 (fail2ban-server)
      Tasks: 5 (limit: 2244)
     Memory: 28.3M
        CPU: 313ms
     CGroup: /system.slice/fail2ban.service
             └─1616302 /usr/bin/python3 /usr/bin/fail2ban-server -xf start

May 26 07:43:39 www.blunix.com systemd[1]: Started fail2ban.service - Fail2Ban Service.
May 26 07:43:39 www.blunix.com fail2ban-server[1616302]: 2024-05-26 07:43:39,686 fail2ban.configreader   [1616302]: WARNING &#x27;allowipv6&#x27; not defined in &#x27;Definition&#x27;. Using default one: &#x27;auto&#x27;
May 26 07:43:39 www.blunix.com fail2ban-server[1616302]: Server ready
</code></pre>

Thats all there is to using fail2ban to block repeated failed ssh authentication attempts. The ssh jail is enabled by default. fail2ban will now monitor the file `/var/log/auth.log `for failed login attempts and ban IPs for multiple failed attempts.

Here are the results after 28 hours:

<pre class="command-line language-bash" data-output="2-3,5-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
systemctl status fail2ban | grep Active
     Active: active (running) since Sun 2024-05-26 07:43:39 CEST; 1 day 4h ago

fail2ban-client status sshd
Status for the jail: sshd
|- Filter
|  |- Currently failed: 1
|  |- Total failed: 180
|  `- File list:    /var/log/auth.log
`- Actions
   |- Currently banned: 0
   |- Total banned: 16
</code></pre>

For additional information on configuring fail2ban and how it works refer to the [linux handbook article about fail2ban](https://linuxhandbook.com/fail2ban-basic/ "linuxhandbook.com: fail2ban basics") and [the official fail2ban documentation](https://fail2ban.readthedocs.io/en/latest/ "fail2ban.readthedocs.io: fail2ban documentation").

## [Configuring Automatic and Unattended apt Package Upgrades](#configuring-automatic-and-unattended-apt-package-upgrades)

Installing general apt upgrades as well as security upgrades is generally not something that is done by hand, but instead automatically by installing and configuring the `unattended-upgrades `apt package.

### [Installing Unattended apt Upgrades](#installing-unattended-apt-upgrades)

In order to use unattended upgrades the following apt package has to be installed:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
apt install unattended-upgrades
</code></pre>

To enable them, issue the following command:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
dpkg-reconfigure unattended-upgrades
</code></pre>

This will prompt you with:

```
Applying updates on a frequent basis is an important part of keeping systems secure. By default, updates need to be applied manually using package management tools. Alternatively, you can choose to have this system automatically download and install important updates.

Automatically download and install stable updates?
<Yes>         <No>
```

Use the TAB key to select `<Yes> `and press ENTER. From now on all apt upgrades will be installed automatically. The configuration of this progress is described in the following sections.

### [How Systemd Triggers the Automatic Apt Upgrades](#how-systemd-triggers-the-automatic-apt-upgrades)

There are two steps to upgrading apt packages: `apt update `to download a list of packages that can be upgraded, and `apt upgrade `to actually install those upgrades. Both of these steps have their own individual systemd timers and associated systemd services.

Both Systemd timers and Systemd services are a bit complex, and apt's unattended upgrade mechanism certainly uses some of the more complex features of systemd timers and systemd services. It works like this in a nutshell: Systemd timers are kind of like cronjobs but with much more fine grained timing and triggering mechansisms. Systemd timers don't do anything themselves except trigger systemd services that have the same name as the systemd timer. These systemd services then, in this case, are not daemons of some sort (like apache2 webserver) but simply commands or, in this case, BASH scripts, that either `apt update `or `apt upgrade `- in a rather complex way.

Refer to [our very extensive blogpost about absolutely everything there is to know about systemd timers](/blog/ultimate-tutorial-about-systemd-timers.html "blunix.com: ultimate systemd timer tutorial") for additional information on systemd timers.

The following section gives an overview of what triggers the unattended-upgrades mechansism how, when and what it triggers.

#### [Systemd Timer and Systemd Service for apt update](#systemd-timer-and-systemd-service-for-apt-update)

For `apt update `this systemd timer is used:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
systemctl status apt-daily.timer
● apt-daily.timer - Daily apt download activities
     Loaded: loaded (/lib/systemd/system/apt-daily.timer; enabled; preset: enabled)
     Active: active (waiting) since Mon 2024-02-26 17:30:11 CET; 2 months 28 days ago
    Trigger: Sun 2024-05-26 19:36:19 CEST; 10h left
   Triggers: ● apt-daily.service
</code></pre>

Which by default triggers at 6am and 6pm:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
systemctl show apt-daily.timer --no-pager | grep TimersCalendar
TimersCalendar={ OnCalendar=*-*-* 06,18:00:00 ; next_elapse=Sun 2024-05-26 18:00:00 CEST }
</code></pre>

The systemd timer `apt-daily.timer `triggers the systemd service `apt-daily.service `:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
systemctl status apt-daily.service
○ apt-daily.service - Daily apt download activities
     Loaded: loaded (/lib/systemd/system/apt-daily.service; static)
     Active: inactive (dead) since Sun 2024-05-26 07:43:16 CEST; 54min ago
TriggeredBy: ● apt-daily.timer
       Docs: man:apt(8)
   Main PID: 1616140 (code=exited, status=0/SUCCESS)
        CPU: 584ms
</code></pre>

The systemd service `apt-daily.service `executes this command:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
systemctl show apt-daily.service --no-pager|grep ^ExecStart=
ExecStart={ path=/usr/lib/apt/apt.systemd.daily ; argv[]=/usr/lib/apt/apt.systemd.daily update ; ignore_errors=no ; start_time=[n/a] ; stop_time=[n/a] ; pid=0 ; code=(null) ; status=0/0 }
</code></pre>

Explaining what `/usr/lib/apt/apt.systemd.daily `does exactly would go beyond the aim of this blogpost. Simply said it does `apt update `;-)

#### [Systemd Timer and Systemd Service for apt upgrade](#systemd-timer-and-systemd-service-for-apt-upgrade)

In order to periodically run `apt upgrade `, the systemd timer `apt-daily-upgrade.timer `is used:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
systemctl status apt-daily-upgrade.timer
● apt-daily-upgrade.timer - Daily apt upgrade and clean activities
     Loaded: loaded (/lib/systemd/system/apt-daily-upgrade.timer; enabled; preset: enabled)
     Active: active (waiting) since Mon 2024-02-26 17:30:11 CET; 2 months 28 days ago
    Trigger: Mon 2024-05-27 06:07:58 CEST; 21h left
   Triggers: ● apt-daily-upgrade.service
</code></pre>

The systemd timer `apt-daily-upgrade.timer `triggers at 6am by default:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
systemctl show apt-daily-upgrade.timer --no-pager | grep TimersCalendar
TimersCalendar={ OnCalendar=*-*-* 06:00:00 ; next_elapse=Mon 2024-05-27 06:00:00 CEST }
</code></pre>

But only after the `apt-daily.timer `, which runs `apt update `, has finished executing:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
systemctl show apt-daily-upgrade.timer --no-pager | grep ^After
After=apt-daily.timer time-set.target time-sync.target -.mount sysinit.target
</code></pre>

The systemd timer `apt-daily-upgrade.timer `triggers the systemd service `apt-daily-upgrade.service `:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
systemctl status apt-daily-upgrade.service
○ apt-daily-upgrade.service - Daily apt upgrade and clean activities
     Loaded: loaded (/lib/systemd/system/apt-daily-upgrade.service; static)
     Active: inactive (dead) since Sun 2024-05-26 06:27:17 CEST; 2h 1min ago
TriggeredBy: ● apt-daily-upgrade.timer
       Docs: man:apt(8)
   Main PID: 1615272 (code=exited, status=0/SUCCESS)
        CPU: 1.666s
</code></pre>

Which executes this command:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
systemctl show apt-daily-upgrade.service --no-pager|grep ^ExecStart=
ExecStart={ path=/usr/lib/apt/apt.systemd.daily ; argv[]=/usr/lib/apt/apt.systemd.daily install ; ignore_errors=no ; start_time=[n/a] ; stop_time=[n/a] ; pid=0 ; code=(null) ; status=0/0 }
</code></pre>

Explaining what `/usr/lib/apt/apt.systemd.daily `does exactly would go beyond the aim of this blogpost. Simplified, by default it runs `apt upgrade `.

#### [Configuring the Interval at which Automatic Unattended apt Upgrades are Triggered](#configuring-the-interval-at-which-automatic-unattended-apt-upgrades-are-triggered)

As described above, if we want to configure the interval that `apt update `and `apt upgrade `(for security upgrades) runs, we have to adjust two systemd timers: `apt-daily.timer `and `apt-daily-upgrade.timer `respectively.

These are the defaults:

<pre class="command-line language-bash" data-output="2,4" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
systemctl show apt-daily.timer --no-pager | grep TimersCalendar
TimersCalendar={ OnCalendar=*-*-* 06,18:00:00 ; next_elapse=Sun 2024-05-26 18:00:00 CEST }

systemctl show apt-daily-upgrade.timer --no-pager | grep TimersCalendar
TimersCalendar={ OnCalendar=*-*-* 06:00:00 ; next_elapse=Mon 2024-05-27 06:00:00 CEST }
</code></pre>

If you are reasonably paranoid, you can run this once an hour in order to install apt security upgrades more often. For most common systems, this is not required. If you administrate a larger number of services and you want to increase this interval, consider running your own instance of [apt-cacher-ng, which caches apt packages for you](https://wiki.debian.org/AptCacherNg "wiki.debian.org: apt-cacher-ng documentation") in order to not annoy the apt repository servers provided by Debian. Many hosting providers also provide their own apt-cacher-ng instances (like hetzner.de) - in this case it might be ok.

You can edit the interval at which the apt timers run with the following command:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
systemctl edit apt-daily.timer
systemctl edit apt-daily-upgrade.timer
</code></pre>

This will open an editor that will create `/etc/systemd/system/apt-daily.timer.d/override.conf `and `/etc/systemd/system/apt-daily-upgrade.timer.d/override.conf `respectively. Paste the following configuration between the designated lines that are described in the file both of the commands will open:

```
[Timer]
OnCalendar=hourly
RandomizedDelaySec=1m
```

Systemd configuration file

As we have edited the systemd configuration, we have to reload systemd itself for the changes to take effect:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
systemctl daemon-reload
</code></pre>

Check if your changes took effect with the following commands - the `LEFT `time should be less than one hour.

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
systemctl list-timers apt-daily*
NEXT                         LEFT     LAST                         PASSED    UNIT                    ACTIVATES
Sun 2024-05-26 12:00:17 CEST 20s left Sun 2024-05-26 11:28:52 CEST 31min ago apt-daily.timer         apt-daily.service
Sun 2024-05-26 12:00:45 CEST 47s left Sun 2024-05-26 11:04:51 CEST 55min ago apt-daily-upgrade.timer apt-daily-upgrade.service
</code></pre>

As explained in the previous section, the systemd service calls the BASH script `/usr/lib/apt/apt.systemd.daily `. This script has a configuration file at `/etc/apt/apt.conf.d/20auto-upgrades `which contains by default:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
cat /etc/apt/apt.conf.d/20auto-upgrades
APT::Periodic::Update-Package-Lists &quot;1&quot;;
APT::Periodic::Unattended-Upgrade &quot;1&quot;;
</code></pre>

The `"1" `stands for the minimum interval for running in days. We have to change that into something that is less than an hour, otherwise the script `/usr/lib/apt/apt.systemd.daily `will run and exit successfully, but not actually do anything:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
cat &lt;&lt; EOF | tee /etc/apt/apt.conf.d/20auto-upgrades
APT::Periodic::Update-Package-Lists &quot;30m&quot;;
APT::Periodic::Unattended-Upgrade &quot;30m&quot;;
EOF
</code></pre>

The systemd journal logs written by unattended upgrades look like this:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
journalctl _COMM=systemd -n 6
May 26 12:00:22 www.blunix.com systemd[1]: Starting apt-daily.service - Daily apt download activities...
May 26 12:00:23 www.blunix.com systemd[1]: apt-daily.service: Deactivated successfully.
May 26 12:00:23 www.blunix.com systemd[1]: Finished apt-daily.service - Daily apt download activities.
May 26 12:01:00 www.blunix.com systemd[1]: Starting apt-daily-upgrade.service - Daily apt upgrade and clean activities...
May 26 12:01:01 www.blunix.com systemd[1]: apt-daily-upgrade.service: Deactivated successfully.
May 26 12:01:01 www.blunix.com systemd[1]: Finished apt-daily-upgrade.service - Daily apt upgrade and clean activities.
</code></pre>

Refer to [this stackoverflow thread](https://unix.stackexchange.com/a/541426 "unix.stackexchange.com: thread on unattended-upgrades configuration") as well as to the [Debian documentation on unattended-upgrades](https://wiki.debian.org/UnattendedUpgrades#Modifying_download_and_upgrade_schedules_.28on_systemd.29 "wiki.debian.org: unattended-upgrades configuration") for an equally confusing explanation ;-)

On a regular system there is no need to modify the interval at which unattended-upgrades runs. Once a day is enough in most common cases.

### [Fine-Grained Configuration of What to Install with Automatic apt Upgrades](#fine-grained-configuration-of-what-to-install-with-automatic-apt-upgrades)

You can configure which packages are to be installed using the configuration file `/etc/apt/apt.conf.d/50unattended-upgrades `:

```
Unattended-Upgrade::Origins-Pattern {
//      "origin=Debian,codename=${distro_codename}-updates";
//      "origin=Debian,codename=${distro_codename}-proposed-updates";
        "origin=Debian,codename=${distro_codename},label=Debian";
        "origin=Debian,codename=${distro_codename},label=Debian-Security";
        "origin=Debian,codename=${distro_codename}-security,label=Debian-Security";
}
```

In order to understand this file, lets also look at the default `/etc/apt/sources.list `:

```
deb http://deb.debian.org/debian/ bookworm main contrib non-free non-free-firmware
deb http://security.debian.org/debian-security bookworm-security main contrib non-free non-free-firmware
deb http://deb.debian.org/debian/ bookworm-updates main contrib non-free non-free-firmware
```

origin=Debian refers to the "Origin" key in the releases file of the repository:

<pre class="command-line language-bash" data-output="2,4" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
curl -s http://deb.debian.org/debian/dists/bookworm/Release | grep Origin
Origin: Debian

curl -s https://security.debian.org/debian-security/dists/bookworm-security/Release | grep Origin
Origin: Debian
</code></pre>

codename=${distro_codename}-updates refers to the "Codename" key in the releases file of the repository:

<pre class="command-line language-bash" data-output="2,4" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
curl -s http://deb.debian.org/debian/dists/bookworm/Release | grep Codename
Codename: bookworm

curl -s https://security.debian.org/debian-security/dists/bookworm-security/Release | grep Codename
Codename: bookworm-security
</code></pre>

label=Debian refers to the "Label" key in the releases file of the repository:

<pre class="command-line language-bash" data-output="2,4" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
curl -s http://deb.debian.org/debian/dists/bookworm/Release | grep -i label
Label: Debian

curl -s https://security.debian.org/debian-security/dists/bookworm-security/Release | grep Label
Label: Debian-Security
</code></pre>

### [Automatically Installing Security Upgrades for Additionally Configured Non-Debian Managed Apt Repositories](#automatically-installing-security-upgrades-for-additionally-configured-non-debian-managed-apt-repositories)

All packages that come from additionally configured apt sources list entries are not upgraded by default, you have to configure this manually.

If, for example, you setup the [sury PHP repository for debian](https://deb.sury.org/ "deb.sury.org: debian PHP package repository"), which allows you to install several specific PHP versions and PHP related packages, and you want to automatically upgrade all PHP packages from sury, you can use the following configuration.

First lets setup the sury repository:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
echo &quot;deb https://packages.sury.org/php/ $(lsb_release -sc) main&quot; | tee -a /etc/apt/sources.list.d/sury-php.list
wget -qO - https://packages.sury.org/php/apt.gpg | apt-key add -
apt update
</code></pre>

Next inspect the repository metadata:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
curl -s https://packages.sury.org/php/dists/$(lsb_release -sc)/Release
Origin: deb.sury.org
Suite: bookworm
Codename: bookworm
[...]
</code></pre>

Using this information, you can add a line to `/etc/apt/apt.conf.d/50unattended-upgrades `to make it look like this:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
grep -v &#x27;//&#x27; /etc/apt/apt.conf.d/50unattended-upgrades
Unattended-Upgrade::Origins-Pattern {
        &quot;origin=Debian,codename=${distro_codename},label=Debian&quot;;
        &quot;origin=Debian,codename=${distro_codename},label=Debian-Security&quot;;
        &quot;origin=Debian,codename=${distro_codename}-security,label=Debian-Security&quot;;
        &quot;origin=packages.sury.org,codename=${distro_codename}&quot;;
};
</code></pre>

To verify your configuration use the following command:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
unattended-upgrade --dry-run --debug
</code></pre>

### [Additional Configuration of Unattended Upgrades](#additional-configuration-of-unattended-upgrades)

Exclude packages from automatic upgrades:

```
Unattended-Upgrade::Package-Blacklist {
    "php"
}
```

Send an email to the administrator if there are problems:

```
Unattended-Upgrade::Mail "info@blunix.com";
Unattended-Upgrade::MailReport "only-on-error";
```

Automatically reboot the system if apt creates the file `/var/run/reboot-required `, for example after installing a new kernel version:

```
Unattended-Upgrade::Automatic-Reboot "true";
Unattended-Upgrade::Automatic-Reboot-Time "02:00"
```

Note that installing new kernels after a security patch is useless if you do not reboot the operating system. You can determine if a reboot is required by looking if this file `/var/run/reboot-required `is present - if it is absent, no reboot is required. The file `/var/run/reboot-required.pkgs `lists the packages that trigger this required reboot:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
cat /var/run/reboot-required.pkgs
linux-image-6.1.0-20-amd64
linux-image-6.1.0-21-amd64
</code></pre>

Log the output of apt to syslog - or systemd journald starting with Debian 12:

```
Unattended-Upgrade::SyslogEnable "true";
```

## [Securing Linux User and Linux Group Accounts](#securing-linux-user-and-linux-group-accounts)

There are things to consider and several settings that should be applied when securely creating new Linux users and groups.

### [Securely Creating Linux Users and Groups Intended for Programs and Tools](#securely-creating-linux-users-and-groups-intended-for-programs-and-tools)

A good example for the need for adding Linux users and groups is when running multiple websites with, for example, PHP. Each website is assigned its own Linux user and group.

To create a Linux user that is not intended to be used by a human but instead by a program like PHP-FPM, use the following command:

<pre class="command-line language-bash" data-output="9-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
adduser\
    --comment &quot;www.example.com PHP website&quot;\
    --uid 5000\
    --system\
    --group www-example-com\
    --home /var/www/www.example.com\
    --shell /bin/false\
    --disabled-password

info: Adding system user `www-example-com&#x27; (UID 5000) ...
info: Adding new group `www-example-com&#x27; (GID 5000) ...
info: Adding new user `www-example-com&#x27; (UID 5000) with group `www-example-com&#x27; ...
useradd warning: www-example-com&#x27;s uid 5000 is greater than SYS_UID_MAX 999
info: Creating home directory `/var/www/www.example.com&#x27; ...
</code></pre>

As you can see from the following command, a user named www-example-com with the user ID and group ID 5000 was created:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
id www-example-com
uid=5000(www-example-com) gid=5000(www-example-com) groups=5000(www-example-com)
</code></pre>

Along its corresponding home directory - note that `/var/www/ `itself is owned by www-data, as there is an nginx webserver installed on the system I am using to write this blogpost ;)

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="user@workstation:~$" data-user="user" data-host="server">
<code class="language-bash">
sudo ls -lah /var/www/www.example.com
total 8.0K
drwxr-x--- 2 www-example-com www-example-com 4.0K Jun  3 05:12 .
drwxr-xr-x 4 www-data        www-data        4.0K Jun  3 05:12 ..
</code></pre>

Lets go through the arguments used one by one:

--comment

This is simply a human readable explanation for what the user is used for. It is the fifth field in `/etc/passwd `. Examples:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
cut -d &#x27;:&#x27; -f 5 /etc/passwd
[...]
systemd Network Management
systemd Time Synchronization
DHCP Client Daemon,,,
systemd Resolver
usbmux daemon,,,
TPM software stack,,,
[...]
</code></pre>

--uid

Sets the Linu user ID as well as the group ID, which is a numerical representation for the user. For example, the user root always has the uid 0:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
grep ^root /etc/passwd | cut -d &#x27;:&#x27; -f 3
0
</code></pre>

If you create a larger number of Linux users, for example to host multiple websites with PHP, you will want to put some order into the UIDs and GIDs used. Regular user accounts typically start from UID 1000 upwards, with lower numbers (0-999) reserved for system and administrative accounts (e.g., root has UID 0). Similarly, GIDs follow a similar convention. When choosing UIDs and GIDs for new users or groups, it's essential to avoid conflicts by adhering to the standard conventions: selecting UIDs and GIDs above 1000 for regular users and groups unless there's a specific need to assign a different range.

UIDs and GIDs below 1000 should be reserved for packages installed via apt, for example www-data, which runs Nginx and Apache2, always has UID and GID 33.

--system

While not strictly required, this, by default, causes the user to be created to have no expiry date (which is the default anyways) and their UID is below 999 (which we override with --uid). In our case it also causes the `/etc/shadow `entry above to contain a `! `instead of the password, which means that no password is defined (not that there is no password, but that no password would work). Additionally, this cases adduser to take the username of the new user from the `--group www-example-com `argument.

--group www-example-com

This argument in combination with the `--system `arguments instructs the adduser command to create a group with the same name as the user we want to create create. This group will be assigned the same group ID as the user ID specified in `--uid `.

Note that this argument, although a bit confusing in this case, also tells `adduser `the name of the user we want to create. Commonly the name of the user that is to be created is the final argument to adduser. In this variant, this can be omitted.

Also note that the user- and groupname we give as argument here uses dashes (www-example-com), not dots (www.example.com). This is because the `adduser `and `addgroup `commands use the following regex to verify usernames. You can view this regex in the `/etc/adduser.conf `file - changing it is not recommended and might cause incompatibility issues with other tools and programs.

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
grep SYS_NAME_REGEX /etc/adduser.conf
#SYS_NAME_REGEX=&quot;^[A-Za-z_][-A-Za-z0-9_]*\$?$&quot;
</code></pre>

--home /var/www/www.example.com

Defines and creates the location of the home directory of the user. The directories contents are copied from `/etc/skel/ `, after which all files user and group ownerships below `/var/www/www.example.com `are changed to the user and group www-example-com, or more specifically to the user and group ID 5000.

--shell /bin/false

Defines the default shell of this user, for example:

<pre class="command-line language-bash" data-output="2,4" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
grep ^root /etc/passwd | cut -d &#x27;:&#x27; -f 7
/bin/bash

grep ^gdm /etc/passwd | cut -d &#x27;:&#x27; -f 7
/bin/false
</code></pre>

You can set this to `/bin/false `if you do not intend to use this user for human interaction but only to run a program, for example PHP-FPM. You can still open a shell for this user with the following command, which overrides the default shell defined:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
su -l www-data --shell=/bin/bash
</code></pre>

--disabled-password

Does not set a password for the user. Note that this does not set an empty password (login is possible without a password), but NO password (no password will work). This causes a `* `to be written instead of a password hash in the file `/etc/shadow `in case the user was created without `--system `, otherwise it writes a `! `instead. Examples:

<pre class="command-line language-bash" data-output="2,4" data-continuation-str="\" data-prompt="user@workstation:~$" data-user="user" data-host="server">
<code class="language-bash">
sudo grep ^root /etc/shadow | cut -d &#x27;:&#x27; -f 2
*

sudo grep ^syslog /etc/shadow | cut -d &#x27;:&#x27; -f 2
!
</code></pre>

### [Securely Creating Linux Users Intended for Humans](#securely-creating-linux-users-intended-for-humans)

Creating Linux users and groups intended to be used by humans is similar to creating Linux users and groups intended to be used by programs and tools. To add a Linux user for a human, use the following command:

<pre class="command-line language-bash" data-output="8-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
adduser\
    --comment &quot;john.doe@example.com&quot;\
    --uid 1010\
    --home /home/john.doe-example-com\
    --shell /bin/bash\
    --disabled-password\
    john-doe-example-com

info: Adding user `john-doe-example-com&#x27; ...
info: Adding new group `john-doe-example-com&#x27; (1010) ...
info: Adding new user `john-doe-example-com&#x27; (1010) with group `john-doe-example-com (1010)&#x27; ...
info: Creating home directory `/home/john.doe-example-com&#x27; ...
info: Copying files from `/etc/skel&#x27; ...
info: Adding new user `john-doe-example-com&#x27; to supplemental / extra groups `users&#x27; ...
info: Adding user `john-doe-example-com&#x27; to group `users&#x27; ...
</code></pre>

View the user and group ID:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
id john-doe-example-com
uid=1010(john-doe-example-com) gid=1010(john-doe-example-com) groups=1010(john-doe-example-com),100(users)
</code></pre>

Note that by default new non system users are automatically added to a group named `users `. This is because all newly created non system users are automatically added to the groups defined as `USERS_GROUP `in `/etc/adduser.conf `:

```
# Defines the groupname or GID of the group all newly-created
# non-system users are placed into.
# It is a configuration error to define both variables
# even if the values are consistent.
# Default: USERS_GID=undefined, USERS_GROUP=users
#USERS_GID=
#USERS_GROUP=users
```

There is no command line argument to disable this - there is an argument to ENABLE this, which is `--add-extra-groups `, but this is the default for `adduser `anyways. You can disable this behavior by defining the following in `/etc/adduser.conf `:

```
USERS_GID=-1
```

If you now create a new user, it will not be added to the supplemental / extra groups:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="user@workstation:~$" data-user="user" data-host="server">
<code class="language-bash">
sudo adduser --comment &quot;john.doe@example.com&quot; --uid 1010 --home /home/john-doe-example-com --shell /bin/bash --disabled-password john-doe-example-com
info: Adding user `john-doe-example-com&#x27; ...
info: Adding new group `john-doe-example-com&#x27; (1010) ...
info: Adding new user `john-doe-example-com&#x27; (1010) with group `john-doe-example-com (1010)&#x27; ...
info: Creating home directory `/home/john-doe-example-com&#x27; ...
info: Copying files from `/etc/skel&#x27; ...
</code></pre>

View the groups the user is in:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
id john-doe-example-com
uid=1010(john-doe-example-com) gid=1010(john-doe-example-com) groups=1010(john-doe-example-com)
</code></pre>

### [Configuring Remote SSH Login Access for Linux Users](#configuring-remote-ssh-login-access-for-linux-users)

Linux users for humans as well as Linux users intented for programs often need to be reachable via SSH. By default, all Linux users can be logged in to via SSH. It is recommended to setup a group to restrict the users that can be reachable via SSH. This group is then defined in the sshd config file.

Add one, or if it serves organizational purpuses multiple Linux groups like this:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
addgroup\
    --gid 5000\
    ssh_users
</code></pre>

Then add members to the group:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
usermod -a -G ssh_users john-doe-example-com
</code></pre>

To verify that the user is now in the group:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
id john-doe-example-com
uid=1010(john-doe-example-com) gid=1010(john-doe-example-com) groups=1010(john-doe-example-com),5000(ssh_users)
</code></pre>

You can now configure the SSH daemon config file to only allow logins from Linux users that are in specific groups:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
cat &lt;&lt; EOF | tee -a /etc/ssh/sshd_config.d/99-custom.conf
AllowGroups root admins
EOF
</code></pre>

Verify the new configuration - note that in the output of `sshd -T `the configuration options are not spelled CamelCase but lowercase:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
sshd -T | grep allowgroups
allowgroups root
allowgroups admins
</code></pre>

Restart the systemd service to apply the changes:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
systemctl restart sshd.service
</code></pre>

### [Adding Linux Users to Existing Groups](#adding-linux-users-to-existing-groups)

To add a Linux user to an existing group use the following command:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
usermod -a -G existing-group existing-user
</code></pre>

## [Using the UMASK to Create Files and Directories with Secure Permissions](#using-the-umask-to-create-files-and-directories-with-secure-permissions)

Even though there is often only one person administrating or using a Linux system, by design Debian Linux is a multi user operating system. Different Linux users, like www-data, are used to run programs like Apache2 or Nginx with only the neccessary privileges, but should be unable to access the private files of other Linux users.

Let's explore what steps we can take to remove as much privileges as possible from Linux users, so that if a program or Linux users SSH private key is compromised, the spread of the damage can be minimized and the user can not view, modify or execute files it was not intended for.

### [What is the umask](#what-is-the-umask)

The umask, or user file creation mask, defines what default permissions all files and directories have that this user creates. On Debian Linux, all files created by a user are commonly world readable. Example:

<pre class="command-line language-bash" data-output="2,4" data-continuation-str="\" data-prompt="user@workstation:~$" data-user="user" data-host="server">
<code class="language-bash">
touch testfile.txt

ls -lah testfile.txt
-rw-rw-r-- 1 myuser myuser 0 May 27 23:04 testfile.txt
</code></pre>

As you can see, the permissions for this file are `-rw-rw-r-- `. Depending on the directory it is located in, other users can read that. This is of course not what we want.

A complete explanation of the umask is out of scope for this blogpost. If this is the first time you hear the word umask, please refer to [this tutorial on geeksforgeeks.org about the Linux umask](https://www.geeksforgeeks.org/umask-command-in-linux-with-examples/ "www.geeksforgeeks.org: umask tutorial") that explains everything very nicely, and then return to this blogpost.

### [Setting a Temporary umask for the Current Shell](#setting-a-temporary-umask-for-the-current-shell)

You can view the current umask with the `umask `command:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="user@workstation:~$" data-user="user" data-host="server">
<code class="language-bash">
umask
0002
</code></pre>

To define a new umask for the current shell, also use the `umask `command:

<pre class="command-line language-bash" data-output="2,4,6-99" data-continuation-str="\" data-prompt="user@workstation:~$" data-user="user" data-host="server">
<code class="language-bash">
umask 0007

touch testfile.txt

ls -lah testfile.txt
-rw-rw---- 1 myuser myuser 0 May 27 23:11 testfile.txt
</code></pre>

As you can see, the file created now is not world readable anymore. Even if another user has access to the directory it is stored in, and provided the other user is not in the `myuser `group, he is not able to read the file, write to it or execute it.

### [Setting a Permanent Umask for Your Default Shell](#setting-a-permanent-umask-for-your-default-shell)

The umask setting has to be defined for every shell you use - that is commonly `/bin/bash `or `/bin/zsh `, but may differ depending on your preference. Each of these shells source one or more dotfiles when you start it. In the case of `/bin/bash `, that would be `~/.bashrc `:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-prompt="user@workstation:~$" data-user="user" data-host="server">
<code class="language-bash">
echo umask 027 | tee -a ~/.bashrc
</code></pre>

Now open a new bash by simply running `bash `:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-prompt="user@workstation:~$" data-user="user" data-host="server">
<code class="language-bash">
bash
</code></pre>

And create a new file:

<pre class="command-line language-bash" data-output="2,4" data-continuation-str="\" data-prompt="user@workstation:~$" data-user="user" data-host="server">
<code class="language-bash">
touch testfile.txt

ls -lha testfile.txt
-rw-r----- 1 user user 0 May 27 23:36 testfile.txt
</code></pre>

You can determine the default shell that is opened when a user logs in via SSH with the following command:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
grep ^root /etc/passwd | cut -d &#x27;:&#x27; -f 7
/bin/bash
</code></pre>

### [Security Preperations Before Creating new Linux Users](#security-preperations-before-creating-new-linux-users)

When creating new users, the umask setting also has to be kept in mind, so that the new home directory and all files it contains have the right permissions right away. The file `/etc/login.defs `has a setting that defines the umask with which the new home directory for users is created. By default this is:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
grep ^UMASK /etc/login.defs
UMASK       022
</code></pre>

which creates a `/home/ `directory like this:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
ls -lha /home/
total 16K
drwxr-xr-x  4 root root 4.0K May  1 19:44 .
drwxr-xr-x 23 root root 4.0K May  1 19:22 ..
drwxr-x--- 54 user user 4.0K May 27 23:42 user
</code></pre>

And copies the files from `/etc/skel `, which in `/etc/skel `have the following permissions:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
ls -lha /etc/skel/
total 28K
drwxr-xr-x   2 root root 4.0K Apr 24 06:47 .
drwxr-xr-x 158 root root  12K May 27 23:29 ..
-rw-r--r--   1 root root  220 Mar 31 04:41 .bash_logout
-rw-r--r--   1 root root 3.7K Mar 31 04:41 .bashrc
-rw-r--r--   1 root root  807 Mar 31 04:41 .profile
</code></pre>

with the following permissions:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="user@workstation:~$" data-user="user" data-host="server">
<code class="language-bash">
ls -lha ~/.bash_logout ~/.bashrc ~/.profile
-rw-r----- 1 user user  220 May  1 19:29 .bash_logout
-rw-r----- 1 user user 4.1K May 27 23:36 .bashrc
-rw-r----- 1 user user  664 May 27 23:34 .profile
</code></pre>

This is generally secure enough. You can set the umask in `/etc/login.defs `to 027 if you do not want the files to be group readable.

If you want newly created users to automatically have `umask 027 `in their `~/.bashrc `file, you can do this by editing the `/etc/skel/.bashrc `file before creating the user:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
echo umask 027 | tee -a /etc/skel/.bashrc
</code></pre>

### [Setting the umask for a Linux User that Runs a Specific Program](#setting-the-umask-for-a-linux-user-that-runs-a-specific-program)

Pretty much all programs you will run as daemons, like PHP-FPM, are managed via systemd on modern Debian Linux systems. If those programs, in this example PHP-FPM, generate files, you most likely want those files to not be word-readable. For example your application could generate .pdf files or users may upload images - these files should not be created with world readable permissions. For this you have to define a umask setting in the systemd service file that runs the program in question.

For example to set a umask for a PHP-FPM process managed by a systemd service unit, use the following command:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
systemctl edit php8.3-fpm.service
</code></pre>

Add this configuration:

```
[Service]
UMask=0007
```

Systemd configuration file

To apply the changes reload systemd and restart the systemd service:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
systemctl daemon-reload
systemctl restart php8.3-fpm.service
</code></pre>

## [Using Sudo to Configure Selectively Elevating Linux User Permissions](#using-sudo-to-configure-selectively-elevating-linux-user-permissions)

The `sudo `command is designed to allow for grant fine grained access to specific commands.

### [Granting Passwordless sudo Privileges to Humans](#granting-passwordless-sudo-privileges-to-humans)

The most common use of the command is often `sudo -i `or `sudo su `, which however is not really the most intended usecase for the sudo command. If you grant a user passwordless sudo permissions, from a security point of view there is no real difference to adding the users SSH public key to `/root/.ssh/authorized_keys `directly. The only advantage is that you can see in the logs which user logged in via SSH, and then used sudo to elevate his privileges to root. If you want to grant root permissions on servers to humans, this is a good way to keep track on who logged in when.

You can give a user passwordless sudo by defining that in `/etc/sudoers `like this:

```
username ALL=(ALL) NOPASSWD:ALL
```

Or for a Linux group like this - notice the `% `character before the name of the Linux group, which specifies that it is a group name and not a user name:

```
%groupname ALL=(ALL) NOPASSWD:ALL
```

To view which user logged in via SSH:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
journalctl _COMM=sshd -f
Jun 04 14:19:29 www.blunix.com sshd[1685548]: Accepted publickey for john-doe from 1.2.3.4 port 47314 ssh2: ED25519 SHA256:xT2+lV8yBBm7c/c+o29YGNsHFTUvsW+/j5uaLzIKg+M
Jun 04 14:19:29 www.blunix.com sshd[1685548]: pam_unix(sshd:session): session opened for user john-doe(uid=5010) by (uid=0)
Jun 04 14:19:28 www.blunix.com sshd[1684952]: pam_unix(sshd:session): session closed for user john-doe
</code></pre>

### [Granting Password-Protected sudo Privileges to Humans](#granting-password-protected-sudo-privileges-to-humans)

You can use a password to protect the sudo privilege escalation with a configuration in `/etc/sudoers `like this:

```
username ALL=(ALL:ALL) ALL
```

Or for a group:

```
%groupname ALL=(ALL:ALL) ALL
```

If you created a Linux user like described above, they will not have a password defined in `/etc/shadow `and this will not work. You can set a password for a Linux user like this:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
passwd john-doe
New password:
Retype new password:
passwd: password updated successfully
</code></pre>

The user will have to enter their Linux user password the first time before they use sudo, after this it will be cached for 15 minutes by default. Refer to `man sudoers `and look for "timestamp_timeout", quote:

"Number of minutes that can elapse before sudo will ask for a password again. The timeout may include a fractional component if minute granularity is insufficient, for example 2.5. The default is 15. Set this to 0 to always prompt for a password. If set to a value less than 0 the users time stamp will not expire until the system is rebooted. This can be used to allow users to create or delete their own time stamps via `sudo -v `and `sudo -k `respectively."

This primarily protects you from [evil maid attacks](https://en.wikipedia.org/wiki/Evil_maid_attack "wikipedia.org: evil maid attack") where you leave your workstation unattended and while leaving it is turned on and the screen is not password-locked by a screensaver.

If your goal is to grant root@ access to servers to humans, there is not much sense in using a password for sudo. The user is required to store her/his SSH private key securely. If the private key is compromised, this is most likely due to his workstation being compromised, which means that the passwords stored in its password store are most likely also compromised or, if the user remembers his password instead of storing it in a password store, it will be discovered using a keylogger.

Use [Qubes OS](https://www.qubes-os.org/ "www.qubes-os.org: reasonably secure linux OS") for your workstation if you have reasons to be paranoid.

### [Granting sudo Privileges to Specific Commands Only](#granting-sudo-privileges-to-specific-commands-only)

It often makes sense to only allow Linux users access to specific commands that require elevated permissions. Here are ten examples for configuring that in `/etc/sudoers `. Note the use of absolute paths. To determine the absolute path for a command, use the `which `like this:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
which systemctl
/usr/bin/systemctl
</code></pre>

Granting a user permission to restart the Apache server:

```
john-doe ALL=(ALL) NOPASSWD: /bin/systemctl restart apache2
```

Allowing a user to update the system packages:

```
john-doe ALL=(ALL) NOPASSWD: /usr/bin/apt-get update, /usr/bin/apt-get upgrade
```

Permitting a user to edit a specific file with nano:

```
john-doe ALL=(ALL) NOPASSWD: /bin/nano /etc/hosts
```

Allowing a user to restart the network service:

```
john-doe ALL=(ALL) NOPASSWD: /bin/systemctl restart networking
```

Allowing a user to reboot the system:

```
john-doe ALL=(ALL) NOPASSWD: /bin/systemctl reboot
```

Allowing a user to mount a specific partition:

```
john-doe ALL=(ALL) NOPASSWD: /bin/mount /dev/sdb1 /mnt/backup
```

Permitting a user to run a specific script:

```
john-doe ALL=(ALL) NOPASSWD: /home/jane-doe/scripts/backup.sh
```

Granting a user permission to change ownership of a specific directory:

```
john-doe ALL=(ALL) NOPASSWD: /bin/chown -R www-data:www-data /var/www/html
```

Allowing a user to start and stop a docker container:

```
john-doe ALL=(ALL) NOPASSWD: /usr/bin/docker start mycontainer, /usr/bin/docker stop mycontainer
```

Allowing a user to tail a specific logfile:

```
john-doe ALL=(ALL) NOPASSWD: /usr/bin/tail -f /var/log/nginx/access.log
```

## [Debian Linux Server Network Firewall Security Basics](#debian-linux-server-network-firewall-security-basics)

If you click a new cloud (or physical server), setup SSH keypair authentication for the root user and disable password authentication in the sshd config file, and configure unattended upgrades as well as reboots, you can safely have that server accessible from the internet with a public IP address. Bots will try to test if they can login via SSH with weak passwords, but that is irrelevant and can be considered "rain hitting the windshield of your car". It is normal and not a security risk that has to be addressed in any way for servers to do not run high risk applications.

The interesting question is what you do next with that server, after you configured the security basics described above.

### [What Do Firewalls for Filtering Incoming Traffic Actually Do](#what-do-firewalls-for-filtering-incoming-traffic-actually-do)

Here is a very common example for cases in which a firewall is required. An admin installs a mariadb-server and configures it to listen on 0.0.0.0:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
apt install mariadb-server
sed -i &#x27;s/^bind-address.*/bind-address = 0.0.0.0/g&#x27; /etc/mysql/mariadb.conf.d/50-server.cnf
systemctl restart mariadb.service
</code></pre>

The mariadb-server now listens on 0.0.0.0:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
lsof -i :3306
COMMAND      PID  USER   FD   TYPE   DEVICE SIZE/OFF NODE NAME
mariadbd 1684019 mysql   21u  IPv4 28054081      0t0  TCP *:mysql (LISTEN)
</code></pre>

Which means that it can be accessed from the internet:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="user@workstation:~$">
<code class="language-bash">
telnet www.blunix.com 3306
Trying 5.6.7.8...
Connected to www.blunix.com.
Escape character is &#x27;^]&#x27;.
HHost &#x27;1.2.3.4&#x27; is not allowed to connect to this MariaDB serverConnection closed by foreign host.
</code></pre>

In this example there are no GRANTs defined in the MariaDB server to allow anyone to login remotely. There however is no need to trust the code of MariaDB to filter this. Only IP addresses that are supposed to access MariaDB should be allowed to access MariaDB.

In some cases it might be required for MariaDB to be configured to listen on multitple IP addresses or network interfaces. [It is sadly not possible to configure this](https://www.cyberciti.biz/faq/unix-linux-mysqld-server-bind-to-more-than-one-ip-address/ "cyberciti.biz: configuring more than one IP address for MySQL"). Hence, the only option in this case is to configure MariaDB to listen to 0.0.0.0 and use a firewall for incoming connections to filter who is allowed to access it and who is not.

Note that firewalls can have configuration errors and might not start, for example after reboots. You should run regular portscans with your monitoring system to verify that your firewall is working as expected.

In addition to that, humans (administrators) make mistakes, and an inexperienced admin might configure a service to listen to 0.0.0.0 by accident. When your MySQL server is reachable on port 3306 (mysql) from the public internet, much like with port 22, automated bots will soon find this out through portscans and start to attempt to gain access or exploit the service that is publicly reachable. Having SSH publicly reachable is (kinda) ok and often required. With MySQL, or anything else other than a webserver, that is most likely not ok.

This is why firewalls for filtering incoming connections exist.

#### [Verifying Your Firewall Configuration for Filtering Incoming Traffic](#verifying-your-firewall-configuration-for-filtering-incoming-traffic)

The best way to verify your configuration is to run a port scan using tools like `nmap `to see which ports are open to the public. Note that scanning all ports from 0-65535 is rather time consuming and only serves illustrational purposes here. For a good and [short introduction to nmap, refer to this article in linuxhandbook.com](https://linuxhandbook.com/nmap-scan-ports/ "linuxhandbook.com: nmap tutorial").

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="user@workstation:~$">
<code class="language-bash">
nmap -p 0-65535 scanme.nmap.org
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-06-04 11:03 EDT
Nmap scan report for scanme.nmap.org (45.33.32.156)
Host is up (0.24s latency).
Other addresses for scanme.nmap.org (not scanned): 2600:3c01::f03c:91ff:fe18:bb2f
Not shown: 65524 closed tcp ports (reset)
PORT      STATE    SERVICE
22/tcp    open     ssh
25/tcp    filtered smtp
53/tcp    open     domain
80/tcp    open     http
137/tcp   filtered netbios-ns
138/tcp   filtered netbios-dgm
139/tcp   filtered netbios-ssn
445/tcp   filtered microsoft-ds
1900/tcp  filtered upnp
2869/tcp  filtered icslap
9929/tcp  open     nping-echo
31337/tcp open     Elite

Nmap done: 1 IP address (1 host up) scanned in 1292.51 seconds
</code></pre>

#### [Advanced Methods of Scanning for Security Issues](#advanced-methods-of-scanning-for-security-issues)

Even though this is out of scope for this blogpost, the [vulnerability scanner greenbone](https://www.greenbone.net/en/ "www.greenbone.net: vulnerability scanner") deserves an honorable mention. It allows you to scan your applications for known vulnerabilities (automatically).

### [What Do Firewalls for Filtering Outgoing Traffic Actually Do](#what-do-firewalls-for-filtering-outgoing-traffic-actually-do)

There are two reasons to configure a firewall to filter outgoing traffic - to block traffic to specific servers, or to block traffic to all but a list of specific server and logging all other(blocked) attempts to trigger monitoring alerts in the assumption that the server has been compromised and some malware is trying to call home.

#### [Blacklisting Destinations with a Firewall for Outoing Traffic](#blacklisting-destinations-with-a-firewall-for-outoing-traffic)

The first reason to filter outgoing traffic can be that you do not trust all software installed on a workstation or server to not contact destinations you do not want to send data to. A common example would be to block all facebook services on your workstation. This is commonly done through both a firewall as well as a blacklist for DNS.

If you want to block facebook services on your workstation, you can find lists on github that collect all servers associated with facebook and use a tool like [dnscrypt-proxy](https://github.com/DNSCrypt/dnscrypt-proxy "github.com: dnscrypt-proxy") to block all DNS requests for those domains. This way, if your browser opens a website that wants to send tracking information to facebook, it will not be able to resolve those domains and hence not know where to send this data. This is what most VPN providers do when you select "block tracking" in the VPN clients settings - they give you a DNS resolver that blocks those domains.

Another way to do this is to resolve all those domains yourself to get a list of IPs or download a list of IPs directly from sources like github. You can then configure your firewall to block all connections to those IPs.

#### [Whitelisting Destinations with a Firewall for Outoing Traffic](#whitelisting-destinations-with-a-firewall-for-outoing-traffic)

If you know exactly what services your server needs to connect to in order to function properly you can whitelist those. This is what we do at Blunix for our hosting customers. It has the advantage that if malicious software is installed and tries to connect to a command and control server or similar, this is blocked and logged by the firewall, which will trigger an alert in a SIEM (Security Information & Event Management) monitoring system.

For a regular debian server, you will need to whitelist at least the following outgoing connections to specific IPs in order for it to function properly:

- DNS to resolve domains, tcp/udp port 53
- NTP to sync the time, udp port 123
- HKP to download GPG keys that sign apt packages, tcp port 11371
- HTTP (and possibly HTTPS) to apt servers, tcp port 80 and possibly 443
- DHCP, udp ports 67 and 68

Depending on the applications you run on your server, for example if you have a more complex webapplication that calls third party APIs in order to function, the list gets longer.

### [Common Mistake: Do Not Forget About Firewalling Your IPv6 Addresses](#common-mistake-do-not-forget-about-firewalling-your-ipv6-addresses)

A common mistake is to forget about IPv6. Many cloud providers assign IPv6 addresses by default now, however especially iptables only handles IPv4 traffic, and the ip6tables command handles IPv6 traffic.

### [What Firewall Wrapper or Tool to Choose](#what-firewall-wrapper-or-tool-to-choose)

With recent Debian versions, [IPtables](https://www.netfilter.org/ "netfilter.org: iptables") is deprecated in favor of [NFtables](https://www.nftables.org/ "nftables.org: nftables"). Explaining nftables is out of the scope for this blogpost and there are several very good tutorials on the internet already.

It is generally a good idea to choose a wrapper program that does most of the work for you when configuring a firewall, like providing templates to "allow incoming HTTP(S)" as well as configuring zones and so on. For iptables we (blunix) used [shorewall](https://shorewall.org/ "shorewall.org: iptables wrapper") for many years and we were very happy with it. Sadly shorewall has no plans on migrating to nftables. At the time of this writing, I have not found a satisfying wrapper for nftables, hence I recommend to just write nftables rules "by hand".

If you want a firewall that is simple to configure I would recommend [UFW - the uncomplicated firewall](https://en.wikipedia.org/wiki/Uncomplicated_Firewall "wikipedia.org: ufw uncomplicated firewall"), which is a firewall wrapper that can use both nftables and the deprecated iptables as backend.

For using more advanced features you will not get around learning nftables itself. It is not to complicated though and you should give it a shot.

### [Best Possible Network Security for Company-Internally Used Tools](#best-possible-network-security-for-company-internally-used-tools)

If you are setting up a tool that is only supposed to be used company-internally, like gitlab, an online password store, a wiki or a ticket management tool, there is no reason that everyone on the planet can access the login page of that tool.

The correct approach for this is to have a company-internal VPN solution like wireguard. All your employees devices then connect to the this wireguard, which either connects to an internal wireguard mesh network which connects to all your company-internal servers, or serves as a default gateway for traffic to the entire internet or specific IPs (like your internal servers). This way, you can setup the firewall on your internally used servers to only accept connections on tcp port 443 (HTTPS) from the IP of your VPN server.

It is always better to restrict incoming connections to specific IPs instead of coding a super secure login page for your application, or trusting the login page of open source applications like gitlab.

## [Security Basics for Webservers: Nginx and Apache2](#security-basics-for-webservers-nginx-and-apache2)

Running "a website" is most likely the most common usecase for most Debian Linux servers, with Nginx and Apache2 most likely being the most commonly used webservers for this.

Both Nginx and Apache come reasonably securely configured by default and only require a small amount of additional configuration for extra security.

### [Checking Response Headers for Leaking Information](#checking-response-headers-for-leaking-information)

You should make sure your webserver, or the underlying application it serves, does not leak any internally used headers that are of no legitimate interest to the enduser. A common example for this is the `x-powered-by `header commonly set by PHP:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="user@workstation:~$">
<code class="language-bash">
curl -I -L https://www.example.com
[...]
x-powered-by: PHP/8.2.1
[...]
</code></pre>

Disclosing this information to endusers servces no purpose and only helps hackers gain additional information about your system. Many tools and programs leak version information in response headers by default. Use `curl -I -L https://www.your-website.com `to check for those and then use your favorite search engine to determine how to disable those.

### [Using Properly Configured SSL](#using-properly-configured-ssl)

When configuring SSL for Nginx or Apache, you should use certificates from [Letsencrypt](https://letsencrypt.org/ "letsencrypt.org: free SSL certificates") and then use the [Mozilla SSL Configuration Generator](https://ssl-config.mozilla.org/ "ssl-config.mozilla.org: secure SSL configuration generator for webservers") to generate secure SSL configuration snippets for your webserver. You will need the following information for this:

**OpenSSL Version**

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
openssl version
OpenSSL 3.0.11 19 Sep 2023 (Library: OpenSSL 3.0.11 19 Sep 2023)
</code></pre>

**Nginx Version**

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
nginx -v
nginx version: nginx/1.22.1
</code></pre>

**Apache2 Version**

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
apache2ctl -v
Server version: Apache/2.4.59 (Debian)
Server built:   2024-04-05T12:08:04
</code></pre>

## [Physical Servers: Unattended Updates for Firmwares for the BIOS / UEFI and Other Physical Components like SSDs](#physical-servers-unattended-updates-for-firmwares-for-the-bios-uefi-and-other-physical-components-like-ssds)

This is only relevant if you administrate a physical server. For cloud servers, your IaaS provider is responsible for keeping the physical servers up to date that run the cloud virtual machines.

On physical Debian Linux servers, you should install and configure the `fwupd `package. From the [project website of fwupd](https://github.com/fwupd/fwupd "github.com: fwupd"): It "makes updating firmware on Linux automatic, safe, and reliable." Refer to the [basic usage documentation](https://github.com/fwupd/fwupd?tab=readme-ov-file#basic-usage-flow-command-line "github.com: fwupd basic usage") for details on how to use it.

`fwupd `will regularly be executed by a systemd timer which is executed daily at 6am and 6pm:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@server:~#">
<code class="language-bash">
systemctl cat fwupd-refresh.timer
# /lib/systemd/system/fwupd-refresh.timer
[Unit]
Description=Refresh fwupd metadata regularly
ConditionVirtualization=!container

[Timer]
OnCalendar=*-*-* 6,18:00
RandomizedDelaySec=12h
Persistent=true

[Install]
WantedBy=timers.target
</code></pre>

## [Additional Security Considerations](#additional-security-considerations)

This blogpost was designed to target basic security measueres that you should apply to every Debian Linux server you run. It is generally not required to go further unless you are technically curious or run a high risk service.

Here are some things you can do in addition to the basic security measures described above.

- Read [our blogpost on central logfiles with systemd-journal-remote](/blog/centralized-logging-server-on-linux-with-systemd-journal-remote.html "blunix.com: blogpost on central logging with systemd-journal-remote"). If your server gets compromised, the attacker will not be able to delete logfiles written before he gained full control of the server, which may help in determining how the attack was carried out.
- Setup a monitoring system (we like [prometheus](https://prometheus.io/ "prometheus.io: very nice monitoring system")). This can help you to detect load spikes, which can be caused by malware such as crypto miners.
- Consider reading the [debian documentation for intrusion detection systems](https://www.debian.org/doc/manuals/securing-debian-manual/intrusion-detect.en.html "debian.org: intrusion detection manual").
- Run regular external security scans, for example with [greenbone](https://www.greenbone.net/en/ "greenbone.net: vulnerability scanner").
- Read [our blogpost on setting up borgbackup2 as backup server](/blog/howto-setup-borgbackup2-on-hetzner-cloud.html "blunix.com: blogpost about borgbackup2 backup tool"), so that you can restore a backup from before your machine was compromised.
- Create an incidence response plan so you do not have to search online what to do when your machine is compromised.
- Use security auditing tools like [lynis](https://github.com/CISOfy/lynis "github.com: lynis system security audit tool") to identify additional ways to enhance your servers security.
- Read [the debian documentation for securing a debian server](https://www.debian.org/doc/manuals/securing-debian-manual/ "debian.org: securing debian manual").
- Save [https://www.blunix.com/#contact](/#contact) in your browsers bookmarks and write down our emergency support number ;-)
