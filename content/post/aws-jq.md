---
date: "2019-08-26T23:10:17+10:00"
title: "aws-cli and jq"
subtitle: ""
tags:  ["aws", "jq", "cli" ]
typora-root-url: ../../static
---
Ah the classic partnerships, hot chocolate and marshmallows , hot dogs and sauce, hot aws cli and jq.

## Installation

The aws cli can be installed like so:

```bash
pip install awscli
```

and jq, thusly:

```code
brew install jq
```



## Top Tips

Before you go crazy with jq alone... remember your friend `--filters` on the aws cli ... because there is nothing like hitting the api limits by running a query over all your instances.  Not that I don't do that sometimes  when nobody is looking. :eyes:



## Examples

* Give me the value of the name tag on all instance with the tag "Application that has the value, Kubernetes"

  ```bash
  aws ec2 describe-instances --filters "Name=tag:Application,Values=Kops" | jq -r '.Reservations[].Instances[] | (.Tags[]|select(.Key=="Name")|.Value)'
  ```

* Look at the IAM role of all instances with a specific tag which could have two possible values and output the PrivateDNSName and IAMInstanceProfile in tsv format.

  ```bash
  aws ec2 describe-instances --filters "Name=tag:Application,Values=[Kops,EKS]" | jq -r '.Reservations[].Instances[] | [.PrivateDnsName, .IamInstanceProfile.Arn] | @tsv'
  ```
  



## Advanced Mode

* DNS Records with a header in a table, ignoring the first three results

  ```bash
  aws route53 list-resource-record-sets --hosted-zone-id MYZONEIDGOESHERE |
  jq -r '["NAME","TYPE","TTL","VALUE"], ["----","----","---","-----"],
  (.ResourceRecordSets[] | [.Name, .Type, .TTL,
  .ResourceRecords[0].Value]) | @tsv' | column -t -s $'\t | head -n 3'
  ```

  