---
title: 'Working with multiple datasources and NamedParameterJdbcTemplate in Spring Boot 2.0.5'
date: Sun, 21 Oct 2018 06:50:29 +0000
draft: false
tags: ['Spring Boot', 'spring-boot']
---

In this post I will illustrate how to use `NamedParameterJdbcTemplate` with multiple datasources in a Spring Boot 2.0.5 application. It expands on the spring guide available [here](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html).

**1\. Define the datasource properties**
----------------------------------------

Add datasource properties to `application.properties` for each datasource to be configured.```
#db1 datasource
db1.datasource.jdbc-url=jdbc:mariadb://localhost/datasources\_db1
db1.datasource.username=root
db1.datasource.password=

#db2 datasource
db2.datasource.jdbc-url=jdbc:mariadb://localhost/datasources\_db2
db2.datasource.username=root
db2.datasource.password=
```Note that with Hikari on the classpath we need to replace the `url` property with `jdbc-url`  as Hikari does not have a url property.

**2\. Configure the custom datasources**
----------------------------------------

Add the following beans to a class annotated with `@Configuration`.```
@Bean
@Primary
@ConfigurationProperties(prefix = "db1.datasource")
DataSource dataSourceOne() {
    return DataSourceBuilder.create().build()
}

@Bean
@ConfigurationProperties(prefix = "db2.datasource")
DataSource dataSourceTwo() {
    return DataSourceBuilder.create().build()
}

@Bean
NamedParameterJdbcTemplate namedParameterJdbcTemplateOne() {
    return new NamedParameterJdbcTemplate(dataSourceOne())
}

@Bean
NamedParameterJdbcTemplate namedParameterJdbcTemplateTwo() {
    return new NamedParameterJdbcTemplate(dataSourceTwo())
}
```According do the guide, the `@Primary` annotation on one of the datasource beans should be put in for the various auto-configurations down the road that expect to get a datasource by type.

**3\. Use the defined beans**
-----------------------------

The `NamedParemeterJdbcTemplate` beans can then be autowired as follows:```
@Autowired
@Qualifier("namedParameterJdbcTemplateOne")
NamedParameterJdbcTemplate jdbcOne

@Autowired
@Qualifier("namedParameterJdbcTemplateTwo")
NamedParameterJdbcTemplate jdbcTwo
```Since by default `@Autowire` entries are resolved by type, the `@Qualifier` annotation is used to specify which of the two beans to use. Another approach would be to use the @Primary annotation on one of the defined beans. In that case, `@Qualifier` would only be needed when autowiring the other bean. An example project to illustrate the above is available on [github](https://github.com/amuponda/blog-posts/tree/master/multiple-datasources).