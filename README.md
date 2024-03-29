# Docker

> Dockerizing a PHP project

## Build Setup

``` bash

# Image Nginx
docker build -f nginx/Dockerfile -t danielgarcia992/nginx nginx

# Image PHP
docker build -f php/Dockerfile -t danielgarcia992/php php

# Create a volume
docker volume create --driver=local mysqldata


# ------------------------------------
# 		Creating a network

docker network create my_network
docker network ls

# Container Redis
docker run --rm -d \
--name redis \
--network my_network \
redis:alpine

# Container MySQL
docker run --rm -d \
--name mysql \
--network my_network \
-v mysqldata:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=root \
-e MYSQL_DATABASE=docker \
-e MYSQL_USER=docker \
-e MYSQL_PASSWORD=docker \
-p 3306:3306 \
mysql:5.7

# Container PHP
docker run --rm -d \
--name php \
-v $(pwd)/application:/var/www/html \
--network my_network \
danielgarcia992/php

# Container Nginx
docker run --rm -d \
--name nginx \
-v $(pwd)/application:/var/www/html \
-p 80:80 \
--network my_network \
danielgarcia992/nginx


# --------------------------------------------
# Create a laravel application in a container
docker run --rm -it \
-v $(pwd):/opt \
-w /opt \
--network dockercompose_docker \  # Network name created by docker-compose
dockercompose_php \
composer create-project laravel/laravel application


# -------------------------------------------
# Install predis/predis package with Composer
docker run --rm -i -t \
-v $(pwd)/application:/opt \
-w /opt \
--network dockercompose_docker \
dockercompose_php \
composer require predis/predis


# ---------------------------------
# Install Laravel auth
docker run --rm -i -t \
-v $(pwd)/application:/opt \
-w /opt \
--network dockercompose_docker \
dockercompose_php \
php artisan make:auth


# -----------------------
# Migrate DB
docker run --rm -i -t \
-v $(pwd)/application:/opt \
-w /opt \
--network dockercompose_docker \
dockercompose_php \
php artisan migrate


# ------------------------------------
#  Linking containers without network
#  not recomended

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


