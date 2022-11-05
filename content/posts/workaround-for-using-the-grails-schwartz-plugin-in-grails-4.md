---
title: 'Workaround for using the grails-schwartz-plugin in Grails 4'
date: Sun, 17 Nov 2019 04:40:13 +0000
draft: false
tags: ['Grails', 'Grails', 'quartz', 'schwartz-plugin']
---

Version 1.0.1 of the [grails-schwartz-plugin](https://github.com/agileorbit/grails-schwartz/tree/master) will not work with a grails 4 application as I recently found whilst trying to upgrade an app. You will get the following compilation error:

```
CONFIGURE SUCCESSFUL in 1s
Error |
Failed to compile create-job.groovy: startup failed:
create-job.groovy: 23: unable to resolve class grails.transaction.Transactional
 @ line 23, column 1.
   import grails.transaction.Transactional
   ^

1 error
 (Use --stacktrace to see the full trace)
Error |
Command \[run-app\] error: null (Use --stacktrace to see the full trace)

Process finished with exit code 1

```

The `create-job.groovy` file used by the create-job command imports the now defunct `@Transactional` AST transform which has now been moved to GORM.

I did some digging and found that there is a release candidate for version 2 fo the plugin(2.0.0.RC1). Using that instead gets rid of the compilation error but introduces a runtime error because the `quartzScheduler` bean can't be created, a `BeanCreationException` is thrown. Digging deeper I realised that the `SchwartzSchedulerFactoryBean` class in the plugin uses a property from `StdSchedulerFactory` that is no longer available in the version of quartz schedular library that is being used.

This is because the version specified in the plugin's dependencies:

```
String quartzVersion = '2.2.3'
compile 'org.quartz-scheduler:quartz:' + quartzVersion, {
  exclude module: 'c3p0'
}
compile 'org.quartz-scheduler:quartz-jobs:' + quartzVersion
```

Is overridden as shown by the gradle build scan I generated

![](https://www.amuponda.com/wp-content/uploads/2019/11/Screen-Shot-2019-11-17-at-2.04.07-pm.png)

schedular version is upgraded to 2.3.1

So as a hack you could enforce the required version by having it as a dependency in your own project's `build.gradle` file. So the plugin dependency can be added like so:

```
compile 'com.agileorbit:schwartz:2.0.0.RC1'
// force version 2.2.3 of quartz
String quartzVersion = '2.2.3'
compile 'org.quartz-scheduler:quartz:' + quartzVersion, {
    exclude module: 'c3p0'
}
compile 'org.quartz-scheduler:quartz-jobs:' + quartzVersion
```

This is just a quick and dirty way to get things running again after an upgrade to grails 4. The new grails guide on scheduling now uses the native [Spring Task Execution and Scheduling](https://docs.spring.io/spring/docs/current/spring-framework-reference/integration.html#scheduling) via `@Scheduled` so you would be better of reimplementing your scheduled tasks to use that.

As always, you can find an example of how to do this on [github](https://github.com/amuponda/blog-posts/tree/master/grails4-schwartz).