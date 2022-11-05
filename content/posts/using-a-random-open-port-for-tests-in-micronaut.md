---
title: 'Using a random open port for tests in Micronaut'
date: Tue, 04 Aug 2020 07:04:01 +0000
draft: false
tags: ['Micronaut']
---

By default if the configuration property `micronaut.server.port` is not specified a Micronaut application will run on port 8080 and tests will run on a random port. If however you specify a port for the application to run on, your tests will also run on that port.

For tests to run on a random port again you need to set `micronaut.server.port` to either -1 or `${random.port}` in your application-test.yml config file. `${random.port}` is one of Micronaut's random properties. Random properties can be used to set random values for configuration properties.

An example project using Micronaut 2.0.1 can be found on [github](https://github.com/amuponda/blog-posts/tree/master/mn-random-ports "https://github.com/amuponda/blog-posts/tree/master/mn-random-ports").