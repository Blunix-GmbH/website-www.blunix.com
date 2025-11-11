---
title: "Blunix GmbH Berlin: Linux Support, Consulting und Hosting"
description: "Linux Support 24/7, projektbasierte Linux-Beratung, Managed Hosting mit FOSS-Konfigurationsmanagement sowie Linux-Workshops und Schulungen"
image: "/images/index/background.webp"

blocks:
  - block: hero
    title: "Blunix GmbH Berlin"
    subtitle: "Linux Support und Consulting,<br>Managed Hosting und Schulungen"
    background: "/images/index/background.webp"
    cta:
      url: "#contact"
      title: "Kontakt"
      label: "Rund um die Uhr Linux-Support für Notfälle"

  - block: partners-scroller
    partners:
      - href: "https://www.qubes-os.org/"
        img: "/images/index/qubes-os-sm.webp"
        alt: "Qubes Logo"
      - href: "https://cloud.ionos.de/"
        img: "/images/index/ionos-sm.webp"
        alt: "IONOS Logo"
      - href: "https://www.hetzner.com/"
        img: "/images/index/hetzner-sm.webp"
        alt: "Hetzner Logo"
      - href: "https://www.proxmox.com/"
        img: "/images/index/proxmox-sm.webp"
        alt: "Proxmox Logo"
      - href: "https://www.debian.org/"
        img: "/images/index/debian-sm.webp"
        alt: "Debian Logo"
      - href: "https://www.openbsd.org/"
        img: "/images/index/openbsd-sm.webp"
        alt: "OpenBSD Logo"
      - href: "https://www.terraform.io/"
        img: "/images/index/terraform-sm.webp"
        alt: "Terraform Logo"
      - href: "https://www.ansible.com/"
        img: "/images/index/ansible-sm.webp"
        alt: "Ansible Logo"

  - block: about
    id: introduction
    title: "Linux Support und Hosting"
    paragraphs:
      - "Wir sind auf zuverlässige, sichere und performante Linux-Lösungen für Unternehmen spezialisiert."
      - "In einer sich rasant wandelnden Welt bevorzugen wir nachhaltige Innovationen anstatt kurzlebigen Trends nachzulaufen. Zuverlässigkeit, Automatisierung, Sicherheit und Innovationsgeist stehen bei uns an erster Stelle."
    image:
      src: "/images/index/ceo.webp"
      alt: "Peter Thurner, Geschäftsführer und Gründer der Blunix GmbH"
      caption: "Peter Thurner – Geschäftsführer und Gründer"

  - block: banner
    id: services
    text: "root@linux:~# support | consulting | hosting | training"

  - block: text-image
    id: support
    image_left: true
    image:
      src: "/images/index/linux-emergency-support.webp"
      alt: "Linux-Notfallsupport"
    title: "Linux-Support rund um die Uhr"
    text: |
      Unsere Linux-Experten stehen Ihnen zu jeder Tages- und Nachtzeit in allen Notfallsituationen mit dedizierten Linux-Servern und Cloud Instanzen zur Seite.

      Wir bieten schnelle und professionelle Hilfe für alle kritischen Probleme mit ausgefallenen Diensten und Netzwerkstörungen sowie allen gängigen Themen rund um Ihre Linux Hosting Infrastruktur.

      ### Linux Support für Unternehmen

      Unser auf Debian Linux und OpenBSD spezialisiertes Experten-Team bietet Ihnen schnelle und unbürokratische Sofort-Hilfe für Linux Notfälle.
    buttons:
      - label: "Weitere Informationen"
        title: "Linux Support"
        url: "linux-support.html"
      - label: "+49 30 / 629 318 76"
        title: "Blunix GmbH Telefonnummer"
        url: "tel:+493062931876"
        color: "red"
        hover_color: "green"

  - block: text-image
    id: consulting
    image_left: false
    image:
      src: "/images/index/linux-consulting.webp"
      alt: "Zwei Pinguine arbeiten an einem Linux-Consulting-Projekt"
    title: "Linux Consulting und Beratung für große und kleine Projekte"
    text: |
      Als Spezialisten rund um Linux-Server unterstützen wir Unternehmen bei der Konzeptionierung, Realisierung und dem Betrieb von Linux-Anwendungen aller Art.

      Wir bieten professionelle Linux-Beratung für Netwerke, Datenbanken, Webserver und allen anderen gängigen Komponenten von produktiven Linux-Infrastrukturen.

      ### Unsere Linux-Experten beraten Sie gerne

      Wir stehen Ihnen mit Rat und Tat zum allen Themen rund um Debian-Linux- und OpenBSD-Server in Cloud-Umgebungen, auf dedizierten Servern sowie Ihren eigenen Colocations zur Seite.
    buttons:
      - label: "Weitere Informationen"
        title: "Linux Consulting"
        url: "linux-consulting.html"
      - label: "Sprechen Sie mit unseren Consultants"
        title: "Kontakt"
        url: "#contact"
        color: "green"
        hover_color: "indigo"

  - block: text-image
    id: hosting
    image_left: true
    image:
      src: "/images/index/linux-managed-hosting.webp"
      alt: "Eine Gruppe Kaiserpinguine verwaltet mehrere Serverschränke"
    title: "Linux Managed Hosting mit FOSS und Konfigurationsmanagement"
    text: |
      Unsere [FOSS-basierte Ansible-Hosting-Lösung](https://git.blunix.com/welcome/index.html) bietet sichere und automatisierte Serververwaltung in Ihren IaaS-Provider-Konten.

      Die [Linux Hosting Dokumentation](https://www.blunix.com/manual/index.html) ermöglicht es Ihren Mitarbeitern, alle alltäglichen Aufgaben schnell und effizient durchzuführen.

      ### Ein langfristig zugeteilter Ansprechpartner

      Jeder SLA-Kunde erhält einen festen, langfristigen Linux-Berater. Sie sprechen immer mit derselben Person. Kein First-Level-Support.
    buttons:
      - label: "Individuelles Linux Hosting erklärt"
        title: "Linux Managed Hosting"
        url: "linux-managed-hosting.html"
      - label: "Mit unseren Linux-Ingenieuren sprechen"
        title: "Kontakt"
        url: "#contact"
        color: "green"
        hover_color: "indigo"

  - block: text-image
    id: qubes
    image_left: false
    image:
      src: "/images/index/qubes-os-consulting-and-support.webp"
      alt: "Ein Kaiserpinguin ist in Würfel partitioniert, um Malware-Ausbreitung zu verhindern"
    title: "Qubes OS Beratung und Support"
    text: |
      [Qubes OS](https://www.qubes-os.org/) ist ein hochsicheres Betriebssystem für Workstations, das den [XEN-Hypervisor](https://wiki.xenproject.org/wiki/Xen_in_Qubes_OS_Security_Architecture) nutzt, um Windows- und Linux-Anwendungen voneinander sicher zu isolieren. Dieser Ansatz verhindert effektiv die Ausbreitung von Malware.

      Bei Blunix GmbH laufen alle Arbeitsstationen unserer Mitarbeiter mit Qubes OS.

      ### Hochsicheres Desktop-Betriebssystem

      Wir unterstützen Unternehmen und Einzelpersonen mit hohen Sicherheitsanforderungen bei der Bewertung, Installation und Konfiguration von Qubes OS sowie beim 24/7/365 Support.
    buttons:
      - label: "Mehr über Qubes OS erfahren"
        title: "Qubes OS Consulting und Support"
        url: "qubes-os-beratung-und-support.html"
      - label: "Qubes OS mit uns evaluieren"
        title: "Kontakt"
        url: "#contact"
        color: "green"
        hover_color: "indigo"

  - block: text-image
    id: training
    image_left: true
    image:
      src: "/images/index/linux-workshops-and-training.webp"
      alt: "Ein Kaiserpinguin unterrichtet Linux an verschiedene Tiere"
    title: "Linux Schulungen, Workshops und Kurse"
    text: |
      Unsere Linux-Kurse werden individuell nach Ihren Bedürfnissen gestaltet. Wir vermitteln praxisnahes Wissen, das sofort angewendet werden kann.

      Jede Schulung vermittelt nicht nur Grundlagen, sondern adressiert gezielt die Herausforderungen Ihres Unternehmens.

      ### Lernen Sie von Linux-Experten, die mit Leidenschaft lehren

      Motivation entsteht durch Begeisterung. Die Leidenschaft unserer Linux-Ingenieure sorgt für ein engagiertes und bereicherndes Lernumfeld.
    buttons:
      - label: "Detaillierte Informationen"
        title: "Persönliche Linux-Schulungen und Workshops"
        url: "linux-schulungen-und-workshops.html"
      - label: "Mit unseren Linux-Trainern sprechen"
        title: "Kontakt"
        url: "#contact"
        color: "green"
        hover_color: "indigo"

  - block: banner
    id: ideology
    text: "business@blunix:~$ ideology"

  - block: ethics-accordion
    id: ethics
    title: "Unternehmensethik, Werte und Prioritäten"
    images:
      left_top:
        src: "/images/index/ideology-partnership.webp"
        alt: "Ein Kaiserpinguin gibt einer Giraffe die Hand"
      left_bottom:
        src: "/images/index/ideology-penguin-stable-software.webp"
        alt: "Ein Kaiserpinguin steht auf einem Anker"
      right:
        src: "/images/index/ideology-penguin-work-life-balance.webp"
        alt: "Ein Kaiserpinguin hinter einer Waage"
    items:
      - question: "Was sind die Kernwerte?"
        answer: "Die Blunix GmbH legt Wert auf Stabilität der Betriebsabläufe, hohe Servicequalität, Integrität in Partnerschaften sowie das Wohlergehen des Teams — statt auf kurzfristige Gewinnmaximierung."
      - question: "Wie stellt Blunix GmbH Qualität und Konsistenz sicher?"
        answer: "Unser Ansatz basiert auf langfristigen Kundenbeziehungen, unterstützt durch stabile Einnahmen aus SLA-Vereinbarungen. Dies ermöglicht hochwertige Engineering-Leistungen. Unsere Kunden haben stets denselben festen Ansprechpartner für ihre Infrastruktur."
      - question: "In welchem Bereich liegt die Expertise?"
        answer: "Unsere Expertise konzentriert sich klar auf die Automatisierung von Debian Linux- und OpenBSD-Hosting-Umgebungen, gemäß der [UNIX-Philosophie 'Do one thing and do it well'](https://de.wikipedia.org/wiki/Unix-Philosophie)."
      - question: "Worin unterscheidet sich Blunixs Ansatz zu Kundenbeziehungen von Mitbewerbern?"
        answer: "Viele Mitbewerber priorisieren Wachstum und Gewinn, oft auf Kosten der Kundenzufriedenheit. Für uns ist Linux-Administration nicht nur ein Beruf — sondern eine Leidenschaft. Das tägliche Entdecken, Automatisieren und Lernen motiviert uns intrinsisch."

  - block: banner
    id: blog
    text: "blog@blunix:~$ while read line"

  - block: blog-cards
    list_url: "blog/index.html"
    list_title: "Alle Blogbeiträge anzeigen"
    list_heading: "Der Blunix Blog – Command Line Chronicles"

  - block: banner
    id: contact
    text: "contact@blunix:~$ mail"

  - block: contact-standard
    id: unencrypted-contact
    title:
    notice: "Unsere Dienstleistungen richten sich ausschließlich an Unternehmen in der EU & USA und erfordern eine gültige USt-ID. Wir arbeiten nicht mit Privatkunden."
    items:
      - logo: "/images/index/telephone-logo.webp"
        logo_alt: "Telefon Logo"
        url: "tel:+493062931876"
        url_title: "Blunix GmbH anrufen"
        heading: "Telefon"
        contact: "+49 30 / 629 318 76"
        contact_url: "tel:+493062931876"
        contact_title: "Blunix GmbH anrufen"
      - logo: "/images/index/emergency-telephone-logo.webp"
        logo_alt: "Notfall-Telefon Logo"
        url: "tel:+493062932267"
        url_title: "Blunix GmbH Notfall-Support anrufen"
        heading: "24/7/365 LINUX NOTFALL TELEFON"
        contact: "+49 30 / 629 322 67"
        contact_url: "tel:+493062932267"
        contact_title: "Blunix GmbH Notfall-Support anrufen"
        description_text: "Nur für Notfälle"
      - logo: "/images/index/email-logo.webp"
        logo_alt: "E-Mail Logo"
        url: "mailto:info@blunix.com"
        url_title: "E-Mail an info@blunix.com senden"
        heading: "E-Mail"
        contact: "info@blunix.com"
        contact_url: "mailto:info@blunix.com"
        contact_title: "E-Mail an info@blunix.com senden"
---
