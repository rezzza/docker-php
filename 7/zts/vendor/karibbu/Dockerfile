FROM php:7-zts-alpine

MAINTAINER Sébastien HOUZÉ <sebastien.houze@verylastroom.com>

ENV LIB_RDKAFKA_VERSION=0.9.1

RUN printf "\
date.timezone=\"UTC\"\n\
request_order=GP\n\
expose_php=Off\n\
enable_dl=Off\n\
short_open_tag=Off\n\
" > /usr/local/etc/php/php.ini \
    && docker-php-ext-enable --ini-name 00-opcache.ini opcache \
    && sed -i -e 's/extension=/zend_extension=/' /usr/local/etc/php/conf.d/00-opcache.ini \
    && printf "\
opcache.max_accelerated_files=16229\n\
opcache.enable_file_override=1\n\
opcache.log_verbosity_level=0\n\
opcache.fast_shutdown=1\n\
" >> /usr/local/etc/php/conf.d/00-opcache.ini \
    && apk add --no-cache zlib-dev icu-dev libev \
    && docker-php-ext-install \
        intl \
        curl \
        iconv \
        mbstring \
        zip \
        bcmath \
        pcntl \
    && apk add --no-cache git \
    && apk add --no-cache --virtual .phpize-deps $PHPIZE_DEPS \
    && curl -sL https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
    && composer global require hirak/prestissimo \
    && printf "\n" | pecl install ev-beta \
    && pecl install inotify \
    && docker-php-ext-enable --ini-name 100-inotify.ini inotify.so \
    && apk add --no-cache --virtual .librdkafka-build-deps bash python \
    && cd /tmp \
    && curl -SL https://github.com/edenhill/librdkafka/archive/confluent-debian-${LIB_RDKAFKA_VERSION}.tar.gz -o librdkafka-${LIB_RDKAFKA_VERSION}.tgz \
    && tar xvzpf librdkafka-${LIB_RDKAFKA_VERSION}.tgz \
    && cd librdkafka-confluent-debian-${LIB_RDKAFKA_VERSION} \
    && ./configure \
    && make -j"$(getconf _NPROCESSORS_ONLN)" \
    && make install \
    && git clone --depth=1 -b php7 https://github.com/arnaud-lb/php-rdkafka.git /tmp/php-rdkafka \
    && cd /tmp/php-rdkafka \
    && phpize \
    && ./configure \
    && make -j"$(getconf _NPROCESSORS_ONLN)" \
    && make install \
    && docker-php-ext-enable rdkafka \
    && apk del --no-cache .librdkafka-build-deps \
    && git clone --depth=1 https://github.com/php-ds/extension /tmp/php-ds \
    && cd /tmp/php-ds \
    && phpize \
    && ./configure \
    && make -j"$(getconf _NPROCESSORS_ONLN)" \
    && make install \
    && docker-php-ext-enable ds \    
    && apk del --no-cache .phpize-deps \
    && apk del --no-cache zlib-dev icu-dev \
    && apk add --no-cache icu \
    && php -m \
    && rm -rf \
        ~/.composer/cache \
        /usr/src \
        /tmp/* \
        /usr/share/doc \
        /usr/share/man \
        /usr/share/doc \
        /var/cache/apk/*
    && mkdir -p /app /var/app \
    && chown daemon /app /var/app

USER daemon

WORKDIR /app

CMD ["php", "-a"]
