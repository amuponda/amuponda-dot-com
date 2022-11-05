---
title: 'Adding Spring Boot Admin to a Grails application'
date: Thu, 29 Nov 2018 17:19:55 +0000
draft: false
tags: ['Grails', 'Grails', 'Spring Boot', 'spring-boot-admin']
---

In this blog post, the  first part of the Spring Boot Admin series, I will illustrate how to use  [Spring Boot Admin](http://codecentric.github.io/spring-boot-admin/1.5.7/#getting-started) to monitor and manage a Grails application.

**1\. Setting up the Spring Boot Admin Server**
-----------------------------------------------

The getting started guide clearly explains how to setup an admin server. This basically involves creating a simple Spring Boot app and adding the SBA server dependency:```
implementation('de.codecentric:spring-boot-admin-starter-server:1.5.7')
```Since grails in built on top of spring boot 1.5.x, I use version 1.5.7 of Spring Boot Admin Server. Next, add `@EnableAdminServer` to your configuration and the server will be go to go.```
@EnableAdminServer
@SpringBootApplication
class SpringBootAdminServerApplication {

  static void main(String\[\] args) {
    SpringApplication.run SpringBootAdminServerApplication, args
  }
}
```

2\. Registering the Grails Application as a client
--------------------------------------------------

With the admin server up and running we need to register the app with it. The first step is to  add the SBA client dependency to the client's `build.gradle` file as shown below:```
compile("de.codecentric:spring-boot-admin-starter-client:1.5.7")
```The rest of the client setup involves adding a few configurations to your `application.yml`. Actuator endpoints are disabled by default since Grails 3.1.8 so they will need to be enabled.```
endpoints:
    enabled: true
```Most of the actuator endpoints are secured. So the following config will disable security for the endpoints:```
management:
    security:
        enabled: false
```Finally, we need enable the SBA client by adding a config for the SBA server base URL:```
spring:
    boot:
        admin:
            url: 'http://localhost:8888'
```Since actuator endpoints expose sensitive information, part two of the series will focus on adding security to both the admin server and the grails app. The code for this post is available on [github](https://github.com/amuponda/blog-posts/tree/master/grails-sba).