FROM php:8.0-cli

RUN pecl install xdebug \
    && docker-php-ext-enable xdebug

RUN echo "zend_extension=xdebug.so \n\r \
xdebug.client_host=172.17.0.1 \n\r \
xdebug.client_port=9000\n\r \
xdebug.log=/app/xdebug.log\n\r \
xdebug.mode=debug \n\r \
xdebug.start_with_request=yes \n\r \
xdebug.discover_client_host=0" >> /usr/local/etc/php/conf.d/ext-xdebug.ini
