---
title: "Linux Fixes"
description: "Fixes for common errors."
lead: "Fixes for common errors."
date: 2023-10-24
lastmod: 2023-10-24
draft: false
images: []
menu:
  docs:
    parent: "linux"
weight: 130
toc: true
---

# DNF Errors

## DNF Update: invalid certificate

If **dnf update** gives you an error like this one:

```sh
RPM:   1. Certificiate CC5F15147A36124D invalid: certificate is not alive
RPM:       because: The primary key is not live
RPM:       because: Expired on 2022-05-15T11:33:53Z
RPM:   2. Key CC5F15147A36124D invalid: key is not alive
RPM:       because: The primary key is not live
RPM:       because: Expired on 2022-05-15T11:33:53Z
RPM: error: Verifying a signature using certificate E9B542ED839913B776AF2AA8CC5F15147A36124D (stable OBS Project <stable@obs-api-v2.codicefactory.
com>).
```

1. Find key for the repo that is not working (stable OBS Project in this case):

```sh
# Command
rpm -q gpg-pubkey --queryformat “%{NAME}-%{VERSION}-%{SUMMARY}\n”

# Example result
“gpg-pubk... public keyn”“gpg-pubkey-7a36124d-stable OBS Project <stable@obs-api-v2.codicefactory.com>”% ....
                         |--THIS-IS-THE-KEY--|
```

2. Delete the key and the repo:

```sh
# Replace with your repository key
sudo rpm -e gpg-pubkey-7a36124d

# Delete the repo (go to /etc/yum.repos.d/)
sudo rm /etc/yum.repos.d/plasticscm-stable.repo
```

3. Clean dnf cache:

```sh
sudo dnf clean all
```

4. Reinstall the repo and update as usual.
