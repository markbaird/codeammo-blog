---
title: "Install PhantomJS on Amazon Linux"
date: 2015-05-16T16:54:43-04:00
draft: false
disqusIdentifier: 5557e2e5c17cc4b50fec95ab
description: "Install PhantomJS on Amazon Linux"
keywords:
- PhantomJS
- Amazon Linux
- AWS
tags:
- AWS
- PhantomJS
---

PhantomJS isn't available from the standard Amazon Linux yum repository. Follow these steps to download a binary distribution of PhantomJS and install it on a server running Amazon Linux.
<!--more-->

{{< codeblock lang="bash" >}}
sudo yum install fontconfig freetype freetype-devel fontconfig-devel libstdc++
wget https://bitbucket.org/ariya/phantomjs/downloads/phantomjs-1.9.8-linux-x86_64.tar.bz2
sudo mkdir -p /opt/phantomjs
bzip2 -d phantomjs-1.9.8-linux-x86_64.tar.bz2
sudo tar -xvf phantomjs-1.9.8-linux-x86_64.tar \
    --directory /opt/phantomjs/ --strip-components 1
sudo ln -s /opt/phantomjs/bin/phantomjs /usr/bin/phantomjs
{{< /codeblock >}}

Once complete, run the following command to verify the installation:

{{< codeblock lang="bash" >}}
phantomjs /opt/phantomjs/examples/hello.js
{{< /codeblock >}}