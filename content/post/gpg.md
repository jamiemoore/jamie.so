---
date: "2018-09-24T20:35:15+10:00"
title: "github code signing"
subtitle: "automatically sign your git commits on your mac"
tags:  ["git", "github", "gpg" ]
typora-root-url: ../../static
---
Signing your code is an important part of verifying your identity to an SCM platform.  Particularly with a system like Github Enterprise where spoofing your colleagues email address can make your commits appear to come from them.  Enforcing signing on the repository would enable you to determine if this has taken place and allow you to verify this import part of the software supply chain.
<!--more-->

## Installation

As always, install using Homebrew. Install GPG with the `gnupg` formula. We also install the package `pinentry-mac` which allows us to enter our GPG passphrase in a GUI.  It can then be stored in the keychain, which means we don't have to type the password all the time if we have already logged in.

1. Install GPG

   ```bash
   brew install gnupg pinentry-mac
   ```

1. Configure pinentry-mac

   ```bash
   echo "pinentry-program $(brew --prefix)/bin/pinentry-mac" >> ~/.gnupg/gpg-agent.conf
   ```

1. Reload the gpg-agent

   ```bash
   gpg-connect-agent reloadagent /bye
   ```
   
1. Test GPG

   ```bash
   echo "test" | gpg --clearsig
   ```

1. When the `pinentry-mac` screen appears tick the keep in keychain box.

   ![pinentry-mac-login](/img/pinentry-mac-login.png)


## GPG Usage 

* Generate a GPG key by running the below command and following the prompts.

  ```bash
  gpg --full-generate-key 
  ```

* List keys

  ```bash
  gpg --list-keys
  /Users/jamie/.gnupg/pubring.kbx
  ---------------------------------
  pub   rsa2048 2018-09-24 [SC] [expires: 2020-09-23]
        2A0F854026C471F2A4E61353692C6E2E9112D882
  uid           [ultimate] Jamie Moore <jbgmoore@gmail.com>
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

* You can now paste it into your github user settings.

## Signing Commits

* Always sign commits for all repos

  ```bash
  git config --global user.signingkey <PUBLIC-KEY-ID>
  git config --global commit.gpgSign true 
  ```
  
* Sign commits just for that repository

  ```bash
  git config user.signingkey <PUBLIC-KEY-ID>
  git config commit.gpgSign true 
  ```
  
* Sign a specific commit

  ```bash
  git sign 07FF520A80246D1897D1ACA26D4AAC0705A1D321
  ```

* Create and sign an annotated tag

  ```bash
  git tag -sm 'My Signed Tag' v1.1.2
  ```

  

Happy Signing!