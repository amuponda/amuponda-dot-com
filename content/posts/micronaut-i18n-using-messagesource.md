---
title: 'Micronaut i18n using MessageSource'
date: Tue, 28 Apr 2020 19:46:29 +0000
draft: false
tags: ['i18n', 'Micronaut', 'micronaut']
---

To implement i18n in micronaut you need to have a bean that is a subclass of `ResourceBundleMessageSource`. This can be done by using a factory to create the bean:

```
@Factory
class AppConfig {
    @Bean
    MessageSource messageSource() {
        new ResourceBundleMessageSource('com.example.Messages')
    }
}

```

Or just having a `@Singleton` annotated class

```
@Singleton
class AppBundle extends ResourceBundleMessageSource {
       AppBundle() {
           super('com.example.Messages')
       }
}
```

In both cases we pass the baseName of our bundle. After this you create your locale specific property files `Messages.properties`, `Messages_fr.properties` etc in the `src/main/resource/com/example` directory.

```
Message.properties

greeting=Hello {person.name}

```

Then you can inject the bean into your controller or service to use it.

```
@Controller("/hello")
class HelloController {

    private final MessageSource messageSource

    HelloController(MessageSource messageSource) {
        this.messageSource = messageSource
    }

    @Get("/{lang}/{name}")
    String greeting(String lang, String name) {
        MessageSource.MessageContext messageContext =
                MessageSource.MessageContext.of(new Locale(lang), Collections.singletonMap("person.name", name))
        String template = messageSource.getMessage("greeting", messageContext).get()
        return messageSource.interpolate(template, messageContext)
    }
}

```

You will notice that even though the `MessageContext` passed into `getMessage` already contains the variables to resolve the message parameters, you actually still need to call `interpolate` to resolve them. This was raised as an issue on [github](https://github.com/micronaut-projects/micronaut-core/issues/2332) and will no longer be the case in Micronaut 2.0.0

You can find a working example using micronaut 1.3.4 on [github](https://github.com/amuponda/blog-posts/new/master/micronaut-i18n).