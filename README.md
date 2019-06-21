# Docker

## Build Setup

``` bash

# Image Nginx
docker build -f nginx/Dockerfile -t danielgarcia992/nginx nginx

# Image PHP
docker build -f php/Dockerfile -t danielgarcia992/php php

# Container PHP
docker run --rm -d \
--name php \
-v $(pwd)/application:/var/www/html \
danielgarcia992/php

# Container Nginx
docker run --rm -d \
--name nginx \
-v $(pwd)/application:/var/www/html \
-p 80:80 \
--link php \
danielgarcia992/nginx


