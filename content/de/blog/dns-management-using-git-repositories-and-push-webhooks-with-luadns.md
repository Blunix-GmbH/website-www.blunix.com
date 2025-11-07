---
title: "DNS Record Management Made Easy: Using Git Repositories with luadns.com, Our Favorite DNS Provider"
description: "This blog post describes how to manage large numbers of DNS records using git repositories by hosting domains with the DNS service provider luadns.com"
date: 2024-04-24
image: "/images/blog/luadns-logo.webp"
image_alt: "luadns.com Logo"
---

Please [contact us](/index.html#contact "Blunix GmbH contact options") if anything is not clearly described, does not work, seems incorrect or if you require support.

## Table of contents

- [Different Methods of DNS Management](#dns-management-methods)
  - [The Tedious Method: Using the DNS Provider WebUI](#dns-management-methods-webui)
  - [The Improved Approach: Leveraging the API](#dns-management-methods-api)
  - [The Optimal Solution: Git Repositories](#dns-management-methods-git)
- [Getting Started with LuaDNS](#luadns-getting-started)
  - [Changing Your NS Servers](#luadns-getting-started-changing-ns-servers)
  - [Setting Up a Git Repository](#luadns-getting-started-creating-the-git-repository)
  - [Configuring Git Hooks for Automated Updates](#luadns-getting-started-configuring-gitlab-or-github-webhooks)

## [Different Methods of DNS Management](#dns-management-methods)

"Can you setup a new subdomain for xyz.example.com?" - these or similar request are a common inquery for sysadmins. Here are three technical approaches on how to handle such requests.

### [The Tedious Method: Using the DNS Provider WebUI](#dns-management-methods-webui)

While utilizing web interfaces for DNS management is sufficient for smaller scales, those of us inclined toward automation, particularly when handling a plethora of domains, prefer the "infrastructure as code" approach.

**Hetzner**: Clicks to create a new domain: 1

![Hetzner DNS Management WebUI](/images/blog/dns-management-using-git-repositories-and-push-webhooks-with-luadns/webui-hetzner.webp)

**LuaDNS** (not german): Clicks to create a new domain: 2

![LUADNS DNS Management WebUI](/images/blog/dns-management-using-git-repositories-and-push-webhooks-with-luadns/webui-luadns.webp)

**IONOS**: Clicks to create a new domain: 3

![IONOS DNS Management WebUI](/images/blog/dns-management-using-git-repositories-and-push-webhooks-with-luadns/webui-ionos.webp)

**HostEurope**: Clicks to domain: 6, if you can find the DNS management WebUI in the clutter of their admin UI

![HostEurope DNS Management WebUI](/images/blog/dns-management-using-git-repositories-and-push-webhooks-with-luadns/webui-hosteurope.webp)

### [The Improved Approach: Leveraging the API](#dns-management-methods-api)

Employing an API offers a more efficient way to manage DNS repositories, especially when contending with thousands of records while working with a single DNS hosting provider

For an Linux managed hosting service provider like Blunix, where each customer has their very own IaaS provider accounts (blunix does not own ANY customer servers by design), this is less efficient, as pretty much all of our customers use different DNS providers. We have been on a crucade for the last years to migrate them all to luadns.com.

### [The Optimal Solution: Git Repositories](#dns-management-methods-git)

The cleanest method of DNS management involves keeping all domains in a git repository with one .lua file for each domain.

## [Getting Started with LuaDNS](#luadns-getting-started)

Signing up for a LUADNS account is free for up to five DNS zones – all you need is an email address. No credit card required.

### [Changing Your NS Servers](#luadns-getting-started-changing-ns-servers)

LUADNS solely provides managed nameserver and does not resell domains. To utilize LUADNS name servers, update your NS records to:

- ns1.luadns.net

- ns2.luadns.net

- ns3.luadns.net

- ns4.luadns.net

Here is how this looks for Hetzner:

![Hetzner Setup LUADNS NS Records](/images/blog/dns-management-using-git-repositories-and-push-webhooks-with-luadns/setup-ns-records-hetzner.webp)

Or for IONOS:

![IONOS Setup LUADNS NS Records](/images/blog/dns-management-using-git-repositories-and-push-webhooks-with-luadns/setup-ns-records-ionos.webp)

### [Setting Up a Git Repository](#luadns-getting-started-creating-the-git-repository)

Simply create a git repository in your GitHub or GitLab account and refer to [this link](https://github.com/luadns/dns/blob/master/example.net.lua "github.com/luadns: examples configuration files") for examples. Manage as many domains as you host with LUADNS within a single git repository.

Here is a screenshot of the very simple configuration for a webhook that is triggered by a git push to the "main" or "master" branch with Gitlab:

![Gitlab Webhook configuration](/images/blog/dns-management-using-git-repositories-and-push-webhooks-with-luadns/webhook-gitlab.webp)

### [Configuring Git Hooks for Automated Updates](#luadns-getting-started-configuring-gitlab-or-github-webhooks)

Configure git hooks to streamline domain updates with a simple push. Create a remote repository in your GitLab or GitHub account, such as "luadns," and set up a webhook to trigger upon pushing the repository's "main" or "master" branch.

For more detailed instructions, consult the LUADNS documentation on [git integration](https://www.luadns.com/help.html#git-integration "www.luadns.com: git integration documentation").

Admin Anecdote: Once, a customer inadvertently misconfigured domains in their git repository and pushed them, which was rejected by LUADNS' API with an error. This prompted the admins of luadns.com to proactively reach out via email to rectify the mistake. Try to find a service provider with this level of engagement.
