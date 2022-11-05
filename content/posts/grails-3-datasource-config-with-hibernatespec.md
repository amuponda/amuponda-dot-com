---
title: 'Grails 3 datasource config with HibernateSpec'
date: Tue, 04 Feb 2020 17:58:35 +0000
draft: false
tags: ['Grails', 'Grails', 'Hibernate']
---

The default implementation of `HibernateSpec` in Grails 3.3.x overrides the datasource's `dbCreate` value with `create-drop`. To get around this you simply override the `getConfiguration` method and set your preferred value for `dbCreate`. The properties in the map returned by this method take precedence to those in your `application.yml`

```
Map getConfiguration() {
    Collections.singletonMap(Settings.SETTING\_DB\_CREATE, "create-drop") // change this
}
```

It is also worthwhile to note that your default datasource setup also take precedence over the test environment setup. So it is best to setup your datasource in such way that only common settings are in the default setup as stated in the [documentation](https://docs.grails.org/4.0.1/guide/conf.html#dataSource).

```
dataSource:
    pooled: true
    jmxExport: true


environments:
    test:
        dataSource:
            driverClassName: org.mariadb.jdbc.Driver
            dialect: org.hibernate.dialect.MySQL5InnoDBDialect
            username: testuser
            password: ''
            dbCreate: update
            url: jdbc:mariadb://localhost/test\_db    
```

Or better yet just return your test datasource config from `getConfiguration`

```
@Override
Map getConfiguration() {
    \[
            dataSource: \[
                    dbCreate: "update",
                    driverClassName: "org.mariadb.jdbc.Driver",
                    dialect: "org.hibernate.dialect.MySQL5InnoDBDialect",
                    username: "testuser",
                    password: "",
                    url: "jdbc:mariadb://localhost/test\_db"
            \]
    \]
}
```

Please note that this is no longer the case is Grails 4 as `HibernateSpec` is now fully environment aware. As always you can find an example of how to do this [github](https://github.com/amuponda/blog-posts/tree/master/grails3-hibernate-spec).