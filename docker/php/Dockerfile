FROM php:8.2-apache

RUN echo "ServerName localhost" >> /etc/apache2/apache2.conf

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
        libjpeg62-turbo-dev \
        libicu-dev  \
        libonig-dev \
        libxml2-dev \
        libxslt1-dev \
        acl \
    && rm -rf /var/lib/apt/lists/* \
    && docker-php-ext-configure intl \
    && docker-php-ext-install intl \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install pdo pdo_mysql mysqli zip xsl gd opcache exif mbstring soap

# Enable Apache modules
RUN a2enmod rewrite headers

# Change the document root to the public directory
ENV APACHE_DOCUMENT_ROOT /var/www/html/public
RUN sed -ri -e 's!/var/www/html!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/sites-available/*.conf
RUN sed -ri -e 's!/var/www/!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/apache2.conf /etc/apache2/conf-available/*.conf


RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Change user id to fit host machine user
RUN usermod --non-unique --uid 1000 www-data \
  && groupmod --non-unique --gid 1000 www-data \
  && chown -R 1000:1000 /var/www

USER www-data

# Install NVM for current user (www-data) and install Node.js with gulp-cli
SHELL ["/bin/bash", "--login", "-c"]
RUN touch ~/.bashrc
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
RUN source ~/.bashrc && \
    nvm install 14 && \
    npm install -g gulp-cli

WORKDIR /var/www/html
