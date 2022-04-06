FROM alpine:3.15
LABEL maintainer="marcelo.de.lm@gmail.com"

ARG uid
ENV user=docker
ENV working_dir=/var/www

# Setup document root
WORKDIR $working_dir

# Speeding up installation by skipping documentation installation
RUN mkdir -p /usr/local/etc \
  && { \
    echo 'install: --no-document'; \
    echo 'update: --no-document'; \
  } >> /etc/gemrc

# Install utilities
RUN apk update
RUN apk add --no-cache --update \
    nginx \
    supervisor \
    curl \
    tzdata \
    htop \
    mysql-client \
    nodejs \
    npm \
    yarn \
    zip

# Install packages and remove default server definition
RUN apk add --no-cache \
    php8-ctype \
    php8-curl \
    php8-dom \
    php8-exif \
    php8-fileinfo \
    php8-fpm \
    php8-gd \
    php8-iconv \
    php8-intl \
    php8-mbstring \
    php8-mysqli \
    php8-opcache \
    php8-openssl \
    php8-pecl-imagick \
    php8-pecl-redis \
    php8-phar \
    php8-session \
    php8-simplexml \
    php8-soap \
    php8-xml \
    php8-xmlreader \
    php8-zip \
    php8-zlib \
    php8-pdo \
    php8-xmlwriter \
    php8-tokenizer \
    php8-pdo_mysql \
    php8-pdo_sqlite \
    php8-pecl-xdebug

# Create symlink so programs depending on `php` still function
RUN ln -s /usr/bin/php8 /usr/bin/php

# Install PHP tools
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" && php composer-setup.php --install-dir=/usr/local/bin --filename=composer

# Configure PHP-FPM
COPY .docker/config/fpm-pool.conf /etc/php8/php-fpm.d/www.conf
COPY .docker/config/php.ini /etc/php8/conf.d/custom.ini

# Configuração XDEBUG
COPY .docker/config/docker-php-ext-xdebug.ini /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini

# Configure nginx
COPY .docker/config/nginx.conf /etc/nginx/nginx.conf

# Configure supervisord
COPY .docker/config/supervisord.conf /etc/supervisor/conf.d/supervisord.conf

# Creating user and group configuration
RUN set -x && \
    adduser -D -u $uid -g $user -s /bin/sh $user && \
    addgroup $user $user && \
    addgroup $user nginx && \
    chown -R $uid:$uid $working_dir

# Make sure files/folders needed by the processes are accessable when they run under the nobody user
RUN chown -R $uid:$uid $working_dir /run /var/lib/nginx /var/log/nginx

# Switch to use a non-root user from here on
USER $user

# Let supervisord start nginx & php-fpm
CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]

# Configure a healthcheck to validate that everything is up&running
HEALTHCHECK --timeout=10s CMD curl --silent --fail http://127.0.0.1:8080/fpm-ping