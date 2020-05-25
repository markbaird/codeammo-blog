---
title: "Using AWS Secrets Manager with Concourse CI"
date: 2020-05-25T00:49:21-04:00
description: "Using the AWS Secrets Manager credential manager in Concourse CI"
draft: false
tags:
- AWS
- Concourse CI
- Secrets Manager
keywords:
- AWS
- Secrets
- Concourse
---
Using [AWS Secrets Manager](https://aws.amazon.com/secrets-manager/) as a credential manager for 
[Concourse CI](https://concourse-ci.org/) works well, 
however [the official documentation](https://concourse-ci.org/aws-asm-credential-manager.html) currently leaves 
out several critical details needed to utilize it properly. These are my notes to fill in the gaps in the documentation.
<!--more-->

----------

## Using IAM roles:

The documentation doesn't make it entirely clear, but if you are following best practices by using
EC2/ECS IAM roles to provide Concourse access to Secrets Manager, all you have to do is set the 
`CONCOURSE_AWS_SECRETSMANAGER_REGION`, or the `aws-secretsmanager-region` command line option,
on your Concourse web node(s) to enable the AWS Secrets Manager credentials manager.

----------

## Storing and using secrets:

The Secrets Manager credentials manager supports secrets that contain a single value, as well as secrets that contain
multiple key/value pairs stored in JSON format. The credential manager treats all secrets of type `SecretString` as a single
string value, and all secrets of type `SecretBinary` as a set of JSON formatted key/value pairs.

The following are some examples of storing and referencing these different secret types:

----------
#### Example 1: 

Add a new string secret in Secrets Manager and reference it in a pipeline named **my-pipeline**, 
in the Concourse team **my-team**:

{{< codeblock "create-secret.sh" "bash" >}}
aws secretsmanager create-secret \
    --name /concourse/my-team/my-pipeline/my_secret_key \
    --secret-string "my_secret_value"
{{< /codeblock >}}

{{< codeblock "pipeline.yml" "yaml" >}}
task: Deploy
params:
  SOME_SECRET: ((my_secret_value))
{{< /codeblock >}}

----------
#### Example 2: 

Add a new JSON secret in Secrets Manager and reference it in a pipeline named **my-pipeline**, 
in the Concourse team **my-team**:

{{< codeblock "secrets.json" "json" >}}
{
  "aws_access_key_id": "#####################",
  "aws_secret_key": "#####################"
}
{{< /codeblock >}}

{{< codeblock "create-secret.sh" "bash" >}}
aws secretsmanager create-secret \
  --name /concourse/my-team/my-pipeline/my_secrets \
  --secret-binary file://secrets.json
{{< /codeblock >}}

{{< codeblock "pipeline.yml" "yaml" >}}
task: Deploy
params:
  AWS_ACCESS_KEY_ID: ((my_secrets.aws_access_key_id))
  AWS_SECRET_ACCESS_KEY: ((my_secrets.aws_secret_key))
{{< /codeblock >}}

----------
#### Example 3:

Add a multi-line string value secret in Secrets Manager, that is shared by all pipelines in the team **my-team**:

{{< codeblock "create-secret.sh" "bash" >}}
aws secretsmanager create-secret 
    --name /concourse/my-team/git_ssh_key \
    --secret-string file://git-ssh.pem \
{{< /codeblock >}}

{{< codeblock "pipeline.yml" "yaml" >}}
name: version
type: semver
source:
  driver: git
  branch: non-notified_version
  file: non-notified_version
  private_key: ((git_ssh_key))
{{< /codeblock >}}

