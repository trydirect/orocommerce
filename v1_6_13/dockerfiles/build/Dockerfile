FROM ubuntu:16.04

RUN apt-get update -y -qq; apt-get install -y -qq nginx sudo supervisor curl cron nodejs software-properties-common; \
    LC_ALL=C.UTF-8 add-apt-repository ppa:ondrej/php -y; apt-get update -y -qq; \
    apt-get install -y wget php7.1-apcu php7.1-bcmath php7.1-cli php7.1-mbstring php-tidy \
    php7.1-curl php7.1-fpm php7.1-gd php7.1-intl php7.1-mcrypt php7.1-mysql php7.1-soap \
    php7.1-xml php7.1-zip php7.1-imagick libfreetype6-dev libmcrypt-dev libpng12-dev; \
    rm -rf /etc/nginx/sites-available/default;rm -rf /etc/nginx/sites-enabled/default;mkdir /run/php; \
    groupadd -r -g 2000  orocommerce; useradd -r -u 2000 -g 2000 -m -c "app account" -d /home/orocommerce -s /bin/bash orocommerce; \
    mkdir -p /opt/letsencrypt
# COPY ./src/1.6.13.tar.gz /usr/src/app.tar.gz
RUN wget https://github.com/oroinc/orocommerce-application/releases/download/1.6.39/orocommerce-application-1.6.39.tar.gz -O /usr/src/app.tar.gz
RUN  chown orocommerce:orocommerce  /usr/src/app.tar.gz
ADD ./configs/php/cli/php.ini /etc/php/7.1/cli/php.ini
ADD ./configs/php/fpm/php.ini /etc/php/7.1/fpm/php.ini
ADD ./configs/php/www.conf /etc/php/7.1/fpm/pool.d/www.conf
ADD configs/supervisord/orocommerce.conf /etc/supervisor/conf.d/orocommerce.conf
ADD ./scripts/letsencrypt-auto /opt/letsencrypt/letsencrypt-auto
RUN chmod +x /opt/letsencrypt/letsencrypt-auto; /opt/letsencrypt/letsencrypt-auto  --os-packages-only --install-only --non-interactive

RUN apt-get clean; apt-get autoclean; apt-get autoremove -y -qq
RUN curl -sS https://getcomposer.org/installer | php
RUN mv composer.phar /usr/local/bin/composer

USER orocommerce
ENV HOME /home/orocommerce
WORKDIR /home/orocommerce
