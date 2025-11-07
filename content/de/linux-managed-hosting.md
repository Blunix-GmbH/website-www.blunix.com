---
title: "Linux Managed Hosting mit FOSS Konfigurationsmanagement"
description: "Vollständig gemanagtes Linux-Hosting auf Debian mit root@-Zugriff und Open-Source-Konfigurationsmanagement, das exakt auf Ihre Bedürfnisse zugeschnitten ist"
image: "/images/linux-managed-hosting/background.webp"

blocks:
  - block: hero-breadcrumb
    title: "Managed Hosting für Unternehmen"
    subtitle: "Konfigurationsmanagement auf Ihrem IaaS- oder Cloud-Provider-Account"
    background: "/images/linux-managed-hosting/background.webp"
    breadcrumb: "Linux Managed Hosting"
    notice: "Unsere Leistungen richten sich ausschließlich an Unternehmen in der EU & USA und erfordern eine gültige Umsatzsteuer-ID. Wir arbeiten nicht mit Privatkundschaft."

  - block: text-image-bg
    id: introduction
    title: "Vollständig anpassbares, automatisiertes und sicheres Linux Managed Hosting"
    text: |
      Wir nutzen Ihren IaaS-Provider-Account, um Debian-Server mit [Terraform](https://www.terraform.io/) aufzusetzen. Anschließend installieren wir unseren FOSS-Hosting-Stack mit [Ansible](https://www.ansible.com/) und richten sämtliche Software ein, die Sie für interne und öffentliche Anwendungen benötigen.

      ### Die bestmögliche Linux-Hosting-Lösung

      Unser Hosting-Stack basiert zu 100 % auf [FOSS](https://de.wikipedia.org/wiki/Freie_Software) und enthält Backups, Monitoring, Gitlab, CI, zentrale Logfiles sowie ein internes WireGuard-VPN für Ihr Team. Wir liefern Ansible-Rollen für alle Anforderungen Ihres Hostings und entwickeln bei Bedarf neue Rollen. Während der gesamten Laufzeit steht Ihnen eine feste Linux-Consultantin bzw. ein fester Linux-Consultant zur Seite.
    image:
      src: "/images/linux-consulting/large-server-project.webp"
      alt: "Mehrere Pinguine arbeiten an einem großen Linux-Consulting-Projekt"
    image_position: "right"
    image_bg: "blue"

  - block: features-grid
    id: features
    title: "Managed-Hosting-Funktionen"
    items:
      - icon: "uil uil-brackets-curly"
        title: "Infrastructure as Code"
        details: "Definieren Sie [Ihre Server](https://www.terraform.io/) und [den kompletten Aufbau](https://www.ansible.com/) in [Git-Repositories](https://git-scm.com/)"
      - icon: "uil uil-code-branch"
        title: "Änderungshistorie"
        details: "Jede Änderung an Ihren Servern wird in einer Changelog-Datei dokumentiert"
      - icon: "uil uil-wrench"
        title: "Ihre Hosting-Werkzeugkiste"
        details: "[Backups](https://www.borgbackup.org/), [Monitoring](https://prometheus.io/), [Zentrale Logfiles](https://systemd.io/), [VPN](https://www.wireguard.com/), [Gitlab](https://about.gitlab.com/)"
      - icon: "uil uil-keyboard"
        title: "Für Entwicklerinnen und Entwickler gemacht"
        details: "[Blunix CLI ansible-cake](https://git.blunix.com/ansible-tools/ansible-cake) ist intuitiv bedienbar und [ausführlich dokumentiert](https://www.blunix.com/manual/index.html)"
      - icon: "uil uil-cloud-computing"
        title: "HA/HP und Mass Hosting"
        details: "Ausgelegt für stark frequentierte (Web-)Anwendungen, die hohe Performance, Sicherheit, Verfügbarkeit und Mass Hosting erfordern"
      - icon: "uil uil-plane-fly"
        title: "Individuelle Deployments"
        details: "Continuous Integration, Delivery & Deployment mit [Gitlab-CI](https://docs.gitlab.com/ee/ci/), Ansible, Docker oder Python3"
      - icon: "uil uil-padlock"
        title: "Security by Default"
        details: "[Private Netzwerke](https://www.wireguard.com/), [Firewall](https://shorewall.org/), [SSH](https://www.openssh.com/), [TLS](https://letsencrypt.org/), [IDS](https://suricata.io/), automatische Sicherheitsupdates, Workstations laufen mit [Qubes OS](https://www.qubes-os.org/)"
      - icon: "uil uil-key-skeleton-alt"
        title: "Volle Kontrolle und root@"
        details: "Kein Vendor Lock-in: Alles läuft auf Ihrem eigenen IaaS-Provider-Account"

  - block: features-list
    id: reliability
    title: "Stabil wie ein Fels"
    items:
      - icon: "uil uil-user-check"
        title: "Feste Ansprechperson"
        text: "Eine dedizierte Consultantin bzw. ein dedizierter Consultant betreut Ihr Projekt während der gesamten Vertragslaufzeit. Bei Fragen sprechen Sie immer direkt mit dieser Person."
      - icon: "uil uil-tachometer-fast-alt"
        title: "24/7/365 SLA-Notfallreaktion"
        text: "In der Regel reagieren wir sofort, garantiert innerhalb von maximal 60 Minuten. Wir reagieren auf Monitoring-Alerts und legen größten Wert darauf, robuste, fehlertolerante Systeme aufzubauen."
      - icon: "uil uil-shield-check"
        title: "Fokus auf Linux-Serversicherheit"
        text: "Wo immer möglich, stellen wir interne Dienste (Gitlab, Backups, Monitoring …) ausschließlich über das Unternehmens-VPN bereit. Wir setzen auf Linux-Sicherheitsbest Practices und arbeiten mit einem externen Security-Unternehmen zusammen, um unsere Lösungen laufend zu verbessern."
    image:
      src: "/images/linux-managed-hosting/reliable-penguin.webp"
      alt: "Ein Pinguin steht auf einem Felsen im Meer und hält eine Fackel"

  - block: video-demo
    id: cake
    label: "ansible-cake"
    title: "Zehn-Minuten-Demonstration"
    videoTitle: "youtube.com: ansible-cake Demonstrationsvideo abspielen"
    paragraphs:
      - "Schauen Sie zu, wie wir eine Beispielinfrastruktur für eine stark frequentierte PHP-Website von Grund auf einrichten."
      - "In diesem Video erstellen wir Server bei [Hetzner](https://www.hetzner.com/) mit [Terraform](https://www.terraform.io/), installieren unsere Hosting-Basiskomponenten – Gitlab, Prometheus Monitoring, Borg Backup, zentrale systemd-Logfiles und WireGuard VPN – und richten anschließend ein HA-Cluster für das Hosting einer beispielhaften PHP-Anwendung ein."
    image:
      src: "/images/linux-managed-hosting/matrix.webp"
      alt: "Matrix-Computerscreen"

  - block: process-timeline
    id: how-it-works
    title: "Hosting-Onboarding-Prozess"
    steps:
      - icon: "uil uil-phone"
        heading: "Online-Konferenz"
        text: "Zu Beginn führen wir eine Online-Konferenz durch, in der wir Ihre Anforderungen, Ziele und Erwartungen besprechen. Dieses Gespräch hilft uns, den Projektumfang zu verstehen und unser Managed Hosting präzise auf Ihre Bedürfnisse zuzuschneiden."
        right: false
      - icon: "uil uil-file-check-alt"
        heading: "Zugriff auf Ihren IaaS-Provider"
        text: "Wir benötigen Zugriff auf Ihre Infrastructure-as-a-Service-Ressourcen, wie in unserem [Hosting-Handbuch](https://www.blunix.com/manual/introduction/requirements/index.html) beschrieben. Dazu gehören vollständiger Zugriff auf Ihren IaaS-Account, Ihren DNS-Provider, ein SMTP-Konto für Monitoring-E-Mails sowie eine technisch versierte Kontaktperson in Ihrem Entwicklerteam."
        right: true
      - icon: "uil uil-keyboard"
        heading: "Setup"
        text: "Wir setzen alle Server mit Terraform auf, installieren unseren Hosting-Stack mit Ansible und implementieren alle benötigten Services – von internen Tools bis zu öffentlich erreichbaren Anwendungen."
        right: false
      - icon: "uil uil-graduation-cap"
        heading: "Schulung"
        text: "Damit Ihr Team selbstständig arbeitet, richten wir den Firmen-VPN-Zugang auf den Arbeitsplätzen ein und schulen Ihre Entwicklerinnen und Entwickler in Betrieb, Best Practices und häufigen Änderungen der Infrastruktur."
        right: true
      - icon: "uil uil-cloud-data-connection"
        heading: "Migration"
        text: "Wir planen und realisieren die Migration Ihrer Anwendungen, Daten und Services in die neue Umgebung mit minimaler – idealerweise null – Ausfallzeit. Dabei arbeiten wir eng mit Ihrem Team zusammen."
        right: false
      - icon: "uil uil-wrench"
        heading: "Wartung"
        text: "Wir sorgen dafür, dass Ihre Hosting-Umgebung sicher, effizient und aktuell bleibt. Regelmäßig stellen wir Ihnen neue Linux-Technologien vor, die wir in unseren Hosting-Werkzeugkasten aufnehmen."
        right: true

  - block: faq
    id: faq
    title: "Häufig gestellte Fragen"
    items:
      - q: "Gibt es Beschränkungen hinsichtlich Größe oder Umfang der betreuten Projekte?"
        a: "Blunix Hosting ist für Umgebungen mit bis zu etwa 500 Servern bzw. Debian-Installationen ausgelegt."
      - q: "Wie lange laufen Verträge für Ihr Managed Hosting üblicherweise?"
        a: "Viele Kundinnen und Kunden, die 2016 mit Gründung der Blunix GmbH zu uns kamen, arbeiten auch heute noch mit uns zusammen."
      - q: "Wie gehen Sie mit Datensouveränität und Compliance um?"
        a: "Die Blunix GmbH speichert keine Kundendaten auf eigenen Servern. Sämtliche Systeme Ihrer Infrastruktur – und damit alle Daten – laufen ausschließlich auf Servern in Ihren IaaS-Accounts. Auf unseren Arbeitsstationen speichern wir nur, was nötig ist, etwa SSH-Private-Keys für den Zugriff, E-Mails oder Chatprotokolle. Unsere Workstations verwenden [cryptsetup LUKS](https://gitlab.com/cryptsetup/cryptsetup) und laufen auf dem [Qubes Operating System](https://www.qubes-os.org/) für maximale Sicherheit. Den Zugriff auf Ihre Server führen wir in der Regel über Ihr Unternehmens-VPN ([WireGuard](https://www.wireguard.com/)) durch, das Teil unseres Hosting-Stacks ist."
      - q: "Welche Leistungskennzahlen überwachen Sie und wie werden diese bereitgestellt?"
        a: "Wir setzen [Prometheus](https://prometheus.io/) als Monitoring-System ein – ein flexibles Toolkit, das wir genau auf Ihre Anforderungen zuschneiden. Sagen Sie uns einfach, welche zusätzlichen Metriken Sie benötigen. Ergänzend installieren wir [Grafana](https://grafana.com/) und stellen Ihnen Dashboards mit allen Kennzahlen Ihrer Infrastruktur bereit."
      - q: "Welche Lizenzrichtlinien gelten für die in Ihrer Hosting-Umgebung genutzte Software?"
        a: "Alle von Blunix entwickelte Software wird unter der Apache-2-Lizenz veröffentlicht, sofern Sie nicht ausdrücklich proprietäre Entwicklungen beauftragen."
      - q: "Können Sie Referenzen, Case Studies oder Projektbeispiele nennen?"
        a: "Ja, bitte sprechen Sie uns an – wir stellen Ihnen gerne weitere Informationen bereit."
      - q: "Wie gehen Sie mit größeren Updates oder Upgrades der Infrastruktur um?"
        a: "Sobald eine neue Debian-Stable-Version erscheint, stellen wir Ihnen eine aktualisierte Blunix-Version bereit, die darauf basiert. Anstatt Distribution-Upgrades durchzuführen, richten wir neue Cloud-Server ein und migrieren alle Services. So kann Ihr Team die neue Infrastruktur ausführlich testen."
      - q: "Was passiert, wenn wir unser Hosting zu einem anderen Anbieter verlagern möchten?"
        a: "Blunix ist explizit ohne Vendor Lock-in konzipiert. Sie haben eine Kündigungsfrist von 30 Tagen und können anschließend einfach eine andere Administration für den FOSS-Stack beauftragen oder ihn selbst übernehmen."
      - q: "Wie stellen Sie Hochverfügbarkeit und Desaster Recovery sicher?"
        a: "Wir fragen für jeden Service, ob Ausfälle direkte Umsatzeinbußen verursachen. Falls ja, setzen wir auf Redundanz oder HA-Strategien: z. B. Active-Passive-Server, mehrere Webserver, Active-Active-Datenbanken, Ceph-Storage oder redundante Loadbalancer. Disaster Recovery ist durch unseren Hosting-Stack aus Terraform, Ansible und Borg Backup gewährleistet."
      - q: "Gibt es eine Testphase für Ihr Managed Hosting?"
        a: "Nein. Sollten Sie jedoch unzufrieden sein, sprechen wir offen darüber und berechnen im Extremfall keine Kosten."

  - block: pricing-2
    id: pricing
    title: "Transparente und flexible Preispläne"
    subtitle: "Wir haben noch nie ein SLA ohne faire Preisverhandlung unterschrieben."
    cards:
      - heading: "Garantierte Reaktionszeit<br>(optional)"
        description: "Bei kritischen Vorfällen, die Sie Geld kosten, reagieren wir in der Regel sofort. Falls Sie eine vertragliche Garantie benötigen, bieten wir maximale Reaktionszeiten bis zum Hands-on-Keyboard von bis zu 60 Minuten."
        tabs_id: "myTabone"
        tabs_toggle: "StarterContent"
        tabs:
          - id: "tab-6-hours"
            target: "start-6-hours"
            label: "6 Stunden"
          - id: "tab-3-hours"
            target: "start-3-hours"
            label: "3 Stunden"
          - id: "tab-1-hour"
            target: "start-1-hour"
            label: "1 Stunde"
        prices:
          - id: "start-6-hours"
            aria_label: "tab-6-hours"
            currency: "€"
            amount: "300"
            unit: "/Monat"
          - id: "start-3-hours"
            aria_label: "tab-3-hours"
            currency: "€"
            amount: "500"
            unit: "/Monat"
          - id: "start-1-hour"
            aria_label: "tab-1-hour"
            currency: "€"
            amount: "700"
            unit: "/Monat"
        gray_box: "Maximale Reaktionszeiten können für Werktage und Wochenenden sowie Tag- und Nachtzeiten individuell an Ihre Anforderungen angepasst werden."
        features:
          - text: "Notfalleinsätze erfolgen in der Regel umgehend"
            icon_color: "text-emerald-600"
          - text: "Individuelle Reaktionszeiten sind verhandelbar"
            icon_color: "text-emerald-600"
          - text: "Wir reagieren auf kritische Monitoring-Alerts"
            icon_color: "text-emerald-600"
          - text: "Flexible Preisoptionen verfügbar"
            icon_color: "text-emerald-600"

      - heading: "Anzahl der Debian-Server"
        description: "Für jede produktive Debian-Linux-Installation (physische Server oder virtuelle Maschinen, Docker ausgenommen) berechnen wir 50 € pro Monat, inklusive 30 Minuten Wartung."
        tabs_id: "myTabthree"
        tabs_toggle: "BusinessContent"
        input:
          id: "numberInput"
          placeholder: "Anzahl der Server"
          aria_controls: "busi-month"
        prices:
          - id: "busi-month"
            aria_label: "busi-month-tab"
            currency: "€"
            amount: "0"
            unit: "/Monat"
            result_id: "result"
        script:
          input_id: "numberInput"
          result_id: "result"
          multiplier: 50
        gray_box: "Umfangreiche oder unregelmäßige Zusatzaufgaben sind nicht enthalten und werden mit unserem SLA-Stundensatz von 100 € separat abgerechnet."
        features:
          - text: "Umfasst Wartung und Weiterentwicklung des Blunix-Stacks"
            icon_color: "text-emerald-600"
          - text: "Server können jederzeit hinzugefügt oder entfernt werden"
            icon_color: "text-emerald-600"
          - text: "Ähnliche Server gelten als ein System (Webworker-1, -2, -3, -4)"
            icon_color: "text-emerald-600"
          - text: "+5 für Backup, Monitoring, Gitlab, VPN, zentrale Logs"
            icon_color: "text-emerald-600"

      - heading: "Verfügbarkeitsgarantie<br>(optional)"
        description: "Garantierte Verfügbarkeit für bestimmte Debian-Server verrechnen wir über einen erhöhten Wartungssatz ab 50 € pro Server – abhängig von der gebuchten Uptime."
        tabs_id: "myTabuptime"
        tabs_toggle: "UptimeContent"
        tabs:
          - id: "busi-90-tab"
            target: "busi-90"
            label: "99,90 %<br><44 Min"
          - id: "busi-95-tab"
            target: "busi-95"
            label: "99,95 %<br><16 Min"
          - id: "busi-99-tab"
            target: "busi-99"
            label: "99,99 %<br><5 Min"
        prices:
          - id: "busi-90"
            aria_label: "busi-90-tab"
            currency: "€"
            amount: "100"
            unit: "/Monat/Server"
          - id: "busi-95"
            aria_label: "busi-95-tab"
            currency: "€"
            amount: "225"
            unit: "/Monat/Server"
          - id: "busi-99"
            aria_label: "busi-99-tab"
            currency: "€"
            amount: "350"
            unit: "/Monat/Server"
        gray_box: "Für garantierte Verfügbarkeit müssen Services hochverfügbar ausgelegt sein – mindestens ein Primärserver plus zwei Failover-Server."
        features:
          - text: "Wir streben ohnehin die maximale Uptime aller Systeme an"
            icon_color: "text-emerald-600"
          - text: "Garantien bilden die Unvorhersehbarkeit von Hosting nur bedingt ab"
            icon_color: "text-amber-600"
          - text: "Wir empfehlen sie nur, wenn Ihre Verträge dies vorgeben"
            icon_color: "text-amber-600"
          - text: "Wir bauen fehlertolerante, aber keine fehlersicheren Systeme"
            icon_color: "text-amber-600"

  - block: cta
    links:
      - url: "linux-emergency-support.html"
        title: "Linux Notfall Support"
        text: "Linux Notfall Support"
      - url: "linux-consulting.html"
        title: "Projektbasierte Linux Beratung"
        text: "Linux Consulting für Projekte"
      - url: "qubes-os-consulting-and-support.html"
        title: "Qubes OS Beratung und Support"
        text: "Qubes OS Beratung und Support"
      - url: "linux-personal-trainings-and-workshops.html"
        title: "Linux Schulungen und Workshops"
        text: "Linux Schulungen und Workshops"
---
