FROM php:7.1.3-zts-alpine

ENV COMPOSER_VERSION=1.3.2 \
    PRESTISSIMO_VERSION=0.3.5 \
    PHPCSFIXER_VERSION=2.1.2 \
    PHPSTAN_VERSION=0.6.4 \
    APCUBC_VERSION=1.0.3 \
    REDIS_VERSION=3.1.1 \
    RAPHF_VERSION=2.0.0 \
    DS_VERSION=1.1.7 \
    AMQP_VERSION=1.8.0 \
    SWOOLE_VERSION=2.0.7

MAINTAINER Sébastien HOUZÉ <sebastien.houze@verylastroom.com>

RUN printf "\
date.timezone=\"UTC\"\n\
request_order=GP\n\
expose_php=Off\n\
enable_dl=Off\n\
short_open_tag=Off\n\
zend.assertions=-1\n\
" > /usr/local/etc/php/php.ini \
    && docker-php-ext-enable --ini-name 00-opcache.ini opcache \
    && sed -i -e 's/extension=/zend_extension=/' /usr/local/etc/php/conf.d/00-opcache.ini \
    && printf "\
opcache.max_accelerated_files=16229\n\
opcache.enable_file_override=1\n\
opcache.log_verbosity_level=0\n\
opcache.fast_shutdown=1\n\
" >> /usr/local/etc/php/conf.d/00-opcache.ini \
    && apk add --no-cache zlib-dev icu-dev curl-dev binutils linux-headers \
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
    && curl -sL https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer --version=$COMPOSER_VERSION \
    && composer global require hirak/prestissimo:$PRESTISSIMO_VERSION \
    && composer global require friendsofphp/php-cs-fixer:$PHPCSFIXER_VERSION \
    && composer global require phpstan/phpstan:$PHPSTAN_VERSION \
    && printf "\n" | pecl install apcu_bc-$APCUBC_VERSION \
    && docker-php-ext-enable --ini-name 100-apc.ini apcu apc \
    && pecl install redis-$REDIS_VERSION \
    && docker-php-ext-enable --ini-name 100-redis.ini redis \
    && pecl install raphf-$RAPHF_VERSION \
    && docker-php-ext-enable --ini-name 100-raphf.ini raphf \
    && echo -e "@edge http://nl.alpinelinux.org/alpine/edge/main\n@edge http://nl.alpinelinux.org/alpine/edge/testing" >> /etc/apk/repositories \
    && apk update --no-cache \
    && apk add --no-cache rabbitmq-c-dev@edge curl@edge libssh2@edge curl-dev@edge \
    && pecl install amqp-$AMQP_VERSION \
    && docker-php-ext-enable --ini-name 100-amqp.ini amqp \
    && pecl install ds-$DS_VERSION \
    && docker-php-ext-enable --ini-name 100-ds.ini ds \
    && pecl install swoole-$SWOOLE_VERSION \
    && docker-php-ext-enable --ini-name 100-swoole.ini swoole \
    && apk del --no-cache .phpize-deps \
    && apk del --no-cache zlib-dev icu-dev curl-dev binutils linux-headers \
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
