FROM php:8.4-apache

# Accept build arguments for user ID and group ID
ARG USER_ID=1000
ARG GROUP_ID=1000

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
        libwebp-dev \
        libavif-dev \
        libicu-dev  \
        libonig-dev \
        libxml2-dev \
        libxslt1-dev \
        acl \
    && rm -rf /var/lib/apt/lists/* \
    && docker-php-ext-configure intl \
    && docker-php-ext-install intl \
    && docker-php-ext-configure gd --with-freetype --with-jpeg --with-webp --with-avif \
    && docker-php-ext-install pdo pdo_mysql mysqli zip xsl gd opcache exif mbstring soap

# Enable Apache modules
RUN a2enmod rewrite headers

# Change the document root to the public directory
ENV APACHE_DOCUMENT_ROOT /var/www/html/public
RUN sed -ri -e 's!/var/www/html!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/sites-available/*.conf
RUN sed -ri -e 's!/var/www/!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/apache2.conf /etc/apache2/conf-available/*.conf

RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Create user and group with dynamic IDs, handle case where user/group might already exist
RUN groupmod --non-unique --gid ${GROUP_ID} www-data || true && \
    usermod --non-unique --uid ${USER_ID} --gid ${GROUP_ID} www-data || true && \
    chown -R ${USER_ID}:${GROUP_ID} /var/www

USER www-data

SHELL ["/bin/bash", "--login", "-c"]
RUN touch ~/.bashrc
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash
RUN source ~/.bashrc && \
    nvm install 14 && \
    npm install -g gulp-cli

WORKDIR /var/www/html
