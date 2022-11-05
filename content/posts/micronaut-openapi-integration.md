---
title: 'Micronaut OpenAPI integration'
date: Sat, 08 Feb 2020 18:41:00 +0000
draft: false
tags: ['Micronaut', 'micronaut']
---

The Micronaut 1.2.10 [documentation](https://micronaut-projects.github.io/micronaut-openapi/latest/guide/index.html) does an excellent job of explaining how to add OpenAPI integration to your app. However, I failed to generate the OpenAPI views when using `swagger-groovy` feature to generate my project:

`mn create-app micronaut-openapi-app --features swagger-groovy --lang=groovy`

After some digging around I found out you need to use a later version of the `micronaut-openapi` library to get things working.

`compileOnly "io.micronaut.configuration:micronaut-openapi:1.3.4"`

When using the `swagger-groovy` feature, the generated project ships with version 1.2.3 of the library which does not work properly.

An example project of how to integrate micronaut with OpenAPI can be found [here](https://github.com/amuponda/blog-posts/tree/master/micronaut-openapi-app).