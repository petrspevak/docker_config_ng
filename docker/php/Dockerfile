FROM php:8.2-apache

RUN echo "ServerName localhost" >> /etc/apache2/apache2.conf

# Set the working directory to /var/www/html
WORKDIR /var/www/html

COPY ./docker/php/php.ini /usr/local/etc/php/conf.d/php.ini

# Install dependencies
RUN apt-get update && \
    apt-get install -y \
        gnupg \
        g++ \
        procps \
        openssl \
        git \
        unzip \
        zlib1g-dev \
        libzip-dev \
        libfreetype6-dev \
        libpng-dev \
        libjpeg-dev \
        libicu-dev  \
        libonig-dev \
        libxml2-dev \
        libxslt1-dev \
        acl \
    && rm -rf /var/lib/apt/lists/* \
    && docker-php-ext-configure intl \
    && docker-php-ext-install intl \
    && docker-php-ext-install pdo pdo_mysql zip xsl gd opcache exif mbstring soap

# Enable Apache modules
RUN a2enmod rewrite

# Change the document root to the public directory
ENV APACHE_DOCUMENT_ROOT /var/www/html/public

RUN sed -ri -e 's!/var/www/html!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/sites-available/*.conf
RUN sed -ri -e 's!/var/www/!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/apache2.conf /etc/apache2/conf-available/*.conf


RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

SHELL ["/bin/bash", "--login", "-c"]
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
RUN nvm install 14 && \
    npm install -g gulp-cli

# Expose port 80 for Apache
EXPOSE 80