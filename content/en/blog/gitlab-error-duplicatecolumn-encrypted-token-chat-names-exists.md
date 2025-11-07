---
title: 'Howto fix the Gitlab upgrade Error PG::DuplicateColumn: ERROR: column "encrypted_token" of relation "chat_names" already exists'
description: 'Howto fix the Gitlab upgrade Error PG::DuplicateColumn: ERROR:  column "encrypted_token" of relation "chat_names" already exists'
date: 2024-01-22
image: "/images/blog/gitlab-logo.webp"
image_alt: 'Gitlab Upgrade Error PG::DuplicateColumn: ERROR: column "encrypted_token" of relation "chat_names" already exists'
---

Please [contact us](/index.html#contact "Blunix GmbH contact options") if anything is not clearly described, does not work, seems incorrect or if you require support.

While upgrading from the apt installed omnibus gitlab Version 16.6.4-ce.0 on Debian 12, I encountered the following error message:

```plaintext
PG::DuplicateColumn: ERROR:  column "encrypted_token" of relation "chat_names" already exists
```

Note that even though the apt upgrade failed, apt is displaying the target version to be installed already:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-user="root" data-host="git.blunix.com">
<code class="language-bash">apt-cache policy gitlab-ce|head
gitlab-ce:
  Installed: 16.7.2-ce.0
  Candidate: 16.7.2-ce.0
  Version table:
 *** 16.7.2-ce.0 500
        500 https://packages.gitlab.com/gitlab/gitlab-ce/debian buster/main amd64 Packages
        100 /var/lib/dpkg/status
     16.7.0-ce.0 500
        500 https://packages.gitlab.com/gitlab/gitlab-ce/debian buster/main amd64 Packages
[...]</code></pre>

The following postgresql will adjust the column name as required:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-user="root" data-host="git.blunix.com">
<code class="language-bash">gitlab-psql

gitlabhq_production=# ALTER TABLE chat_names RENAME COLUMN encrypted_token_iv TO encrypted_token_iv_old;
ALTER TABLE

gitlabhq_production=# ALTER TABLE chat_names RENAME COLUMN encrypted_token TO encrypted_token_old;
ALTER TABLE

gitlabhq_production=# exit</code></pre>

To continue the upgrade, use the following command:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-user="root" data-host="git.blunix.com">
<code class="language-bash">apt -f install gitlab-ce</code></pre>
