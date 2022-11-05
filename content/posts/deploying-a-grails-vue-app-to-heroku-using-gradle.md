---
title: 'Deploying a grails vue app to heroku using gradle'
date: Tue, 06 Nov 2018 15:52:39 +0000
draft: false
tags: ['gradle', 'Grails', 'Grails', 'heroku', 'Heroku', 'Vue.js', 'vuejs']
---

Continuing from my previous [post](https://www.amuponda.com/2018/10/28/combining-the-grails-vue-profile-projects-into-a-single-jar/) on combining the server and client projects of a grails vue profile app into a single executable jar file, you can make use of the [heroku-gradle](https://github.com/heroku/heroku-gradle) plugin to deploy the resulting jar to Heroku. The tutorial [here](https://devcenter.heroku.com/articles/deploying-gradle-apps-on-heroku) can help get started with deploying gradle projects to Heroku. Prior to using the plugin you would need to take the following steps to deploy.

1.  Define your `Procfile` with the command to execute the web app```
    web: java -Dserver.port=$PORT $JAVA\_OPTS -jar build/server-0.1.jar
    ``` 
2.  Create a stage task (default Heroku build task) and make it depend on the assembleServerAndClient so that the build to create the executable jar is triggered.```
    task stage {
      dependsOn assembleServerAndClient
      }
    ``` 
3.  Execute `git push heroku master` to deploy the app.

With the plugin you define following config in build.gradle:```
heroku {
    appName = "floating-shore-99282"
    jdkVersion = "1.8"
    buildpacks = \["heroku/jvm"\]
    processTypes(web: "java -Dserver.port=\\$PORT \\$JAVA\_OPTS -jar build/server-0.1.jar")
}
```The `processTypes` definition effectively replaces the `Procfile` and the stage task can be replaced by this line in your `build.gradle`.```
deployHeroku.dependsOn(assembleServerAndClient)
```Making the `deployHeroku` task depend on assembleServerAndClient task ensures the jar is created. All that is left to do now is run `/.gradlew deployHeroku` to deploy the app. You can refer to [this](https://github.com/amuponda/blog-posts/tree/master/deploy-grails-vue-app-to-heroku) github repo for an example project.