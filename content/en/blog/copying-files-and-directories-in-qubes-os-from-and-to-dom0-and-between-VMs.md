---
title: "Copying Files in Qubes OS: From and to dom0 and between VMs"
description: "This tutorial describes how to copy files and directories in Qubes OS: from dom0 to VMs, between VMs and how to do that automatically in scripts"
date: 2024-05-25
image: "/images/blog/qubes-os-logo.webp"
image_alt: "Qubes OS Logo"
---

Please [contact us](/index.html#contact "Blunix GmbH contact options") if anything is not clearly described, does not work, seems incorrect or if you require support.

Qubes OS VMs are isolated from one another by design. Hence, copying files between VMs is made a bit more complicated by design. This tutorial explains how to copy files or directories from dom0 to a Qubes VM, from one Qubes VM to another VM, how to copy from a VM to dom0 and how to do all that automatically in BASH scripts.

## Table of contents

- [Copying Files and Directories from dom0 to a VM](#copying-files-and-directories-from-dom0-to-a-vm)
- [Copying Files from a VM to dom0](#copying-files-from-a-vm-to-dom0)
  - [Copying a Single File to dom0](#copying-a-single-file-to-dom0)
  - [Copying a Directory from a VM to dom0](#copying-a-directory-from-a-vm-to-dom0)
- [Copying Files and Directories from one Qubes VM to another VM](#copying-files-and-directories-from-one-qubes-vm-to-another-vm)
- [Copying Files and Directories from one Qubes VM to another VM Automatically, for Example in Scripts](#copying-files-and-directories-from-one-qubes-vm-to-another-vm-automatically-for-example-in-scripts)
- [Very Bad Examples](#very-bad-examples)
- [Documentation and Additional Information](#documentation-and-additional-information)

## [Copying Files and Directories from dom0 to a VM](#copying-files-and-directories-from-dom0-to-a-vm)

In order to copy a file from dom0 to a Qubes VM, use the `qvm-copy-to-vm` command:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-prompt="user@dom0 ~ $">
<code class="language-bash">echo test-string > testfile.txt
qvm-copy-to-vm work testfile.txt </code></pre>

This works exactly the same way for directories - simply specify the directory to copy as the final argument.

All files that are copied from one VM to another are stored in the directory `/home/user/QubesIncoming/<source-vm>/ `:

<pre class="command-line language-bash" data-output="2-3,5-99" data-continuation-str="\" data-prompt="user@dom0 ~ $">
<code class="language-bash">ls /home/user/QubesIncoming/dom0/
testfile.txt

cat /home/user/QubesIncoming/dom0/testfile.txt
test-string</code></pre>

## [Copying Files from a VM to dom0](#copying-files-from-a-vm-to-dom0)

Copying files or directories from a VM to dom0 is highly discouraged. Dom0 is the most trusted OS and hence should not be modied from outside sources at all. It is for that reason that it is not attached to the internet at all.

However especially during the initial setup of a new Qubes OS installation, copying files to dom0 might not be avoidable, for example to copy a window manager configuration file to dom0.

### [Copying a Single File to dom0](#copying-a-single-file-to-dom0)

Here is how you can copy a single file from the work VM to dom0:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="user@dom0 ~ $">
<code class="language-bash">qvm-run --pass-io work 'cat /home/user/QubesIncoming/dom0/testfile.txt' > testfile.txt</code></pre>

To check the contents of the file from the work VM:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="user@dom0 ~ $">
<code class="language-bash">cat testfile.txt 
testfile.txt</code></pre>

### [Copying a Directory from a VM to dom0](#copying-a-directory-from-a-vm-to-dom0)

Using the method above, you can only copy files. In order to copy directories, you have to put the directory into an archive first, then copy the archive file and then unpack it again.

<pre class="command-line language-bash" data-output="4" data-continuation-str="\" data-prompt="[user@work ~]$">
<code class="language-bash">mkdir testdir/
echo test-string > testdir/testfile1.txt
echo some-string > testdir/testfile2.txt

tar cvzf myarchive.tar.gz testdir/</code></pre>

Now copy the archive to dom0 and unpack it:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-prompt="user@dom0 ~ $">
<code class="language-bash">qvm-run --pass-io work 'cat myarchive.tar.gz' > myarchive.tar.gz
tar xvzf myarchive.tar.gz</code></pre>

## [Copying Files and Directories from one Qubes VM to another VM](#copying-files-and-directories-from-one-qubes-vm-to-another-vm)

You can copy a file or directory from one VM to another VM using the `qvm-copy ` command. Notice that you can not define a destination VM with the `qvm-copy ` command.

<pre class="command-line language-bash" data-output="3" data-continuation-str="\" data-prompt="[user@work ~]$">
<code class="language-bash">echo test-string > testfile.txt
qvm-copy testfile.txt 
sent 1/1 KB</code></pre>

Notice how the following window will appear on your screen, asking you to define the destination VM as well as to confirm the action. This is an additional step for security - copying files from one VM to another could be used for malicious purposes, which is why all of these actions have to be confirmed in dom0 by the user.

![Qubes OS popup window to confirm copying a file](/images/blog/copying-files-and-directories-in-qubes-os-from-and-to-dom0-and-between-VMs/qubes-os-copy-confirmation-window.webp)

If you try to copy a file with the same filename twice, you will get this error:

<pre class="command-line language-bash" data-output="2" data-continuation-str="\" data-prompt="[user@work ~]$">
<code class="language-bash">qvm-copy testfile.txt 
qfile-agent: Fatal error: A file named "testfile.txt" already exists in QubesIncoming dir (error type: File exists)</code></pre>

To prevent this, you first have to move or delete the file in `/home/user/QubesIncoming/<source-vm>/<filename> `.

## [Copying Files and Directories from one Qubes VM to another VM Automatically, for Example in Scripts](#copying-files-and-directories-from-one-qubes-vm-to-another-vm-automatically-for-example-in-scripts)

The permission to copy files between VMs is defined in the Qubes policy file located in `dom0:/etc/qubes/policy.d/90-default.policy `. The following is the default policy:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="user@dom0 ~ $">
<code class="language-bash">grep Filecopy /etc/qubes/policy.d/90-default.policy
qubes.Filecopy          *           @anyvm          @anyvm      ask</code></pre>

The asterisk in the second column means that for the [Qubes RPC policy](https://www.qubes-os.org/doc/rpc-policy/ "qubes-os.org: documentation on RPC policies") `Filecopy`, all arguments are accepted. That is irrelevant here though, and is only part of the syntax of Qubes policy files, as `Filecopy` does not have any arguments. An example for an argument for a RPC policy would be `GetDate` (available from Qubes 4.2 and up), which can be used like `qrexec-client-vm @default qubes.GetDate` or with an argument `qrexec-client-vm @default qubes.GetDate+nanosec`. The asterisk here would mean that all arguments are acceptable, like the argument `+nanosec`. Fun fact: comparing timestamps is a pretty good way to figure out whether two VMs are on the same machine. Special thanks to xaki23 from the [Qubes Matrix channel](https://www.qubes-os.org/support/#chat "qubes-os.org: list of support chatrooms") for the insight.

As you can see by the last column, the default policy to `qubes.Filecopy ` from `@anyvm ` to `@anyvm ` is to `ask `. Ask in this case means the popup described in the section above, where you have to define the destination VM as well as confirm the copy action.

You can override this for specific VMs like so to allow for automatically copying files:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="user@dom0 ~ $">
<code class="language-bash">echo "qubes.Filecopy * work sys-net allow" | tee -a /etc/qubes/policy.d/30-user.policy                             </code></pre>

This will allow the `work ` VM to automatically copy files to the `sys-net ` VM without prior confirmation from the popup window in dom0. This might be useful for scripts.

As you noticed above, the command `qvm-copy ` does not have an argument to specify the destination VM. Because of this, you have to use the tool `qvm-copy-to-vm `, which allows to specify the destination VM:

<pre class="command-line language-bash" data-output="" data-continuation-str="\" data-prompt="[user@work ~]$">
<code class="language-bash">qvm-copy-to-vm sys-net testfile.txt</code></pre>

## [Very Bad Examples](#very-bad-examples)

The following example can be used to copy files between VMs without user confirmation. Note however that the file is “streamed” over dom0, which might be dangerous and is HIGHLY discouraged! DO NOT DO THIS!

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="user@dom0 ~ $">
<code class="language-bash">qvm-run --pass-io work 'cat testfile.txt' | qvm-run --pass-io sys-net 'cat > testfile.txt'</code></pre>

You should also not generally disable copying files without the interactive prompt like so, because this would allow a compromised VM to copy files to any other VM:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="user@dom0 ~ $">
<code class="language-bash">echo "qubes.Filecopy * @anyvm @anyvm allow" | tee -a /etc/qubes/policy.d/30-user.policy</code></pre>

## [Documentation and Additional Information](#documentation-and-additional-information)

For additional information refer to the following documentation and Qubes OS forum pages:

[Qubes OS documentation on how to copy and move files](https://www.qubes-os.org/doc/how-to-copy-and-move-files/ "qubes-os.org: documentation on how to copy and move files")  
[Qubes OS documentation on how to copy files from dom0](https://qubes-doc-rst.readthedocs.io/en/latest/user/how-to-guides/how-to-copy-from-dom0.html "qubes-os.org: documentation on how to copy files from dom0")  
[Qubes OS forum thread about copying files to dom0](https://forum.qubes-os.org/t/copying-files-to-dom0/19025 "forum.qubes-os.org: copying files to dom0")  
[Qubes OS forum thread about how to copy files between VMs without the GUI](https://forum.qubes-os.org/t/how-to-copy-files-between-vms-without-gui/8311/12 "forum.qubes-os.org: how to copy files between VMs without the GUI")
