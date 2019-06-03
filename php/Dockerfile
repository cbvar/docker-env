FROM php:7.0-fpm

# Set timezome
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# Install package and PHP Core extensions
RUN apt-get update

## Create build dir
ENV BUILD_DIR=/tmp/build
RUN mkdir $BUILD_DIR

## Extrac php source (/usr/src/php.tar.xz)
RUN docker-php-source extract

    RUN apt-get install -y \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpng-dev \
    	&& docker-php-ext-configure gd --with-freetype-dir --with-jpeg-dir --with-png-dir \
    	&& docker-php-ext-install -j$(nproc) gd
    RUN docker-php-ext-install gettext
    RUN docker-php-ext-install pdo_mysql
    RUN docker-php-ext-install mysqli
    RUN docker-php-ext-enable opcache

    ### Install zip(1.2.0) extensions
    COPY ./exts/libzip-1.2.0.tar.xz $BUILD_DIR/libzip.tar.xz
    RUN apt-get install -y \
        zlib1g-dev
    RUN cd $BUILD_DIR \
        && tar -Jxvf libzip.tar.xz \
        && mv libzip-* libzip \
        && cd libzip \
        && ./configure && make && make install
    RUN docker-php-ext-configure zip --with-libzip \
        && docker-php-ext-install zip

## Clean docker-php-source extrace
RUN docker-php-source delete

# Install PECL and other extensions

## Copy extensions had downloaded
COPY ./exts/memcached-3.0.4.tgz $BUILD_DIR/memcached.tgz
COPY ./exts/redis-4.0.2.tgz $BUILD_DIR/redis.tgz
COPY ./exts/cphalcon-3.3.1.tar.gz $BUILD_DIR/cphalcon.tar.gz

## Install PECL extensions (Memcached)
RUN apt-get install -y \
    libmemcached-dev \
    && echo '' | pecl install $BUILD_DIR/memcached.tgz \
    && docker-php-ext-enable memcached

## Install PECL extensions (Redis)
RUN echo '' | pecl install $BUILD_DIR/redis.tgz \
    && docker-php-ext-enable redis

## Install Phalcon extensions
RUN cd $BUILD_DIR \
    && tar -zxvf cphalcon.tar.gz \
    && mv cphalcon-* phalcon \
    && cd phalcon/build \
    && ./install \
    && docker-php-ext-enable phalcon

## Remove build dir
RUN rm -rf $BUILD_DIR

# Clean apt-get update
RUN rm -r /var/lib/apt/lists/*

# Install Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
