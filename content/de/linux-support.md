---
title: "Linux Support und Expertenrat für Unternehmen"
slug: "linux-support"
description: "Linux Support für Distributions-Upgrades, Migrationen, Evaluierung neuer Technologien, Best-Practice-Prüfungen und fundierte Beratung."
image: "/images/linux-support/background.webp"

blocks:
  - block: hero-breadcrumb
    title: "Linux Support"
    subtitle: "Fachkundige Unterstützung durch professionelle Linux Administratorinnen und Administratoren"
    background: "/images/linux-support/background.webp"
    breadcrumb: "Linux Support"
    notice: "Unsere Leistungen richten sich ausschließlich an Unternehmen in der EU & USA und erfordern eine gültige Umsatzsteuer-ID. Wir arbeiten nicht mit Privatkundschaft."

  - block: text-image-bg
    id: support
    title: "Debian- und OpenBSD-Support für Server"
    text: |
      Planen und realisieren Sie Änderungen an Ihrer Infrastruktur mit professioneller Unterstützung. Wir begleiten Sie bei Distributions-Upgrades, Migrationen sowie der Einführung neuer Tools und Services. Linux Support und fundierter Rat für Unternehmen.

      ### Hilfestellung für anspruchsvolle Server-Setups

      Wir stellen sicher, dass unsere Kundinnen und Kunden das Problem verstehen und liefern Dokumentation für zukünftige Referenzen. Unser Ziel sind transparente und verlässliche Lösungen.
    image:
      src: "/images/linux-support/linux-support-and-openbsd-support.webp"
      alt: "Linux Support – rund um die Uhr im Notfall verfügbar"
    image_position: "left"
    image_bg: "blue"

  - block: features-grid
    id: features
    title: "Debian Linux Support für alle gängigen Tools und Anwendungen"
    subtitle: "Wir verfügen über umfassende Erfahrung in der Administration aller Programme, die auf Linux-Servern und in Cloud-Umgebungen üblich sind."
    items:
      - icon: "uil uil-server"
        title: "Webserver"
        details: "Nginx, Apache2, Lighttpd"
      - icon: "uil uil-envelope"
        title: "Mailserver"
        details: "OpenSMTPD statt Postfix, IMAP mit Dovecot"
      - icon: "uil uil-database"
        title: "Datenbanken"
        details: "PostgreSQL, MySQL/MariaDB, Redis, RabbitMQ"
      - icon: "uil uil-shield"
        title: "Netzwerk"
        details: "IPtables, NFtables, fail2ban, Wazuh"
      - icon: "uil uil-window-grid"
        title: "Virtualisierung"
        details: "QEMU/KVM, Proxmox, Docker"
      - icon: "uil uil-cog"
        title: "Automatisierung"
        details: "BASH, Python"
      - icon: "uil uil-setting"
        title: "Configuration Management"
        details: "Ansible, Terraform"
      - icon: "uil uil-chart-line"
        title: "Backup, Monitoring, Logfiles"
        details: "BorgBackup, Prometheus, systemd-journal"

  - block: process-timeline
    id: linux-support-process
    title: "Ablauf unseres Linux Supports"
    steps:
      - heading: "Nehmen Sie Kontakt mit uns auf"
        text: "Zeigen Sie uns genau, welche Änderungen, Upgrades oder neuen Komponenten benötigt werden."
        icon: "uil uil-phone"
        right: true
      - heading: "Implementierung"
        text: "Wir greifen per SSH auf Ihre Infrastruktur zu, setzen die Änderungen um und dokumentieren unsere Arbeit."
        icon: "uil uil-wrench"
        right: false
      - heading: "Weitere Unterstützung"
        text: "Blunix steht jederzeit für Unterstützung oder einen inspirierenden Linux-Austausch bereit."
        icon: "uil uil-file-info-alt"
        right: true

  - block: faq
    id: frequently-asked-questions
    title: "Häufig gestellte Fragen"
    items:
      - q: "Bieten Sie Support für Privatpersonen?"
        a: "Unser Partner [linuxguides.de](https://www.linuxguides.de/linux-support/) bietet Linux-Support für Endnutzerinnen und Endnutzer – inklusive Expertise zu Ubuntu auf Laptops und Desktop-PCs, Druckern, defekten Festplatten und mehr."
      - q: "Auf welche Linux-Distributionen konzentrieren Sie sich?"
        a: "Blunix fokussiert sich auf Debian-basierte Distributionen und setzt OpenBSD in sensiblen Umgebungen ein. Zusätzlich beraten und unterstützen wir bei der hochsicheren Desktop-Distribution Qubes OS."
      - q: "Unterstützen Sie auch hardwarebezogene Themen?"
        a: "Unsere Kernkompetenz ist die Administration von Linux-Servern und Cloud-Umgebungen. Wir können defekte Hardware über die Kommandozeile identifizieren, übernehmen jedoch kein physisches Hardware-Management. Gerne geben wir Empfehlungen, welche Server sich bei dedizierten oder Cloud-Anbietern eignen."
      - q: "Wann ist Ihr Linux Support erreichbar?"
        a: "Unser Expertensupport ist werktags von Montag bis Freitag zwischen 10:00 und 18:00 Uhr (Zeitzone Europa/Berlin) verfügbar."
      - q: "Bieten Sie 24/7/365 Notfall-Support?"
        a: "Ja, wählen Sie hierzu bitte unsere [Kontaktseite](index.html#contact) und rufen Sie unsere Notfallnummer an."
      - q: "Was kostet Beratung?"
        a: "Beratung ist immer kostenlos, wir berechnen lediglich die Zeit, in der wir aktiv auf der Tastatur arbeiten. Wenn Sie nur eine zweite Meinung brauchen, sprechen wir jederzeit gerne über Linux."
      - q: "Was muss vor dem Start erledigt werden?"
        a: "Bitte stellen Sie sicher, dass eine vollständige Sicherung aller Daten und Dateien der Server vorliegt, an denen wir arbeiten sollen. Teilen Sie uns vor Projektbeginn mit, falls Sie Unterstützung beim Erstellen eines Backups benötigen."

  - block: examples-slider-projects
    id: examples
    title: "Highlights kürzlich realisierter Projekte"
    items:
      - project: "Einrichtung eines Prometheus-Monitorings für ~200 Server"
        solution: "Installation eines Prometheus-Monitorings auf einem Ubuntu Linux 24.04 AWS Cloudserver und Anbindung von rund 200 Ubuntu- und Debian-Servern über ein WireGuard-Mesh, ausgerollt mit Ansible."
      - project: "Migration von SaltStack zu Ansible für Debian-Server"
        solution: "Umstellung von 27 Debian-Servern von SaltStack auf Ansible inklusive Entwicklung individueller Playbooks für Software-Rollouts und Wartung."
      - project: "Zentrales Logging mit systemd-journal-remote und Graylog"
        solution: "Zentrales Logging für mehrere IONOS- und Hetzner-Cloud-Server auf Debian mit systemd-journal-remote und Graylog inklusive Alarmierung."
      - project: "Automatisierte Backups mit BorgBackup in der IONOS Cloud"
        solution: "Rollout eines automatisierten Backup-Systems mit BorgBackup auf IONOS-Cloud-Servern inklusive angepasster Hook-Skripte."
      - project: "Einrichtung und Schulung für Wazuh Security Monitoring"
        solution: "Installation und Konfiguration von Wazuh in einer Multi-Cloud-Umgebung (Hetzner und AWS) sowie Schulung des Entwicklerteams der Kundschaft."
      - project: "Loadbalancer-Setup für eine stark frequentierte Website"
        solution: "Unterstützung beim Aufbau eines Multi-Server-Setups für eine PHP-FPM-Anwendung inklusive NFS für geteilten Speicher, Redis für Sessions, MariaDB Active-Active als HA-Datenbank und Haproxy zum Lastenausgleich."
      - project: "WireGuard-VPN für sicheren Remote-Zugriff"
        solution: "Aufbau eines WireGuard-VPNs über mehrere Cloud-Umgebungen, um weltweit verteilten Teams sicheren Zugriff zu ermöglichen."
      - project: "Einführung einer CI/CD-Pipeline für automatisierte Deployments"
        solution: "Konzeption und Implementierung einer CI/CD-Pipeline mit GitLab CI auf einem Debian-Server bei IONOS Cloud."
      - project: "Notfallwiederherstellungsplan: Entwicklung und Tests"
        solution: "Erstellung eines Desaster-Recovery-Plans gemeinsam mit dem Entwicklerteam der Kundschaft inklusive Umsetzung, Konfiguration, Testläufen und Dokumentation von Konfigurationsmanagement und Backup-Software."
      - project: "Security Hardening von Linux Servern"
        solution: "Umfassendes Hardening von Debian-Servern mit Firewall-Konfiguration, SSH-Härtung sowie Installation von Intrusion-Prevention- und -Detection-Systemen."
      - project: "Servermigration von On-Premises in die IONOS Cloud"
        solution: "Migration von 17 On-Premises-Servern in die IONOS Cloud inklusive Debian-Upgrade und Optimierung zahlreicher PHP-FPM- und Apache2-Einstellungen für Massen-WordPress-Hosting."

  - block: cta
    links:
      - url: "linux-consulting.html"
        title: "Projektbasierte Linux Beratung"
        text: "Linux Consulting für Projekte"
      - url: "linux-managed-hosting.html"
        title: "Linux Managed Hosting"
        text: "Linux Managed Hosting"
      - url: "qubes-os-beratung-und-support.html"
        title: "Qubes OS Beratung und Support"
        text: "Qubes OS Beratung und Support"
      - url: "linux-schulungen-und-workshops.html"
        title: "Linux Schulungen und Workshops"
        text: "Linux Schulungen und Workshops"
---
