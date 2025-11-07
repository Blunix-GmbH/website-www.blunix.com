---
title: "Linux Managed Hosting using FOSS Configuration Management"
description: "Fully managed Linux Hosting on Debian with root access using Open Source Configuration Management that is customized to your needs"
image: "/images/linux-managed-hosting/background.webp"

blocks:
  - block: hero-breadcrumb
    title: "Enterprise Managed Hosting"
    subtitle: "Configuration Management on your IaaS- or Cloud-Provider Account"
    background: "/images/linux-managed-hosting/background.webp"
    breadcrumb: "Linux Managed Hosting"
    notice: "Our services are exclusively for businesses in the EU & USA and require a valid business tax ID. We do not work with consumers or end users."

  - block: text-image-bg
    id: introduction
    title: "Fully Customizable, Fully Automated and Secure Linux Managed Hosting"
    text: |
      We use your IaaS provider account to setup Debian servers using [Terraform](https://www.terraform.io/), followed by the installation of our FOSS hosting software stack with [Ansible](https://www.ansible.com/), which is also used to install all the software required for both your internal and public-facing applications.

      ### Best possible Linux Hosting Solution

      Our hosting stack is 100% [FOSS](https://en.wikipedia.org/wiki/Free_and_open-source_software) based and comes with Backups, Monitoring, Gitlab, CI, Central Logfiles and an internal Wireguard-VPN for your employees. We provide Ansible-roles for all of your hosting needs and develop additional roles as required. You are provided one permanent Linux consultant for the duration of your contract.
    image:
      src: "/images/linux-consulting/large-server-project.webp"
      alt: "Several penguins are working on large Linux consulting project"
    image_position: "right"
    image_bg: "blue"

  - block: features-grid
    id: features
    title: "Managed Hosting Features"
    items:
      - icon: "uil uil-brackets-curly"
        title: "Infrastructure as Code"
        details: "Define [your servers](https://www.terraform.io/) and [complete setup](https://www.ansible.com/) in [git repositories](https://git-scm.com/)"
      - icon: "uil uil-code-branch"
        title: "Change History"
        details: "Every change to your servers is saved in a changelog file"
      - icon: "uil uil-wrench"
        title: "Your Hosting Toolbox"
        details: "[Backups](https://www.borgbackup.org/), [Monitoring](https://prometheus.io/), [Central Logfiles](https://systemd.io/), [VPN](https://www.wireguard.com/), [Gitlab](https://about.gitlab.com/)"
      - icon: "uil uil-keyboard"
        title: "Designed for Developers"
        details: "[Blunix CLI ansible-cake](https://git.blunix.com/ansible-tools/ansible-cake) is very intuitive and [well-documented](https://www.blunix.com/manual/index.html)"
      - icon: "uil uil-cloud-computing"
        title: "HA/HP and Mass Hosting"
        details: "Designed for very busy (web) applications that require high performance, security and uptime, and mass hosting setups"
      - icon: "uil uil-plane-fly"
        title: "Tailored Deployment"
        details: "Continous Integration, Delivery & Deployment with [Gitlab-CI](https://docs.gitlab.com/ee/ci/), Ansible, Docker or Python3"
      - icon: "uil uil-padlock"
        title: "Secure by Default"
        details: "[Private Networks](https://www.wireguard.com/), [Firewall](https://shorewall.org/), [SSH](https://www.openssh.com/), [TLS](https://letsencrypt.org/), [IDS](https://suricata.io/), automatic security upgrades, workstations run [Qubes OS](https://www.qubes-os.org/)"
      - icon: "uil uil-key-skeleton-alt"
        title: "Full Ownership and root@"
        details: "We ensure your infrastructure stays free of vendor lock-in. Everything runs on your own IaaS provider account"

  - block: features-list
    id: reliability
    title: "Steady as a rock"
    items:
      - icon: "uil uil-user-check"
        title: "Permanent Contact Person"
        text: "A dedicated consultant oversees your project for the entirety of your contract. You directly talk to this one person whenever you contact us."
      - icon: "uil uil-tachometer-fast-alt"
        title: "24/7/365 SLA Emergency Response"
        text: "Generally immediately, guaranteed within up to 60 minutes. We respond to monitoring alerts and emphasize building robust, fault-tolerant systems over responding to problems."
      - icon: "uil uil-shield-check"
        title: "Focused on Linux Server Security"
        text: "Where possible, we make all internal services (like Gitlab, Backups, Montiroing, ...) accessible from the company VPN only. We implement Linux security best practices and cooperate with an external Linux security company to constantly upgrade our security solutions."
    image:
      src: "/images/linux-managed-hosting/reliable-penguin.webp"
      alt: "A penguin is standing on a rock in the ocean holding a torch"

  - block: video-demo
    id: cake
    label: "ansible-cake"
    title: "Ten Minute Demonstration"
    videoTitle: "youtube.com: Play ansible-cake demonstration video"
    paragraphs:
      - "Watch us set up an example infrastructure for hosting a very busy PHP website from scratch."
      - "In this video, we create servers on [hetzner](https://www.hetzner.com/) using [terraform](https://www.terraform.io/), install our Hosting Baseline of Gitlab, Prometheus Monitoring, Borg Backup, systemd central log files and WireGuard VPN, and then setup a cluster for HA (High Availability) Hosting of an exemplary PHP Application."
    image:
      src: "/images/linux-managed-hosting/matrix.webp"
      alt: "Matrix computer screen"

  - block: process-timeline
    id: how-it-works
    title: "Hosting Onboarding Process"
    steps:
      - icon: "uil uil-phone"
        heading: "Online Conference"
        text: "The journey begins with an initial online conference, where we discuss your specific needs, objectives, and expectations. This session is crucial for understanding the scope of your project and allows us to tailor our managed hosting services to fit your requirements precisely."
        right: false
      - icon: "uil uil-file-check-alt"
        heading: "IaaS-Provider Access"
        text: "We require access to your Infrastructure as a Service (IaaS) resources as [documented in our hosting manual](https://www.blunix.com/manual/introduction/requirements/index.html). This includes full access to your IaaS provider account, your DNS provider account, one monitoring email address SMTP account as well as one technically versed contact person in your team of developers."
        right: true
      - icon: "uil uil-keyboard"
        heading: "Setup"
        text: "We set up all servers using Terraform, install our hosting solutions using Ansible, and implement all the services you require, including both your internal and public-facing applications and tools."
        right: false
      - icon: "uil uil-graduation-cap"
        heading: "Training"
        text: "To empower your developers, we assist with configuring the company-internal VPN access on your employees workstations and provide comprehensive training covering system management and best practices for making common changes to the infrastructure."
        right: true
      - icon: "uil uil-cloud-data-connection"
        heading: "Migration"
        text: "We plan and execute the migration of your applications, data, and services to the new environment to ensure minimal or, if possible, zero downtime. Our team works closely with your developers to guarantee a smooth transition."
        right: false
      - icon: "uil uil-wrench"
        heading: "Maintenance"
        text: "We are dedicated to ensuring your hosting environment remains secure, efficient, and up-to-date. Regularly, we will introduce you to new Linux technologies that we incorporate into our hosting toolkit."
        right: true

  - block: faq
    id: faq
    title: "Frequently Asked Questions"
    items:
      - q: "Are there any limitations on the size or scale of the projects you manage?"
        a: "Blunix Hosting is effective up to about 500 servers or installations of Debian Linux."
      - q: "What is the typical duration of a contract for your managed hosting services?"
        a: "Most of the customers we gained while Blunix GmbH was founded in 2016 are still with us."
      - q: "How do you handle data sovereignty and compliance issues?"
        a: "Blunix GmbH does not store any customer data on its own servers. All servers of your infrastructure, containing all your data, are exclusively on the servers hosted with your IaaS provider accounts. We save data of your company on our workstations as required, like SSH private keys used to access your servers, emails from you as well as chat logs with you. We use [cryptsetup LUKS encryption](https://gitlab.com/cryptsetup/cryptsetup) on our workstations, which run the [Qubes Operating System](https://www.qubes-os.org/) for maximum security. We commonly access your servers over your company VPN ([Wireguard](https://www.wireguard.com/)) that is part of the Blunix hosting stack."
      - q: "What performance metrics do you monitor, and how are these reported to clients?"
        a: "We use [Prometheus](https://prometheus.io/) as Monitoring System, which is essentially a toolkit for building your own monitoring system. You simply tell us what you want to monitor on top of what is configured by default, and we add it to Prometheus. On top of this we install [Grafana](https://grafana.com/) to provide you with dashboards showing all metrics of your infrastructure."
      - q: "What is your policy on software licensing for the tools and services used in the hosting environment?"
        a: "All software developed by Blunix is licenced under the Apache2 License unless you request us to develop closed-source software for you."
      - q: "Can you provide customer references, case studies or examples of previous projects?"
        a: "Yes, please contact us for more information."
      - q: "What is the process for handling major updates or upgrades to the infrastructure?"
        a: "Whenever there's a new Debian stable release, we provide you with a new version of Blunix that's based on this Debian release. We generally don't run distribution upgrades. Instead, we set up new cloud servers and move all services from old to new servers. This allows your developers to test out the new infrastructure and new versions of software before the change."
      - q: "What happens if we decide to move our hosting away from your service?"
        a: "Blunix is explicitly designed to not put you into a vendor lock-in. You have a 30 days cancellation period, after which you can simply find another administrator for the Blunix FOSS stack, or you learn how to maintain it yourself."
      - q: "How do you ensure high availability and disaster recovery?"
        a: "We ask you for each service you run whether downtime would cause you to directly lose money. If this is the case, we setup such services redundantly or with an HA strategy. For example: Active-Passive Servers, Multiple Webservers, Active-Active Databases, Ceph Storage, redundant Loadbalancers. Disaster recovery is ensured by the Blunix hosting stack itself: Terraform, Ansible and Borg Backup."
      - q: "Is there a trial period for your managed hosting services?"
        a: "No, but if you are very unhappy with our service we are open to discuss this, and in extreme cases we simply won't charge you."

  - block: pricing-2
    id: pricing
    title: "Transparent and Flexible Pricing Plans"
    subtitle: "We've never signed a SLA without fairly negotiating the price."
    cards:
      - heading: "Guaranteed Response Time<br>(Optional)"
        description: "We generally respond immediately to critical problems causing you to lose money. If you require a contractual guarantee, we provide up to 60 minutes of guaranteed maximum response time until hands-on-keyboard."
        tabs_id: "myTabone"
        tabs_toggle: "StarterContent"
        tabs:
          - id: "tab-6-hours"
            target: "start-6-hours"
            label: "6 hours"
          - id: "tab-3-hours"
            target: "start-3-hours"
            label: "3 hours"
          - id: "tab-1-hour"
            target: "start-1-hour"
            label: "1 hour"
        prices:
          - id: "start-6-hours"
            aria_label: "tab-6-hours"
            currency: "€"
            amount: "300"
            unit: "/month"
          - id: "start-3-hours"
            aria_label: "tab-3-hours"
            currency: "€"
            amount: "500"
            unit: "/month"
          - id: "start-1-hour"
            aria_label: "tab-1-hour"
            currency: "€"
            amount: "700"
            unit: "/month"
        gray_box: "Maximum response times are customizable for workdays and weekends, as well as day and night times, based on your company's requirements."
        features:
          - text: "Emergency responses are generally immediate"
            icon_color: "text-emerald-600"
          - text: "Custom response times are negotiable"
            icon_color: "text-emerald-600"
          - text: "We respond to critical monitoring alerts"
            icon_color: "text-emerald-600"
          - text: "Flexible pricing options are available"
            icon_color: "text-emerald-600"

      - heading: "Number of Debian Servers"
        description: "For each operational Debian Linux installation (including physical servers and virtual machines, excluding Docker), we charge €50 monthly, which accounts for 30 minutes of monthly maintenance."
        tabs_id: "myTabthree"
        tabs_toggle: "BusinessContent"
        input:
          id: "numberInput"
          placeholder: "Number of Servers"
          aria_controls: "busi-month"
        prices:
          - id: "busi-month"
            aria_label: "busi-month-tab"
            currency: "€"
            amount: "0"
            unit: "/month"
            result_id: "result"
        script:
          input_id: "numberInput"
          result_id: "result"
          multiplier: 50
        gray_box: "Extensive and unregular maintenance tasks or additional work are not covered under this fee and will be billed separately at our standard SLA customer rate of €100 per hour."
        features:
          - text: "Covers Maintenance and continuous development of Blunix"
            icon_color: "text-emerald-600"
          - text: "You can add and remove servers at any time"
            icon_color: "text-emerald-600"
          - text: "Similar servers count as one (Webworker-1, -2, -3, -4)"
            icon_color: "text-emerald-600"
          - text: "+5 for Backup, Monitoring, Gitlab, VPN, central logs"
            icon_color: "text-emerald-600"

      - heading: "Server Uptime Guarantee<br>(Optional)"
        description: "Booking guaranteed uptime for specific Debian servers is billed by increasing the maintenance cost from €50 per server, depending on the booked uptime."
        tabs_id: "myTabuptime"
        tabs_toggle: "UptimeContent"
        tabs:
          - id: "busi-90-tab"
            target: "busi-90"
            label: "99.90 %<br><44min"
          - id: "busi-95-tab"
            target: "busi-95"
            label: "99.95 %<br><16min"
          - id: "busi-99-tab"
            target: "busi-99"
            label: "99,99 %<br><5min"
        prices:
          - id: "busi-90"
            aria_label: "busi-90-tab"
            currency: "€"
            amount: "100"
            unit: "/month/server"
          - id: "busi-95"
            aria_label: "busi-95-tab"
            currency: "€"
            amount: "225"
            unit: "/month/server"
          - id: "busi-99"
            aria_label: "busi-99-tab"
            currency: "€"
            amount: "350"
            unit: "/month/server"
        gray_box: "To qualify for guaranteed uptime, services must be configured for high availability, requiring a primary server and at least two failover servers."
        features:
          - text: "We generally try to acchieve the maximum possible uptime for <i>all</i> systems"
            icon_color: "text-emerald-600"
          - text: "Uptime guarantees often don't reflect the unpredictable nature of hosting"
            icon_color: "text-amber-600"
          - text: "We recommend considering them only if needed for your contractual obligations"
            icon_color: "text-amber-600"
          - text: "We can design systems fault resilient but not fault proof"
            icon_color: "text-amber-600"

  - block: cta
    links:
      - url: "linux-emergency-support.html"
        title: "Linux Emergency Support"
        text: "Linux Emergency Support"
      - url: "linux-consulting.html"
        title: "Project based Linux Consulting"
        text: "Linux Consulting for Projects"
      - url: "qubes-os-consulting-and-support.html"
        title: "Qubes OS Consulting and Support"
        text: "Qubes OS Consulting and Support"
      - url: "linux-personal-trainings-and-workshops.html"
        title: "Linux Trainings und Workshops"
        text: "Linux Trainings and Workshops"
---
