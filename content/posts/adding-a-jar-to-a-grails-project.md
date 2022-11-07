---
title: 'Adding a JAR to a Grails project'
date: Mon, 08 Apr 2019 17:06:30 +0000
draft: false
tags: ['Grails']
---

When you find yourself working with an old Grails 2 project and you need to experiment with changes to one of it's the dependencies without having to publish a snapshot version to an artifact repo, you can always drop the `.jar` into the `lib` folder and you are good to go. If you do not have a `.pom` file associated with the `jar`, attempting to use the local maven repo (`.m2` folder) would result in the error below:

```
Resolve error obtaining dependencies: Failed to read artifact descriptor for com.amuponda:foo:jar:1.0.0-SNAPSHOT (Use --stacktrace to see the full trace)
```

With Grails 3 the `lib` directory was deprecated so the above approach will not work. You explicitly need to tell gradle to use it to resolve dependencies:

```
dependencies {
    ...
    compile fileTree(dir: './lib', include: ['*.jar'])
    ...
}
```