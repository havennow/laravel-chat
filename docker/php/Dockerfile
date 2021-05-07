FROM composer:2 as composer

FROM php:8.0-cli
# Fix debconf warnings upon build
ARG DEBIAN_FRONTEND=noninteractive
ENV NODE_VERSION 12.13.0

# Install selected extensions and other stuff
RUN apt-get update \
    && apt-get install -y git bash wget curl gnupg libbz2-dev zlib1g-dev zip libzip-dev libpng-dev libicu-dev libtidy-dev libssl-dev libcurl4-openssl-dev

RUN docker-php-ext-install opcache pdo pdo_mysql bz2 gd intl tokenizer zip
RUN pecl install redis && docker-php-ext-enable redis
RUN docker-php-ext-install sockets
RUN pecl install --configureoptions 'enable-sockets="yes" enable-openssl="yes" enable-http2="yes" enable-mysqlnd="yes" enable-swoole-json="no" enable-swoole-curl="yes"' swoole
RUN docker-php-ext-enable swoole
RUN pecl install inotify
RUN docker-php-ext-enable inotify
RUN docker-php-ext-enable opcache
# Install Nodejs and Yarn
RUN wget https://deb.nodesource.com/setup_12.x  | bash -
RUN apt-get -y install nodejs npm
RUN npm install yarn --global
# Install supervisord
RUN apt-get install -y supervisor
COPY worker.conf /etc/supervisor/conf.d/worker.conf
COPY supervisord.conf /etc/supervisor/supervisord.conf

COPY --from=composer /usr/bin/composer /usr/bin/composer

WORKDIR "/application"

CMD ["/usr/bin/supervisord", "-n"]
