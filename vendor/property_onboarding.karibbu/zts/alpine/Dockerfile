FROM php:7-zts-alpine

MAINTAINER Sébastien HOUZÉ <sebastien.houze@verylastroom.com>

RUN printf "\
date.timezone=\"UTC\"\n\
request_order=GP\n\
expose_php=Off\n\
enable_dl=Off\n\
short_open_tag=Off\n\
" > /usr/local/etc/php/php.ini \
    && docker-php-ext-enable --ini-name 00-opcache.ini opcache \
    && printf "\
opcache.max_accelerated_files=16229\n\
opcache.enable_file_override=1\n\
opcache.log_verbosity_level=0\n\
opcache.fast_shutdown=1\n\
" >> /usr/local/etc/php/conf.d/00-opcache.ini \
    && apk add --no-cache zlib-dev icu-dev \
    && docker-php-ext-install \
        intl \
        curl \
        iconv \
        mbstring \
        zip \
        bcmath \
    && apk add --no-cache git \
    && curl -sL https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
    && pear config-set preferred_state beta \
    && printf "\n" | pecl install apcu_bc \
    && docker-php-ext-enable --ini-name 01-apc.ini apcu apc \
    && rm -rf \
        ~/.composer \
        ~/src/* \
        /tmp/* \
        /usr/share/doc \
        /usr/share/man \
        /usr/share/doc \
        /var/cache/apk/*

CMD ["php", "-a"]
