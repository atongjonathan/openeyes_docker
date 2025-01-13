# Use the official PHP Apache image
FROM php:7.4-apache

# Install necessary system packages and PHP extensions
RUN apt-get update && apt-get install -y \
    git \
    curl \
    unzip \
    mariadb-client \
    libpng-dev \
    libjpeg-dev \
    libfreetype6-dev \
    libxml2-dev \
    zip \
    libzip-dev \
    npm \
    nodejs \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install gd soap sockets mysqli pdo pdo_mysql zip \
    && a2dismod mpm_event \
    && a2enmod mpm_prefork rewrite \
    && apt-get clean

# Copy Apache virtual host configuration
COPY ./config/000-default.conf /etc/apache2/sites-available/000-default.conf

# Set working directory
WORKDIR /var/www/openeyes

# Install Composer globally
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Clone OpenEyes repository and submodules
RUN git clone https://github.com/AppertaFoundation/openeyes.git /var/www/openeyes && \
    cd /var/www/openeyes/protected && git submodule init && git submodule update

RUN npm install --unsafe-perm && npm install -g sortablejs 

# Set permissions for the application
RUN chown -R www-data:www-data /var/www/openeyes && chmod -R 755 /var/www/openeyes

# Create necessary directories and set permissions
RUN mkdir -p /var/www/openeyes/cache \
    /var/www/openeyes/protected/cache \
    /var/www/openeyes/assets \
    && chmod -R 755 /var/www/openeyes/assets


# Clone EyeDraw module
RUN git clone https://github.com/AppertaFoundation/eyedraw.git /var/www/openeyes/protected/modules/eyedraw && \
    cd /var/www/openeyes/protected/modules/eyedraw && git checkout v6.7.19

# Install Node.js and npm
RUN curl -fsSL https://deb.nodesource.com/setup_16.x | bash - 
    
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash \
    && export NVM_DIR="$HOME/.nvm" \
    && [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" \
    && [ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion" \
    && nvm install 16.20.2 \
    && nvm use 16.20.2 \
    && nvm alias default 16.20.2


# Copy db.conf to the container
COPY config/db.conf /etc/openeyes/db.conf

# Ensure proper permissions for db.conf
# RUN chmod 600 /etc/openeyes/db.conf && \ chown www-data:www-data /etc/openeyes/db.conf


# Set up local configuration file
COPY  common.php /var/www/openeyes/protected/config/local/common.php

# Install Composer dependencies
RUN composer install --working-dir=/var/www/openeyes

# Run database migrations
# RUN /usr/local/bin/wait-for-db.sh db php yiic migrate --interactive=0 && /usr/local/bin/wait-for-db.sh db php yiic migratemodules --interactive=0

RUN chmod -R 777 /var/www/openeyes/assets 

EXPOSE 80

CMD ["/bin/sh", "-c", "cd /var/www/openeyes/protected && php yiic migrate --interactive=0 && php yiic migratemodules --interactive=0 && apache2-foreground"]


