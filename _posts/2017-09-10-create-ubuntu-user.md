---
layout: post
current: post
cover:  assets/images/ubuntu.jpg
navigation: True
title: How to create a new Ubuntu user via bash
date: 2017-09-10 10:00:00
tags: [snippets, linux]
class: post-template
subclass: 'post'
author: lucas
---

Follow the steps below to create a new user with sudo privileges on Ubuntu:

```bash
# Add an user lucas and its home folder at /home/lucas
sudo useradd -d /home/lucas -m lucas
# Add a password for user lucas
sudo passwd lucas
# Make bash as default terminal
sudo usermod -s /bin/bash lucas
# Add lucas to sudoers group
sudo adduser lucas sudo
```