FROM php:8.0-apache


# Arguments defined in docker-compose.yml
ARG user
ARG uid


# Install system dependencies
RUN apt-get update && apt-get install -y \
    git \
    zip  \
    curl  \
    unzip  \
    libzip-dev\
    libssh-dev \
    libjpeg-dev \
    libbz2-dev \
    libxml2-dev \
    libpng-dev \
    libonig-dev \
    libmcrypt-dev \
    librabbitmq-dev\
    libreadline-dev \
    libfreetype6-dev \
    libicu-dev \
    g++


# Point to Public Dir for Laravel App
#ENV APACHE_DOCUMENT_ROOT=/var/www/html/public
#RUN sed -ri -e 's!/var/www/html!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/sites-available/*.conf
#RUN sed -ri -e 's!/var/www/!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/apache2.conf /etc/apache2/conf-available/*.conf    


# MOD REWRITE
RUN a2enmod rewrite headers


# Fetching Extensions Manually 

# Get Redis And Put it in a Dir For Direct Install
RUN mkdir -p /usr/src/php/ext/redis
RUN	curl -fsSL https://pecl.php.net/get/redis --ipv4 | tar xvz -C "/usr/src/php/ext/redis" --strip 1

# Get MongoDb And Put it in a Dir For Direct Install
RUN mkdir -p /usr/src/php/ext/mongodb
RUN	curl -fsSL https://pecl.php.net/get/mongodb --ipv4 | tar xvz -C "/usr/src/php/ext/mongodb" --strip 1

# Get Amqp And Put it in a Dir For Direct Install
RUN mkdir -p /usr/src/php/ext/amqp
RUN	curl -fsSL https://pecl.php.net/get/amqp --ipv4 | tar xvz -C "/usr/src/php/ext/amqp" --strip 1


# Clear cache
RUN apt-get clean && rm -rf /var/lib/apt/lists/*


# Install PHP extensions **(Redis, Mongo, Amqp will only work for PHP 8 within this block)
RUN docker-php-ext-install \
    gd \
    bz2 \
    intl \
    redis \
    zip\ 
    amqp\
    exif \
    iconv \
    mongodb\
    pcntl \
    bcmath \
    opcache \
    calendar \
    pdo_mysql \
    mbstring   \
    sockets 


# Get latest Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Pull NodeJs From REPO
RUN curl -sL https://deb.nodesource.com/setup_14.x | bash
# Install NodeJs
RUN apt-get install nodejs

# Create system user to run Composer and Artisan Commands
RUN useradd -G www-data,root -u $uid -d /home/$user $user
RUN mkdir -p /home/$user/.composer && \
    chown -R $user:$user /home/$user


# Copy Everything from local to docker-volume
#COPY ./ /var/www/html


# Set working directory
WORKDIR /var/www/html


# Set User
USER $user


# Expose Port for Forwarding
# EXPOSE 80