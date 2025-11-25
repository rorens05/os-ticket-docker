FROM php:8.1-apache

# Install required system libs & PHP extensions
RUN apt-get update && apt-get install -y \
    unzip \
    libicu-dev \
    libpng-dev \
    libjpeg-dev \
    libfreetype6-dev \
    libzip-dev \
    libxml2-dev \
  && docker-php-ext-configure gd --with-freetype --with-jpeg \
  && docker-php-ext-install gd mysqli intl zip xml opcache \
  && rm -rf /var/lib/apt/lists/*

# Enable Apache modules and .htaccess overrides (needed by osTicket)
RUN a2enmod rewrite \
  && sed -ri 's/AllowOverride None/AllowOverride All/g' /etc/apache2/apache2.conf

WORKDIR /var/www/html

# osTicket version (change if you want a specific version)
ARG OSTICKET_VERSION=1.18.1

# Download and install osTicket
RUN curl -L -o osticket.zip "https://github.com/osTicket/osTicket/archive/refs/tags/v${OSTICKET_VERSION}.zip" \
  && unzip osticket.zip \
  && if [ -d "osTicket-${OSTICKET_VERSION}/upload" ]; then \
       mv osTicket-${OSTICKET_VERSION}/upload/* .; \
     else \
       mv osTicket-${OSTICKET_VERSION}/* .; \
     fi \
  && rm -rf osTicket-${OSTICKET_VERSION} osticket.zip \
  && chown -R www-data:www-data /var/www/html


# Optional: custom php.ini
COPY php.ini /usr/local/etc/php/php.ini

# Expose Apache port
EXPOSE 80
