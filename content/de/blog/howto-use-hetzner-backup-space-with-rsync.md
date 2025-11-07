---
title: "Howto use the Hetzner Backup Space with rsync"
description: "This post describes how to overcome the hetzner backup space restriction of not being able to set linux uid / gids and so on on the (sshfs) mounted backup space"
date: 2014-11-26
image: "/images/blog/hetzner-logo.webp"
image_alt: "How to use the hetzner backup space with rsync"
---

Please [contact us](/index.html#contact "Blunix GmbH contact options") if anything is not clearly described, does not work, seems incorrect or if you require support.

This post was revived for nostalgy and SEO purposes. It might be outdated. The original version can be found on [archive.org](https://web.archive.org/web/20150904035115/https://www.blunix.org/howto-use-hetzner-backup-space-with-rsync/).

## Table of contents

- [1. The Problem](#problem)
- [2. The Solution](#solution)
  - [2.1 Automatic SSHFS for the Hetzner Backup Space](#solution-sshfs)
  - [>[2.2 Create a dd image for the actual backups](#solution-dd)](#22-create-a-dd-image-for-the-actual-backupssolution-dd)
  - [2.3 Automatically mounting the backup space and the image](#solution-mount)
- [3. Storing backups encrypted](#encrypted)
- [4. Additional information](#info)

## [1. The Problem](#problem)

The [www.hetzner.de Backup Space](https://www.hetzner.com/storage/storage-box) does not allow for rsync to change the linux uids. It however is often desirable to keep those file informations when doing backups. Rsync will print the following error when trying to copy files to the hetzner backup space mounted when via sshfs:

```plaintext
sending incremental file list
rsync: chown "/srv/backup/test" failed: Permission denied (13)

Please also notice that hetzner closes the sshfs connection upon multiple errors. sshfs mount will then automatically be unmounted. For running rsyncs, this results in the following error:

rsync: read errors mapping "/srv/backup/[...]": Input/output error (5)
```

## [2. The Solution](#solution)

Hetzner describes [in their documentation on how to access the storage box](https://docs.hetzner.com/robot/storage-box/access/access-ssh-rsync-borg) that in order to take full advantage of rsync it is required to create an image file of the size of the booked backup space, loop mount this image file, create a filesystem on it and mount it for the backups. The following describes how to do that. Of course this will cost some speed when doing backups. Compared to using just the normal backup space with rsync while not keeping the uids it seems negligible. Besides, you don’t have any choice. Also note that the access speed to the hetzner backup space is also dependent on other hetzner customers currently using it, as described here.

### [2.1 Automatic SSHFS for the Hetzner Backup Space](#solution-sshfs)

The following commands allow to automatically mount the hetzner backup space using sshfs, without having to enter a password every time:

```plaintext
mkdir -p /srv/backup/{backup,hetzner-backup-space}
apt-get install sshfs
sshfs u01234@u01234.your-backup.de:/ /srv/backup/hetzner-backup-space # Enter password
mkdir /srv/backup/hetzner-backup-space/.ssh
# If you did not yet create a ssh keypair for your user, do this now
ssh-keygen
# Export the public key in RFC4716 format, hetzner requires that
ssh-keygen -e -f ~/.ssh/id_rsa.pub | grep -v "Comment:" > ~/.ssh/id_rsa_rfc.pub
cat ~/.ssh/id_rsa_rfc.pub > /srv/backup/hetzner-backup-space/.ssh/authorized_keys
chmod 700 /srv/backup/hetzner-backup-space/.ssh
chmod 600 /srv/backup/hetzner-backup-space/.ssh/authorized_keys
```

### >[2.2 Create a dd image for the actual backups](#solution-dd)

Now an empty empty dd image has to be created and a filesystem has to be written on it. Using dd without seek on the sshfs would take forever, as every zero from /dev/zero would have to be sent over the network to the backup space. Therefore I recommend to create a sparse image file (think thin provisioning) with dd like follows. This does not take longer than a few seconds. Its also possible to use `fallocate` for this.

```plaintext
dd if=/dev/zero of=/srv/backup/hetzner-backup-space/filesystem.img bs=1 seek=100G count=1
mkfs.ext4 /srv/backup/hetzner-backup-space/filesystem.img
```

If you need more space to backup your data you can just order more from hetzner as described here. To grow the image file use the following commands (note the change from 100G to 200G in the dd seek argument). This will not overwrite existing data on the image file.

```plaintext
dd if=/dev/zero of=/srv/backup/hetzner-backup-space/filesystem.img bs=1 seek=200G count=1
resize2fs /srv/backup/hetzner-backup-space/filesystem.img
```

### [2.3 Automatically mounting the backup space and the image](#solution-mount)

To automatically mount the hetzner backup space and the filesystem.img just created append the following lines to the /etc/fstab file. You can then also manually execute `mount -a` or `mount /srv/backup/hetzner-backup-space; mount /srv/backup/backup` to mount them during runtime:

```plaintext
u0815@u0815.your-backup.de:/ /srv/backup fuse.sshfs defaults,_netdev 0 0
/srv/backup/hetzner-backup-space/filesystem.img /srv/backup/backup ext4 defaults,loop 0 0
```

## [3. Storing backups encrypted](#encrypted)

Note that this way it is also possible to encrypt your backups using cryptsetup. It is recommended to not store keyfiles for temporary usage on the hard disk, as deleted files can be recovered using data forensic tools. Permanently store it in a (preferably offline) safe location and only upload it to a virtual filesystem.

```plaintext
# 32 bytes are 256 bits
dd if=/dev/random of=/dev/shm/hetzner-backup.key bs=1 count=32
```

Save the file on a safe location now. Do not permanently store it on the server.

```plaintext
cryptsetup luksFormat --key-file=/dev/shm/hetzner-backup.key /dev/backup/hetzner-backup-space/filesystem.img
cryptsetup luksOpen --key-file=/dev/shm/hetzner-backup.key /dev/backup/hetzner-backup-space/filesystem.img backup_crypt
```

Remember to delete the keyfile on the server after usage

```plaintext
rm /dev/shm/hetzner-backup.key
```

Create a filesystem on the unencrypted mapping

```plaintext
mkfs.ext4 /dev/mapper/backup_crypt
```

The decrypted mapping has to be mounted by hand. The /etc/fstab (or /etc/crypttab) file makes no sense here, as the keyfile is not stored on the server and therefore requires human interaction to access the encrypted backup image.

```plaintext
mount -o loop /dev/mapper/backup_crypt /srv/backup/backup
```

When all backups are finished for today, you can also close the cryptsetup mapping again. This has to be done before umounting the backup directory!

```plaintext
cryptsetup luksClose /dev/mapper/backup_crypt
```

## [4. Additional information](#info)

[https://docs.hetzner.com/robot/storage-box/general](https://docs.hetzner.com/robot/storage-box/general) [https://wiki.hetzner.de/index.php/Backup_Space_SSH_Keys/en](https://wiki.hetzner.de/index.php/Backup_Space_SSH_Keys/en) [https://wiki.archlinux.org/index.php/sparse_file#Resizing_the_sparse_file](https://wiki.archlinux.org/index.php/sparse_file#Resizing_the_sparse_file)
