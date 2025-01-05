FROM php:8.1.0-fpm
COPY php.ini /usr/local/etc/php/

WORKDIR /var/www/

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

# install Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# COPY ssh/ ~/.ssh/

COPY crontab /etc/cron.d/crontab

# Tambahkan baris baru (newline) secara eksplisit ke file crontab untuk menghindari error
RUN echo "" >> /etc/cron.d/crontab

# Pastikan hak akses file crontab sudah benar
RUN chmod 0644 /etc/cron.d/crontab

# Menambahkan crontab
RUN crontab /etc/cron.d/crontab

# Membuat file log untuk cron
RUN touch /var/log/cron.log

# Install Node.js
RUN apt-get update
RUN apt-get install -y ca-certificates curl gnupg
RUN mkdir -p /etc/apt/keyrings
RUN curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg
RUN echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_18.x nodistro main" | tee /etc/apt/sources.list.d/nodesource.list
RUN apt-get update
RUN apt-get install nodejs -y
RUN npm install npm@latest -g

# Install GD & exif
RUN apt-get update && apt-get install -y \
    libfreetype6-dev libjpeg62-turbo-dev libmcrypt-dev libpng-dev \
    && docker-php-ext-configure gd --with-freetype=/usr/include/ --with-jpeg=/usr/include/ \
    && docker-php-ext-install -j$(nproc) gd exif

RUN apt-get update && apt-get install -y curl