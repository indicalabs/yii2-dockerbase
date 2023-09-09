FROM php:8-fpm

MAINTAINER venu.narukulla@gmail.com

ENV PATH $PATH:/root/.composer/vendor/bin

# PHP extensions come first, as they are less likely to change between Yii releases
RUN apt-get update \
    && apt-get -y install \
            git \
            g++ \
            libicu-dev \
            libsasl2-dev \
            openssl	\
            libssl-dev \
            libcurl4-openssl-dev \
            libmagickwand-dev \
            libzip-dev \
            zip \
        --no-install-recommends
    # Install PHP extensions
RUN docker-php-ext-install intl \
    && docker-php-ext-install pdo_mysql \
#     && docker-php-ext-install mbstring \
#     && docker-php-ext-install mcrypt \
    && docker-php-ext-install zip \
# for php 5.6    
  #  && pecl install apcu-4.0.10 && echo extension=apcu.so > /usr/local/etc/php/conf.d/apcu.ini \
# for php7
#     && pecl install mcrypt-1.0.4 \
#     && docker-php-ext-enable mcrypt \
    && pecl install apcu-5.1.19 && echo extension=apcu.so > /usr/local/etc/php/conf.d/apcu.ini
# for php 5.6  
 #   && pecl install mongo && docker-php-ext-enable mongo \
# for php7	
RUN pecl install mongodb && docker-php-ext-enable mongodb \
	&& echo "extension=mongodb.so" >> `php --ini | grep "Loaded Configuration" | sed -e "s|.*:\s*||"`
RUN pecl install imagick && docker-php-ext-enable imagick \
    # && git clone https://github.com/Imagick/imagick \
    #     cd imagick \
    #     phpize && ./configure \
    #     make \
    #     make install \
    && echo "extension=imagick.so" >> `php --ini | grep "Loaded Configuration" | sed -e "s|.*:\s*||"`
RUN echo "Venu1 -->".`php -m` \
	&& echo "Venu2 -->".`php --ini | grep "Loaded Configuration" | sed -e "s|.*:\s*||"` \
	&& echo "Venu3 -->".`php --ini` \
    && apt-get purge -y g++ \
    && apt-get autoremove -y \
    && rm -r /var/lib/apt/lists/* \
    # Don't clear our valuable environment vars in PHP
    && echo "\nclear_env = no" >> /usr/local/etc/php-fpm.conf \
    # Fix write permissions with shared folders
    && usermod -u 1000 www-data \
    && apt-get clean
# Imagick Commit to install
# https://github.com/Imagick/imagick
ARG IMAGICK_COMMIT="132a11fd26675db9eb9f0e9a3e2887c161875206"

# RUN echo "**** install imagick php extension from source ****" && \
#         cd /usr/local/src && \
#         git clone https://github.com/Imagick/imagick && \
#         cd imagick && \
#         git checkout ${IMAGICK_COMMIT} && \
#         phpize && \
#         ./configure && \
#         make && \
#         make install && \
#         cd .. && \
#         rm -rf imagick && \
#         docker-php-ext-enable imagick && \
#         echo "extension=imagick.so" >> `php --ini | grep "Loaded Configuration" | sed -e "s|.*:\s*||"`

# RUN printf "\n" | pecl install imagick
# RUN docker-php-ext-enable imagick

# Next composer and global composer package, as their versions may change from time to time
RUN curl -sS https://getcomposer.org/installer | php \
    && mv composer.phar /usr/local/bin/composer.phar \
    && composer.phar self-update --1 \
    && composer.phar global require --no-progress "composer-plugin-api=^1.0" \
    && composer.phar global require --no-progress "fxp/composer-asset-plugin=dev-main" \
#    && composer.phar global require --no-progress "codeception/codeception=2.0.*" \
#    && composer.phar global require --no-progress "codeception/specify=*" \
#    && composer.phar global require --no-progress "codeception/verify=*" \
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
