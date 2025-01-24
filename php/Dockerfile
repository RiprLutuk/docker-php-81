FROM php:8.1.0-fpm
COPY php.ini /usr/local/etc/php/

WORKDIR /var/www/

# Install Dependencies
RUN apt-get update
RUN apt-get install -y libonig-dev \
    libzip-dev \
    libsodium-dev \
    zip \
    unzip \
    mariadb-client \
    nano \
    git \
    cron
RUN docker-php-ext-install pdo_mysql mysqli mbstring sodium zip bcmath

# Install Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Install Cron Jobs
COPY crontab /etc/cron.d/crontab
RUN echo "" >> /etc/cron.d/crontab
RUN chmod 0644 /etc/cron.d/crontab
RUN crontab /etc/cron.d/crontab
RUN touch /var/log/cron.log

# Install Node.js v18.x (current stable)
RUN apt-get update
RUN apt-get install -y ca-certificates curl gnupg
RUN mkdir -p /etc/apt/keyrings
RUN curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg
RUN echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_18.x nodistro main" | tee /etc/apt/sources.list.d/nodesource.list
RUN apt-get update
RUN apt-get install nodejs -y

# Install compatible npm version (npm 8 or npm 9)
RUN npm install -g npm@9

# Install GD & exif
RUN apt-get update && apt-get install -y \
    libfreetype6-dev libjpeg62-turbo-dev libmcrypt-dev libpng-dev \
    && docker-php-ext-configure gd --with-freetype=/usr/include/ --with-jpeg=/usr/include/ \
    && docker-php-ext-install -j$(nproc) gd exif

# Install additional dependencies (curl)
RUN apt-get update && apt-get install -y curl
