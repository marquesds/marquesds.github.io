---
layout: post
current: post
cover:  assets/images/terminal.jpeg
navigation: True
comments: true
title: Flush Memcached
date: 2017-09-14 10:00:00
tags: [linux]
class: post-template
subclass: 'post'
author: lucas
---

To flush your local memcached follow the steps below:

```bash
telnet 127.0.0.1 11211
flush_all
quit
```