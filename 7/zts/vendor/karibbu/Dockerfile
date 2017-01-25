FROM php:7.0-zts-alpine

MAINTAINER Sébastien HOUZÉ <sebastien.houze@verylastroom.com>

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
    && apk add --no-cache zlib-dev icu-dev curl-dev binutils \
    && docker-php-ext-install \
        intl \
        curl \
        iconv \
        mbstring \
        zip \
        bcmath \
        pcntl \
    && docker-php-source extract \
    && apk add --no-cache git \
    && apk add --no-cache --virtual .phpize-deps $PHPIZE_DEPS \
    && curl -sL https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer --version=1.3.1 \
    && composer global require hirak/prestissimo:0.3.5 \
    && composer global require friendsofphp/php-cs-fixer:2.0.0 \
    && composer global require phpstan/phpstan:0.5.2 \
    && pecl install inotify \
    && pecl install redis-3.1.0 \
    && docker-php-ext-enable --ini-name 100-redis.ini redis \
    && pecl install ds-1.1.6 \
    && docker-php-ext-enable --ini-name 100-ds.ini ds \
    && apk del --no-cache .phpize-deps \
    && apk del --no-cache zlib-dev icu-dev curl-dev binutils \
    && apk add --no-cache icu \
    && docker-php-source delete \
    && apk add --no-cache make \
    && php -m \
    && rm -rf \
        ~/.composer/cache \
        /usr/src \
        /tmp/* \
        /usr/share/doc \
        /usr/share/man \
        /usr/share/doc \
        /var/cache/apk/*

ENV PATH "$PATH:/root/.composer/vendor/bin"

CMD ["php", "-a"]
