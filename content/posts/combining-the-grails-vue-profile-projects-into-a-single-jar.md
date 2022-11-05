---
title: 'Combining the Grails Vue profile projects into a single jar'
date: Sun, 28 Oct 2018 04:24:41 +0000
draft: false
tags: ['Grails', 'Grails', 'Vue.js', 'vuejs']
---

When working with the Vue profile in grails it creates a multi-project build with server and client projects. Grails offers [documentation](http://guides.grails.org/react-combined/guide/index.html) on how to combine the two into a single deployable jar. You create a top level `build.gradle` file with a task to copy the contents of the client build folder into the server resources folder.

However, since Webpack bundles your client app into the `dist` folder, the tutorial will not work as it is. You will need to copy this `dist` folder over to the server project as well. This results in this modified `copyClientResources` task:

```
task copyClientResources(dependsOn: ':client:build') { 
    group = 'build'
    description = 'Copy client resources into server'
    doLast {
        copy {
            from "${project(':client').projectDir}/dist/"
            into "${project(':server').buildDir}/resources/main/public"
        }
        copy {
            from project(':client').buildDir.absolutePath
            into "${project(':server').buildDir}/resources/main/public"
        }
    }
}
```

### Update for Grails 4

With Grails 4, the Vue profile will now use vue-cli 3 and this comes with some changes. The URL that the Vue app uses for REST requests will now be set using [environment variables](https://cli.vuejs.org/guide/mode-and-env.html#environment-variables). So previously were you would set the the `SERVER_URL` to the root context in `client/config/prod.env.js` like so:

```
'use strict'
module.exports = {
  NODE\_ENV: '"production"',
  SERVER\_URL: '""'
}
```

You now need to set it in `client/.env.production` like so:

```
VUE\_APP\_SERVER\_URL=""
```

Also syntax changes in Gradle 5 mean that this line in the root project's `build.gradle`:

```
task(":server:assemble").mustRunAfter(copyClientResources)
```

Will now need to change to something like this:

```
project(":server").getTasksByName('assemble', true)\[0\].mustRunAfter(copyClientResources) 
```

You can have a look at this example project on [github](https://github.com/amuponda/blog-posts/tree/master/assemble-grails-vue-app) that illustrates this.