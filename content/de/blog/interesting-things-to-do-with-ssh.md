---
title: "Interesting and common SSH commands, tips, tricks and hacks"
description: "Blog Post about interesting and common SSH commands, tips, tricks and hacks for Linux Administrators which can be used to assist or automate daily tasks."
date: 2024-01-24
image: "/images/blog/openssh-logo.webp"
image_alt: "Six interesting things to do with SSH"
---

Please [contact us](/index.html#contact "Blunix GmbH contact options") if anything is not clearly described, does not work, seems incorrect or if you require support.

## Table of contents

- [Access a private network servers port by jumphosting via publicly reachable server: ssh -L](#l)
- [Forward a remote servers port to your local machines port: ssh -R: ssl -R](#r)
- [Take a disk dump (dd .img) from a remote host and save it to your workstation](#dd)
- [Decrypting a remote luks encrypted disks without saving the keyfile in the remote servers filesystem](#luks)
- [Sharing directories using the SSHFS network filesystem](#sshfs)
- [Executing a shell script without uploading it to the remote servers filesystem](#pipe-script)

## [Access a private network servers port by jumphosting via publicly reachable server: ssh -L](#l)

The argument -L for the ssh command allows for forwarding remote ports to the local workstation.

Forwarding a **local port to a remote host** with the -L argument could for example be used to publish a webserver running on a workstation on a server with a public IP.

Relevant sections from the ssh manual page:

```bash
user@debian:~$ man ssh
```

```bash
-L [bind_address:]port:host:hostport

Specifies that connections to the given TCP port or Unix socket on the local (client) host are to be forwarded to the given host and port, or Unix socket, on the remote side.

The bind_address of “localhost” indicates that the listening port be bound for local use only, while an empty address or ‘*’ indicates that the port should be available from all interfaces.
```

When forwarding ports with SSH, it is good practice to pass the -N option to not execute a command but just keep the session open. From man ssh:

```bash
-N      Do not execute a remote command.  This is useful for just forwarding ports.
```

As a practical example, let's say you want to access a database server which has no public IP address. You however have access to another server with a public IP that has access to said database server. The following command will forward all traffic send to 127.0.0.1:3456 over public-server-ip to database-server-internal-ip:3306

```bash
# [bind_address:]port:host:hostport
ssh -L 3456:database-server-internal-ip:3306 -N user@public-server-ip
```

Now when you access your workstations 127.0.0.1:3456 with a database client, you will be able to access the database server inside its internal network via the server with the public IP.

## [Forward a remote servers port to your local machines port: ssh -R: ssl -R](#r)

The argument -R for the ssh command allows for forwarding ports from the local workstation to a remote server.

Relevant sections from the ssh manual page:

```bash
user@debian:~$ man ssh
```

```bash
-R [bind_address:]port:host:hostport

Specifies that connections to the given TCP port or Unix socket on the remote (server) host are to be forwarded to the local side.

An empty bind_address, or the address ‘*’, indicates that the remote socket should listen on all interfaces.  Specifying a remote bind_address will only succeed if the server's GatewayPorts option is enabled (see sshd_config(5)).
```

When forwarding ports with SSH, it is good practice to pass the -N option to not execute a command but just keep the session open. From man ssh:

```bash
-N      Do not execute a remote command.  This is useful for just forwarding ports.
```

As a practical example, lets forward all HTTP traffic on tcp port 80 from a public server to our local workstation:

```bash
# -R [bind_address:]port:host:hostport
ssh -R 80:127.0.0.1:8080 -N root@public-server-ip
```

Now all HTTP traffic reaching this server will go to your local workstations port 8080.

## [Take a disk dump (dd .img) from a remote host and save it to your workstation](#dd)

SSH can be used to pipe input and output from the dd command. To take a snapshot (or disk dump .img) of a disk device file from a remote server, run this command:

```bash
ssh root@remote-server "dd if=/dev/sda" | dd of=remote-server-disk-sda.img status=progress
```

## [Decrypting a remote luks encrypted disks without saving the keyfile in the remote servers filesystem](#luks)

When decrypting a cryptsetup luks encrypted disk on a remote server, it is best not to save the encryption keyfile in the remote hosts filesystem. SSH can receive the data as a stream like so:

```bash
cat secret-disk-keyfile.img | ssh root@server "cryptsetup luksOpen /dev/sda3 secret_disk --key-file=-"
```

## [Sharing directories using the SSHFS network filesystem](#sshfs)

The SSHFS tool allows you to mount remote directories to your local workstation, much like NFS, the Network File System. SSHFS is not installed by default on most Debian based operating systems.

```bash
user@workstation:~$ sudo apt install sshfs
```

From the sshfs manual page:

```bash
user@workstation:~$ man sshfs

sshfs [user@]host:[dir] mountpoint [options]
```

Use this command to mount a remote servers directory to your local workstation:

```bash
user@workstation:~$ mkdir remote-www
user@workstation:~$ sshfs user@remote-server:/var/www remote-www/
```

## [Executing a shell script without uploading it to the remote servers filesystem](#pipe-script)

It is possible to execute a shell script on a remote server without uploading it via scp first.

```bash
user@workstation:~$ cat myscript.sh
#!/bin/bash
set -x
whoami
hostname
```

To execute the script on the remote server just pipe the script to ssh's stdin:

```bash
user@workstation:~$ cat myscript.sh | ssh user@remote-server
+ whoami
user
+ hostname
remote-server
```
