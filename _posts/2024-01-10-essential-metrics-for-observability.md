---
title: Essential metrics for Observability
author: lucas
date: 2024-01-10 18:30:00 -0300
categories: [Blogging, Tutorial, Observability]
tags: [observability, metrics]
---

Observability is a powerful technique that allows you to monitor the internal state of your application using external tools. Simply put, it's like having an electrocardiogram to monitor your system.

Observability primarily rests on three pillars:

- Metrics;
- Traces;
- Logs;

A common question that arises with metrics is: I've just created a new application or endpoint. What should I measure?

A good starting point is to look at the "Four Golden Signals" as defined by Google, which are:

- Latency: How long does it take for your application to respond to a request?
- Traffic (or Throughput): The number of requests per second;
- Errors: The number of errors per second;
- Saturation: How much of the available resources have you used? (for example, how many GB of your database disk are being utilized?)

An alternative when saturation measurement is not necessary is to use the RED metrics:

- R (requests): The number of requests per second;
- E (errors): The number of errors per second;
- D (duration): The amount of time it takes for the application to respond to a request (latency)

In this way, you'll cover much of what is necessary to understand the current state of your application.

Resources:

- https://sre.google/sre-book/monitoring-distributed-systems/;
- https://www.weave.works/blog/the-red-method-key-metrics-for-microservices-architecture/;
- Book: [Observability Engineering](https://www.amazon.com/Observability-Engineering-Achieving-Production-Excellence/dp/1492076449/ref=sr_1_1?crid=3HUSUIFI6E5XQ&keywords=observability+engineering&qid=1704922623&sprefix=observability+engineeri%2Caps%2C220&sr=8-1);

Thanks for reading this post! Bye 👋🏾
