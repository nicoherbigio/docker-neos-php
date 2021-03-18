FROM nicoherbigde/php:7.4.15-apache

LABEL maintainer="Nico Herbig <nico@nicoherbig.io>"


# Add additional Apache user and add them to the Apache group (www-data) to ensure that the ID (UID)
# is consistently assigned regardless of which additional dependencies are added.

ENV WWW_USER_ID=10033 WWW_USER=www-user
ENV WWW_GROUP_ID=33 WWW_GROUP=www-data

RUN useradd --gid ${WWW_GROUP} --uid ${WWW_USER_ID} --create-home --shell /bin/bash ${WWW_USER}


# Install additional required software packages for NEOS.

RUN set -ex \
    && apt-get update \
    && apt-get install -y --no-install-recommends git ghostscript graphicsmagick libgraphicsmagick1-dev imagemagick libmagickwand-dev libvips libvips-dev libvips-tools libzip-dev libldap2-dev sudo unzip wget \
    && docker-php-ext-install -j$(nproc) exif intl ldap opcache pdo_mysql soap zip \
    && pecl install gmagick-beta imagick redis vips \
    && docker-php-ext-enable gmagick imagick redis vips \
    && apt-get remove -y libzip-dev \
    && rm -rf /var/lib/apt/lists/*


# Reconfigure Apache and PHP for NEOS.

RUN set -ex \
    && a2enmod expires deflate headers rewrite \
    && { \
        echo 'max_execution_time = 300'; \
        echo 'max_input_vars = 1500'; \
        echo 'memory_limit = 512M'; \
        echo 'post_max_size = 32M'; \
        echo 'upload_max_filesize = 32M'; \
       } | tee /usr/local/etc/php/conf.d/docker-neos.ini
