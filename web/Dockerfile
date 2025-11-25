FROM php:8.1-apache

# Base tools (unzip for osTicket, curl to fetch installer + zip)
RUN apt-get update && apt-get install -y \
    unzip \
    curl \
 && rm -rf /var/lib/apt/lists/*

# Easy PHP extension installer (handles all apt deps for us)
ADD https://github.com/mlocati/docker-php-extension-installer/releases/latest/download/install-php-extensions /usr/local/bin/

RUN chmod +x /usr/local/bin/install-php-extensions \
    # Install all needed PHP extensions:
    # - gd, mysqli, intl, zip, xml, opcache: core osTicket requirements
    # - imap: for email fetching
    # - apcu: for performance
    && install-php-extensions gd mysqli intl zip xml opcache imap apcu

# Enable Apache mod_rewrite and allow .htaccess overrides (needed by osTicket)
RUN a2enmod rewrite \
  && sed -ri 's/AllowOverride None/AllowOverride All/g' /etc/apache2/apache2.conf

WORKDIR /var/www/html

# osTicket version (you can bump this as needed)
ARG OSTICKET_VERSION=1.18.1

# Download and install osTicket
# Works whether the archive has /upload or directly the app in root
RUN curl -L -o osticket.zip "https://github.com/osTicket/osTicket/archive/refs/tags/v${OSTICKET_VERSION}.zip" \
  && unzip osticket.zip \
  && if [ -d "osTicket-${OSTICKET_VERSION}/upload" ]; then \
       mv osTicket-${OSTICKET_VERSION}/upload/* .; \
     else \
       mv osTicket-${OSTICKET_VERSION}/* .; \
     fi \
  && rm -rf osTicket-${OSTICKET_VERSION} osticket.zip \
  && chown -R www-data:www-data /var/www/html

# Optional: your custom PHP config
# (comment this out if you don't have php.ini yet)
COPY php.ini /usr/local/etc/php/php.ini

EXPOSE 80
