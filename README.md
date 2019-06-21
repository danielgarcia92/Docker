# Docker

## Build Setup

``` bash

# Image Nginx
docker build -f nginx/Dockerfile -t danielgarcia992/nginx nginx

# Image PHP
docker build -f php/Dockerfile -t danielgarcia992/php php

# Container Redis
docker run --rm -d \
--name redis \
redis:alpine

# Container MySQL
docker run --rm -d \
--name mysql \
-e MYSQL_ROOT_PASSWORD=root \
-e MYSQL_DATABASE=docker \
-e MYSQL_USER=docker \
-e MYSQL_PASSWORD=docker \
mysql:5.7

# Container PHP
docker run --rm -d \
--name php \
-v $(pwd)/application:/var/www/html \
--link redis:redis \
--link mysql:mysql \
danielgarcia992/php

# Container Nginx
docker run --rm -d \
--name nginx \
-v $(pwd)/application:/var/www/html \
-p 80:80 \
--link php:php \
danielgarcia992/nginx


