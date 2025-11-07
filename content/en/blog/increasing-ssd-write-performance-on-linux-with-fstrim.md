---
title: "Increasing SSD Performance on Linux using Fstrim"
description: "This blog post explains what the tool fstrim is used for, why it runs weekly by default on most Linux distributions and how it helps speed up SSDs"
date: 2024-04-25
image: "/images/blog/ssd.webp"
image_alt: "Image of a SSD"
---

Please [contact us](/index.html#contact "Blunix GmbH contact options") if anything is not clearly described, does not work, seems incorrect or if you require support.

## Table of contents

- [What does the Linux `fstrim` Command do?](#what-is-linux-fstrim)
- [How an SSD Saves Data Compared to a HDD](#how-a-ssd-saves-data-compared-to-a-hdd)
- [How an SSD Writes to Blocks That Have Never Been Written to Before](#how-a-ssd-writes-to-blocks-that-have-been-written-to-before)
- [Why SSD's Need Trimming to Optimize Write Speed](#why-ssds-need-trimming-to-optimize-write-speed)
- [Additional Considerations like Wear Leveling](#additional-considerations)
- [Summary](#summary)

## [What does the Linux `fstrim` Command do?](#what-is-linux-fstrim)

`fstrim` is a command in Linux used to inform the filesystem to discard unused blocks on a solid-state drive (SSD), Non-Volatile Memory Drive (NVME) or thinly-provisioned storage device.

## [How an SSD Saves Data Compared to a HDD](#how-a-ssd-saves-data-compared-to-a-hdd)

Solid-state drives (SSD's) work differently from traditional hard disk drives (HDDs) in terms of how they handle data storage and modification.

Unlike HDDs, which can overwrite existing data directly, SSD's use a technology called [NAND (Not AND) flash memory](https://en.wikipedia.org/wiki/Flash_memory "wikipedia.org: Flash Memory") to store data, which requires a more complex process for writing and modifying data.

## [How an SSD Writes to Blocks That Have Never Been Written to Before](#how-a-ssd-writes-to-blocks-that-have-been-written-to-before)

In NAND flash memory, data is stored in memory cells organized into blocks. These blocks must be erased before new data can be written to them. When files are deleted or modified on an SSD, the blocks previously occupied by those files may still contain data, even though the files are no longer present in the filesystem. This leftover data is known as "garbage" or "stale data."

This means that when new data needs to be written, and the availble block still contains existing data, the controller must first erase it before writing the new data. This takes more time than simply writing the data to an already erased block. This phenomenon is referred to as (one of several causes for) write amplification in SSDs.

## [Why SSD's Need Trimming to Optimize Write Speed](#why-ssds-need-trimming-to-optimize-write-speed)

When data is deleted or modified on an SSD, the filesystem may mark the corresponding blocks as free, but the SSD's controller may not immediately erase the block that become available by the deletion of the file. This is because filesystems only remove the "reference points", as in the metadata information about where on the disk the file is stored instead of overwriting the file completely. As an example, it takes lots of time to save a 10GB file, but only half a second to delete it. This proofs that not all of the 10GB have been overwritten, but only the metadata about the files existance is deleted while the data itself remains untouched and is still saved on the disk - it is just not accessible anymore by the filesystem.

As a result, when new data is written to the SSD, the controller may need to first erase these previously-used blocks before writing the new data. This makes the write operation that is requested by the operating system of the computer slower.

By running `fstrim` and informing the SSD's controller of the blocks that are no longer in use, the controller can proactively erase these blocks during idle periods, for example at night when your webservers database is less busy.

This operation is executed by default on Debian and Ubuntu Linux every Monday at 00:00 am, which is triggered by the following systemd timer (a systemd replacement for Cron jobs). For additional details on systemd timers refer to [our blogpost explaining systemd timers in detail](ultimate-tutorial-about-systemd-timers.html "The Ultimate Tutorial About Systemd Timers - The Replacement for Cron Jobs").

<pre class="command-line language-bash" data-output="2-16,18-99" data-continuation-str="\" data-user="user" data-host="workstation">
<code class="language-bash">cat /etc/systemd/system/timers.target.wants/fstrim.timer
[Unit]
Description=Discard unused blocks once a week
Documentation=man:fstrim
ConditionVirtualization=!container
ConditionPathExists=!/etc/initrd-release

[Timer]
OnCalendar=weekly
AccuracySec=1h
Persistent=true
RandomizedDelaySec=6000

[Install]
WantedBy=timers.target

cat /usr/lib/systemd/system/fstrim.service
[Unit]
Description=Discard unused blocks on filesystems from /etc/fstab
Documentation=man:fstrim(8)
ConditionVirtualization=!container

[Service]
Type=oneshot
ExecStart=/sbin/fstrim --listed-in /etc/fstab:/proc/self/mountinfo --verbose --quiet-unsupported
PrivateDevices=no
PrivateNetwork=yes
PrivateUsers=no
ProtectKernelTunables=yes
ProtectKernelModules=yes
ProtectControlGroups=yes
MemoryDenyWriteExecute=yes
SystemCallFilter=@default @file-system @basic-io @system-service</code></pre>

## [Additional Considerations like Wear Leveling](#additional-considerations)

Depending on the SSD's internal controllers operations and algorithms, the execution of `fstrim` triggers background processes in the SSDs controller, such as garbage collection or wear leveling, which results in additional write operations on the SSD.

Wear leveling is a technique used in solid-state drives (SSDs) to distribute write and erase cycles evenly across the [NAND flash memory](https://en.wikipedia.org/wiki/Flash_memory "wikipedia.org: Flash Memory") cells. The controller dynamically allocates data to different cells, ensuring that each cell is written to and erased an approximately equal number of times over the SSD's lifespan. This prevents certain cells from experiencing excessive wear, thus extending the overall lifespan of the SSD.

While the primary purpose of `fstrim` is to reduce write amplification by informing the SSD's controller of unused blocks, the execution of `fstrim` itself may introduce a small amount of write amplification due to these background processes. However, the overall impact on write amplification is typically minimal compared to the benefits of running `fstrim` to optimize SSD performance.

## [Summary](#summary)

In summary, trimming your SSD regularly using the `fstrim` command helps to maintain a consistent write performance for SSD's by performing deletions of freed up blocks during times where write speed is less required (for example during nighttime, while your database server has less load). minimizing unnecessary write operations and reducing the impact of write amplification, ultimately prolonging the lifespan of the SSD.
