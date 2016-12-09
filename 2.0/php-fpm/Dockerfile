FROM php:7-fpm

MAINTAINER haertl.mike@gmail.com

ENV PATH $PATH:/root/.composer/vendor/bin

# PHP extensions come first, as they are less likely to change between Yii releases
RUN apt-get update \
    && apt-get -y install \
            git \
            g++ \
            libicu-dev \
            libmcrypt-dev \
            zlib1g-dev \
            libsasl2-dev \
            libcurl4-openssl-dev \
            libssl-dev \
            pkg-config \
            openssl	\
            mongodb \
            libmagickwand-dev \
        --no-install-recommends \

    # Install PHP extensions
    && docker-php-ext-install intl \
    && docker-php-ext-install pdo_mysql \
    && docker-php-ext-install mbstring \
    && docker-php-ext-install mcrypt \
    && docker-php-ext-install zip \

# for php 5.6    
  #  && pecl install apcu-4.0.10 && echo extension=apcu.so > /usr/local/etc/php/conf.d/apcu.ini \
# for php7
    && pecl install apcu-5.1.7 && echo extension=apcu.so > /usr/local/etc/php/conf.d/apcu.ini \
 
# for php 5.6  
 #   && pecl install mongo && docker-php-ext-enable mongo \
# for php7	
	&& pecl install mongodb-1.2.0 && docker-php-ext-enable mongodb \
	#echo "extension=mongodb.so" >> `php --ini | grep "Loaded Configuration" | sed -e "s|.*:\s*||"` \
	
	&& pecl install imagick-3.4.3RC1 && docker-php-ext-enable imagick \    
	&& echo "Venu -->".`php -m` \
    && apt-get purge -y g++ \
    && apt-get autoremove -y \
    && rm -r /var/lib/apt/lists/* \

    # Don't clear our valuable environment vars in PHP
    && echo "\nclear_env = no" >> /usr/local/etc/php-fpm.conf \

    # Fix write permissions with shared folders
    && usermod -u 1000 www-data

# Next composer and global composer package, as their versions may change from time to time
RUN curl -sS https://getcomposer.org/installer | php \
    && mv composer.phar /usr/local/bin/composer.phar \
    && composer.phar global require --no-progress "fxp/composer-asset-plugin:@dev" \
    && composer.phar global require --no-progress "codeception/codeception=2.0.*" \
    && composer.phar global require --no-progress "codeception/specify=*" \
    && composer.phar global require --no-progress "codeception/verify=*" \ 
    && rm -rf ~/.composer/cache

# Nginx example config and composer wrapper
COPY nginx /opt/nginx
COPY composer /usr/local/bin/composer

WORKDIR /var/www/html

# Composer packages are installed outside the app directory /var/www/html.
# This way developers can mount the source code from their host directory
# into /var/www/html and won't end up with an empty vendors/ directory.
#  remove all the below comments after devlopment
#COPY composer.json /var/www/html/
COPY config.json /root/.composer/config.json
# RUN composer install --prefer-dist --no-progress \
#    && rm composer.*
