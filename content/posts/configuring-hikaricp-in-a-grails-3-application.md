---
title: 'Configuring HikariCP in a Grails 3.3.X Application'
date: Mon, 10 Dec 2018 06:13:22 +0000
draft: false
tags: ['Grails', 'Grails', 'HikariCP']
---

This post will show how to use a HikariCP JDBC connection pool in a Grails 3 application. By default, GORM for Hibernate uses a Tomcat JDBC connection pool when you bootstrap your app using grails' `create-app` command. Switching to HikariCP is simple. Remove the Tomcat JDBC dependency in `build.gradle````
runtime "org.apache.tomcat:tomcat-jdbc"
```And replace it with the HikariCP dependency as follows:```
runtime "com.zaxxer:HikariCP:3.2.0"
```Just like Tomcat JDBC, HikariCP config properties can be set in `application.yml` under the `datasource.properties` key. An example of specifying the poolName is shown below:```
dataSource:
            properties:
                poolName: 'grails-hikari'
```And just like that the switch to HikariCP is complete. A sample grails application using HikariCP is available on [github](https://github.com/amuponda/blog-posts/tree/master/grails-hikari-configuration).