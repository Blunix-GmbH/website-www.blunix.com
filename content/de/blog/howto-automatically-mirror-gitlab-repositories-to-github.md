---
title: "Automatically mirror Gitlab repositories to Github"
description: "This blogpost describes how to periodically mirror specific Gitlab repositories to your Github account or organization using the glab and gh cli tools"
date: 2024-02-16
image: "/images/blog/mirror-gitlab-to-github.webp"
image_alt: "Automatically mirror Gitlab repositories to Github"
---

Please [contact us](/index.html#contact "Blunix GmbH contact options") if anything is not clearly described, does not work, seems incorrect or if you require support.

## Table of contents

- [The Problem with mirroring from Gitlab to Github](#gitlab-mirror-to-github-error)
- [Installing the Gitlab CLI glab](#installing-glab-cli)
- [Installing the Github CLI gh](#installing-gh-cli)
- [Setting up the Gitlab to Github mirror script](#mirror-script)

## [The Problem with mirroring from Gitlab to Github](#gitlab-mirror-to-github-error)

We want to mirror some of our public Gitlab repositories to github for better visibility. There is a gitlab function available for that at Settings -> Repository -> Repository mirroring, which is [documented here](https://gitlab.com/gitlab-org/cli/-/blob/main/docs/source/repo/mirror.md), but sadly it gives this error when used with github:

13: push to mirror: git push: exit status 128, stderr: "remote Support for password authentication was removed on August 13, 2021.\nremote: Please see https://docs.github.com/en/getting-started/getting-started-with-git/about-remote-repositories#cloning-with-https-urls for information on currently recommended modes of authentication.\nfatal: Authentication failed for: 'https://github.com/Blunix-GmbH/role-haproxy/'\n".

![Gitlab Mirror Repository to Github Error](/images/blog/howto-automatically-mirror-gitlab-repositories-to-github/gitlab-mirror-to-github-error.webp)

## [Installing the Gitlab CLI glab](#installing-glab-cli)

So lets just script it. This will not be triggered automatically, but you can setup a cronjob on your gitlab server that does it once a day.

Gitlab has a [CLI tool for administration](https://gitlab.com/gitlab-org/cli). A .deb package can be downloaded for Ubuntu and Debian Linux based workstations [at the gitlab.com download page](https://gitlab.com/gitlab-org/cli/-/releases). For most Ubuntu and Debian based servers, choose "glab\_<version>\_Linux_x86_64.deb".

<pre class="command-line" data-continuation-str="\" data-host="workstation" data-output="" data-user="user"><code class="language-bash">
wget https://gitlab.com/gitlab-org/cli/-/releases/v1.36.0/downloads/glab_1.36.0_Linux_x86_64.deb
sudo dpkg -i glab_1.36.0_Linux_x86_64.deb
</code></pre>

After installation you have to authenticate against your gitlab instance. This configuration will be saved below "~/.config/glab-cli/". In order to authenticate you will need a token, which can be generated here:

https://your-gitlab-domain.com/-/user_settings/personal_access_tokens

You only need to select the "api" and "read_repository" permission:

![Create a Gitlab API token](/images/blog/howto-automatically-mirror-gitlab-repositories-to-github/gitlab-create-api-token.webp)

<pre class="command-line" data-continuation-str="\" data-host="workstation" data-output="2-99" data-user="user"><code class="language-bash">
glab auth login
? What GitLab instance do you want to log into? GitLab Self-hosted Instance
? GitLab hostname: git.blunix.com
? API hostname: git.blunix.com
- Logging into git.blunix.com
? How would you like to login? Token

Tip: you can generate a Personal Access Token here https://git.blunix.com/-/profile/personal_access_tokens?scopes=api,write_repository
The minimum required scopes are 'api' and 'write_repository'.
? Paste your authentication token: **************************
? Choose default git protocol SSH
? Choose host API protocol HTTPS
- glab config set -h git.blunix.com git_protocol ssh
✓ Configured git protocol
- glab config set -h git.blunix.com api_protocol https
✓ Configured API protocol
</code></pre>

If we check it now it wont work:

<pre class="command-line" data-continuation-str="\" data-host="workstation" data-output="2-99" data-user="user"><code class="language-bash">
glab repo list
Showing 0 of 0 projects (Page 1 of 1)
</code></pre>

For whatever reason the configuration created by "glab auth login", which is located at "~/.config/glab-cli/config.yml", is setup to only talk to gitlab.com by default, not your personal gitlab instance. Lets fix that by replacing all occurances of gitlab.com with your Gitlabs hostname:

<pre><code>
# Change this to your gitlab instance
host: git.blunix.com
[...]

hosts:
    # Change this too
    git.blunix.com:
        [...]
        # And this as well
        api_host: git.blunix.com
        [...]
</code></pre>

Or in one line:

<pre class="command-line" data-continuation-str="\" data-host="workstation" data-output="" data-user="user"><code class="language-bash">
sed -i 's/gitlab.com/git.blunix.com/g' ~/.config/glab-cli/config.yml
</code></pre>

Now the repositories should be visible:

<pre class="command-line" data-continuation-str="\" data-host="workstation" data-output="2-99" data-user="user"><code class="language-bash">
glab repo list
Showing 30 of 372 projects (Page 1 of 12)

ansible-roles/role-imgproxy                git@git.blunix.com:ansible-roles/role-imgproxy.git                Ansible role to install and configure imgproxy on Debian Linux                                                   
ansible-roles/role-golang                  git@git.blunix.com:ansible-roles/role-golang.git                  Ansible role to install and configure golang on Debian Linux    
ansible-roles/role-systemd-journal-remote  git@git.blunix.com:ansible-roles/role-systemd-journal-remote.git  Ansible role to install and configure systemd-journal-remote on Debian Linux                                     
[...]
</code></pre>

## [Installing the Github CLI gh](#installing-gh-cli)

Next we need a cli client for github to automatically create repositores there. There is a github cli called "gh", which can simply be installed by apt:

<pre class="command-line" data-continuation-str="\" data-host="workstation" data-output="" data-user="user"><code class="language-bash">
sudo apt install gh
</code></pre>

The configuration is very similar to the glab cli tool (but works right away). In our case, gitlab is not able to start a browser from the cli, so we copy paste the auth code to the URL displayed:

<pre class="command-line" data-continuation-str="\" data-host="workstation" data-output="2-99" data-user="user"><code class="language-bash">
gh auth login
? What account do you want to log into? GitHub.com
? What is your preferred protocol for Git operations? HTTPS
? Authenticate Git with your GitHub credentials? Yes
? How would you like to authenticate GitHub CLI? Login with a web browser

! First copy your one-time code: D946-B977
Press Enter to open github.com in your browser... 
2024/02/15 23:44:52.178314 cmd_run.go:1055: WARNING: cannot start document portal: Expected portal at "/run/user/1000/doc", got "/home/user/.cache/doc"
/user.slice/user-1000.slice/session-2.scope is not a snap cgroup
! Failed opening a web browser at https://github.com/login/device
  exit status 4
  Please try entering the URL in your browser manually

✓ Authentication complete.
- gh config set -h github.com git_protocol https
✓ Configured git protocol
✓ Logged in as blunix0815
</code></pre>

Lets check if the setup was successful:

<pre class="command-line" data-continuation-str="\" data-host="workstation" data-output="2-4,6-99" data-user="user"><code class="language-bash">
gh repo list

There are no repositories in @blunix0815

gh repo list Blunix-GmbH

Showing 30 of 47 repositories in @Blunix-GmbH

Blunix-GmbH/role-borgbackup-server            Ansible role to install and configure borgbackup server on Debian Linux 
[...]
</code></pre>

## [Setting up the Gitlab to Github mirror script](#mirror-script)

The following script uses the "glab" and "gh" cli tools to iterate over all repositories on the Blunix gitlab instance, create the same project on github, clone the project from gitlab and push the master or main branch to github.

<pre><code class="language-bash">
#!/bin/bash
#
# Replicate all repositories from https://git.blunix.com/ansible-roles/ to https://github.com/Blunix-GmbH/

# List all repos below https://git.blunix.com/ansible-roles/
# Example output of glab repo list:
# ansible-roles/role-ssh                          git@git.blunix.com:ansible-roles/role-ssh.git                          Ansible role for managing OpenSSH-Server and users authorized_keys
glab repo list --all --group ansible-roles --per-page 1000 | while read line; do

    # Skip "Showing of 51 projects (Page 1 of 1)"
    [[ "$line" == Showing* ]] &amp;&amp; continue
    # Skip empty lines
    [[ "$line" == "" ]] &amp;&amp; continue

    # Extract repo name
    repo_name=$(echo $line | cut -d ' ' -f 1 | cut -d '/' -f 2)

    # Extract repo url
    repo_url=$(echo $line | cut -d ' ' -f 2)

    # Extract description
    repo_description=$(echo $line | cut -d ' ' -f 3-)

    # Give the user some information about current repo
    echo "Processing repo \"$repo_name\" with url \"$repo_url\""

    # Abort on empty description
    if [[ "$repo_description" == "" ]]; then
        echo "Description empty, aborting!"
        exit 1
    fi

    # Create repo on github
    gh repo create --disable-issues --disable-wiki --homepage https://www.blunix.com --public --description "$repo_description" Blunix-GmbH/$repo_name

    # Clone repo from gitlab
    git clone $repo_url

    # Push to github
    cd $repo_name
    git remote add github git@github.com:Blunix-GmbH/$repo_name
    git push github master
    cd ..
    rm -rf $repo_name

done
</code></pre>

Make it executable like so:

<pre class="command-line" data-continuation-str="\" data-host="git.blunix.com" data-output="" data-user="root"><code class="language-bash">
chmod 700 /usr/local/sbin/sync-repos-to-github.sh
</code></pre>

The following command will setup a cronjob for the script that will be executed daily at 5am:

<pre class="command-line" data-continuation-str="\" data-host="git.blunix.com" data-output="" data-user="root"><code class="language-bash">
(crontab -l 2&gt;/dev/null; echo "0 5 * * * /usr/local/sbin/sync-repos-to-github.sh") | crontab 
</code></pre>
