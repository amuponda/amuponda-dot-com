---
title: 'Using SQL Changelogs with the Micronaut Liquibase library'
date: Mon, 31 Jan 2022 08:36:06 +0000
draft: false
tags: ['Micronaut']
---

1\. Introduction
----------------

Micronaut provides an integration with Liquibase to make it easy to manage database schema changes. This tutorial will show you can write your changelog files using SQL instead of XML. To begin, you need to add the `micronaut-liquibase` dependency to your `build.gradle` file.
{{< admonition note "build.gradle" >}}
```
implementation("io.micronaut.liquibase:micronaut-liquibase")
```
{{< /admonition >}}

2\. Configuration
-----------------

Set the `schema-generation` configuration for your datasource to `NONE` so that Micronaut Data does not attempt to generate the schema for you after the liquibase changelogs have been run. We are using MariaDB in this project.
{{< admonition note "application.yml" >}}
```yaml
datasources:
  default:
    url: jdbc:mariadb://localhost:3306/mn-liquibase
    driverClassName: org.mariadb.jdbc.Driver
    username: sa
    password: ''
    schema-generate: NONE
    dialect: MYSQL
```
{{< /admonition >}}

Define you liquibase configuration specifying the location of your changelog file on the classpath. In our case it is located in `src/main/resources/db/changelog.xml`

{{< admonition note "application.yml" >}}
```yaml {hl_lines=[4]}
liquibase:
    datasources: 
        default: 
            change-log: 'classpath:db/changelog.xml'
```
{{< /admonition >}}

3\. Changelog files
-------------------

{{< admonition note "src/main/resources/db/changelog.xml" >}}
```xml {hl_lines=[8]}
<?xml version="1.0" encoding="UTF-8"?>

<databaseChangeLog
  xmlns="http://www.liquibase.org/xml/ns/dbchangelog"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://www.liquibase.org/xml/ns/dbchangelog
         http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-3.1.xsd">
  <include file="changelog/CreateUserTable.sql" relativeToChangelogFile="true"/>
</databaseChangeLog>
```
{{< /admonition >}}

Our example root changelog file above has one entry that points to the `CreateUserTable.sql` changelog sql.

{{< admonition note "src/main/resources/db/changelog/CreateUserTable.sql" >}}
```sql
-- liquibase formatted sql
-- changeset alfred:CreateUserTable
CREATE TABLE `user` (
    `id` bigint(20) NOT NULL AUTO_INCREMENT,
    `email` varchar(255) NOT NULL,
    `first_name` varchar(255) NOT NULL,
    `last_name` varchar(255) NOT NULL,
    `password` varchar(255) NOT NULL,
    PRIMARY KEY (`id`),
    UNIQUE KEY (`email`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```
{{< /admonition >}}
The SQL changelog file must follow [liquibase format rules](https://docs.liquibase.com/concepts/basic/sql-format.html?Highlight=sql) and begin with the comment below

{{< admonition >}}
```sql
-- liquibase formatted sql
```
{{< /admonition >}}

And each changeset in the file must begin with comment in the following format

{{< admonition >}}
```sql
--changeset author:id attribute1:value1 attribute2:value2 [...]
```
{{< /admonition >}}

4\. Run the application
-----------------------

That's it, with everything in place the `CreateUserTable.sql` changelog should be executed when the application starts up.

You can find an example project using Micronaut 3.3.0 on [github](https://github.com/amuponda/blog-posts/tree/master/micronaut-liquibase "github").