# john
FROM php:8.2-fpm

# Install netcat
RUN apt-get update && apt-get install -y netcat

WORKDIR /var/www/html

RUN apt-get update && apt-get install -y \
    libzip-dev \
    zip \
    && docker-php-ext-configure zip \
    && docker-php-ext-install zip pdo_mysql

COPY . .

RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Set the COMPOSER_ALLOW_SUPERUSER environment variable to 1
ENV COMPOSER_ALLOW_SUPERUSER 1

RUN composer install --no-interaction

CMD php artisan migrate:refresh && php artisan serve --host=0.0.0.0 --port=8010