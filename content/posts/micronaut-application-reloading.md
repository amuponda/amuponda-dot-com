---
title: 'Micronaut application reloading'
date: Tue, 26 May 2020 19:20:01 +0000
draft: false
tags: ['gradle', 'Gradle', 'Micronaut', 'micronaut']
---

Since micronaut has a quick startup time the easiest way to reload an application after making changes is to have automatic restart setup. The first step in order to achieve this is to have this configuration in your `application.yml`

```
micronaut:
  io:
    watch:
      paths: src/main
      restart: true
```

On its own `micronaut.io.watch.paths` results in a `FileChangedEvent` being fired every time a file on the specified path(s) is changed. An event listener can be added if you want to do something on file changes.

```
package com.example

import groovy.util.logging.Slf4j
import io.micronaut.context.event.ApplicationEventListener
import io.micronaut.scheduling.io.watch.event.FileChangedEvent

import javax.inject.Singleton

@Singleton
@Slf4j
class FileChangedEventListener implements ApplicationEventListener<FileChangedEvent> {
    @Override
    void onApplicationEvent(FileChangedEvent event) {
        log.info("A file has been changed.")
    }
}
```

Adding `micronaut.io.watch.restart` will stop the server when a file changes. To restart the server you can make use the gradle [continuous build](https://docs.gradle.org/current/userguide/command_line_interface.html#sec:continuous_build) feature:

`./gradlew run --continuous` or `./gradlew run -t`

This will restart the application after the server is stopped.

If you are working on a Mac OS X the documentation recommends adding the following dependencies as the native file polling is slow.

```
developmentOnly "io.micronaut:micronaut-runtime-osx:${micronautVersion}"
developmentOnly "net.java.dev.jna:jna"
developmentOnly "io.methvin:directory-watcher"
```

A working example of how to apply do this wit micronaut 1.3.5 can be found on [github](https://github.com/amuponda/blog-posts/tree/master/micronaut-continous-build).