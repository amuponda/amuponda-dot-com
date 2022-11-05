---
title: 'Configuring HTTP to HTTPS redirects on AWS classic load balancer'
date: Sat, 20 Nov 2021 06:18:16 +0000
draft: false
tags: ['AWS', 'Micronaut', 'micronaut']
---

If you have a Micronaut application deployed on AWS Elastic Beanstalk in an environment with a classic load balancer it is a straightforward process to redirect your HTTP traffic to HTTPS.

Create a file named `./platform/nginx/conf.d/elasticbeanstalk/00_application.conf` in the build file you upload to Elastic Beanstalk.

```
location / {
     set $redirect 0;
     if ($http\_x\_forwarded\_proto != "https") {
       set $redirect 1;
     }
     if ($http\_user\_agent ~\* "ELB-HealthChecker") {
       set $redirect 0;
     }
     if ($redirect = 1) {
       return 301 https://$host$request\_uri;
     }

     proxy\_pass          http://127.0.0.1:5000;
     proxy\_http\_version  1.1;

     proxy\_set\_header    Connection          $connection\_upgrade;
     proxy\_set\_header    Upgrade             $http\_upgrade;
     proxy\_set\_header    Host                $host;
     proxy\_set\_header    X-Real-IP           $remote\_addr;
     proxy\_set\_header    X-Forwarded-For     $proxy\_add\_x\_forwarded\_for;
}
```

Since the environment uses load balancing health checks it is important not to redirect these requests, hence the check for the user agent. In our case we are extending the reverse proxy configuration so our file will be included in the default configuration:

/etc/nginx/nginx.conf

```
#Elastic Beanstalk Nginx Configuration File

user                    nginx;
error\_log               /var/log/nginx/error.log warn;
pid                     /var/run/nginx.pid;
worker\_processes        auto;
worker\_rlimit\_nofile    32650;

events {
    worker\_connections  1024;
}

http {
    include       /etc/nginx/mime.types;
    default\_type  application/octet-stream;

    log\_format  main  '$remote\_addr - $remote\_user \[$time\_local\] "$request" '
                      '$status $body\_bytes\_sent "$http\_referer" '
                      '"$http\_user\_agent" "$http\_x\_forwarded\_for"';

    include       conf.d/\*.conf;

    map $http\_upgrade $connection\_upgrade {
        default     "upgrade";
    }

    server {
        listen        80 default\_server;
        access\_log    /var/log/nginx/access.log main;

        client\_header\_timeout 60;
        client\_body\_timeout   60;
        keepalive\_timeout     60;
        gzip                  off;
        gzip\_comp\_level       4;
        gzip\_types text/plain text/css application/json application/javascript application/x-javascript text/xml application/xml application/xml+rss text/javascript;

        # Include the Elastic Beanstalk generated locations
        include conf.d/elasticbeanstalk/\*.conf;
    }
}
```

As you can see, as part of the server configuration, the default nginx configuration includes any configuration files located in `/etc/nginx/conf.d/elasticbeanstalk`.

And with that HTTP requests to your micronaut application should be redirected to HTTPS. You can read more about configuring the reverse proxy in the [reverse proxy section](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/java-se-nginx.html) of the AWS Elastic Beanstalk documentation.