FROM library/php:7-apache

WORKDIR /var/
RUN chown www-data:www-data www

# php runs as www-data - do everything as this user
# so there are no permission problems
USER www-data

WORKDIR /var/www/
RUN rm -R html

# download craft
RUN curl -L -o /tmp/craft.tar.gz http://craftcms.com/latest.tar.gz?accept_license=yes \
  && tar -zxf /tmp/craft.tar.gz 

# configure craft
RUN mv public html
RUN mv html/htaccess html/.htaccess
RUN sed -i.bak "s/'server' => 'localhost'/'server' => \$_ENV['DB_HOST']/" craft/config/db.php \
  && sed -i.bak "s/'database' => ''/'database' => \$_ENV['DB_NAME']/" craft/config/db.php \
  && sed -i.bak "s/'password' => ''/'password' => \$_ENV['DB_PASSWORD']/" craft/config/db.php \
  && sed -i.bak "s/'user' => 'root'/'user' => \$_ENV['DB_USER']/" craft/config/db.php


# set permissions
RUN chmod 744 craft/app craft/config craft/storage


# --- lastly ---
# switch back to root to allow binding on port 80
USER root