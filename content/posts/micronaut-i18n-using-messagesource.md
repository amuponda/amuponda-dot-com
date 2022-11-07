---
title: 'Micronaut i18n using MessageSource'
date: Tue, 28 Apr 2020 19:46:29 +0000
draft: false
tags: ['i18n', 'Micronaut']
---

1\. Create a bean of type ResourceBundleMessageSource
----------------------------
To implement i18n in micronaut you need to have a bean that is a subclass of `ResourceBundleMessageSource`. This can be done by using a factory to create the bean:

{{< admonition note "src/main/groovy/com/example/AppConfig.groovy" >}}
```groovy
@Factory
class AppConfig {
    @Bean
    MessageSource messageSource() {
        new ResourceBundleMessageSource('com.example.Messages')
    }
}

```
{{< /admonition >}}

Or just having an `@Singleton` annotated class

{{< admonition note "src/main/groovy/com/example/AppBundle.groovy" >}}
```
@Singleton
class AppBundle extends ResourceBundleMessageSource {
       AppBundle() {
           super('com.example.Messages')
       }
}
```
{{< /admonition>}}

In both cases we pass the baseName of our bundle.

2\. Add the property files
----------------------------
After this you create your locale specific property files `Messages.properties`, `Messages_fr.properties` etc in the `src/main/resource/com/example` directory.

{{< admonition note "src/main/resources/Messages.properties" >}}
```properties
greeting=Hello {person.name}
```
{{< /admonition>}}

{{< admonition note "src/main/resources/Messages_fr.properties" >}}
```properties
greeting=Bonjour {person.name}
```
{{< /admonition>}}

3\. Utilize the bean
----------------------------
Then you can inject the bean into your controller or service to use it.
{{< admonition note "src/main/resources/Messages.properties" >}}
```groovy {hl_lines=15}
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
{{< /admonition >}}

You will notice that even though `MessageContext` is passed into `getMessage()` and it already contains the variables to resolve the message parameters, you actually still need to call `interpolate()` to resolve them. 
This was raised as an issue on [github](https://github.com/micronaut-projects/micronaut-core/issues/2332) and will no longer be the case in Micronaut 2.0.0

You can find a working example using micronaut 1.3.4 on [github](https://github.com/amuponda/blog-posts/tree/master/micronaut-i18n).