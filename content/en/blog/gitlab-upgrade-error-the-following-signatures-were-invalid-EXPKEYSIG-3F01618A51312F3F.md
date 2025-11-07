---
title: "Gitlab upgrade signature invalid EXPKEYSIG 3F01618A51312F3F"
description: "This blog post explains the Gitlab Upgrade Error The following signatures were invalid: EXPKEYSIG 3F01618A51312F3F and how to fix it"
date: 2024-03-27
image: "/images/blog/gitlab-logo.webp"
image_alt: "Gitlab Upgrade Error: The following signatures were invalid: EXPKEYSIG 3F01618A51312F3F"
---

Please [contact us](/index.html#contact "Blunix GmbH contact options") if anything is not clearly described, does not work, seems incorrect or if you require support.

## Table of contents

- [The Problem: Expired apt-key Signature Problem while Upgrading Gitlab](#problem)
- [The Problems Explanation: Why APT Keys Expire](#explanation)
- [The Solution to the Problem: Updating the Archive Keyring with the New GPG Key](#solution)

## [The Problem: Expired apt-key Signature Problem while Upgrading Gitlab](#problem)

We encountered the following error message while upgrading our Gitlab server git.blunix.com:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@git.blunix.com:~#">
<code class="language-bash">apt update
Hit:1 http://mirror.hetzner.com/debian/packages bookworm InRelease
Hit:2 http://deb.debian.org/debian bookworm InRelease                                            
Get:3 http://deb.debian.org/debian bookworm-updates InRelease [55,4 kB]                          
Hit:4 http://security.debian.org/debian-security bookworm-security InRelease                     
Get:5 http://mirror.hetzner.com/debian/packages bookworm-updates InRelease [55,4 kB]             
Get:6 http://mirror.hetzner.com/debian/security bookworm-security InRelease [48,0 kB]            
Get:7 https://packages.gitlab.com/gitlab/gitlab-ce/debian bookworm InRelease [23,3 kB]           
Err:7 https://packages.gitlab.com/gitlab/gitlab-ce/debian bookworm InRelease
  The following signatures were invalid: EXPKEYSIG 3F01618A51312F3F GitLab B.V. (package repository signing key) 
Fetched 182 kB in 1s (162 kB/s)
Reading package lists... Done
W: An error occurred during the signature verification. The repository is not updated and the previous index files will be used. GPG error: https://packages.gitlab.com/gitlab/gitlab-ce/debian bookworm InRelease: The following signatures were invalid: EXPKEYSIG 3F01618A51312F3F GitLab B.V. (package repository signing key) 
W: Failed to fetch https://packages.gitlab.com/gitlab/gitlab-ce/debian/dists/bookworm/InRelease  The following signatures were invalid: EXPKEYSIG 3F01618A51312F3F GitLab B.V. (package repository signing key) 
W: Some index files failed to download. They have been ignored, or old ones used instead.</code></pre>

## [The Problems Explanation: Why APT Keys Expire](#explanation)

This error indicates that the APT repository key used for signing packages from GitLab has expired (EXPKEYSIG), meaning the cryptographic signature can no longer be verified as valid.

The specific key ID in question is 3F01618A51312F3F, which is associated with "GitLab B.V." ("GitLab Besloten Vennootschap", which is Dutch for "GitLab Private Company"). This key expired March first 2024:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@git.blunix.com:~#">
<code class="language-bash">gpg2 --keyring /usr/share/keyrings/gitlab_gitlab-ce-archive-keyring.gpg --list-keys
/usr/share/keyrings/gitlab_gitlab-ce-archive-keyring.gpg
--------------------------------------------------------
pub   rsa4096 2020-03-02 [SC] [expired: 2024-03-01]      <==== HERE
      F6403F6544A38863DAA0B6E03F01618A51312F3F
uid           [ expired] GitLab B.V. (package repository signing key) </code></pre>

APT keys expire to enhance security by periodically requiring the verification and renewal of keys, thereby mitigating the risk of long-term compromises. When an APT key expires, your system will refuse to update or install packages from the related repository until the key is updated or replaced with a valid (not expired) one.

It used to be common practice to download the new Gitlab gpg key and import it into the archive keyring file "/etc/apt/trusted.gpg" using the following command:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@git.blunix.com:~#">
<code class="language-bash">curl -s https://packages.gitlab.com/gpg.key | apt-key add -
Warning: apt-key is deprecated. Manage keyring files in trusted.gpg.d instead (see apt-key(8)).</code></pre>

However this will issue a warning message that the tool "apt-key" is deprecated. Newer Gitlab installations stopped storing gpg keys in the Debian (or Ubuntu) default keyring file "/etc/apt/trusted.gpg" (the path will be slightly different for Ubuntu servers), and started storing the gpg keys in a dedicated file "/usr/share/keyrings/gitlab_gitlab-ce-archive-keyring.gpg".

The approach with "apt-key" will ONLY work if your "/etc/apt/sources.list.d/gitlab_gitlab-ce.list" file does NOT contain a line like this: "signed-by=/usr/share/keyrings/gitlab_gitlab-ce-archive-keyring.gpg". In this case, "apt-key" will update the file "/etc/apt/trusted.gpg". If you do have this line defined, then you can not use "apt-key" command and you have to do it "the modern way":

## [The Solution to the Problem: Updating the Archive Keyring with the New GPG Key](#solution)

This sources.list will work with the "apt-key" command:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@git.blunix.com:~#">
<code class="language-bash">grep archive-keyring /etc/apt/sources.list.d/gitlab_gitlab-ce.list 
deb https://packages.gitlab.com/gitlab/gitlab-ce/debian/ bookworm main</code></pre>

Use the following commands in this case:

<pre class="command-line language-bash" data-output="2" data-continuation-str="\" data-prompt="root@git.blunix.com:~#">
<code class="language-bash">curl -s https://packages.gitlab.com/gpg.key | apt-key add -
Warning: apt-key is deprecated. Manage keyring files in trusted.gpg.d instead (see apt-key(8)).
apt update
apt upgrade gitlab-ce</code></pre>

This sources.list file requires the approach described in the following:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@git.blunix.com:~#">
<code class="language-bash">grep archive-keyring /etc/apt/sources.list.d/gitlab_gitlab-ce.list 
deb [signed-by=/usr/share/keyrings/gitlab_gitlab-ce-archive-keyring.gpg] https://packages.gitlab.com/gitlab/gitlab-ce/debian/ bookworm main</code></pre>

The following command will import the Gitlab gpg key into the Gitlab keyring file "/usr/share/keyrings/gitlab_gitlab-ce-archive-keyring.gpg":

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-prompt="root@git.blunix.com:~#">
<code class="language-bash">wget -qO- https://packages.gitlab.com/gpg.key | \
    gpg --no-default-keyring --keyring /usr/share/keyrings/gitlab_gitlab-ce-archive-keyring.gpg --import</code></pre>

"apt update" will now work without errors:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@git.blunix.com:~#">
<code class="language-bash">apt update
Hit:1 http://mirror.hetzner.com/debian/packages bookworm InRelease
Hit:2 http://deb.debian.org/debian bookworm InRelease                                                                                                                                          
Hit:3 http://security.debian.org/debian-security bookworm-security InRelease                                                                                                                   
Get:4 http://deb.debian.org/debian bookworm-updates InRelease [55,4 kB]                                                                                                                        
Hit:5 http://mirror.hetzner.com/debian/packages bookworm-updates InRelease                                  
Hit:6 http://mirror.hetzner.com/debian/security bookworm-security InRelease
Get:7 https://packages.gitlab.com/gitlab/gitlab-ce/debian bookworm InRelease [23,3 kB]
Get:8 https://packages.gitlab.com/gitlab/gitlab-ce/debian bookworm/main amd64 Packages [11,0 kB]
Fetched 89,8 kB in 2s (40,8 kB/s)     
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
1 package can be upgraded. Run 'apt list --upgradable' to see it.</code></pre>
