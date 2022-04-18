FROM php:7.4-fpm

# Install system dependencies
RUN apt-get clean && apt-get update && apt-get install --no-install-recommends -yq \
  curl \
  gcc \
  git \
  libcurl3-dev \
  libfreetype6-dev \
  libjpeg62-turbo-dev \
  libonig-dev \
  libpcre3-dev \
  libpng-dev \
  libxml2-dev \
  libzip-dev \
  make automake autoconf \
  sudo \
  unzip \
  zip \
  zlib1g-dev

# Install php extensions:
RUN docker-php-ext-install curl fileinfo gettext json gd pdo pdo_mysql zip

# Install php Composer:
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Install php PSR:
RUN git clone https://github.com/jbboehr/php-psr.git /root/psr \
  && cd /root/psr \
  && phpize \
  && ./configure \
  && make \
  && make test \
  && make install \
  && echo "extension=psr.so" > /usr/local/etc/php/conf.d/29-psr.ini \
  && cd && rm -Rf /root/psr

# Install php Imagick:
RUN apt-get install -y libmagickwand-dev \
	&& pecl install imagick-3.7.0 \
	&& docker-php-ext-enable imagick

# Install php OpenSSL:
RUN apt-get install -y openssl libssl-dev libcurl4-openssl-dev \
  && pecl install crypto-0.3.2 \
  && docker-php-ext-enable crypto

# Install php Memcached:
RUN apt-get install -y libmemcached-dev \
	&& pecl install memcached-3.2.0 \
	&& docker-php-ext-enable memcached

# Install php Phalcon:
RUN git clone -b v4.1.2 --depth=1 --single-branch https://github.com/phalcon/cphalcon.git /root/buildphalcon \
  && cd /root/buildphalcon/build/php7/64bits \
  && phpize && export CFLAGS="-O2 -s" \
  && ./configure && make -j 8 && make install \
  && echo "extension=phalcon.so" > /usr/local/etc/php/conf.d/30-phalcon.ini \
  && rm -Rf /root/buildphalcon

# Install php Phalcon-devtools:
RUN git clone https://github.com/phalcon/phalcon-devtools.git /opt/phalcon-devtools \
  && cd /opt/phalcon-devtools \
  && composer install \
  && ln -s /opt/phalcon-devtools/phalcon /usr/bin/phalcon \
  && chmod ugo+x /opt/phalcon-devtools/phalcon

# Clear cache
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

# Set working directory
WORKDIR /phalcon

COPY ./php.ini /usr/local/etc/php/conf.d/
