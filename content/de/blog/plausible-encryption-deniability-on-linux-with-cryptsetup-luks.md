---
title: "Encryption Plausible Deniability on Linux with Cryptsetup LUKS"
description: "Plausible Encryption Deniability on Linux using crypsetup LUKS: How to encrypt USB drives, external drives and other storage media"
date: 2024-04-23
image: "/images/blog/luks-logo.webp"
image_alt: "Linux Unified Key Setup Logo"
---

Please [contact us](/index.html#contact "Blunix GmbH contact options") if anything is not clearly described, does not work, seems incorrect or if you require support.

This blog post provides a clear and straightforward guide on how to encrypt various storage devices - including USB sticks, external hard drives, and internal storage - for plausible encryption deniability on Linux. It focuses on using the cryptsetup tool with LUKS encryption, which is installed by default on most common Linux distributions like Ubuntu. The article details how to configure a detached LUKS header, effectively making it impossible for adversaries to prove the existence of encrypted data.

## Table of contents

- [What is Plausible Encryption Deniability?](#what-is-plausible-deniability)
- [How Plausible Is It to Carry Around Storage Devices That Have Been Overwritten With Random Data?](#how-plausible-is-plausible-deniability)
- [How to Encrypt a Storage Device for Plausible Encryption Deniability Using Cryptsetup LUKS](#how-to-encrypt-a-storage-device-for-plausible-deniability)
  - [Identifying the Correct Storage Device in Linux](#identifying-the-correct-storage-device)
  - [Overwriting the Storage Device with Random Data](#overwriting-the-storage-device-with-random-data)
  - [Why Do We Overwrite the Storage Device with Random Data?](#why-overwrite-the-storage-device-with-random-data)
  - [Working with Storage Devices Encrypted for Plausible Deniability inside a Linux Live System](#working-with-storage-devices-encrypted-for-plausible-deniability-inside-a-linux-live-system)
  - [Encrypting the Storage Device with a Detached LUKS Header](#encrypting-the-storage-device-with-a-detatched-luks-header)
- [Decrypting and Preparing the Storage Device for Storing Data](#decrypting-and-preparing-the-storage-device-for-storing-data)
- [Unmounting and Removing the Storage Device](#unmonting-and-removing-the-storage-device)
- [Decrypting the Wrong Storage Device](#decrypting-the-wrong-storage-device)
- [Inspecting the Encrypted Storage Device for Signs of Encryption](#inspecting-the-encrypted-storage-device-for-signs-of-encryption)
- [Additional Security Considerations for using Plausible Encryption Deniability](#additional-security-considerations-for-using-plausible-encryption-deniability)
  - [Do Not Use Software That is Not Installed by Default](#do-not-use-non-default-software)
  - [Access the Encrypted Device Only from a USB Live System that Does Not Permanently Save System Logs Anywhere](#access-only-from-live-linux-system-to-avoid-digital-footprints)
  - [Store the LUKS Header on a Remote Server](#store-the-luks-header-on-a-remote-server)

## [What is Plausible Encryption Deniability?](#what-is-plausible-deniability)

Plausible encryption deniability is the ability to deny the presence of encrypted data on a device convincingly. It ensures that, even under coercion, an individual can plausibly claim that no encrypted data exists beyond what has already been revealed. This is done by making a storage device that contains encrypted data seem identical to a storage device that has been overwritten with random data for the purpose of securely deleting the data that was stored on it before.

In IT it is common to overwrite storage devices, such as USB sticks, SD cards, Solid State Drives or Hard Drives, with random data several times before repurposing them. This ensures that the old data on it can not be recovered anymore. After this, the storage device can be repurposed for storing different data or discarded.

You can overwrite any storage device, such as a USB stick or external drive, with random data using the Linux tool `dd`, which is installed on most Debian based distributions by default, using the following command:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="user@workstation:~$">
<code class="language-bash">sudo dd if=/dev/urandom of=/dev/sdX status=progress</code></pre>

The "data" on the USB stick is then overwritten by `dd` with random ones and zeros. The goal of plausible encryption deniability is to make an encrypted storage media look exactly like it has been overwritten with random data in order to be repurposed or safely discarded.

When you use any encryption tool to encrypt any storage media, that encryption tool, just like cryptsetup, has to write at least a small amount of clear text data somewhere. In cryptsetup this is the so called LUKS header. In regular usage of the cryptsetup tool, this LUKS header information is stored directly on the encrypted storage device. It is responsible for verifying the encryption password and "opening" or decrypting the device. If this LUKS header information is stored directly on the device that is encrypted, it is easy to tell that the storage medium is encrypted.

By saving this "LUKS header" to a different storage device, not the one that contains the encrypted data, the only thing that remains on the actually encrypted storage device is the encrypted data, which can not be differentiated from the random data generated by the `dd` command above.

## [How Plausible Is It to Carry Around Storage Devices That Have Been Overwritten With Random Data?](#how-plausible-is-plausible-deniability)

Non-technically versed people do not commonly overwrite storage devices with random data. If anything, they "partition" the devices, which means deleting the filesystem and creating a new one. You can imagine this like using correction fluid or liquid paper to erase the table of contents in a book. After that, you create a "fresh" table of contents. This does not mean you painted over each page of the book and the "pages" (or data) is not deleted, by creating a new filesystem on the storage device, we simply erased all reference points to where the data is stored. Using data forensics tools such as https://www.cgsecurity.org/wiki/TestDisk, which comes with operating systems for such purposes such as https://www.caine-live.net/, this data can easily be recovered.

Simply formatting a USB stick does not erase the data on it. Only overwriting every byte of the storage device (preferably several times) actually makes the data unrecoverable.

Here are some good excuses reasons why you might own storage devices that have been overwritten with random data:

- Your buddy, who works in IT, previously used this USB stick for storing company data. He overwrote the USB stick with random data to securely erase confidential data before gifting it to you.
- You plan to dispose of this device, so overwriting it with random data ensures that no recoverable personal information remains.
- In accordance with your workplace policy, you overwrite storage devices with random data before repurposing them to ensure data security and compliance.
- After your system was recently compromised by malware, you decided to securely erase everything in order to get rid of this particularly persistent malware.

As you can see, each approach is a bit shady, and plausible encryption deniability might not be that plausible depending on how you explain the existence of securely erased data storage devices.

## [How to Encrypt a Storage Device for Plausible Encryption Deniability Using Cryptsetup LUKS](#how-to-encrypt-a-storage-device-for-plausible-deniability)

Creating a storage device for usage with plausible encryption deniability on Linux using cryptsetup can be achieved by following the upcoming examples.

### [Identifying the Correct Storage Device in Linux](#identifying-the-correct-storage-device)

Attach the storage device you want to encrypt to your Laptop running Linux. Then execute the following command:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="user@workstation:~$">
<code class="language-bash">lsblk --nodeps --exclude 7,11 --output NAME,PATH,MOUNTPOINTS
NAME    PATH         MOUNTPOINTS
sda     /dev/sda     /media/user/95eb4cb1-f2c8-4180-805a-d1c830a5c34f
mmcblk0 /dev/mmcblk0 
nvme0n1 /dev/nvme0n1 </code></pre>

As you can see, my laptop has three physical devices attached: a USB stick `/dev/sda`, a SD card `/dev/mmcblk0` and the laptops NVMe SSD `/dev/nvme0n1`. Refer to `man lsblk` for additional information on the command.

Make sure the third column is empty (MOUNTPOINTS). If it is not, like in the example above where it shows `/media/user/95eb4cb1-f2c8-4180-805a-d1c830a5c34f`, your device is mounted and you need to unmount it first:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="user@workstation:~$">
<code class="language-bash">sudo umount --verbose /dev/sda
umount: /mnt (/dev/sda) unmounted</code></pre>

If you are not sure which device is the correct one, use the following command:

<pre class="command-line language-bash" data-output="3-6,9-12,15-99" data-continuation-str="\" data-prompt="user@workstation:~$">
<code class="language-bash"># USB Stick
udevadm info --query=property /dev/sda
[...]
ID_MODEL=DataTraveler_3.0
[...]

# SD Card
udevadm info --query=property /dev/mmcblk0
[...]
MMC_TYPE=SD
[...]

# Laptops internal NVMe SSD
udevadm info --query=property /dev/nvme0n1
[...]
ID_MODEL=K53DVL01YA04 TOSHIBA
[...]</code></pre>

Refer to `man udevadm` for additional information on the command.

Another approach is to execute the following command before you attach the storage device to your laptop, look at its output and then attach the device. In the following example the USB stick is attached to the laptop and creates the device file `/dev/sda`:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="user@workstation:~$">
<code class="language-bash">sudo dmesg --human --color --follow
[...]
[Tue May 21 20:10:16 2024] usb 1-6: new high-speed USB device number 15 using xhci_hcd
[Tue May 21 20:10:16 2024] usb 2-6: new SuperSpeed USB device number 10 using xhci_hcd
[Tue May 21 20:10:17 2024] usb 2-6: New USB device found, idVendor=0951, idProduct=1666, bcdDevice= 1.00
[Tue May 21 20:10:17 2024] usb 2-6: New USB device strings: Mfr=1, Product=2, SerialNumber=3
[Tue May 21 20:10:17 2024] usb 2-6: Product: DataTraveler 3.0
[Tue May 21 20:10:17 2024] usb 2-6: Manufacturer: Kingston
[Tue May 21 20:10:17 2024] usb 2-6: SerialNumber: NREOLTEEBGL1VMBRKDTTCMZG
[Tue May 21 20:10:17 2024] usb-storage 2-6:1.0: USB Mass Storage device detected
[Tue May 21 20:10:17 2024] scsi host0: usb-storage 2-6:1.0
[Tue May 21 20:10:18 2024] scsi 0:0:0:0: Direct-Access     Kingston DataTraveler 3.0 PMAP PQ: 0 ANSI: 6
[Tue May 21 20:10:18 2024] sd 0:0:0:0: Attached scsi generic sg0 type 0
[Tue May 21 20:10:18 2024] sd 0:0:0:0: [sda] 30720000 512-byte logical blocks: (15.7 GB/14.6 GiB)
[Tue May 21 20:10:18 2024] sd 0:0:0:0: [sda] Write Protect is off
[Tue May 21 20:10:18 2024] sd 0:0:0:0: [sda] Mode Sense: 23 00 00 00
[Tue May 21 20:10:18 2024] sd 0:0:0:0: [sda] No Caching mode page found
[Tue May 21 20:10:18 2024] sd 0:0:0:0: [sda] Assuming drive cache: write through
[Tue May 21 20:10:18 2024] sd 0:0:0:0: [sda] Attached SCSI removable disk</code></pre>

### [Overwriting the Storage Device with Random Data](#overwriting-the-storage-device-with-random-data)

Use the following command to overwrite the storage device with random data. Replace "sda" with the correct device name! Be very careful with this! It is not possible to recover the data that was saved on this device afterwards! Note that overwriting the device with random data might take several minutes up to several hours, depending on the size of the storage device. Overwriting the storage device once is enough for the purpose of plausible encryption deniability.

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="user@workstation:~$">
<code class="language-bash">time sudo dd if=/dev/urandom of=/dev/sda status=progress
15727428096 bytes (16 GB, 15 GiB) copied, 1923 s, 8,2 MB/s 
dd: writing to '/dev/sda': No space left on device
30720001+0 records in
30720000+0 records out
15728640000 bytes (16 GB, 15 GiB) copied, 1952,6 s, 8,1 MB/s

real    32m33,017s
user    0m0,028s
sys     0m0,077s</code></pre>

As you can in the third line from the bottom, overwriting my (very old) 16 GB USB stick with random data took about 32 minutes. The error message `dd: writing to '/dev/sda': No space left on device` is expected, as we have written to the end of the storage devices capacity.

### [Why Do We Overwrite the Storage Device with Random Data?](#why-overwrite-the-storage-device-with-random-data)

While you wait for your storage device to be overwritten with random data, here is why you are doing that. As you may have noticed when creating new filesystems in the past, creating a filesystem (or setting up encryption for a storage device with cryptsetup) is much faster than overwriting it with random data. This is because creating a filesystem, such as vFAT, NTFS or ext4, only creates a "table of contents" like structure on the storage device. It does not overwrite every possible byte on the device.

When you buy a new storage device, it is commonly overwritten with zeros. If you now create a regular or encrypted filesystem and save some data on it until it is filled up to 10% capacity, 10% of the device will contain data and the other 90% of the device will still just be zeros.

The same applies when you encrypt a device - when you encrypt a storage device with cryptsetup using the default method, it will only write the LUKS header to the beginning of the device - or, if you follow this article, to a second, different storage device (not the one you want to actually encrypt). The rest of the storage space remains untouched. If you now add less data than 100% of its capacity, the device will contain a certain percentage of seemingly random data (thats the encrypted data) and the rest will just be zeros. In this case, it is more reasonable to assume that you use encryption, because there is no logical reason why only half the device would be overwritten with random data and not the rest as well.

You might have noticed that recent Linux distributions offer root disk encryption during the initial installation, and that these installers ask if you want to overwrite your laptops disk drive with random data - that is for exactly this purpose.

### [Working with Storage Devices Encrypted for Plausible Deniability inside a Linux Live System](#working-with-storage-devices-encrypted-for-plausible-deniability-inside-a-linux-live-system)

Ideally you should execute the following steps in an Ubuntu Linux live system in order to not leave digital footprints about encrypting, decrypting and working with the storage device. If you execute the following commands on your regular Linux installation of your computer, it will write informations about the devices you attached and the commands you executed to the logfiles, which are saved to your computers internal disk.

In order to create a Ubuntu live USB stick, [simply follow the official Ubuntu tutorial](https://ubuntu.com/tutorials/create-a-usb-stick-on-ubuntu "ubuntu.com: creating a bootable USB stick").

When booting such a USB stick you will be able to "try" Ubuntu as well as install it. Try in this context means to run a live Ubuntu Linux system on your computer, which does not permanently save data to the computers internal disk or the USB stick itself. All data that you save, alongside log files that the system will write when you attach storage media or execute commands, will only be saved in your computers RAM and will be lost when you shut down the computer.

### [Encrypting the Storage Device with a Detached LUKS Header](#encrypting-the-storage-device-with-a-detatched-luks-header)

To encrypt the storage device with cryptsetup LUKS, use the following command. Make sure to replace `/dev/sda` with the correct device file. Note that we write the "luksheader.img" file to the directory "/dev/shm/" - [this is a mounted Linux tmpfs](https://www.kernel.org/doc/html/latest/filesystems/tmpfs.html "www.kernel.org: information about the Linux tmpfs"), which means that files in this directory are only stored in RAM. This makes absolutely sure that the file will not be written to a physical disk.

Anwer the question of the following command "Are you sure?" with uppercase "YES". After that, enter and then verify your password. [Here are some guidelines for choosing a secure password](https://wiki.archlinux.org/title/Security#Passwords "wiki.archlinux.org: guidelines for choosing a secure password").

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="user@ubuntu-live:~$">
<code class="language-bash">sudo cryptsetup luksFormat /dev/sda --header /dev/shm/luksheader.img
WARNING!
========
Header file does not exist, do you want to create it?

Are you sure? (Type 'yes' in capital letters): YES
Enter passphrase for luksheader.img: 
Verify passphrase: </code></pre>

As you can see with the following command, we are now left with a file called "luksheader.img" that is 16 MB in size:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="user@ubuntu-live:~$">
<code class="language-bash">ls -lah /dev/shm/luksheader.img 
-rw------- 1 root root 16M Apr 23 10:27 /dev/shm/luksheader.img</code></pre>

Permanently store this file in a secure location, such as a uploading it to a trusted remote server using SSH. If you carry this file with you while your encrypted storage devices are being inspected, your encryption will not be plausible. While there is no way to associate which physical storage device the luksheader.img file belongs to without having the password for it, having such a file in your posession is abviously proof of you attempting to implement plausible encryption deniability.

If you loose this file, your data is lost, and there is nothing you can do about it. Refer [to the cryptsetup project wiki](https://gitlab.com/cryptsetup/cryptsetup/-/wikis/FrequentlyAskedQuestions#6-backup-and-data-recovery "gitlab.com/cryptsetup: cryptsetup project wiki") for additional information.

## [Decrypting and Preparing the Storage Device for Storing Data](#decrypting-and-preparing-the-storage-device-for-storing-data)

To decrypt the storage device using the detatched LUKS header, use the following command:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="user@ubuntu-live:~$">
<code class="language-bash">sudo cryptsetup --verbose luksOpen /dev/sda my-secret-storage --header=/dev/shm/luksheader.img
No usable token is available.
Enter passphrase for /dev/sda: 
Key slot 0 unlocked.
Command successful.</code></pre>

The warning message "No usable token is available." can be ignored, as you can read from the last line, the decryption was successful.

We have now created a new, decrypted storage device file at `/dev/mapper/my-secret-storage`. This device file does not contain a filesystem yet. We can choose any filesystem we like to format the decrypted device, in this case ext4:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="user@ubuntu-live:~$">
<code class="language-bash">sudo mkfs.ext4 /dev/mapper/my-secret-storage 
mke2fs 1.47.0 (5-Feb-2023)
Creating filesystem with 3840000 4k blocks and 960992 inodes
Filesystem UUID: 1e1c9470-711d-4986-b1b5-8d84f3a06c79
Superblock backups stored on blocks: 
	32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (16384 blocks): done
Writing superblocks and filesystem accounting information: done   </code></pre>

We can now mount the new filesystem so we can start saving data to it:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="user@ubuntu-live:~$">
<code class="language-bash">sudo mount -v /dev/mapper/my-secret-storage /mnt
mount: /dev/mapper/my-secret-storage mounted on /mnt.</code></pre>

We should change the permissions of this directory so that the regular Linux user, which is the one that runs your graphical desktop environment, is able to store data on it:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="user@ubuntu-live:~$">
<code class="language-bash">sudo chown --recursive $USER:$USER /mnt</code></pre>

You can now store your data inside the directory "/mnt/", which will write it encrypted to "/dev/sda".

## [Unmounting and Removing the Storage Device](#unmonting-and-removing-the-storage-device)

When writing your data to the decrypted storage device, some of that data will be cached by Linux in order to improve write speed, and not actually be written to disk right away. Before removing any (external) storage devices from your Linux computer, you have to un-mount them first:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="user@ubuntu-live:~$">
<code class="language-bash">sudo umount -v /mnt
umount: /mnt unmounted</code></pre>

After this, you have to "close" the cryptsetup LUKS encryption - this means that the device will not be decrypted anymore and you will need both the "luksheader.img" file and the password to decrypt it again.

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="user@ubuntu-live:~$">
<code class="language-bash">sudo cryptsetup --verbose luksClose /dev/mapper/my-secret-storage
Command successful.</code></pre>

It is equally save to simply shutdown the Ubuntu live system instead of un-mounting and luksClosing the encrypted disk. During the shutdown operation, the live Ubuntu will take care of un-mounting and closing the LUKS encryption for you. You should preferably not just "switch off", as in remove power to the computer, as this might lead to data corruption with the files you have been working with while the computer was running. Simply disconnecting power or keeping the power button pressed until the computer turns off does not pose a risk to the strength and effectiveness of the encryption however.

## [Decrypting the Wrong Storage Device](#decrypting-the-wrong-storage-device)

Note that because we are storing the LUKS header in an additional file instead of directly on the storage device we encrypt, we can take any other storage device and "open" it successfully using this header. In the following example I will use a SD card, which I have freshly overwritten with random data. I will enter the same password that I used to encrypt the USB stick from the examples above.

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="user@ubuntu-live:~$">
<code class="language-bash">sudo dd if=/dev/urandom of=/dev/mmcblk0 status=progress
[...]

sudo cryptsetup --verbose luksOpen /dev/mmcblk0 my-secret-storage --header=/dev/shm/luksheader.img
No usable token is available.
Enter passphrase for /dev/mmcblk0: 
Key slot 0 unlocked.
Command successful.</code></pre>

As you can see the command was successful, meaning that there now also is a device file "/dev/mapper/my-secret-storage". As this isn't the USB stick I was working with in the previous examples, there obviously also isn't any of the data I stored on it earlier. This proofs that the storage device itself is irrelevant to the decryption operation. All information we need to decrypt THE DATA on the USB stick I created above is stored inside the file "luksheader.img". Cryptsetup itself does not have any understanding of what this data is exactly. You can imagine cryptsetup in combination with the "luksheader.img" as a sort of pipe, in which you put data on one end and on the other end (the physical storage device) it outputs "scrambled data". There is not way to determine if this data is encrypted data or random data generated by overwriting the storage device with random data.

## [Inspecting the Encrypted Storage Device for Signs of Encryption](#inspecting-the-encrypted-storage-device-for-signs-of-encryption)

Lets closely inspect the USB stick we used to store encrypted data in the examples above.

Checking the USB stick for a [file signature, also called the magic file header](https://en.wikipedia.org/wiki/List_of_file_signatures "en.wikipedia.org: information about file signatures"), only shows "data" as output:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="user@workstation:~$">
<code class="language-bash">sudo file --special-files --keep-going /dev/sda
/dev/sda: data</code></pre>

For comparison, here is the same command for a disk that is encrypted with cryptsetup without detaching the LUKS header. Note that the output of the command is cut off at the end... I sadly found no option in `man file` to prevent this.

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="user@workstation:~$">
<code class="language-bash">sudo file --special-files --keep-going /dev/nvme0n1
/dev/nvme0n1p3: LUKS encrypted file, ver 2, header size 16384, ID 3, algo sha256, salt 0x7aijqgdczmhftkkxc..., UUID: 07821642-7aa5-47bb-a84a-8e195eba5b0b, crc 0xzkadqosdmifby2evn..., at 0x1000 {"keyslots":{"0":{"type":"luks2","key_size":64,"af":{"type":"luks1","stripes":4000,"hash":"sha256"},"area":{"type":"raw","offse</code></pre>

Checking the encrypted USB stick for a partition table revelas nothing:

<pre class="command-line language-bash" data-output="2-7,9-15,17-99" data-continuation-str="\" data-prompt="user@workstation:~$">
<code class="language-bash">sudo fdisk -l /dev/sda
Disk /dev/sda: 14,65 GiB, 15728640000 bytes, 30720000 sectors
Disk model: DataTraveler 3.0
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes

sudo parted -l /dev/sda
Error: /dev/sda: unrecognised disk label
Model: Kingston DataTraveler 3.0 (scsi)                                   
Disk /dev/sda: 15,7GB
Sector size (logical/physical): 512B/512B
Partition Table: unknown
Disk Flags: 

sudo lsblk /dev/sda
NAME MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sda    8:0    1 14,6G  0 disk </code></pre>

Even checking if the storage device is encrypted using the `cryptsetup` command returns false, because we did not store the LUKS header on the storage device itself:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="user@workstation:~$">
<code class="language-bash">sudo cryptsetup isLuks /dev/sda --debug
# cryptsetup 2.6.1 processing "cryptsetup isLuks /dev/sda --debug"
# Verifying parameters for command isLuks.
# Running command isLuks.
# Installing SIGINT/SIGTERM handler.
# Unblocking interruption on signal.
# Allocating context for crypt device /dev/sda.
# Trying to open and read device /dev/sda with direct-io.
# Initialising device-mapper backend library.
# Trying to load any crypt type from device /dev/sda.
# Crypto backend (OpenSSL 3.0.8 7 Feb 2023 [default][legacy]) initialized in cryptsetup library version 2.6.1.
# Detected kernel Linux 6.2.0-39-generic x86_64.
# Loading LUKS2 header (repair disabled).
# Acquiring read lock for device /dev/sda.
# Opening lock resource file /run/cryptsetup/L_8:0
# Verifying lock handle for /dev/sda.
# Device /dev/sda READ lock taken.
# Trying to read primary LUKS2 header at offset 0x0.
# Opening locked device /dev/sda
# Verifying locked device handle (bdev)
# Trying to read secondary LUKS2 header at offset 0x4000.
# Reusing open ro fd on device /dev/sda
# Trying to read secondary LUKS2 header at offset 0x8000.
# Reusing open ro fd on device /dev/sda
# Trying to read secondary LUKS2 header at offset 0x10000.
# Reusing open ro fd on device /dev/sda
# Trying to read secondary LUKS2 header at offset 0x20000.
# Reusing open ro fd on device /dev/sda
# Trying to read secondary LUKS2 header at offset 0x40000.
# Reusing open ro fd on device /dev/sda
# Trying to read secondary LUKS2 header at offset 0x80000.
# Reusing open ro fd on device /dev/sda
# Trying to read secondary LUKS2 header at offset 0x100000.
# Reusing open ro fd on device /dev/sda
# Trying to read secondary LUKS2 header at offset 0x200000.
# Reusing open ro fd on device /dev/sda
# Trying to read secondary LUKS2 header at offset 0x400000.
# Reusing open ro fd on device /dev/sda
# LUKS2 header read failed (-22).
# Device /dev/sda READ lock released.
# Releasing crypt device /dev/sda context.
# Releasing device-mapper backend.
# Closing read only fd for /dev/sda.
Command failed with code -1 (wrong or missing parameters).</code></pre>

Only if we run the `cryptsetup isLuks` command on the "luksheader.img" itself, cryptsetup will return that it is using cryptsetup encryption - note the last line of the output of the command above and below.

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="user@workstation:~$">
<code class="language-bash">sudo cryptsetup isLuks /dev/shm/luksheader.img --debug
# cryptsetup 2.6.1 processing "cryptsetup isLuks /dev/shm/luksheader.img --debug"
# Verifying parameters for command isLuks.
# Running command isLuks.
# Installing SIGINT/SIGTERM handler.
# Unblocking interruption on signal.
# Allocating context for crypt device /dev/shm/luksheader.img.
# Trying to open and read device /dev/shm/luksheader.img with direct-io.
# Trying to open device /dev/shm/luksheader.img without direct-io.
# Initialising device-mapper backend library.
# Trying to load any crypt type from device /dev/shm/luksheader.img.
# Crypto backend (OpenSSL 3.0.8 7 Feb 2023 [default][legacy]) initialized in cryptsetup library version 2.6.1.
# Detected kernel Linux 6.2.0-39-generic x86_64.
# Loading LUKS2 header (repair disabled).
# Acquiring read lock for device /dev/shm/luksheader.img.
# Verifying lock handle for /dev/shm/luksheader.img.
# Device /dev/shm/luksheader.img READ lock taken.
# Trying to read primary LUKS2 header at offset 0x0.
# Opening locked device /dev/shm/luksheader.img
# Verifying locked device handle (regular file)
# LUKS2 header version 2 of size 16384 bytes, checksum sha256.
# Checksum:18b1b8177a99f265a633dd87f4486673ba2f392979e4b9b1a87f5d3c3738d1d6 (on-disk)
# Checksum:18b1b8177a99f265a633dd87f4486673ba2f392979e4b9b1a87f5d3c3738d1d6 (in-memory)
# Trying to read secondary LUKS2 header at offset 0x4000.
# Reusing open ro fd on device /dev/shm/luksheader.img
# LUKS2 header version 2 of size 16384 bytes, checksum sha256.
# Checksum:3b1dd3ed097221b7c35e82c25f03680efca776d5847456a64c4e07fc7fc081e3 (on-disk)
# Checksum:3b1dd3ed097221b7c35e82c25f03680efca776d5847456a64c4e07fc7fc081e3 (in-memory)
# Device size 16777216, offset 16777216.
# Device /dev/shm/luksheader.img READ lock released.
# PBKDF argon2id, time_ms 2000 (iterations 0), max_memory_kb 1048576, parallel_threads 4.
# Releasing crypt device /dev/shm/luksheader.img context.
# Releasing device-mapper backend.
# Closing read only fd for /dev/shm/luksheader.img.
Command successful.</code></pre>

## [Additional Security Considerations for using Plausible Encryption Deniability](#additional-security-considerations-for-using-plausible-encryption-deniability)

Make sure to follow the following security guidelines when handling storage devices which are encrypted in the way described in this blogpost.

### [Do Not Use Software That is Not Installed by Default](#do-not-use-non-default-software)

Apart from the fact that you should not interact with your encrypted device on the operating system that is installed on your computers internal disk - if additional encryption tools like VeraCrypt are installed on your computers internal drive and you are being forced to decrypt the laptop, having such additional tools installed will appear suspicious. Cryptsetup is installed by default on most recent Linux distributions like Ubuntu, and encrypting your computers internal drive is common, as it is (an optional) part of the default installation.

### [Access the Encrypted Device Only from a USB Live System that Does Not Permanently Save System Logs Anywhere](#access-only-from-live-linux-system-to-avoid-digital-footprints)

When you attach, decrypt and use a storage device with a regular Ubuntu installation, it will write logs about this to disk. To avoid leaving such digital footprints, boot from a regular Ubuntu live USB stick to access the encrypted device. Every Ubuntu installer first boots into a normal live system, from which you can "check out Ubuntu before deciding to install it or not".

Avoid mounting your computers internal disks from this USB stick, as this could leave detectable traces in the file system of the laptops disk. If you want to be paranoid, physically remove your laptops internal disk before booting the USB live stick. Ubuntu might try to auto-mount the laptops internal disk during regular usage.

There are [digital forensics focused Linux distributions such as caine-live](https://www.caine-live.net/ "www.caine-live.net: digital forensics live system"), but carrying this around on a bootable USB stick will look suspicious. Owning a Ubuntu live USB stick, which contains the default way to install Ubuntu, can simply be explained by a "sometimes slow and regularly crashing system" and the desire do a fresh install soon.

### [Store the LUKS Header on a Remote Server](#store-the-luks-header-on-a-remote-server)

Do not carry the LUKS header around with you. Instead, upload it to a trusted server using SSH and commit the SSH password to memory. This strategy avoids the need to store an SSH private key somewhere and having to carry it with you, which might also look suspicious. Do not save a SSH private key on your computers internal drive and access it from the live system - mounting your computers internal drive from the live system can leave digital footprints.
