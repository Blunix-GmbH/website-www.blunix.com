---
title: "Qubes OS Beratung und Support für Hochrisiko-Umgebungen"
description: "Wir integrieren das hochsichere Betriebssystem Qubes OS in Ihr Unternehmen: Evaluation, Installation, Konfiguration und laufender Support"
image: "/images/qubes-consulting/background.webp"

blocks:
  - block: hero-breadcrumb
    title: "Qubes OS Beratung und Support"
    subtitle: "Evaluation, Installation, Konfiguration und Wartung für Unternehmen und Freelancer"
    background: "/images/qubes-consulting/background.webp"
    breadcrumb: "Qubes OS Beratung und Support"
    notice: "Unsere Leistungen richten sich ausschließlich an Unternehmen in der EU & USA und erfordern eine gültige Umsatzsteuer-ID. Wir arbeiten nicht mit Privatkundschaft."

  - block: text-image-bg
    id: introduction
    title: "Qubes OS Enterprise Support"
    text: |
      [Qubes OS](https://www.qubes-os.org/) ist ein sicherheitsorientiertes Betriebssystem, das Desktop-Anwendungen konsequent in separaten virtuellen Maschinen isoliert. Dadurch wird eine Ausbreitung von Malware effektiv verhindert.

      Wir bei Blunix nutzen Qubes OS selbst, um unsere Arbeitsstationen bestmöglich abzusichern und höchste Datensicherheit für unsere Kundschaft zu gewährleisten.

      ### Schutz vor Wirtschaftsspionage

      Aktuelle geopolitische Entwicklungen sowie rasant fortschreitende KI verändern den Blick vieler Unternehmen auf IT-Sicherheit. Wir unterstützen Sie bei der reibungslosen Umstellung auf Qubes OS und beim sicheren Betrieb von Windows- und Linux-Anwendungen.
    image:
      src: "/images/qubes-consulting/qubes-penguin.webp"
      alt: "Ein würfelförmiger Pinguin"
    image_position: "right"
    image_bg: "blue"

  - block: features-grid
    id: features
    title: "Qubes OS Highlights"
    subtitle: "Qubes OS setzt auf mehrere Sicherheitslayer, um Desktop-Anwendungen strikt voneinander zu trennen. Selbst wenn der Browser kompromittiert wird, bleiben alle anderen Programme und Daten geschützt."
    items:
      - icon: "uil uil-brackets-curly"
        title: "Isolation durch Virtualisierung"
        details: "Qubes OS verwendet den [XEN-Hypervisor](https://wiki.xenproject.org/wiki/Xen_in_Qubes_OS_Security_Architecture), um Anwendungen voneinander abzuschirmen"
      - icon: "uil uil-user"
        title: "Benutzerfreundlich"
        details: "Auch nicht-technische Anwender profitieren von leicht verständlichen grafischen Tools"
      - icon: "uil uil-window-grid"
        title: "Windows- und Linux-VMs"
        details: "Betrieb sowohl von Windows-10- als auch Debian-Linux-12-Anwendungen in getrennten VMs"
      - icon: "uil uil-desktop"
        title: "Zertifizierte Hardware"
        details: "Qubes OS arbeitet mit [zertifizierten Hardware-Anbietern](https://www.qubes-os.org/doc/certified-hardware/) zusammen, die auf [Coreboot](https://www.coreboot.org/) setzen"
      - icon: "uil uil-code-branch"
        title: "Netzwerksegmentierung"
        details: "Physische Netzwerkschnittstellen, Firewall und VPN-Clients laufen in eigenen VMs"
      - icon: "uil uil-copy"
        title: "Template-basierte VMs"
        details: "VMs entstehen aus Template-Snapshots und werden beim Herunterfahren verworfen"
      - icon: "uil uil-arrow"
        title: "Infrastructure as Code"
        details: "Alle Aspekte lassen sich mit Skripten über die [qvm-Werkzeuge](https://www.qubes-os.org/doc/tools/) definieren"
      - icon: "uil uil-thumbs-up"
        title: "Granulare Allow-Policies"
        details: "Autorisierung von VM-zu-VM-Kommunikation, Dateifreigaben und mehr über Allow-Listen"

  - block: text-image-bg
    id: partners
    title: "Doppelt geprüft"
    text: |
      Gemeinsam mit externen IT-Sicherheitsprofis und Qubes-OS-Entwicklerinnen stellen wir sicher, dass unsere Arbeit für Hochsicherheitsprojekte doppelt geprüft wird.

      ### Sichere Business-Laptops

      Unser Partner [NovaCustom aus den Niederlanden](https://novacustom.com/) fertigt Qubes-OS-zertifizierte Business-Laptops, die sicher, zuverlässig und mit zusätzlichen Hardware-Sicherheitsfunktionen ausgestattet sind.

      ### Unterstützung für das Qubes-OS-Projekt

      10 % unserer Einnahmen aus Qubes-OS-Projekten spenden wir an [das Qubes-OS-Projekt](https://www.qubes-os.org/donate/).
    image:
      src: "/images/qubes-consulting/qubes-partners.webp"
      alt: "Ein Stapel würfelförmiger Pinguine"
    image_position: "left"
    image_bg: "green"

  - block: faq
    id: faq
    title: "Häufige Fragen zu Qubes-OS-Beratung"
    subtitle: "Weltweite Vor-Ort- und Online-Installation, Migrationssupport und individuelle Schulungen. [Kontaktieren Sie uns](index.html#contact), um zu erfahren, wie Qubes OS die Sicherheit Ihres Unternehmens erhöhen kann!"
    items:
      - q: "Wie viel kostet Qubes-OS-Consulting?"
        a: "Für Qubes-OS-Beratung berechnen wir **130,00 € pro Stunde**. Bei größeren Projekten schätzen wir den Gesamtaufwand und geben Ihnen ein **verbindliches Angebot** – dauert es länger, **zahlen Sie nur den geschätzten Betrag**, dauert es kürzer, **zahlen Sie entsprechend weniger**."
      - q: "Unterstützen Sie beim Migrieren von Daten und Tools?"
        a: "Ja. Wir übertragen sämtliche Daten und Anwendungen von Ihren bisherigen Arbeitsplätzen auf die neuen Qubes-OS-Systeme. Dafür legen wir Ihre Daten in einer vordefinierten Backup-Struktur ab, die anschließend wie ein normales Backup eingespielt wird."
      - q: "Bieten Sie Qubes-OS-Schulungen an?"
        a: "Ja. Wir haben einen vorbereiteten Schulungskurs, der alle wichtigen Aspekte von Qubes OS vermittelt. Das Curriculum passen wir an Ihre eingesetzten Anwendungen an. [Kontaktieren Sie uns](index.html#contact) für Details."
      - q: "Bieten Sie Vor-Ort-Support an?"
        a: "Ja, wir installieren Qubes OS weltweit vor Ort. Unsere Installationssoftware ist jedoch so gestaltet, dass das meist nicht nötig ist. Mit einer technisch versierten Person in Ihrem Team können wir in einem Online-Meeting die Einrichtung vollständig automatisieren."
      - q: "Womit automatisieren Sie die Qubes-OS-Konfiguration?"
        a: "Qubes OS nutzt intern Saltstack zur Automatisierung der Standard-VMs. Dieses Verfahren ist jedoch recht komplex und schwer zu vermitteln. Damit Sie den Aufbau Ihrer Arbeitsplätze nachvollziehen können, setzen wir auf Bash-Skripte mit [qvm-Befehlen](https://www.qubes-os.org/doc/tools/)."
      - q: "Wer kann Qubes-OS-Beratung buchen?"
        a: "Wir arbeiten mit Unternehmen sowie Einzelpersonen wie Menschenrechtsaktivistinnen und -aktivisten, Juristinnen und Juristen oder Journalistinnen und Journalisten. **Für soziale und Menschenrechtsorganisationen gewähren wir 20 % Rabatt.**"

  - block: process-timeline
    id: how-it-works
    title: "So führen wir Qubes OS in Ihrem Unternehmen ein"
    subtitle: "Wir begleiten jeden Schritt der Migration. Für technische und nicht-technische Mitarbeitende bieten wir 24/7-Support, damit der Umstieg auf Qubes OS stressfrei gelingt."
    steps:
      - icon: "uil uil-phone"
        heading: "Online-Meeting"
        text: "Gemeinsam mit Ihrem Team analysieren wir Ihr Bedrohungsmodell und erfassen sämtliche eingesetzten Anwendungen."
        right: false
      - icon: "uil uil-desktop"
        heading: "Hardware-Auswahl"
        text: "Wir helfen bei der Auswahl hochsicherer Laptops und Workstations, die Qubes OS unterstützen."
        right: true
      - icon: "uil uil-file-check"
        heading: "Schriftliches Angebot"
        text: "Sie erhalten ein Angebot, das alle Anforderungen und unseren Lösungsansatz mit Qubes OS detailliert beschreibt."
        right: false
      - icon: "uil uil-keyboard"
        heading: "Implementierung"
        text: "Wir definieren jede Komponente Ihrer Qubes-OS-Umgebung als Infrastructure-as-Code in leicht nachvollziehbaren Bash-Skripten."
        right: true
      - icon: "uil uil-graduation-cap"
        heading: "Schulung"
        text: "Wir erklären im Detail den Aufbau Ihres individuellen Qubes-OS-Setups, beantworten Fragen und trainieren Ihre Mitarbeitenden."
        right: false
      - icon: "uil uil-lock-alt"
        heading: "Migration"
        text: "Wir richten die Arbeitsplätze vor Ort ein oder unterstützen Ihr Team bei der automatisierten Einrichtung und beim Migrieren der Anwendungen."
        right: true
      - icon: "uil uil-wrench"
        heading: "Support & Updates"
        text: "Wir bieten 24/7/365 Support mit maximal einer Stunde Reaktionszeit und informieren Sie proaktiv über Sicherheitsupdates und neue Features."
        right: false

  - block: cta
    links:
      - url: "linux-emergency-support.html"
        title: "Linux Notfall Support"
        text: "Linux Notfall Support"
      - url: "linux-consulting.html"
        title: "Projektbasierte Linux Beratung"
        text: "Linux Consulting für Projekte"
      - url: "linux-managed-hosting.html"
        title: "Linux Managed Hosting"
        text: "Linux Managed Hosting"
      - url: "linux-personal-trainings-and-workshops.html"
        title: "Linux Schulungen und Workshops"
        text: "Linux Schulungen und Workshops"
---
