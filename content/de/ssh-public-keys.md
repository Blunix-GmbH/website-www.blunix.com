---
title: "SSH-Public-Keys der Blunix GmbH für Linux Notfall Support"
description: "Die SSH-Public-Keys unserer Consultants für den Linux Support. Fügen Sie nur den Schlüssel der Person hinzu, mit der Sie gerade sprechen, und entfernen Sie ihn nach Abschluss des Einsatzes."
layout: "ssh-public-keys"
url: "/de/ssh-public-keys.html"
image: "/images/linux-emergency-support/background.webp"
hero_background: "/images/linux-emergency-support/background.webp"
hero_subtitle: "Bitte öffnen Sie Ihre Firewall für SSH-Zugriff von gateway.blunix.com"
---

Installieren Sie **nur** den SSH-Public-Key der Blunix-Consultantin bzw. des Blunix-Consultants, mit der oder dem Sie gerade sprechen. Entfernen Sie den Schlüssel unmittelbar nach Abschluss des Supports.

## SSH-Public-Keys unseres Linux-Notfall-Teams

<pre><code class="language-plaintext">
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIDdR6UEPCX8rxpdWSCRJ7qs/2blKeyKzzCeOunyZ3Ake f.winter@blunix.com
</code></pre>

<pre><code class="language-plaintext">
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAILMtrN645dDW010UTbTCE/Bu4GW60f0Dj3k4VIhDRCDF p.thurner@blunix.com
</code></pre>

<pre><code class="language-plaintext">
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAINZqLUZceWIm365F6MEop404K/SLqGgosasFoRx75Bi3 g.nair@blunix.com
</code></pre>

<pre><code class="language-plaintext">
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIKB4uC+mHtvwkDpvnSNV4xdIRVyKK1+J9Ulr/WhdSiQZ j.schaefer@blunix.com
</code></pre>

## So richten Sie den SSH-Public-Key auf Ihren Servern ein

Die Consultants der Blunix GmbH benötigen ein Linux-Konto mit sudo-Rechten oder `root@`-Zugriff. Folgen Sie diesen Schritten, um den Schlüssel zu hinterlegen:

<pre class="command-line" data-user="benutzer" data-host="arbeitsplatz" data-output="" data-continuation-str="\"><code class="language-bash">
ssh root@server
</code></pre>

<pre class="command-line" data-user="root" data-host="ihr-server" data-output="" data-continuation-str="\"><code class="language-bash">
mkdir ~/.ssh/
nano ~/.ssh/authorized_keys
</code></pre>

Fügen Sie **nur** den SSH-Public-Key der Blunix-Consultantin oder des Blunix-Consultants ein, mit der oder dem Sie telefonieren. Speichern Sie die Datei anschließend:

- `STRG + o`
- `ENTER`
- `STRG + x`

Sobald Ihr Anliegen gelöst ist, entfernen Sie den SSH-Public-Key wieder. Im Editor `nano` löschen Sie eine Zeile mit:

- `STRG + k`

Setzen Sie zuletzt die korrekten Dateiberechtigungen:

<pre class="command-line" data-user="root" data-host="ihr-server" data-output="" data-continuation-str="\"><code class="language-bash">
chmod 700 ~/.ssh/
chmod 600 ~/.ssh/authorized_keys
</code></pre>
