FROM php:7-alpine

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
        pdo_mysql \
        intl \
        curl \
        iconv \
        mbstring \
        zip \
        bcmath \
    && apk add --no-cache git \
    && curl -sL https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
    && mkdir -p ~/src \
    && git clone https://github.com/FriendsOfPHP/pickle.git ~/src/pickle \
    && cd ~/src/pickle \
    && git reset --hard 71fd8a97ac6c3f67fbb7f032533ddaa31cb6b662 \
    && composer install --no-dev --optimize-autoloader --prefer-dist \
    && curl -sL http://box-project.github.io/box2/installer.php | php \
    && mv box.json.dist box.json \
    && php -d phar.readonly=0 box.phar build \
    && mv pickle.phar /usr/local/bin/pickle \
    && pear config-set preferred_state beta \
    && printf "\n" | pecl install apcu_bc \
    && docker-php-ext-enable --ini-name 01-apc.ini apcu apc \
    && git clone --branch php7 https://github.com/phpredis/phpredis.git ~/src/phpredis \
    && cd ~/src/phpredis \
    && git reset --hard 2887ad1130aba5aee029a64140d111544e62d56d \
    && sed -i -e 's/2.2.8-devphp7/2.2.7/' php_redis.h \
    && pickle install --defaults ~/src/phpredis \
    && docker-php-ext-enable --ini-name 02-redis.ini redis \
    && rm -rf \
        ~/.composer \
        ~/src/* \
        /tmp/* \
        /usr/share/doc \
        /usr/share/man \
        /usr/share/doc \
        /var/cache/apk/*

CMD ["php", "-a"]
