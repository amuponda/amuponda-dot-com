---
title: 'The grails database migration plugin'
date: Mon, 22 Apr 2019 17:12:51 +0000
draft: false
tags: ['Grails', 'Grails']
---

Grails has a comprehensive [guide](https://guides.grails.org/grails-database-migration/guide/index.html) on how to use the [database-migration plugin](http://plugins.grails.org/plugin/grails/database-migration) for your greenfield Grails projects. The guide however does not explain clearly how to go about this with an existing app. This blog post details how to setup it up in that scenario.

The first thing is to set your datasource `dbCreate` property to `none` in the `application.yml`:

```
dataSource:
          dbCreate: none
```

And install the plugin as mentioned in the guide. With the plugin installed and the migrations folder location setup in `build.gradle` the next step is to create the `changelog.groovy` file. Instead of running the:

`$ grails dbm-generate-gorm-changelog`

or

`$ grails dbm-generate-changelog grails`

commands to create `changelog.groovy` based on your GORM classes or database respectively run:

`$ grails dbm-create-changelog changelog.groovy`

This creates an empty `changelog.groovy` file eliminating the need to sync the current state of the database with the plugin's migrations tracking tables. The only thing left is to create the `DATABASECHANGELOG` and `DATABASECHANGELOGLOCK` tables in the database. You can either run:

`$ grails dbm-update`

Or more appropriately setup such that migrations are run on startup:

_grails-app/conf/application.yml_

```
grails:
    plugin:
        databasemigration:
            updateOnStart: true
            updateOnStartFileName: changelog.groovy
```

And then just run:

`$ grails run-app`

Which will create the two tables in the database on the first run. From here on in you can proceed as normal and create change sets manually.

You can find a simple app illustrating this on [github](https://github.com/amuponda/blog-posts/tree/master/grails-dbmigration).