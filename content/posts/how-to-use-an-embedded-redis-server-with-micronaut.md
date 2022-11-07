---
title: 'How to use an embedded redis server with Micronaut'
date: Mon, 26 Oct 2020 04:52:17 +0000
draft: false
tags: ['Micronaut']
---

Micronaut provides support to use an embedded redis server which can be handy for running your tests.

To run an embedded redis server you need to include the embedded redis dependency in your `build.gradle` file.

{{< admonition note "build.gradle" >}}
`testImplementation "com.github.kstyrc:embedded-redis:0.6"`
{{< /admonition >}}

This will automatically start and the shutdown an embedded instance of Redis if none is available for the configured Redis URI.

Micronaut achieves this by adding [EmbeddedRedisServer](https://micronaut-projects.github.io/micronaut-redis/snapshot/api/io/micronaut/configuration/lettuce/test/EmbeddedRedisServer.html) which implements [BeanCreatedEventListener](https://docs.micronaut.io/latest/api/io/micronaut/context/event/BeanCreatedEventListener.html) and listens for the creation of the redis configuration. It checks to see if redis is configured to use localhost and if the port is available. If that is the case, an embedded Redis instance is started.

{{< admonition note "EmbeddedRedisServer.java" >}}
```java
if (StringUtils.isNotEmpty(host) && host.equals("localhost") && SocketUtils.isTcpPortAvailable(port)) {
   RedisServerBuilder builder = embeddedConfiguration.builder;
   builder.port(port);
   builder.setting(DEFAULT_MAXMEMORY_SETTING);
   redisServer = builder.build();
   redisServer.start();
}
```
{{< /admonition >}}

So essentially all you need to get this working is configuring your `redis.uri` in `application-test.yml` to use localhost and a random port as shown below:
{{< admonition note "application-test.yml" >}}
```yaml
    redis.uri: redis://localhost:${random.port}/0
```
{{< /admonition >}}


An example project using Micronaut 2.1.1 is available on [github](https://github.com/amuponda/blog-posts/tree/master/mn-embedded-redis).