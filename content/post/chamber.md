---
date: "2019-08-27T23:39:46+10:00"
title: "chamber"
subtitle: "secrets management using kms and aws parameter store"
tags:  ["aws", "security", "cli", "kms", "parameter store", "terraform", "aws-vault", "chamber" ]
typora-root-url: ../../static
---
Secrets management ... the bane of automation in enterprises!  If you are lucky enough to use aws parameter store and kms (every SecArch loves kms, so that part should be straight forward).  Then you can manage your secrets using [chamber](https://github.com/segmentio/chamber).  A tool that allows you to read a secret from parameter store and place them in your environment.  It pairs fantastically well with aws-vault for identity management.  Excellent to use as glue between an encrypted secret in parameter store and terraform.
<!--more-->

## Installation

1. Install from the pre built binary on github as `chamber`

   ```bash
   cd ~/bin
   wget https://github.com/segmentio/chamber/releases/download/{version} -O chamber
   chmod +x chamber
   ```

2. Configure which kms key you will use to encrypt passwords by default

   ```bash
   echo 'export CHAMBER_KMS_KEY_ALIAS="alias/my-project-key"' >> ~/.bashrc
   ```



## Usage

* Write a secret

  ```bash
  aws-vault exec my-aws-account -- chamber write myprojectname TF_VAR_DB_PASSWORD $(date | md5)
  ```

* Read a secret

  ```bash
  aws-vault exec my-aws-account -- chamber read -q myprojectname  TF_VAR_DB_PASSWORD
  ```

* Run terraform passing in a secret variable.  Note that secrets are always exported in uppercase

  ```bash
  aws-vault exec my-aws-account -- chamber exec myprojectname -- terraform plan
  ```

  