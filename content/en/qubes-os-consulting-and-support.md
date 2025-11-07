---
title: "Qubes OS Consulting and Support for High Risk Environments"
description: "We provide consulting services for integrating the high security operating system Qubes OS into your business: installation, configuration and ongoing support"
image: "/images/qubes-consulting/background.webp"

blocks:
  - block: hero-breadcrumb
    title: "Qubes OS Consulting and Support"
    subtitle: "Evaluation, Installation, Configuration and Maintenance for Business and Freelancers"
    background: "/images/qubes-consulting/background.webp"
    breadcrumb: "Qubes OS Consulting and Support"
    notice: "Our services are exclusively for businesses in the EU & USA and require a valid business tax ID. We do not work with consumers or end users."

  - block: text-image-bg
    id: introduction
    title: "Qubes OS Enterprise Support"
    text: |
      [Qubes OS](https://www.qubes-os.org/) is a security-focused operating system that isolates desktop applications by running them in separate virtual machines. This approach provides very strong protection against the spread of malware.

      At Blunix, we use Qubes OS to secure our own workstations to provide the best possible data protection for our customers.

      ### Countering Industrial Espionage

      Recent geopolitical events alongside increasingly strong artificial intelligence have changed the perspective on IT security for many companies. We assist in siemlessly transitioning to Qubes OS to securely run both Windows and Linux applications.
    image:
      src: "/images/qubes-consulting/qubes-penguin.webp"
      alt: "A cubical penguin"
    image_position: "right"
    image_bg: "blue"

  - block: features-grid
    id: features
    title: "Qubes OS Features"
    subtitle: "Qubes OS uses multiple approach vectors to securely isolate desktop applications from one another. If your browser is compromised, all other programs and their data remain secure."
    items:
      - icon: "uil uil-brackets-curly"
        title: "Isolation by Virtualization"
        details: "Qubes OS uses the [XEN hypervisor](https://wiki.xenproject.org/wiki/Xen_in_Qubes_OS_Security_Architecture) to securely isolate applications from each other"
      - icon: "uil uil-user"
        title: "End-User Friendly"
        details: "Non-technical users benefit from the easy to use graphical tools and interfaces"
      - icon: "uil uil-window-grid"
        title: "Windows and Linux VMs"
        details: "Securely run both your applications in both Windows 10 and Debian Linux 12 VMs"
      - icon: "uil uil-desktop"
        title: "Certified Hardware"
        details: "Qubes OS cooperates with [certified hardware vendors](https://www.qubes-os.org/doc/certified-hardware/) that use [Coreboot](https://www.coreboot.org/)"
      - icon: "uil uil-code-branch"
        title: "Network Segmentation"
        details: "Physical network adapters, firewall and VPN clients run in seperated VMs"
      - icon: "uil uil-copy"
        title: "Template-Based VMs"
        details: "VMs are created from snapshots of templates which are discarded at shutdown"
      - icon: "uil uil-arrow"
        title: "Infrastructure as Code"
        details: "Every aspect of the OS can be configured with scripts using [qvm- management tools](https://www.qubes-os.org/doc/tools/)"
      - icon: "uil uil-thumbs-up"
        title: "Granular Allow-Policies"
        details: "VM-to-VM communication, sharing files, and more are based on allow-lists"

  - block: text-image-bg
    id: partners
    title: "Double-Checked"
    text: |
      We partner with external IT security specialist and Qubes OS developers to ensure our work in high security projects is checked twice.

      ### Secure Business Laptops

      Our partner [NovaCustom from the Netherlands](https://novacustom.com/) builds Qubes OS certified enterprise laptops that are secure, reliable and offer a variety of additional hardware security features.

      ### Support for the Qubes OS Project

      We donate 10% of our earnings from Qubes OS-related contracts to [the Qubes OS project](https://www.qubes-os.org/donate/).
    image:
      src: "/images/qubes-consulting/qubes-partners.webp"
      alt: "A stack of cubical penguins"
    image_position: "left"
    image_bg: "green"

  - block: faq
    id: faq
    title: "Common Questions About Qubes OS Consulting"
    subtitle: "Global on-site and online installation support, migration assistance and individualized training. [Contact us](index.html#contact) to learn how Qubes OS can enhance the security of your company!"
    items:
      -
        q: "What is the price for Qubes OS consulting?"
        a: "We charge **130,00 € per hour for Qubes OS consulting**. For larger projects, we estimate the required time to complete all tasks and give you a **binding quote** - if it takes longer, you **only pay what we estimated**, and if it takes less time, you **pay less**."
      -
        q: "Do you assist in migrating data and tools from the old workstations?"
        a: "Yes, we fully assist in copying all data and programs from your existing computers to the new Qubes OS setups. For this we copy your data into a pre-defined backup structure that is then simply restored like a regular backup."
      -
        q: "Do you provide Qubes OS training?"
        a: "Yes, we have a pre-prepared training course that teaches everything you need to know about Qubes OS. We adjust this curriculum depending on the requirements and applications used in your business. Please [contact us](index.html#contact) for more information."
      -
        q: "Do you provide on-site support?"
        a: "Yes, we offer global on-site installation support. However we design our installation software in a way that will most likely not make this neccessary. If you have one technically versed employee, we provide you with software and very easy to follow instructions for configuring Qubes OS fully automatically in an online meeting."
      -
        q: "What do you use to automate the configuration of Qubes OS?"
        a: "Qubes OS internally uses Saltstack to automate the configuration of the default VMs. This approach however is rather complicated to use and very complicated and confusing to teach to our customers. We need our customers to fully understand the setup of their workstations. Because of this, we use BASH scripts with [qvm- commands](https://www.qubes-os.org/doc/tools/)"
      -
        q: "Who can buy Qubes OS consulting?"
        a: "We work with businesses as well as individuals like human rights activists and lawyers, journalists and alike. **We offer a discount of 20% for social and human rights organizations**."

  - block: process-timeline
    id: how-it-works
    title: "Steps to Implement Qubes OS in Your Business"
    subtitle: "We guide you through all aspects of migrating to Qubes OS. We offer 24/7 support for both your non-technical employees as well as your developers to guarantee a stress-free transition and experience with using Qubes OS."
    steps:
      - icon: "uil uil-phone"
        heading: "Online Meeting"
        text: "We connect with your and your employees to understand your thread model and make a list of all applications that are used in your company."
        right: false
      - icon: "uil uil-desktop"
        heading: "Choosing Hardware"
        text: "We assist you in choosing from the list of high security optimized laptops and workstations that support running Qubes OS."
        right: true
      - icon: "uil uil-file-check"
        heading: "Written Proposal"
        text: "You receive a written proposal that outlines all your requirements and our approach for implementating them using Qubes OS."
        right: false
      - icon: "uil uil-keyboard"
        heading: "Implementation"
        text: "We use the infrastructure as code approach to define every aspect of your Qubes OS setup in BASH scripts that are easy to audit and understand."
        right: true
      - icon: "uil uil-graduation-cap"
        heading: "Training"
        text: "We show you in detail how your customized Qubes OS setup looks and works, answer all of your employees questions and train them how to use it."
        right: false
      - icon: "uil uil-lock-alt"
        heading: "Migration"
        text: "We setup your workstations on-site or assist your employees in setting up multiple workstations automatically and migrate each employees applications."
        right: true
      - icon: "uil uil-wrench"
        heading: "Support & Updates"
        text: "We provide 24/7/365 support with a maximum response time of one hour and proactively approach you about security issues and interesting new features."
        right: false

  - block: cta
    links:
      - url: "linux-emergency-support.html"
        title: "Linux Emergency Support"
        text: "Linux Emergency Support"
      - url: "linux-consulting.html"
        title: "Project based Linux Consulting"
        text: "Linux Consulting for Projects"
      - url: "linux-managed-hosting.html"
        title: "Linux Managed Hosting"
        text: "Linux Managed Hosting"
      - url: "linux-personal-trainings-and-workshops.html"
        title: "Linux Trainings und Workshops"
        text: "Linux Trainings and Workshops"
---
