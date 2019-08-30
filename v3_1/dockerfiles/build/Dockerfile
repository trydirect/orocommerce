FROM centos:7

RUN yum update -y && yum install -y epel-release \
    && yum update -y && yum install -y nginx wget git nodejs supervisor yum-utils \
    && wget http://rpms.remirepo.net/enterprise/remi-release-7.rpm && rpm -Uvh remi-release-7.rpm \
    && yum-config-manager --enable remi-php71 \
    && yum update -y

RUN yum install -y php-fpm php-cli php-pdo php-mysqlnd php-xml php-soap php-gd php-mbstring php-zip php-intl \
    php-mcrypt php-opcache \
    && php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" && php composer-setup.php \
    && php -r "unlink('composer-setup.php');" \
    && mv composer.phar /usr/bin/composer


RUN cd /usr/src && curl -L -o app.tar.gz https://github.com/oroinc/orocommerce/archive/3.1.6.tar.gz

RUN groupadd -r -g 20005  orocommerce; useradd -r -u 20005 -g 20005 -m -c "app user" -d /home/orocommerce -s /bin/bash orocommerce \
    && mkdir -p /var/log/supervisord \
    && touch /var/log/supervisord/supervisord.log

RUN chown orocommerce:orocommerce  /usr/src/app.tar.gz
ADD configs/php/fpm/php.ini /etc/php.ini
ADD configs/php/www.conf /etc/php-fpm.d/www.conf
ADD configs/supervisord/orocommerce.conf /etc/supervisor/conf.d/orocommerce.conf
ADD configs/supervisord/supervisord.conf /etc/supervisor/supervisord.conf
ADD scripts/letsencrypt-auto /opt/letsencrypt/letsencrypt-auto
RUN chmod +x /opt/letsencrypt/letsencrypt-auto; /opt/letsencrypt/letsencrypt-auto  --os-packages-only --install-only --non-interactive

USER orocommerce
ENV HOME /home/orocommerce
WORKDIR /home/orocommerce
