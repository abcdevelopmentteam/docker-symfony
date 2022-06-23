FROM php:7.4-fpm-buster
ARG TIMEZONE

RUN mkdir /var/www/html/app
WORKDIR /var/www/html/app

RUN mkdir -p /usr/local/etc/php/extra/ \
    && curl "http://browscap.org/stream?q=Full_PHP_BrowsCapINI" -o /usr/local/etc/php/extra/full_php_browscap.ini \
    && mv /usr/local/etc/php/extra/full_php_browscap.ini /usr/local/etc/php/extra/browscap.ini
COPY php.ini /usr/local/etc/php/conf.d/docker-php-config.ini

RUN apt-get update && apt-get install -y \
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
    libxslt1-dev \
    acl \
    wget \
    vim \
    && echo 'alias sf="php bin/console"' >> ~/.bashrc

RUN docker-php-ext-configure gd --with-jpeg --with-freetype 

RUN docker-php-ext-install \
    pdo pdo_mysql zip xsl gd intl opcache exif mbstring

# Install ldap PHP7 Extension #
RUN apt-get install libldap2-dev -y && \
    docker-php-ext-install ldap

# Other PHP7 Extensions
RUN docker-php-ext-install zip && \
    docker-php-ext-install soap && \
    docker-php-ext-install exif
    
# Set timezone
RUN ln -snf /usr/share/zoneinfo/${TIMEZONE} /etc/localtime && echo ${TIMEZONE} > /etc/timezone \
    && printf '[PHP]\ndate.timezone = "%s"\n', ${TIMEZONE} > /usr/local/etc/php/conf.d/tzone.ini \
    && "date"

# Install composer #
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
    && composer --version

# Install Symfony
RUN wget https://get.symfony.com/cli/installer -O - | bash
RUN mv /root/.symfony/bin/symfony /usr/local/bin/symfony

# YARN -----------------------------------------------------------------------------------------------------------------
# Install Yarn
RUN wget -q https://nodejs.org/dist/v16.15.0/node-v16.15.0-linux-x64.tar.xz && \
  tar -C /usr/local --strip-components 1 -xJf node-v16.15.0-linux-x64.tar.xz && \
  curl -o- -L https://yarnpkg.com/install.sh | bash -s -- --version 1.22.19 && \
  ln -sf $HOME/.yarn/bin/yarn /usr/local/bin/yarn && \
  ln -sf $HOME/.yarn/bin/yarnpkg /usr/local/bin/yarnpkg

ENTRYPOINT ["sh", "/usr/local/bin/docker-php-entrypoint"] 
