FROM debian AS php

ARG PHP_VERSION=8.3
ARG NODE_VERSION=20
ARG WWWUSER
ARG WWWGROUP
ARG WORKDIR=/app

ENV DEBIAN_FRONTEND noninteractive

WORKDIR $WORKDIR

RUN apt-get update \
    && apt-get install -y \
    lsb-release \
    curl \
    supervisor

RUN curl -sSLo /usr/share/keyrings/deb.sury.org-php.gpg https://packages.sury.org/php/apt.gpg \
    && sh -c 'echo "deb [signed-by=/usr/share/keyrings/deb.sury.org-php.gpg] https://packages.sury.org/php/ $(lsb_release -sc) main" > /etc/apt/sources.list.d/php.list' \
    && apt-get update

RUN apt-get install -y \
    php$PHP_VERSION-fpm \
    php$PHP_VERSION-curl \
    php$PHP_VERSION-mysql \
    php$PHP_VERSION-bcmath \
    php$PHP_VERSION-mbstring \
    php$PHP_VERSION-xml \
    php$PHP_VERSION-xmlrpc

RUN php -r "readfile('https://getcomposer.org/installer');" | php -- --install-dir=/usr/bin/ --filename=composer

RUN curl -fsSL https://deb.nodesource.com/setup_$NODE_VERSION.x | bash - \
    && apt-get install -y nodejs

FROM php AS proxyfilter

RUN groupadd -g $WWWGROUP www
RUN useradd -u $WWWUSER -g $WWWGROUP -ms /bin/bash www

RUN apt-get clean

COPY conf/www-pool.conf /etc/php/$PHP_VERSION/fpm/pool.d/www.conf
COPY start-container /usr/local/bin/start-container

RUN chmod +x /usr/local/bin/start-container

ENTRYPOINT ["start-container"]
