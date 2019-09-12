FROM centos:7

RUN yum update -y && yum install -y epel-release \
    && yum update -y && yum install -y nginx wget git nodejs supervisor yum-utils \
    && wget http://rpms.remirepo.net/enterprise/remi-release-7.rpm && rpm -Uvh remi-release-7.rpm \
    && yum-config-manager --enable remi-php71 \
    && yum update -y

RUN yum install -y php-fpm php-cli php-pdo php-mysqlnd php-xml php-soap php-gd php-mbstring php-zip php-pecl-zip php-intl \
    php-mcrypt php-opcache php-mysqli \
    && php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" && php composer-setup.php \
    && php -r "unlink('composer-setup.php');" \
    && mv composer.phar /usr/bin/composer


# orocommerce v1.6.13
WORKDIR /usr/src
RUN git clone -b 1.6.13 https://github.com/orocommerce/orocommerce-application-de.git
WORKDIR /usr/src/orocommerce-application-de
RUN curl -s https://getcomposer.org/installer | php \
    && composer install --prefer-dist --no-dev ; exit 0 \
    && echo "_*_ Vendor collected! _*_"

WORKDIR /usr/src
RUN tar -zcvf app.tar.gz orocommerce-application-de

RUN groupadd -r -g 20005  orocommerce; useradd -r -u 20005 -g 20005 -m -c "app user" -d /home/orocommerce -s /bin/bash orocommerce \
    && mkdir -p /var/log/supervisord \
    && mkdir -p /run/php-fpm \
    && mkdir -p /var/log/php-fpm \
    && chown -R orocommerce. /var/log/php-fpm \
    && touch /run/php-fpm/php-fpm.pid \
    && chown orocommerce. /run/php-fpm/php-fpm.pid \
    && touch /var/log/supervisord/supervisord.log \
    && touch /var/run/supervisord.pid \
    && chown orocommerce. /var/run/supervisord.pid \
    && chmod 777 /var/run/supervisord.pid

RUN chown orocommerce:orocommerce  /usr/src/app.tar.gz
ADD configs/php/fpm/php.ini /etc/php.ini
ADD configs/php/www.conf /etc/php-fpm.d/www.conf
ADD configs/supervisord/orocommerce.conf /etc/supervisor/conf.d/orocommerce.conf
ADD configs/supervisord/supervisord.conf /etc/supervisor/supervisord.conf
ADD scripts/letsencrypt-auto /opt/letsencrypt/letsencrypt-auto
RUN chmod +x /opt/letsencrypt/letsencrypt-auto; /opt/letsencrypt/letsencrypt-auto  --os-packages-only --install-only --non-interactive

WORKDIR /home/orocommerce
USER orocommerce
ENV HOME /home/orocommerce
EXPOSE 9000
