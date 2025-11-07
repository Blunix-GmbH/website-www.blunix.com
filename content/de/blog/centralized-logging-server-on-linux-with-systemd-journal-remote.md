---
title: "Open Source Centralized Logging Server for Linux"
description: "Howto Setup an Open Source Centralized Logging Server with Log Management on Debian and Ubuntu Linux with Systemd-Journal-Remote"
date: 2024-03-24
image: "/images/blog/systemd-logo.webp"
image_alt: "Open Source Centralized Logging Server with Systemd-Journal-Remote on Debian and Ubuntu Linux"
featured: true
featured_image: "/images/index/blog-1.webp"
featured_image_alt: "Systemd Logo"
featured_title: "Centralized Logging with Systemd-Journal-Remote"
featured_excerpt: "Learn how to centralize logs on servers running systemd based Linux distributions using systemd-journal-remote."
---

Please [contact us](/index.html#contact "Blunix GmbH contact options") if anything is not clearly described, does not work, seems incorrect or if you require support.

This blogpost describes how to install and configure systemd-journal-remote to stream logs of up to hundrets of client servers to a central log aggregation machine. It was designed to be easy to follow for Linux administrators with little Linux experience. All commands and configuration files required to setup this service are explained in detail and can be copy-pasted. It was written for and tested with Debian 13 and Ubuntu 24.04, but should work with any other Linux Distribution that is using systemd.

## Table of contents

- [What is an Open Source Centralized Logfile Server for Linux?](#what-are-centralized-logfiles)
- [What are the Advantages of a Centralized Log Management Solution?](#advantages-of-centralized-logfiles)
- [Systemd-Journal-Remote vs Graylog vs FluentD vs Splunk vs LogDNS vs DataDog](#advantages-of-centralized-logfiles-systemd-over-others)
- [Centralized Log Management Server Installation](#installing-the-central-log-server)
  - [Installing systemd-journal-remote using apt](#installing-the-central-log-server-apt-package)
  - [Setting Up Log Directory Permissions](#installing-the-central-log-server-log-directory)
  - [Configuring systemd-journal-remote](#installing-the-central-log-server-configuration-files)
  - [Creating a Systemd-Timer to Prune Old Logs](#installing-the-central-log-server-prune-systemd-timer)
- [Centralized Log Management Clients Installation](#installing-the-central-log-client)
  - [Installing systemd-journal-remote using apt](#installing-the-central-log-client-install-apt)
  - [Configuring systemd-journal-upload](#installing-the-central-log-client-configuration-upload)
  - [Configuring systemd-journal-upload Service](#installing-the-central-log-client-configuration-systemd-service)
  - [Checking if it works](#installing-the-central-log-client-checking-if-it-works)
- [Commonly used Journalctl Commands to view and analyze client logs](#common-journalctl-commands)

## [What is an Open Source Centralized Logfile Server for Linux?](#what-are-centralized-logfiles)

A centralized logfile server is a system designed to aggregate, store, and analyze log data from multiple sources in a single location for easier monitoring and management.

With recent Linux distributions switching to systemd as an init system and system manager, log messages are now stored in an efficient [systemd journal file format](https://systemd.io/JOURNAL_FILE_FORMAT/).

By default these journal logs are stored directly on every server in a location like "/var/log/journal/8GYiL51Rgi2n5IyNFGqoGiBwaqQ4WRV0/system.journal", where "8GYiL51Rgi2n5IyNFGqoGiBwaqQ4WRV0" is the machine identifyer set in "/etc/machine-id".

This works well for a single system, however when you're working with a larger number of servers or cloud instances, you will need a better solution to centrally manage all of those logfiles.

Centrally managing logfiles of many servers also has the advantage that logs, once send to the central server, can not be modified anymore by an attacker - an attacker could modify the logs that are saved on the client, but you could compare them to the server and the modification would show up. This increases the security of your infrastructure overall.

Fun fact: we have had such an incident once with a client where a third party made a change on their servers and messed it up, and then tried to cover their tracks by deleting log lines. Our consultants were able to determine this by comparing the logs on the (client) server with those that were send to the central log server.

A centralized log management solution helps with these issues. It is consolidating all client servers individual journals into a single journal on the central log server, which can be searched using identifyers such as the clients hostname or the hostname plus the name of a service or services.

## [What are the Advantages of a Centralized Log Management Solution?](#advantages-of-centralized-logfiles)

Here are some good reasons for setting up a centralized logging system:

- You can assign more disk space to the central log server in order to store logs longer than on the clients.

- Attackers will often try to delete logfiles - by streaming the log lines out to the central server, they can not be deleted anymore by the attacker.

- Analyzing logs becomes a breeze when you can cross-reference data from multiple systems in one place.

- For system administrators, it streamlines access to logs across all systems, some of which they might not have direct access to due to security policies.

To create a log server that's Open Source, we will leverage components of [systemd](https://systemd.io/), namely [systemd-journal-remote](https://www.freedesktop.org/software/systemd/man/latest/systemd-journal-remote.service.html), to stream all logs from various Debian and Ubuntu Linux hosts to a central log collector server.

## [Systemd-Journal-Remote vs Graylog vs FluentD vs Splunk vs LogDNS vs DataDog](#advantages-of-centralized-logfiles-systemd-over-others)

Systemd-Journal-Remote is following the unix philosophy [do one thing and do it well](https://en.wikipedia.org/wiki/Unix_philosophy#Do_One_Thing_and_Do_It_Well) - it only streams the logfiles of Linux servers to a central log server, nothing else. In addition to that, systemd provides the [journalctl cli command](https://www.man7.org/linux/man-pages/man1/journalctl.1.html) which can be used to query and search systemd journal files, which store all the logs in an efficient format.

On the central log server you can additionally install graylog or another log analysis tool to examine the logs of the client hosts. Graylog brings log aggregation tools as well, but they are neither as lightweight nor as efficient in transmitting and storing logfiles as the Linux default systemd-journal-remote is. Hence we recommend to use systemd-journal-remote to stream the logs to the central server and then use graylog to analyze them with a graphical user interface.

While it is possible to use the command line interface "journalctl" to inspect the logfiles of all client hosts in detail and search through them, systemd-journal is neither build nor meant to replace a full scale log analysis tool like graylog or similar.

We think that using systemd-journal-remote to collect and centralize logfiles on Linux is a very good approach as it uses the systemd technology that already comes with the Linux distrubution anyways. It is very little additional code to run on the system, it is very stable (we have been using this solution for two years since before the time of this writing) especially during outages of the network or the central log server and it is the default solution for centralizing logfiles in systemd based Linux distributions.

At the end TODO of this blogpost we will provide you with the most common examples of using "journalctl" to search the logfiles of the log-client-servers, however we do recommend to install a graphical log-analysis solution on the central logserver, like graylog or similar. This will make searching through and alerting on logs much more easy than the somewhat hard to memorize "journalctl" commands.

## [Centralized Log Management Server Installation](#installing-the-central-log-server)

To effectively set up a centralized log management server using systemd-journal-remote, we'll translate the previously mentioned Ansible tasks into bash commands. This approach will enable you to manually set up your server, offering a hands-on understanding of each step.

### [Installing systemd-journal-remote using apt](#installing-the-central-log-server-apt-package)

Lets begin by installing the systemd-journal-remote apt package:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@central-log-server:~#">
<code class="language-bash">apt install systemd-journal-remote</code></pre>

### [Setting Up Log Directory Permissions](#installing-the-central-log-server-log-directory)

After installation set up the correct permissions for the directory where remote logs will be stored:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-prompt="root@central-log-server:~#">
<code class="language-bash">mkdir -p /var/log/journal/remote/
chown systemd-journal-remote:systemd-journal-remote /var/log/journal/remote/
chmod 0750 /var/log/journal/remote/</code></pre>

### [Configuring systemd-journal-remote](#installing-the-central-log-server-configuration-files)

Next, we configure systemd-journal-remote by creating the necessary configuration files:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@central-log-server:~#">
<code class="language-bash">cat << EOF > /etc/systemd/journal-remote.conf
[Remote]
Seal=true
SplitMode=none
EOF</code></pre>

Set the correct owner, group and permissions for the file:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-prompt="root@central-log-server:~#">
<code class="language-bash">chown systemd-journal-remote:systemd-journal-remote /etc/systemd/journal-remote.conf
chmod 0640 /etc/systemd/journal-remote.conf</code></pre>

And for the systemd service configuration /etc/systemd/system/systemd-journal-remote.service:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@central-log-server:~#">
<code class="language-bash">cat << EOF > /etc/systemd/system/systemd-journal-remote.service
[Unit]
Description=Journal Remote Sink Service
Documentation=man:systemd-journal-remote(8) man:journal-remote.conf(5)
Requires=systemd-journal-remote.socket

[Service]
ExecStart=/lib/systemd/systemd-journal-remote --listen-http=-3 --output=/var/log/journal/remote/all.journal
LockPersonality=yes
LogsDirectory=journal/remote
MemoryDenyWriteExecute=yes
NoNewPrivileges=yes
PrivateDevices=yes
PrivateNetwork=yes
PrivateTmp=yes
ProtectProc=invisible
ProtectClock=yes
ProtectControlGroups=yes
ProtectHome=yes
ProtectHostname=yes
ProtectKernelLogs=yes
ProtectKernelModules=yes
ProtectKernelTunables=yes
ProtectSystem=strict
RestrictAddressFamilies=AF_UNIX AF_INET AF_INET6
RestrictNamespaces=yes
RestrictRealtime=yes
RestrictSUIDSGID=yes
SystemCallArchitectures=native
User=systemd-journal-remote
WatchdogSec=3min

# If there are many split up journal files we need a lot of fds to access them
# all in parallel.
LimitNOFILE=524288

[Install]
Also=systemd-journal-remote.socket
EOF</code></pre>

Set the correct owner, group and permissions for the file:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-prompt="root@central-log-server:~#">
<code class="language-bash">chown root:root /etc/systemd/system/systemd-journal-remote.service
chmod 0644 /etc/systemd/system/systemd-journal-remote.service</code></pre>

And enable the systemd service:

<pre class="command-line language-bash" data-output="5-99" data-continuation-str="\" data-prompt="root@central-log-server:~#">
<code class="language-bash">systemctl daemon-reload
systemctl enable systemd-journal-remote.service
systemctl start systemd-journal-remote.service
systemctl status systemd-journal-remote.service
● systemd-journal-remote.service - Journal Remote Sink Service
     Loaded: loaded (/etc/systemd/system/systemd-journal-remote.service; indirect; preset: disabled)
     Active: active (running) since Sun 2024-03-24 17:50:31 UTC; 25s ago
TriggeredBy: ● systemd-journal-remote.socket
       Docs: man:systemd-journal-remote(8)
             man:journal-remote.conf(5)
   Main PID: 1638 (systemd-journal)
     Status: "Processing requests..."
      Tasks: 1 (limit: 4531)
     Memory: 5.6M
        CPU: 63ms
     CGroup: /system.slice/systemd-journal-remote.service
             └─1638 /lib/systemd/systemd-journal-remote --listen-http=-3 --output=/var/log/journal/remote/all.journal

Mar 24 17:50:31 central-log-server systemd[1]: Started systemd-journal-remote.service - Journal Remote Sink Service.
Mar 24 17:50:31 central-log-server systemd-journal-remote[1638]: microhttpd: MHD_OPTION_EXTERNAL_LOGGER is not the first option specified for the daemon. Some messages may be printed by the standard MHD logger.</code></pre>

### [Creating a Systemd-Timer to Prune Old Logs](#installing-the-central-log-server-prune-systemd-timer)

To maintain efficient storage management and prevent your log directory from becoming overly crowded, lets set up a systemd timer to periodically delete logs that are older than 90 days.

First, create a systemd service file that specifies the command to be executed. This service will not start automatically but will be triggered by a systemd timer. This service file defines a simple service that, when started, will execute the command to find and delete logs older than 90 days in /var/log/journal/remote.

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@central-log-server:~#">
<code class="language-bash">cat << EOF > /etc/systemd/system/delete-old-logs.service
[Unit]
Description=Delete old systemd logs

[Service]
Type=oneshot
ExecStart=/usr/bin/find /var/log/journal/remote -mtime +90 -delete
EOF</code></pre>

To define the correct user, group and permissions for the file:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-prompt="root@central-log-server:~#">
<code class="language-bash">chown root:root /etc/systemd/system/delete-old-logs.service
chmod 0644 /etc/systemd/system/delete-old-logs.service</code></pre>

Next, create a systemd timer that is set to trigger the delete-old-logs.service daily. The Persistent=true option ensures that if the timer was missed (e.g., the central log server was turned off), it will be triggered upon the next boot.

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@central-log-server:~#">
<code class="language-bash">cat << EOF > /etc/systemd/system/delete-old-logs.timer
[Unit]
Description=Timer for deleting old systemd logs

[Timer]
OnCalendar=daily
Persistent=true

[Install]
WantedBy=timers.target
EOF</code></pre>

To define the correct user, group and permissions for the file:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-prompt="root@central-log-server:~#">
<code class="language-bash">chown root:root /etc/systemd/system/delete-old-logs.timer
chmod 0644 /etc/systemd/system/delete-old-logs.timer</code></pre>

To enable and start the timer, run the following commands. These commands make sure the timer is active and will start based on the defined schedule, automatically triggering the service to delete old logs daily.

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-prompt="root@central-log-server:~#">
<code class="language-bash">systemctl enable delete-old-logs.timer
systemctl start delete-old-logs.timer</code></pre>

You can check the status of your timer by executing:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@central-log-server:~#">
<code class="language-bash">systemctl list-timers delete-old-logs.timer
NEXT                        LEFT    LAST PASSED UNIT                  ACTIVATES              
Mon 2024-03-25 00:00:00 UTC 6h left -    -      delete-old-logs.timer delete-old-logs.service

1 timers listed.
Pass --all to see loaded but inactive timers, too.</code></pre>

## [Centralized Log Management Clients Installation](#installing-the-central-log-client)

The next step is to configure all client servers to send their journals to the central log server.

We will configure the clients in such a way that they will both stream their logs to the central server as well as save them locally, like we are already used to from single server setups.

Keep in mind that when the network to the central log server is interrupted, or the central log server is down, or the disk on the central log server is full, the clients will interrupt streaming their logs and stream everything at onceby the time the central log server becomes available again. This way systemd-journal-remote ensures that logs do not get lost during temporary interruptions.

### [Installing systemd-journal-remote using apt](#installing-the-central-log-client-install-apt)

First, we ensure the necessary tools are installed on the client system. The systemd-journal-remote package contains the systemd-journal-upload service, which is responsible for sending logs to the centralized server.

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@log-client:~#">
<code class="language-bash">apt install systemd-journal-remote</code></pre>

### [Configuring systemd-journal-upload](#installing-the-central-log-client-configuration-upload)

Now, set up the configuration for the systemd-journal-upload service. This involves specifying the URL of your centralized logging server. You'll replace the placeholders with your server's IP address and port number.

Create /etc/systemd/journal-upload.conf with the following content, substituting systemd_journal_remote_server_ip with the actual IP of your central log server.

**Please note** that this setup does not configure TLS encryption using SSL certificates (note the http:// in the next config file), which is optionally available for systemd-journal-remote. We left this out because we believe that all servers in any given infrastructure should always be part of a private encrypted VPN mesh network, like [for example a wireguard mesh network](https://git.blunix.com/ansible-roles/role-wireguard-mesh), through which all such traffic is routed - alongside backup and monitoring traffic as well as any other internally used traffic. You however can configure systemd-journald-remote to use httpS:// if you plan to send the log data over networks that are not end-to-end encrypted. Please refer to [the systemd-journal-remote.conf manual page](https://www.freedesktop.org/software/systemd/man/latest/journal-remote.conf.html) or, if you want to be 100% sure to have it configured professionally and securely, [get in contact with our Linux Consultants](/index.html#contact) to have them setup the required configurations for you.

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@log-client:~#">
<code class="language-bash">cat << EOF > /etc/systemd/journal-upload.conf
[Upload]
URL=http://your_central_logserver_ip:19532
EOF</code></pre>

Set the correct user, group and permissions for the file:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-prompt="root@log-client:~#">
<code class="language-bash">chown root:root /etc/systemd/journal-upload.conf
chmod 0644 /etc/systemd/journal-upload.conf</code></pre>

### [Configuring systemd-journal-upload Service](#installing-the-central-log-client-configuration-systemd-service)

Lastly, define the systemd-journal-upload.service file. This includes various security and operational settings to optimize log uploading.

Create /etc/systemd/system/systemd-journal-upload.service with the following content:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@log-client:~#">
<code class="language-bash">cat << EOF > /etc/systemd/system/systemd-journal-upload.service
[Unit]
Description=Journal Remote Upload Service
Documentation=man:systemd-journal-upload(8)
Wants=network-online.target
After=network-online.target

[Service]
ExecStartPre=/bin/sleep 10
Restart=on-failure
DynamicUser=yes
ExecStart=/lib/systemd/systemd-journal-upload --save-state
LockPersonality=yes
MemoryDenyWriteExecute=yes
PrivateDevices=yes
ProtectProc=invisible
ProtectControlGroups=yes
ProtectHome=yes
ProtectHostname=yes
ProtectKernelLogs=yes
ProtectKernelModules=yes
ProtectKernelTunables=yes
RestrictAddressFamilies=AF_UNIX AF_INET AF_INET6
RestrictNamespaces=yes
RestrictRealtime=yes
StateDirectory=systemd/journal-upload
SupplementaryGroups=systemd-journal
SystemCallArchitectures=native
User=systemd-journal-upload
WatchdogSec=3min

[Install]
WantedBy=multi-user.target
EOF</code></pre>

Set the correct user, group and permissions for the file:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-prompt="root@log-client:~#">
<code class="language-bash">chown root:root /etc/systemd/system/systemd-journal-upload.service
chmod 0644 /etc/systemd/system/systemd-journal-upload.service</code></pre>

After configuring, enable and start the systemd-journal-upload service:

<pre class="command-line language-bash" data-output="3-4,7-99" data-continuation-str="\" data-prompt="root@log-client:~#">
<code class="language-bash">systemctl daemon-reload
systemctl enable systemd-journal-upload
Created symlink /etc/systemd/system/multi-user.target.wants/systemd-journal-upload.service → /etc/systemd/system/systemd-journal-upload.service.

systemctl start systemd-journal-upload
systemctl status systemd-journal-upload
● systemd-journal-upload.service - Journal Remote Upload Service
     Loaded: loaded (/etc/systemd/system/systemd-journal-upload.service; enabled; preset: disabled)
     Active: active (running) since Sun 2024-03-24 18:19:46 UTC; 45s ago
       Docs: man:systemd-journal-upload(8)
    Process: 1700 ExecStartPre=/bin/sleep 10 (code=exited, status=0/SUCCESS)
   Main PID: 1701 (systemd-journal)
     Status: "Processing input..."
      Tasks: 1 (limit: 4531)
     Memory: 2.8M
        CPU: 130ms
     CGroup: /system.slice/systemd-journal-upload.service
             └─1701 /lib/systemd/systemd-journal-upload --save-state

Mar 24 18:19:35 central-log-client systemd[1]: Starting systemd-journal-upload.service - Journal Remote Upload Service...
Mar 24 18:19:46 central-log-client systemd[1]: Started systemd-journal-upload.service - Journal Remote Upload Service.</code></pre>

### [Checking if it works](#installing-the-central-log-client-checking-if-it-works)

To check if everything works, open a terminal on the server and start viewing the logs of the client in follow mode:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-prompt="root@central-log-server:~#">
<code class="language-bash">journalctl --file /var/log/journal/remote/all.journal -n 0 -f</code></pre>

Now log something on the client:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@log-client:~#">
<code class="language-bash">systemd-cat --identifier="administrator" echo "this is a test string"</code></pre>

It should show up on the server:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="root@central-log-server:~#">
<code class="language-bash">journalctl --file /var/log/journal/remote/all.journal -n 0 -f
Mar 24 18:27:00 central-log-client administrator[1886073]: this is a test string</code></pre>

## [Commonly used Journalctl Commands to view and analyze client logs](#common-journalctl-commands)

For working examples on how to search the logs from the client servers, please [refer to the Blunix Manual](https://www.blunix.com/manual/utility/functions/log/index.html#viewing-and-live-streaming-logs), which documents exemplary journalctl commands on how to do this.

As mentioned before, we recommend to install a tool like graylog on the central log server in order to do this graphically and to setup alerts on logged errors.
