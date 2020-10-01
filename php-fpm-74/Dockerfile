# Set php version arg
ARG PHP_VERSION

FROM php:$PHP_VERSION-fpm-alpine3.10


RUN echo "PHP Version $PHP_VERSION"


RUN mkdir -p /var/www

RUN apk update && apk upgrade

# Install essential tools
RUN apk add --no-cache --virtual .build-deps \
  wget \
  git \
  zsh \
  curl \
  unzip \
  zip \
  nano \
  libxml2-dev \
  freetype \
  libpng \
  libjpeg-turbo \
  freetype-dev \
  libjpeg-turbo-dev \
  shadow \
  autoconf \
  libzip-dev \
  libpng-dev \
  libsodium \
  libsodium-dev \
  #libfreetype6-dev \
  #libjpeg62-turbo-dev \
  libmcrypt-dev \
  #libpng12-dev \
  libwebp-dev \
  libxpm-dev \
  g++ \
  libmemcached \
  libmemcached-libs \
  libmemcached-dev \
  php7-dev \
  autoconf \
  file \
  cyrus-sasl-dev \
  gcc \
  libc-dev \
  pkgconf \
  re2c \
  zlib-dev \
  make

RUN apk add --no-cache imagemagick-dev imagemagick
RUN pecl install imagick-beta

RUN docker-php-ext-configure zip
RUN docker-php-ext-configure pdo_mysql && docker-php-ext-install pdo_mysql  
#RUN docker-php-ext-configure sodium && docker-php-ext-install sodium 

RUN docker-php-ext-install gd

RUN docker-php-ext-configure gd 


RUN pecl install xdebug
RUN docker-php-ext-enable xdebug

RUN pecl install redis && docker-php-ext-enable redis


RUN echo "xdebug.remote_enable=on" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
  && echo "xdebug.remote_autostart=off" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
  && echo "xdebug.remote_port=9001" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
  && echo "xdebug.remote_handler=dbgp" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
  && echo "xdebug.remote_connect_back=0" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
  && echo "xdebug.idekey=mertblog.net" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
  && echo "xdebug.remote_host=docker.for.mac.localhost" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini 

# Download oh-my-zsh
RUN sh -c "$(wget -O- https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
# Set "gnzh" as theme fot zsh terminal
RUN sed -i 's/ZSH_THEME="robbyrussell"/ZSH_THEME="gnzh"/g' ~/.zshrc
#Set zsh has default shell executable

RUN sed -i -e "s/bin\/ash/bin\/zsh/" /etc/passwd


# Install composer
RUN curl -sL https://getcomposer.org/installer | php -- --install-dir /usr/bin --filename composer
RUN composer clear-cache

# Add GITHUB_API_TOKEN support for composer
RUN chmod 700 /usr/bin/composer

# Install faketime library for manage times for multipurpose tests
RUN git clone https://github.com/wolfcw/libfaketime /libfaketime && apk -U add build-base
WORKDIR /libfaketime
RUN make && make install

RUN cp /usr/local/lib/faketime/libfaketimeMT.so.1 /faketime.so
RUN cp /faketime.so /lib/faketime.so

ENV LD_PRELOAD=/lib/faketime.so
ENV FAKETIME="-0d" 
RUN echo "FAKETIME=-0d" >> /etc/environment && source /etc/environment
ENV DONT_FAKE_MONOTONIC=1
RUN date

RUN cp /usr/local/etc/php/php.ini-development /usr/local/etc/php/php.ini

RUN sed -i 's/max_execution_time = 30/max_execution_time = 300/g' /usr/local/etc/php/php.ini
RUN sed -i 's/max_input_time = 60/max_input_time = 300/g' /usr/local/etc/php/php.ini
RUN sed -i 's/post_max_size = 8M/post_max_size = 100M/g' /usr/local/etc/php/php.ini
RUN sed -i 's/upload_max_filesize = 2M/upload_max_filesize = 100M/g' /usr/local/etc/php/php.ini
RUN sed -i 's/memory_limit = 128M/memory_limit = 512M/g' /usr/local/etc/php/php.ini



RUN pecl config-set php_ini  /usr/local/etc/php/php.ini \
  && pecl install -f memcached \
  && echo extension=memcached.so >> /usr/local/etc/php/conf.d/docker-php-ext-memcached.ini \
  && rm -rf /tmp/pear


RUN chown www-data:www-data -R /var/www

EXPOSE 9000

CMD zsh && php-fpm