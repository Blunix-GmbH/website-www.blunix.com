---
title: "Linux Support and Expert Advice for Businesses"
description: "Linux Support for distribution upgrades, migrations, evaluation of new technologies, best-practice checkups and general good advice."
image: "/images/linux-support/background.webp"

blocks:
  - block: hero-breadcrumb
    title: "Linux Support"
    subtitle: "Expert Support and Advice from Professional Linux Administrators"
    background: "/images/linux-support/background.webp"
    breadcrumb: "Linux Support"
    notice: "Our services are exclusively for businesses in the EU & USA and require a valid business tax ID. We do not work with consumers or end users."

  - block: text-image-bg
    id: support
    title: "Debian and OpenBSD Support for Servers"
    text: |
      Plan and execute changes to your infrastructure with professional assistance. We assist in distribution version upgrades, migrations, implementing new tools and services. Linux Support and Expert Advice for Businesses.

      ### A Helping Hand for Challanging Server Setups

      We make sure our customers understand the problem at hand and provide documentation of the issue for future reference. Our goal is to provide you with transparent and reliable solutions.
    image:
      src: "/images/linux-support/linux-support-and-openbsd-support.webp"
      alt: "Linux Support - available 24 hours a day in case of emergency"
    image_position: "left"
    image_bg: "blue"

  - block: features-grid
    id: features
    title: "Debian Linux Support for All Common Tools and Applications"
    subtitle: "We offer extensive experience in administrating all programs common on Linux servers and cloud environments."
    items:
      - icon: "uil uil-server"
        title: "Web Servers"
        details: "Nginx, Apache2, Lighttpd"
      - icon: "uil uil-envelope"
        title: "Mail Servers"
        details: "OpenSMTPD instead of Postifx, IMAP with Dovecot"
      - icon: "uil uil-database"
        title: "Databases"
        details: "PostgreSQL, MySQL/MariaDB, Redis, RabbitMQ"
      - icon: "uil uil-shield"
        title: "Network Support"
        details: "IPtables, NFtables, fail2ban, wazuh"
      - icon: "uil uil-window-grid"
        title: "Virtualization"
        details: "QEMU/KVM, Proxmox, docker"
      - icon: "uil uil-cog"
        title: "Automation"
        details: "BASH, Python"
      - icon: "uil uil-setting"
        title: "Configuration Management"
        details: "Ansible, Terraform"
      - icon: "uil uil-chart-line"
        title: "Backup, Monitoring, Logfiles"
        details: "Borgbackup, Prometheus, systemd-journal"

  - block: process-timeline
    id: linux-support-process
    title: "Linux Support Process"
    steps:
      - heading: "Get in Contact With Us"
        text: "Show us exactly what changes, upgrades or new components are required."
        icon: "uil uil-phone"
        right: true
      - heading: "Implementation"
        text: "We access your infrastructure by SSH, implement the changes and document our work."
        icon: "uil uil-wrench"
        right: false
      - heading: "Further Assistance"
        text: "Blunix is always available for help or an interesting chat about Linux."
        icon: "uil uil-file-info-alt"
        right: true

  - block: faq
    id: frequently-asked-questions
    title: "Frequently Asked Questions"
    items:
      - q: "Do you provide support for private users?"
        a: "Our partner [linuxguides.de](https://www.linuxguides.de/linux-support/) offers Linux support services for endusers and provides expert knowledge about Ubuntu on Laptops and Tower-PCs, Printers, broken hard drives and other related topics."
      - q: "What Linux distributions do you focus on?"
        a: "Blunix is primarily focused on Debian based distributions and uses OpenBSD in sensitive environments. Additionally, we offer support and consulting for the highsecurity desktop Linux distribution Qubes OS."
      - q: "Do you offer support for hardware related issues?"
        a: "Our primary field of expertise is the administration of Linux server and cloud environments. While we can debug and identify faulty hardware on the command line, we do not offer expertise for the management of hardware. We can give recommendations about which server to rent on dedicated and cloud server providers."
      - q: "During which times is your Linux support available?"
        a: "Our Linux expert support is available from monday to friday from 10am to 6pm on workdays, timezone Europe/Berlin."
      - q: "Do you offer 24/7/365 emergency support?"
        a: "Yes, please refer to our [contact page](index.html#contact) and dial our emergency telephone number."
      - q: "How much do you charge for advice?"
        a: "Advice is always free, we only charge for hands-on-keyboard time. If you just need a second opinion, we are always happy to chat about Linux."
      - q: "What has to be done before we start?"
        a: "Please make sure to have a full backup of all data and files from the servers you want our professional Linux administrators to work on. Please inform us before the project if you require assistance in creating a backup."

  - block: examples-slider-projects
    id: examples
    title: "Highlights of Recently Realized Projects"
    items:
      - project: "Setup of Prometheus Monitoring System for ~200 Servers"
        solution: "Installation of a Prometheus monitoring system on Ubuntu Linux 24.04 AWS cloud server, attaching ~200 Ubuntu and Debian-based servers using a WireGuard mesh network deployed with Ansible."
      - project: "Migration from SaltStack to Ansible for Managing Debian Servers"
        solution: "Transitioned 27 Debian servers from SaltStack to Ansible for configuration management, including the development of custom Ansible playbooks for software deployment and server maintenance."
      - project: "Setup of Centralized Logging with Systemd-Journal-Remote and Graylog"
        solution: "Configured centralized logging for several IONOS cloud and Hetzner cloud servers running Debian using Systemd-Journal-Remote and Graylog including alerts."
      - project: "Automated Backup Solution with BorgBackup on IONOS Cloud"
        solution: "Deployed an automated backup system using BorgBackup across IONOS Cloud servers, including customized hook scripts."
      - project: "Setup and Training for Wazuh Security Monitoring"
        solution: "Installed and configured Wazuh on a multi-cloud environment (Hetzner and AWS) and trained the clients developers for using and maintaining Wazuh."
      - project: "Load Balancer Configuration for High-Traffic Website"
        solution: "Assisted in implementing a multi-server-setup for a PHP-FPM application, including NFS for shared storage, redis for shared sessions, mariadb Active-AA as HA database and haproxy for loadbalancing."
      - project: "Deployment of WireGuard VPN for Secure Company Remote Access"
        solution: "Set up a WireGuard VPN across various cloud environments to enable secure remote access for a team of globally spread employees."
      - project: "Implementation of CI/CD Pipeline for Automated Deployments"
        solution: "Designed and implemented a CI/CD pipeline using GitLab CI on a Debian server hosted with IONOS cloud."
      - project: "Disaster Recovery Plan Development and Testing"
        solution: "Developed a disaster recovery plan in cooperating with the clients developers which includes implementation, configuration, test-runs and documentation of configuration management and backup software."
      - project: "Security Hardening of Linux Servers"
        solution: "Conducted a comprehensive security hardening process on Debian servers, including firewall configuration, SSH hardening, and installation of intrusion prevention and detection systems."
      - project: "Server Migration from On-Premise to IONOS Cloud"
        solution: "Migrated 17 on-premises servers to IONOS Cloud and upgraded the Debian version along the way. Improved several PHP-FPM and apache2 settings to optimize mass wordpress hosting."

  - block: cta
    links:
      - url: "linux-consulting.html"
        title: "Project based Linux Consulting"
        text: "Linux Consulting for Projects"
      - url: "linux-managed-hosting.html"
        title: "Linux Managed Hosting"
        text: "Linux Managed Hosting"
      - url: "qubes-os-consulting-and-support.html"
        title: "Qubes OS Consulting and Support"
        text: "Qubes OS Consulting and Support"
      - url: "linux-personal-trainings-and-workshops.html"
        title: "Linux Trainings und Workshops"
        text: "Linux Trainings and Workshops"
---
