---
title: 'Micronaut profile based configuration properties'
date: Mon, 24 Feb 2020 17:49:02 +0000
draft: false
tags: ['Micronaut', 'micronaut']
---

Micronaut allows environment specific configuration in the same manner as Spring Boot. It searches for your application.yml, application.properties, application.json or application.groovy file. So for your production environment you could define application-prod.yml:

```
micronaut:
  application:
    name: hello-service

hello:
  message: This is prod
```

The configuration properties can be accessed using the `@Value` annotation in controllers and services.

```
@Value('${hello.message}')
private String message
```

When you run the jar you can specify the environment using the `micronaut.environments` system property:

`java -Dmicronaut.environments=prod -jar app.jar`

Injecting the `Environment` bean allows you to get access to the environment programmatically:

```
@Inject
Environment environment

@Get("/")
public Set<String> getProfiles() {
    return environment.getActiveNames()
}
```