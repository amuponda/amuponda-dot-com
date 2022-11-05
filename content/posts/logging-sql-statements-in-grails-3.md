---
title: 'Logging SQL statements in Grails 3'
date: Thu, 21 Feb 2019 18:36:08 +0000
draft: false
tags: ['Grails', 'Grails']
---

You can enable logging of the SQL queries that Hibernate executes via the `logSql` property of your datasource config in either `application.yml` or `application.groovy`. Setting `logSql` to `true` enables logging to stdout. You will see output similar to the one below:

```
Hibernate: create table book\_chapter\_page\_count (book\_id bigint not null, chapter\_page\_count\_integer integer) ENGINE=InnoDB

Hibernate: alter table book\_chapter\_page\_count add constraint FK87brho4gbq6gbns18fnschhsx foreign key (book\_id) references book (id)
```

The logged SQL can be formatted by setting `formatSql` to `true`.

```
Hibernate: 
    
    create table book\_chapter\_page\_count (
        book\_id bigint not null,
        chapter\_page\_count\_integer integer
    ) ENGINE=InnoDB
Hibernate: 
    
    alter table book\_chapter\_page\_count 
        add constraint FK87brho4gbq6gbns18fnschhsx 
        foreign key (book\_id) 
        references book (id)
```

Turning on SQL logging can be an effective way to a look at what is happening behind the scenes when your encounter issues with GORM. I have come across scenarios were it fails silently without any feedback.

As always you can head over to [github](https://github.com/amuponda/blog-posts/tree/master/logging-sql-statements) for an example project on this.