---
date: "2019-08-27T21:52:42+10:00"
title: "aws-vault"
subtitle: "local aws identity management"
tags:  ["aws", "cli", "security", "aws-vault" ]
typora-root-url: ../../static
---
Need to use the awscli and don't have time to implement federation?  Can't connect to existing identity systems due to the skunkworks nature of your project? Have many seperate clients and aws accounts to deal with? [aws-vault](https://github.com/99designs/aws-vault) is here to save you.
<!--more-->

aws-vault allows you to  add your AWS access key and secret to your computers keyring and then generates an ephemeral key for you to use for a certain time period (1h).  If that key is accidentally revealed during development work, for example discovered in a log in a few days time, no harm done.  Compared to traditionally where the access key and secret are long lived and exist as environment variables. 

## Installation

1. Install aws-vault

   ```bash
   brew cask install aws-vault
   ```

2. Go into the AWS console and generate yourself a new [access and secret key](https://docs.aws.amazon.com/general/latest/gr/aws-sec-cred-types.html#access-keys-and-secret-access-keys).

3. Create a new aws  `~/.aws/config` if you don't have one already.

4. Add your account structure into that file.  Normal rules for configuring the  [aws config file](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-files.html) apply.  For example to configure an account where use have user credentials requiring mfa you would use

   ```bash
   [default]
   region=ap-southeast-2
    
   [profile root-account]
   mfa_serial = arn:aws:iam::12345:mfa/YOURACCOUTNAMEGOESHERE
   ```

5. **Don't** place credentials into `~/.aws/credentials` as you may currently be doing.  Instead add the credentials into aws-vault, using the example from above.

   ```bash
   aws-vault add root-account 
   ```

6. Optionally, configure your environment with additional variables to control how aws-vault is configured, a full list can be found [here](https://github.com/99designs/aws-vault/blob/master/USAGE.md#environment-variables) Notice that we usually using the aws cli so we are adding that autocompletion too.

   ```bash
   complete -C aws_completer aws-vault
   export AWS_VAULT_KEYCHAIN_NAME=login
   export AWS_VAULT_PROMPT=osascript
   export AWS_ASSUME_ROLE_TTL=1h
   export AWS_SESSION_TTL=1h
   export AWS_ASSUME_ROLE_TTL=1h
   export AWS_FEDERATION_TOKEN_TTL=1h
   ```

7. You should now be able to confirm that aws is working by checking your aws identity

   ```bash
   aws-vault exec root-account -- aws sts get-caller-identity
   ```

## Usage

In these examples we have configured our  `~/.aws/config ` file with contents:

```bash
[default]
region=ap-southeast-2
 
[profile root-account]
mfa_serial = arn:aws:iam::12345:mfa/YOURACCOUTNAMEGOESHERE
 
[profile child-account-a]
source_profile = root-account
role_arn = arn:aws:iam::23456:role/OrganizationAccountAccessRole

[profile child-account-b]
source_profile = root-account
role_arn = arn:aws:iam::34567:role/OrganizationAccountAccessRole
```

This means we have created a user account in the aws root account with mfa and we assume roles from that account to access child accounts in our organisation.

* Run an aws command in the root account

  ```bahs
  aws-vault exec root-account -- aws sts get-caller-identity
  ```

* Run an aws command in a child account (you will need credentials for the root account)

  ```bash
  aws-vault exec child-account-a -- aws sts get-caller-identity
  ```

* Combine with [chamber](https://github.com/segmentio/chamber) and run terraform

  ```bash
  aws-vault exec root-account -- chamber exec prod -- terraform plan
  ```

* List your different credentials

  ```bash
  aws-vault list
  ```

* Clear an existing session.  Not the sessions-only flag is important or you will remove the configuration.

  ```bash
  aws-vault remove --sessions-only root-account 
  ```

  



