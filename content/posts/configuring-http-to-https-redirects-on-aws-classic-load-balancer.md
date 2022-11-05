---
title: 'Configuring HTTP to HTTPS redirects on AWS classic load balancer'
date: Sat, 20 Nov 2021 06:18:16 +0000
draft: false
tags: ['AWS', 'Micronaut']
---

If you have a Micronaut application deployed on AWS Elastic Beanstalk in an environment with a classic load balancer it is a straightforward process to redirect your HTTP traffic to HTTPS.

Create a file named `./platform/nginx/conf.d/elasticbeanstalk/00_application.conf` in the build file you upload to Elastic Beanstalk. The `00_application.conf` file should contain the following configuration.

{{< admonition note "./platform/nginx/conf.d/elasticbeanstalk/00_application.conf" >}}
```nginx {hl_lines=[3,6]}
location / {
     set $redirect 0;
     if ($http_x_forwarded_proto != "https") { #1
       set $redirect 1;
     }
     if ($http_user_agent ~* "ELB-HealthChecker") { #2
       set $redirect 0;
     }
     if ($redirect = 1) {
       return 301 https://$host$request_uri;
     }

     proxy_pass          http://127.0.0.1:5000;
     proxy_http_version  1.1;

     proxy_set_header    Connection          $connection_upgrade;
     proxy_set_header    Upgrade             $http_upgrade;
     proxy_set_header    Host                $host;
     proxy_set_header    X-Real-IP           $remote_addr;
     proxy_set_header    X-Forwarded-For     $proxy_add_x_forwarded_for;
}
```
{{< /admonition >}}
`1` If the protocol is not https set `redirect = 1`

`2` If the environment uses load balancing health checks it is important not to redirect these requests, hence the check for the user agent. 

In our case we are extending the reverse proxy configuration so our file will be included in the default configuration `/etc/nginx/nginx.conf`.

{{< admonition note "/etc/nginx/nginx.conf" >}}
```nginx
# Elastic Beanstalk Nginx Configuration File

user                    nginx;
error_log               /var/log/nginx/error.log warn;
pid                     /var/run/nginx.pid;
worker_processes        auto;
worker_rlimit_nofile    32650;

events {
    worker_connections  1024;
}

http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    include       conf.d/*.conf;

    map $http_upgrade $connection_upgrade {
        default     "upgrade";
    }

    server {
        listen        80 default_server;
        access_log    /var/log/nginx/access.log main;

        client_header_timeout 60;
        client_body_timeout   60;
        keepalive_timeout     60;
        gzip                  off;
        gzip_comp_level       4;
        gzip_types text/plain text/css application/json application/javascript application/x-javascript text/xml application/xml application/xml+rss text/javascript;

        # Include the Elastic Beanstalk generated locations
        include conf.d/elasticbeanstalk/*.conf; #1
    }
}
```
{{< /admonition >}}

`1` As you can see, as part of the server configuration, the default nginx configuration includes any configuration files located in `/etc/nginx/conf.d/elasticbeanstalk`.

And with that HTTP requests to your micronaut application should be redirected to HTTPS. You can read more about configuring the reverse proxy in the [reverse proxy section](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/java-se-nginx.html) of the AWS Elastic Beanstalk documentation.