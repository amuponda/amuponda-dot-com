---
title: 'Triggering a schwartz job to run immediately in grails'
date: Thu, 14 Nov 2019 08:10:42 +0000
draft: false
tags: ['Grails', 'Grails', 'schwartz-plugin']
---

The [grails schwartz plugin](http://blog.agileorbit.com/grails-schwartz/latest/index.html) comes with a utility service, the `QuartzService`, which you can use to trigger a job immediately.

For example, if you want to run a job on application startup, you can just call the services' `triggerJob` method from the init closure in `Bootstrap.groovy`

```
package schwartz.grails.plugin

class BootStrap {

    def quartzService
    def helloWorldJobService

    def init = { servletContext ->
        quartzService.triggerJob(helloWorldJobService.jobKey)
    }
    def destroy = {
    }
}

```

The `triggerJob` method takes a `JobKey` as a parameter. An example grails application to illustrate this can be found [here](https://github.com/amuponda/blog-posts/tree/master/schwartz-grails-plugin).