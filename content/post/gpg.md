---
date: "2019-08-26T20:35:15+10:00"
title: "github code signing"
subtitle: "Configure your mac to sign your git commits"
tags:  ["git", "github", "gpg" ]
---
Signing your code is an important part of verifying your identity.  Particularly with a system like github enterprise where changing your email address can result make your commits appear to come from another user.  Enforcing signing on the repository would enable you to determine if this has taken place and allow you to verify this import part of the software supply chain.
<!--more-->

## Installation

Install as always using homebrew. Here we install the package `pinentry-mac` which allows us to store our GPG passphrase in the login keychain where the GPG agent can access it.  Which means we don't have to type the password all the time if we have already logged in.


1. Install GPG

   ```bash
   brew install pinentry-mac
   ```

2. Configure pinentry-mac

   ```bash
   echo "pinentry-program $(brew --prefix)/bin/pinentry-mac" >> ~/.gnupg/gpg-agent.conf
   ```

3. Reload the gpg-agent

   ```bash
   gpg-connect-agent reloadagent /bye
   ```




## GPG Usage 

* Generate a GPG key

  ```bash
  gpg --full-generate-key # RSA4096 
  ```

* List keys

  ```bash
  gpg --list-keys
  /Users/jamie/.gnupg/pubring.kbx
  ---------------------------------
  pub   rsa2048 2018-09-24 [SC] [expires: 2020-09-23]
        2A0F854026C471F2A4E61353692C6E2E9112D882
  uid           [ultimate] Jamie Moore <jamie.moore@jamie.so>
  sub   rsa2048 2018-09-24 [E] [expires: 2020-09-23]
  ```

* Deleting Keys

  ```bash
  gpg  --delete-keys 2A0F854026C471F2A4E61353692C6E2E9112D882
  gpg  --delete-secret-keys 2A0F854026C471F2A4E61353692C6E2E9112D882
  ```

* Export GPG key to clipboard (So you can copy them to GitHub)

  ```bash
  gpg --armor --export 2A0F854026C471F2A4E61353692C6E2E9112D882 | pbcopy
  ```



## Signing Commits

* Always sign commits for all repos

  ```bash
  git config --global commit.gpgSign true 
  ```
  
* Sign commits just for that repository

  ```bash
  git config commit.gpgSign true 
  ```
  
* Sign a specific commit

  ```bash
  git sign 07FF520A80246D1897D1ACA26D4AAC0705A1D321
  ```
