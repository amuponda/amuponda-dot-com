---
title: 'Merging OpenAPI Schemas in Micronaut'
date: Mon, 01 Jun 2020 18:56:00 +0000
draft: false
tags: ['Micronaut', OpenAPI]
---

Merging your own schemas into the generated OpenAPI specification is pretty straightforward. 
With OpenAPI integrated into your application, all you need to do is to add the system property `micronaut.openapi.additional.files` 
specifying the location of the additional schema files. In this case I have it set to `src/main/resources/swagger`.

{{< admonition note "build.gradle" >}}
```
tasks.withType(GroovyCompile) {
   groovyOptions.forkOptions.jvmArgs.add("-Dmicronaut.openapi.additional.files=${project.projectDir}/src/main/resources/swagger".toString())
}
```
{{< /admonition >}}

It is worth noting what information is taken from additional files:
{{< admonition note "OpenApiApplicationVisitor.java" >}}
```java
Optional.ofNullable(from.getTags()).ifPresent(tags -> tags.forEach(to::addTagsItem));
Optional.ofNullable(from.getServers()).ifPresent(servers -> servers.forEach(to::addServersItem));
Optional.ofNullable(from.getSecurity()).ifPresent(securityRequirements -> securityRequirements.forEach(to::addSecurityItem));
Optional.ofNullable(from.getPaths()).ifPresent(paths -> paths.forEach(to::path));
Optional.ofNullable(from.getComponents()).ifPresent(components -> { ... });
```
{{< /admonition >}}

The snippet of code above from `OpenApiApplicationVisitor.java` in the micronaut-open-api library shows that you can integrate tag, server, security, path and component info from other schemas.

You can find a working example using micronaut 1.3.5 that integrates a security schema on [github](https://github.com/amuponda/blog-posts/tree/master/micronaut-merge-schemas).