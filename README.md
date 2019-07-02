# Docker Workshop

## Instalación docker CE

- Ubuntu: https://docs.docker.com/install/linux/docker-ce/ubuntu/
- Windows: https://store.docker.com/editions/community/docker-ce-desktop-windows
- Mac: https://store.docker.com/editions/community/docker-ce-desktop-mac
- Legacy:
  - Windows: https://docs.docker.com/toolbox/toolbox_install_windows/
  - Mac: https://docs.docker.com/toolbox/toolbox_install_mac/

# Timeline

> \> tty 1
```bash
$ sudo groupadd docker  	
$ sudo usermod -aG docker $USER
```

> \> tty 1
```bash
$ cd $HOME && mkdir dockerWorkshop && cd dockerWorkshop
```

> \> tty 1
```bash
$ docker run hello-world
```

> \> tty 1
```bash
$ docker ps
$ docker ps -a
```

> \> tty 1
```bash
$ docker run ubuntu
$ docker ps
$ docker ps -a
$ docker images
$ docker run -it ubuntu
root@f709b1535b47:/#
```

> \> tty 2
```bash
$ cd $HOME/dockerWorkshop
$ docker run -it node
```

> \> tty 3
```bash
$ docker ps
```

> \> tty 1
```bash
root@f709b1535b47:/# top
```

> \> tty 3
```bash
$ ps afux
```

> \> tty 1
```bash
root@f709b1535b47:/# Ctrl+C
root@f709b1535b47:/# exit
$ docker run --name=php-fpm php:7.2-fpm
```
 
> \> tty 2
```bash
$ Ctrl+C
$ docker exec php-fpm php -v
```
 
> \> tty 2
```bash
$ docker run -p 8080:80 -it --name=php-apache php:7.2-apache
```
 
> \> tty 3
```bash
$ docker exec -it php-apache bash
root@be8415f43808:/var/www/html# apt update && apt-install vim
root@be8415f43808:/var/www/html# vim index.php
```

```php
<?php 
phpinfo();
```

```bash
$ Ctrl+C
$ exit
```

> \> tty 1
```bash
$ Ctrl+C
$ docker run -it --name=php-fpm php:7.2-fpm
$ docker start php-fpm
```

> \> tty 1
```bash
$ mkdir docker && cd docker
```

> \> tty 1
```bash
$ mkdir php-base && cd php-base
$ touch Dockerfile
$ vim Dockerfile
```

> dockerWorkshop/docker/php-base/Dockerfile

```dockerfile
FROM php:7.2-fpm

WORKDIR "/application"
RUN apt-get update \
    && apt-get install -y libicu-dev libpng-dev libjpeg-dev libpq-dev  \
    && apt-get clean; rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/* \
    && docker-php-ext-configure gd --with-png-dir=/usr --with-jpeg-dir=/usr \
    && docker-php-ext-install intl gd mbstring
RUN apt-get update && apt-get install -y mysql-client \
    && apt-get clean; rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/* \
    && docker-php-ext-install mysqli pdo pdo_mysql
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
```

> \> tty 1
```bash
$ docker build -t "php-fpm-base" .
$ docker run --name="php-fpm-base" php-fpm-base
```

> \> tty 3
```bash
$ docker exec php-fpm-base php -i
$ docker exec php-fpm-base composer -V
```

```bash
$ cd .. && mkdir php-fpm-dev && cd php-fpm-dev
$ touch Dockerfile
$ vim Dockerfile
```

> workshopDocker-phpVigo/docker/php-xdebug/Dockerfile
```dockerfile
FROM php-fpm-base

# install xdebug
RUN pecl install xdebug && docker-php-ext-enable xdebug

RUN apt-get update \
    && apt-get -y install unzip zlib1g-dev \
    && apt-get clean; rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/* \
    && docker-php-ext-install zip
```

> \> tty 3
```bash
$ docker build -t "php-fpm-dev" .
$ docker run --name="php-fpm-dev" php-fpm-dev
```

> \> tty 1
```bash
$ cd .. && mkdir symfony && cd symfony
$ touch Dockerfile
$ vim Dockerfile
```

> symfony/Dockerfile
```dockerfile
FROM php-fpm-dev
 
RUN cd / && composer create-project symfony/skeleton /application
```

> \> tty 1
```bash
$ docker build -t "symfony4" .
$ docker run --name="symfony4" symfony4
```

> \> tty 3
```bash
$ Ctrl+C
$ docker tag php-fpm-base rolandocaldas/workshop-php:7.2 
$ docker login
$ docker push rolandocaldas/workshop-php:7.2
$ docker pull rolandocaldas/workshop-php 
$ docker pull rolandocaldas/workshop-php:7.2
$ docker tag php-fpm-base rolandocaldas/workshop-php 
$ docker push rolandocaldas/workshop-php
$ docker tag php-fpm-dev rolandocaldas/workshop-php:7.2-dev 
$ docker push rolandocaldas/workshop-php:7.2-dev

$ cd $HOME/dockerWorkshop/docker && mkdir workshop-php
$ mv php-base php-fpm-dev ./workshop-php/ && cd workshop-php

$ git init
$ git config --global user.email "rolando.caldas@gmail.com"
$ git config --global user.name "Rolando Caldas"
$ git add .
$ git commit -m "My php docker containers configuration for development"
$ git remote add origin https://github.com/rolando-caldas/cebem-docker-php.git
$ git push -u origin master
```