ARG PHP_VERSION=7.4

FROM php:${PHP_VERSION}-fpm

LABEL maintainer="spilchin.serghei@gmail.com"

ADD https://raw.githubusercontent.com/mlocati/docker-php-extension-installer/master/install-php-extensions /usr/local/bin/

RUN chmod uga+x /usr/local/bin/install-php-extensions && sync && \
    install-php-extensions apcu bcmath mbstring tidy curl gd intl pcntl mcrypt pdo_mysql soap xml zip imagick opcache

RUN apt-get update \
        && apt-get install -y --no-install-recommends \
            sudo \
            git \
            unzip \
            vim \
            supervisor \
            cron \
            software-properties-common \
            gnupg \
            libfreetype6-dev \
            libjpeg62-turbo-dev \
            libpng-dev \
        && curl -sL https://deb.nodesource.com/setup_12.x -o nodesource_setup.sh && bash nodesource_setup.sh \
        && echo "deb http://nginx.org/packages/debian/ buster nginx" | tee -a /etc/apt/sources.list.d/nginx.list \
        && echo "deb-src http://nginx.org/packages/debian/ buster nginx" | tee -a /etc/apt/sources.list.d/nginx.list \
        && curl -L https://nginx.org/keys/nginx_signing.key | apt-key add - \
        && apt-get update \
        && apt-get install -y --no-install-recommends nginx nodejs \
        && rm -rf \
            ./nodesource_setup.sh \
            /etc/nginx/sites-available/default \
            /etc/nginx/sites-enabled/default \
        && mkdir -p \
            /run/php \
            /opt/letsencrypt

RUN curl -sS https://getcomposer.org/installer | php
RUN mv composer.phar /usr/local/bin/composer

WORKDIR /var/www/html

ARG OROCOMMERCE_VERSION=4.1.4

RUN git clone -b ${OROCOMMERCE_VERSION} "https://github.com/oroinc/orocommerce-application" orocommerce
RUN cd orocommerce && composer install --prefer-dist --no-dev

RUN chmod -R 777 /var/www/html/orocommerce/
RUN chown -R www-data:www-data /var/www/html/orocommerce/

ADD ./configs/php/fpm/php.ini /usr/local/etc/php/php.ini
ADD ./configs/nginx/orocommerce.conf /etc/nginx/conf.d/default.conf
ADD ./configs/cron /var/spool/cron/crontabs
ADD ./configs/supervisord/orocommerce.conf /etc/supervisor/conf.d/orocommerce.conf
ADD ./scripts/letsencrypt-auto /opt/letsencrypt/letsencrypt-auto

RUN chmod +x /opt/letsencrypt/letsencrypt-auto; /opt/letsencrypt/letsencrypt-auto  --os-packages-only --install-only --non-interactive

RUN apt-get clean; apt-get autoclean; apt-get autoremove -y -qq

CMD ["nginx", "-g", "daemon off;"]