---
title: "Howto setup borgbackup2 on Debian12 on Hetzner Cloud Servers"
description: "Setup borgbackup2 for up to 50-100 Hetzner Cloud Servers on Debian 13 using BASH parallelisation for automation"
date: 2024-02-12
image: "/images/blog/borgbackup-logo.webp"
image_alt: "Howto setup borgbackup2 on Debian12 on Hetzner Cloud Servers"
---

Please [contact us](/index.html#contact "Blunix GmbH contact options") if anything is not clearly described, does not work, seems incorrect or if you require support.

## Table of contents

- [1. Creating the Cloud Servers on Hetzner](#creating-servers)
  - [1.1 Creating the Servers using the Hetzner Cloud WebUI](#creating-servers-using-webui)
  - [1.2 Choosing CPU and RAM ressources for the borgbackup2 servers and clients](#creating-servers-ressource-requirements)
  - [1.3 Add SSH public key for passwordless secure authentication](#creating-servers-add-ssh-public-key)
  - [1.5 Specify number of servers and hostnames](#creating-servers-number-and-names)
  - [1.4 Add storage volume to backup server](#creating-servers-add-volume)
  - [1.6 Checking if the servers are reachable via SSH](#first-ssh-connection-check)
  - [1.7 Installing example services like Nginx, Mariadb and Gitlab](#installing-exemplary-services)
- [2. Installing and configuring the borgbackup2 server components](#setup-borgbackup2-server)
  - [2.1 Preperations for the backup server](#server-preperations)
  - [2.2 Installing borgbackup2 server with apt](#server-installation)
  - [2.3 Configuring the borgbackup2 server](#server-configuration)
- [3. Installing and configuring the borgbackup2 client servers](#setup-borgbackup2-clients)
  - [3.1 Preperations for the backup clients](#clients-preperations)
  - [3.2 Installing borgbackup2 client with apt](#clients-installation)
  - [3.3 Configuring the borgbackup2 clients](#clients-configuration)
  - [3.4 Creating a BASH script and cronjob to automatically create borg backups](#clients-backup-script)
- [4. Connecting the borgbackup2 clients to the server](#connect-borgbackup2-clients-with-server)
  - [4.1 Copying the clients SSH public keys to the server](#connect-ssh-keys)
  - [4.2 Testing the backup and restore functionality](#connect-test)

## [1. Creating the Cloud Servers on Hetzner](#creating-servers)

This blogpost describes how to setup borgbackup2 on Debian Linux 12 with the German IaaS provider [hetzner.com/cloud/](https://www.hetzner.com/cloud/). It is designed to setup a working borgbackup system on up to around 50-100 servers depending on your workstations internet connection.

This post leaves out a multitude of important key aspects in setting up a production backup environment like automatic security upgrades, firewalls, server-to-server VPN encryption, intrusion detection and more. If this document and borgbackup catches your interest, consider [talking to a Blunix GmbH Linux Expert](/linux-consulting.html) about implementing borg as a backup solution in your companies infrastructure.

### [1.1 Creating the Servers using the Hetzner Cloud WebUI](#creating-servers-using-webui)

For this blogpost we will create four Cloud Servers running Debian 12:

![Create four Cloud Servers with Hetzner](/images/blog/howto-setup-borgbackup2-on-hetzner-cloud/create-servers.webp)

### [1.2 Choosing CPU and RAM ressources for the borgbackup2 servers and clients](#creating-servers-ressource-requirements)

The ressource requirements in RAM and CPU depend for the borgbackup2 server depend on how many clients you want to backup simultaniously. By executing the backups nightly at random times between 0am and 6am, concurent backup processes can be circumvented, however depending on the number of servers you want to backup this might not be possible. Enabling encryption and compression will also increase ressource usage. Obviously the amount of data to be synchronized per server, or better said the amount of data that has changed since the last backup, is also relevant.

As a rule of thumb you may start with four CPUs and 8 GB RAM for up to 15-20 servers with disks around 200 GB per server and not to regular changes like webservers and databases hosting PHP, Typo3, Wordpress, Blogs and from a storage space perspective rather static data.

![Select the Hetzner Cloud server type](/images/blog/howto-setup-borgbackup2-on-hetzner-cloud/select-server-type.webp)

### [1.3 Add SSH public key for passwordless secure authentication](#creating-servers-add-ssh-public-key)

Make sure to add your SSH public key to be able to SSH login to the new cloud servers without a password. Refer to [the Blunix Manual](https://www.blunix.com/manual/getting-started/new-employee-workstation-setup/index.html#generating-a-ssh-keypair) to create a secure private and public Keypair.

![Add SSH public key to Hetzner Cloud Servers](/images/blog/howto-setup-borgbackup2-on-hetzner-cloud/add-ssh-key.webp)

### [1.5 Specify number of servers and hostnames](#creating-servers-number-and-names)

We will create four servers following the [Blunix naming scheme for servers](https://www.blunix.com/manual/introduction/design-concepts/index.html#hostnames-and-groups):

![Naming the new Hetzner Cloud Servers](/images/blog/howto-setup-borgbackup2-on-hetzner-cloud/server-names.webp)

<table>
  <tbody><tr>
    <th>Server Name</th>
    <th>Main Function</th>
  </tr>
  <tr>
    <td>blu-util-prod-backup-1</td>
    <td>Runs the borgbackup2 server component</td>
  </tr>
  <tr>
    <td>blu-util-prod-git-1</td>
    <td>Runs gitlab to host the Blunix code</td>
  </tr>
  <tr>
    <td>blu-www-prod-web-1</td>
    <td>Runs a nginx webserver and PHP to host a website</td>
  </tr>
  <tr>
    <td>blu-www-prod-db-1</td>
    <td>Runs mariaDB for the web application</td>
  </tr>
</tbody></table>

### [1.4 Add storage volume to backup server](#creating-servers-add-volume)

Finally we have to create a storage volume for (at least) our borgbackup2 server to store the backups of the other servers:

![Create a volume for borgbackup2 on the Hetzner Cloud Server](/images/blog/howto-setup-borgbackup2-on-hetzner-cloud/create-borgbackup2-volume.webp)

With all servers newly created it is time to perform a first login check. Lets first collect the IPs of the new servers.

### [1.6 Checking if the servers are reachable via SSH](#first-ssh-connection-check)

In order to not have to deal with IP addresses we can setup some /etc/hosts entries:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-user="user" data-host="workstation">
<code class="language-bash">echo "49.13.155.189 blu-util-prod-backup-1 backup
5.75.149.214 blu-www-prod-db-1 db
128.140.37.109 blu-util-prod-git-1 git
188.34.205.212 blu-www-prod-web-1 web" | sudo tee -a /etc/hosts
[sudo] password for user: 
49.13.155.189 blu-util-prod-backup-1 backup
5.75.149.214 blu-www-prod-db-1 db
128.140.37.109 blu-util-prod-git-1 git
188.34.205.212 blu-www-prod-web-1 web</code></pre>

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-user="user" data-host="workstation">
<code class="language-bash">ping -c 1 backup
PING blu-util-prod-backup-1 (49.13.155.189) 56(84) bytes of data.
64 bytes from blu-util-prod-backup-1 (49.13.155.189): icmp_seq=1 ttl=52 time=92.8 ms</code></pre>

To quickly automate interacting with all servers, we will use a small tool called parallel-ssh. It needs a simple config file of one hostname per line:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-user="user" data-host="workstation">
<code class="language-bash">echo "blu-util-prod-backup-1
blu-util-prod-git-1
blu-www-prod-web-1
blu-www-prod-db-1" > hetzner-servers.txt</code></pre>

We can now use this list with parallel-ssh in order to execute commands on all hosts simultaniously. To check if all servers are reachable and to accept their SSH host keys at the same time use the following command:

<pre class="command-line language-bash" data-output="3-99" data-continuation-str="\" data-user="user" data-host="workstation">
<code class="language-bash">sudo apt install pssh
parallel-ssh --hosts=hetzner-servers.txt --user=root --inline --extra-arg="-o StrictHostKeyChecking=accept-new" whoami
[1] 05:35:14 [SUCCESS] blu-util-prod-git-1
root
[2] 05:35:14 [SUCCESS] blu-util-prod-backup-1
root
[3] 05:35:14 [SUCCESS] blu-www-prod-db-1
root
[4] 05:35:14 [SUCCESS] blu-www-prod-web-1
root</code></pre>

### [1.7 Installing example services like Nginx, Mariadb and Gitlab](#installing-exemplary-services)

In a real world scenario we would now use the Blunix tools to setup initial basic packages and configurations as documented in the [Blunix Manual in the provisioning section](https://www.blunix.com/manual/provisioning/introduction/index.html). After this we would setup and configure a [baseline of services](https://www.blunix.com/manual/baseline/introduction/index.html) like a [firewall](https://www.blunix.com/manual/baseline/functions/shorewall/index.html), a [mailrelay](https://www.blunix.com/manual/baseline/functions/mailrelay/index.html), a [server to server mesh VPN](https://www.blunix.com/manual/baseline/functions/wireguard-mesh/index.html) and more.

For simplicity's sake we will overlook these steps in this blogpost and concentrate on setting up (mockups of) the services that are to be the main purpose of the machines: a webserver, a database server and a gitlab server.

Chain commands for ssh using the && operator, which only executes the next command if the previous one succeeeded (exit status = 0) like so:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-user="user" data-host="workstation">
<code class="language-bash">ssh root@db "apt-get update && apt-get -y install mariadb-server"
ssh root@web "apt-get update && apt-get -y install nginx"</code></pre>

Lets [quickly install gitlab](https://about.gitlab.com/install/#debian) on the new git server in "one line" of BASH. You can define multiline consecuitive commands to be executed on a remote server directly on your workstation without opening an interactive shell on the server by using a double-quote after ssh root@server like so:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-user="user" data-host="workstation">
<code class="language-bash">ssh root@git "
apt-get update
apt-get install -y curl openssh-server ca-certificates perl
apt-get install -y postfix
curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | bash
EXTERNAL_URL='https://git.blunix.com' apt-get -y install gitlab-ce
gitlab-ctl reconfigure
gitlab-ctl restart"</code></pre>

Now that all servers are installed with their exemplary main purpose software, lets move on to install, setup and configure borgbackup2 to backup these client servers.

## [2. Installing and configuring the borgbackup2 server components](#setup-borgbackup2-server)

### [2.1 Preperations for the backup server](#server-preperations)

Lets configure the borgbackup2 server. First use SSH to login to the backup server:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-user="user" data-host="workstation">
<code class="language-bash">ssh root@backup</code></pre>

Setup all clients in the borgbackup servers /etc/hosts file:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-user="root" data-host="blu-util-prod-backup-1">
<code class="language-bash">echo "5.75.149.214 blu-www-prod-db-1
128.140.37.109 blu-util-prod-git-1
188.34.205.212 blu-www-prod-web-1" | tee --append /etc/hosts</code></pre>

As the first entry in /etc/hosts matching the backup servers hostname is set to 127.0.1.1 by Debian default, the entry for the backup server itself can be omitted.

### [2.2 Installing borgbackup2 server with apt](#server-installation)

In order to mount the additional disk to /home/borgbackup, we first have to unmount the default Hetzner configured mountpoint:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-user="root" data-host="blu-util-prod-backup-1">
<code class="language-bash">umount /mnt/HC_Volume*
sed -i 's/.*HC_Volume.*//g' /etc/fstab</code></pre>

Then add the disks UUID to /etc/fstab:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-user="root" data-host="blu-util-prod-backup-1">
<code class="language-bash">grep -q borg /etc/fstab || echo "UUID=$(blkid -s UUID -o value /dev/sdb) /home/borgbackup ext4 defaults 0 0" >> /etc/fstab
systemctl daemon-reload</code></pre>

Mount the disk to /home/borgbackup:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-user="root" data-host="blu-util-prod-backup-1">
<code class="language-bash">mkdir /home/borgbackup
chmod -v 750 /home/borgbackup
mount -a</code></pre>

Then install the required apt packages:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-user="root" data-host="blu-util-prod-backup-1">
<code class="language-bash">apt update
apt install borgbackup2</code></pre>

Add a borgbackup Linux Group and Linux User to run the backup server process without root privileges. As the home directory already exists and is a mounted directory, we will have to copy the files from /etc/skel manually and then chown the new users home directory:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-user="root" data-host="blu-util-prod-backup-1">
<code class="language-bash">groupadd --system borgbackup
useradd --system --shell /bin/bash --home-dir /home/borgbackup --create-home --gid borgbackup borgbackup
cp -a /etc/skel/. /home/borgbackup/
chown -R borgbackup:borgbackup /home/borgbackup/</code></pre>

### [2.3 Configuring the borgbackup2 server](#server-configuration)

Create the BASH list of clients and then iterate the list, creating an archive directory for each client and then initializing the archive. For the sake of simplicity, we will skip archive file encryption for now, which means that the backups of the backup client servers will be saved on the backup server unencrypted. Please do not use this in production.

If you are looking for a ready to run hosting environment that is FOSS and designed to be easy to use for your developers, consider taking a look at [Blunix Managed Hosting](/linux-managed-hosting.html), which comes with borgbackup configured for production use.

For those who already know borgbackup version 1, the command borg init has been renamed to borg rcreate (repo create) in borg version 2.

<pre class="command-line language-bash" data-output="3-99" data-continuation-str="\" data-user="root" data-host="blu-util-prod-backup-1">
<code class="language-bash">clients=(blu-util-prod-backup-1 blu-util-prod-git-1 blu-www-prod-web-1 blu-www-prod-db-1)
for client in ${clients[@]}; do
    mkdir -p /home/borgbackup/archives/$client
    chown -v borgbackup:borgbackup /home/borgbackup/archives/$client
    chmod -v 700 /home/borgbackup/archives/$client
    borg2 rcreate --encryption none --repo /home/borgbackup/archives/$client
done</code></pre>

Additionally change the ownership of /home/borgbackup to the borgbackup Linux user and group:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-user="root" data-host="blu-util-prod-backup-1">
<code class="language-bash">chown -R borgbackup:borgbackup /home/borgbackup</code></pre>

Lets view the borgbackup2 configuration files that were created in the archive directory:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-user="root" data-host="blu-util-prod-backup-1">
<code class="language-bash">ls /home/borgbackup/archives/*
/home/borgbackup/archives/blu-util-prod-backup-1:
config	data  hints.1  index.1	integrity.1  README

/home/borgbackup/archives/blu-util-prod-git-1:
config	data  hints.1  index.1	integrity.1  README

/home/borgbackup/archives/blu-www-prod-db-1:
config	data  hints.1  index.1	integrity.1  README

/home/borgbackup/archives/blu-www-prod-web-1:
config	data  hints.1  index.1	integrity.1  README</code></pre>

A basic directory structure for logs and, later on, ssh public keys from the backup client servers is required:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-user="root" data-host="blu-util-prod-backup-1">
<code class="language-bash">mkdir -p /home/borgbackup/logs/prune/
chmod -v 700 /home/borgbackup/logs/prune/
mkdir -v /home/borgbackup/.ssh/
touch /home/borgbackup/.ssh/authorized_keys
chmod 700 /home/borgbackup/.ssh/
chmod 600 /home/borgbackup/.ssh/*
chown -R -v borgbackup:borgbackup /home/borgbackup/</code></pre>

## [3. Installing and configuring the borgbackup2 client servers](#setup-borgbackup2-clients)

Now that the borgbackup2 server has been set up lets concentrate on the backup clients.

### [3.1 Preperations for the backup clients](#clients-preperations)

As the configuration of the borgbackup clients is (close to) identical for all servers, this is another task we can partially automate using parallel-ssh. First we have to setup a /etc/hosts entry for the backup server on all backup client servers:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-user="user" data-host="workstation">
<code class="language-bash">parallel-ssh --hosts=hetzner-servers.txt --user=root --inline "echo 49.13.155.189 blu-util-prod-backup-1 | tee -a /etc/hosts"
[1] 18:55:43 [SUCCESS] 49.13.155.189
49.13.155.189 blu-util-prod-backup-1
[2] 18:55:43 [SUCCESS] 188.34.205.212
49.13.155.189 blu-util-prod-backup-1
[3] 18:55:43 [SUCCESS] 5.75.149.214
49.13.155.189 blu-util-prod-backup-1
[4] 18:55:43 [SUCCESS] 128.140.37.109
49.13.155.189 blu-util-prod-backup-1</code></pre>

### [3.2 Installing borgbackup2 client with apt](#clients-installation)

Next install the borgbackup2 and python3-llfuse apt packages:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-user="user" data-host="workstation">
<code class="language-bash">parallel-ssh --hosts=hetzner-servers.txt --user=root --inline "apt-get update && apt-get -y install borgbackup2 python3-llfuse"</code></pre>

### [3.3 Configuring the borgbackup2 clients](#clients-configuration)

Create a SSH keypair for on each client server. We will later use this key to login to the borgbackup server to deposit backups.

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-user="user" data-host="workstation">
<code class="language-bash">parallel-ssh --hosts=hetzner-servers.txt --user=root --inline "
chmod 700 /root/.ssh/
test -f /root/.ssh/id_ed25519.pub || ssh-keygen -q -t ed25519 -a 100 -o -f /root/.ssh/id_ed25519 -N ''
chmod 600 /root/.ssh/*
cat /root/.ssh/id_ed25519.pub"

# Expected output
[1] 21:00:01 [SUCCESS] 49.13.155.189
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIPe8a1IUf6qv5yqjqiPm9X+7ATm/uVTceS3PGkgGnInN root@blu-util-prod-backup-1
[2] 21:00:01 [SUCCESS] 188.34.205.212
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIIoLelk1eP5XoCP6WAD7hRHSdePEm5XsEpjUsRACUsFX root@blu-www-prod-web-1
[3] 21:00:01 [SUCCESS] 5.75.149.214
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIOt0hyyxw0UCjQsUJ1+y0kP5HOcIymKWLD2gDqrR7yTJ root@blu-www-prod-db-1
[4] 21:00:01 [SUCCESS] 128.140.37.109
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIIzUXL4IBxsK5bnbQ0sPyBunsD0k5RkEBE1PmCy8wBru root@blu-util-prod-git-1</code></pre>

All backup client servers will have to accept the SSH host key of the backup server in order to establish SSH connections automatically / in scripts:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-user="user" data-host="workstation">
<code class="language-bash">parallel-ssh --hosts=hetzner-servers.txt --user=root --inline "ssh-keyscan -H blu-util-prod-backup-1 | tee -a /root/.ssh/known_hosts"</code></pre>

The borg2 command requires the environment variable BORG_REPO to be set. This variable is used to tell borg2 as which linux-user and at which server it has to login by SSH, and at which path it is allowed to deposit its backup archives. The following command will save the variable BORG_REPO in all backup client servers /root/.bashrc file so it is loaded every time you login by SSH to the backup client server. This way you can interact, debug and restore backups directly on the backup client server, which is how borgbackup is intended to be used.

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-user="user" data-host="workstation">
<code class="language-bash">parallel-ssh --hosts=hetzner-servers.txt --user=root --inline 'echo export BORG_REPO=\"ssh://borgbackup@blu-util-prod-backup-1/home/borgbackup/archives/$(hostname)\" | tee -a /root/.bashrc'</code></pre>

After writing the variable to your /root/.bashrc file in order for it to be loaded simply logout:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-user="root" data-host="blu-www-prod-web-1">
<code class="language-bash">echo $BORG_REPO # Gives no output
exit</code></pre>

And log back in again:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-user="user" data-host="workstation">
<code class="language-bash">ssh root@web</code></pre>

The variable should now be exported from your /root/.bashrc file:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-user="root" data-host="blu-www-prod-web-1">
<code class="language-bash">echo $BORG_REPO
ssh://borgbackup@blu-util-prod-backup-1/home/borgbackup/archives/blu-www-prod-web-1</code></pre>

### [3.4 Creating a BASH script and cronjob to automatically create borg backups](#clients-backup-script)

The actual command to create a backup is saved in a BASH script that can later be executed using a cronjob. Here is a working example for a borg create shell script for Debian Linux 12. The following script will backup everything in your filesystem tree including mounted filesystems. Blunix recommends to always backup everything including the whole Debian Linux Operating System files, because you never know when you might need it and backup space is generally cheap, especially now that you use deduplication and compression with borg backup :)

Lets first save the script on the workstation and then upload it to all backup client servers:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-user="user" data-host="workstation">
<code class="language-bash">cat > borg2-create.sh << 'EOF'
#!/bin/bash
#
# Create a borg2 backup of this server

source /root/.bashrc.d/borgbackup.sh
current_date=$(date +%d_%m_%y-%H_%M_%S)
logfile=/var/log/borgbackup/${current_date}.log
echo -e "BACKUP STARTING AT $(date +%d_%m_%y-%H_%M_%S)\n\n" >> $logfile

# Run hook scripts here and save the data in the filesystem of the backup client server
# The borg backup will later backup (almost) all files on the server
# mysqldump > $logfile 2>&1
# gitlab-backup create > $logfile 2>&1


# Create the actual backup. Extend the --exclude arguments as required in your usecase.
borg create \
    --verbose \
    --stats \
    --exclude-caches \
    --exclude /mnt \
    --exclude /media \
    --exclude /tmp \
    --exclude /proc \
    --exclude /sys \
    --exclude /run \
    --exclude /var/lib \
    --exclude /var/log/lastlog \
    --exclude /home/borgbackup/archives \
    --exclude "*.journal" \
    --exclude "*.fsck" \
    --exclude "*.lost+found" \
    $current_date / >> $logfile 2>&1
EOF</code></pre>

Now upload the borg backup script to the servers:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-user="user" data-host="workstation">
<code class="language-bash">parallel-scp --hosts=hetzner-servers.txt --user=root borg2-create.sh /usr/local/sbin/borg2-create.sh
parallel-ssh --hosts=hetzner-servers.txt --user=root --inline 'chown -v root:root /usr/local/sbin/borg2-create.sh && chmod -v 500 /usr/local/sbin/borg2-create.sh'
rm borg2-create.sh</code></pre>

To automate creating nightly backups we set up a cronjob to run the /usr/local/sbin/borg2-create.sh BASH script at a random minute at a random hour between 0am and 6am:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-user="user" data-host="workstation">
<code class="language-bash">parallel-ssh --hosts=hetzner-servers.txt --user=root --inline 'grep --no-messages borg2-create.sh /var/spool/cron/crontabs/root || (crontab -l 2>/dev/null; echo "$((0 + RANDOM % 59)) $((0 + RANDOM % 6)) * * * /usr/local/sbin/borg2-create.sh") | crontab -'</code></pre>

To view the newly created crontab entries:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-user="user" data-host="workstation">
<code class="language-bash">parallel-ssh --hosts=hetzner-servers.txt --user=root --inline "crontab -l"
[1] 01:42:41 [SUCCESS] 49.13.155.189
11 1 * * * /usr/local/sbin/borg2-create.sh
[2] 01:42:41 [SUCCESS] 188.34.205.212
56 5 * * * /usr/local/sbin/borg2-create.sh
[3] 01:42:41 [SUCCESS] 5.75.149.214
18 2 * * * /usr/local/sbin/borg2-create.sh
[4] 01:42:41 [SUCCESS] 128.140.37.109
16 2 * * * /usr/local/sbin/borg2-create.sh</code></pre>

## [4. Connecting the borgbackup2 clients to the server](#connect-borgbackup2-clients-with-server)

Borgbackup uses passwordless SSH keypair authentication to send data between clients and servers.

### [4.1 Copying the clients SSH public keys to the server](#connect-ssh-keys)

All backup client servers SSH public keys for the root user have to be installed for the borgbackup user on the backup server. This is because only the root user can read all the files in the backup clients filesystem.

This command creates the ~/.ssh/ directory structure on the backup server:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-user="user" data-host="workstation">
<code class="language-bash">ssh root@backup "mkdir -v /home/borgbackup/.ssh/ && touch /home/borgbackup/.ssh/authorized_keys && chmod -v 700 /home/borgbackup/.ssh/ && chmod 600 /home/borgbackup/.ssh/*"</code></pre>

Load all backup clients SSH public keys into a BASH variable:

<pre class="command-line language-bash" data-output="3-99" data-continuation-str="\" data-user="user" data-host="workstation">
<code class="language-bash">all_ssh_public_keys=$(parallel-ssh --hosts=hetzner-servers.txt --user=root --inline 'cat /root/.ssh/id_ed25519.pub' | grep ^ssh)
echo "$all_ssh_public_keys"
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIPe8a1IUf6qv5yqjqiPm9X+7ATm/uVTceS3PGkgGnInN root@blu-util-prod-backup-1
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIIoLelk1eP5XoCP6WAD7hRHSdePEm5XsEpjUsRACUsFX root@blu-www-prod-web-1
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIIzUXL4IBxsK5bnbQ0sPyBunsD0k5RkEBE1PmCy8wBru root@blu-util-prod-git-1
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIOt0hyyxw0UCjQsUJ1+y0kP5HOcIymKWLD2gDqrR7yTJ root@blu-www-prod-db-1</code></pre>

Next paste all SSH public keys into a file that we can upload to the backpu server. We will restrict each backup client to only be able to execute the command borg2 serve:

<pre class="command-line language-bash" data-output="4-99" data-continuation-str="\" data-user="user" data-host="workstation">
<code class="language-bash">touch backup-authorized-keys.txt
clients=(blu-util-prod-backup-1 blu-util-prod-git-1 blu-www-prod-web-1 blu-www-prod-db-1)
for client in ${clients[@]}; do
    echo "command=\"/usr/bin/borg2 serve --append-only --restrict-to-path /home/borgbackup/archives/$client\",restrict $(grep $client <<< $all_ssh_public_keys)" >> backup-authorized-keys.txt
done</code></pre>

Then upload the authorized_keys file to the backup server:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-user="user" data-host="workstation">
<code class="language-bash">scp backup-authorized-keys.txt root@backup:/home/borgbackup/.ssh/authorized_keys
ssh root@backup "chown -R borgbackup:borgbackup /home/borgbackup/.ssh/ && chmod 600 /home/borgbackup/.ssh/authorized_keys"
rm backup-authorized-keys.txt</code></pre>

To view the /home/borgbackup/.ssh/authorized_keys file:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-user="user" data-host="workstation">
<code class="language-bash">ssh root@backup "cat /home/borgbackup/.ssh/authorized_keys"
command="/usr/bin/borg2 serve --append-only --restrict-to-path /home/borgbackup/archives/blu-util-prod-backup-1",restrict ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIMvOS1zheS9gYLKDYBdSH0ugwqXx1z0FfvCB+nIDtan5 root@blu-util-prod-backup-1
command="/usr/bin/borg2 serve --append-only --restrict-to-path /home/borgbackup/archives/blu-util-prod-git-1",restrict ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIJVzduwcbuNDtPRKpvssns+c0KcSPUcq0DXsmmUN+Uat root@blu-util-prod-git-1
command="/usr/bin/borg2 serve --append-only --restrict-to-path /home/borgbackup/archives/blu-www-prod-web-1",restrict ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIMt42AcSgMQOV99CMMlQ9LMjQSUkVHDarvFGTsoUQf/z root@blu-www-prod-web-1
command="/usr/bin/borg2 serve --append-only --restrict-to-path /home/borgbackup/archives/blu-www-prod-db-1",restrict ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIGJyJCeH7mh6vMiwM8CiuFlVgKzZZbRW4L0Dg7iO2va7 root@blu-www-prod-db-1</code></pre>

### [4.2 Testing the backup and restore functionality](#connect-test)

Time to test the new backup system. Login to any client server:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-user="user" data-host="workstation">
<code class="language-bash">ssh root@web</code></pre>

Now run the following commands to create a first small backup of the /root/ directory:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-user="root" data-host="blu-www-prod-web-1">
<code class="language-bash">borg2 create first-backup /root/
Warning: Attempting to access a previously unknown unencrypted repository!
Do you want to continue? [yN] y</code></pre>

To list all backups present on the backup server:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-user="root" data-host="blu-www-prod-web-1">
<code class="language-bash">borg2 rlist
first-backup                         Sun, 2024-02-11 22:52:22 +0000 [6211ee61c6515848ca3f70655b86c26dfb9998bb88dface986edff969a4ceb3f]</code></pre>

To list the contents of a specific backup:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-user="root" data-host="blu-www-prod-web-1">
<code class="language-bash">borg2 list first-backup
drwx------ root   root          0 Sun, 2024-02-11 21:28:39 +0000 root
-rw-r--r-- root   root        679 Sun, 2024-02-11 20:15:49 +0000 root/.bashrc
drwx------ root   root          0 Sun, 2024-02-11 20:03:25 +0000 root/.ssh
-rw------- root   root         81 Sun, 2024-02-11 12:58:38 +0000 root/.ssh/authorized_keys
[...]</code></pre>

To list the contents of a directory within a specific backup:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-user="root" data-host="blu-www-prod-web-1">
<code class="language-bash">borg2 list first-backup root/.ssh
drwx------ root   root          0 Sun, 2024-02-11 20:03:25 +0000 root/.ssh
-rw------- root   root         81 Sun, 2024-02-11 12:58:38 +0000 root/.ssh/authorized_keys
-rw------- root   root        419 Sun, 2024-02-11 19:57:53 +0000 root/.ssh/id_ed25519
-rw------- root   root        105 Sun, 2024-02-11 19:57:53 +0000 root/.ssh/id_ed25519.pub
-rw-r--r-- root   root        978 Sun, 2024-02-11 20:03:25 +0000 root/.ssh/known_hosts</code></pre>

The most userfriendly way of restoring files is to mount all backups directly on the backup client machine:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-user="root" data-host="blu-www-prod-web-1">
<code class="language-bash">borg2 mount /mnt/

ls /mnt
first-backup

ls /mnt/first-backup/root/.ssh/
authorized_keys  id_ed25519  id_ed25519.pub  known_hosts</code></pre>

After the restore don't forget to unmount the backup archives again:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-user="root" data-host="blu-www-prod-web-1">
<code class="language-bash">umount /mnt/</code></pre>

To learn more about common usecases and more advanced usage of the borg to create, restore, debug and interact with backup archives please refer to [the Blunix Manual Section about Borgbackup](https://www.blunix.com/manual/utility/functions/backup/index.html).
