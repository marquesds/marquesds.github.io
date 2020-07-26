---
layout: post
current: post
cover:  assets/images/mice-bypass.jpg
navigation: True
comments: true
title: Bypass SSL - Python Zeep and Suds
date: 2017-09-10 10:00:00
tags: [snippets, python]
class: post-template
subclass: 'post'
author: lucas
---

When you try to consume a webservice with an invalid certificate, both [python-zeep](http://docs.python-zeep.org/en/master/) and [suds](https://bitbucket.org/jurko/suds) may raise an error that looks like: `requests.exceptions.SSLError: [SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed (_ssl.c:600)`.

You can bypass this error with the snippets below:

### Python Zeep

```python
from requests import Session
from zeep.transports import Transport
from zeep import Client


session = Session()
session.verify = False
transport = Transport(session=session)
client = Client('your_webservice?wsdl', transport=transport, strict=False)
```

### Suds

```python
# Before instantiate suds client
import ssl


try:
    _create_unverified_https_context = ssl._create_unverified_context
except AttributeError:
    pass
else:
    ssl._create_default_https_context = _create_unverified_https_context
```