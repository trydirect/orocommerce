FROM centos:7

LABEL maintainer="info@optimum-web.com"

RUN groupadd -r -g 2000 orocommerce; useradd -r -u 2000 -g 2000 -m -c "app user" -d /home/orocommerce -s /bin/bash orocommerce
RUN yum update -y && yum install -y epel-release \
    && yum update -y && yum install -y git supervisor yum-utils \
    && curl -OL http://rpms.remirepo.net/enterprise/remi-release-7.rpm && rpm -Uvh remi-release-7.rpm \
    && yum-config-manager --enable remi-php71 \
    && yum update -y

RUN yum install -y php-fpm php-cli php-pdo php-mysqlnd php-xml php-soap php-gd php-mbstring php-zip php-intl \
    php-mcrypt php-opcache \
    && curl -sS https://getcomposer.org/installer | php \
    && mv composer.phar /usr/bin/composer \
    && yum clean all && rm -rf /var/cache/yum

ADD configs/php/fpm/php.ini /etc/php.ini
ADD configs/php/www.conf /etc/php-fpm.d/www.conf
ADD configs/supervisord/orocommerce.conf /etc/supervisor/supervisord.conf

ENV APP_VERSION=3.1.7
RUN curl -o /usr/src/app.tar.gz -L https://github.com/oroinc/orocommerce/archive/${APP_VERSION}.tar.gz

ENV HOME /home/orocommerce
WORKDIR /home/orocommerce

# speed up composer
RUN /usr/bin/composer global require hirak/prestissimo

RUN chown -R orocommerce:orocommerce /home/orocommerce
USER orocommerce

RUN tar -xf /usr/src/app.tar.gz
WORKDIR /home/orocommerce/orocommerce-${APP_VERSION}
COPY src/composer.json composer1.json
COPY src/composer2.json composer2.json
RUN COMPOSER_MEMORY_LIMIT=8G COMPOSER=composer1.json /usr/bin/composer install --no-dev --verbose --profile --no-suggest
#RUN COMPOSER_MEMORY_LIMIT=-1 /usr/bin/composer config --file=composer2.json && /usr/bin/composer --dry-run --no-dev --verbose --profile --apcu-autoloader --no-suggest install
#RUN COMPOSER_MEMORY_LIMIT=-1 COMPOSER=composer2.json /usr/bin/composer --dry-run --no-dev --verbose --profile --apcu-autoloader --no-suggest install

USER root
CMD ['supervisord']
