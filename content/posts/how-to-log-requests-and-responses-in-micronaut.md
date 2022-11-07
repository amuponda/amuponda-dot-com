---
title: 'How to log requests and responses in micronaut'
date: Sun, 18 Oct 2020 20:25:10 +0000
draft: false
tags: ['Micronaut', 'Logbook']
---

Micronaut allows you to customize the Netty pipeline by using a bean event listener. You can therefore add custom channel handlers to the pipeline. Using this knowledge together with [Logbook](https://github.com/zalando/logbook) library you can easily log a request and the corresponding response.

1\. Add logbook dependencies
----------------------------

Add the Logbook dependencies `logbook-core` and `logbook-netty` to your `build.gradle`.
{{< admonition note "build.gradle" >}}
```
implementation("org.zalando:logbook-core:2.3.0") 
implementation("org.zalando:logbook-netty:2.3.0")
```
{{< /admonition>}}

2\. Add the Logbook bean
------------------------

Create a bean of type `Logbook` via a `@Factory` annotated configuration class to be used by the pipeline customizer.

{{< admonition note "src/main/groovy/mn/zalando/logbook/BeanFactory.groovy" >}}
```groovy
package mn.zalando.logbook

import io.micronaut.context.annotation.Factory
import org.zalando.logbook.Logbook

import javax.inject.Singleton

@Factory
class BeanFactory {

    @Singleton
    Logbook logbook() {
        Logbook.create()
    }
}
```
{{< /admonition>}}

3\. Create the ChannelPipelineCustomizer
----------------------------------------
{{< admonition note "src/main/groovy/mn/zalando/logbook/LogbookPipelineCustomizer.groovy" >}}
```groovy
package mn.zalando.logbook

import io.micronaut.context.annotation.Requires
import io.micronaut.context.event.BeanCreatedEvent
import io.micronaut.context.event.BeanCreatedEventListener
import io.micronaut.http.netty.channel.ChannelPipelineCustomizer
import io.netty.channel.ChannelPipeline
import org.zalando.logbook.Logbook
import org.zalando.logbook.netty.LogbookClientHandler
import org.zalando.logbook.netty.LogbookServerHandler

import javax.inject.Singleton

@Requires(beans = Logbook.class)
@Singleton
class LogbookPipelineCustomizer implements BeanCreatedEventListener<ChannelPipelineCustomizer> {

    private final Logbook logbook

    LogbookPipelineCustomizer(Logbook logbook) {
        this.logbook = logbook
    }

    @Override
    ChannelPipelineCustomizer onCreated(BeanCreatedEvent<ChannelPipelineCustomizer> event) {
        ChannelPipelineCustomizer customizer = event.getBean()
        if (customizer.isServerChannel()) {
            customizer.doOnConnect({ ChannelPipeline pipeline ->
                pipeline.addAfter(
                        ChannelPipelineCustomizer.HANDLER_HTTP_SERVER_CODEC,
                        "logbook",
                        new LogbookServerHandler(logbook)
                )
                return pipeline
            })
        } else {
            customizer.doOnConnect({ ChannelPipeline pipeline ->
                pipeline.addAfter(
                        ChannelPipelineCustomizer.HANDLER_HTTP_SERVER_CODEC,
                        "logbook",
                        new LogbookClientHandler(logbook))
                return pipeline
            })
        }
        return customizer
    }
}
```
{{< /admonition>}}

The class above implements the `BeanCreatedEventListener` interface enabling you to listen for the creation of the `ChannelPipelineCustomizer` bean. With `ChannelPipelineCustomizer` at hand you can add custom channel handlers to the pipeline whenever a new channel is established. In this case we add `LogbookServerHandler` from the `Logbook` library which takes care of the logging.

If clients are sending requests with the `Accept-Encoding` header, then for the server you might want to add the logbook handler after the compression handler `HANDLER_HTTP_COMPRESSOR` instead of `HANDLER_HTTP_SERVER_CODEC`. This ensures that the logging of the response happens before the data is compressed.

{{< admonition note "src/main/groovy/mn/zalando/logbook/LogbookPipelineCustomizer.groovy" >}}
```groovy
if (customizer.isServerChannel()) { 
 	customizer.doOnConnect({ ChannelPipeline pipeline -> 
 		pipeline.addAfter( ChannelPipelineCustomizer.HANDLER_HTTP_COMPRESSOR, "logbook", new LogbookServerHandler(logbook) ) 
 		return pipeline 
 	}) 
}
```
{{< /admonition >}}


4\. Set Logbook log level
-------------------------

The logbook logger needs to be configured to trace level in order to log the requests and responses.
{{< admonition note "src/main/resources/logback.xml" >}}
```xml
<logger name="org.zalando.logbook" level="TRACE"/>
```
{{< /admonition >}}
A working example using Micronaut 2.1.1 can be found on [github](https://github.com/amuponda/blog-posts/tree/master/mn-zalando-logbook "https://github.com/amuponda/blog-posts/tree/master/mn-zalando-logbook").