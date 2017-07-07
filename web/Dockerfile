FROM library/php:7-apache

# base packages
RUN apt update \
  && apt install -y zip rsync

# imagemagick
RUN apt install -y libmagickwand-6.q16-dev --no-install-recommends \
 && ln -s /usr/lib/x86_64-linux-gnu/ImageMagick-6.8.9/bin-Q16/MagickWand-config /usr/bin \
 && pecl install imagick \
 && echo "extension=imagick.so" > /usr/local/etc/php/conf.d/ext-imagick.ini

# mysql
RUN docker-php-ext-install pdo pdo_mysql zip

# enable mod rewrite
RUN a2enmod rewrite

WORKDIR /tmp/
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
  && php -r "if (hash_file('SHA384', 'composer-setup.php') === '669656bab3166a7aff8a7506b8cb2d1c292f042046c5a994c43155c0be6190fa0355160742ab2e1c88d40d5be660b410') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;" \
  && php composer-setup.php \
  && php -r "unlink('composer-setup.php');" \
  && mv composer.phar /usr/local/bin/composer

WORKDIR /var/
RUN chown www-data:www-data www

# php runs as www-data - do everything as this user
# so there are no permission problems
USER www-data

WORKDIR /var/www/
RUN rm -R html

RUN composer create-project craftcms/craft /tmp/craft -s beta

RUN rsync -au --delete-after /tmp/craft/ /var/www/


RUN mv web html


# --- lastly ---
# switch back to root to allow binding on port 80
USER root



