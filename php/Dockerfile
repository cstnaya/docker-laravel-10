FROM php:8.1.18RC1-fpm-alpine

# Install php extension
RUN curl -sSL https://github.com/mlocati/docker-php-extension-installer/releases/latest/download/install-php-extensions -o - | sh -s \
    gd xdebug

# Update libraries 

# Install Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Install Node v18
RUN apk add --update nodejs npm

# COPY .env.example .env

# RUN php artisan key:generate

# RUN php artisan jwt:secret