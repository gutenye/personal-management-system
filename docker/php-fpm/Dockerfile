FROM php:8.3-fpm

RUN apt-get update && apt-get install -y zlib1g-dev libicu-dev g++
RUN docker-php-ext-configure intl
RUN docker-php-ext-install intl
RUN docker-php-ext-install pdo pdo_mysql opcache

RUN apt-get install -y \
        libzip-dev \
        zip \
  && docker-php-ext-install zip

RUN apt-get update -y && apt-get install -y libpng-dev

RUN apt-get update && \
    apt-get install zlib1g-dev tree mariadb-client -y

RUN docker-php-ext-install gd

RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
    && php composer-setup.php --filename=composer \
    && php -r "unlink('composer-setup.php');" \
    && mv composer /usr/local/bin/composer

COPY docker/php-fpm/fpm-pool.conf /usr/local/etc/php-fpm.d/zz-docker.conf
COPY docker/php-fpm/php.ini /usr/local/etc/php/conf.d/php.ini
COPY docker/php-fpm/crontab/pms /etc/cron.d/pms

RUN chown root:root /etc/cron.d/pms && chmod 644 /etc/cron.d/pms

RUN    apt-get update \
    && apt-get -y install gcc g++ make autoconf libc-dev pkg-config \
    && yes '' | pecl install apcu

WORKDIR /application

COPY ./ /application/

RUN pear config-set php_ini /application/docker/php-fpm/php.ini

RUN curl -sLO https://github.com/gordalina/cachetool/releases/latest/download/cachetool.phar --create-dirs -O --output-dir /usr/bin && chmod +x /usr/bin/cachetool.phar

ENTRYPOINT ["sh", "-c", "/usr/local/bin/docker-php-entrypoint && php-fpm -D && ./docker/php-fpm/entrypoint.bash && nano /tmp/keep"]

CMD ["php-fpm"]

EXPOSE 9000

RUN apt-get install -y nano less cron;
