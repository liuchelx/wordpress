FROM php:8.0.1-apache

EXPOSE 8080
ENV WWW_ROOT=/var/www/html

USER www-data

WORKDIR ${WWW_ROOT}
RUN curl -f -L https://en-ca.wordpress.org/latest-en_CA.tar.gz | tar -zxvf -
RUN docker-php-ext-install mysqli && docker-php-ext-enable mysqli

HEALTHCHECK --interval=5m --timeout=3s \
  CMD curl --connect-timeout 5 --slient http://127.0.0.1 || exit 1

CMD ["apache2-foreground"]
ENTRYPOINT ["docker-php-entrypoint"]
