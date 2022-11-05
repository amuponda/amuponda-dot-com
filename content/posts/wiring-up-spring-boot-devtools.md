---
title: 'Wiring up Spring Boot DevTools'
date: Wed, 23 Oct 2019 16:15:05 +0000
draft: false
tags: ['dev-tools', 'DevTools', 'Gradle', 'Spring Boot', 'spring-boot']
---

The [spring-boot-dev-tools](https://docs.spring.io/spring-boot/docs/2.2.0.RELEASE/reference/html/using-spring-boot.html#using-boot-devtools) [](https://docs.spring.io/spring-boot/docs/2.2.0.RELEASE/reference/html/using-spring-boot.html#using-boot-devtools) module allows you to automatically restart your application whenever you make changes. It works by detecting changes to files on the classpath. To add devtools to a project just add the dependency to your `build.gradle` as shown below:

```
configurations {
    developmentOnly
    runtimeClasspath {
        extendsFrom developmentOnly
    }
}
dependencies {
    developmentOnly("org.springframework.boot:spring-boot-devtools")
}
```

If you are using Intellij IDE you can trigger a restart by building the project. When working with an IDE that continually compiles changes you can use a trigger file and restarts will only occur when that file changes.

```
spring.devtools.restart.trigger-file=.reloadTriggerFile
```

[Gradle's continuous build](https://docs.gradle.org/current/userguide/command_line_interface.html#sec:continuous_build) allows you to use devtools when you are not working with and IDE. Since it continuously runs a task you can use it to continuously build the project.

In the first terminal window, navigate to the project and run:

`gradle build --continuous`

In the second, navigate to the project and run the app

`gradle bootRun`

With this gradle will behave as if you have run `gradle build` when you save changes to files, this will in-turn trigger a restart.

You can find an example on [github](https://github.com/amuponda/blog-posts/tree/master/spring-boot-devtools).