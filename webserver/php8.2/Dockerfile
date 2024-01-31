FROM ubuntu:22.04

LABEL maintainer="Cristian Enache"

WORKDIR /var/www/html

RUN DEBIAN_FRONTEND=noninteractive TZ=UTC

RUN apt-get update -y && apt-get upgrade -y

RUN apt-get install -y systemctl \
   && apt-get install -y iputils-ping \
   && apt-get install -y nginx \
   && apt-get install -y sudo \
   && apt-get install -y gnupg gosu curl ca-certificates zip unzip git supervisor sqlite3 libcap2-bin \
   && mkdir -p ~/.gnupg \
   && chmod 600 ~/.gnupg \
   && echo "disable-ipv6" >> ~/.gnupg/dirmngr.conf \
   && curl -sL https://deb.nodesource.com/setup_18.x | bash - \
   && apt-get install -y nodejs \
   && apt-get install -y wget \
   && apt-get install cron \
   && apt-get install nano \
   && apt-get -y autoremove \
   && apt-get clean \
   && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Make installer noninterractive
RUN echo 'debconf debconf/frontend select Noninteractive' | debconf-set-selections

#PHP8.2-FPM needs this directory
RUN DEBIAN_FRONTEND=noninteractive mkdir /run/php
RUN DEBIAN_FRONTEND=noninteractive apt -y update
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y software-properties-common
RUN DEBIAN_FRONTEND=noninteractive add-apt-repository ppa:ondrej/php
RUN DEBIAN_FRONTEND=noninteractive apt -y update
RUN DEBIAN_FRONTEND=noninteractive apt install -y php8.2
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y php8.2-cli php8.2-common php8.2-mysql php8.2-zip php8.2-gd php8.2-mbstring php8.2-curl php8.2-xml php8.2-bcmath
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y php8.2-curl php8.2-zip php8.2-simplexml php8.2-fpm php8.2-intl php8.2-redis php8.2-sqlite3

# This installs php8.3 for some reason
# RUN apt-get install -y php-xdebug

RUN DEBIAN_FRONTEND=noninteractive php -r "readfile('http://getcomposer.org/installer');" | php -- --install-dir=/usr/bin/ --filename=composer
        
RUN npm install -g laravel-echo-server

# Turn off daemon mode, so we can control nginx via supervisor
# supervisord can only handle processes in foreground. The default for nginx is running in background as daemon. To ensure that your nginx is running with supervisord you have to set 'daemon off' in your nginx.conf
RUN echo "daemon off;" >> /etc/nginx/nginx.conf

# DELETE DEFAULT NGINX SITE & REPLACE WITH OUR ONE
RUN rm -rf /etc/nginx/sites-available/default
COPY ./nginx/default.conf /etc/nginx/sites-available/default
COPY ./supervisord.conf /etc/supervisor/conf.d/supervisord.conf
#COPY ./php.ini /etc/php/8.2/fpm/conf.d/custom-php.ini

#Add test alias
RUN echo "alias test='APP_ENV=testing php artisan test'" >> ~/.bashrc

# RUN php8.2-fpm as www-data / 1000
#RUN sed -i 's/user = www-data/user = 1000/g' /etc/php/8.2/fpm/pool.d/www.conf
#RUN sed -i 's/group = www-data/group = 1000/g' /etc/php/8.2/fpm/pool.d/www.conf

# Change ownership of /var/www
RUN usermod -u 1000 www-data
RUN chown -R 1000:1000 /var/www

# Slack permissions for storage folder
#RUN chmod -R 775 /var/www/html/storage/

CMD ["/usr/bin/supervisord", "-n"]
