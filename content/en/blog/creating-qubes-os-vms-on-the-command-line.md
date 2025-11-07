---
title: "How to Create Qubes OS VMs Using the Command Line"
description: "How to create Qubes OS VMs on the command line using the Qubes management tools qvm-create, qvm-clone, qvm-prefs, qubes-prefs, qvm-run and qvm-volume"
date: 2024-05-21
image: "/images/blog/qubes-os-logo.webp"
image_alt: "Qubes OS Logo"
---

Please [contact us](/index.html#contact "Blunix GmbH contact options") if anything is not clearly described, does not work, seems incorrect or if you require support.

This tutorial explains how to create Qubes OS VMs on the command line using the Qubes management tools `qvm-create `, `qvm-clone `, `qvm-prefs `, `qubes-prefs `, `qvm-run ` and `qvm-volume `.

It explains in detail how to create the different classes of Qubes OS VMs, namely TemplateVMs, AppVMs, DispVMs and StandaloneVMs, how to customize TemplateVMs by installing and configuring software, how root and private disks are used and cloned for each type of VM and how different Qubes Prefs can be used to modify the Qubes internal usage and behavior of VMs.

## Table of contents

- [Different VM Classes in Qubes](#different-vm-classes-in-qubes)
  - [Viewing the VM Class on the Command Line](#viewing-the-vm-class-on-the-command-line)
- [Creating a New TemplateVM](#creating-a-new-templatevm)
  - [Installing Additional Templates](#installing-additional-templates)
  - [Cloning an Existing TemplateVM](#cloning-an-existing-templatevm)
  - [Setting qvm-prefs for the New TemplateVM](#setting-qvm-prefs-for-the-new-templatevm)
    - [Viewing All Prefs of a TemplateVM](#viewing-all-prefs-of-a-templatevm)
    - [Viewing a specific Pref](#viewing-a-specific-pref)
    - [Settings Prefs](#settings-prefs)
  - [Qvm-Prefs Inherited from the Original TemplateVM](#qvm-prefs-inherited-from-the-original-templatevm)
  - [Resizing the Root Disk of a Template VM](#resizing-the-root-disk-of-a-template-vm)
  - [LVM Volumes used by Template VMs](#lvm-volumes-used-by-template-vms)
  - [Customizing a Template VM by Installing and Configuring Software like apt Packages](#customizing-a-template-vm-by-installing-and-configuring-software-like-apt-packages)
    - [Executing Simple Commands in a VM with qvm-run](#executing-simple-commands-in-a-vm-with-qvm-run)
    - [Insecure: Showing the Output of qvm-run Commands](#insecure-showing-the-output-of-qvm-run-commands)
    - [Insecure: Using qvm-run –pass-io to Execute Whole Scripts](#insecure-using-qvm-run-pass-io-to-execute-whole-scripts)
    - [Secure but Unpractical: Using a Graphical Terminal Emulator Inside the VM to Execute Commands](#secure-but-unpractical-using-a-graphical-terminal-emulator-inside-the-vm-to-execute-commands)
    - [Secure: Using a Graphical Terminal Emulator Inside the VM to Execute Scripts](#secure-using-a-graphical-terminal-emulator-inside-the-vm-to-execute-scripts)
    - [Practical and Secure: Using a Script to Copy a Script to the VM and then Start a Graphical Terminal Emulator Inside the VM that Executes a Script](#practical-and-secure-using-a-script-to-copy-a-script-to-the-vm-and-then-start-a-graphical-terminal-emulator-inside-the-vm-that-executes-a-script)
  - [Shutting down the TemplateVM](#shutdown-the-templatevm)
- [Creating an AppVM from a Template VM](#creating-an-appvm-from-a-template-vm)
  - [Commonly Used Prefs for AppVMs](#commonly-used-prefs-for-appvms)
  - [Resizing the home parition of an AppVM](#resizing-the-home-parition-of-an-appvm)
  - [LVM Volumes used by App VMs](#lvm-volumes-used-by-app-vms)
  - [Making Persistent Modifications in AppVMs](#making-persistent-modifications-in-appvms)
  - [Qvm-Prefs Inherited from the TemplateVM](#qvm-prefs-inherited-from-the-templatevm)
- [Creating Disposable VMs from App VMs](#creating-disposable-vms-from-app-vms)
  - [Creating Regular Disposable VMs](#creating-regular-disposable-vms)
  - [Creating a Named Disposable VM](#creating-a-named-disposable-vm)
  - [Qvm-Prefs Inherited from the TemplateVM](#qvm-prefs-inherited-from-the-templatevm-1)
  - [LVM Volumes used by Disposable VMs](#lvm-volumes-used-by-disposable-vms)
- [Creating a StandaloneVM From a TemplateVM](#creating-a-standalonevm-from-a-templatevm)
  - [Qvm-Prefs Inherited from the TemplateVM](#qvm-prefs-inherited-from-the-templatevm-2)
  - [LVM Volumes used by Disposable VMs](#lvm-volumes-used-by-disposable-vms-1)

## [Different VM Classes in Qubes](#different-vm-classes-in-qubes)

In Qubes OS, each application or group of similar applications run in their own virtual machine (VM). This provides robust security by compartmentalization. This blog post introduces the different classes of VMs in Qubes OS and guides you through the process of creating each type using the Qubes command-line VM management tools.

Qubes OS categorizes VMs into several classes:

- **TemplateVM**
  : is “simply a VM”, but can only be used for the creation of AppVMs or StandaloneVMs

- **StandaloneVM**
  : is a copy of TemplateVM that can be used like a regular VM

- **AppVM**
  : uses a disk snapshot of its TemplateVM when running and has persistent disks for special directories like
  `/home/ `
  , but discards changes to all other directories at shutdown

- **DispVM (disposable VM)**
  : is a snapshot of an AppVMs that discards all data at shutdown

For additional information refer to [the Qubes OS documentation gloassry.](https://www.qubes-os.org/doc/glossary/ "qubes-os.org: documentation glossary")

### [Viewing the VM Class on the Command Line](#viewing-the-vm-class-on-the-command-line)

You can view the class of a VM with the following command. In this example the class of the VM “debian-12-xfce”, which is installed by default with Qubes 4.2, is “TemplateVM”. This template contains debian and a large choice of installed applications, like gnome-terminal, firefox, thunderbird and so on.

<pre class="command-line language-bash" data-output="2-999" data-continuation-str="\" data-user="user" data-host="dom0">
<code class="language-bash">qvm-ls debian-12-xfce
NAME            STATE    CLASS       LABEL  TEMPLATE  NETVM
debian-12-xfce  Running  TemplateVM  black  -         -</code></pre>

The work VM, which the Qubes OS installer creates by default, is an AppVM which uses the TemplateVM “fedora-39-xfce”. You can start firefox in the work VM, and its changes below the `/home/ ` directory, for example when saving a bookmark in firefox or downloading a file to `~/Downloads/ `, are persistent. When You save anything outside the `/home/ `, `/rw/ ` or `/usr/local/ ` directory in an AppVM, the changes are lost when you shut down the VM.

<pre class="command-line language-bash" data-output="2-999" data-continuation-str="\" data-user="user" data-host="dom0">
<code class="language-bash">qvm-ls work
NAME  STATE    CLASS  LABEL  TEMPLATE        NETVM
work  Running  AppVM  blue   fedora-39-xfce  sys-firewall</code></pre>

AppVMs can also serve as templates for Disposable VMs. Disposable VMs do not preserve any changes across reboots, regardless in which directory you save or change files:

<pre class="command-line language-bash" data-output="2-999" data-continuation-str="\" data-user="user" data-host="dom0">
<code class="language-bash">qvm-ls sys-net
NAME         STATE    CLASS   LABEL   TEMPLATE         NETVM
sys-net      Running  DispVM  purple  fedora-39-xfce   -</code></pre>

StandaloneVMs are copies of TemplateVMs. After their creation, they do not have a template themselves, and all changes below all directories are persistently saved. It is basically just a normal VM.

<pre class="command-line language-bash" data-output="2-999" data-continuation-str="\" data-user="user" data-host="dom0">
<code class="language-bash">qvm-ls code-playground
NAME             STATE    CLASS         LABEL  TEMPLATE  NETVM
code-playground  Running  StandaloneVM  green  -         sys-firewall</code></pre>

## [Creating a New TemplateVM](#creating-a-new-templatevm)

When creating new VMs in Qubes OS, everything starts with the Template VM. In most cases you can simply use the default templates debian-12-xfce, which already contains a large number of packages.

It is HIGHLY recommended not to modify the default template VMs, except for apt security upgrades. If the default templates miss a package you need, you should create a new template VM. A new template VM is created by creating a copy of an existing template VM.

### [Installing Additional Templates](#installing-additional-templates)

The template VMs “debian-12-xfce” and “fedora-39-xfce” are installed on Qubes OS by default.

If you want smaller sized images that only contain the software you want, you can start with the debian minimal image, which can be installed with the following command:

<pre class="command-line language-bash" data-output="2-999" data-continuation-str="\" data-user="user" data-host="dom0">
<code class="language-bash">sudo qubes-dom0-update --enablerepo=qubes-templates-itl-testing qubes-template-debian-12-minimal</code></pre>

You can view all available templates [in the Qubes OS documentation on templates](https://www.qubes-os.org/doc/templates/ "qubes-os.org: template documentation").

### [Cloning an Existing TemplateVM](#cloning-an-existing-templatevm)

Lets say we want to create a VM to run firefox. Clone an existing template VM to create a new VM called template-firefox:

<pre class="command-line language-bash" data-output="2-999" data-continuation-str="\" data-user="user" data-host="dom0">
<code class="language-bash">qvm-clone debian-12-minimal template-firefox</code></pre>

### [Setting qvm-prefs for the New TemplateVM](#setting-qvm-prefs-for-the-new-templatevm)

Qubes prefs (preferences) are configuration settings that define the behavior and characteristics of virtual machines in Qubes OS. These settings control various aspects such as networking options, available memory, disk sizes and so on.

#### [Viewing All Prefs of a TemplateVM](#viewing-all-prefs-of-a-templatevm)

You can view all prefs of a VM with the qvm-prefs tool:

<pre class="command-line language-bash" data-output="2-999" data-continuation-str="\" data-user="user" data-host="dom0">
<code class="language-bash">qvm-prefs template-firefox
audiovm             D  dom0
autostart           D  False
backup_timestamp    U
debug               D  False
default_dispvm      D  default-dvm
default_user        D  user
dns                 D  10.139.1.1 10.139.1.2
gateway             D  
gateway6            D  
guivm               D  dom0
icon                D  templatevm-orange
include_in_backups  D  True
installed_by_rpm    D  False
ip                  D  10.137.0.13
ip6                 D  
kernel              D  6.6.29-1.fc37
kernelopts          D  swiotlb=2048
keyboard_layout     D  us++
klass               D  TemplateVM
label               -  orange
mac                 D  00:16:3e:5e:6c:00
management_dispvm   D  default-mgmt-dvm
maxmem              -  4096
memory              -  1024
name                -  template-firefox
netvm               -  sys-firewall
provides_network    D  False
qid                 -  13
qrexec_timeout      D  60
shutdown_timeout    D  60
start_time          D  
stubdom_mem         U
stubdom_xid         D  -1
updateable          D  True
uuid                -  d5455b0d-35b7-4d2d-9ded-cc0eb4849c03
vcpus               -  2
virt_mode           -  hvm
visible_gateway     D  10.138.21.233
visible_gateway6    D  
visible_ip          D  10.137.0.13
visible_ip6         D  
visible_netmask     D  255.255.255.255
xid                 D  -1</code></pre>

#### [Viewing a specific Pref](#viewing-a-specific-pref)

To view a specific pref, simply add it at the end of the command - the k in class is not a spelling error, thats how Qubes OS writes it.

<pre class="command-line language-bash" data-output="2-999" data-continuation-str="\" data-user="user" data-host="dom0">
<code class="language-bash">qvm-prefs template-firefox klass
TemplateVM</code></pre>

#### [Settings Prefs](#settings-prefs)

You can set prefs by giving the desired value of the pref as third argument:

<pre class="command-line language-bash" data-output="2-3,5,7" data-continuation-str="\" data-user="user" data-host="dom0">
<code class="language-bash">qvm-prefs template-firefox memory
400

qvm-prefs template-firefox memory 2048

qvm-prefs template-firefox memory
2048</code></pre>

Note that different classes of VMs have some slightly different pref keys - some keys like “memory” are available for all types of VMs, while “template_for_dispvms” for example is only availble to AppVMs.

To get a detailed explaination of each pref that is availble for the “template-firefox” VM, use the following command:

<pre class="command-line language-bash" data-output="2-999" data-continuation-str="\" data-user="user" data-host="dom0">
<code class="language-bash">qvm-prefs template-firefox --help-properties</code></pre>

### [Qvm-Prefs Inherited from the Original TemplateVM](#qvm-prefs-inherited-from-the-original-templatevm)

When creating a new TemplateVM from an existing TemplateVMs, the new TemplateVMs inherits all prefs of the original TemplateVM.

Identical prefs:

```plaintext
audiovm             dom0
autostart           False
backup_timestamp
debug               False
default_dispvm      default-dvm
default_user        user
dns                 10.139.1.1
gateway
gateway6
guivm               dom0
icon                templatevm-orange
include_in_backups  True
installed_by_rpm    False
ip6
kernel              6.6.29-1.fc37
kernelopts          swiotlb=2048
keyboard_layout     us++
klass               TemplateVM
label               orange
mac                 00:16:3e:5e:6c:00
management_dispvm   default-mgmt-dvm
maxmem              4096
memory              1024
netvm               None
provides_network    False
qrexec_timeout      60
shutdown_timeout    60
start_time
stubdom_mem
stubdom_xid         -1
updateable          True
vcpus               2
virt_mode           hvm
visible_gateway6
visible_ip6
visible_netmask     255.255.255.255
xid                 -1
```

Different prefs:

```plaintext
< Prefs of the original TemplateVM
> Prefs of the new TemplateVM

< ip                  10.137.0.13
> ip                  10.137.0.22

< name                debian-12-xfce
> name                template-test

< qid                 13
> qid                 22

< uuid                d5455b0d-35b7-4d2d-9ded-cc0eb4849c03
> uuid                73e8cf04-8827-417d-ad90-6c36e8e5b398

< visible_gateway     10.138.21.233
> visible_gateway     10.137.0.8

< visible_ip          10.137.0.13
> visible_ip          10.137.0.22
```

### [Resizing the Root Disk of a Template VM](#resizing-the-root-disk-of-a-template-vm)

You can resize the VMs disk for the root directory directory, which by default is 20 GB. The `/home/` directory of a TemplateVM is not used when creating the `/home/` directory of an AppVM. Instead the `/etc/skel` directory of the TemplateVM is used to populate the `/home/` directory in the AppVM. Hence all changes to the TemplateVMs `/home/` directory are pretty much irrelevant.

<pre class="command-line language-bash" data-output="2-999" data-continuation-str="\" data-user="user" data-host="dom0">
<code class="language-bash">qvm-volume resize template-firefox:root 50G</code></pre>

### [LVM Volumes used by Template VMs](#lvm-volumes-used-by-template-vms)

Qubes OS uses LVM to create logical volumes for each VM. Here is how you show all volumes used by a TemplateVM:

<pre class="command-line language-bash" data-output="2-999" data-continuation-str="\" data-user="user" data-host="dom0">
<code class="language-bash">sudo lvdisplay /dev/qubes_dom0/vm-template-firefox-*   
  --- Logical volume ---
  LV Path                /dev/qubes_dom0/vm-template-firefox-root
  LV Name                vm-template-firefox-root
  VG Name                qubes_dom0
  LV UUID                ZrYB1S-eEd7-RMeG-uIdA-7OXM-ZszW-fGPJxT
  LV Write Access        read/write
  LV Creation host, time dom0, 2024-05-14 16:12:13 -0400
  LV Pool name           vm-pool
  LV Thin origin name    vm-template-firefox-root-1715717848-back
  LV Status              available
  # open                 0
  LV Size                20.00 GiB
  Mapped size            8.99%
  Current LE             5120
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     2048
  Block device           253:84

  --- Logical volume ---
  LV Path                /dev/qubes_dom0/vm-template-firefox-private
  LV Name                vm-template-firefox-private
  VG Name                qubes_dom0
  LV UUID                SGON7W-Rg38-De6v-Aufe-ZJ3n-Es0A-TelCXc
  LV Write Access        read/write
  LV Creation host, time dom0, 2024-05-14 16:12:13 -0400
  LV Pool name           vm-pool
  LV Thin origin name    vm-template-firefox-private-1715717848-back
  LV Status              available
  # open                 0
  LV Size                2.00 GiB
  Mapped size            4.98%
  Current LE             512
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     2048
  Block device           253:56</code></pre>

### [Customizing a Template VM by Installing and Configuring Software like apt Packages](#customizing-a-template-vm-by-installing-and-configuring-software-like-apt-packages)

In the next step we will make customizations to the template VM, like installing and configuring apt packages. This can be done by running commands or by executing scripts on the VM. Keep in mind that all changes to the `/home/ ` directory will not be present in AppVMs that use the TemplateVM as template, because AppVMs create a new LVM logical volume to mount to that directory. If you want persistent changes to the `/home/ ` directory, do that in the AppVM.

#### [Executing Simple Commands in a VM with qvm-run](#executing-simple-commands-in-a-vm-with-qvm-run)

Use `qvm-run ` to run simple BASH commands on the VM. Note that without an additonal argument called “–pass-io”, this tool does not print the executed commands output:

<pre class="command-line language-bash" data-output="2-3,5" data-continuation-str="\" data-user="user" data-host="dom0">
<code class="language-bash">qvm-run template-firefox whoami
Running 'whoami' on template-firefox

echo $?
0</code></pre>

#### [Insecure: Showing the Output of qvm-run Commands](#insecure-showing-the-output-of-qvm-run-commands)

In order to show the output the command returns, we can use the `qvm-run --pass-io ` argument. This approach is discouraged however, as it can be used to send data from a VM into a terminal that is running in dom0!

<pre class="command-line language-bash" data-output="2-999" data-continuation-str="\" data-user="user" data-host="dom0">
<code class="language-bash">qvm-run --pass-io template-firefox whoami
user</code></pre>

You can also execute commands as the root user directly:

<pre class="command-line language-bash" data-output="2-999" data-continuation-str="\" data-user="user" data-host="dom0">
<code class="language-bash">qvm-run --user root --pass-io template-firefox whoami
root</code></pre>

#### [Insecure: Using qvm-run –pass-io to Execute Whole Scripts](#insecure-using-qvm-run-pass-io-to-execute-whole-scripts)

Execute small scripts on a Qubes VM directly with the following command:

<pre class="command-line language-bash" data-output="6-999" data-continuation-str="\" data-user="user" data-host="dom0">
<code class="language-bash">cat << EOF | qvm-run --pass-io --user root --service template-firefox qubes.VMShell
set -x
whoami
hostname
EOF
root
template-firefox</code></pre>

#### [Secure but Unpractical: Using a Graphical Terminal Emulator Inside the VM to Execute Commands](#secure-but-unpractical-using-a-graphical-terminal-emulator-inside-the-vm-to-execute-commands)

Note: if you are working with the debian minimal template, there are two graphical terminal emulators installed by default: xterm and uxterm. I Personally prefer xfce4-terminal, you can use the following commands to install it:

<pre class="command-line language-bash" data-output="3-99" data-continuation-str="\" data-user="user" data-host="dom0">
<code class="language-bash">qvm-run -u root template-firefox "apt-get update"
qvm-run -u root template-firefox "apt-get -y install xfce4-terminal"</code></pre>

The most practical and secure approach to run commands inside a Qubes VM is to start a graphical terminal emulator, like xterm, gnome-terminal or xfce4-terminal inside a VM that then executes the command. This way the output that the commands generate are only viewed inside the graphical terminal emulator, xfce4-terminal in this example, which runs INSIDE the VM. Hence, dom0 can not be affected by it if malware generated output is produced.

<pre class="command-line language-bash" data-output="2-999" data-continuation-str="\" data-user="user" data-host="dom0">
<code class="language-bash">qvm-run template-firefox xfce4-terminal --hold --command "whoami"</code></pre>

The `--command ` option of graphical terminals like xfce4-terminal and gnome-terminal is rather limited, and launching the terminal emulator as an argument to qvm-run makes passing arguments to the tools you want to run inside the terminal rather problematic. The next section covers the best solution.

#### [Secure: Using a Graphical Terminal Emulator Inside the VM to Execute Scripts](#secure-using-a-graphical-terminal-emulator-inside-the-vm-to-execute-scripts)

As we are configuring a TemplateVM, it is safe to assume that more than one command has to be executed on the VM. We can put all those commands into a script, upload the script to the VM and execute the script using it as single argument to xfce4-terminal.

To do this, create a script in dom0 called new-template-basics.sh with the following content:

```bash
#!/bin/bash
#
# Setup everything we need for the firefox VM
apt-get update
apt-get update
apt-get -y upgrade

# Configure locale
apt-get -y install locales-all
locale-gen en_US.UTF-8

# Install apt packages
apt-get -y install xfce4-terminal firefox curl flatpak

# Add flatpak remote
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo

# Install libre-wolf, a firefox browser modified for privacy
flatpak install --verbose -y --noninteractive flathub io.gitlab.librewolf-community
```

Now upload that script the VM:

<pre class="command-line language-bash" data-output="2-999" data-continuation-str="\" data-user="user" data-host="dom0">
<code class="language-bash">qvm-copy-to-vm template-firefox new-template-basics.sh</code></pre>

The script is now located at `template-firefox:/home/user/QubesIncoming/dom0/new-template-basics.sh `. We can now make it executable. Remember to not use the `--pass-io ` flag for security reasons!

<pre class="command-line language-bash" data-output="2-999" data-continuation-str="\" data-user="user" data-host="dom0">
<code class="language-bash">qvm-run template-firefox "chmod 700 /home/user/QubesIncoming/dom0/new-template-basics.sh"</code></pre>

We can now run the script in a graphical terminal:

<pre class="command-line language-bash" data-output="2-999" data-continuation-str="\" data-user="user" data-host="dom0">
<code class="language-bash">qvm-run template-firefox "xfce4-terminal --hold --command /home/user/QubesIncoming/dom0/new-template-basics.sh"</code></pre>

If your scripts need additional files, like config files, you can copy a whole directory to the VM:

<pre class="command-line language-bash" data-output="2-999" data-continuation-str="\" data-user="user" data-host="dom0">
<code class="language-bash">qvm-copy-to-vm template-firefox templates/</code></pre>

The directory is then available at the VM at `template-firefox:/home/user/QubesIncoming/dom0/templates ` and you can use the files in your setup scripts.

#### [Practical and Secure: Using a Script to Copy a Script to the VM and then Start a Graphical Terminal Emulator Inside the VM that Executes a Script](#practical-and-secure-using-a-script-to-copy-a-script-to-the-vm-and-then-start-a-graphical-terminal-emulator-inside-the-vm-that-executes-a-script)

The following BASH script copies BASH scripts into VMs and executes them. Usage:

```bash
# Syntax
qvm-run-script [target-vm] [path-to-script] <user>

# Run dom0:~/scripts/fix-network-manager.sh in sys-net as default user (root)
qvm-run-script sys-net ~/scripts/fix-network-manager.sh

# Run a script as user "user" inside an AppVM
qvm-run-script app-firefox ~/scripts/update-virtualenv-pip-packages.sh user
```

You can copy the script to dom0 by creating the script as file in any given VM, like the VM that you use to run a browser to view this website, and then run the the following command to copy the file to dom0:

<pre class="command-line language-bash" data-output="2-999" data-continuation-str="\" data-user="user" data-host="dom0">
<code class="language-bash">qvm-run work --pass-io "cat /home/user/qvm-run-script.sh" | sudo tee /usr/local/bin/qvm-run-script.sh</code></pre>

Save the following BASH script in dom0 to `/usr/local/bin/qvm-run-script `

```bash
#!/bin/bash
#
# Execute a script inside a Qubes VM



# Parse arguments
myvm=$1
myscript=$2
myuser=${3:-root}

# Verify arguments
if test -z "$myvm"; then
    echo "Give target VM as first argument, aborting!"
    exit 1
elif ! qvm-ls | grep --quiet $myvm; then
    echo "No such VM: $myvm, aborting!"
    exit 1
elif ! test -f "$myscript"; then
    echo "Give script to run in target VM as second argument, aborting!"
    exit 1
fi

# Make sure the destination path on the VM is empty and no file from the previous run are pesent there
qvm-run --pass-io --user $myuser $myvm "rm -f /home/user/QubesIncoming/dom0/$myscript"

# Copy the script to the VM
qvm-copy-to-vm $myvm $myscript

# Make the script executable
qvm-run --pass-io --user $myuser $myvm "chmod 500 /home/user/QubesIncoming/dom0/$myscript"

# Execute the script inside xfce4-terminal
# --hold will not close the terminal after the last command finished executing, you have to manually close the window
qvm-run --pass-io --user $myuser $myvm "xfce4-terminal --hold --command /home/user/QubesIncoming/dom0/$myscript"

# Clean up: remove the executed scrpit
qvm-run --pass-io --user $myuser $myvm "rm /home/user/QubesIncoming/dom0/$myscript"
```

Do not forget to make the script executable:

<pre class="command-line language-bash" data-output="2-999" data-continuation-str="\" data-user="user" data-host="dom0">
<code class="language-bash">chmod 750 /usr/local/bin/qvm-run-script</code></pre>

### [Shutting down the TemplateVM](#shutdown-the-templatevm)

Before the template VM can be used to create app VMs it must be shut down:

<pre class="command-line language-bash" data-output="2-999" data-continuation-str="\" data-user="user" data-host="dom0">
<code class="language-bash">qvm-shutdown --wait template-firefox</code></pre>

## [Creating an AppVM from a Template VM](#creating-an-appvm-from-a-template-vm)

Now that we have a template VM with firefox installed, we can create an AppVM from that TemplateVM.

When starting an AppVM in qubes, a temporary snapshot of the root disk of the the TemplateVM is created, which is then booted. AppVMs can persistently save data, for which a LVM volume is created in dom0, which is later mounted to `appvm:/rw/`, where `/rw/home/` is bind mounted to `/home/`. The contents of the `/home/` directory are populated by the contents of the `/etc/skel/` directory from the TemplateVM. Additionally, the contents of the TemplateVMs `/usr/local.orig/` are used to pupulate the AppVMs `/usr/local/` directory. For additional information refer to [the Qubes documentation on inheritence and persistence in different VM types](https://www.qubes-os.org/doc/templates/#inheritance-and-persistence "qubes-os.org: documentation on inheritence and persistence of different VM types").

There are three special directories: `/home/user/`, `/rw/` and `/usr/local/`, which are persistent. All files in them are saved to a private volume that is associated with the AppVM. All other files and directories, including those that contain the operating system files, are discarded, as they are loaded from the temporary snapshot of the TemplateVMs root volume.

The persistent directories are all mounted from `appvm:/dev/xvdb`, which is the private volume associated with the AppVM. This device file is mounted to `/rw/` and contains a directory called `/rw/home` and `/rw/usrlocal` (we did not forget a slash here). Using `/etc/fstab`, these directories are bind mounted to `/home/` and `/usr/local`:

`app:/dev/xvdb` is mounted to `/rw/`:

<pre class="command-line language-bash" data-output="2-999" data-continuation-str="\" data-user="user" data-host="AppVM">
<code class="language-bash">grep xvdb /etc/fstab
/dev/xvdb            /rw            auto    noauto,defaults,discard,nosuid,nodev    1 2</code></pre>

After that, `/rw/home ` and `/rw/usrlocal ` are bind mounted to `/home ` and `/usr/local `:

<pre class="command-line language-bash" data-output="2-999" data-continuation-str="\" data-user="user" data-host="AppVM">
<code class="language-bash">grep bind /etc/fstab
/rw/home             /home          none    noauto,bind,defaults,nosuid,nodev        0 0
/rw/usrlocal         /usr/local     none    noauto,bind,defaults        0 0</code></pre>

This way AppVMs offer persistent storage for files like documents or your browsers configuration files like Bookmarks and so on, or SSH keypairs and configuration inside `~/.ssh/ `.

If you use firefox to visit a malicious website and it installs malware anwhere outside of these persistent directories, for example inside `/etc/firefox `, then this modification will not persist accross reboots - unless you visit the website again.

To create an AppVM from a TemplateVM:

<pre class="command-line language-bash" data-output="2-999" data-continuation-str="\" data-user="user" data-host="dom0">
<code class="language-bash">qvm-create --class AppVM --label blue --template template-firefox app-firefox</code></pre>

Note that `qvm-create` has a `--property` argument, which can be used to set prefs directly. As this leads to rather long `qvm-create` commands, I personally prefer to use `qvm-prefs` after creating the VM.

### [Commonly Used Prefs for AppVMs](#commonly-used-prefs-for-appvms)

Here are some commonly used Qubes Prefs for AppVMs:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-user="user" data-host="dom0">
<code class="language-bash">qvm-prefs app-firefox netvm                sys-firewall
qvm-prefs app-firefox template_for_dispvms false
qvm-prefs app-firefox autostart            true
qvm-prefs app-firefox provides_network     false
qvm-prefs app-firefox label                orange
qvm-prefs app-firefox vcpus                4
qvm-prefs app-firefox memory               2048
qvm-prefs app-firefox maxmem               4096</code></pre>

### [Resizing the home parition of an AppVM](#resizing-the-home-parition-of-an-appvm)

To resize the home partition of an AppVM use the following command, which resizes the private LVM volume of the AppVM. See below for additional information on the LVM volumes used.

<pre class="command-line language-bash" data-output="2-999" data-continuation-str="\" data-user="user" data-host="dom0">
<code class="language-bash">qvm-volume resize app-firefox:private 50G</code></pre>

### [LVM Volumes used by App VMs](#lvm-volumes-used-by-app-vms)

When starting an AppVM, Qubes OS clones the LVM volumes of the TemplateVM to create root volumes for the AppVM. Here is how you show all volumes used by an AppVM that is currently NOT running. For running AppVMs there are additional volumes, like the temporary clone of the TemplateVMs root disk.

<pre class="command-line language-bash" data-output="2-999" data-continuation-str="\" data-user="user" data-host="dom0">
<code class="language-bash">sudo lvdisplay /dev/qubes_dom0/vm-app-firefox-*
  --- Logical volume ---
  LV Path                /dev/qubes_dom0/vm-app-firefox-private
  LV Name                vm-app-firefox-private
  VG Name                qubes_dom0
  LV UUID                srQf86-f44I-B31g-8TJU-eDpz-4FTQ-KIYKI0
  LV Write Access        read/write
  LV Creation host, time dom0, 2024-05-14 16:44:24 -0400
  LV Pool name           vm-pool
  LV Status              available
  # open                 0
  LV Size                2.00 GiB
  Mapped size            0.00%
  Current LE             512
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     2048
  Block device           253:79</code></pre>

### [Making Persistent Modifications in AppVMs](#making-persistent-modifications-in-appvms)

When you install packages using `apt ` inside of an AppVM, the changes are discarded when you shut it down. Only changes below the `/home/ `, `/usr/local/ ` and `/rw/ ` directories are preserved. For additional information refer to [the qubes documentation on persistent storage in AppVMs](https://www.qubes-os.org/doc/template-implementation/#privateimg-xvdb "qubes-os.org: template documentation on private LVM images").

Note that the `/home/ ` directory of the TemplateVM is not used to populate the `/home/ ` directory in the AppVM!

<pre class="command-line language-bash" data-output="2-999" data-continuation-str="\" data-user="user" data-host="dom0">
<code class="language-bash">qvm-run app-firefox "mkdir /home/user/projects/"</code></pre>

### [Qvm-Prefs Inherited from the TemplateVM](#qvm-prefs-inherited-from-the-templatevm)

When creating AppVMs from TemplateVMs, the AppVMs inherits almost all prefs from the TemplateVMs. The remainder come from whatever is set in global defaults, which you can view and define using the `qubes-prefs` command.

Identical prefs:

```plaintext
audiovm             dom0
autostart           False
backup_timestamp
debug               False
default_dispvm      default-dvm
default_user        user
dns                 10.139.1.1
gateway
gateway6
guivm               dom0
include_in_backups  True
installed_by_rpm    False
ip6
kernel              6.6.29-1.fc37
kernelopts          swiotlb=2048
keyboard_layout     us++
label               orange
mac                 00:16:3e:5e:6c:00
management_dispvm   default-mgmt-dvm
maxmem              4096
memory              1024
provides_network    False
qrexec_timeout      60
shutdown_timeout    60
start_time
stubdom_mem
stubdom_xid         -1
vcpus               2
virt_mode           hvm
visible_gateway     10.138.21.233
visible_gateway6
visible_ip6
visible_netmask     255.255.255.255
xid                 -1
```

Different prefs:

```plaintext
< Pref of the TemplateVM
> Pref of the AppVM

< icon templatevm-orange
> icon appvm-orange

< ip 10.137.0.13
> ip 10.137.0.35

< klass TemplateVM
> klass AppVM

< name tpl-deb-12
> name app-test

< netvm None
> netvm sys-firewall

< qid 13
> qid 35

< updateable True
> updateable False

< uuid d5455b0d-35b7-4d2d-9ded-cc0eb4849c03
> uuid 3d47bf5f-80d0-4770-8a0a-3a0a3e33332a

< visible_ip 10.137.0.13
> visible_ip 10.137.0.35
```

## [Creating Disposable VMs from App VMs](#creating-disposable-vms-from-app-vms)

No files modified in a disposable VM are preserved. All volumes that DispVMs use are discarded upon shutdown of the VM. Disposable VMs need an AppVM as their template.

There are two types of disposable VMs:

- **Regular Disposable VMs**
  : created from an AppVM using
  `qvm-run --dispvm `
  to start a single command, like
  `firefox `
  , and is assigned a random name like “Disp123”

- **Named Disposable VMs**
  : are created using
  `qvm-create --dispvm `
  and have a persistent name. Although they can be shutdown, the AppVM is completely cloned during each start. Most helpful for system related VMs like “sys-net”, where you need a persistent name for the VM in order to chain network VMs.

All disposable VMs, both temporary ones created with `qvm-run --dispvm <AppVM> <tool-to-launch-in-disp-vm> ` as well as persistent ones created with `qvm-create --class DispVM --label <color> --template <AppVM> <permanent-dispvm-name> ` inherit the `appvm:/dev/xvdb ` device, which is used to mount the `/home/ `, `/rw/ ` and `/usr/local/ ` directory. This means that all files in those directories in the AppVM are also present in the disposable VM.

In order for an AppVM to be used as a template for a DisposbleVM, use the following `qvm-prefs ` command:

<pre class="command-line language-bash" data-output="2-999" data-continuation-str="\" data-user="user" data-host="dom0">
<code class="language-bash">qvm-prefs app-firefox template_for_dispvms true</code></pre>

As you can see below, the pref `template_for_disvms ` is only available for AppVMs and not for TemplateVMs or StandaloneVMs:

<pre class="command-line language-bash" data-output="2-999" data-continuation-str="\" data-user="user" data-host="dom0">
<code class="language-bash">qvm-prefs template-firefox template_for_dispvms
usage: qvm-prefs [--verbose] [--quiet] [--help] [--help-properties] [--hide-default] [--get]
                 [--set] [--default]
                 VMNAME [PROPERTY] [VALUE]
qvm-prefs: error: no such property: 'template_for_dispvms'</code></pre>

### [Creating Regular Disposable VMs](#creating-regular-disposable-vms)

With using app-firefox as a template, we can spawn multiple disposable VMs from it - each DispVM that is created is its completely own VM and each one runs the command `/usr/bin/firefox `. You can also just type `firefox `.

<pre class="command-line language-bash" data-output="2-999" data-continuation-str="\" data-user="user" data-host="dom0">
<code class="language-bash">qvm-run --dispvm app-firefox /usr/bin/firefox
Running 'firefox' on $dispvm:app-firefox</code></pre>

The `qvm-ls ` output below shows that Qubes assigned the VM the name “disp960”. This is a random name given to this temporary disposable VM. You can repeat the `qvm-run ` command above to spawn additional instances like this one.

<pre class="command-line language-bash" data-output="2-999" data-continuation-str="\" data-user="user" data-host="dom0">
<code class="language-bash">qvm-ls --running | grep DispVM
disp960        Running  DispVM   orange  app-firefox        -</code></pre>

After the command completes, as in after you close firefox, qubes will automatically shutdown and delete the disposable VM.

### [Creating a Named Disposable VM](#creating-a-named-disposable-vm)

Especially for network VMs like “sys-net”, it can be desirable to have a disposable VM - sys-net is rather high risk as it directly connects to “the coffee shop wifi”. Hence it makes sense to discard all changes at shutdown. The Qubes OS installer even has a feature during installation to make sys-net disposable.

If you want to create a dispoable VM with a persistent name like “sys-net” instead of a random name like “disp960”, use `qvm-create ` with the ``class=DispVM ` argument:

<pre class="command-line language-bash" data-output="2-999" data-continuation-str="\" data-user="user" data-host="dom0">
<code class="language-bash">qvm-create --class DispVM --label purple --template app-firefox disposable-firefox</code></pre>

When you now start firefox inside the named disposable VM using `qvm-run `:

<pre class="command-line language-bash" data-output="2-999" data-continuation-str="\" data-user="user" data-host="dom0">
<code class="language-bash">qvm-run disposable-firefox /usr/bin/firefox</code></pre>

Qubes will start firefox in a VM named “disposable-firefox”. You can shut down this VM and it will enter the Shutoff state, and no changes that were made during its runtime will persist. The `qvm-run --dispvm ` argument is not required for persistent disposable VMs.

Refer to the [Qubes OS documentation on named disposable VMs](https://www.qubes-os.org/doc/disposable-customization/#using-named-disposables-for-service-qubes "qubes-os.org: documentation on customizing disposable VMs") for additional information.

### [Qvm-Prefs Inherited from the TemplateVM](#qvm-prefs-inherited-from-the-templatevm-1)

When creating a regular or named (persistent) DispVM from an AppVM, the new DispVM inherits some of the prefs from the AppVM:

Identical prefs:

```plaintext
audiovm             dom0
autostart           False
backup_timestamp
debug               False
default_user        user
dns                 10.139.1.1
gateway
gateway6
guivm               dom0
include_in_backups  True
installed_by_rpm    False
ip6
kernel              6.6.29-1.fc37
kernelopts          swiotlb=2048
keyboard_layout     us++
mac                 00:16:3e:5e:6c:00
management_dispvm   default-mgmt-dvm
maxmem              4096
memory              1024
netvm               sys-firewall
provides_network    False
qrexec_timeout      60
shutdown_timeout    60
start_time
stubdom_mem
stubdom_xid         -1
updateable          False
vcpus               2
virt_mode           hvm
visible_gateway     10.138.21.233
visible_gateway6
visible_ip6
visible_netmask     255.255.255.255
xid                 -1
```

Different prefs:

```plaintext
< Prefs of the AppVM
> Prefs of the DispVM

< ip 10.137.0.35
> ip 10.138.35.139

< klass AppVM
> klass DispVM

> label black
< label orange

< name app-test
> name dsp-test

< qid 35
> qid 36

< template tpl-deb-12
> template app-test

< uuid 3d47bf5f-80d0-4770-8a0a-3a0a3e33332a
> uuid 138775bb-f46a-44f1-a40b-18603af928b0

< visible_ip 10.137.0.35
> visible_ip 10.138.35.139
```

### [LVM Volumes used by Disposable VMs](#lvm-volumes-used-by-disposable-vms)

When starting a DispVM, Qubes OS snapshots the LVM volumes for the root disk from the TemplateVM that the AppVM uses, and the private volume, containing `/home/ `, from the AppVM. Here is how you show all volumes used by a DispVM that is currently running:

<pre class="command-line language-bash" data-output="2-999" data-continuation-str="\" data-user="user" data-host="dom0">
<code class="language-bash">sudo lvdisplay /dev/qubes_dom0/vm-disp834*
  --- Logical volume ---
  LV Path                /dev/qubes_dom0/vm-disp834-volatile
  LV Name                vm-disp834-volatile
  VG Name                qubes_dom0
  LV UUID                J462m5-ebXA-fs6Y-3fhV-e245-8N4i-LyNkBi
  LV Write Access        read/write
  LV Creation host, time dom0, 2024-05-21 17:34:39 -0400
  LV Pool name           vm-pool
  LV Status              available
  # open                 2
  LV Size                12.00 GiB
  Mapped size            0.01%
  Current LE             3072
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     2048
  Block device           253:132
   
  --- Logical volume ---
  LV Path                /dev/qubes_dom0/vm-disp834-root-snap
  LV Name                vm-disp834-root-snap
  VG Name                qubes_dom0
  LV UUID                5vTKHJ-tIET-VzVi-EJqm-DTGX-26Uo-7Ec0nd
  LV Write Access        read/write
  LV Creation host, time dom0, 2024-05-21 17:34:39 -0400
  LV Pool name           vm-pool
  LV Thin origin name    vm-template-firefox-root
  LV Status              available
  # open                 2
  LV Size                20.00 GiB
  Mapped size            9.12%
  Current LE             5120
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     2048
  Block device           253:133
   
  --- Logical volume ---
  LV Path                /dev/qubes_dom0/vm-disp834-private-snap
  LV Name                vm-disp834-private-snap
  VG Name                qubes_dom0
  LV UUID                dVtXed-7GnK-L13W-f76E-OfvX-aSry-UW3FNv
  LV Write Access        read/write
  LV Creation host, time dom0, 2024-05-21 17:34:39 -0400
  LV Pool name           vm-pool
  LV Thin origin name    vm-app-firefox-private
  LV Status              available
  # open                 2
  LV Size                2.00 GiB
  Mapped size            1.81%
  Current LE             512
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     2048
  Block device           253:134</code></pre>

## [Creating a StandaloneVM From a TemplateVM](#creating-a-standalonevm-from-a-templatevm)

A standalone VM is a regular virtual machine like you already know it. All changes to it are persistent. It is created by cloning a template VM. All modifications to a standalone VM are permanent, none of the “Qubes Magic” that is used to make parts or all of the changes made to VMs non-persistent are in use.

To create a standalone VM use the following command:

<pre class="command-line language-bash" data-output="2-999" data-continuation-str="\" data-user="user" data-host="dom0">
<code class="language-bash">qvm-create --standalone --template debian-12-xfce --label green code-playground
code-playground: Cloning root volume</code></pre>

Start the VM:

<pre class="command-line language-bash" data-output="2-999" data-continuation-str="\" data-user="user" data-host="dom0">
<code class="language-bash">qvm-start --wait code-playground</code></pre>

To view the class for a standalone VM:

<pre class="command-line language-bash" data-output="2-999" data-continuation-str="\" data-user="user" data-host="dom0">
<code class="language-bash">qvm-ls code-playground
NAME             STATE   CLASS         LABEL  TEMPLATE  NETVM
code-playground  Running StandaloneVM  green  -         sys-firewall</code></pre>

This VM type makes most sense for experimenting and should be avoided for production use.

### [Qvm-Prefs Inherited from the TemplateVM](#qvm-prefs-inherited-from-the-templatevm-2)

When creating a StandaloneVM from a TemplateVM, the new StandaloneVM inherits almost all of the prefs from the TemplateVM:

Identical prefs:

```plaintext
audiovm                 dom0
autostart               False
debug                   False
default_dispvm          default-dvm
default_user            user
dns                     10.139.1.1
guivm                   dom0
include_in_backups      True
installed_by_rpm        False
kernel                  6.6.29-1.fc37
kernelopts              swiotlb=2048
keyboard_layout         us++
mac                     00:16:3e:5e:6c:00
management_dispvm       default-mgmt-dvm
maxmem                  4096
memory                  1024
provides_network        False
qrexec_timeout          60
shutdown_timeout        60
stubdom_xid             -1
updateable              True
vcpus                   2
virt_mode               hvm
visible_gateway         10.138.21.233
visible_netmask         255.255.255.255
xid                     -1
```

Different prefs:

```plaintext
< icon                    templatevm-orange
> icon                    standalonevm-green

< ip                      10.137.0.13
> ip                      10.137.0.33

< klass                   TemplateVM
> klass                   StandaloneVM

> label                   green
< label                   orange

< name                    debian-12-xfce
> name                    code-playground

< netvm                   None
> netvm                   sys-firewall

< qid                     13
> qid                     33

< uuid                    d5455b0d-35b7-4d2d-9ded-cc0eb4849c03
> uuid                    988f5070-35e9-4b0f-a361-fa0080083a46

< visible_ip              10.137.0.13
> visible_ip              10.137.0.33
```

### [LVM Volumes used by Disposable VMs](#lvm-volumes-used-by-disposable-vms-1)

A Standalone VM has two volumes, one for the root filesystem and one for home. Even though it has two volumes, both save data persistently and all changes made to them are permanent.

<pre class="command-line language-bash" data-output="2-999" data-continuation-str="\" data-user="user" data-host="dom0">
<code class="language-bash">sudo lvdisplay /dev/qubes_dom0/vm-code-sandbox*
  --- Logical volume ---
  LV Path                /dev/qubes_dom0/vm-code-sandbox-root
  LV Name                vm-code-sandbox-root
  VG Name                qubes_dom0
  LV UUID                vHwtNU-9AUr-APMG-V8cL-IOTc-u1qY-RTFXMI
  LV Write Access        read/write
  LV Creation host, time dom0, 2024-05-15 17:10:21 -0400
  LV Pool name           vm-pool
  LV Thin origin name    vm-code-sandbox-root-1716327722-back
  LV Status              available
  # open                 0
  LV Size                20.00 GiB
  Mapped size            8.73%
  Current LE             5120
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     2048
  Block device           253:118
   
  --- Logical volume ---
  LV Path                /dev/qubes_dom0/vm-code-sandbox-private
  LV Name                vm-code-sandbox-private
  VG Name                qubes_dom0
  LV UUID                u3qsKy-nHzc-2MmH-aIfd-dfkK-BLqJ-nNhCmk
  LV Write Access        read/write
  LV Creation host, time dom0, 2024-05-15 17:10:22 -0400
  LV Pool name           vm-pool
  LV Thin origin name    vm-code-sandbox-private-1716327722-back
  LV Status              available
  # open                 0
  LV Size                2.00 GiB
  Mapped size            4.93%
  Current LE             512
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     2048
  Block device           253:120</code></pre>
