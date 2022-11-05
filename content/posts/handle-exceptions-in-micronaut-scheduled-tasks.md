---
title: 'Handle exceptions in micronaut scheduled tasks'
date: Sat, 28 Dec 2019 22:05:38 +0000
draft: false
tags: ['Micronaut', 'micronaut']
---

By default micronaut simply logs any exceptions that are thrown during scheduled tasks. To change this behaviour you simply have to override the bean `DefaultTaskExceptionHandler`, the default implementation of `TaskExceptionHandler` with your own implementation:

```
@Singleton
@Replaces(DefaultTaskExceptionHandler.class)
@Slf4j
class HelloWorldTaskExceptionHandler implements TaskExceptionHandler<Object, Throwable> {
  //override handle method here
}
```

`@Replaces` lets a bean specify that it replaces another bean. All that is left is to implement the `handle` method which takes the offending bean and the exception that was thrown as arguments.

```
@Override
void handle(@Nullable Object bean, @Nonnull Throwable throwable) {
    log.error("${bean.toString()} threw an exception ${throwable.message}")
    log.info("You can do whatever you want with the thrown exception in here")
}
```

You can find an example implementation of this on [github](https://github.com/amuponda/blog-posts/tree/master/micronaut-scheduled-exceptions).