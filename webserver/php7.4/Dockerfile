FROM ubuntu:20.04

LABEL maintainer="Cristian Enache"

WORKDIR /var/www/html

ENV TZ=UTC

RUN apt-get update \
    && apt-get install -y iputils-ping \
    && apt-get install -y nginx \
    && apt-get install -y gnupg gosu curl ca-certificates zip unzip git supervisor sqlite3 libcap2-bin libpng-dev python2 \
    && mkdir -p ~/.gnupg \
    && chmod 600 ~/.gnupg \
    && echo "disable-ipv6" >> ~/.gnupg/dirmngr.conf \
    && apt-key adv --homedir ~/.gnupg --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys E5267A6C \
    && apt-key adv --homedir ~/.gnupg --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys C300EE8C \
    && echo "deb http://ppa.launchpad.net/ondrej/php/ubuntu focal main" > /etc/apt/sources.list.d/ppa_ondrej_php.list \
    && apt-get update \
    && apt-get install -y php7.4-cli php7.4-dev \
       php7.4-pgsql php7.4-sqlite3 php7.4-gd \
       php7.4-curl php7.4-memcached \
       php7.4-imap php7.4-mysql php7.4-mbstring \
       php7.4-xml php7.4-zip php7.4-bcmath php7.4-soap \
       php7.4-intl php7.4-readline \
       php7.4-msgpack php7.4-igbinary php7.4-ldap \
       php7.4-redis \
       php7.4-fpm \
       nano \
    && pecl install xdebug-3.0.0 \
    && php -r "readfile('http://getcomposer.org/installer');" | php -- --install-dir=/usr/bin/ --filename=composer \
    && curl -sL https://deb.nodesource.com/setup_15.x | bash - \
    && apt-get install -y nodejs \
    && apt-get install -y wget \
    && apt-get -y autoremove \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN apt-get update
RUN npm install -g laravel-echo-server

# Turn off daemon mode, so we can control nginx via supervisor
# supervisord can only handle processes in foreground. The default for nginx is running in background as daemon. To ensure that your nginx is running with supervisord you have to set 'daemon off' in your nginx.conf
RUN echo "daemon off;" >> /etc/nginx/nginx.conf

# DELETE DEFAULT NGINX SITE & REPLACE WITH OUR ONE
RUN rm -rf /etc/nginx/sites-available/default

COPY ./nginx/default.conf /etc/nginx/sites-available/default

COPY ./supervisord.conf /etc/supervisor/conf.d/supervisord.conf

COPY ./php.ini /etc/php/7.4/fpm/conf.d/custom-php.ini

# RUN php7.4-fpm as www-data / 1000
RUN sed -i 's/user = www-data/user = 1000/g' /etc/php/7.4/fpm/pool.d/www.conf
RUN sed -i 's/group = www-data/group = 1000/g' /etc/php/7.4/fpm/pool.d/www.conf

# Change ownership of /var/www
RUN usermod -u 1000 www-data
RUN chown -R 1000:1000 /var/www

# Slack permissions for storage folder
#RUN chmod -R 775 /var/www/html/storage/

CMD ["/usr/bin/supervisord", "-n"]
